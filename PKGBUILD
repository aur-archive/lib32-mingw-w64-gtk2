pkgname=lib32-mingw-w64-gtk2
pkgver=2.24.14
pkgrel=1
pkgdesc="GTK+ is a multi-platform toolkit (v2) (mingw-w64, 32-bit)"
arch=(any)
url="http://www.gtk.org"
license=("LGPL")
makedepends=(mingw-w64-gcc mingw-w64-pkg-config)
depends=(mingw-w64-crt
mingw-w64-cairo
mingw-w64-gdk-pixbuf2
mingw-w64-glib2
mingw-w64-pango
mingw-w64-atk)
options=(!libtool !strip !buildflags)
source=("http://ftp.gnome.org/pub/gnome/sources/gtk+/${pkgver%.*}/gtk+-${pkgver}.tar.xz")
md5sums=('e2c16f119ed624893ecfc3775930ae4c')

_architectures="i686-w64-mingw32"

_optimal_make_jobs() {
  if [ -r /proc/cpuinfo ]; then
    local core_count=$(grep -Fc processor /proc/cpuinfo)
  else
    local core_count=0
  fi
  if [ $core_count -gt 1 ]; then
    echo -n $[$core_count-1]
  else
    echo -n 1
  fi
}

build() {
  for _arch in ${_architectures}; do
    unset LDFLAGS
    export CFLAGS="-O2 -mms-bitfields"
    export CXXFLAGS="${CFLAGS}"
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    ${srcdir}/gtk+-${pkgver}/configure \
      --prefix=/usr/${_arch} \
      --build=$CHOST \
      --host=${_arch} \
      --disable-modules \
      --disable-cups \
      --with-gdktarget=win32 \
      --disable-glibtest \
      --disable-gtk-doc-html
    make -j$(_optimal_make_jobs)
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    make DESTDIR="$pkgdir" install
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' -o -name '*.manifest' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
    rm "$pkgdir/usr/${_arch}/bin/gtk-builder-convert"
    rm -r "$pkgdir/usr/${_arch}/"{etc,share/{aclocal,gtk-2.0,locale}}
  done
}
