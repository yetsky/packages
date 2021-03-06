# 
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=postfix
PKG_RELEASE:=1
PKG_SOURCE_URL:=ftp://ftp.porcupine.org/mirrors/postfix-release/official/
PKG_VERSION:=2.11.3
PKG_MD5SUM:=c3f0f51d8865559b40e9350eb3816011
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_MAINTAINER:=Denis Shulyaka <Shulyaka@gmail.com>
PKG_LICENSE:=IPL-1.0
PKG_LICENSE_FILE:=LICENSE
PKG_BUILD_DEPENDS:=+tinycdb

include $(INCLUDE_DIR)/package.mk

define Package/postfix
  SECTION:=mail
  CATEGORY:=Mail
  TITLE:=Postfix Mail Transmit Agent
  URL:=http://www.postfix.org/
  DEPENDS:=+POSTFIX_TLS:libopenssl +POSTFIX_SASL:libsasl2 +POSTFIX_LDAP:libopenldap +libpcre
endef

define Package/postfix/description
 Postfix is Wietse Venema's mailer that started life as an alternative to the widely-used Sendmail program. Postfix attempts to be fast, easy to administer, and secure, while at the same time being sendmail compatible enough to not upset existing users. Thus, the outside has a sendmail-ish flavor, but the inside is completely different.
endef

define Package/postfix/config
	menu "Select postfix build options"
		config POSTFIX_TLS
			bool "TLS support"
			default y
			help
			  Implements TLS support in postfix (using OpenSSL).
		config POSTFIX_SASL
			bool "SASL support"
			default y
			help
			  Implements SASL support in postfix (using Cyrus SASL).
		config POSTFIX_LDAP
			bool "LDAP support"
			default y
			help
			  Implements LDAP support in postfix (using OpenLDAP).
	endmenu
endef

define Package/postfix/conffiles
/etc/postfix/main.cf
/etc/postfix/master.cf
/etc/postfix/aliases
endef

CCARGS=-DHAS_CDB -DNO_DB -DNO_EPOLL -DNO_SIGSETJMP -DNO_NIS -DDEF_DB_TYPE=\"cdb\"
AUXLIBS=-L$(STAGING_DIR)/usr/lib -lcdb

ifdef CONFIG_POSTFIX_TLS
  CCARGS+=-DUSE_TLS
  AUXLIBS+=-lssl -lcrypto
endif

ifdef CONFIG_POSTFIX_SASL
  CCARGS+=-DUSE_SASL_AUTH -DUSE_CYRUS_SASL -I$(STAGING_DIR)/usr/include/sasl
  AUXLIBS+=-lsasl2
endif

ifdef CONFIG_POSTFIX_LDAP
  CCARGS+=-DHAS_LDAP
  AUXLIBS+=-lldap -llber
endif

config_directory=/etc/postfix
sample_directory=/etc/postfix
command_directory=/usr/sbin
daemon_directory=/usr/libexec/postfix
data_directory=/usr/var/lib/postfix
queue_directory=/usr/var/spool/postfix
mail_spool_directory=/usr/var/mail
html_directory=no
manpage_directory=no
readme_directory=no
sendmail_path=/usr/sbin/sendmail
newaliases_path=/usr/bin/newaliases
mailq_path=/usr/bin/mailq

ln_suffix=.postfix
ln_old_suffix=.old

define Build/Configure
	cd $(PKG_BUILD_DIR); $(MAKE) makefiles CCARGS='$(CCARGS)' $(TARGET_CONFIGURE_OPTS) AUXLIBS="$(AUXLIBS)"
endef

