# Canon ScanGear MP 1.60-1 source code and build instructions

## Original Canon ScanGear MP sources
The original Canon ScanGear MP sources have been downloaded from:
http://gdlp01.c-wss.com/gds/3/0100003033/01/scangearmp-source-1.60-1.tar.gz
and it contains the code for the following Canon PIXMA model series:

* MP250
* MP280
* MP495
* MG5100
* MG5200
* MG6100
* MG8100

```text
Name: scangearmp-source-1.60-1.tar.gz
Size: 3931034 bytes
Last modified date: 3 set 2010
Hashes:
   MD5: 15782d670f9d5c5904e00610508114f3  scangearmp-source-1.60-1.tar.gz
  SHA1: 9adfb9f4fc0177445489d51b03e52d60eed124dc  scangearmp-source-1.60-1.tar.gz
SHA256: fab6b764409f17a674ba31e45a515353cdf027562b2daee96c316bb86d6d6340  scangearmp-source-1.60-1.tar.gz
SHA512: 759dfab54989a80922f058dba31bf924af4ec71d5c0dba93eb879d0f87f9dfeae89324ddcf0b647bff3065d92a8b5e8be02590aebff7502d3878d534f1364211  scangearmp-source-1.60-1.tar.gz
```

## How to build the binary package

```bash
docker build -t canon-build-env:1 .
docker run --rm --ulimit "nofile=1024:1048576" -v $(pwd):/build -it -u $(id -u):$(id -g) canon-build-env:1
cd /build/scangearmp-source-1.60-1
dpkg-buildpackage --build=binary --unsigned-changes --unsigned-source
```

## Run from a docker container

```bash
# to show magic cookie
xauth list
docker run --rm -it --privileged -v $(pwd):/debs -v /dev/bus/usb:/dev/bus/usb -v /tmp/.X11-unix/:/tmp.X11-unix -e DISPLAY=$DISPLAY --net=host debian:13.6 bash
apt-get update
apt-get install --yes xauth
xauth add <magic-cookie>
cd /debs
apt-get install ./scangearmp-common_1.60-2_amd64.deb ./scangearmp-mp280series_1.60-2_amd64.deb
scangearmp
```

## Variables to set in ~/.quiltrc to work with quilt patches

This .quiltrc is already set in the Dockerfile in `/tmp/.quiltrc`,
it is not needed if you just build the package.

```bash
cat <<EOF >> ~/.quiltrc
QUILT_PATCHES=debian/patches
QUILT_SERIES=debian/patches/series
QUILT_DIFF_ARGS="--no-timestamps --no-index -pab"
QUILT_REFRESH_ARGS="--no-timestamps --no-index -pab"
QUILT_PATCH_OPTS="--reject-format=unified"
EOF
```

## Migration to GTK3

[gtk3-migration](gtk3-migration.md)
