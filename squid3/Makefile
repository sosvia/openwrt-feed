#
# Copyright (C) 2006-2012 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=squid
PKG_VERSION:=3.4.5
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://www.squid-cache.org/Versions/v3/3.4/
PKG_SHA1SUM:=482c0946cd5a529cdd89cbd5dbbc8e3e7327a7ea

include $(INCLUDE_DIR)/package.mk

define Package/squid3/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  URL:=http://www.squid-cache.org/
endef

define Package/squid3
  $(call Package/squid3/Default)
  MENU:=1
  DEPENDS:=+libpthread +libopenssl +librt +libcom_err +libltdl +libstdcpp +libnetfilter-conntrack +libnettle
  TITLE:=full-featured Web proxy cache
  MAINTAINER:=Jorge Vargas <jorge.vargas@saint-tech.com>
endef

define Package/squid3/description
	Squid is a high-performance proxy caching server for web clients,
	supporting FTP, gopher, and HTTP data objects. Unlike traditional
	caching software, Squid handles all requests in a single,
	non-blocking, I/O-driven process.
endef

define Package/squid3/conffiles
/etc/squid/mime.conf
/etc/squid/squid.conf
endef

CONFIGURE_ARGS += \
	--config-cache \
	--enable-ssl \
	--datadir=/usr/share/squid \
	--libexecdir=/usr/lib/squid \
	--sysconfdir=/etc/squid \
	--enable-shared \
	--enable-static \
	--enable-x-accelerator-vary \
	--with-pthreads \
	--with-dl \
	--enable-icmp \
	--enable-kill-parent-hack \
	--enable-arp-acl \
	--enable-err-languages=English \
	--enable-default-err-language=English \
	--enable-linux-netfilter \
	--enable-icmp \
	--disable-external-acl-helpers \
	--disable-auth-negotiate \
	--disable-auth-ntlm \
	--disable-auth-digest \
	--disable-auth-basic \
	--disable-wccp \
	--disable-wccpv2 \
	--disable-snmp \
	--disable-htcp \
	--enable-underscores \
	--enable-cache-digests \
	--enable-referer-log \
	--enable-delay-pools \
	--enable-useragent-log \
	--disable-storeio \
	--enable-epoll \
	--with-maxfd=4096 \
	--without-libcap \
	--with-krb5-config=no \
	--enable-ssl-crtd \
	--enable-icap-client

#	--with-openssl=$(STAGING_DIR)/usr \
CONFIGURE_VARS += \
	ac_cv_header_linux_netfilter_ipv4_h=yes \
	ac_cv_epoll_works=yes \

define Build/Compile
	# pass INCLUDES to compile host sources against our OpenSSL, not the host one
	$(MAKE) -C $(PKG_BUILD_DIR)/lib \
		all
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
endef

define BuildPlugin
  define Package/$(1)/install
	$(INSTALL_DIR) $$(1)/usr/lib/squid
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/squid/$(2) $$(1)/usr/lib/squid/
  endef

  $$(eval $$(call BuildPackage,$(1)))
endef

define Package/squid3/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/squid $(1)/usr/sbin/

	$(INSTALL_DIR) $(1)/usr/lib/squid
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/squid/{ssl_crtd,unlinkd,pinger,log_file_daemon} $(1)/usr/lib/squid/

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/squid.config $(1)/etc/config/squid

	$(INSTALL_DIR) $(1)/etc/squid
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/etc/squid/mime.conf $(1)/etc/squid/
	$(INSTALL_CONF) ./files/squid.conf $(1)/etc/squid/

	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/squid.init $(1)/etc/init.d/squid

	$(INSTALL_DIR) $(1)/usr/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/usr/share/squid/icons/* $(1)/usr/share/squid/icons/

	$(INSTALL_DIR) $(1)/usr/share/squid/errors/templates/
	$(CP) $(PKG_INSTALL_DIR)/usr/share/squid/errors/templates/* $(1)/usr/share/squid/errors/templates/
endef

$(eval $(call BuildPackage,squid3,+librt,+libstdcpp,+libltdl,+libnettle,+libnetfilter-conntrack))