define Build/Compile
	# Currently postfix has a bug with Makefiles that CCARGS are not passed to the compiler, so we are copying them to CC
	cd $(PKG_BUILD_DIR); $(MAKE) $(TARGET_CONFIGURE_OPTS) CC='$(TARGET_CC) $(CCARGS)'
	cp ./files/main.cf.default $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "sendmail_path = $(sendmail_path)$(ln_suffix)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "newaliases_path = $(newaliases_path)$(ln_suffix)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "mailq_path = $(mailq_path)$(ln_suffix)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "html_directory = $(html_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "manpage_directory = $(manpage_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "sample_directory = $(sample_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "readme_directory = $(readme_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "command_directory = $(command_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "daemon_directory = $(daemon_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "data_directory = $(data_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "queue_directory = $(queue_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "config_directory = $(config_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
	echo "mail_spool_directory = $(mail_spool_directory)" >> $(PKG_BUILD_DIR)/conf/main.cf.default
endef

define Package/postfix/install
	cd $(PKG_BUILD_DIR); $(MAKE) install_root=$(1) command_directory=$(command_directory) daemon_directory=$(daemon_directory) data_directory=$(data_directory) html_directory=$(html_directory) mail_owner=postfix mailq_path=$(mailq_path)$(ln_suffix) manpage_directory=$(manpage_directory) newaliases_path=$(newaliases_path)$(ln_suffix) queue_directory=$(queue_directory) readme_directory=$(readme_directory) sendmail_path=$(sendmail_path)$(ln_suffix) setgid_group=postdrop sample_directory=$(sample_directory) config_directory=$(config_directory) non-interactive-package
	$(INSTALL_DIR) $(1)$(mail_spool_directory)
	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/postfix.init $(1)/etc/init.d/postfix
endef

define Package/postfix/postinst
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then

 if [ -f "$(sendmail_path)" -a "$$(readlink "$(sendmail_path)")" != "$(sendmail_path)$(ln_suffix)" ]; then
  mv "$(sendmail_path)" "$(sendmail_path)$(ln_old_suffix)"
  echo "Warning: $(sendmail_path) saved as $(sendmail_path)$(ln_old_suffix)"
 fi
 if [ ! -f "$(sendmail_path)" ]; then
  ln -s "$(sendmail_path)$(ln_suffix)" "$(sendmail_path)"
 fi

 if [ -f "$(newaliases_path)" -a "$$(readlink "$(newaliases_path)")" != "$(newaliases_path)$(ln_suffix)" ]; then
  mv "$(newaliases_path)" "$(newaliases_path)$(ln_old_suffix)"
  echo "Warning: $(newaliases_path) saved as $(newaliases_path)$(ln_old_suffix)"
 fi
 if [ ! -f "$(newaliases_path)" ]; then
  ln -s "$(newaliases_path)$(ln_suffix)" "$(newaliases_path)"
 fi

 if [ -f "$(mailq_path)" -a "$$(readlink "$(mailq_path)")" != "$(mailq_path)$(ln_suffix)" ]; then
  mv "$(mailq_path)" "$(mailq_path)$(ln_old_suffix)"
  echo "Warning: $(mailq_path) saved as $(mailq_path)$(ln_old_suffix)"
 fi
 if [ ! -f "$(mailq_path)" ]; then
  ln -s "$(mailq_path)$(ln_suffix)" "$(mailq_path)"
 fi

 echo "myhostname = $$(uci get system.@system[0].hostname)" >> $(config_directory)/main.cf.default
 echo "mydomain = $$(uci get system.@system[0].hostname|sed -e "s/[^\.]*\.\(.*\)/\1/")" >> $(config_directory)/main.cf.default
 for net in $$(uci show network|grep ipaddr|sed -e "s/network\.\([^\.]*\).*/\1/"); do eval "$$(ipcalc.sh $$(uci get network.$$net.ipaddr) $$(uci get network.$$net.netmask))"; echo "$$IP/$$PREFIX"; done | xargs echo "mynetworks =" >> $(config_directory)/main.cf.default

 grep -qc "^sendmail_path" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^sendmail_path =" /etc/postfix/main.cf.default)"
 grep -qc "^newaliases_path" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^newaliases_path =" /etc/postfix/main.cf.default)"
 grep -qc "^mailq_path" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^mailq_path =" /etc/postfix/main.cf.default)"
 grep -qc "^html_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^html_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^manpage_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^manpage_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^sample_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^sample_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^readme_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^readme_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^command_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^command_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^daemon_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^daemon_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^data_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^data_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^queue_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^queue_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^config_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^config_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^mail_spool_directory" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^mail_spool_directory =" /etc/postfix/main.cf.default)"
 grep -qc "^mail_owner" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^mail_owner =" /etc/postfix/main.cf.default)"
 grep -qc "^setgid_group" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^setgid_group =" /etc/postfix/main.cf.default)"
 grep -qc "^myhostname" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^myhostname =" /etc/postfix/main.cf.default)"
 grep -qc "^mydomain" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^mydomain =" /etc/postfix/main.cf.default)"
 grep -qc "^mynetworks" /etc/postfix/main.cf >/dev/null || postconf -e "$$(grep "^mynetworks =" /etc/postfix/main.cf.default)"

 EXTRA_COMMANDS=create_users /etc/init.d/postfix create_users

 postfix set-permissions
 postfix post-install upgrade-source
 postfix upgrade-configuration
 newaliases
 if [ `ps | grep "postfix/master" | grep -cv grep` -gt 0 ]
 then
  postfix reload
 fi
 if [ `grep -c aliases /etc/sysupgrade.conf` -eq 0 ]
 then
  echo "$(config_directory)/main.cf" >> /etc/sysupgrade.conf
  echo "$(config_directory)/aliases" >> /etc/sysupgrade.conf
 fi

fi
endef

define Package/postfix/prerm
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then

 if [ `ps | grep "postfix/master" | grep -cv grep` -gt 0 ]
 then
  postfix stop
 fi

 /etc/init.d/postfix disable

fi
endef

define Package/postfix/postrm
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then
 rm -f $(config_directory)/aliases.cdb $(data_directory)/master.lock

 rm -f "$(sendmail_path)" "$(newaliases_path)" "$(mailq_path)"

 if [ -f "$(sendmail_path)$(ln_old_suffix)" ]; then
  mv "$(sendmail_path)$(ln_old_suffix)" "$(sendmail_path)"
  echo "Warning: $(sendmail_path) restored from $(sendmail_path)$(ln_old_suffix)"
 fi
 if [ -f "$(newaliases_path)$(ln_old_suffix)" ]; then
  mv "$(newaliases_path)$(ln_old_suffix)" "$(newaliases_path)"
  echo "Warning: $(newaliases_path) restored from $(newaliases_path)$(ln_old_suffix)"
 fi
 if [ -f "$(mailq_path)$(ln_old_suffix)" ]; then
  mv "$(mailq_path)$(ln_old_suffix)" "$(mailq_path)"
  echo "Warning: $(mailq_path) restored from $(mailq_path)$(ln_old_suffix)"
 fi

fi
endef

$(eval $(call BuildPackage,postfix))
