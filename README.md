# Universal CSS Color Classes (UCCC)

## Overview

This repository hosts four individual CSS files containing every possible 24-bit RGB color, expressed as a utility class. The naming convention is a **hybrid** of the original c-class and the non c-class, designed to avoid selector issues while minimizing length:

- If the hex code starts with a letter (`a`–`f`), the class is `.RRGGBB{color:#RRGGBB}` (no prefix).  
- If it starts with a digit (`0`–`9`), the class is `.cRRGGBB{color:#RRGGBB}` — the leading `c` avoids the invalid unescaped digit-leading class selector.  

Example entries:

```css
.abcdef{color:#abcdef}      /* starts with letter, no prefix */
.c1a2b3{color:#1a2b3}       /* starts with digit, prefixed with c */
```

There are **16,777,216** total lines — one for every combination of red, green, and blue in hex — for a total file size of **379,584,512 bytes (378.00 MiB)** in this hybrid form.

## Generation Method

The file was generated programmatically with a small script that iterates all values of red, green, and blue (0–255) and emits a line per color using the hybrid naming rule:

```python
with open("every_color_conditional.css", "w", newline="\n") as f:
    for r in range(256):
        for g in range(256):
            for b in range(256):
                hexcode = f"{r:02x}{g:02x}{b:02x}"
                prefix = "" if hexcode[0] in "abcdef" else "c"
                f.write(f".{prefix}{hexcode}{{color:#{hexcode}}}\n")
```

Total generation time in this instance: ~6 seconds on modern hardware after the prompt was issued.

## File Format and Size

- **Class naming scheme:**  
  - `.RRGGBB{color:#RRGGBB}` when the hex starts with a letter (`a`–`f`).  
  - `.cRRGGBB{color:#RRGGBB}` when the hex starts with a digit (`0`–`9`) to avoid needing escaping.  
- **Counts:**  
  - No leading `c` (starts with a–f): **6,291,456** lines  
  - With leading `c` (starts with 0–9): **10,485,760** lines  
  - **Total:** 16,777,216 lines  
- **Per-line size:**  
  - Lines without `c`: 22 bytes content + 1 byte newline = **23 bytes**  
  - Lines with `c`: 23 bytes content + 1 byte newline = **24 bytes**  
- **Total (LF endings):** 396,361,728 bytes ≈ **378 MiB**  
- **Encoding:** ASCII / UTF-8 safe (no multibyte characters)  

This hybrid approach trades a single extra character on the subset that would otherwise require escaping for full validity while keeping the majority of class names as short as possible.

## Usage

This file is intentionally naive; it can be consumed directly by browsers or build tooling.

Because the classes are predictable given the rules, they can be referenced easily and applied to all CSS elements where color applies.

## Performance & Hosting Notes

- **Not optimized for delivery** as-is: ~364 MiB total is large for four single CSS assets 
- Consider **on-the-fly extraction**, subsetting, or serving only the needed subset if used in production.  
- Serving these files over a CDN with aggressive compression and caching mitigates delivery cost/pain.

## Compression Recommendations

This file compresses extremely well. Suggested pipelines:

- **Gzip:**  
  ```sh
  gzip -9 every_color_conditional.css
  ```
  Expect a dramatic size reduction (often to single-digit MiB depending on server settings).  

- **Brotli:**  
  ```sh
  brotli --quality=11 every_color_conditional.css
  ```
  Even better compression for modern browsers.

Configure your web server to serve the compressed variant with appropriate `Content-Encoding` headers and fall back gracefully if needed.

## Reproducibility

If you want to recreate or adapt this:

1. Use the Python snippet above with the conditional prefix logic.  
2. Modify the template if you want different CSS properties (e.g., `background-color` instead of `color`).  
3. Chunk generation (e.g., by splitting on `r` or `g`) for parallelism or incremental builds.

## Intent & Philosophy

This is a **display** more than a tool. It’s a snapshot of an era where the friction of generating combinatorial boilerplate has been effectively erased by tooling and AI prompting. The hybrid naming encodes a small pragmatic insight — balancing strict CSS validity with minimal verbosity — while the whole artifact stands to remind, provoke, and inspire further reflection about where human effort is being reallocated in the march of progress.

## Acknowledgments

Generated with the assistance of modern AI tooling and a minimal script. The synthesis of prompt + program reflects current human+machine collaboration patterns: we supply intent; processors supply scale.

## License

```text
MIT License

Copyright (c) 2025 Arkwell Agency Ltd.

Permission is hereby granted, free of charge, to any person obtaining a copy
...
```
