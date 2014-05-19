.PHONY: all clean 

SPLC_FLAGS = -a -g
SPLC = $(STREAMS_INSTALL)/bin/sc

SPL_CMD_ARGS ?= 
SPL_MAIN_COMPOSITE = com.ibm.streamsx.demo.logwatch.language::LogWatch

all: distributed

standalone:
	$(SPLC) $(SPLC_FLAGS) -z -T -M $(SPL_MAIN_COMPOSITE) $(SPL_CMD_ARGS)

distributed:
	$(SPLC) $(SPLC_FLAGS) -z -M $(SPL_MAIN_COMPOSITE) $(SPL_CMD_ARGS)

clean: 
	$(SPLC) $(SPLC_FLAGS) -C -M $(SPL_MAIN_COMPOSITE)

