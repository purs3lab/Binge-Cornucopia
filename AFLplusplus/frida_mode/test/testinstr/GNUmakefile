PWD:=$(shell pwd)/
ROOT:=$(shell realpath $(PWD)../../..)/
BUILD_DIR:=$(PWD)build/
TESTINSTR_DATA_DIR:=$(BUILD_DIR)in/
TESTINSTR_DATA_FILE:=$(TESTINSTR_DATA_DIR)in

TESTINSTBIN:=$(BUILD_DIR)testinstr
TESTINSTSRC:=$(PWD)testinstr.c

QEMU_OUT:=$(BUILD_DIR)qemu-out
FRIDA_OUT:=$(BUILD_DIR)frida-out

.PHONY: all clean qemu frida

all: $(TESTINSTBIN)
	make -C $(ROOT)frida_mode/

$(BUILD_DIR):
	mkdir -p $@

$(TESTINSTR_DATA_DIR): | $(BUILD_DIR)
	mkdir -p $@

$(TESTINSTR_DATA_FILE): | $(TESTINSTR_DATA_DIR)
	echo -n "000" > $@

$(TESTINSTBIN): $(TESTINSTSRC) | $(BUILD_DIR)
	$(CC) -o $@ $<

clean:
	rm -rf $(BUILD_DIR)


qemu: $(TESTINSTBIN) $(TESTINSTR_DATA_FILE)
	$(ROOT)afl-fuzz \
		-D \
		-Q \
		-i $(TESTINSTR_DATA_DIR) \
		-o $(QEMU_OUT) \
		-- \
			$(TESTINSTBIN) @@

frida: $(TESTINSTBIN) $(TESTINSTR_DATA_FILE)
	$(ROOT)afl-fuzz \
		-D \
		-O \
		-i $(TESTINSTR_DATA_DIR) \
		-o $(FRIDA_OUT) \
		-- \
			$(TESTINSTBIN) @@