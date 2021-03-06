#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gdb
PKG_VERSION:=7.10
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@GNU/gdb
PKG_MD5SUM:=2a35bac41fa8e10bf04f3a0dd7f7f363

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_LICENSE:=GPL-3.0+

include $(INCLUDE_DIR)/package.mk

define Package/gdb/Default
  SECTION:=devel
  CATEGORY:=Development
  DEPENDS:=+!USE_MUSL:libthread-db +PACKAGE_zlib:zlib @!TARGET_mipselsf
  URL:=http://www.gnu.org/software/gdb/
endef

define Package/gdb
$(call Package/gdb/Default)
  TITLE:=GNU Debugger
  DEPENDS+=+libreadline +libncurses +zlib
endef

define Package/gdb/description
GDB, the GNU Project debugger, allows you to see what is going on `inside'
another program while it executes -- or what another program was doing at the
moment it crashed.
endef

define Package/gdbserver
$(call Package/gdb/Default)
  TITLE:=Remote server for GNU Debugger
endef

define Package/gdbserver/description
GDBSERVER is a program that allows you to run GDB on a different machine than the
one which is running the program being debugged.
endef

# XXX: add --disable-werror to prevent build failure with arm
CONFIGURE_ARGS+= \
	--with-system-readline \
	--without-expat \
	--without-lzma \
	--disable-werror

CONFIGURE_VARS+= \
	ac_cv_search_tgetent="$(TARGET_LDFLAGS) -lncurses -lreadline"



# if binutils 2.4 are compiled before gdb - then ansidecl.h in opt/include (from binutils) will make compilation fail
# no -I or iquote options helped. So we rename the file before compilation and rename back after

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CPPFLAGS="-iquote $(PKG_BUILD_DIR)/include $(TARGET_CPPFLAGS)" \
		all
endef

define Build/Install
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CPPFLAGS="$(TARGET_CPPFLAGS)" \
		install-gdb
endef

define Package/gdb/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/gdb $(1)/opt/bin/
endef

define Package/gdbserver/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/gdbserver $(1)/opt/bin/
endef

$(eval $(call BuildPackage,gdb))
$(eval $(call BuildPackage,gdbserver))
