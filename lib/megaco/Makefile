# 
# %CopyrightBegin%
# 
# Copyright Ericsson AB 1999-2022. All Rights Reserved.
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# 
# %CopyrightEnd%

#
# --------------------------------------------------------------------
# If downloading this app separately (not as part of 
# an OTP source release), then there is two ways to
# build and install this app.
# 1) Replace the megaco source dir in an existing
#    OTP source tree. Then re-configure, build and 
#    install OTP.
#    Note that the directory name has to be renamed
#    from megaco-<version> to megaco.
# 2) Unpack outside an OTP-source tree. 
#    a) Configure app (make ERL_TOP=</path/to/top/OTP-source/dir> conf)
#    b) make ERL_TOP=</path/to/top/OTP-source/dir>
#    c) make ERL_TOP=</path/to/top/OTP-source/dir> \
#            OTP_INSTALL_DIR=</path/to/top/OTP-install/dir> app_install
#       (e.g. if otp is installed in /usr/local/otp-r9b,
#        then OTP_INSTALL_DIR is /usr/local/otp-r9b and
#        the app will be installed in dir 
#        /usr/local/otp-r9b/lib/erlang/lib/megaco-<version>)
# -----------------------------------------------------------------------
#

include $(ERL_TOP)/make/target.mk
include $(ERL_TOP)/make/$(TARGET)/otp.mk

# ----------------------------------------------------
# Application version
# ----------------------------------------------------
include vsn.mk
VSN=$(MEGACO_VSN)

DIR_NAME = megaco_src-$(VSN)$(PRE_VSN)

nullstring :=
space := $(nullstring) # a space at the end

ifndef APP_RELEASE_DIR
  ifndef TESTROOT
    APP_RELEASE_DIR = /tmp
  else
    APP_RELEASE_DIR = $(subst $(space),\ ,$(TESTROOT))
  endif
endif

ifndef APP_TAR_FILE
  APP_TAR_FILE = $(APP_RELEASE_DIR)/$(DIR_NAME).tgz
endif

APP_DIR = $(APP_RELEASE_DIR)/$(DIR_NAME)

ifdef OTP_INSTALL_DIR
  APP_INSTALL_DIR = $(OTP_INSTALL_DIR)/lib/erlang
else
  # If installing into an OTP structure created
  # by installing an source OTP build, the '/tmp'
  # shall be replaced with the value of ERL_TOP
  APP_INSTALL_DIR = /tmp/lib/erlang
endif

# ----------------------------------------------------
# Common Macros
# ----------------------------------------------------

include subdirs.mk

SUB_DIRECTORIES = $(SUB_DIRS) doc/src

SPECIAL_TARGETS = 

ifeq ($(FLEX_SCANNER_LINENO),disable)
  FLEX_SCANNER_LINENO_ENABLER = --disable-megaco-flex-scanner-lineno
else
  FLEX_SCANNER_LINENO_ENABLER = --enable-megaco-flex-scanner-lineno
endif

ifeq ($(FLEX_SCANNER_REENTRANT),disable)
  FLEX_SCANNER_REENTRANT_ENABLER = --disable-megaco-reentrant-flex-scanner
else
  FLEX_SCANNER_REENTRANT_ENABLER = --enable-megaco-reentrant-flex-scanner
endif

ifeq ($(ERL_DETERMINISTIC),yes)
  ERL_DETERMINISTIC_ENABLER = --enable-deterministic-build
else
  ERL_DETERMINISTIC_ENABLER = --disable-deterministic-build
endif

CONFIGURE_OPTS = $(FLEX_SCANNER_LINENO_ENABLER) $(FLEX_SCANNER_REENTRANT_ENABLER) $(ERL_DETERMINISTIC_ENABLER)


DIA_PLT      = ./priv/plt/$(APPLICATION).plt
DIA_ANALYSIS = $(basename $(DIA_PLT)).dialyzer_analysis


