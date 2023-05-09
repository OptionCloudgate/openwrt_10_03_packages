include $(TOPDIR)/rules.mk

PKG_NAME:=chirpstack-gateway-bridge
PKG_VERSION:=4.0.8
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/chirpstack/chirpstack-gateway-bridge.git
PKG_REV:=6b202d1c4d1cc85e9b7fb4d7fb493f0b7c71c659

PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_REV).tar.gz
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_MIRROR:=0

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

GO_PKG:=github.com/brocaar/chirpstack-gateway-bridge
GO_PKG_BUILD_PKG:=github.com/brocaar/chirpstack-gateway-bridge/cmd/chirpstack-gateway-bridge

include $(INCLUDE_DIR)/package.mk
# golang-package.mk should be relocate regarding the env
include $(TOPDIR)/package/lang/golang/golang-package.mk

define Package/$(PKG_NAME)
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=LoRaWAN
  TITLE:=Chirpstack Gateway Bridge
  DEPENDS:=$(GO_ARCH_DEPENDS) +mosquitto
endef

define Package/$(PKG_NAME)/description
  ChirpStack Gateway Bridge is a service which converts LoRa packet-forwarder
  protocols into a ChirpStack Network Server common protocol (JSON and Protobuf).
endef

GO_PKG_LDFLAGS:=-s -w
GO_PKG_LDFLAGS_X:=main.version=$(PKG_VERSION)

define Package/$(PKG_NAME)/install
	$(call GoPackage/Package/Install/Bin,$(1))

# 	$(INSTALL_DIR) $(1)/etc/init.d/
# 	$(INSTALL_BIN) ./files/chirpstack-gateway-bridge.init $(1)/etc/init.d/chirpstack-gateway-bridge

# 	$(INSTALL_DIR) $(1)/etc/chirpstack-gateway-bridge
# 	$(INSTALL_DATA) ./files/chirpstack-gateway-bridge.toml $(1)/etc/chirpstack-gateway-bridge/
endef

$(eval $(call GoBinPackage,$(PKG_NAME)))
$(eval $(call BuildPackage,$(PKG_NAME)))
