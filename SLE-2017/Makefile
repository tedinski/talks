
SVGFILES := $(wildcard *.svg)

all: slides.pdf

clean:
	rm slides.pdf slides.tex *.aux *.log *.nav *.out *.snm *.toc *.vrb $(SVGFILES:%.svg=%.pdf) 2> /dev/null || true

edit:
	gedit slides.md template.tex Makefile README.md &

open: slides.pdf
	xdg-open slides.pdf


slides.pdf: slides.tex $(SVGFILES:%.svg=%.pdf)
	xelatex -file-line-error -halt-on-error slides.tex

slides.tex: slides.md template.tex
	pandoc -s -H template.tex -t beamer --listings -fmarkdown-implicit_figures -o slides.tex slides.md

%.pdf : %.svg
	inkscape -A $*.pdf $*.svg

