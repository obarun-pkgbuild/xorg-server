# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/xorg-server
# 						Maintainer: AndyRTR <andyrtr@archlinux.org>
# 						Maintainer: Jan de Groot <jgc@archlinux.org>

pkgbase=xorg-server
pkgname=('xorg-server' 'xorg-server-xephyr' 'xorg-server-xdmx' 'xorg-server-xvfb'
		'xorg-server-xnest' 'xorg-server-xwayland' 'xorg-server-common' 'xorg-server-devel')
pkgver=1.19.3
pkgrel=4
arch=('x86_64')
license=('custom')
groups=('xorg')
url="http://xorg.freedesktop.org"
makedepends=('pixman' 'libx11' 'mesa' 'libgl' 'xf86driproto' 'xcmiscproto' 'xtrans' 'bigreqsproto' 'randrproto' 
             'inputproto' 'fontsproto' 'videoproto' 'presentproto' 'compositeproto' 'recordproto' 'scrnsaverproto'
             'resourceproto' 'xineramaproto' 'libxkbfile' 'libxfont2' 'renderproto' 'libpciaccess' 'libxv'
             'xf86dgaproto' 'libxmu' 'libxrender' 'libxi' 'dmxproto' 'libxaw' 'libdmx' 'libxtst' 'libxres'
             'xorg-xkbcomp' 'xorg-util-macros' 'xorg-font-util' 'glproto' 'dri2proto' 'libgcrypt' 'libepoxy'
             'xcb-util' 'xcb-util-image' 'xcb-util-renderutil' 'xcb-util-wm' 'xcb-util-keysyms' 'dri3proto'
             'libxshmfence' 'libunwind' 'wayland-protocols') 
source=(https://xorg.freedesktop.org/releases/individual/xserver/${pkgbase}-${pkgver}.tar.bz2
        xvfb-run
		xvfb-run.1
		nvidia-add-modulepath-support.patch
		xserver-autobind-hotplug.patch
		modesetting-Set-correct-DRM-event-context-version.patch
		CVE-2017-10971.patch
        CVE-2017-10972.patch
        bug99708.patch)
		
sha256sums=('677a8166e03474719238dfe396ce673c4234735464d6dadf2959b600d20e5a98'
            'ff0156309470fc1d378fd2e104338020a884295e285972cc88e250e031cc35b9'
            '2460adccd3362fefd4cdc5f1c70f332d7b578091fb9167bf88b5f91265bbd776'
            '23f2fd69a53ef70c267becf7d2a9e7e07b739f8ec5bec10adb219bc6465099c7'
            '67aaf8668c5fb3c94b2569df28e64bfa1dc97ce429cbbc067c309113caff6369'
            'acb0357acf54073eda30b4014a9f402448dc65b5e465ae5b5c5b807914b43da2'
            '3950d5d64822b4a34ca0358389216eed25e159751006d674e7cb491aa3b54d0b'
            '700af48c541f613b376eb7a7e567d13c0eba7a835d0aaa9d4b0431ebdd9f397c'
            '67013743ba8ff1663b233f50fae88989d36504de83fca5f93af5623f2bef6920')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare() {
  cd "${pkgbase}-${pkgver}"
  
  # merged upstream in trunk
  patch -Np1 -i ../nvidia-add-modulepath-support.patch

  # patch from Fedora, not yet merged
  patch -Np1 -i ../xserver-autobind-hotplug.patch
  
  # merged in trunk
  patch -Np1 -i ../modesetting-Set-correct-DRM-event-context-version.patch
  patch -Np1 -i ../CVE-2017-10971.patch
  patch -Np1 -i ../CVE-2017-10972.patch

  # https://bugs.archlinux.org/task/53404
  patch -Np1 -i ../bug99708.patch

  autoreconf -vfi
}

