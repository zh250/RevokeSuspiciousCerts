﻿RevokeSuspiciousCerts on Windows
==============
Revoke Suspicious certificates.

### Introduction
There are 2 types of batches:
* Online: Verifys Internet connection certificates.
  * Online - Revoke or restore online certificates in system store.
  * Firefox - Revoke or restore online certificates in Firefox NSS.
  * Update - Update or reset online certificates in system store.
* CodeSigning and Organization: Verifies software publisher and organization certificates.
  * CodeSigning - Revoke or restore code signing certificates in system store.
  * Organization - Revoke or restore organization certificates in system store.

### Usage
* [Online](../Shared/Documents/ReadMe_Online.md)
* [CodeSigning/Organization](../Shared/Documents/ReadMe_CodeSigning_Organization.md)


### Parameter
Batches can revoke or restore with below parameters:
* Online
  * Run `xx.bat 1` to revoke Base version.
  * Run `xx.bat 2` to revoke Extended version.
  * Run `xx.bat 3` to revoke All version.
  * Run `xx.bat 4` to restore all Online revoking.
  * Firefox NSS parameter
    * Run `xx.bat * ProfilePath` to locate the path of Firefox profile folder.
* CodeSigning and Organization
  * Run `xx.bat 1` to revoke.
  * Run `xx.bat 2` to restore.
  * Run `xx.bat * -f` to enable software force policy.

### License
[GNU General Public License/GNU GPL v2](./LICENSE)
