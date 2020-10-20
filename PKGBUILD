# ARMv7 multi-platform
# Maintainer: Kevin Mihelich <kevin@archlinuxarm.org>

buildarch=4

pkgbase=linux-helios4
_srcname=linux-5.3
_kernelname=${pkgbase#linux}
_desc="ARMv7 Helios4"
pkgver=5.3.1
pkgrel=1
rcnver=5.3.0
rcnrel=armv7-x8
arch=('armv7h')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'git' 'uboot-tools' 'vboot-utils' 'dtc')
options=('!strip')
source=("http://www.kernel.org/pub/linux/kernel/v5.x/${_srcname}.tar.xz"
        "http://www.kernel.org/pub/linux/kernel/v5.x/patch-${pkgver}.xz"
        "http://rcn-ee.com/deb/sid-armhf/v${rcnver}-${rcnrel}/patch-${rcnver%.0}-${rcnrel}.diff.gz"
        '0001-ARM-atags-add-support-for-Marvell-s-u-boot.patch'
        '0002-ARM-atags-fdt-retrieve-MAC-addresses-from-Marvell-bo.patch'
        '0003-SMILE-Plug-device-tree-file.patch'
        '0004-fix-mvsdio-eMMC-timing.patch'
        '0005-net-smsc95xx-Allow-mac-address-to-be-set-as-a-parame.patch'
        '0006-set-default-cubietruck-led-triggers.patch'
        '0007-exynos4412-odroid-set-higher-minimum-buck2-regulator.patch'
        '0008-ARM-dove-enable-ethernet-on-D3Plug.patch'
        '0009-USB-Armory-MkII-support.patch'
        'config'
        'kernel.its'
        'kernel.keyblock'
        'kernel_data_key.vbprivk'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook'
        '92-mvebu-gpio-remove-hardcoded-timer-assignment.patch')

md5sums=('c99feaade8047339528fb066ec5f8a49'
         '476673d3ab1470fd9bac0da60774f3fd'
         '9c61370104816184d2b4e1b59163532c'
         'f989c450907a0af28867b38dc0a1112e'
         '884c3ef691a300cd2b0c704d795130c3'
         'd9ab3a3d50a3684b6f13c5c1c8c23dbe'
         '65449dc8fa2b45522135899c5e8acaed'
         'c5a2831b832c726ba65077b69a8bc8e7'
         '196a7cb2a570808dabfd22066646dbab'
         '118d67de1d31194435b5564c36b0c57f'
         '7334902800f72a0d17c26bef373d2c49'
         '065e0eb93091edeea111c1c1f6e8fb44'
         '98eb2eada163f86923622b9fdc260779'
         '4f2379ed84258050edb858ee8d281678'
         '61c5ff73c136ed07a7aadbf58db3d96a'
         '584777ae88bce2c5659960151b64c7d8'
         '86d4a35722b5410e3b29fc92dae15d4b'
         'ce6c81ad1ad1f8b333fd6077d47abdaf'
         '3e2a512f8da5db5fe9f17875405e56a3'
	 '8ab2c7698b2f38042f8c0c71f16f07fd')


prepare() {
  cd "${srcdir}/${_srcname}"

  # add upstream patch
  git apply --whitespace=nowarn ../patch-${pkgver}

  # RCN patch
  git apply ../patch-${rcnver%.0}-${rcnrel}.diff

  # ALARM patches
  git apply ../0001-ARM-atags-add-support-for-Marvell-s-u-boot.patch
  git apply ../0002-ARM-atags-fdt-retrieve-MAC-addresses-from-Marvell-bo.patch
  git apply ../0003-SMILE-Plug-device-tree-file.patch
  git apply ../0004-fix-mvsdio-eMMC-timing.patch
  git apply ../0005-net-smsc95xx-Allow-mac-address-to-be-set-as-a-parame.patch
  git apply ../0006-set-default-cubietruck-led-triggers.patch
  git apply ../0007-exynos4412-odroid-set-higher-minimum-buck2-regulator.patch
  git apply ../0008-ARM-dove-enable-ethernet-on-D3Plug.patch
  git apply ../0009-USB-Armory-MkII-support.patch
  patch -Np1 < ../92-mvebu-gpio-remove-hardcoded-timer-assignment.patch

  cat "${srcdir}/config" > ./.config

  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
}

build() {
  cd "${srcdir}/${_srcname}"

  # get kernel version
  make prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  # ... or manually edit .config

  # Copy back our configuration (use with new kernel version)
  #cp ./.config ../${pkgbase}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config

  # build!
  make ${MAKEFLAGS} zImage modules dtbs
}

