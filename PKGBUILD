# Maintainer: YuutaW <i@yuuta.moe>
pkgname='rsshub-git'
pkgver=r5817.9af9e5807
pkgrel=1
epoch=
pkgdesc="Everything is RSSible"
arch=('any')
url="https://rsshub.app/"
license=('MIT')
depends=('nodejs')
makedepends=('npm' 'git')
checkdepends=()
optdepends=(
  'chromium: for routes using a headless browser'
)
backup=('etc/rsshub/rsshub.conf')
source=("$pkgname::git+https://github.com/DIYgod/RSSHub.git"
	"rsshub.conf"
	"rsshub.service"
    "rsshub_sysuser.conf"
    "rsshub_tmpfile.conf")
noextract=()
md5sums=('SKIP'
         '207c040f122e73f5782a75920b46b106'
         '079e30a1e74485a571b104dfb5df0b49'
         '6a6a769bf29d4281b36d7826e26a1117'
         'd5e489cdc03b59987e8f1385210d2010')
pkgver() {
    cd "$srcdir/$pkgname"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
	cd "$srcdir/$pkgname"
	# Remove unnecessary directory
	rm -rf .github
	rm -rf docs
	rm -rf test
}

build() {
	cd "$srcdir/$pkgname"
	PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true npm install --verbose --production --cache "${srcdir}/npm-cache"

	# Cleanup node modules, as Dockerfile does.
	chmod +x ./tools/clean-nm.sh
	./tools/clean-nm.sh
}

package() {
	cd "$srcdir/$pkgname"
	mkdir -p $pkgdir/opt/rsshub
	cp -R ./* $pkgdir/opt/rsshub/
 	chown -R root:root "${pkgdir}/opt/"
 	find "${pkgdir}/opt" -type d -exec chmod 755 {} +

	mkdir -p $pkgdir/etc/rsshub
	install -Dm644 $srcdir/rsshub.conf $pkgdir/etc/rsshub/rsshub.conf

	mkdir -p $pkgdir/usr/share/licenses/rsshub/
	install -Dm644 ./LICENSE $pkgdir/usr/share/licenses/rsshub/LICENSE

    # TODO: We still have several JS which contains the $srcdir
	find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

	mkdir -p $pkgdir/usr/lib/systemd/system
	install -Dm644 $srcdir/rsshub.service $pkgdir/usr/lib/systemd/system/rsshub.service

	mkdir -p $pkgdir/usr/lib/sysusers.d/
    install -Dm644 $srcdir/rsshub_sysuser.conf $pkgdir/usr/lib/sysusers.d/rsshub.conf

	mkdir -p $pkgdir/usr/lib/tmpfiles.d/
    install -Dm644 $srcdir/rsshub_tmpfile.conf $pkgdir/usr/lib/tmpfiles.d/rsshub.conf
}
