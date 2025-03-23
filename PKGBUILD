# Maintainer: NuSkool <nuskool@null.net>
# linux-stable-rc 2025-03-22
# Credits: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
#
# Depends on AUR 'modprobed-db' being installed/setup to eliminate unneeded modules.
# To OPT-OUT 'modprobed-db' search/comment out line containing 'HOME' in 'prepare()' function.
# Builds -rc version listed 'stable' kernel.org ie: 'stable: 6.13...'
# Info: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/
# Have custom kernel config? Replace existing config when promped for user input.
#-------------------------------------------------------------------------------------------------------------------------------------
_basedir=$(pwd)
pkgbase=linux-stable-rc
pkgver=6.13
pkgrel=0
pkgdesc='Current "stable" version of Linux -rc'
url=https://www.kernel.org
license=(GPL-2.0-only)
arch=(x86_64)

makedepends=(
	bc
	cpio
	gettext
	graphviz
	imagemagick
	kmod
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
# arch-mod.patch

_version=$(curl -sL "${url}"/finger_banner | awk '/latest stable version/{gsub(/[^0-9.]/,"",$NF); print $NF}')

source=(https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/snapshot/linux-stable-rc-linux-"${_version%.*}".y.tar.gz
	arch-config::https://gitlab.archlinux.org/archlinux/packaging/packages/linux/-/raw/main/config?ref_type=heads
	     config::https://raw.githubusercontent.com/Cody-Learner/linux-stable-rc/main/config
	https://raw.githubusercontent.com/Cody-Learner/linux-stable-rc/main/arch-mod.patch
	)

#-------------------------------------------------------------------------------------------------------------------------------------
validpgpkeys=(ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
              647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
              83BC8889351B5DEBBB68416EB8AC08600F108CDF) # Jan Alexander Steffens (heftig)

#-------------------------------------------------------------------------------------------------------------------------------------
# 'SKIP'	# sha256sum linux-stable-rc-linux-6.13.y.tar.gz arch-config config arch-mod.patch

sha256sums=('9e2f25ebc9525c3f84cae4fc8cd055e214cb98cf138ed311d97419b6e2d51d94'		# linux-stable-rc-linux-6.13.y.tar.gz
            'ccae8f9a7773146224f796b753d26031cdca8d859d1a1a787a00de54875e25ce'		# arch-config
            'SKIP'									# config
            '9231987b5798f2f179048f46e53290be4a19e83f7bfe91687d4fe43ca0a61e32')		# arch-mod.patch

#-------------------------------------------------------------------------------------------------------------------------------------
export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

#-------------------------------------------------------------------------------------------------------------------------------------
_remove-rust(){
	if	grep -q 'CONFIG_RUST=y' config ; then			# Edit rust in kernel config
		sed -i 's/CONFIG_RUST=y/CONFIG_RUST=n/'	config
	fi
}

#-------------------------------------------------------------------------------------------------------------------------------------
prepare() {


#......................................................# Begin Additional Code #......................................................#
_ptr=$(printf "[1;33m ===>[00m")

if	[[ ! -e src/in_process ]]; then				# Prevent running enclosed code during build.

	cd "${_basedir}"
if	[[ ! -s config ]]; then
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

if	[[ "$_verst" != *rc* ]]; then
	cat << EOF

$_ptr	Kernel version for stable -rc is ${_verst}. 
	This means an -rc version is currently unavailable.
	Appending a fake '-rc0' to it, to differentiate it from repo kernel.
	This kernel s/b available in the Arch official repos.
EOF
	_verst="${_verst%}-rc0"
fi

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

$_ptr	Building linux-stable-rc version: ${_verst} with Arch patches applied excluding Makefile.
	Now would be the time to confirm/change kernel config settings before proceeding.
	If kernel fails to build, try removing rust from config option.

	Select an option:
	 p  to proceed normally with build
	 r  to remove rust from kernel config
	 e  to exit now, wrong version, etc.

EOF
	while read -n1 -rp "         Press [p/r/e] " reply
	do
		 if [[ ${reply} == p ]]; then echo ; break ; fi ;
		 if [[ ${reply} == r ]]; then echo ; _remove-rust ; break ; fi
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
		src="${src%.zst}"
		[[ $src = *.patch ]] || continue
		echo "Applying patch $src..."
		patch -Np1 < "../$src"
	done

	echo "Setting config..."

	cp ../config .config

	make olddefconfig

if	[[ -s "${_basedir}/modprobed.db" ]]; then				# -s Possibly a placeholder file implemented.
      _modpdb_path="${_basedir}/modprobed.db"
  elif  [[ -e "${HOME}/.config/modprobed.db" ]]; then
      _modpdb_path="${HOME}/.config/modprobed.db"
fi
if	[[ -n "$_modpdb_path" ]]; then
        printf '%s\n\n' "${_ptr} Using $_modpdb_path for modules."
        yes "" | make LSMOD="$_modpdb_path" localmodconfig
    else
        printf '%s\n' "${_ptr} No modprobed.db found. Building all modules."
fi

	diff -u ../config .config || :

	make -s kernelrelease > version

	printf '%s\n\n' "$_ptr Prepared ${pkgbase} version $(<version)"
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

