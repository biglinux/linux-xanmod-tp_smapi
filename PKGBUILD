# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Maintainer: Lukas Fleischer <archlinux at cryptocrack dot de>
# Contributor: xduugu
# Contributor: nh2
# Contributor: Steven Davidovitz <steviedizzle ð gmail đ com>
# Contributor: Nick B <Shirakawasuna ð gmail đ com>
# Contributor: Christof Musik <christof ð senfdax đ de>
# Contributor: Stefan Rupp <archlinux ð stefanrupp đ de>
# Contributor: Ignas Anikevicius <anikevicius ð gmail đ com>

_linuxprefix=linux-xanmod
_pkgname=tp_smapi
_kernver="$(cat /usr/src/${_linuxprefix}/version)"
_extramodules=$(find /usr/lib/modules -type d -iname 6.7.5*xanmod* | rev | cut -d "/" -f1 | rev)
pkgname=$_linuxprefix-tp_smapi
pkgver=0.44
pkgrel=67510
pkgdesc="Modules for ThinkPad's SMAPI functionality"
arch=('x86_64')
url='https://github.com/evgeni/tp_smapi'
license=('GPL')
depends=("$_linuxprefix")
makedepends=('git' "$_linuxprefix-headers")
groups=("$_linuxprefix-extramodules")
_commit=6e80bb1752280bcd142d86ecd0739661bd0e8312  # tags/tp-smapi/0.44
source=("git+https://github.com/evgeni/tp_smapi#commit=$_commit")
sha256sums=('SKIP')

pkgver() {
  cd $_pkgname
  git describe --tags | sed 's/^tp-smapi\///;s/[^-]*-g/r&/;s/-/+/g'
}

prepare() {
  cd $_pkgname

  local src
  for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      msg2 "Applying patch: $src..."
      patch -Np1 < "../$src"
  done
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"
  cd $_pkgname

  # https://bugs.archlinux.org/task/54975 (kernel has no _GLOBAL_OFFSET_TABLE_):
  # Clear EXTRA_CFLAGS since it defaults to injecting CFLAGS and -fno-plt breaks the modules
  make HDAPS=1 KVER="$_kernver" EXTRA_CFLAGS=
}

package() {
  # install kernel modules
  find . -name "*.ko" -exec install -Dt "$pkgdir$_extramodules" {} +

  # compress kernel modules
  find "$pkgdir" -name "*.ko" -exec strip {} +
  find "$pkgdir" -name "*.ko" -exec xz {} +

  # load module on startup
  echo tp_smapi > "../${_pkgname}.conf"
  install -Dm 644 "../${_pkgname}.conf" "${pkgdir}/usr/lib/modules-load.d/${pkgname}.conf"
}
