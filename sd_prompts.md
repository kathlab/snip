# Stable Diffusion Prompt Guide (AUTOMATIC1111)

Prompts using () and []
---

When you place the keyword within square brackets (), it amplifies its strength by a factor of 1.1. This can be represented as (keyword:1.1).

Enclose the keyword in square brackets [] to decrease the strength of the keyword by a factor of 0.9. This is the same as (keyword:0.9)

## Generate an anime avatar from a given picture while retaining the origin:

Steps: 20, Sampler: Euler a, CFG scale: 7, Seed: 1405222331, Size: 512x512, Model hash: 34dccb9e60, Model: v2-1_512-nonema-pruned, Denoising strength: 0.33

```
Positive:
(anime drawing:0.3) of (a woman), glasses, smiling, (dark grey hair)

Negative:
old woman
```
