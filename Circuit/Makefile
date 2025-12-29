# Resolve KiCad CLI (stable first, then nightly)
KICAD_CLI := $(shell which kicad-cli 2>/dev/null || which kicad-cli-nightly)

PROJECT_NAME=roTec

# Files
SCH := fakeTecv5.kicad_sch
PCB := fakeTecv5.kicad_pcb
DOCS := documentation
FAB_DIR := fabrication
FAB_ZIP := roTec_fabrication.zip
SVG_DIR := $(DOCS)/svg

# Render options
#BACKGROUND ?= transparent

.PHONY: all
all: sch pcb fab

.PHONY: sch
sch: $(DOCS)/$(PROJECT_NAME)_schematic.pdf $(DOCS)/$(PROJECT_NAME)_bom.csv $(SVG_DIR)/.timestamp

.PHONY: pcb
pcb: $(DOCS)/front.png $(DOCS)/back.png

.PHONY: fab
fab: ${FAB_ZIP}

## PREPARATION ##

$(DOCS):
	mkdir -p $(DOCS)

## SCHEMATIC ##

$(SVG_DIR):
	mkdir -p $(SVG_DIR)

$(DOCS)/$(PROJECT_NAME)_schematic.pdf: $(SCH) | $(DOCS)
	$(KICAD_CLI) sch export pdf -o $@ $<

$(SVG_DIR)/.timestamp: $(SCH) | $(SVG_DIR)
	$(KICAD_CLI) sch export svg -o $(SVG_DIR) $(SCH)
	date -u +"Exported SVGs on %Y-%m-%d" > $@

$(DOCS)/$(PROJECT_NAME)_bom.csv: $(SCH) | $(DOCS)
	$(KICAD_CLI) sch export bom --preset "roTec" -o $@ $<

## PCB RENDERING ##

$(DOCS)/front.png: $(PCB) | $(DOCS)
	$(KICAD_CLI) pcb render --side top --preset "render" --background $(BACKGROUND) -o $@ $<
	convert $@ -trim $@

$(DOCS)/back.png: $(PCB) | $(DOCS)
	$(KICAD_CLI) pcb render --side bottom --preset "render" --background $(BACKGROUND) -o $@ $<
	convert $@ -trim $@

## FABRICATION ##

$(FAB_DIR):
	mkdir -p $(FAB_DIR)

${FAB_ZIP}: $(PCB) | $(FAB_DIR)
	$(KICAD_CLI) pcb export gerbers $(PCB) -o $(FAB_DIR)
	$(KICAD_CLI) pcb export drill $(PCB) -o $(FAB_DIR)
	cd $(FAB_DIR) && zip -r ../$@ .

## UTILITY ##

.PHONY: clean
clean:
	rm -rf $(DOCS)
