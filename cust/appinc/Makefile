#
# Copyright (C) 2006-2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=appinc
PKG_VERSION:=0.2

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

# With this package it is possible to bundle an already builded app with your own app
# 
# usage:
# 1) copy the app you want to include to the "files" subdirectory. The app binary (.zip or .bin) is
#    the same as you use to upgrade the cloudgate.
#    UPDATE: it is also possible to give the file location in de .config file like CONFIG_appinc_FILE="myapptoinclude.bin"
# 2) in case of conflicting config files, see CFGCONFLICT
# 3) in your own startup file (isv) you need to call "/rom/mnt/cust/isv-appinc" to start the included app.
#    The "isv" file of the included app will be renamed to isv-appinc
# Note: with the current SDK the config files of the included app will be copied to config/config
# which will not be cleaned. this means that if you do not include the app any more or update the
# included app with a different version you might want to manually remove all config files in config/config
# which do not belong to your app.

# TODO: the current SDK only takes config files from $(TOPDIR)/config/config
# this means we need to copy them there, however a clean will not remove these files !!
# the best we can do for now is just to give an error when both the app being built as
# the app being included try to modify the same config file (CFGCONFLICT)
# To fix this we need to adjust the base SDK

# CFGCONFLICT
# the included app can modify the same config files as the app which is being build.
# in case there is a conflict, manually merge the files and put the result in config/config
# then set the CFGS_IGNORED here so the configs are not copied from this app
# example CFGS_IGNORED:="m2mweb devices"
# can also be customized via the config "CONFIG_appinc_IGNORECFGS"
CFGS_IGNORED:=$(CONFIG_appinc_IGNORECFGS)
CFGCONFLICT_TEXT:="ERROR: config file conflict, search for CFGCONFLICT"

define Package/$(PKG_NAME)
  SECTION:=base
  CATEGORY:=Base system
  TITLE:=appinc
  DEPENDS:=
endef

define Package/$(PKG_NAME)/config
  config $(PKG_NAME)_FILE
    string "the filename of the package to include"
  config $(PKG_NAME)_IGNORECFGS
    string "a list of config names to exclude"
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(CP) files/* $(PKG_BUILD_DIR)/
	$(if $(CONFIG_$(PKG_NAME)_FILE),pwd && $(CP) $(TOPDIR)/$(CONFIG_$(PKG_NAME)_FILE) $(PKG_BUILD_DIR)/)
endef

define Build/Compile
	# conver bin to zip (if needed)
	[ ! -e $(PKG_BUILD_DIR)/*.bin ] || (\
		mv $(PKG_BUILD_DIR)/*.bin $(PKG_BUILD_DIR)/app2inc.zip && \
		echo -n "504b0304" | xxd -ps -r | dd conv=notrunc of=$(PKG_BUILD_DIR)/app2inc.zip bs=4 count=1 1>/dev/null 2>&1)
	# unzip the app
	unzip $(PKG_BUILD_DIR)/*.zip -d $(PKG_BUILD_DIR)
	# unsquash the config & reinstall in config/config
	cd $(PKG_BUILD_DIR) && unsquashfs4 config.squashfs
	mv $(PKG_BUILD_DIR)/squashfs-root $(PKG_BUILD_DIR)/squashfs-config
	cp -r $(PKG_BUILD_DIR)/squashfs-config $(PKG_BUILD_DIR)/squashfs-config-full
	-cd $(PKG_BUILD_DIR)/squashfs-config/config && rm $(subst ",,$(CFGS_IGNORED))
	# unsquash the rootfs
	cd $(PKG_BUILD_DIR) && unsquashfs4 root.squashfs
	# mv the isv to isv-appinc (which needs to be called by the parent app)
	mv $(PKG_BUILD_DIR)/squashfs-root/isv $(PKG_BUILD_DIR)/squashfs-root/isv-$(PKG_NAME)
endef

define Build/InstallDev
	echo "nothing to do"
endef

define Package/$(PKG_NAME)/install
	# copy all files
	$(CP) -r $(PKG_BUILD_DIR)/squashfs-root/* $(1)/
	# copy config files (if they do not exists) + check if the same otherwise
	-$(CP) -r -n $(PKG_BUILD_DIR)/squashfs-config/config/* $(TOPDIR)/config/config
	cd $(PKG_BUILD_DIR)/squashfs-config/config && find . -type f | xargs -I{} \
		diff -q $(PKG_BUILD_DIR)/squashfs-config/config/{} $(TOPDIR)/config/config/{} || (echo $(CFGCONFLICT_TEXT) && false)
endef


$(eval $(call BuildPackage,$(PKG_NAME)))