build() {
  
  # Since pacman 5.0.2-2, hardened flags are now enabled in makepkg.conf
  # With them, module fail to load with undefined symbol.
  # See https://bugs.archlinux.org/task/55102 / https://bugs.archlinux.org/task/54845
  export CFLAGS=${CFLAGS/-fno-plt}
  export CXXFLAGS=${CXXFLAGS/-fno-plt}
  export LDFLAGS=${LDFLAGS/,-z,now}
  
  cd "${pkgbase}-${pkgver}"
  ./configure --prefix=/usr \
      --enable-ipv6 \
      --enable-dri \
      --enable-dmx \
      --enable-xvfb \
      --enable-xnest \
      --enable-composite \
      --enable-xcsecurity \
      --enable-libunwind \
      --enable-xorg \
      --enable-xephyr \
      --enable-glamor \
      --enable-xwayland \
      --enable-kdrive \
      --enable-kdrive-kbd \
      --enable-kdrive-mouse \
      --enable-config-udev \
      --enable-suid-wrapper \
      --enable-install-setuid \
      --enable-record \
      --disable-xfbdev \
      --disable-xfake \
      --disable-static \
      --libexecdir=/usr/lib/xorg-server \
      --sysconfdir=/etc \
      --localstatedir=/var \
      --with-xkb-path=/usr/share/X11/xkb \
      --with-xkb-output=/var/lib/xkb \
      --with-fontrootdir=/usr/share/fonts \
      --with-sha1=libgcrypt \
      --disable-systemd \
      --without-systemd-daemon \
      --enable-systemd-logind=no 
      
  make

  # Disable subdirs for make install rule to make splitting easier
  sed -e 's/^DMX_SUBDIRS =.*/DMX_SUBDIRS =/' \
      -e 's/^XVFB_SUBDIRS =.*/XVFB_SUBDIRS =/' \
      -e 's/^XNEST_SUBDIRS =.*/XNEST_SUBDIRS = /' \
      -e 's/^KDRIVE_SUBDIRS =.*/KDRIVE_SUBDIRS =/' \
      -e 's/^XWAYLAND_SUBDIRS =.*/XWAYLAND_SUBDIRS =/' \
      -i hw/Makefile
}

package_xorg-server-common() {
  pkgdesc="Xorg server common files"
  depends=('xkeyboard-config' 'xorg-xkbcomp' 'xorg-setxkbmap' 'xorg-fonts-misc' 'libunwind')

  cd "${pkgbase}-${pkgver}"
  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-common"
  install -m644 COPYING "${pkgdir}/usr/share/licenses/xorg-server-common"
  
  make -C xkb DESTDIR="${pkgdir}" install-data

  install -m755 -d "${pkgdir}/usr/share/man/man1"
  install -m644 man/Xserver.1 "${pkgdir}/usr/share/man/man1/"

  install -m755 -d "${pkgdir}/usr/lib/xorg"
  install -m644 dix/protocol.txt "${pkgdir}/usr/lib/xorg/"
}

package_xorg-server() {
  pkgdesc="Xorg X server"
  depends=('libepoxy' 'libxfont2' 'pixman' 'xorg-server-common' 'libunwind' 'libgl' 'xf86-input-libinput'
			'libpciaccess' 'libdrm' 'libxshmfence')
  # see xorg-server-*/hw/xfree86/common/xf86Module.h for ABI versions - we provide major numbers that drivers can depend on
  # and /usr/lib/pkgconfig/xorg-server.pc in xorg-server-devel pkg
  provides=('X-ABI-VIDEODRV_VERSION=23' 'X-ABI-XINPUT_VERSION=24.1' 'X-ABI-EXTENSION_VERSION=10.0' 'x-server')
  conflicts=('nvidia-utils<=331.20' 'glamor-egl' 'xf86-video-modesetting')
  replaces=('glamor-egl' 'xf86-video-modesetting')
  install=xorg-server.install

  cd "${pkgbase}-${pkgver}"
  make DESTDIR="${pkgdir}" install

  # distro specific files must be installed in /usr/share/X11/xorg.conf.d
  install -m755 -d "${pkgdir}/etc/X11/xorg.conf.d"	

  rm -rf "${pkgdir}/var"

  rm -f "${pkgdir}/usr/share/man/man1/Xserver.1"
  rm -f "${pkgdir}/usr/lib/xorg/protocol.txt"

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server/COPYING"

  rm -rf "${pkgdir}/usr/lib/pkgconfig"
  rm -rf "${pkgdir}/usr/include"
  rm -rf "${pkgdir}/usr/share/aclocal"
  
}

