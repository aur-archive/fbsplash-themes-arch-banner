#!/bin/bash

# Maintainer: Kurt J. Bosch <kjb-temp-2009 at alpenjodel.de>

pkgdesc="Fbsplash themes with Arch Linux banner logos"
pkgname=fbsplash-themes-arch-banner
pkgver=1.5
pkgrel=6

_screen_sizes=(
	640x480 800x600 1024x768 1152x864 1152x900 1280x960 1280x1024 1400x1050 1600x1200
	1280x720 1280x768 1280x800 1280x854 1366x768 1440x900 1680x1050 1920x1080 1920x1200
)
arch=('any')
license=("GPL" "CCPL:cc-by-nc-sa" "custom:ArchLinux-trademarks" "custom:Vera-copyright")
_theme_license="CCPL:cc-by-nc-sa, Arch Linux Trademarks, Bitstream Vera Fonts Copyright"
url="http://aur.archlinux.org/packages.php?ID=26966"
depends=(
	'fbsplash>=1.5.4.4-10'
)
makedepends=(
	'archlinux-artwork>=1.6' 'ttf-dejavu' 'ttf-liberation'
	'imagemagick' 'librsvg'
)
optdepends=(
	'fbsplash-extras: Show icons for daemons and pseudo-services'
	'tango-icon-theme: Default configuration icons for services/daemons'
	'uswsusp-fbsplash: Suspend/resume with Fbsplash'
)
conflicts=( ${pkgname}-{common,normal,wide,icons,noicons} )
replaces=( ${pkgname}-{common,normal,wide,icons,noicons} )
backup=( etc/splash/arch-banner-icons/icons.conf )
install=INSTALL
changelog=CHANGELOG
_font=Vera.ttf
_vera_ver=1.10
source=(
	make-theme.sh
	icons.conf
	cache-icons
	svc_event
	http://ftp.gnome.org/pub/GNOME/sources/ttf-bitstream-vera/${_vera_ver}/ttf-bitstream-vera-${_vera_ver}.tar.bz2
	Screenshot-noicons.png
	Screenshot-noicons-error.png
	Screenshot-icons-Tango.png
	Screenshot-icons-Tango-error.png
	Screenshot-grad1-Tango.png
	Screenshot-grad2-Tango.png
	Screenshot-mono-Tango.png
	Screenshot-outline-Tango.png
)
_font_size=13
# daemon icons to use - also change values in cache-icons and comments in icons.conf !
_icon_size=32
_icon_size_small=16
_share_dir=/usr/share/${pkgname}

