# Encrypted Git Repository

### Requirements

- git
- gpg
- [git-remote-gcrypt](https://github.com/spwhitton/git-remote-gcrypt) (**WARNING: This tool is great, but encrypted repositories it produces have similarities which can help someone link them. (ex: Manifest file name, commit date.)**)
- Know how signing commits works and when github or other parties display "Verified" message next to your commits.

Alternative:
- [git-remote-gcrypt-dr57](https://github.com/Dataram57/git-remote-gcrypt-dr57) - My own fork of `git-remote-gcrypt`. With this fork you can specify custom manifest file name and use current timestamps with configured user as a commiter for commits.

### Key Generation

```sh
# 1. Get your self a private key
gpg --full-gen-key

# 2. Find the $FINGERPRINT of your public key
gpg --list-keys

# 3. Export your public key to a file (for others to import it)
gpg --armor --export "$FINGERPRINT" > user.pubkey
# or
gpg --armor --export "$EMAIL" > user.pubkey

# 4. Importing someone's public key from a file
gpg --import user.pubkey

```

It is important to note that the `$FINGERPRINT` derives from the public key.

Commands that set your default git to sign your commits.
```sh
git config --global user.signingkey "$FINGERPRINT"
git config --global gpg.program "$(which gpg)"
git config --global gpg.program commit.gpgsign true
```

### Git setup

Steps marked with `*` are only for repositories that are meant to be decryptable by more than 1 private key (or pair, depending on the crypto mechanism used).

```sh
# 1. First make sure specify the remote name (in this case I will use origin)
git remote add origin gcrypt::https://github.com/Dataram57/encrypted_repo_test
# ^
# |
### Personal Alternative:
git remote add origin "gcrypt-dr57::https://github.com/Dataram57/encrypted_repo_test#$MANIFEST_FILE_NAME#"

# 2*. Set users who can encrypt and decrypt this repository (You must have their public keys stored). Separate fingerprints by space.
git config remote.origin.gcrypt-participants "$SIGNER_FINGERPRINT $FRIEND_FINGERPRINT_1 $FRIEND_FINGERPRINT_2"

# 3*(optional). If you don't want to reuse the same key pair you have set globally you may want to specify per repo. Make sure that $SIGNER_FINGERPRINT will also be mentioned in remote.origin.gcrypt-participants.
git config user.signingkey "$SIGNER_FINGERPRINT"
# alternative: remote.<name>.gcrypt-signingkey
# to be honest I prefer this option much better:
git config remote.origin.gcrypt-signingkey "$SIGNER_FINGERPRINT"
# keep in mind that you can also change the remote name from origin to something else.

4. Cloning the repo (will work only if your public key was specified in remote.origin.gcrypt-participants via fingerprint)
git clone gcrypt::https://github.com/Dataram57/encrypted_repo_test
# ^
# |
### Personal Alternative:
git clone "gcrypt-dr57::https://github.com/Dataram57/encrypted_repo_test#$MANIFEST_FILE_NAME#"
```

### Verifications / Configuration prints

```sh
gpg -k
git config -l
```