package_xorg-server-xephyr() {
  pkgdesc="A nested X server that runs as an X application"
  depends=('libxfont2' 'libgl' 'libepoxy' 'libunwind' 'libxv' 'pixman' 'xorg-server-common' 'xcb-util-image'
           'xcb-util-renderutil' 'xcb-util-wm' 'xcb-util-keysyms')

  cd "${pkgbase}-${pkgver}/hw/kdrive"
  make DESTDIR="${pkgdir}" install

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-xephyr"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-xephyr/COPYING"
}

package_xorg-server-xvfb() {
  pkgdesc="Virtual framebuffer X server"
  depends=('libxfont2' 'libunwind' 'pixman' 'xorg-server-common' 'xorg-xauth' 'libgl')

  cd "${pkgbase}-${pkgver}/hw/vfb"
  make DESTDIR="${pkgdir}" install

  install -m755 "${srcdir}/xvfb-run" "${pkgdir}/usr/bin/"
  install -m644 "${srcdir}/xvfb-run.1" "${pkgdir}/usr/share/man/man1/"

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-xvfb"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-xvfb/COPYING"
}

package_xorg-server-xnest() {
  pkgdesc="A nested X server that runs as an X application"
  depends=('libxfont2' 'libxext' 'libunwind' 'pixman' 'xorg-server-common')

  cd "${pkgbase}-${pkgver}/hw/xnest"
  make DESTDIR="${pkgdir}" install

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-xnest"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-xnest/COPYING"
}

package_xorg-server-xdmx() {
  pkgdesc="Distributed Multihead X Server and utilities"
  depends=('libxfont2' 'libxi' 'libxaw' 'libxrender' 'libdmx' 'libxfixes' 'libunwind' 'pixman' 'xorg-server-common')

  cd "${pkgbase}-${pkgver}/hw/dmx"
  make DESTDIR="${pkgdir}" install

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-xdmx"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-xdmx/COPYING"
}

package_xorg-server-xwayland() {
  pkgdesc="run X clients under wayland"
  depends=('libxfont2' 'libepoxy' 'libunwind' 'libgl' 'pixman' 'xorg-server-common')

  cd "${pkgbase}-${pkgver}/hw/xwayland"
  make DESTDIR="${pkgdir}" install

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-xwayland"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-xwayland/COPYING"
}

package_xorg-server-devel() {
  pkgdesc="Development files for the X.Org X server"
  depends=(# see pkgdir/usr/lib/pkgconfig/xorg-server.pc
           'xproto' 'randrproto' 'renderproto' 'xextproto' 'inputproto' 'kbproto' 
           'fontsproto' 'pixman' 'videoproto' 'xf86driproto' 'glproto' 
           'mesa' 'dri2proto' 'dri3proto' 'xineramaproto' 'libpciaccess'
           'resourceproto' 'scrnsaverproto' 'presentproto'
           # not technically required but almost every Xorg pkg needs it to build
           'xorg-util-macros')

  cd "${pkgbase}-${pkgver}"
  make DESTDIR="${pkgdir}" install

  rm -rf "${pkgdir}/usr/bin"
  rm -rf "${pkgdir}/usr/share/man"
  rm -rf "${pkgdir}/usr/share/doc"
  rm -rf "${pkgdir}/usr/share/X11"
  rm -rf "${pkgdir}/usr/lib/xorg"
  rm -rf "${pkgdir}/usr/lib/xorg-server"
  rm -rf "${pkgdir}/var"

  install -m755 -d "${pkgdir}/usr/share/licenses/xorg-server-devel"
  ln -sf ../xorg-server-common/COPYING "${pkgdir}/usr/share/licenses/xorg-server-devel/COPYING"
}
