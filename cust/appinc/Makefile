#
# Copyright (C) 2006-2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=appinc
PKG_VERSION:=0.5

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

# General info: see the readme.md
# logic:
# extract all defined bundles and there squashfses to (directory per image)
#   $(TOPDIR)/build_dir/target*/appinc*/apps/<myimage_1>
# In that dir you have
# ./squashfs-root         ->  the extracted rootfs of this image
# ./squashfs-config-full  ->  the extracted config of this image
# ./squashfs-config       ->  the config but stripped the "configs to ignore"
# Then all images will be combined in
#   $(TOPDIR)/build_dir/target*/appinc*/apps/final
# during this combining already a first check will be done for config file conflicts
# In the install step:
# - install ALL <final> files
# - make symlinks in $(TOPDIR)/config/config to the config files in <final>
#   Here we also check for config conflicts
#   note: since we use symlinks in the config we need to path the sdk target makefile (see TargetMakefileFixup)

CFGS_IGNORED:=$(CONFIG_appinc_IGNORECFGS)
CFGCONFLICT_TEXT:="*** ERROR ***: config file conflict, search for CFGCONFLICT cfgfile="

define Package/$(PKG_NAME)
  SECTION:=base
  CATEGORY:=Base system
  TITLE:=appinc
  DEPENDS:=
endef


define Package/$(PKG_NAME)/config
  config $(PKG_NAME)_FILE
    string "list of images to include (relative to sdk rootdir ($(TOPDIR)))"
  config $(PKG_NAME)_IGNORECFGS
    string "list of config files to ignore"
endef

# we don't want to copy our config files to the $(TOPDIR)/config folder because
# then we don't know any more which are the user config files and which came from the included packages.
# Because of this we use symlinks. However the current sdk target makefile will not follow these symlinks
# and the resulting config will contain the symlinks instead of the referenced files.
# This fixup will adjust the sdk/target makefile to fix this. Packages should not change the
# top makefiles but it is better then the alternative (impossible to clean the $(TOPDIR)/config
define $(PKG_NAME)/TargetMakefileFixup
	[ ! -e $(TOPDIR)/target/linux/imx28/Makefile ] || sed -i 's@$$$$(MKSQUASHFS_CMD) $$$$(TOPDIR)/config@rm -rf $$$$(BUILD_DIR)/config \&\& cp -r -L $$$$(TOPDIR)/config $$$$(BUILD_DIR) \&\& $$$$(MKSQUASHFS_CMD) $$$$(BUILD_DIR)/config@' $(TOPDIR)/target/linux/imx28/Makefile
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(call $(PKG_NAME)/TargetMakefileFixup)
endef

define $(PKG_NAME)/extractApp
	# $(1) is the file $(2) the dir to extract
	echo "llala" $(1) $(2)
	echo $(suffix $(1))
	# cp the app (even it was a bin)
	$(CP) $(1) $(2)/app2inc.zip
	# conver bin to zip (if needed)
	[ ! $(suffix $(1)) == ".bin" ] || (\
		echo -n "504b0304" | xxd -ps -r | dd conv=notrunc of=$(2)/app2inc.zip bs=4 count=1 1>/dev/null 2>&1)
	# unzip the app
	unzip $(2)/app2inc.zip -d $(2)
	# unsquash the config & reinstall in config/config
	# note: when the config is empty unsquashfs will fail so we just make the directory
	cd $(2) && ( unsquashfs4 config.squashfs || mkdir -p $(2)/squashfs-root/config )
	mv $(2)/squashfs-root $(2)/squashfs-config
	cp -r $(2)/squashfs-config $(2)/squashfs-config-full
	-cd $(2)/squashfs-config/config && rm $(subst ",,$(CFGS_IGNORED))
	# unsquash the rootfs
	cd $(2) && unsquashfs4 root.squashfs
	# mv the isv to isv-appinc (which needs to be called by the parent app) (but only if it exists)
	[ ! -e $(2)/squashfs-root/isv ] || mv $(2)/squashfs-root/isv $(2)/squashfs-root/isv-$(PKG_NAME)-$(notdir $(basename $(1)))
endef

#$(1)=$(PKG_BUILD_DIR)/squashfs-config/config
#$(2)=$(TOPDIR)/config/config
define $(PKG_NAME)/cpConfigs
	# copy config files (if they do not exists) or check if the same otherwise
	# $(1) is the src config dir. $(2) is the destination directory
	-$(CP) -r -n $(1)/* $(2)
	cd $(1) && find . -type f | xargs -I{} \
		diff -q $(1)/{} $(2)/{} || (echo $(CFGCONFLICT_TEXT) && false)
endef

AI_ABS_FILE=$(TOPDIR)/$(call qstrip,$(APPFILE))
AI_TAG=$(notdir $(basename $(AI_ABS_FILE)))

define Build/Compile
	# we don't support the old method any more, you need to explicit config the filename you want to include
	[ "$(call qstrip,$(CONFIG_appinc_FILE))" ] || ( echo "ERROR: appinc: need to configure a list of files to include via make menuconfig" && false )
	-rm -rf $(PKG_BUILD_DIR)/apps/final
	mkdir -p $(PKG_BUILD_DIR)/apps/final/squashfs-config/config
	$(foreach APPFILE,$(call qstrip,$(CONFIG_appinc_FILE)),
		-rm -rf $(PKG_BUILD_DIR)/apps/$(AI_TAG)
		mkdir -p $(PKG_BUILD_DIR)/apps/$(AI_TAG)
		$(call $(PKG_NAME)/extractApp,$(AI_ABS_FILE),$(PKG_BUILD_DIR)/apps/$(AI_TAG))
		# copy rootfs of each app to /final: in case both apps contain a particular file the one of the last package will be taken
		$(CP) -r $(PKG_BUILD_DIR)/apps/$(AI_TAG)/squashfs-root $(PKG_BUILD_DIR)/apps/final
		# copy the wanted config files
		$(call $(PKG_NAME)/cpConfigs,$(PKG_BUILD_DIR)/apps/$(AI_TAG)/squashfs-config/config,$(PKG_BUILD_DIR)/apps/final/squashfs-config/config)
	)
endef

define Build/InstallDev
	echo "nothing to do"
endef

define Build/Clean
	# remove all previous links created by appinc
	for LINK in $(TOPDIR)/config/config/*; do \
		echo $$LINK && readlink $$LINK | grep $(PKG_NAME) && rm -rf $$LINK || true; done
endef

define Package/$(PKG_NAME)/install
	# copy all root files
	$(CP) -r $(PKG_BUILD_DIR)/apps/final/squashfs-root/* $(1)/
	# remove all previous links created by appinc
	for LINK in $(TOPDIR)/config/config/*; do \
		echo $$$$LINK && readlink $$$$LINK | grep $(PKG_NAME) && rm -rf $$$$LINK || true; done
	# link all config files to the config dir, but fail if they already exists (conflict with user added config file)
	cd $(TOPDIR)/config/config && [ ! "$$$$(ls -A $(PKG_BUILD_DIR)/apps/final/squashfs-config/config)" ] || \
		for CFGFILE in $(PKG_BUILD_DIR)/apps/final/squashfs-config/config/*; do \
			ln -s -r $$$$CFGFILE $$$$(basename $$$$CFGFILE) || CFAIL=y; \
			[ "$$$$CFAIL" ] && echo $(CFGCONFLICT_TEXT) $$$$(basename $$$$CFGFILE); [ "$$$$CFAIL" ] && break; \
			 done; \
		[ ! "$$$$CFAIL" ] || false
endef


$(eval $(call BuildPackage,$(PKG_NAME)))
