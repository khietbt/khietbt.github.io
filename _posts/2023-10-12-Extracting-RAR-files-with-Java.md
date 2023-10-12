---
layout: post
title: "Extracting RAR files with Java"
date: 2023-10-04 11:43:01 +0700
categories: Rar UnRar SevenZipLibJBinding
---

I hate RAR, but some guys love to archive files in this format. JUNRAR works but it has no plan to
support
the modern RAR5.

SevenZipLibJBinding works properly with RAR5, but it introduces some fatal errors
in initialing in alpine.

Finally, I end up with an external tool: 7z,