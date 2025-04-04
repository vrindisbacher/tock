# Licensed under the Apache License, Version 2.0 or the MIT License.
# SPDX-License-Identifier: Apache-2.0 OR MIT
# Copyright Tock Contributors 2022.

# Makefile for building the tock kernel for the ESP32-C3 platform

RISC_PREFIX = riscv64-linux-gnu

include ../Makefile.common

# Default target for installing the kernel.
.PHONY: install
install: flash

flash: $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/release/$(PLATFORM).elf
	esptool.py --port /dev/ttyUSB0 --chip esp32c3 elf2image --use_segments --output binary.hex $^ --dont-append-digest
	esptool.py --port /dev/ttyUSB0 --chip esp32c3 write_flash --flash_mode dio --flash_size detect --flash_freq 80m  0x0 binary.hex

flash-app: $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/release/$(PLATFORM).elf
ifeq ($(APP),)
	$(error "Please specify an APP to be flashed")
endif
	$(RISC_PREFIX)-objcopy --set-section-flags .apps=LOAD,ALLOC $^ $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/release/$(PLATFORM)-app.elf
	$(RISC_PREFIX)-objcopy --update-section .apps=$(APP) $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/release/$(PLATFORM)-app.elf
	esptool.py --port /dev/ttyUSB0 --chip esp32c3 elf2image --use_segments --output binary.hex $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/release/$(PLATFORM)-app.elf --dont-append-digest
	esptool.py --port /dev/ttyUSB0 --chip esp32c3 write_flash --flash_mode dio --flash_size detect --flash_freq 80m 0x0 binary.hex

test:
	$(Q)$(CARGO) test $(NO_RUN) --bin $(PLATFORM) --release
