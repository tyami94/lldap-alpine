pkgname=lldap
pkgver=0.6.1
pkgrel=0
pkgdesc="Light LDAP implementation for authentication"
url="https://github.com/lldap/lldap"
arch="all"
license="GPL-3.0"
pkgusers="lldap"
pkggroups="lldap"
depends=
depends_dev=
makedepends="cargo wasm-pack openssl-dev perl curl gzip"
install="
		$pkgname.pre-install
		$pkgname.pre-upgrade
"
subpackages="$pkgname-openrc"
options="net"
source="$pkgname-$pkgver.tar.gz::https://github.com/lldap/lldap/archive/refs/tags/v$pkgver.tar.gz
		lldap.initd
		lldap.confd
		00-enable-custom-assets-path.patch
		01-config-defaults.patch		
"

builddir="$srcdir"/$pkgname-$pkgver

build() {
	cargo build --release -p lldap -p lldap_migration_tool -p lldap_set_password
	# Need to unset RUSTFLAGS otherwise the front-end wasm won't build
	RUSTFLAGS='' ./app/build.sh
	# Need to retrieve static dependencies for web app.
	for file in $(cat "$builddir"/app/static/libraries.txt); do wget -P "$builddir"/app/static "$file"; done
	for file in $(cat "$builddir"/app/static/fonts/fonts.txt); do wget -P "$builddir"/app/static/fonts "$file"; done
}

package() {
    install -m755 -D "$builddir"/target/release/lldap "$pkgdir"/usr/bin/lldap
    install -m755 -D "$builddir"/target/release/lldap_migration_tool "$pkgdir"/usr/bin/lldap_migration_tool
    install -m755 -D "$builddir"/target/release/lldap_set_password "$pkgdir"/usr/bin/lldap_set_password
    install -m640 -D "$builddir"/lldap_config.docker_template.toml "$pkgdir"/etc/lldap.toml
    # Default index.html uses assets from 3rd party CDN's, so we don't package it.
    install -m644 -D "$builddir"/app/index_local.html "$pkgdir"/usr/share/webapps/lldap/index.html
    (cd "$builddir"/app/static/; find . -type f -exec \
		install -m644 -D {} "$pkgdir"/usr/share/webapps/lldap/static/{} \;)
	(cd "$builddir"/app/pkg/; find . -type f -exec \
		install -m644 -D {} "$pkgdir"/usr/share/webapps/lldap/pkg/{} \;)
    install -m755 -D "$srcdir"/$pkgname.initd "$pkgdir"/etc/init.d/$pkgname
    install -m644 -D "$srcdir"/$pkgname.confd "$pkgdir"/etc/conf.d/$pkgname
}

check() {
    cargo test
}

sha512sums="
35a7abd18167453e79cf8abe62dc4440afe1ef7ccb6d7880380d4fca0f3d6f314ec090d82ad36941c466a154e9921be8be0da8fc728f90720391e2c421bc41e7  lldap-0.6.1.tar.gz
6dbd032ff283a922304ca0dc91e5d817c62dd7fcbfd56339e92ed4e08a489c9a9bae81110e5ec2254a923161107f60a2f91a897ea33e50310eaa411b72a91c54  lldap.initd
bdcccf554e850a1b0eda81a1b6f4bd83bfcbf67b6f1f257076589ac069357dc5b7767cc8a391dc5cb58362dc59d8d29fb3df9830994134d5dfb3fbaac28bf203  lldap.confd
457d4ae0f663c59c242c5434db480bbd142c12c45ebfdcedc0acf4d626a989605863e08a5a83db36b22ea1b5c9aa816d337523390db4feab8c2b3cb4e61287c7  00-enable-custom-assets-path.patch
c7545d7c5c274ea31673d26011946af88921975487d2c130d70beff5bf5aac932a5a57a3ecaf84739ed6cfaa50c915fcb4798f7983e61344e006e5844f0d8ae0  01-config-defaults.patch
"
