#!/bin/bash
# Maintainer: NuSkool
# linux-stable-rc 2025-03-12
# Credits: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
#
# Depends on AUR package 'modprobed-db' being setup and installed to eliminate unneeded module compile time.
# To build all modules search and comment out line containing 'HOME' in 'prepare' function.
# Builds 'stable' version listed: 'kernel.org', -rc linux kernel.
# Info: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/
# Info: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/log/?h=linux-6.13.y
# Info: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/commit/?h=linux-6.13.y

#-------------------------------------------------------------------------------------------------------------------------------------
pkgbase=linux-stable-rc
pkgver=6.13
pkgrel=0
pkgdesc='Current "stable" version of Linux -rc'
url=kernel.org
license=(GPL-2.0-only)
arch=(x86_64)

makedepends=(
	bc
	cpio
	gettext
	graphviz
	imagemagick
	libelf
	pahole
	perl
	python
	python-sphinx
	python-yaml
	rust
	rust-bindgen
	rust-src
	tar
	texlive-latexextra
	xz
	)

options=(
	!debug
	!strip
	)

#-------------------------------------------------------------------------------------------------------------------------------------
# SOURCE:
# kernel
# config
# remove-rust

_version=$(curl -s "https://kernel.org/" | awk -F'linux-|.tar' '/downloadarrow/ {split($2, a, "\""); print a[1]}')

source=(https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/snapshot/linux-stable-rc-linux-"${_version%.*}".y.tar.gz
	arch-config::https://gitlab.archlinux.org/archlinux/packaging/packages/linux/-/raw/main/config?ref_type=heads
	remove-rust::https://raw.githubusercontent.com/Cody-Learner/linux-stable-rc/main/remove-rust
	config)

#-------------------------------------------------------------------------------------------------------------------------------------
validpgpkeys=(ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
              647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
              83BC8889351B5DEBBB68416EB8AC08600F108CDF) # Jan Alexander Steffens (heftig)

#-------------------------------------------------------------------------------------------------------------------------------------
			# sha256sum linux-stable*gz config remove-rust

sha256sums=('4a6de23a34f0036ad87ce92fa37ec74296e07526f45b02845fd369901dac38b8'
            '9d02a908be9f0a408af5166c245509763864b0a037e7c5b14ba3a546ca597fe9'
            '62fc8201f9bb4f36aaa84184fc008dad2db2fd594d537dd66d9c432576f85d4c'
	    'SKIP')

# sha256sums=(	'SKIP'
#		'SKIP'
#		'SKIP'
#		'SKIP')

#-------------------------------------------------------------------------------------------------------------------------------------
export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER="${pkgbase}"
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

