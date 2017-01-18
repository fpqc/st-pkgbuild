# Maintainer: mar77i <mar77i at mar77i dot ch>
# Past Maintainer: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Scytrin dai Kinthra <scytrin@gmail.com>

pkgname=st-git-tic
_pkgname=st-tmux-clip
pkgver=0.7
pkgrel=1
pkgdesc='Simple virtual terminal emulator for X'
url='http://st.suckless.org/'
arch=('i686' 'x86_64')
license=('MIT')
options=('zipman')
depends=('libxft')
makedepends=('ncurses' 'libxext' 'git')
epoch=1
# include config.h and any patches you want to have applied here
source=('git://github.com/fpqc/st-tmux-clip'
	'fpqc.diff'
	'solarized-dark.diff'
	'colorfix.diff'
	)
sha256sums=('SKIP'
            'bde0613f264352d1c0ff45647514059a261b7ed594377f51191e4e5014b8ab4f'
            '4782f52c4147a352579586c1b066f9fd1da934e580cbd3b78943f058394d9883'
            '2e8cdbeaaa79ed067ffcfdcf4c5f09fb5c8c984906cde97226d4dd219dda39dc')

provides=("${_pkgname}")
conflicts=("${_pkgname}")

pkgver() {
	cd "${_pkgname}"
	git describe --tags |sed 's/-/./g'
}

prepare() {
	local file
	cd "${_pkgname}"

	# Rename the terminfo files to our custom ones
	sed -e '/| \|use=/s/st/st-git/' -i st.info
	# Set the default terminfo to our custom one
	sed -e 's/st-256color/st-git-256color/' -i config.def.h
	#Manually compile our own terminfo
	mkdir -p  "terminfo"
	tic -sx "st.info" -o "terminfo"
	#Remove the makefile's defective terminfo compilation
	sed '/@tic/d' -i Makefile

        for file in "${source[@]}"; do
                if [[ "$file" == "config.h" ]]; then
                        # add config.h if present in source array
                        # Note: this supersedes the above sed to config.def.h
                        cp "$srcdir/$file" .
                elif [[ "$file" == *.diff || "$file" == *.patch ]]; then
                        # add all patches present in source array
                        patch -Np1 <"$srcdir/$(basename ${file})"
                fi
        done

}

build() {
	cd "${_pkgname}"
	make
}

package() {
	cd "${_pkgname}"
	make PREFIX=/usr DESTDIR="${pkgdir}" install
	install -d "${pkgdir}/usr/share/terminfo/s"
	install -Dm644 terminfo/s/* "${pkgdir}/usr/share/terminfo/s"
	install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
	install -Dm644 README "${pkgdir}/usr/share/doc/${pkgname}/README"
}
