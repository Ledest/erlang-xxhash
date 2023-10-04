# Based on c_src.mk from erlang.mk by Loic Hoguin <essen@ninenines.eu>

CURDIR := $(shell pwd)
BASEDIR := $(abspath $(CURDIR)/..)

PROJECT = xxhash

ERTS_INCLUDE_DIR ?= $(shell erl -noshell -eval "io:format(\"~s/erts-~s/include/\", [code:root_dir(), erlang:system_info(version)])." -s init stop)
ERL_INTERFACE_INCLUDE_DIR ?= $(shell erl -noshell -eval "io:format(\"~s\", [code:lib_dir(erl_interface, include)])." -s init stop)
ERL_INTERFACE_LIB_DIR ?= $(shell erl -noshell -eval "io:format(\"~s\", [code:lib_dir(erl_interface, lib)])." -s init stop)

C_SRC_DIR = $(CURDIR)
C_SRC_OUTPUT ?= $(CURDIR)/../priv/$(PROJECT).so

# System type and C compiler/flags.

UNAME_SYS := $(shell uname -s)
ifeq ($(UNAME_SYS), Darwin)
	CC ?= cc
	CFLAGS ?= -O2 -std=c99 -Wall -W -Wundef -Wmissing-prototypes -Wno-implicit-function-declaration
	LDFLAGS ?= -flat_namespace -undefined suppress
else ifeq ($(UNAME_SYS), FreeBSD)
	CC ?= cc
	CFLAGS ?= -O2 -std=c99 -Wall -Wmissing-prototypes -Wno-implicit-function-declaration
else ifeq ($(UNAME_SYS), Linux)
	CC ?= gcc
	CFLAGS ?= -O2 -std=c99 -Wall -Wmissing-prototypes -Wno-implicit-function-declaration
endif

CFLAGS += -fPIC -I $(ERTS_INCLUDE_DIR) -I $(ERL_INTERFACE_INCLUDE_DIR) $(shell pkg-config --cflags libxxhash)

LDLIBS += -L $(ERL_INTERFACE_LIB_DIR) -lei $(shell pkg-config --libs libxxhash)
LDFLAGS += -shared

# Verbosity.

c_verbose_0 = @echo " C     " $(?F);
c_verbose = $(c_verbose_$(V))

link_verbose_0 = @echo " LD    " $(@F);
link_verbose = $(link_verbose_$(V))

SOURCES := $(shell find $(C_SRC_DIR) -type f -name "*.c")
OBJECTS = $(addsuffix .o, $(basename $(SOURCES)))

COMPILE_C = $(c_verbose) $(CC) $(CFLAGS) $(CPPFLAGS) -c

$(C_SRC_OUTPUT): $(OBJECTS)
	@mkdir -p $(BASEDIR)/priv/
	$(link_verbose) $(CC) $(OBJECTS) $(LDFLAGS) $(LDLIBS) -o $(C_SRC_OUTPUT)

%.o: %.c
	$(COMPILE_C) $(OUTPUT_OPTION) $<

clean:
	@rm -f $(C_SRC_OUTPUT) $(OBJECTS)
