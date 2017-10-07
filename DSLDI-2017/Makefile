
all: slides.pdf

clean:
	rm slides.pdf slides.tex *.aux *.log *.nav *.out *.snm *.toc *.vrb 2> /dev/null || true

open: slides.pdf
	xdg-open slides.pdf


slides.pdf: slides.tex
	xelatex -file-line-error -halt-on-error slides.tex

slides.tex: slides.md template.tex
	pandoc -s -H template.tex -t beamer -o slides.tex slides.md

