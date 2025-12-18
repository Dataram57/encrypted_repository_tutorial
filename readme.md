# Encrypted Repository Tutorial

### Requirements

- git
- gpg
- [git-remote-gcrypt](https://github.com/spwhitton/git-remote-gcrypt)
- Know how signing commits works and when github or other parties display "Verified" message next to your commits.

### Key Generation

```
1. Get your self a private key
gpg --full-gen-key

2. Find the <FINGERPRINT> of your public key
gpg --list-keys

3. Export your public key to a file (for others to import it)
gpg --armor --export <FINGERPRINT> > user.pubkey
or
gpg --armor --export <EMAIL> > user.pubkey

4. Importing someone's public key from a file
gpg --import user.pubkey

```

It is important to note that the `<FINGERPRINT>` derives from the public key.

Commands that set your default git to sign your commits.
```
git config --global user.signingkey "<FINGERPRINT>"
git config --global gpg.program "$(which gpg)"
git config --global gpg.program commit.gpgsign true
```

### Git setup

Steps marked with `*` are only for repositories that are meant to be decryptable by more than 1 private key (or pair, depending on the crypto mechanism used).

```
1. First make sure specify the remote name (in this case I will use origin)
git remote add origin gcrypt::https://github.com/Dataram57/encrypted_repo_test

2*. Set users who can encrypt and decrypt this repository (You must have their public keys stored). Separate fingerprints by space.
git config remote.origin.gcrypt-participants "<SIGNER_FINGERPRINT> <FRIEND_FINGERPRINT> <FRIEND_FINGERPRINT>"

3*(optional). If you don't want to reuse the same key pair you have set globally you may want to specify per repo. Make sure that <SIGNER_FINGERPRINT> will also be mentioned in remote.origin.gcrypt-participants.
git config user.signingkey "<SIGNER_FINGERPRINT>"
//alternative: remote.<name>.gcrypt-signingkey

4. Cloning the repo (will work only if your public key was specified in remote.origin.gcrypt-participants via fingerprint)
git clone gcrypt::https://github.com/Dataram57/encrypted_repo_test
```