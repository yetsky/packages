# Contributing Guidelines  
(See <http://wiki.openwrt.org/doc/devel/packages> for overall format and construction)

All packages you commit or submit by pull-request should follow these simple guidelines:

* Package a version which is still maintained by the upstream author.
* Will be updated regularly to maintained and supported versions.
* Have no dependencies outside the OpenWrt core packages or this repository feed.
* Have been tested to compile with the correct includes and dependencies. Also, test with "Compile with full language support" found under "General Build Settings" set.
* Best of all -- it works as expected!

Makefile contents should contain:

* An up-to-date copyright notice. Use OpenWrt if no other present or supply your own.
* A (PKG_)MAINTAINER definition listing either yourself or another person in the field.  
    (E.g.: PKG_MAINTAINER:= Joe D. Hacker `<jdh@jdhs-email-provider.org`>)
* A PKG_LICENSE tag declaring the main license of the package.
    (E.g.: PKG_LICENSE:=GPL-2.0+) Please use SPDX identifiers if possible (see list at the bottom).
* An optional PKG_LICENSE_FILE including the filename of the license-file in the source-package.
    (E.g.: PKG_LICENSE_FILE:=COPYING)

Commits and pull-requests:

* Have a useful description prefixed with the package name  
    (E.g.: "foopkg: Add libzot dependency")
* Include Signed-off-by in the comment  
    (See <https://dev.openwrt.org/wiki/SubmittingPatches#a10.Signyourwork>)

If you have commit access:

* Do NOT use git push --force.
* Do NOT commit to other maintainer's packages without their consent.
* Use Pull Requests if you are unsure and to suggest changes to other maintainers.

Gaining commit access:

* We will gladly grant commit access to responsible contributors who have made
  useful pull requests and / or feedback or patches to this repository or
  OpenWrt in general. Please include your request for commit access in your
  next pull request or ticket.

Release Branches:

* Branches named "for-XX.YY" (e.g. for-14.07) are release branches.
* These branches are built with the respective OpenWrt release and are created
  during the release stabilisation phase.
* Please ONLY cherry-pick or commit security and bug-fixes to these branches.
* Do NOT add new packages and do NOT do major upgrades of packages here.
* If you are unsure if your change is suitable, please use a pull request.

####Common LICENSE tags (short list)  
(Complete list can be found at: <http://spdx.org/licenses>)
####  

| Full Name | Identifier  |
|---|:---|
|Apache License 1.0|Apache-1.0|
|Apache License 1.1|Apache-1.1|
|Apache License 2.0|Apache-2.0|
|Artistic License 1.0|Artistic-1.0|
|Artistic License 1.0 (Perl)|Artistic-1.0-Perl|
|Artistic License 1.0 w/clause 8|Artistic-1.0-cl8|
|Artistic License 2.0|Artistic-2.0|
|BSD 2-clause "Simplified" License|BSD-2-Clause|
|BSD 2-clause FreeBSD License|BSD-2-Clause-FreeBSD|
|BSD 2-clause NetBSD License|BSD-2-Clause-NetBSD|
|BSD 3-clause "New" or "Revised" License|BSD-3-Clause|
|BSD 3-clause Clear License|BSD-3-Clause-Clear|
|BSD 4-clause "Original" or "Old" License|BSD-4-Clause|
|BSD Protection License|BSD-Protection|
|BSD with attribution|BSD-3-Clause-Attribution|
|BSD-4-Clause (University of California-Specific)|BSD-4-Clause-UC|
|GNU General Public License v1.0 only|GPL-1.0|
|GNU General Public License v1.0 or later|GPL-1.0+|
|GNU General Public License v2.0 only|GPL-2.0|
|GNU General Public License v2.0 or later|GPL-2.0+|
|GNU General Public License v3.0 only|GPL-3.0|
|GNU General Public License v3.0 or later|GPL-3.0+|
|GNU Lesser General Public License v2.1 only|LGPL-2.1|
|GNU Lesser General Public License v2.1 or later|LGPL-2.1+|
|GNU Lesser General Public License v3.0 only|LGPL-3.0|
|GNU Lesser General Public License v3.0 or later|LGPL-3.0+|
|GNU Library General Public License v2 only|LGPL-2.0|
|GNU Library General Public License v2 or later|LGPL-2.0+|
|Fair License|Fair|
|ISC License|ISC|
|MIT License|MIT|
|No Limit Public License|NLPL|
|OpenSSL License|OpenSSL|
|X11 License|X11|
|zlib License|Zlib|
