#
# Copyright (C) 2020 OpenWrt.org
# Author: Mirko Vogt <mirko-openwrt@nanl.de>
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

# TODO / known bugs:
# - plugins don't work when sstrip is used for stripping
# - no X11/wayland/xcb/kms support so far - primary target is linuxfb

include $(TOPDIR)/rules.mk

PKG_NAME:=qt5base
PKG_RELEASE:=1
PKG_HASH:=b04815058c18058b6ba837206756a2c87d1391f07a0dcb0dd314f970fd041592

include ../common.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/nls.mk
include ../qmake.mk

# Yes, the host build depends on the target build. This is not a mistake!
# The target build provides the (target specific) qmake.mk file which
# is also used for host builds.
# Host projects depend on qt5base/host which however don't build
# if qt5base didn't install the qmake.mk file before.
# Benefit of the target build providing qmake.mk instead of the host build
# is that the host build really only needs to be built, if there're targets
# which actually depend on it. At the time of this writing, only qt5tools
# makes use of the host build.
HOST_BUILD_DEPENDS:=qt5base

# hard disable udev support for now
CONFIG_PACKAGE_qt5base-input-autodiscovery:=

PKG_CONFIG_DEPENDS:= \
	CONFIG_PACKAGE_qt5base-plugin-imageformats-jpeg \
	CONFIG_PACKAGE_qt5base-network \
	CONFIG_PACKAGE_qt5base-gui \
	CONFIG_PACKAGE_qt5base-widgets \
	CONFIG_PACKAGE_qt5base-examples \
	CONFIG_PACKAGE_qt5base-input-autodiscovery \
	CONFIG_PACKAGE_qt5base-plugin-platforms-minimal \
	CONFIG_PACKAGE_qt5base-plugin-platforms-linuxfb \
	CONFIG_PACKAGE_qt5base-plugin-input-libinput \
	CONFIG_BUILD_qt5base-core_INOTIFY \
	CONFIG_PACKAGE_qt5base-plugin-platforms-eglfs \
#	CONFIG_PACKAGE_qt5base-gl \
#	CONFIG_PACKAGE_qt5base-plugin-imageformats-png \
#	CONFIG_PACKAGE_qt5base-plugin-platforms-minimalegl \

# Do not use sstrip for QT5. When sstrip is used the QT5 plugin loading does
# not work, because of QT''s internal elf parser, see
# https://bugreports.qt.io/browse/QTBUG-52567
# Use the code from the gcc package to use strip instaed.
ifneq ($(CONFIG_USE_SSTRIP),)
  STRIP:=$(TARGET_CROSS)strip $(call qstrip,$(CONFIG_STRIP_ARGS))
  RSTRIP:= \
    export CROSS="$(TARGET_CROSS)" \
		$(if $(CONFIG_KERNEL_KALLSYMS),NO_RENAME=1) \
		$(if $(CONFIG_KERNEL_PROFILING),KEEP_SYMBOLS=1); \
    NM="$(TARGET_CROSS)nm" \
    STRIP="$(STRIP)" \
    STRIP_KMOD="$(SCRIPT_DIR)/strip-kmod.sh" \
    $(SCRIPT_DIR)/rstrip.sh
endif

define Package/qt5base/Default
  SECTION:=video-frameworks
  CATEGORY:=Video
  SUBMENU:=Frameworks and Toolkits
  TITLE:=Qt5base
  URL:=http://qt.io
  MAINTAINER:=Mirko Vogt <mirko-openwrt@nanl.de>
endef

define Package/qt5base-core
  $(call Package/qt5base/Default)
  TITLE+=core
  DEPENDS+=+libpthread +zlib +libzstd +libstdcpp +librt +libpcre2-16 +libdouble-conversion $(ICONV_DEPENDS) +PACKAGE_icu:icu #FIXME: do not include ICONV_DEPENDS if ICU is selected (though, that's only an issue when using GNU iconv)
endef

define Package/qt5base-core/config
        source "$(SOURCE)/Config.in"
