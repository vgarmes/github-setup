# github-setup

A guide to set up Github with SSH authentication and signed commits

## Adding commit signature verification

#### Check for existing GPG keys

```shell
gpg --list-secret-keys --keyid-format=long
```

If there are already keys follow [this guide](https://docs.github.com/en/authentication/managing-commit-signature-verification/checking-for-existing-gpg-keys). Otherwise generate new keys with the steps below.

### Generating a new GPG key

#### Install GPG command line tools:

```shell
brew install gnupg
```

#### Generate a GPG key

```shell
gpg --full-generate-key
```

This will prompt you to configure the kind, size and length of the key, just press `Enter` for using defaults.

After that, it will prompt you to enter your user ID and user email (same email as in Github account).

#### Type a secure passphrase

#### Copy the GPG key ID

1. Use the `gpg --list-secret-keys --keyid-format=long` command to list the long form of the GPG keys and copy the ID (number after slash). In this example, the GPG key ID is `3AA5C34371567BD2`:

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
```

2. Paste the text below, substituting in the GPG key ID you'd like to use. In this example, the GPG key ID is `3AA5C34371567BD2`:

```shell
gpg --armor --export 3AA5C34371567BD2
```

3. Copy your GPG key, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK------` and ending with `-----END PGP PUBLIC KEY BLOCK-----`.

4. [Add the GPG key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account)

#### Tell Git about your signing key

1.

```shell
git config --global user.signingkey [key]
git config --global commit.gpgsign true
git config --global tag.gpgSign true
```

3. If you configured the GPG keys with a passphrase (recommended), install `pinentry-mac` and add it to your GNUPG configuration:

```shell
brew install pinentry-mac

echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
```

This will prompt you to enter the passphrase the first time you make a signed commit. If you check the box "Add to keychain" it will never prompt again.
