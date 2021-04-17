# Maintainer : Ultimate Hosts Blacklist contributors
# Maintainer : Nissar Chababy <contacttafunilrystodcom>
# Contributor : Samuel Williams <samuel@oriontransfer.net>
# Contributor : Edvinas Valatka <edacval@gmail.com>
# Contributor : Jingbei Li <i@jingbei.li>

_pkgname=github-actions
pkgname=${_pkgname}-bin
pkgver=2.278.0
pkgrel=1
pkgdesc='GitHub Actions self-hosted runner tools.'
arch=('x86_64' 'armv6h' 'armv7h' 'aarch64')
url='https://github.com/actions/runner'
license=('MIT')

OPTIONS=(!strip !docs libtool emptydirs)

install=PKGBUILD

provides=($_pkgname)
conflicts=($_pkgname)
_common_source=(github-actions.service github-actions.tmpfiles github-actions.sysusers)
source=(
       "https://github.com/actions/runner/releases/download/v$pkgver/actions-runner-linux-x64-$pkgver.tar.gz"
       ${_common_source[@]}
)
source_armv6h=(
       "https://github.com/actions/runner/releases/download/v$pkgver/actions-runner-linux-arm-$pkgver.tar.gz"
       ${_common_source[@]}
)
source_armv7h=(${source_armv6h[@]})
source_aarch64=(
       "https://github.com/actions/runner/releases/download/v$pkgver/actions-runner-linux-arm64-$pkgver.tar.gz"
       ${_common_source[@]}
)

sha512sums=('121239ada8a565624c34f43358d94ce32b9449c5175c3c1a80a31d2a7d35ab359e0c0bdd94eb88bf9eeca93b1f1b5949d52beb8b93d4b09c3b75ae6f8ba21f21'
            'abeb32b58cd526bb6abe928d978664de85cab5715d93189919412f889a8a1089a11ec1e8bf21e72e96e8640ca85ecb97daff0c797541317dbe4f6f508c39858d'
            'e321a16ac18fca3a918f94c575940bf0c1f303e2490e59ec7814f85063c8bedd5c75f3de257b3342f7682490b0ca1fe7d3c481899a82b19dd22a45b25aa6a9df'
            '49329a3c65987f7bb219100b41deb33fcbc64f5e6424c4e31d580e2fbd408545d2d4a990c5511a3625250bd37ad7d13496cfd152ffd20de04fd24250242088d4')
sha512sums_armv6h=('84f659ddb842fe2b930929f244021659e17fe7afa18ebc3d8285f22ba4ef1f1f827f53dfacab2dcd243a6c46ab5c01261b82dd1aa2531e5f12ee90f803bb1798'
                   'abeb32b58cd526bb6abe928d978664de85cab5715d93189919412f889a8a1089a11ec1e8bf21e72e96e8640ca85ecb97daff0c797541317dbe4f6f508c39858d'
                   'e321a16ac18fca3a918f94c575940bf0c1f303e2490e59ec7814f85063c8bedd5c75f3de257b3342f7682490b0ca1fe7d3c481899a82b19dd22a45b25aa6a9df'
                   '49329a3c65987f7bb219100b41deb33fcbc64f5e6424c4e31d580e2fbd408545d2d4a990c5511a3625250bd37ad7d13496cfd152ffd20de04fd24250242088d4')
sha512sums_armv7h=('84f659ddb842fe2b930929f244021659e17fe7afa18ebc3d8285f22ba4ef1f1f827f53dfacab2dcd243a6c46ab5c01261b82dd1aa2531e5f12ee90f803bb1798'
                   'abeb32b58cd526bb6abe928d978664de85cab5715d93189919412f889a8a1089a11ec1e8bf21e72e96e8640ca85ecb97daff0c797541317dbe4f6f508c39858d'
                   'e321a16ac18fca3a918f94c575940bf0c1f303e2490e59ec7814f85063c8bedd5c75f3de257b3342f7682490b0ca1fe7d3c481899a82b19dd22a45b25aa6a9df'
                   '49329a3c65987f7bb219100b41deb33fcbc64f5e6424c4e31d580e2fbd408545d2d4a990c5511a3625250bd37ad7d13496cfd152ffd20de04fd24250242088d4')
sha512sums_aarch64=('043269c0ec53bc361b6abb200a47d34f09d7c9117c2717da295e17f482425a2b967cfa7a541b28ac362f95e60ed940f72bd068470b595a6211d539631e118f1e'
                    'abeb32b58cd526bb6abe928d978664de85cab5715d93189919412f889a8a1089a11ec1e8bf21e72e96e8640ca85ecb97daff0c797541317dbe4f6f508c39858d'
                    'e321a16ac18fca3a918f94c575940bf0c1f303e2490e59ec7814f85063c8bedd5c75f3de257b3342f7682490b0ca1fe7d3c481899a82b19dd22a45b25aa6a9df'
                    '49329a3c65987f7bb219100b41deb33fcbc64f5e6424c4e31d580e2fbd408545d2d4a990c5511a3625250bd37ad7d13496cfd152ffd20de04fd24250242088d4')

package() {
       depends=(sudo)
       mkdir -p "$pkgdir"/var/lib/$_pkgname

       # Useless on pacman-based distributions
       rm -f "$srcdir"/bin/installdependencies.sh

       cp -r -t "$pkgdir"/var/lib/$_pkgname "$srcdir"/{bin,externals,*.sh}

       # also see github-actions.tmpfiles
       chmod 0775 "$pkgdir"/var/lib/$_pkgname

       # make ldd happy
       chmod +x "$pkgdir"/var/lib/$_pkgname/bin/*.so

       install -Dm644 "$srcdir"/$_pkgname.tmpfiles "${pkgdir}"/usr/lib/tmpfiles.d/$_pkgname.conf
       install -Dm644 "$srcdir"/$_pkgname.sysusers "${pkgdir}"/usr/lib/sysusers.d/$_pkgname.conf
       install -Dm644 "$srcdir"/$_pkgname.service  "${pkgdir}"/usr/lib/systemd/system/$_pkgname.service
}

pre_remove() {
       if systemctl -q is-enabled $_pkgname.service; then
              systemctl disable $_pkgname.service
       fi
}

post_remove() {
       echo
       echo "Remove $_pkgname user and this HOME /var/lib/$_pkgname manually, if not needed anymore."
       echo
}

