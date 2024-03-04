include $(TOPDIR)/rules.mk

PKG_NAME:=chirpstack-gateway-bridge
PKG_VERSION:=4.0.10
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/chirpstack/chirpstack-gateway-bridge.git

PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_SOURCE_VERSION).tar.gz
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_MIRROR:=0

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

GO_PKG:=github.com/brocaar/chirpstack-gateway-bridge
GO_PKG_BUILD_PKG:=github.com/brocaar/chirpstack-gateway-bridge/cmd/chirpstack-gateway-bridge

include $(INCLUDE_DIR)/package.mk
# golang-package.mk should be relocate regarding the env
include ../../lang/golang/golang-package.mk

define Package/chirpstack-gateway-bridge
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=LoRaWAN
  TITLE:=Chirpstack Gateway Bridge
  DEPENDS:=$(GO_ARCH_DEPENDS) +mosquitto
endef

define Package/chirpstack-gateway-bridge/description
  ChirpStack Gateway Bridge is a service which converts LoRa packet-forwarder
  protocols into a ChirpStack Network Server common protocol (JSON and Protobuf).
endef

# Do not attempt default downloads. Directly clone from git.
define Download/Defaults
endef

GO_PKG_LDFLAGS:=-s -w
GO_PKG_LDFLAGS_X:=main.version=$(PKG_VERSION)

define Package/chirpstack-gateway-bridge/install
	$(call GoPackage/Package/Install/Bin,$(1))

# 	$(INSTALL_DIR) $(1)/etc/init.d/
# 	$(INSTALL_BIN) ./files/chirpstack-gateway-bridge.init $(1)/etc/init.d/chirpstack-gateway-bridge

# 	$(INSTALL_DIR) $(1)/etc/chirpstack-gateway-bridge
# 	$(INSTALL_DATA) ./files/chirpstack-gateway-bridge.toml $(1)/etc/chirpstack-gateway-bridge/
endef

$(eval $(call GoBinPackage,chirpstack-gateway-bridge))
$(eval $(call BuildPackage,chirpstack-gateway-bridge))
