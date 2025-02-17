#
# Copyright (C) 2011-2021 Entware
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=tailscale
PKG_VERSION:=1.22.2
PKG_RELEASE:=1
PKG_GOGET:=github.com/tailscale/tailscale

GOPKG_SOURCE=GO_$(PKG_NAME)-$(PKG_VERSION)-$(PKG_RELEASE).tar.bz2

#!!! uncomment PKG_SOURCE to download fixed version
PKG_SOURCE:=$(GOPKG_SOURCE)
PKG_HASH:=5ef4d263ef74ef1bf881714953992196c144a5411806dd59a327f893bb448106
PKG_COMMIT:=6f700925cef22d8b2a100840c8d9eb084dadfece

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

ifeq ($(BUILD_VARIANT),nohf)
GOARM=GOARM=5
endif

define Package/tailscale/Default
	$(call Package/gopackage/Default)
	TITLE:=Private WireGuard networks made easy
	URL:=https://tailscale.com/
endef

define Package/tailscale
	$(call Package/tailscale/Default)
	VARIANT:=hf
	DEPENDS:=+ca-bundle
endef

define Package/tailscale_nohf
	$(call Package/tailscale/Default)
	VARIANT:=nohf
	DEPENDS:=@(TARGET_armv7_2_6||TARGET_armv7_3_2) +ca-bundle
endef

define Package/tailscale/description
 Zero config VPN. Installs on any device in minutes, manages firewall rules for you, and works from anywhere
endef

Package/tailscale_nohf/description=$(Package/tailscale/description)

define Build/Compile
        (\
		cd $(PKG_BUILD_DIR); \
		mkdir -p bin; \
		cd src/$(PKG_GOGET); \
		CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=$(GOARCH) $(GOARM) GOPATH=$(PKG_BUILD_DIR) $(GOROOT)/bin/go build -ldflags="-X tailscale.com/version.Long=${PKG_VERSION} -s -w" -x -v tailscale.com/cmd/tailscale; \
		cp tailscale $(PKG_BUILD_DIR)/bin; \
		CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=$(GOARCH) $(GOARM) GOPATH=$(PKG_BUILD_DIR) $(GOROOT)/bin/go build -ldflags="-X tailscale.com/version.Long=${PKG_VERSION} -s -w" -x -v tailscale.com/cmd/tailscaled; \
		cp tailscaled $(PKG_BUILD_DIR)/bin; \
		chmod -R +w $(PKG_BUILD_DIR)/pkg; \
        )
endef


define Package/tailscale/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/tailscale $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/tailscaled $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S06tailscaled $(1)/opt/etc/init.d/
endef

Package/tailscale_nohf/install=$(Package/tailscale/install)

$(eval $(call BuildPackage,tailscale))
$(eval $(call BuildPackage,tailscale_nohf))

