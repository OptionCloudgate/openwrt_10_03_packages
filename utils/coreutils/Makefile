#
# Copyright (C) 2008-2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=coreutils
PKG_VERSION:=8.8
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@GNU/coreutils
PKG_MD5SUM:=bad596d9654e299eedea0eaf93f178fa
PKG_BUILD_DEPENDS:=libpthread

include $(INCLUDE_DIR)/package.mk

COREUTILS_APPLETS:=\
	base64 basename cat chcon chgrp chmod chown chroot cksum comm cp \
	csplit cut date dd dir dircolors dirname du echo env expand expr \
	factor false fmt fold groups head hostid id install join kill link \
	ln logname ls md5sum mkdir mkfifo mknod mktemp mv nice nl nohup nproc \
	od paste pathchk pinky pr printenv printf ptx pwd readlink rm rmdir \
	runcon seq sha1sum sha224sum sha256sum sha384sum sha512sum shred shuf \
	sleep sort split stat stdbuf stty su sum sync tac tail tee test timeout \
	touch tr true truncate tsort tty uname unexpand uniq unlink uptime users \
	vdir wc who whoami yes

FILES_stdbuf := usr/lib/coreutils/libstdbuf.so

define Package/coreutils/Default
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=The GNU core utilities
  URL:=http://www.gnu.org/software/coreutils/
endef

define Package/coreutils
  $(call Package/coreutils/Default)
  TITLE:=The GNU core utilities
  MENU:=1
endef

define Package/coreutils/description
	Full versions of standard GNU utilities.  Normally, you would not
	use this package, since the functionality in BusyBox is more than
        sufficient and much smaller.
endef

define GenPlugin
  define Package/$(1)
     $(call Package/coreutils/Default)
     DEPENDS:=coreutils
     TITLE:=Utility $(2) from the GNU core utilities
  endef

  define Package/$(1)/description
	Full version of standard GNU $(2) utility.  Normally, you would not
	use this package, since the functionality in BusyBox is more than
	sufficient.
  endef
endef

$(foreach a,$(COREUTILS_APPLETS),$(eval $(call GenPlugin,coreutils-$(a),$(a))))

CONFIGURE_VARS += \
	gl_cv_func_mbrtowc_incomplete_state=yes \
	gl_cv_func_mbrtowc_retval=yes \
	gl_cv_func_wcrtomb_retval=yes

ifneq ($(CONFIG_USE_UCLIBC),)
  CONFIGURE_VARS += \
	ac_cv_type_pthread_spinlock_t=$(if $(filter 0.9.30% 0.9.2% 0.9.31%,$(call qstrip,$(CONFIG_UCLIBC_VERSION))),no,yes)
endif

CONFIGURE_ARGS += \
	--enable-install-program=su

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		SHELL="/bin/bash" \
		all install install-root
endef

define Package/coreutils/install
	true
endef

define BuildPlugin
  define Package/$(1)/install
	$(INSTALL_DIR) $$(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/$(2) $$(1)/usr/bin/
	$(foreach f,$(FILES_$(2)),
		$(INSTALL_DIR) $$(1)/$(dir $(f))
		$(INSTALL_BIN) $(PKG_INSTALL_DIR)/$(f) $$(1)/$(f)
	)
  endef

  $$(eval $$(call BuildPackage,$(1)))
endef

$(eval $(call BuildPackage,coreutils))

$(foreach a,$(COREUTILS_APPLETS),$(eval $(call BuildPlugin,coreutils-$(a),$(a))))
