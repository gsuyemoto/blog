---
title: Known File Headers (HEX)
slug: known-file-headers
date: 2023-06-04
---

  [extensive list]: https://en.wikipedia.org/wiki/List_of_file_signatures

### Known File Headers in Hexadecimal

For some common files...

These are handy to know when doing some reverse engineering, especially when perusing through a blob for a file system.

| File Type  | Hexadecimal Code                                                                                         |
| ---------- | -------------------------------------------------------------------------------------------------------- |
| JPEG       | FF D8 FF E0                                                                                              |
| PNG        | 89 50 4E 47 0D 0A 1A 0A                                                                                  |
| SQLITE     | 53 51 4C 69 74 65 20 66 6F 72 6D 61 74 20 33 00                                                          |
| OPENSSH    | 2D 2D 2D 2D 2D 42 45 47 49 4E 20 4F 50 45 4E 53 53 48 20 50 52 49 56 41 54 45 20 4B 45 59 2D 2D 2D 2D 2D |
| GZIP       | 1F 8B                                                                                                    | 

<!-- more -->

An [extensive list]{target=_blank} can be found on Wikipedia.
