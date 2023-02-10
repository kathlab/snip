# Linux shell

Merge PDFs
---

Add alias to bashrc:

```
alias mergepdf='gs -dNOPAUSE -sDEVICE=pdfwrite -sOUTPUTFILE=~/Documents/`date +%F`_merged.pdf -dBATCH '
```

Start a terminal and type __mergepdf__ and drag-and-drop PDF files in the order you want to have them merged. Then press __enter__.