endef

define Package/qt5base-concurrent
  $(call Package/qt5base/Default)
  TITLE+=concurrent
  DEPENDS+=+qt5base-core
endef

# define Package/qt5base-gl
#   $(call Package/qt5base/Default)
#   TITLE+=gl
#   DEPENDS+=+qt5base-core +qt5base-gui +qt5base-widgets @BROKEN
# endef

define Package/qt5base-gui
  $(call Package/qt5base/Default)
  TITLE+=gui
  DEPENDS+=+qt5base-core +libpng +libfreetype +BUILD_qt5base-gui_OPENGL_OPENGLES2:libmesa # +BUILD_qt5base-gui_OPENGL_OPENGLES2:qt5base-widgets # qt5base-widgets appears to be needed when compiled with gl support, otherwise unneeded
endef

define Package/qt5base-network
  $(call Package/qt5base/Default)
  TITLE+=network
  DEPENDS+=+qt5base-core +BUILD_qt5base-network_SSL:libopenssl
endef

# seems to be only present as static lib
# define Package/qt5base-paltformSupport
#   $(call Package/qt5base/Default)
#   DEPENDS+=
# endef

define Package/qt5base-printSupport
  $(call Package/qt5base/Default)
  TITLE+=print support
  DEPENDS+=+qt5base-core +qt5base-gui +qt5base-widgets
endef

define Package/qt5base-sql
  $(call Package/qt5base/Default)
  TITLE+=sql
  DEPENDS+=+qt5base-core
endef

define Package/qt5base-test
  $(call Package/qt5base/Default)
  TITLE+=test
  DEPENDS+=+qt5base-core
endef

define Package/qt5base-widgets
  $(call Package/qt5base/Default)
  TITLE+=widgets
  DEPENDS+=+qt5base-core +qt5base-gui
endef

define Package/qt5base-xml
  $(call Package/qt5base/Default)
  TITLE+=xml
  DEPENDS+=+qt5base-core
endef

# define Package/qt5base-eglfs-device-integration
#   $(call Package/qt5base/Default)
#   TITLE+=xml
#   DEPENDS+=+qt5base-core +qt5base-gui +libmesa
# endef

#### plugins

####### bearer

define Package/qt5base-plugin-bearer-generic
  $(call Package/qt5base/Default)
TITLE+=bearer (plugin)
  DEPENDS+=+qt5base-core +qt5base-network
endef

####### generic / input

