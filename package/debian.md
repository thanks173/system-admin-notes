# DEB packaging

## Import GPG key

```bash
apt-key add <key_file>
```

## Sign .deb package

```bash
dpkg-sig -k <key-name> --sign builder *.deb
```

## Verify signed package

```bash
dpkg-sig --verify *.deb
```

## Force update from unsigned repository

```bash
apt update --allow-unauthenticated
```

## Create info files

```bash
apt-ftparchive packages . > Packages
gzip -c Packages > Packages.gz
apt-ftparchive release . > Release
gpg --yes --clearsign -o InRelease Release
gpg --yes -abs -u <key-name> -o Release.gpg Release
```

## Using on client

edit /etc/apt/sources.list.d/repo-name.list
```
deb https://repo-server.com/custom/dir ./
```