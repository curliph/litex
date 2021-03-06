include variables.mak

CC = gcc
CFLAGS = -Wall -O3 -ggdb
LDFLAGS = -lpthread -ljson-c -lm -lstdc++ -ldl -levent

SRC_DIR ?= .
INC_DIR ?= .
MOD_DIR = $(SRC_DIR)/modules
export OBJ_DIR = $(abspath obj_dir)

SRCS_SIM_ABSPATH = $(wildcard $(SRC_DIR)/*.c)
SRCS_SIM = $(notdir $(SRCS_SIM_ABSPATH))
SRCS_SIM_CPP = dut_init.cpp $(SRC_DIR)/veril.cpp
OBJS_SIM = $(SRCS_SIM:.c=.o)

all: modules sim

mkdir:
	mkdir -p $(OBJ_DIR)

$(OBJS_SIM): %.o: $(SRC_DIR)/%.c
	$(CC) -c $(CFLAGS) -o $(OBJ_DIR)/$@ $<

.PHONY: sim
sim: mkdir $(OBJS_SIM)
	verilator -Wno-fatal -O3 --cc dut.v --top-module dut --exe \
		$(SRCS_SIM_CPP) $(OBJS_SIM) \
		--top-module dut \
		--threads $(THREADS) \
		-CFLAGS "$(CFLAGS) -I$(SRC_DIR)" \
		-LDFLAGS "$(LDFLAGS)" \
		-trace $(INC_DIR)
	make -j -C $(OBJ_DIR) -f Vdut.mk Vdut

.PHONY: modules
modules: mkdir
	$(MAKE) -C $(MOD_DIR)

.PHONY: clean
clean:
	rm -rf $(OBJ_DIR)