build() {
	cd "${srcdir}"

	rm -rf build
	mkdir build
	cd build

	# get in font file
	cp -a ../ttf-bitstream-vera-${_vera_ver}/${_font} ${_font}

	local args=( $_font $_font_size $_share_dir ${pkgver} "${url}" "${_theme_license}" "${_screen_sizes[@]}" )

	# Light blue themes
	# Keep the old noicons/icons theme names for backward compatibility !

	local flavour=official
	local theme_name="arch-banner-icons"
	msg2 "Creating theme: ${theme_name}"
	../make-theme.sh $theme_name $flavour light blue mono $_icon_size "${args[@]}"
	install -D ../icons.conf arch-banner-icons/icons.conf

	local theme_name="arch-banner-noicons"
	msg2 "Creating theme: ${theme_name}"
	mkdir "${theme_name}"
	cd "${theme_name}"
	ln -s ../arch-banner-icons/* ./
	rm -f icons.conf scripts
	cd ..

	local flavour
	for flavour in grad1 grad2; do
		local theme_name="arch-banner-${flavour}"
		msg2 "Creating theme: ${theme_name}"
		../make-theme.sh $theme_name $flavour light blue mono $_icon_size "${args[@]}"
		ln -s ../arch-banner-icons/{icons.conf,scripts} $theme_name/
	done

	# White themes

#	local flavour
#	for flavour in mono outline; do
#		local theme_name="arch-banner-${flavour}"
#		msg2 "Creating theme: ${theme_name}"
#		../make-theme.sh $theme_name $flavour white white $flavour $_icon_size "${args[@]}"
#		ln -s ../arch-banner-icons/{icons.conf,scripts} $theme_name/
#	done
}

package() {
	msg2 "Installing theme files"
	mkdir -p "${pkgdir}"/etc/splash
	mkdir -p "${pkgdir}"$_share_dir
	cd "${srcdir}"/build
	local file
	for file in *; do
		case $file
		in arch-banner-* ) cp -a "$file" "${pkgdir}"/etc/splash/
		;;             * ) cp -a "$file" "${pkgdir}"$_share_dir/
		esac
	done
	# fix permissions
	find "${pkgdir}" -type f -exec chmod 644 {} \;

	msg2 "Installing hook scripts"
	cd "${pkgdir}"/etc/splash/arch-banner-icons
	mkdir scripts
	cd scripts
	install -m755 "${srcdir}"/{cache-icons,svc_event} .
	ln -sT cache-icons rc_init-pre
	ln -sT cache-icons rc_init-post
	local hook
	for hook in \
		svc_{start,started,start_failed}-pre \
		svc_{stop,stopped,stop_failed}-pre # svc_inactive_{start,stop}-pre
	do
		# with fbsplash-extras we support hooks sourcing for speed up
		ln -sT svc_event ${hook}.sh
		# old style executable hooks are still needed for splash_manager replay
		ln -sT svc_event ${hook}
	done

	msg2 "Installing screenshots"
	mkdir -p "${pkgdir}"/usr/share/doc/${pkgname}
	cd "${pkgdir}"/etc/splash/
	local theme_name
	for theme_name in arch-banner-*; do
		if [[ $theme_name = *-noicons ]]
		then install -m644 "${srcdir}"/Screenshot-${theme_name#arch-banner-}.png "${pkgdir}"/usr/share/doc/${pkgname}/
		else install -m644 "${srcdir}"/Screenshot-${theme_name#arch-banner-}-Tango.png "${pkgdir}"/usr/share/doc/${pkgname}/
		fi
		local file
		for file in ${theme_name}/images/verbose*.png; do # prefere 1024x786 or bigger
			ln -s /etc/splash/"${file}" "${pkgdir}"/usr/share/doc/${pkgname}/Verbose-background-${theme_name}.png
			break
		done
	done
	install -m644 "${srcdir}"/Screenshot-icons-Tango-error.png "${pkgdir}"/usr/share/doc/${pkgname}/

	msg2 "Installing license files"
	mkdir -p "${pkgdir}"/usr/share/licenses/${pkgname}
	cd "${pkgdir}"/usr/share/licenses/${pkgname}
	install -m644 /usr/share/licenses/archlinux-artwork/TRADEMARKS ArchLinux-trademarks
	install -m644 "${srcdir}"/ttf-bitstream-vera-${_vera_ver}/COPYRIGHT.TXT Vera-copyright
}

md5sums=('ce4f6aa766a781bd7cedf429984a4698'
         '3db6762f95f4c7270b403599fa16f0d3'
         '04a86f6124b7d2e46c2de1385166febb'
         '942e1fdbe0e4b71621d8e2dabcb28d2c'
         'bb22bd5b4675f5dbe17c6963d8c00ed6'
         '0a2567cbc3e934df5055401db7047628'
         '0cacfc60921b67dc86010c55033329ea'
         'a33671a6c41615949b049b434e74fc63'
         '608624f70a822d2a819a33b66137c9b2'
         '19aa8a643aa379fc9296c44b4ecfa510'
         '5370461c7914ef566b711c5ce1e65add'
         '804f10c7a6efc7664c799a89274cb25b'
         '9674d83e6a3d632d3e24c3942ee98a5e')
