# Maintainer: Andreas Pettersson <andreas at roughedge dot se>

pkgname=linuxcnc
pkgver=2.9.0~pre0
options=(!emptydirs)
pkgrel=1
pkgdesc="Controls CNC machines. It can drive milling machines, lathes, 3d printers, laser cutters, plasma cutters, robot arms, hexapods, and more (formerly EMC2)"
arch=('i686' 'x86_64' 'aarch64' 'armhf')
license=('GPL2')
url="http://linuxcnc.org"
depends=('bc'
         'bwidget'
         'pyenv'
         'libxaw'
         'python2-imaging'
         'python2-yapps2'
         'tkimg'
         'python2-gtkglext'
         'tclx'
         'xorg-server'
         'boost'
         'procps-ng'
         'python-opengl'
         'psmisc')
makedepends=('intltool' 'git')
provides=('linuxcnc')
conflicts=('linuxcnc' 'linuxcnc-bin' 'linuxcnc-git')
source=("${pkgname}::git+https://github.com/LinuxCNC/linuxcnc?ref=master"
        "support-${pkgname}::git+https://github.com/scottalford75/LinuxCNC-on-RPi")
sha256sums=('SKIP'
            'SKIP')

pkgver() {
  cd "${srcdir}/${pkgname}"
  #git describe --long --tags | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
  cat src/config.h | grep "#define PACKAGE_VERSION" | sed 's/"//g' | awk '{printf $3}'
}

prepare() {
  msg2 "Building shit and hope it will not explode in  your face..."
  cd "${srcdir}/${pkgname}/src"
  echo "export TCLLIBPATH=$TCLLIBPATH:/usr/lib/tcltk/linuxcnc" > ${pkgname}.sh
  pyenv local 2.7.18
  find . -iname fixpaths.py -o -iname checkglade -o \
   -iname update_ini | xargs perl -p -i -e "s/python/python2/"
  patch -Np2 -i "${srcdir}/support-${pkgname}/libtirpc.patch"
  ./autogen.sh
  ./configure --with-realtime=uspace \
   --without-libmodbus \
   --enable-non-distributable=yes
 #  --prefix=/opt/linuxcnc
 # Linking time errors fix
  sed -i "163s|FileName|FileNameArr|" hal/classicladder/files_project.c
  sed -i "174s|FileName|FileNameArr|g" hal/classicladder/files_project.c
  sed -i "175s|FileName|FileNameArr|" hal/classicladder/files_project.c
}

build () {
  eval "$(pyenv init -)"
  msg2 "Cooompiling - Coffe time!"
  cd "${srcdir}/${pkgname}/src"
  make
  msg "All done..."
}

package() {
  msg2 "Packing it up..."
  cd "${srcdir}/${pkgname}/src"
  mkdir -p "${pkgdir}/opt"
  mkdir -p "${pkgdir}"/usr/bin
  cp -PR "${srcdir}/${pkgname}" "${pkgdir}/opt/linuxcnc"
  echo "#!/bin/bash" >> ${pkgdir}/usr/bin/run-lcnc
  echo ". /opt/linuxcnc/scripts/rip-environment" >> ${pkgdir}/usr/bin/run-lcnc
  echo "linuxcnc & disown" >> ${pkgdir}/usr/bin/run-lcnc
  chmod +x ${pkgdir}/usr/bin/run-lcnc
  rm -rf ${pkgdir}/opt/linuxcnc/.git*
  cd ${pkgdir}/opt/linuxcnc
  grep -rl './scripts' -e "${srcdir}" | xargs sed -i "s|${srcdir}/${pkgname}|/opt/linuxcnc|g"
  msg2 "Run with: run-lcnc"
}
