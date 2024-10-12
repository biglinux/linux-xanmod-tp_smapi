# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Maintainer: Bernhard Landauer <bernhard@manjaro.org>

# Arch credits:
# Maintainer: Lukas Fleischer <archlinux at cryptocrack dot de>
# Contributor: xduugu
# Contributor: nh2
# Contributor: Steven Davidovitz <steviedizzle ð gmail đ com>
# Contributor: Nick B <Shirakawasuna ð gmail đ com>
# Contributor: Christof Musik <christof ð senfdax đ de>
# Contributor: Stefan Rupp <archlinux ð stefanrupp đ de>
# Contributor: Ignas Anikevicius <anikevicius ð gmail đ com>

_linuxprefix=linux-xanmod

_module=tp_smapi
pkgname="${_linuxprefix}-${_module}"
pkgver=0.44
pkgrel=61132
pkgdesc="Modules for ThinkPad's SMAPI functionality"
arch=('x86_64')
url='https://github.com/evgeni/tp_smapi'
license=('GPL')
groups=("${_linuxprefix}-extramodules")
depends=("${_linuxprefix}")
makedepends=('git' "${_linuxprefix}-headers")
provides=("${_module}=$pkgver")
_commit=6e80bb1752280bcd142d86ecd0739661bd0e8312  # tags/tp-smapi/0.44^0
source=("git+https://github.com/evgeni/tp_smapi#commit=$_commit")
sha256sums=('SKIP')

pkgver() {
  cd "${_module}"
  git describe --tags | sed 's/^tp-smapi\///;s/[^-]*-g/r&/;s/-/+/g'
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "${_module}"

  # https://bugs.archlinux.org/task/54975 (kernel has no _GLOBAL_OFFSET_TABLE_):
  # Clear EXTRA_CFLAGS since it defaults to injecting CFLAGS and -fno-plt breaks the modules
  make HDAPS=1 KVER="${_kernver}" EXTRA_CFLAGS=
}

package() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  find . -name "*.ko" -exec install -Dt "$pkgdir/usr/lib/modules/${_kernver}/extramodules" {} +
  find "$pkgdir" -name "*.ko" -exec strip --strip-debug {} +
  find "$pkgdir" -name "*.ko" -exec xz {} +

  # load module on startup
  echo tp_smapi | install -Dm644 /dev/stdin "$pkgdir/usr/lib/modules-load.d/$pkgname.conf"
}