define Package/qt5base-plugin-input-libinput
  $(call Package/qt5base/Default)
  TITLE+=libinput (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +libinput
endef

define Package/qt5base-plugin-input-evdevkeyboard
  $(call Package/qt5base/Default)
  TITLE+=evdev keyboard (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef

define Package/qt5base-plugin-input-evdevmouse
  $(call Package/qt5base/Default)
  TITLE+=evdev mouse (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef

define Package/qt5base-plugin-input-evdevtablet
  $(call Package/qt5base/Default)
  TITLE+=evdev tablet (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef

define Package/qt5base-plugin-input-evdevtouch
  $(call Package/qt5base/Default)
  TITLE+=evdev touch (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef

####### imageformats

#define Package/qt5base-plugin-imageformats-gif
#  $(call Package/qt5base/Default)
#TITLE+=imageformat gif (plugin)
#  DEPENDS+=+qt5base-core +qt5base-gui
#endef

define Package/qt5base-plugin-imageformats-ico
  $(call Package/qt5base/Default)
  TITLE+=imageformat ico (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui
endef

define Package/qt5base-plugin-imageformats-jpeg
  $(call Package/qt5base/Default)
  TITLE+=imageformat jpeg (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +libjpeg
endef

#define Package/qt5base-plugin-imageformats-png
#  $(call Package/qt5base/Default)
#  TITLE+=imageformat png (plugin)
#  DEPENDS+=+qt5base-core +qt5base-gui +libpng
#endef

####### platforms

define Package/qt5base-plugin-platforms-linuxfb
  $(call Package/qt5base/Default)
  TITLE+=platform linuxfb (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +libfreetype +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef

define Package/qt5base-plugin-platforms-eglfs
  $(call Package/qt5base/Default)
  TITLE+=platform eglfs (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +libfreetype +fontconfig +libmesa # +qt5base-eglfs-device-integration
endef

define Package/qt5base-plugin-platforms-minimalegl
  $(call Package/qt5base/Default)
  TITLE+=platform minimalegl (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui # +libfreetype +fontconfig
endef

define Package/qt5base-plugin-platforms-minimal
  $(call Package/qt5base/Default)
  TITLE+=platform minimal (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui # +libfreetype +fontconfig
endef

define Package/qt5base-plugin-platforms-offscreen
  $(call Package/qt5base/Default)
  TITLE+=platform offscreen (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui # +libfreetype +fontconfig
endef

define Package/qt5base-plugin-platforms-vnc
  $(call Package/qt5base/Default)
  TITLE+=platform vnc (plugin)
  DEPENDS+=+qt5base-core +qt5base-gui +qt5base-network +libfreetype +fontconfig +PACKAGE_qt5base-input-autodiscovery:libudev +PACKAGE_qt5base-plugin-input-libinput:libinput
endef



###### sql

#define Package/qt5base-plugin-sqldrivers-sqlite
#  $(call Package/qt5base/Default)
#  TITLE+=sqldriver sqlite (plugin)
#  DEPENDS+=+qt5base-core +qt5base-sql +libsqlite3
#endef

define Package/qt5base-examples
  $(call Package/qt5base/Default)
  TITLE+=examples
  DEPENDS+=\
		+qt5base-core \
		+qt5base-gui \
		+qt5base-widgets \
		+qt5base-network \
		+qt5base-sql \
		+qt5base-xml \
		+qt5base-printSupport \
		+qt5base-concurrent \
		+qt5base-test
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(CP) \
		./files/mkspecs/* \
		$(PKG_BUILD_DIR)/mkspecs/
endef


define Build/Configure
	# CROSS/TARGET_* need to be passed to configure, in order to use cross-compiling tools to check for requirements.
	# Usually used variables such as CC/CXX/CFLAGS/CXXFLAGS/LDFLAGS would be used for compilation of host tools (qmake, moc, etc.),
	# hence we use the TARGET_* nomenclature.
	# 
	# NOTES:
	#  - icu support currently depends on selection of icu package which is not ideal, since unpredictable.
	#  - icu support overrides iconv support
	( cd $(PKG_BUILD_DIR) ; \
		TARGET_CROSS="$(TARGET_CROSS)" \
		TARGET_CFLAGS="$(TARGET_CFLAGS)" \
		TARGET_CXXFLAGS="$(TARGET_CFLAGS) $(TARGET_CXXFLAGS)" \
		TARGET_LDFLAGS="$(TARGET_LDFLAGS)" \
		PKG_CONFIG="$(STAGING_DIR_HOST)/bin/pkg-config.real" \
		PKG_CONFIG_SYSROOT_DIR="$(STAGING_DIR)" \
		./configure \
			--extprefix=$(STAGING_DIR)/$(CONFIGURE_PREFIX) \
			--prefix=$(QT_INSTALL_PREFIX) \
			--bindir=$(QT_INSTALL_BINS) \
			--headerdir=$(QT_INSTALL_HEADERS) \
			--libdir=$(QT_INSTALL_LIBS) \
			--archdatadir=$(QT_INSTALL_ARCHDATA) \
			--plugindir=$(QT_INSTALL_PLUGINS) \
			--libexecdir=$(QT_INSTALL_LIBEXECS) \
			--importdir=$(QT_INSTALL_IMPORTS) \
			--qmldir=$(QT_INSTALL_QML) \
			--datadir=$(QT_INSTALL_DATA) \
			--docdir=$(QT_INSTALL_DOCS) \
			--translationdir=$(QT_INSTALL_TRANSLATIONS) \
			--sysconfdir=$(QT_INSTALL_CONFIGURATION) \
			--examplesdir=$(QT_INSTALL_EXAMPLES) \
			--testsdir=$(QT_INSTALL_TESTS) \
			--hostprefix=$(QT_HOST_PREFIX) \
			--hostbindir=$(QT_HOST_BINS) \
			--hostdatadir=$(QT_HOST_DATA) \
			--gcc-sysroot=no \
			--verbose=yes \
			--opensource \
			--confirm-license \
			--release=yes \
			--optimized-tools=no \
			--optimize-size=no \
			--strip=yes \
			--shared=yes \
			--framework=no \
			--platform=linux-openwrt-host-g++ \
			--xplatform=linux-openwrt-g++ \
			--reduce-exports=no \
			--reduce-relocations=no \
			--pch=no \
			--ltcg=no \
			--use-gold-linker=no \
			--warnings-are-errors=no \
			--pkg-config=yes \
			--compile-examples=$(if $(CONFIG_PACKAGE_qt5base-examples),yes,no) \
			$(if $(CONFIG_PACKAGE_qt5base-examples),,--nomake=examples) \
			--gui=$(if $(CONFIG_PACKAGE_qt5base-gui),yes,no) \
			--widgets=$(if $(CONFIG_PACKAGE_qt5base-widgets),yes,no) \
			--dbus=no \
			--accessibility=yes \
			--doubleconversion=system \
			--glib=no \
			--eventfd=no \
			--inotify=$(if $(CONFIG_BUILD_qt5base-core_INOTIFY),yes,no) \
			--iconv=$(if $(CONFIG_BUILD_NLS),$(if $(CONFIG_LIBC_USE_GLIBC)$(CONFIG_LIBC_USE_MUSL),posix)$(if $(CONFIG_LIBC_USE_UCLIBC),gnu),no) \
			--icu=$(if $(CONFIG_PACKAGE_icu),yes,no) \
			--pcre=system \
			--zlib=system \
			--journald=no \
			--syslog=no \
			--ssl=$(if $(CONFIG_BUILD_qt5base-network_SSL),yes,no) \
			--openssl=$(if $(CONFIG_BUILD_qt5base-network_SSL),yes,no) \
			$(if $(CONFIG_BUILD_qt5base-network_SSL),--openssl-linked,) \
			--libproxy=no \
			--system-proxies=yes \
			--cups=no \
			--fontconfig=no \
			--freetype=system \
			--harfbuzz=no \
			--gtk=no \
			--opengl=$(if $(CONFIG_BUILD_qt5base-gui_OPENGL_OPENGLES2),es2,no) \
			--opengles3=no \
			--egl=$(if $(CONFIG_BUILD_qt5base-gui_OPENGL_OPENGLES2),yes,no) \
			--xcb-xlib=no \
			--qpa=$(if $(CONFIG_PACKAGE_qt5base-plugin-platforms-linuxfb),linuxfb,) \
			--directfb=no \
			--eglfs=$(if $(CONFIG_BUILD_qt5base-gui_OPENGL_OPENGLES2),yes,no) \
			--gbm=$(if $(CONFIG_BUILD_qt5base-gui_DRM),yes,no) \
			--kms=$(if $(CONFIG_BUILD_qt5base-gui_DRM),yes,no) \
			--linuxfb=$(if $(CONFIG_PACKAGE_qt5base-plugin-platforms-linuxfb),yes,no) \
			--xcb=no \
			--libudev=$(if $(CONFIG_PACKAGE_qt5base-input-autodiscovery),yes,no) \
			--evdev=yes \
			--libinput=$(if $(CONFIG_PACKAGE_qt5base-plugin-input-libinput),yes,no) \
			--mtdev=no \
			--tslib=no \
			--bundled-xcb-xinput=no \
			--xkbcommon=no \
			--gif=$(if $(CONFIG_PACKAGE_qt5base-gui),yes,no) \
			--ico=$(if $(CONFIG_PACKAGE_qt5base-plugin-imageformats-ico),yes,no) \
			--libjpeg=$(if $(CONFIG_PACKAGE_qt5base-plugin-imageformats-jpeg),system,no) \
			--libpng=system \
			--sql-db2=no \
			--sql-ibase=no \
			--sql-mysql=no \
			--sql-oci=no \
			--sql-odbc=no \
			--sql-psql=no \
			--sql-sqlite=no \
			--sql-sqlite2=no \
			--sql-tds=no \
			--sqlite=no \
	)
endef


define Host/Configure
	# CROSS/TARGET_* need to be passed to configure, in order to use cross-compiling tools to check for requirements.
	# Usually used variables such as CC/CXX/CFLAGS/CXXFLAGS/LDFLAGS would be used for compilation of host tools (qmake, moc, etc.),
	# hence we use the TARGET_* nomenclature.
	( cd $(HOST_BUILD_DIR) ; \
		TARGET_CFLAGS="$(HOST_CFLAGS)" \
		TARGET_CXXFLAGS="$(HOST_CFLAGS) $(HOST_CXXFLAGS)" \
		TARGET_LDFLAGS="$(HOST_LDFLAGS)" \
		./configure \
			--prefix=$(QT_HOSTPKG_PREFIX) \
			--bindir=$(QT_HOSTPKG_BINS) \
			--headerdir=$(QT_HOSTPKG_HEADERS) \
			--libdir=$(QT_HOSTPKG_LIBS) \
			--archdatadir=$(QT_HOSTPKG_ARCHDATA) \
			--plugindir=$(QT_HOSTPKG_PLUGINS) \
			--libexecdir=$(QT_HOSTPKG_LIBEXECS) \
			--importdir=$(QT_HOSTPKG_IMPORTS) \
			--qmldir=$(QT_HOSTPKG_QML) \
			--datadir=$(QT_HOSTPKG_DATA) \
			--docdir=$(QT_HOSTPKG_DOCS) \
			--translationdir=$(QT_HOSTPKG_TRANSLATIONS) \
			--sysconfdir=$(QT_HOSTPKG_CONFIGURATION) \
			--examplesdir=$(QT_HOSTPKG_EXAMPLES) \
			--testsdir=$(QT_HOSTPKG_TESTS) \
			--gcc-sysroot=no \
			--verbose=yes \
			--opensource \
			--confirm-license \
			--release=yes \
			--optimized-tools=no \
			--optimize-size=no \
			--strip=no \
			--shared=yes \
			--framework=no \
			--reduce-exports=no \
			--reduce-relocations=no \
			--pch=no \
			--ltcg=no \
			--use-gold-linker=no \
			--warnings-are-errors=no \
			--pkg-config=yes \
			--compile-examples=no \
			--make=libs \
			--nomake=tools \
			--nomake=examples \
			--gui=yes \
			--widgets=no \
			--dbus=no \
			--accessibility=no \
			--doubleconversion=qt \
			--glib=no \
			--eventfd=no \
			--inotify=no \
			--iconv=no \
			--icu=no \
			--pcre=qt \
			--zlib=qt \
			--journald=no \
			--syslog=no \
			--ssl=no \
			--libproxy=no \
			--system-proxies=no \
			--cups=no \
			--fontconfig=no \
			--freetype=qt \
			--harfbuzz=no \
			--gtk=no \
			--opengl=no \
			--opengles3=no \
			--egl=no \
			--xcb-xlib=no \
			--directfb=no \
			--eglfs=no \
			--gbm=no \
			--linuxfb=no \
			--xcb=no \
			--libudev=no \
			--evdev=no \
			--libinput=no \
			--mtdev=no \
			--tslib=no \
			--bundled-xcb-xinput=no \
			--xkbcommon=no \
			--gif=no \
			--ico=no \
			--libjpeg=no \
			--libpng=no \
			--sql-db2=no \
			--sql-ibase=no \
			--sql-mysql=no \
			--sql-oci=no \
			--sql-odbc=no \
			--sql-psql=no \
			--sql-sqlite=no \
			--sql-sqlite2=no \
			--sql-tds=no \
			--sqlite=no \
	)
endef

define Build/InstallDev
	$(call Build/Install/HostFiles,$(1))
	$(call Build/Install/Headers,$(1))
	$(call Build/Install/Cmakes,$(1))
	$(call Build/Install/Pkgconfigs,$(1))
	$(call Build/Install/Libs,$(1),*)
	$(call Build/Install/Plugins,$(1),*,*)

	# sed -i \
	#   -e "s|###TARGET_CFLAGS###||g" \
	#   -e "s|###TARGET_CXXFLAGS###||g" \
	#   -e "s|###TARGET_LDFLAGS###||g" \
	#   $(STAGING_DIR)/host/share/qt5/mkspecs/linux-openwrt-host-g++/qmake.conf
endef

define Host/Install
	$(call Host/Install/Default)

	$(CP) \
	  $(HOST_INSTALL_DIR)/* \
	  $(STAGING_DIR_HOST)/
endef

define Package/qt5base-core/install
	$(call Build/Install/Libs,$(1),libQt5Core)
endef

define Package/qt5base-concurrent/install
	$(call Build/Install/Libs,$(1),libQt5Concurrent)
endef

# define Package/qt5base-gl/install
# 	$(call Build/Install/Libs,$(1),libQt5OpenGL)
# endef

define Package/qt5base-gui/install
	$(call Build/Install/Libs,$(1),libQt5Gui)
	$(if $(BUILD_qt5base-gui_OPENGL),$(call Build/Install/Libs,$(1),libQt5OpenGL),)
endef

define Package/qt5base-network/install
	$(call Build/Install/Libs,$(1),libQt5Network)
endef

define Package/qt5base-printSupport/install
	$(call Build/Install/Libs,$(1),libQt5PrintSupport)
endef

define Package/qt5base-sql/install
	$(call Build/Install/Libs,$(1),libQt5Sql)
endef

define Package/qt5base-test/install
	$(call Build/Install/Libs,$(1),libQt5Test)
endef

define Package/qt5base-widgets/install
	$(call Build/Install/Libs,$(1),libQt5Widgets)
endef

define Package/qt5base-xml/install
	$(call Build/Install/Libs,$(1),libQt5Xml)
endef

define Package/qt5base-eglfs-device-integration/install
	$(call Build/Install/Libs,$(1),libQt5EglFSDeviceIntegration)
	$(call Build/Install/Plugins,$(1),egldeviceintegrations,libqeglfs-kms-egldevice-integration)
endef

define Package/qt5base-plugin-bearer-generic/install
	$(call Build/Install/Plugins,$(1),bearer,libqgenericbearer)
endef

define Package/qt5base-plugin-input-libinput/install
	$(call Build/Install/Plugins,$(1),generic,libqlibinputplugin)
endef

define Package/qt5base-plugin-input-evdevkeyboard/install
	$(call Build/Install/Plugins,$(1),generic,libqevdevkeyboardplugin)
endef

define Package/qt5base-plugin-input-evdevmouse/install
	$(call Build/Install/Plugins,$(1),generic,libqevdevmouseplugin)
endef

define Package/qt5base-plugin-input-evdevtablet/install
	$(call Build/Install/Plugins,$(1),generic,libqevdevtabletplugin)
endef

define Package/qt5base-plugin-input-evdevtouch/install
	$(call Build/Install/Plugins,$(1),generic,libqevdevtouchplugin)
endef

#define Package/qt5base-plugin-imageformats-gif/install
#	$(call Build/Install/Plugins,$(1),imageformats,libqgif)
#endef

define Package/qt5base-plugin-imageformats-ico/install
	$(call Build/Install/Plugins,$(1),imageformats,libqico)
endef

define Package/qt5base-plugin-imageformats-jpeg/install
	$(call Build/Install/Plugins,$(1),imageformats,libqjpeg)
endef

#define Package/qt5base-plugin-imageformats-png/install
#	$(call Build/Install/Plugins,$(1),imageformats,libqpng)
#endef

define Package/qt5base-plugin-platforms-linuxfb/install
	$(call Build/Install/Plugins,$(1),platforms,libqlinuxfb)
endef

define Package/qt5base-plugin-platforms-vnc/install
	$(call Build/Install/Plugins,$(1),platforms,libqvnc)
endef

define Package/qt5base-plugin-platforms-eglfs/install
	$(call Build/Install/Libs,$(1),libQt5EglFsKmsSupport)
	$(call Build/Install/Libs,$(1),libQt5EglFSDeviceIntegration)
	$(call Build/Install/Plugins,$(1),egldeviceintegrations,libqeglfs-kms-egldevice-integration)
	$(call Build/Install/Plugins,$(1),egldeviceintegrations,libqeglfs-kms-integration)
	$(call Build/Install/Plugins,$(1),platforms,libqeglfs)
endef

define Package/qt5base-plugin-platforms-minimalegl/install
	$(call Build/Install/Plugins,$(1),platforms,libqminimalegl)
endef

define Package/qt5base-plugin-platforms-minimal/install
	$(call Build/Install/Plugins,$(1),platforms,libqminimal)
endef

define Package/qt5base-plugin-platforms-offscreen/install
	$(call Build/Install/Plugins,$(1),platforms,libqoffscreen)
endef

#define Package/qt5base-plugin-sqldrivers-sqlite/install
#	$(call Build/Install/Plugins,$(1),sqldrivers,libqsqlite)
#endef

define Package/qt5base-examples/install
	$(call Build/Install/Examples,$(1))
endef

# $(eval $(call BuildPackage,qt5base))
$(eval $(call BuildPackage,qt5base-core))
$(eval $(call BuildPackage,qt5base-concurrent))
$(eval $(call BuildPackage,qt5base-gui))
# $(eval $(call BuildPackage,qt5base-gl))
$(eval $(call BuildPackage,qt5base-network))
$(eval $(call BuildPackage,qt5base-printSupport))
$(eval $(call BuildPackage,qt5base-sql))
$(eval $(call BuildPackage,qt5base-test))
$(eval $(call BuildPackage,qt5base-widgets))
$(eval $(call BuildPackage,qt5base-xml))
#$(eval $(call BuildPackage,qt5base-eglfs-device-integration))
$(eval $(call BuildPackage,qt5base-plugin-bearer-generic))
$(eval $(call BuildPackage,qt5base-plugin-input-libinput))
$(eval $(call BuildPackage,qt5base-plugin-input-evdevkeyboard))
$(eval $(call BuildPackage,qt5base-plugin-input-evdevmouse))
$(eval $(call BuildPackage,qt5base-plugin-input-evdevtablet))
$(eval $(call BuildPackage,qt5base-plugin-input-evdevtouch))
#$(eval $(call BuildPackage,qt5base-plugin-imageformats-gif))
$(eval $(call BuildPackage,qt5base-plugin-imageformats-ico))
$(eval $(call BuildPackage,qt5base-plugin-imageformats-jpeg))
#$(eval $(call BuildPackage,qt5base-plugin-imageformats-png))
$(eval $(call BuildPackage,qt5base-plugin-platforms-linuxfb))
$(eval $(call BuildPackage,qt5base-plugin-platforms-eglfs))
$(eval $(call BuildPackage,qt5base-plugin-platforms-minimalegl))
$(eval $(call BuildPackage,qt5base-plugin-platforms-minimal))
$(eval $(call BuildPackage,qt5base-plugin-platforms-offscreen))
$(eval $(call BuildPackage,qt5base-plugin-platforms-vnc))
#$(eval $(call BuildPackage,qt5base-plugin-sqldrivers-sqlite))
$(eval $(call BuildPackage,qt5base-examples))
$(eval $(call HostBuild))
