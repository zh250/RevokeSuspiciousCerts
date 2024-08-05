Revoke-Suspicious-Certs on Linux
==========================================

This tool revokes certain CA certificates for OpenSSL and NSS-based applications
on Linux (most notably, Firefox & Chrome).


## Introduction
On Linux there are multiple libraries for SSL/TLS and each may have its own
certificate store. The `/etc/ca-certificates.conf` configures the trusted
Root CAs for OpenSSL (which `wget` uses by default). Another widely used
library is NSS by Mozilla, which supports blacklisting a specific intermediate
CA without fiddling with the Root CA.

Please make sure all scripts must have execute permission.

**This tool is experimental. DO MAKE BACKUPS before you do anything!**

## Revoke CA certificates for NSS.
### Usage
First you need to have packages installed to provide `certutil`. On Ubuntu it would be:

``` sh
sudo apt-get install libnss3-tools
```

Then, use the `revoke-suspicious-certs.sh` to do the revocation.
For most NSS-based applications including Chrome, it would be:

``` sh
./revoke-suspicious-certs.sh extended $HOME/.pki/nssdb
```

to revoke trust of CAs within the *extended* set. Change `extended` to `all` or `base`
or `restore` to revoke other sets of certs.

Since Firefox maintains different certificate store for different browser profile (rather
than per Linux user for Chrome), you need to do this for every profile under `~/.mozilla/firefox*`.
In addition, to execute the following command, system may require you execute as root 
otherwise it will prompt permission denied, And also if you execute bash as sudo, the path 
`~/.mozilla/firefox*/*.default` should specify as absolute path `/home/XXX/.mozilla/firefox*/*.default`
where the `XXX` here must be replaced by your username

``` sh
for profile in `ls -d ~/.mozilla/firefox*/*.default`; do
  ./revoke-suspicious-certs.sh extended "$profile"
done
```


### Certificate Pinning Test
Certificate pinning test is implemented in `certificate_pinning_test.py`.
Use it as:

``` python
./certificate_pinning_test.py
```
Mention: the script cannot work well because M2Crypto cannot be installed successfully

Further mention: for newer versions of debian-based Linux, such packages should be installed
via `sudo apt install python3-XYZ` rather than 'pip3 install XYZ'. However, the package
python3-M2Crypto does not exist, Although M2Cypto has pip package yet, traditional
installation method is deprecated, and `pip install XYZ` is more suitable for venv


This should reveal any HTTPS Man-In-The-Middle devices that may eavesdrop
your connection (with Google), including but not limited to a transparent
proxy.

The pinning test uses signatures extracted from Android.

(This is experimental. If you do encounter an error, please file an issue
with the certificate you get.)

### Applications use `~/.pki/nssdb`
Most NSS-based applications use `~/.pki/nssdb`, including but not limited to:

- Curl
- Google Chrome
- Chromium
- Evolution
- GNUnet
- Wine

#### Exceptions
- Firefox/Iceweasel `~/.mozilla/firefox/*.default/`
- Thunderbird/Icedove `~/.thunderbird/*.default` 

### Notes
- Deselecting a CA by `dpkg-reconfigure ca-certificates` does NOT affect any NSS-based applications.
- There is a global database at `/etc/pki/nssdb`. On Debian/Ubuntu, this
  global database is installed by `libnss3-nssdb`. And it actually links to
  `/var/lib/nssdb/`, which is an empty database. And since most NSS based applications only read it, so it's safe to assume that it is empty. On Arch
  it is not created by default, too. On Fedora `/etc/pki/nssdb` exists but is
  also not enabled by default. Though OpenSuse will install certificates into
  `/etc/pki/nssdb` provided that `mozilla-nss-sysinit` is installed. Thus if
  you worry about it, you can check its emptiness with `certutil -d sql:${DBPATH} -L`
  yourself. And revoke certificates in it via `revoke-suspicious-certs.sh` if necessary.


## Revoke CA certificates for OpenSSL.
### Usage
``` sh
sudo ./cac_revoke.sh extended
```

The above command will revoke trust of CAs within the *extended* set.
Change `extended` to `all` or `base` to revoke other sets of certs.
Change `extended` to `restore` to restore the revocation.

### Test
Make sure `wget` is available on your `$PATH`.

``` sh
./validator.sh
```

## References
[A note about SSL/TLS trusted certificate stores, and platforms (OpenSSL and GnuTLS)](https://www.happyassassin.net/2015/01/12/a-note-about-ssltls-trusted-certificate-stores-and-platforms)


## License
This Linux utility is distributed under the Apache License.
