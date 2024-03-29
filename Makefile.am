###############################################################################
## Makefile for udm-rus
###############################################################################

LANG1=udm
LANG2=rus
PREFIX1=$(LANG1)-$(LANG2)
PREFIX2=$(LANG2)-$(LANG1)

BASENAME=apertium-$(PREFIX1)

TARGETS_COMMON = $(PREFIX1).autobil.bin $(PREFIX2).autobil.bin \
	$(PREFIX1).automorf.hfst $(PREFIX1).autogen.bin $(PREFIX1).t1x.bin  \
	$(PREFIX2).automorf.bin $(PREFIX2).autogen.hfst $(PREFIX2).t1x.bin  \
        $(PREFIX1).t2x.bin $(PREFIX1).t3x.bin \
        $(PREFIX2).t2x.bin $(PREFIX2).t3x.bin \
        $(PREFIX1).rlx.bin $(PREFIX2).rlx.bin \
        $(PREFIX1).lrx.bin $(PREFIX2).lrx.bin

@ap_include@

###############################################################################
## udm transducer
###############################################################################

.deps/$(LANG1).twol.hfst: $(BASENAME).$(LANG1).twol
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	hfst-twolc $< -o $@
 
.deps/$(LANG1).RL.lexc: $(BASENAME).$(LANG1).lexc
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	cat $< | grep -v 'Dir/LR' > $@
 
.deps/$(LANG1).LR.lexc: $(BASENAME).$(LANG1).lexc
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	cat $< | grep -v 'Dir/RL' > $@

.deps/$(LANG1).RL.lexc.hfst: .deps/$(LANG1).RL.lexc
	hfst-lexc --format foma $< -o $@

.deps/$(LANG1).LR.lexc.hfst: .deps/$(LANG1).LR.lexc
	hfst-lexc --format foma $< -o $@

.deps/$(LANG1).RL.hfst: .deps/$(LANG1).RL.lexc.hfst .deps/$(LANG1).twol.hfst
	hfst-compose-intersect -1 .deps/$(LANG1).RL.lexc.hfst -2 .deps/$(LANG1).twol.hfst -o $@

.deps/$(LANG1).LR.hfst: .deps/$(LANG1).LR.lexc.hfst .deps/$(LANG1).twol.hfst
	hfst-compose-intersect -1 .deps/$(LANG1).LR.lexc.hfst -2 .deps/$(LANG1).twol.hfst -o $@

$(PREFIX2).autogen.hfst: .deps/$(LANG1).RL.hfst
	hfst-fst2fst -O $< -o $@

$(PREFIX1).automorf.hfst: .deps/$(LANG1).LR.hfst
	hfst-invert $< | hfst-fst2fst -O -o $@

###############################################################################
## rus transducer
###############################################################################

$(PREFIX1).autogen.bin: $(BASENAME).$(LANG2).dix
	apertium-validate-dictionary $<
	lt-comp rl $< $@

$(PREFIX2).automorf.bin: $(BASENAME).$(LANG2).dix
	apertium-validate-dictionary $< 
	lt-comp lr $< $@

###############################################################################
## Debugging transducers (for testvoc)
###############################################################################

.deps/$(LANG1).LR-debug.hfst: $(BASENAME).$(LANG1).lexc
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	cat $< | grep -v 'Dir/RL' | grep -v 'Use/Circ' > .deps/$(LANG1).LR-debug.lexc
	hfst-lexc --format foma .deps/$(LANG1).LR-debug.lexc -o .deps/$(LANG1).LR-debug.lexc.hfst
	hfst-compose-intersect -1 .deps/$(LANG1).LR-debug.lexc.hfst -2 .deps/$(LANG1).twol.hfst -o $@

.deps/$(LANG2).LR-debug.hfst: $(BASENAME).$(LANG2).lexc
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	cat $< | grep -v 'Dir/RL' | grep -v 'Use/Circ' > .deps/$(LANG2).LR-debug.lexc
	hfst-lexc --format foma .deps/$(LANG2).LR-debug.lexc -o .deps/$(LANG2).LR-debug.lexc.hfst
	hfst-compose-intersect -1 .deps/$(LANG2).LR-debug.lexc.hfst -2 .deps/$(LANG2).twol.hfst -o $@

