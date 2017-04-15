---
layout: post
title:  "Add TTF fonts for html2pdf library"
date:   2017-04-16 00:55:32 +0800
category: programming
tags: 
  - PHP
  - pdf
  - font
---


### Task
I am working on my client's project, which I need to add a font for the `html2pdf` library to use.

### Background
my `html2pdf` version is 4.4.0 and `TCPDF`'s version is 5.0.002 (`TCPDF` is the depencency of `html2pdf`. This version of `TCPDF` is super old cuz it is legacy code from a Indian team).

I managed to find the **TTF** file for the font, which `html2pdf` cannot comprehand. 

### Difficulty
newer version of `TCPDF` has a function `addTTFFont` (<https://tcpdf.org/docs/fonts/>) which would help but my old version does not have it.

### Solution
So i found this answer from stackoverflow (<http://stackoverflow.com/questions/28138913/how-to-add-ttf-font-to-html2pdf-php-program>)

using this site <http://www.xml-convert.com/en/convert-tff-font-to-afm-pfa-fpdf-tcpdf> i can transform the `.ttf` file into 3 files:

- `.php` file
- `.z` file
- `.arm` file

turned out `TCPDF` does **not** need the `.arm` file, all i need to do is to copy the `.php` and `.z` file into its fonts folder, and then run this

```
$html2pdf->addFont('custom-font', '', 'custom-font');
```

which would add the font into the PDF file and can be used. 

### TODO

The conversion website is not considered as stable, we should create a on premise solution for it.

