# RPM packaging

## Import GPG key

```bash
rpm --import public.gpg
```

## Build rpm file

```bash
```
# With debug package
rpmbuild -ba SPECS/<package-name>.spec
# Without debug package
rpmbuild --define "debug_package %{nil}" -ba SPECS/<package-name>.spec

## Sign package

```bash
rpm --addsign *.rpm
```

## Verify signed package

```bash
rpm -K *.rpm
rpm -qpi *.rpm
```

## Create info files

```bash
createrepo /dir/to/repo-folder
createrepo --update /dir/to/repo-folder
```

## Using on client
edit /etc/yum.repos.d/repo-name.repo

```
[<repo-id>]
name=<repo-name>
baseurl=<repo-url>
gpgcheck=1
enabled=1
gpgkey=<key-url>
```
