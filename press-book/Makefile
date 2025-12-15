BOOK:=content/book.md
TEMPLATE:=$(CURDIR)/template.tex
META:=metadata.yaml
OUTDIR:=output
PRINT_PDF:=$(OUTDIR)/42-degrees-at-dawn-architecting-a-life-with-ai-huan-li.pdf
SCREEN_PDF:=$(OUTDIR)/book-screen.pdf
PRINT_TEST:=$(OUTDIR)/cover-test.pdf
RESOURCE_PATH:=.

PANDOC:=pandoc
PDF_ENGINE:=xelatex

.PHONY: all print-book screen-book print-test clean

all: print-book

$(OUTDIR):
	@mkdir -p $(OUTDIR)

print-book: $(OUTDIR)
	$(PANDOC) \
	--from=markdown+yaml_metadata_block-implicit_figures \
	  --to=latex \
	  --top-level-division=part \
	--pdf-engine=$(PDF_ENGINE) \
	--template=$(TEMPLATE) \
	  --metadata-file=$(META) \
	  --resource-path=$(RESOURCE_PATH) \
	  -o $(PRINT_PDF) \
	  $(BOOK)

screen-book: $(OUTDIR)
	$(PANDOC) \
	--from=markdown+yaml_metadata_block-implicit_figures \
	  --to=latex \
	  --top-level-division=part \
	--pdf-engine=$(PDF_ENGINE) \
	--template=$(TEMPLATE) \
	  --metadata-file=$(META) \
	  --resource-path=$(RESOURCE_PATH) \
	  -o $(SCREEN_PDF) \
	  $(BOOK)

print-test: $(OUTDIR)
	echo "" | $(PANDOC) \
	--from=markdown+yaml_metadata_block-implicit_figures \
	  --to=latex \
	  --top-level-division=part \
	--pdf-engine=$(PDF_ENGINE) \
	--template=$(TEMPLATE) \
	  --metadata-file=$(META) \
	  --resource-path=$(RESOURCE_PATH) \
	  -o $(PRINT_TEST) \
	  -

clean:
	rm -rf $(OUTDIR)
