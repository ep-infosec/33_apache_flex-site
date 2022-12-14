Title:  Download Apache Flex Squiggly

Apache Flex Squiggly is a ActionScript spell checking library.

Use the links below to download Apache Squiggly 1.1 release from one of our mirrors. You must verify the integrity of the downloaded files using signatures downloaded from this page.

Squiggly has some compile-time and run-time dependencies.  Please make sure to review the dependencies section if you plan on re-compiling the source from scratch.

**Please review the release notes before installing or compiling this release.**

<div class="headline"><h4>Current Release (1.1)</h4></div>

The Apache Flex team is pleased to offer this release, available as of the 29th of October 2014.  This is the initial release of the Squiggly library under Apache.

Binaries are provided as a convenience for those who do not wish to compile Squiggly themselves.  The 1.1 binaries were published on the 29th of October 2014.

**Please be aware of the dependencies listed in the section below.**  They are required in order to compile the Apache Flex Squiggly library.

- Release Notes : [RELEASE_NOTES][2]
- README : [README][1]
- Source (tar.gz) : [apache-flex-squiggly-1.1-src.tar.gz][3] [[PGP](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.tar.gz.asc)] [[MD5](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.tar.gz.md5)]
- Source (zip) : [apache-flex-squiggly-1.1-src.zip][4] [[PGP](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.zip.asc)] [[MD5](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.zip.md5)]
- Binaries (tar.gz) : [apache-flex-squiggly-1.1-bin.tar.gz][5] [[PGP](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.tar.gz.asc)] [[MD5](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.tar.gz.md5)]
- Binaries (zip) : [apache-flex-squiggly-1.1-bin.zip][6] [[PGP](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.zip.asc)] [[MD5](https://www.apache.org/dist/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.zip.md5)]

<div class="headline"><h4>Dependencies</h4></div>

Apache Squiggly requires some build tools which must be installed prior to building Squiggly.  Some of these have different licenses.
#### General Requirements
To compile the source code you need:
- Java SDK 1.6 or greater
- ANT 1.7.1 or greater
- Apache Flex 4.8 or greater

#### Software Dependencies

Apache Squiggly requires 3rd party dictionaries to in order to perform spell checking. These are not required to just compile Squiggly. See the [README][1] on where to obtain these dictionaries other than the British and American English directories contained in this release. Most of these dictionaries are not Apache licensed.

#### Verify the integrity of the files

Apache relies on 3rd party mirrors to distribute their software.  Because these releases are hosted on servers that are not under our control, we strongly recommend that you verify the integrity of the binaries or source code before you install it using the PGP or MD5 signatures listed next to the downloads.  This involves a few extra steps, but it will verify that you are getting a true Apache release.

The PGP signatures can be verified using PGP or GPG. First download the [KEYS][10] as well as the asc signature file for the relevant distribution. Make sure you get these files as linked from this page (rather than a mirror) to insure the integrity of the files.

    % pgpk -a KEYS

    % pgpv apache-flex-squiggly-1.1-src.tar.gz.asc

*or*

    % pgp -ka KEYS

    % pgp apache-flex-squiggly-1.1-src.tar.gz.asc

*or*

    % gpg --import KEYS

    % gpg --verify apache-flex-squiggly-1.1-src.tar.gz.asc


Alternatively, you can verify the MD5 signature on the files. A unix/Mac program called `md5` or `md5sum` is included in many unix distributions. It is also available as part of [GNU Textutils][11]. Windows users can get binary md5 programs from [here][12] , [here][13] , or [here][14]. An MD5 signature consists of 32 hex characters, and a SHA1 signature consists of 40 hex characters. Ensure your generated signature string matches the signature string published in the files above.

[1]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/README
[2]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/RELEASE_NOTES
[3]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.tar.gz
[4]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/apache-flex-squiggly-1.1-src.zip
[5]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.tar.gz
[6]: https://www.apache.org/dyn/closer.lua/flex/squiggly/1.1/apache-flex-squiggly-1.1-bin.zip
[10]: https://www.apache.org/dist/flex/KEYS
[11]: https://www.gnu.org/software/textutils/textutils.html
[12]: https://www.fourmilab.ch/md5/
[13]: https://www.pc-tools.net/win32/freeware/console/
[14]: https://www.slavasoft.com/fsum/

