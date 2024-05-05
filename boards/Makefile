# subdirectories of kr260, kv260 and kd240 under source directory - one per app
APPS := $(wildcard kr260/*/ kv260/*/ kd240/*/)

# *.bit, *.dtsi, *.xclbin and shell.json files under these specific directories
BITS := $(wildcard $(patsubst %,%*.bit,$(APPS)))
DTSIS := $(wildcard $(patsubst %,%*.dtsi,$(APPS)))
XCLBINS := $(wildcard $(patsubst %,%*.xclbin,$(APPS)))
JSONS := $(wildcard $(patsubst %,%shell.json,$(APPS)))

# prebuilt *.bin and *.dtbo files
BINS_PRE := $(wildcard $(patsubst %,%*.bin,$(APPS)))
DTBOS_PRE := $(wildcard $(patsubst %,%*.dtbo,$(APPS)))

# *.dtbo files to generate
DTBOS_GEN += $(patsubst %.dtsi,%.dtbo,$(DTSIS))

%.dtbo: %.dtsi
	dtc -I dts -O dtb -o $@ $<

# *.bif template
BIF_TEMPLATE := template.bif

# *.bif and *.bins files to generate
BIFS := $(patsubst %.bit,%.bif,$(BITS))
BINS_GEN := $(patsubst %.bit,%.bin,$(BITS))

# combination of all bins and dtbos (generated or prebuilt)
# use sort function to remove duplicates
BINS := $(sort $(BINS_GEN) $(BINS_PRE))
DTBOS := $(sort $(DTBOS_GEN) $(DTBOS_PRE))

%.bif: %.bit
	sed 's#@BIT@#$<#' <$(BIF_TEMPLATE) >$@

%.bin: %.bif
	bootgen -image $< -arch zynqmp -o $@ -w

default: all

all: bins dtbos
bins: $(BINS)
dtbos: $(DTBOS)

clean:
	rm -f $(BIFS)
	rm -f $(BINS_GEN)
	rm -f $(DTBOS_GEN)

INSTALLDIR := $(DESTDIR)/lib/firmware/xilinx

install: $(BINS) $(DTBOS) $(XCLBINS) $(JSONS)
	for f in $^; do \
	    file=$$(basename $$f); \
	    app=$$(basename $$(dirname $$f)); \
	    board=$$(basename $$(dirname $$(dirname $$f))); \
	    install -D -m 644 \
	        $$f $(INSTALLDIR)/$$board-$$app/$$file; \
	done


.PHONY: default all bins dtbos clean install

