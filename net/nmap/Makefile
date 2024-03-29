#
# Copyright (C) 2011 Nuno Goncalves <nunojpg@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

#TODO
#
# * uClibcpp not supported, performance +100x slower. libstdcpp is being used. https://bugs.busybox.net/show_bug.cgi?id=2545
# * liblua is still not recognized/blindly accepted. Impossible to compile with support
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nmap
PKG_VERSION:=5.51
PKG_RELEASE:=4

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=http://nmap.org/dist-old/
PKG_MD5SUM:=0b80d2cb92ace5ebba8095a4c2850275

PKG_INSTALL:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

NMAP_DEPENDS:=+libnl +libpcap +libstdcpp
NCAT_DEPENDS:=+libpcap
NPING_DEPENDS:=+libnl +libpcap +libpthread +libstdcpp
NDIFF_DEPENDS:=+python

define Package/nmap/default
  SUBMENU:=NMAP Suite
  SECTION:=net
  CATEGORY:=Network
  URL:=http://nmap.org/
  MAINTAINER:=Nuno Goncalves <nunojpg@gmail.com>
endef

define Package/nmap
$(call Package/nmap/default)
  DEPENDS:=$(NMAP_DEPENDS)
  VARIANT:=nossl
  TITLE:=Utility for network exploration or security auditing
endef

define Package/nmap-ssl
$(call Package/nmap/default)
  DEPENDS:=$(NMAP_DEPENDS) +libopenssl
  VARIANT:=ssl
  TITLE:=Nmap (with OpenSSL support)
endef

define Package/ncat
$(call Package/nmap/default)
  DEPENDS:=$(NCAT_DEPENDS)
  VARIANT:=nossl
  TITLE:=Much-improved reimplementation of Netcat
endef

define Package/ncat-ssl
$(call Package/nmap/default)
  DEPENDS:=$(NCAT_DEPENDS) +libopenssl
  VARIANT:=ssl
  TITLE:=Ncat (with OpenSSL support)
endef

define Package/nping
$(call Package/nmap/default)
  DEPENDS:=$(NPING_DEPENDS)
  VARIANT:=nossl
  TITLE:=Network packet generation tool / ping utility
endef

define Package/ndiff
$(call Package/nmap/default)
  DEPENDS:=$(NDIFF_DEPENDS)
  VARIANT:=nossl
  TITLE:=Utility to compare the results of Nmap scans
endef

CONFIGURE_ARGS += \
	--with-libdnet=included \
	--with-libpcre=included \
	--with-libpcap="$(STAGING_DIR)/usr" \
	--without-liblua \
	--without-zenmap

CONFIGURE_VARS += CXXFLAGS="$$$$CXXFLAGS -fno-builtin -fno-rtti"

ifeq ($(BUILD_VARIANT),ssl)
	CONFIGURE_ARGS += --with-openssl="$(STAGING_DIR)/usr"
else
	CONFIGURE_ARGS += --without-openssl
endif

define Package/nmap/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nmap $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/share/nmap
	$(CP) $(PKG_INSTALL_DIR)/usr/share/nmap/* $(1)/usr/share/nmap/
endef

define Package/nmap-ssl/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nmap $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/share/nmap
	$(CP) $(PKG_INSTALL_DIR)/usr/share/nmap/* $(1)/usr/share/nmap/
endef

define Package/ncat/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ncat $(1)/usr/bin/
endef

define Package/ncat-ssl/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ncat $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/share/ncat
	$(CP) $(PKG_INSTALL_DIR)/usr/share/ncat/ca-bundle.crt $(1)/usr/share/ncat/
endef

define Package/ndiff/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ndiff $(1)/usr/bin/
endef

define Package/nping/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nping $(1)/usr/bin/
endef


$(eval $(call BuildPackage,nmap))
$(eval $(call BuildPackage,nmap-ssl))
$(eval $(call BuildPackage,ncat))
$(eval $(call BuildPackage,ncat-ssl))
$(eval $(call BuildPackage,nping))
$(eval $(call BuildPackage,ndiff))