#-------------------------------------------------------------------------------------------------------------------------------------
prepare() {


#......................................................# Begin Additional Code #......................................................#

if	[[ ! -e src/in_process ]]; then				# Prevent running enclosed code during build.

	cd ..
if	[[ ! -e config ]]; then
	cp 'arch-config' 'config'
fi

_verst=$(_vdata='linux-stable-rc-linux-6.13.y.tar.gz'
	tar -xzOf "${_vdata}" "${_vdata%.tar*}"/Makefile | awk -F'= *' '
		/^VERS/ {v1 = $2}
		/^PATC/ {v2 = $2}
		/^SUBL/ {v3 = $2}
		/^EXTR/ {v4 = $2}
	END {print v1 "." v2 "." v3 v4}
	')

export pkgver="${_verst%-rc*}rc"	#ie: 6.13.5rc
export pkgrel="${_verst#*-rc*}"		#ie: 2
_pb=PKGBUILD
_nver=$(awk -F'=' '/^pkgver=/{print $2}' "${_pb}")
_nrel=$(awk -F'=' '/^pkgrel=/{print $2}' "${_pb}")
if	[[ "${_nver}" != "${pkgver}" ]]; then
	sed -i s/^pkgver=6\.13.*/pkgver="${pkgver}"/ "${_pb}"
fi
if	[[ "${_nrel}" != "${pkgrel}" ]]; then
	sed -i s/^pkgrel=[0-9].*/pkgrel="${pkgrel}"/ "${_pb}"
fi
	cat << EOF

	Building linux-stable-rc version: ${_verst}

	You have 3 choices going forward
	 p  to proceed normally with build, '-g', etc...
	 e  to exit now, wrong version, etc.
	 r  to run 'remove-rust' script, possibly useful for build failures

EOF
	while read -n1 -rp "         Press [p/e/r] " reply
	do
		 if [[ ${reply} == p ]]; then echo ; break ; fi ;
		 if [[ ${reply} == r ]]; then echo ; . remove-rust ; break ; fi
	       { if [[ ${reply} == e ]]; then echo ; fi ; exit ; }
	done
	echo
fi

dirname=$(find "${srcdir}"/ -maxdepth 1 -type d -name 'linux*y' -printf "%f\n")

#......................................................# End Additional Code #......................................................#

	cd "${srcdir}"

if	[[ -d  "${srcdir}/${pkgbase}" ]]; then
	rm -rd "${srcdir}/${pkgbase}"
fi
	mv "${dirname}" "${pkgbase}"
	cd "${pkgbase}" || exit
	touch localversion.10-pkgrel
	touch localversion.20-pkgname
	local src
	for src in "${source[@]}"

	do
		src="${src%%::*}"
		src="${src##*/}"
		src="${src%.zst}"						# Leaving in the case repo patches are added later.
		[[ $src = *.patch ]] || continue
		echo "Applying patch $src..."
		patch -Np1 < "../$src"
	done

	echo "Setting config..."

	cp ../config .config

	make olddefconfig

	yes "" | make LSMOD="${HOME}"/.config/modprobed.db localmodconfig	# Comment this out to build all modules

	diff -u ../config .config || :

	make -s kernelrelease > version

	echo "Prepared ${pkgbase} version $(<version)"
}
#-------------------------------------------------------------------------------------------------------------------------------------
pkgver(){
	printf '%s' "${pkgver}"
}
#-------------------------------------------------------------------------------------------------------------------------------------
build() {

	touch  "${srcdir}"/in_process
	cd "${pkgbase}"
	make all
	make -C tools/bpf/bpftool vmlinux.h feature-clang-bpf-co-re=1
}

#-------------------------------------------------------------------------------------------------------------------------------------
_package() {

	pkgdesc="The $pkgdesc kernel and modules"
	depends=(
		coreutils
		initramfs
		kmod
		)

	optdepends=(
		'linux-firmware: firmware images needed for some devices'
		'scx-scheds: to use sched-ext schedulers'
		'wireless-regdb: to set the correct wireless channels of your country'
		)

	provides=(
		KSMBD-MODULE
		VIRTUALBOX-GUEST-MODULES
		WIREGUARD-MODULE
		)

	replaces=(
		virtualbox-guest-modules-arch
		wireguard-arch
		)

	cd "${pkgbase}"

	local modulesdir="${pkgdir}/usr/lib/modules/$(<version)"

	echo "Installing boot image..."

	install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"

	echo "${pkgbase}" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"		# Used by mkinitcpio to name the kernel
	echo "Installing modules..."

	ZSTD_CLEVEL=19 make INSTALL_MOD_PATH="${pkgdir}/usr" INSTALL_MOD_STRIP=1 \
		DEPMOD=/doesnt/exist modules_install  					# Suppress depmod
	rm "$modulesdir"/build								# remove build link
}
#-------------------------------------------------------------------------------------------------------------------------------------
_package-headers() {

	pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"
	depends=(pahole)

	cd "${pkgbase}"

	local builddir="${pkgdir}/usr/lib/modules/$(<version)/build"

	echo "Installing build files..."

	install -Dt "${builddir}" -m644 .config Makefile Module.symvers System.map \
		localversion.* version vmlinux tools/bpf/bpftool/vmlinux.h
	install -Dt "${builddir}/kernel" -m644 kernel/Makefile
	install -Dt "${builddir}/arch/x86" -m644 arch/x86/Makefile

	cp -t "${builddir}" -a scripts

	ln -srt "${builddir}" "${builddir}/scripts/gdb/vmlinux-gdb.py"

	install -Dt "${builddir}/tools/objtool" tools/objtool/objtool		# required when STACK_VALIDATION is enabled
	install -Dt "${builddir}/tools/bpf/resolve_btfids" \
		tools/bpf/resolve_btfids/resolve_btfids				# required when DEBUG_INFO_BTF_MODULES is enabled

	echo "Installing headers..."

	cp -t "${builddir}" -a include
	cp -t "${builddir}/arch/x86" -a arch/x86/include
	install -Dt "${builddir}/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s
	install -Dt "${builddir}/drivers/md" -m644 drivers/md/*.h
	install -Dt "${builddir}/net/mac80211" -m644 net/mac80211/*.h
										
	install -Dt "${builddir}/drivers/media/i2c" -m644 \
		drivers/media/i2c/msp3400-driver.h				# https://bugs.archlinux.org/task/13146
	install -Dt "${builddir}/drivers/media/usb/dvb-usb" -m644 \
		drivers/media/usb/dvb-usb/*.h					# https://bugs.archlinux.org/task/20402
	install -Dt "${builddir}/drivers/media/dvb-frontends" -m644 \
		drivers/media/dvb-frontends/*.h
	install -Dt "${builddir}/drivers/media/tuners" -m644 \
		drivers/media/tuners/*.h
	install -Dt "${builddir}/drivers/iio/common/hid-sensors" -m644 \
		drivers/iio/common/hid-sensors/*.h				# https://bugs.archlinux.org/task/71392

	echo "Installing KConfig files..."

	find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

	echo "Removing unneeded architectures..."

	local arch

	for arch in "${builddir}"/arch/*/
	do
		[[ $arch = */x86/ ]] && continue
		echo "Removing $(basename "$arch")"
		rm -r "$arch"
	done

	echo "Removing documentation..."

	rm -r "${builddir}/Documentation"

	echo "Removing broken symlinks..."

	find -L "${builddir}" -type l -printf 'Removing %P\n' -delete

	echo "Removing loose objects..."

	find "${builddir}" -type f -name '*.o' -printf 'Removing %P\n' -delete

	echo "Stripping build tools..."

	local file

	while read -rd '' file
	do
		case "$(file -Sib "$file")" in
			application/x-sharedlib\;*)      # Libraries (.so)
			strip -v $STRIP_SHARED "$file" ;;
			application/x-archive\;*)        # Libraries (.a)
			strip -v $STRIP_STATIC "$file" ;;
			application/x-executable\;*)     # Binaries
			strip -v $STRIP_BINARIES "$file" ;;
			application/x-pie-executable\;*) # Relocatable binaries
			strip -v $STRIP_SHARED "$file" ;;
		esac
	done < <(find "${builddir}" -type f -perm -u+x ! -name vmlinux -print0)

	echo "Stripping vmlinux..."

	strip -v $STRIP_STATIC "${builddir}/vmlinux"

	echo "Adding symlink..."

	mkdir -p "${pkgdir}/usr/src"
	ln -sr "${builddir}" "${pkgdir}/usr/src/${pkgbase}"

	rm "${srcdir}"/in_process				# Comment this out if packaging docs.
}
#-------------------------------------------------------------------------------------------------------------------------------------
_package-docs() {

	pkgdesc="Documentation for the ${pkgdesc} kernel"

	cd "${pkgbase}"

	local builddir="${pkgdir}/usr/lib/modules/$(<version)/build"

	echo "Installing documentation..."

	local src dst

	while read -rd '' src
	do
		dst="${src#Documentation/}"
		dst="${builddir}/Documentation/${dst#output/}"
		install -Dm644 "$src" "$dst"
	done < <(find Documentation -name '.*' -prune -o ! -type d -print0)

	echo "Adding symlink..."

	mkdir -p "${pkgdir}/usr/share/doc"
	ln -sr "$builddir/Documentation" "${pkgdir}/usr/share/doc/${pkgbase}"

	rm "${srcdir}"/in_process
}
#-------------------------------------------------------------------------------------------------------------------------------------
pkgname=(
	"${pkgbase}"
	"$pkgbase-headers"
	)

	for _p in "${pkgname[@]}"
	do
		eval "package_$_p() {
		$(declare -f "_package${_p#$pkgbase}")
		_package${_p#$pkgbase}
  	}"
	done


