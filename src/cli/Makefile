# FOSSology Makefile - cli/
# Copyright (C) 2008 Hewlett-Packard Development Company, L.P.
TOP=../..
VARS=$(TOP)/Makefile.conf
DEPS=$(TOP)/Makefile.deps
include $(VARS)

EXE=cp2foss.php fossjobs.php schema-export.php fo_nomos_license_list.php fo_copyright_list.php fo_bucket_list.php
WRAP=cp2foss fossjobs schema-export fo_nomos_license_list fo_copyright_list fo_bucket_list
DOCS=cp2foss.pod fossjobs.pod 
MAN1FILES=$(DOCS:%.pod=%.1)
HTMLFILES=$(DOCS:%.pod=%.html)
TEXTFILES=$(DOCS:%.pod=%.txt)

#########################
# iPATHFILE build rules #
#########################
$(iPATHFILE):
	$(MAKE) -C $(FOWWWDIR)

all: $(iPATHFILE) docs fo_wrapper

# include the preprocessing stuff
include $(TOP)/Makefile.process

docs: $(DOCS) $(MAN1FILES) $(HTMLFILES) $(TEXTFILES)

%.1: %.pod
	pod2man --name=$* --center="FOSSology" --release="Version $(VERSION)" $< > $*.1

%.html: %.pod
	pod2html --infile=$< --outfile=$*.html
	# pod2html leaves temporary files: see debian bug #378328
	rm -f pod*.tmp

# generate the postinstall script
fo_wrapper: fo_wrapper-process_php
	@chmod +x fo_wrapper.php
	@for file in $(WRAP); do \
	  ln -sf fo_wrapper.php $$file; \
	done

%.txt: %.pod
	pod2text $< $*.txt

install: all
	for file in $(EXE); do \
		$(INSTALL_DATA) $$file $(DESTDIR)$(LIBEXECDIR)/$$file; \
	done
	$(INSTALL_PROGRAM) fo_wrapper.php $(DESTDIR)$(LIBEXECDIR)/fo_wrapper

	for file in $(MAN1FILES); do \
		$(INSTALL_DATA) $$file $(DESTDIR)$(MAN1DIR)/$$file; \
	done
	
#$(INSTALL_DATA) libschema.php $(DESTDIR)$(LIBEXECDIR)/libschema.php
# create BINDIR if not exsit
	if [ ! -e $(DESTDIR)$(BINDIR) ] ; then \
		mkdir -p $(DESTDIR)$(BINDIR); \
	fi	
# create wrappers in BINDIR
	for file in $(WRAP); do \
		echo "Making wrapper for $$file"; \
	  ln -sf $(LIBEXECDIR)/fo_wrapper  $(DESTDIR)$(BINDIR)/$$file; \
	done

uninstall:
	for file in $(EXE); do \
		rm -f $(DESTDIR)$(BINDIR)/$${file%.php}; \
	  rm -f $(DESTDIR)$(LIBEXECDIR)/$$file; \
	done
	rm -f $(DESTDIR)$(LIBEXECDIR)/fo_wrapper
	
	rm -rf $(DESTDIR)$(MODDIR)/cli

	for file in $(MAN1FILES); do \
		rm -f $(DESTDIR)$(MAN1DIR)/$$file; \
	done

test: all
	@echo "*** Tests are still run by hand for cli/ ***"

coverage:
	@echo "NOTICE: no coverage available for cli"

clean:
	# pod2html leaves temporary files: see debian bug #378328
	rm -f *.o core $(MAN1FILES) $(HTMLFILES) $(TEXTFILES) pod*.tmp fo_wrapper.php $(WRAP)

include $(DEPS)

.PHONY: all install uninstall clean test