# ----------------------------------------------------
# Default Subdir Targets
# ----------------------------------------------------
include $(ERL_TOP)/make/otp_subdir.mk

.PHONY: reconf conf dconf econf configure setup info_megaco version \
	app_install dialyzer

reconf:
	(cd $(ERL_TOP) && \
		./otp_build configure $(ERL_DETERMINISTIC_ENABLER) && \
		cd $(ERL_TOP)/../libraries/megaco)

conf: do_configure

dconf: 
	$(MAKE) conf FLEX_SCANNER_REENTRANT=disable

econf: 
	$(MAKE) conf FLEX_SCANNER_REENTRANT=enable

do_configure: configure 
	./configure $(CONFIGURE_OPTS)

configure: configure.in
	autoconf

setup:
	(cd src && $(MAKE) $@)

info_megaco:
	@echo "APP_RELEASE_DIR: $(APP_RELEASE_DIR)"
	@echo "APP_DIR:         $(APP_DIR)"
	@echo "APP_TAR_FILE:    $(APP_TAR_FILE)"
	@echo "OTP_INSTALL_DIR: $(OTP_INSTALL_DIR)"
	@echo "APP_INSTALL_DIR: $(APP_INSTALL_DIR)"
	@echo ""

info: info_megaco

version:
	@echo "$(VSN)"


# ----------------------------------------------------
# Application install (of a app built from source) targets
# ----------------------------------------------------
app_install:
	$(MAKE) TESTROOT="$(APP_INSTALL_DIR)" release 


# ----------------------------------------------------
# Application (source) release targets
# ----------------------------------------------------
app_release: app_doc tar

app_doc:
	cd doc/src; $(MAKE) html man

app_dir: $(APP_DIR)

tar_exclude: TAR.exclude2

TAR.exclude2: Makefile TAR.exclude
	cat TAR.exclude > TAR.exclude2; \
        echo "megaco/TAR.exclude2" >> TAR.exclude2; \
        echo "megaco/priv/lib/$(TARGET)" >> TAR.exclude2; \
        echo "megaco/src/flex/$(TARGET)" >> TAR.exclude2; \
	(cd ..; find megaco -name 'prebuild.skip' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name 'findmerge.*' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '*.contrib*' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '*.keep*' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '*.mkelem*' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '*~' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name 'erl_crash.dump' >> megaco/TAR.exclude2)
	(cd ..; find megaco/test -name '*.beam' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '*.log' >> megaco/TAR.exclude2)
	(cd ..; find megaco/examples/meas -name '*.xls' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name 'core' >> megaco/TAR.exclude2)
	(cd ..; find megaco -name '.cmake.state' >> megaco/TAR.exclude2)

$(APP_DIR): tar_exclude
	mkdir -p "$(subst $(space),\ ,$@)"; \
        (cd ..; tar cfX - megaco/TAR.exclude2 megaco) | \
        (cd "$(subst $(space),\ ,$@)"; tar xf -); \
        mv "$(subst $(space),\ ,$@)"/megaco/* "$(subst $(space),\ ,$@)"/; \
	mkdir $(subst $(space),\ ,$@)/autoconf; \
        cp autoconf/config.guess "$(subst $(space),\ ,$@)"/autoconf/; \
        cp autoconf/config.sub "$(subst $(space),\ ,$@)"/autoconf/; \
        cp autoconf/install-sh "$(subst $(space),\ ,$@)"/autoconf/; \
        rmdir "$(subst $(space),\ ,$@)"/megaco

tar: $(APP_TAR_FILE)

$(APP_TAR_FILE): $(APP_DIR)
	(cd "$(APP_RELEASE_DIR)"; gtar zcf "$(subst $(space),\ ,$@)" $(DIR_NAME))

DIA_PLT_APPS=asn1 runtime_tools et debugger
TEST_NEEDS_RELEASE=true

include $(ERL_TOP)/make/app_targets.mk
