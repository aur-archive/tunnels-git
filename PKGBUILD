# Maintainer: Etienne Perot <etienne at perot dot me>
pkgname=tunnels-git
pkgver=20130503
pkgrel=1
pkgdesc='Transparent, system-wide, domain-name-specific proxying rules'
arch=('any')
url='https://github.com/EtiennePerot/tunnels'
license=('LGPL')
depends=('bash' 'python2' 'python2-paramiko' 'python2-yaml')
makedepends=('git' 'svn')
source=(
	'https://socksipy-branch.googlecode.com/svn-history/r19/trunk/socks.py'
	'https://pypi.python.org/packages/source/p/python-daemon/python-daemon-1.6.tar.gz'
	'https://pylockfile.googlecode.com/files/lockfile-0.9.1.tar.gz'
)
sha512sums=(
	'58e7393b365ad5bde308cb2290224c15dfce82c0dc8e3209b8c4fea9876d1afa5bebfd184ea5e9d577d1494037f78bf5013a64d7ad21b5ed4b7405fb4a3a86dd'
	'ff9480844d8f57a09f27067aaa78669c167f66b2cc05f9b3baa660bc0aa21228d4894eeaeb3440611c1e0f0d5633b1c60eac60893da4a86d1db4b94d99961299'
	'1f7e2b13c42df730339e653a361bf4b85a289d62ed9277f159ab454b1e951d922884086299912472236ce0772d5eceebab7e0c6407590bb2ccbe9c56b664de05'
)

_gitroot='git://perot.me/tunnels'
_gitname='tunnels'

build() {
	cd "$srcdir"
	msg "Connecting to GIT server...."

	if [[ -d "$_gitname" ]]; then
		cd "$_gitname" && git pull origin
		msg "The local files are updated."
	else
		git clone "$_gitroot" "$_gitname"
	fi

	msg "Starting build..."

	rm -rf "$srcdir/$_gitname-build"
	git clone "$srcdir/$_gitname" "$srcdir/$_gitname-build"
	cd "$srcdir/$_gitname-build"
	cp -ra "$srcdir/socks.py" "$srcdir"/python-daemon-*/daemon "$srcdir"/lockfile-*/lockfile src/
	python util/mkportslist.py > conf.d.sample/autogenerated-ports.yml
}

package() {
	cd "$srcdir/$_gitname-build"
	install -D -m644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
	install -D -m644 pkg/resources/tunnels.service "$pkgdir/usr/lib/systemd/system/tunnels.service"
	mkdir -p "$pkgdir/usr/lib/tunnels"
	for target in conf.d.sample src tunnels tunnelsd; do
		cp -ra "$target" "$pkgdir/usr/lib/tunnels/"
	done
	mkdir -p "$pkgdir/etc/tunnels.d"
	cp -a conf.d.sample/* "$pkgdir/etc/tunnels.d/"
	mkdir -p "${pkgdir}/usr/bin"
	ln -sf /usr/lib/tunnels/tunnels  "$pkgdir/usr/bin/tunnels"
	ln -sf /usr/lib/tunnels/tunnelsd "$pkgdir/usr/bin/tunnelsd"
}
