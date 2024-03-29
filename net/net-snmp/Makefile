#
# Copyright (C) 2006-2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=net-snmp
PKG_VERSION:=5.9.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@SF/$(PKG_NAME)
PKG_MD5SUM:=e5ee202dfb15000342354d64f1837d19
PKG_FIXUP = libtool no-autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/net-snmp/Default
  SECTION:=net
  CATEGORY:=Network
  URL:=http://www.net-snmp.org/
endef

define Package/net-snmp/Default/description
 Simple Network Management Protocol (SNMP) is a widely used protocol for
 monitoring the health and welfare of network equipment (eg. routers),
 computer equipment and even devices like UPSs. Net-SNMP is a suite of
 applications used to implement SNMP v1, SNMP v2c and SNMP v3 using both
 IPv4 and IPv6.
endef

define Package/libnetsnmp
$(call Package/net-snmp/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libelf +libcrypto +libnl +!TARGET_imx28:libpcre
  TITLE:=Open source SNMP implementation (libraries)
endef

define Package/libnetsnmp/description
$(call Package/net-snmp/Default/description)
 .
 This package contains shared libraries, needed by other programs.
endef


define Package/snmp-mibs
$(call Package/net-snmp/Default)
  TITLE:=Open source SNMP implementation (MIB-files)
endef

define Package/snmp-mibs/description
$(call Package/net-snmp/Default/description)
 .
 This package contains SNMP MIB-Files.
endef


define Package/snmp-utils
$(call Package/net-snmp/Default)
  DEPENDS:=+libnetsnmp
  TITLE:=Open source SNMP implementation (utilities)
endef

define Package/snmp-utils/description
$(call Package/net-snmp/Default/description)
 .
 This package contains SNMP client utilities:
   - snmpget
   - snmpset
   - snmpstatus
   - snmptest
   - snmptrap
   - snmpwalk
endef

define Package/snmpd
$(call Package/net-snmp/Default)
  DEPENDS:=+libnetsnmp
  TITLE:=Open source SNMP implementation (daemon)
endef

define Package/snmpd/description
$(call Package/net-snmp/Default/description)
 .
 This package contains the SNMP agent, dynamically linked.
endef

define Package/snmpd-static
$(call Package/net-snmp/Default)
  TITLE:=Open source SNMP implementation (daemon)
  DEPENDS:=+libelf +libcg +libcrypto +libnl +!TARGET_imx28:libpcre
endef

define Package/snmpd-static/description
$(call Package/net-snmp/Default/description)
 This package contains the SNMP agent, statically linked.
endef


SNMP_MIB_MODULES_INCLUDED = \
	agentx \
	host/hr_device \
	host/hr_disk \
	host/hr_filesys \
	host/hr_network \
	host/hr_partition \
	host/hr_proc \
	host/hr_storage \
	host/hr_system \
	if-mib/ifXTable \
	ip-mib/inetNetToMediaTable \
	mibII/at \
	mibII/icmp \
	mibII/ifTable \
	mibII/ip \
	mibII/snmp_mib \
	mibII/sysORTable \
	mibII/system_mib \
	mibII/tcp \
	mibII/udp \
	mibII/vacm_conf \
	mibII/vacm_context \
	mibII/vacm_vars \
	snmpv3/snmpEngine \
	snmpv3/snmpMPDStats \
	snmpv3/usmConf \
	snmpv3/usmStats \
	snmpv3/usmUser \
	tunnel \
	ucd-snmp/disk \
	ucd-snmp/extensible \
	ucd-snmp/loadave \
	ucd-snmp/memory \
	ucd-snmp/pass \
	ucd-snmp/proc \
	ucd-snmp/vmstat \
	util_funcs \
	utilities/execute \

SNMP_MIB_MODULES_EXCLUDED = \
	agent_mibs \
	hardware \
	host \
	if-mib \
	ip-mib \
	mibII \
	notification \
	notification-log-mib \
	snmpv3mibs \
	target \
	tcp-mib \
	ucd_snmp \
	udp-mib \
	utilities \
	ieee802dot11 \
	disman \

# These two need to be enabled if disman is required
# disman/event \
# disman/schedule \
# future optimizations
# --disable-privacy \
# --disable-md5 \

SNMP_TRANSPORTS_INCLUDED = Callback UDP Unix

SNMP_TRANSPORTS_EXCLUDED = TCP TCPIPv6

TARGET_CFLAGS += $(FPIC)

CONFIGURE_ARGS += \
	--enable-mfd-rewrites \
	--enable-shared \
	--enable-static \
	--with-endianness=little \
	--with-logfile=/var/log/snmpd.log \
	--with-persistent-directory=/usr/lib/snmp/ \
	--with-default-snmp-version=1 \
	--with-sys-contact=admin@localhost \
	--with-sys-location=Unknown \
	--enable-applications \
	--disable-debugging \
	--disable-manuals \
	--disable-mibs \
	--disable-mib-loading \
	--enable-agentx-dom-sock-only \
	--disable-scripts \
	--with-out-mib-modules="$(SNMP_MIB_MODULES_EXCLUDED)" \
	--with-mib-modules="$(SNMP_MIB_MODULES_INCLUDED)" \
	--with-out-transports="$(SNMP_TRANSPORTS_EXCLUDED)" \
	--with-transports="$(SNMP_TRANSPORTS_INCLUDED)" \
	--with-openssl \
	--without-libwrap \
	--without-mysql \
	--without-rpm \
	--without-zlib \
	 $(call autoconf_bool,CONFIG_IPV6,ipv6) \
	--disable-perl-cc-checks \
	--disable-embedded-perl \
	--without-perl-modules

ifeq ($(CONFIG_IPV6),y)
SNMP_TRANSPORTS_INCLUDED+= UDPIPv6
endif

TARGET_LDFLAGS += -L$(TOOLCHAIN_DIR)/usr/lib

ifdef CONFIG_PACKAGE_snmpd-static
  define Build/Compile/static
	$(MAKE) -C $(PKG_BUILD_DIR)/agent \
		LDFLAGS="$(TARGET_LDFLAGS) -lm -static" \
		EXEEXT="-static" \
		snmpd-static
  endef
endif

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		INSTALL_PREFIX="$(PKG_INSTALL_DIR)" \
		LDFLAGS="$(TARGET_LDFLAGS) -lm -lc" \
		all install
	$(Build/Compile/static)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(2)/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/net-snmp-config $(2)/bin/
	$(SED) 's,=/usr,=$(STAGING_DIR)/usr,g' $(2)/bin/net-snmp-config
	$(INSTALL_DIR) $(STAGING_DIR)/usr/bin
	$(LN) $(STAGING_DIR)/host/bin/net-snmp-config $(STAGING_DIR)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/net-snmp $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetsnmp{,agent,helpers,mibs}.{a,so*} $(1)/usr/lib/
endef

define Package/libnetsnmp/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetsnmp{,agent,helpers,mibs}.so.* $(1)/usr/lib/
endef

define Package/snmpd/conffiles
/etc/default/snmpd
/etc/config/snmpd
endef

define Package/snmpd/install
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/snmpd.conf $(1)/etc/config/snmpd
	$(INSTALL_DIR) $(1)/etc/snmp
	$(LN) /var/run/snmpd.conf $(1)/etc/snmp/
	$(INSTALL_DIR) $(1)/etc/default
	$(INSTALL_DATA) ./files/snmpd.default $(1)/etc/default/snmpd
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/snmpd.init $(1)/etc/init.d/snmpd
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/snmpd $(1)/usr/sbin/snmpd
endef

define Package/snmpd-static/conffiles
/etc/default/snmpd
/etc/config/snmpd
endef

define Package/snmpd-static/install
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/snmpd.conf $(1)/etc/config/snmpd
	$(INSTALL_DIR) $(1)/etc/snmp
	$(LN) /var/run/snmpd.conf $(1)/etc/snmp/
	$(INSTALL_DIR) $(1)/etc/default
	$(INSTALL_DATA) ./files/snmpd.default $(1)/etc/default/snmpd
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/snmpd.init $(1)/etc/init.d/snmpd
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/agent/snmpd-static $(1)/usr/sbin/snmpd
endef

define Package/snmp-utils/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/snmp{get,set,status,test,trap,walk} $(1)/usr/bin/
endef

$(eval $(call BuildPackage,libnetsnmp))
$(eval $(call BuildPackage,snmp-utils))
$(eval $(call BuildPackage,snmpd))
$(eval $(call BuildPackage,snmpd-static))
