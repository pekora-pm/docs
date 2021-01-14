# Binary Package Specification

Pekora is a source-based package manager. That is, all packages are built from source on your machine on demand. But, Pekora is not purely source based. For convenience of users, Pekora also provides binary package support.

## Naming, Formatting and Structure

All Pekora Binary Package should be named with the following format: `pkgname_pkgver-pkgrel_pkgarch.pkg.tar.zst`. As its suffix indicates, these packages should be a Zstandard-compressed tarball, with the following structure:

```tree
pkgname_pkgver-pkgrel_pkgarch.pkg.tar.zst
|--- root
|--- scripts
|--- *
|--- manifest.yml
```

Each directory explained:
* `root`: This directory is mandatory (except if this package is a placeholder). It contains major files of the package. This should be the        PREFIX of the installing target if you are using make/cmake or other building systems
* `scripts`: This directory is not mandatory. You could put pre-/post-install scripts here and define them in the manifest
* `*`: Actually you can put anything here for configuring purposes -- just make sure they are all being used in the installing/configuring process and don't bloat the package
* `manifest.yml`: This is the manifest of the binary package -- the most important and mandatory file for any binary package. Its content is defined in the manifest section below.

Although using other compressing format (such as bzip2, gzip or xz) is possible, the Zstandard compression is always preferred, and you shouldn't use other format unless there's a good reason to.

## Manifest

The manifest is like the soul of a package: it defines what this package is. It is stored in human-friendly YAML format, which means that in certain circumstances you can write or fix a manifest by hand without any hassle. Its content is as follows:

```yaml
name: String
version: String
rel: u8
implements: Vec<String>
conflicts: Vec<String>
```

The meaning of each field is described below:

* `name`: The name of the package.
* `version`: A version number that follows the Semantic Versioning standard.
* `rel`: 8-bit unsigned integer (increase this by one if it is only a newer build, or contains a new patch that has not yet been merged into upstream)
* `implements`: Not mandatory. Indicates what this packages implements specific requirements Some packages could have same functionality, but is implemented differently, and this information helps the alternative system to choose an appropriate package to do a specific job. Examples could be binutil and busybox (probably an unsuitable comparison here).
* `conflicts`: Not mandatory. Indicates what package conflicts with this package. If the conflicting package is present, the package will still install, but pekora-env will refuse to link one to the environment when another is present.

