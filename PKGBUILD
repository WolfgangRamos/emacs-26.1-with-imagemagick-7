# Maintainer: Haroogan <Haroogan@gmail.com>
# Maintainer: Oscar Fuentes <ofv@wanadoo.es>

_realname=emacs
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}"
pkgver=26.1
pkgrel=1
pkgdesc="The extensible, customizable, self-documenting, real-time display editor (mingw-w64)"
url="https://www.gnu.org/software/${_realname}/"
license=('GPL3')
arch=('any')
depends=("${MINGW_PACKAGE_PREFIX}-ctags"
         "${MINGW_PACKAGE_PREFIX}-zlib"
         "${MINGW_PACKAGE_PREFIX}-xpm-nox"
         "${MINGW_PACKAGE_PREFIX}-dbus"
         "${MINGW_PACKAGE_PREFIX}-gnutls"
         "${MINGW_PACKAGE_PREFIX}-imagemagick"
         "${MINGW_PACKAGE_PREFIX}-libwinpthread")
optdepends=("${MINGW_PACKAGE_PREFIX}-giflib"
            "${MINGW_PACKAGE_PREFIX}-libjpeg-turbo"
            "${MINGW_PACKAGE_PREFIX}-libpng"
            "${MINGW_PACKAGE_PREFIX}-librsvg"
            "${MINGW_PACKAGE_PREFIX}-libtiff"
            "${MINGW_PACKAGE_PREFIX}-libxml2")
makedepends=("${MINGW_PACKAGE_PREFIX}-gcc"
             "${MINGW_PACKAGE_PREFIX}-pkg-config"
             "patch")
# Don't zip info files because the built-in info reader uses gzip to
# decompress them. gzip is not available as a mingw binary.
options=('strip' '!zipman')
source=("https://ftp.gnu.org/gnu/${_realname}/${_realname}-${pkgver}.tar.xz"{,.sig}
        '001-emacs-26.1-imagemagick-7.patch'
        'lread.c.diff')
sha256sums=('1cf4fc240cd77c25309d15e18593789c8dbfba5c2b44d8f77c886542300fd32c'
            'SKIP'
			'964ef878dd96f506d49df54207e82d99c342c0059807f75f6cf81548e2bda262'
            '24b2201aa2e965c759cfc9539aa4b610d97f1d0240d9888e335da490b87b84b5')

prepare() {
  cd "${_realname}-${pkgver}"
  patch -p1 < "${srcdir}/001-emacs-26.1-imagemagick-7.patch"
  patch --binary --forward -p0 < "${srcdir}/lread.c.diff"
  ./autogen.sh
}

build() {
  [[ -d "${srcdir}/build-${MINGW_CHOST}" ]] && rm -rf "${srcdir}/build-${MINGW_CHOST}"
  mkdir -p "${srcdir}/build-${MINGW_CHOST}"
  cd "build-${MINGW_CHOST}"

  ../${_realname}-${pkgver}/configure \
    --prefix="${MINGW_PREFIX}" \
    --build="${MINGW_CHOST}" \
    --with-modules \
    --without-compress-install

  # --without-compress-install is needed because we don't have gzip in
  # the mingw binaries.

  make
}

package() {
  cd "build-${MINGW_CHOST}"
  make DESTDIR="${pkgdir}" install
  rm -f "${pkgdir}${MINGW_PREFIX}/bin/ctags.exe"
  rm -f "${pkgdir}${MINGW_PREFIX}/share/man/man1/ctags.1.gz"

  local dir="${pkgdir}${MINGW_PREFIX}/share/${_realname}"
        dir="${dir}/$(ls -1 ${dir} | grep -E '([0-9]+\.[0-9]+)(\.[0-9]+)?')/src"

  mkdir -p "${dir}"
  cd "${srcdir}/${_realname}-${pkgver}/src"
  cp *.c *.h *.m "${dir}"
}

# TODO:
# Patch `shell-file-name' default in the C source code similarly to
# `source-directory'.
