include $(TOPDIR)/rules.mk

PKG_NAME:=ocpp-go-lodo
PKG_VERSION:=0.15.x
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/lorenzodonini/ocpp-go.git
PKG_REV:=8a6b9f8ab9ea09e19e7c8a6b777ae906eccb3d77

PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_REV).tar.gz

PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=golang/host
GO_PKG:=github.com/lorenzodonini/ocpp-go/example/1.6/cs

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/$(PKG_NAME)/config
  config $(PKG_NAME)_autostart
    bool "add script to autostart the example central cerver"
    depends PACKAGE_$(PKG_NAME)
    default y
endef

define Package/$(PKG_NAME)
  SECTION:=base
  CATEGORY:=Base system
  TITLE:=OCPP central server example (GO - Lorenzo Donini impl)
  DEPENDS:=$(GO_ARCH_DEPENDS)
endef

# strip package
GO_PKG_LDFLAGS:=-s -w


define Package/$(PKG_NAME)/install
	$(call GoPackage/Package/Install/Bin,$(1))
	[ $(CONFIG_$(PKG_NAME)_autostart) == "y" ] && $(INSTALL_BIN) files/start.isv $(1)/isv
endef

$(eval $(call GoBinPackage,$(PKG_NAME)))
$(eval $(call BuildPackage,$(PKG_NAME)))