###############################################################################
## Bilingual dictionaries
###############################################################################

$(PREFIX1).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

$(PREFIX2).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $< 
	lt-comp rl $< $@

###############################################################################
## Disambiguation rules 
###############################################################################

$(PREFIX1).rlx.bin: $(BASENAME).$(PREFIX1).rlx
	cg-comp $< $@

$(PREFIX2).rlx.bin: $(BASENAME).$(PREFIX2).rlx
	cg-comp $< $@


###############################################################################
## Lexical selection rules
###############################################################################

$(PREFIX1).lrx.bin: $(BASENAME).$(PREFIX1).lrx
	lrx-comp lr $< $@

$(PREFIX2).lrx.bin: $(BASENAME).$(PREFIX2).lrx
	lrx-comp lr $< $@

###############################################################################
## Structural transfer rules
###############################################################################

$(PREFIX1).t1x.bin: $(BASENAME).$(PREFIX1).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX1).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t1x $@

$(PREFIX1).t2x.bin: $(BASENAME).$(PREFIX1).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX1).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t2x $@

$(PREFIX1).t3x.bin: $(BASENAME).$(PREFIX1).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX1).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t3x $@

$(PREFIX2).t1x.bin: $(BASENAME).$(PREFIX2).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX2).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t1x $@

$(PREFIX2).t2x.bin: $(BASENAME).$(PREFIX2).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX2).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t2x $@

$(PREFIX2).t3x.bin: $(BASENAME).$(PREFIX2).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX2).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t3x $@


###############################################################################
## Postgeneration dictionaries
###############################################################################

#$(PREFIX1).autopgen.bin: $(BASENAME).post-$(LANG1).dix
#	apertium-validate-dictionary $<
#	lt-comp lr $< $@
#
#$(PREFIX2).autopgen.bin: $(BASENAME).post-$(LANG2).dix
#	apertium-validate-dictionary $< 
#	lt-comp lr $< $@

###############################################################################
## Installation stuff
###############################################################################

debug: .deps/$(LANG1).LR-debug.hfst .deps/$(LANG2).LR-debug.hfst

EXTRA_DIST=$(PREFIX1).prob $(PREFIX2).prob \
           $(BASENAME).$(PREFIX1).t1x $(BASENAME).$(PREFIX1).t2x $(BASENAME).$(PREFIX1).t3x \
           $(BASENAME).$(PREFIX2).t1x $(BASENAME).$(PREFIX2).t2x $(BASENAME).$(PREFIX2).t3x \
           modes.xml

apertium_udm_rusdir=$(prefix)/share/apertium/$(BASENAME)/

apertium_udm_rus_DATA=$(PREFIX1).autobil.bin $(PREFIX2).autobil.bin \
                    $(PREFIX1).prob $(PREFIX2).prob \
		    $(PREFIX1).autogen.bin $(PREFIX2).autogen.hfst \
		    $(PREFIX1).automorf.hfst $(PREFIX2).automorf.bin \
		    $(PREFIX1).t1x.bin $(PREFIX2).t1x.bin \
		    $(PREFIX1).t2x.bin $(PREFIX2).t2x.bin \
		    $(PREFIX1).t3x.bin $(PREFIX2).t3x.bin \
		    $(PREFIX1).rlx.bin $(PREFIX2).rlx.bin \
		    $(PREFIX1).lrx.bin $(PREFIX2).lrx.bin \
		    $(BASENAME).$(PREFIX1).t1x $(BASENAME).$(PREFIX2).t1x \
		    modes.xml

#		    $(PREFIX1).autopgen.bin $(PREFIX2).autopgen.bin 

noinst_DATA=modes/$(PREFIX1).mode modes/$(PREFIX2).mode


install-data-local: install-modes
	$(INSTALL_DATA) $(BASENAME).$(PREFIX1).t1x $(DESTDIR)$(apertium_udm_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX2).t1x $(DESTDIR)$(apertium_udm_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX1).t2x $(DESTDIR)$(apertium_udm_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX2).t2x $(DESTDIR)$(apertium_udm_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX1).t3x $(DESTDIR)$(apertium_udm_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX2).t3x $(DESTDIR)$(apertium_udm_rusdir)

CLEANFILES = -r $(TARGETS_COMMON) .deps/

test: all
	apertium-regtest test
