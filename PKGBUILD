# _     _            _        _          _____
#| |__ | | __ _  ___| | _____| | ___   _|___ /
#| '_ \| |/ _` |/ __| |/ / __| |/ / | | | |_ \
#| |_) | | (_| | (__|   <\__ \   <| |_| |___) |
#|_.__/|_|\__,_|\___|_|\_\___/_|\_\\__, |____/
#                                  |___/

#Maintainer: blacksky3 <https://github.com/blacksky3>
#Credits: Janusz Lewandowski <lew21@xtreeme.org>
#Credits: David McFarland <corngood@gmail.com>
#Credits: Andrew Shark <ashark @at@ linuxcomp.ru>


#Upstream pages
#https://aur.archlinux.org/pkgbase/amdgpu-pro-installer
#https://github.com/Ashark/archlinux-amdgpu-pro
#https://repo.radeon.com/amdgpu

major=21.20
minor=1271047
ubuntu_ver=20.04

pkgbase=vulkan-amdgpu-pro-21.20
pkgname=(amf-amdgpu-pro-21.20 vulkan-amdgpu-pro-21.20 lib32-vulkan-amdgpu-pro-21.20)
pkgver=${major1}_${minor}
pkgrel=1
arch=(x86_64)
url='https://repo.radeon.com/amdgpu'
license=(custom: multiple)
source=(https://github.com/blacksky3/OPENCL-AMDGPU-PRO-21.20/releases/download/21.20/amdgpu-pro-${major}-${minor}-ubuntu-${ubuntu_ver}.tar.xz)

# extracts a debian package
# $1: deb file to extract
extract_deb(){
  local tmpdir="$(basename "${1%.deb}")"
  rm -Rf "$tmpdir"
  mkdir "$tmpdir"
  cd "$tmpdir"
  ar x "$1"
  tar -C "${pkgdir}" -xf data.tar.xz
}

# move ubuntu specific /usr/lib/x86_64-linux-gnu to /usr/lib
# $1: debian package library dir (goes from opt/amdgpu or opt/amdgpu-pro and from x86_64 or i386)
# $2: arch package library dir (goes to usr/lib or usr/lib32)
move_libdir(){
  local deb_libdir="$1"
  local arch_libdir="$2"

  if [ -d "${pkgdir}/${deb_libdir}" ]; then
    if [ ! -d "${pkgdir}/${arch_libdir}" ]; then
      mkdir -p "${pkgdir}/${arch_libdir}"
    fi
    mv -t "${pkgdir}/${arch_libdir}/" "${pkgdir}/${deb_libdir}"/*
    find ${pkgdir} -type d -empty -delete
  fi
}

# move copyright file to proper place and remove debian changelog
move_copyright(){
  find ${pkgdir}/usr/share/doc -name "changelog.Debian.gz" -delete
  mkdir -p ${pkgdir}/usr/share/licenses/${pkgname}
  find ${pkgdir}/usr/share/doc -name "copyright" -exec mv {} ${pkgdir}/usr/share/licenses/${pkgname} \;
  find ${pkgdir}/usr/share/doc -type d -empty -delete
}

package_amf-amdgpu-pro-21.20(){
  pkgdesc='AMDGPU Pro Advanced Multimedia Framework'
  license=(custom: AMDGPU-PRO EULA)
  depends=(libdrm vulkan-amdgpu-pro=${major1}_${minor}-${pkgrel})
  optdepends=('rocm-opencl-runtime: Warning unspecified optdep description')
  conflicts=(amf-amdgpu-pro)

  extract_deb "${srcdir}"/amdgpu-pro-${major}-${minor}-ubuntu-${ubuntu_ver}/amf-amdgpu-pro_${major}-${minor}_amd64.deb
  #extract_deb "${srcdir}"/amdgpu-pro-${major}-${minor}-ubuntu-${ubuntu_ver}/libamdenc-amdgpu-pro_1.0-${major}-${minor}_amd64.deb
  move_libdir "opt/amdgpu-pro/lib/x86_64-linux-gnu" "usr/lib"
  move_copyright
}

package_vulkan-amdgpu-pro-21.20(){
  pkgdesc='AMDGPU Pro Vulkan driver'
  license=(custom: AMDGPU-PRO EULA)
  provides=(vulkan-driver)
  depends=(vulkan-icd-loader)
  optdepends=('openssl: Warning unspecified optdep description')
  conflicts=(vulkan-amdgpu-pro)

  extract_deb "${srcdir}"/amdgpu-pro-${major}-${minor}-ubuntu-${ubuntu_ver}/vulkan-amdgpu-pro_${major}-${minor}_amd64.deb
  move_libdir "opt/amdgpu-pro/lib/x86_64-linux-gnu" "usr/lib"
  move_copyright

  # extra_commands:
  mkdir -p "${pkgdir}"/usr/share/vulkan/icd.d/
  mv "${pkgdir}"/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd64.json "${pkgdir}"/usr/share/vulkan/icd.d/amd_pro_icd64.json
  mv "${pkgdir}"/usr/lib/amdvlk64.so "${pkgdir}"/usr/lib/amdvlkpro64.so
  sed -i "s#/opt/amdgpu-pro/lib/x86_64-linux-gnu/amdvlk64.so#/usr/lib/amdvlkpro64.so#" "${pkgdir}"/usr/share/vulkan/icd.d/amd_pro_icd64.json
  find ${pkgdir} -type d -empty -delete
}

package_lib32-vulkan-amdgpu-pro-21.20(){
  pkgdesc='AMDGPU Pro Vulkan driver (32-bit)'
  license=(custom: AMDGPU-PRO EULA)
  provides=(lib32-vulkan-driver)
  depends=(lib32-vulkan-icd-loader vulkan-amdgpu-pro=${major1}_${minor}-${pkgrel})
  optdepends=('lib32-openssl: Warning unspecified optdep description')
  conflicts=(lib32-vulkan-amdgpu-pro)

  extract_deb "${srcdir}"/amdgpu-pro-${major}-${minor}-ubuntu-${ubuntu_ver}/vulkan-amdgpu-pro_${major}-${minor}_i386.deb
  move_libdir "opt/amdgpu-pro/lib/i386-linux-gnu" "usr/lib32"
  move_copyright

  # extra_commands:
  mkdir -p "${pkgdir}"/usr/share/vulkan/icd.d/
  mv "${pkgdir}"/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd32.json "${pkgdir}"/usr/share/vulkan/icd.d/amd_pro_icd32.json
  mv "${pkgdir}"/usr/lib32/amdvlk32.so "${pkgdir}"/usr/lib32/amdvlkpro32.so
  sed -i "s#/opt/amdgpu-pro/lib/i386-linux-gnu/amdvlk32.so#/usr/lib32/amdvlkpro32.so#" "${pkgdir}"/usr/share/vulkan/icd.d/amd_pro_icd32.json
  find ${pkgdir} -type d -empty -delete
}

sha256sums=('8ea051de8c9c6814eb45ce18d102e639bb6edb5786e948b50c5105e3e21978f9')
