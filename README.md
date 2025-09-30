# github-setup

A guide to set up Github with SSH authentication and signed commits

## Set user name and email

```shell
git config --global user.name "Mona Lisa"
git config --global user.email "YOUR_EMAIL"
```

## Connecting to GitHub with SSH

[Connecting over HTTPS](https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git) seems to be the recommended way, but if you want SSH these are the steps:

### Check for existing keys

Enter `ls -al ~/.ssh` to see if existing SSH keys are present.

If there are, follow [this guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys).

Otherwise, follow the steps below.

### Generating a new SSH key

Create a new SSH key with:

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

At the prompt, type a secure passphrase.

### Adding SSH key to the ssh-agent

1. Start the ssh-agent in the background

```shell
eval "$(ssh-agent -s)"
> Agent pid 59566
```

2. Check to see if your `~/.ssh/config` file exists in the default location.

3. If it doesn't exist, create the file:

```shell
touch ~/.ssh/config
```

4. Open your `~/.ssh/config`file, then modify the file to contain the following lines. If your SSH key file has a different name or path than the example code, modify the filename or path to match your current setup.

```text
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

5. Add your SSH private key to the ssh-agent and store your passphrase in the keychain.

```shell
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

5. Copy the SSH public key to your clipboard:

```shell
pbcopy < ~/.ssh/id_ed25519.pub
```

6. [Add the SSH public key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## Adding commit signature verification

### Check for existing GPG keys

```shell
gpg --list-secret-keys --keyid-format=long
```

If there are already keys follow [this guide](https://docs.github.com/en/authentication/managing-commit-signature-verification/checking-for-existing-gpg-keys). Otherwise generate new keys with the steps below.

### Generating a new GPG key

1. Install GPG command line tools:

```shell
brew install gnupg
```

2. Generate a GPG key

```shell
gpg --full-generate-key
```

This will prompt you to configure the kind, size and length of the key, just press `Enter` for using defaults.

After that, it will prompt you to enter your user ID and user email (same email as in Github account).

3. Type a secure passphrase

4. Copy the GPG key ID

5. Use the `gpg --list-secret-keys --keyid-format=long` command to list the long form of the GPG keys and copy the ID (number after slash). In this example, the GPG key ID is `3AA5C34371567BD2`:

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
```

6. Paste the text below, substituting in the GPG key ID you'd like to use. In this example, the GPG key ID is `3AA5C34371567BD2`:

```shell
gpg --armor --export 3AA5C34371567BD2
```

7. Copy your GPG key, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK------` and ending with `-----END PGP PUBLIC KEY BLOCK-----`.

8. [Add the GPG key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account)

### Tell Git about your signing key

1. Set your primary GPG signing key. In this example, the GPG key ID is `3AA5C34371567BD2`:

```shell
git config --global user.signingkey 4BB6D45482678BE3
```

2. Optionally, to configure Git to sign all commits and tags by default, enter the following command:

```shell
git config --global commit.gpgsign true
git config --global tag.gpgSign true
```

3. If you configured the GPG keys with a passphrase (recommended), install `pinentry-mac` and add it to your GNUPG configuration:

```shell
brew install pinentry-mac

echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
```

This will prompt you to enter the passphrase the first time you make a signed commit. If you check the box "Add to keychain" it will never prompt again.