_package() {
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
  optdepends=('crda: to set the correct wireless channels of your country')
  backup=("etc/mkinitcpio.d/${pkgbase}.preset")
  provides=('kernel26' "linux=${pkgver}")
  conflicts=('linux')
  replaces=('linux-mvebu' 'linux-udoo' 'linux-sun4i' 'linux-sun5i' 'linux-sun7i' 'linux-usbarmory' 'linux-wandboard' 'linux-clearfog')
  install=${pkgname}.install

  cd "${srcdir}/${_srcname}"

  KARCH=arm

  # get kernel version
  _kernver="$(make kernelrelease)"
  _basekernel=${_kernver%%-*}
  _basekernel=${_basekernel%.*}

  mkdir -p "${pkgdir}"/{boot,usr/lib/modules}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  make INSTALL_DTBS_PATH="${pkgdir}/boot/dtbs" dtbs_install
  cp arch/$KARCH/boot/zImage "${pkgdir}/boot/zImage"

  # make room for external modules
  local _extramodules="extramodules-${_basekernel}${_kernelname}"
  ln -s "../${_extramodules}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"

  # add real version for building modules and running depmod from hook
  echo "${_kernver}" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modules/${_extramodules}/version"

  # remove build and source links
  rm "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}

  # now we call depmod...
  depmod -b "${pkgdir}/usr" -F System.map "${_kernver}"

  # sed expression for following substitutions
  local _subst="
    s|%PKGBASE%|${pkgbase}|g
    s|%KERNVER%|${_kernver}|g
    s|%EXTRAMODULES%|${_extramodules}|g
  "

  # install mkinitcpio preset file
  sed "${_subst}" ../linux.preset |
    install -Dm644 /dev/stdin "${pkgdir}/etc/mkinitcpio.d/${pkgbase}.preset"

  # install pacman hooks
  sed "${_subst}" ../60-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/60-${pkgbase}.hook"
  sed "${_subst}" ../90-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/90-${pkgbase}.hook"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')
  replaces=('linux-mvebu-headers' 'linux-sun4i-headers' 'linux-sun5i-headers' 'linux-sun7i-headers' 'linux-usbarmory-headers' 'linux-wandboard-headers' 'linux-clearfog-headers')

  cd ${_srcname}
  local _builddir="${pkgdir}/usr/lib/modules/${_kernver}/build"

  install -Dt "${_builddir}" -m644 Makefile .config Module.symvers
  install -Dt "${_builddir}/kernel" -m644 kernel/Makefile

  mkdir "${_builddir}/.tmp_versions"

  cp -t "${_builddir}" -a include scripts

  install -Dt "${_builddir}/arch/${KARCH}" -m644 arch/${KARCH}/Makefile
  install -Dt "${_builddir}/arch/${KARCH}/kernel" -m644 arch/${KARCH}/kernel/asm-offsets.s arch/$KARCH/kernel/module.lds

  cp -t "${_builddir}/arch/${KARCH}" -a arch/${KARCH}/include
  for i in dove exynos omap2; do
    mkdir -p "${_builddir}/arch/${KARCH}/mach-${i}"
    cp -t "${_builddir}/arch/${KARCH}/mach-${i}" -a arch/$KARCH/mach-${i}/include
  done
  for i in omap orion samsung versatile; do
    mkdir -p "${_builddir}/arch/${KARCH}/plat-${i}"
    cp -t "${_builddir}/arch/${KARCH}/plat-${i}" -a arch/$KARCH/plat-${i}/include
  done

  install -Dt "${_builddir}/drivers/md" -m644 drivers/md/*.h
  install -Dt "${_builddir}/net/mac80211" -m644 net/mac80211/*.h

  # http://bugs.archlinux.org/task/13146
  install -Dt "${_builddir}/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # http://bugs.archlinux.org/task/20402
  install -Dt "${_builddir}/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "${_builddir}/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "${_builddir}/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # add xfs and shmem for aufs building
  mkdir -p "${_builddir}"/{fs/xfs,mm}

  # copy in Kconfig files
  find . -name Kconfig\* -exec install -Dm644 {} "${_builddir}/{}" \;

  # remove unneeded architectures
  local _arch
  for _arch in "${_builddir}"/arch/*/; do
    [[ ${_arch} == */${KARCH}/ ]] && continue
    rm -r "${_arch}"
  done

  # remove files already in linux-docs package
  rm -r "${_builddir}/Documentation"

  # remove now broken symlinks
  find -L "${_builddir}" -type l -printf 'Removing %P\n' -delete

  # Fix permissions
  chmod -R u=rwX,go=rX "${_builddir}"

  # strip scripts directory
  local _binary _strip
  while read -rd '' _binary; do
    case "$(file -bi "${_binary}")" in
      *application/x-sharedlib*)  _strip="${STRIP_SHARED}"   ;; # Libraries (.so)
      *application/x-archive*)    _strip="${STRIP_STATIC}"   ;; # Libraries (.a)
      *application/x-executable*) _strip="${STRIP_BINARIES}" ;; # Binaries
      *) continue ;;
    esac
    /usr/bin/strip ${_strip} "${_binary}"
  done < <(find "${_builddir}/scripts" -type f -perm -u+w -print0 2>/dev/null)
}


pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
