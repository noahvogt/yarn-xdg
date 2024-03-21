# Maintainer: Noah Vogt (noahvogt) <noah@noahvogt.com>
# Maintainer: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: Jan Koppe <post@jankoppe.de>

pkgname=yarn-xdg
pkgver=1.22.22
pkgrel=1
pkgdesc='Fast, reliable, and secure dependency management - with xdg basedir patches'
provides=('yarn')
conflicts=('yarn')
arch=('any')
license=('BSD-2-Clause')
depends=('nodejs')
makedepends=('git' 'jq' 'yarn')
source=("git+https://github.com/${pkgname%-*}pkg/${pkgname%-*}.git#tag=v$pkgver"
        "remove-legacy-yarnrc-path.patch")
b2sums=('SKIP'
        '8a02d546bbcef7353887fef2de70061526313370cd3c383e08d5535d4574647b41c1251e3c1c8324944de8a534ec4f6dca9f3014aacc957f18053f7a7517f3b3')

prepare() {
  cd ${pkgname%-*}
  patch -p1 -i "$srcdir/remove-legacy-yarnrc-path.patch"
}

build() {
  cd ${pkgname%-*}
  yarn --frozen-lockfile
  yarn build
}

package() {
  local mod_dir=/usr/lib/node_modules/${pkgname%-*}
  install -d  "$pkgdir"/{usr/bin,$mod_dir/bin}
  ln -s $mod_dir/bin/${pkgname%-*}.js "$pkgdir"/usr/bin/${pkgname%-*}
  ln -s $mod_dir/bin/${pkgname%-*}.js "$pkgdir"/usr/bin/${pkgname%-*}pkg

  cd ${pkgname%-*}
  # Prune unnecessary packages
  cp package.json{,.bak}
  read -ra devDependencies < <(jq -r '.devDependencies | keys | join(" ")' package.json)
  yarn remove --frozen-lockfile "${devDependencies[@]}"
  mv package.json{.bak,}

  cp -r lib node_modules package.json "$pkgdir"/$mod_dir
  install -t "$pkgdir"/$mod_dir/bin bin/${pkgname%-*}.js
  install -Dm644 -t "$pkgdir"/usr/share/doc/${pkgname%-*} README.md
  install -Dm644 -t "$pkgdir"/usr/share/licenses/${pkgname%-*} LICENSE
}
