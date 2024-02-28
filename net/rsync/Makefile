#
# Copyright (C) 2007-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=rsync
PKG_VERSION:=3.2.7
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://download.samba.org/pub/$(PKG_NAME)/src
PKG_MD5SUM:=f216f350ef56b9ba61bc313cb6ec2ed6

PKG_MAINTAINER:=Maxim Storchak <m.storchak@gmail.com>
PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:rsync:rsync

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/rsync
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=File Transfer
  TITLE:=Fast remote file copy program (like rcp)
  DEPENDS:=+libpopt
  URL:=http://rsync.samba.org/
endef

CONFIGURE_ARGS += \
	--without-included-popt \
	--without-included-zlib \
	--disable-debug \
	--disable-asm \
	--disable-lz4 \
	--disable-locale \
	--disable-md2man \
	--disable-openssl \
	--disable-simd \
	--disable-roll-simd \
	--disable-xxhash \
	--disable-iconv \
	--disable-zstd \
	--disable-xattr-support \
	--disable-acl-support \
	--disable-ipv6

define Package/rsyncd
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=File Transfer
  TITLE:=Rsync daemon
  DEPENDS:=+rsync
  URL:=https://rsync.samba.org/
endef

define Package/rsync/description
 rsync is a program that allows files to be copied to and from remote machines
 in much the same way as rcp. It has many more options than rcp, and uses the
 rsync remote-update protocol to greatly speed up file transfers when the
 destination file already exists.

 The rsync remote-update protocol allows rsync to transfer just the differences
 between two sets of files across the network link.
endef

define Package/rsync/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/$(PKG_NAME) $(1)/usr/bin/
endef

define Package/rsyncd/description
 rsyncd is a configuration file and initscript to utilize rsync as a daemon. It
 uses the same binary as rsync.
endef

define Package/rsyncd/conffiles
/etc/rsyncd.conf
endef

define Package/rsyncd/install
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_DATA) ./files/rsyncd.conf $(1)/etc/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/rsyncd.init $(1)/etc/init.d/rsyncd
endef

$(eval $(call BuildPackage,rsync))
$(eval $(call BuildPackage,rsyncd))
