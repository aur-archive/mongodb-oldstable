# $Id: PKGBUILD 100481 2013-11-05 02:40:41Z fyan $
# Maintainer: Felix Yan <felixonmars@gmail.com>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Mathias Stearn <mathias@10gen.com>
# Contributor: Alec Thomas
# Contributor: Wilson Pinto Júnior <wilsonpjunior at gmail >

pkgname=mongodb-oldstable
provides=('mongodb')
conflicts=('mongodb')
pkgver=2.2.6
pkgrel=1
pkgdesc='A high-performance, open source, schema-free document-oriented database'
arch=('i686' 'x86_64')
url='http://www.mongodb.org'
license=('AGPL3')
depends=('boost-libs')
makedepends=('scons' 'boost' 'libpcap')
checkdepends=('python2-pymongo')
optdepends=('libpcap: needed for mongosniff')
backup=('etc/mongodb.conf')
install=mongodb.install
source=("http://downloads.mongodb.org/src/mongodb-src-r${pkgver}.tar.gz"
        'mongodb.service' 'mongodb.conf'
        'SConscript.client.patch' 'boost1.50.patch')

build() {
  # fucking mongo aint no fun to package
  export SCONSFLAGS="$MAKEFLAGS"

  cd mongodb-src-r${pkgver}

  # fix https://jira.mongodb.org/browse/SERVER-5575
  patch -Np1 -i ${srcdir}/SConscript.client.patch

  # fix boost incompatibility
  patch -Np1 -i ${srcdir}/boost1.50.patch

  scons all --use-system-boost # --sharedclient currently fails
}

<<COMMENT
check() {
  export SCONSFLAGS="$MAKEFLAGS"

  cd mongodb-src-r${pkgver}

  scons smokeAll --smokedbprefix=$srcdir
}
COMMENT

package() {
  export SCONSFLAGS="$MAKEFLAGS"

  cd mongodb-src-r${pkgver}

  scons install --use-system-boost --full --prefix=$pkgdir/usr # --sharedclient currently fails

  install -Dm644 $srcdir/mongodb.conf $pkgdir/etc/mongodb.conf
  [[ -f $pkgdir/usr/lib ]] && rm $pkgdir/usr/lib # wtf mongo
  install -Dm644 $srcdir/mongodb.service $pkgdir/usr/lib/systemd/system/mongodb.service
  install -dm700 $pkgdir/var/lib/mongodb
  install -dm755 $pkgdir/var/log/mongodb

  # the earlier SConscript patch actually gives us some trouble on x86_64 so we need to hack it out again
  mv ${pkgdir}/usr/lib64/libmongoclient.a ${pkgdir}/usr/lib/ || true
  rm -r ${pkgdir}/usr/lib64 || true

}
md5sums=('63f320ef4e5e1bca4ff4a1495c9d0d30'
         '96ab4517b48974ce0e566d9746a75a4f'
         '4839fe1d638187ca3226e8267b947318'
         'a9529e2a6e392ffecef7a9178394c814'
         'da8a9d78e4a38d1ed8e92a210d4b5ba7')