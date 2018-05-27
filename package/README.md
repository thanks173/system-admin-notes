# General packaging note

## Generate GPG key

```bash
gpg --full-gen-key
```

## Export GPG keys

```bash
gpg -a --export-secret-key CyRadar > secret.gpg
gpg -a --export CyRadar > public.gpg
```

## Import gpg key

```bash
rpm --import public.gpg
gpg --import --allow-secret-key-import secret.gpg
```

## Strip binary

```bash
strip --strip-debug --strip-unneeded <binary_file>
```