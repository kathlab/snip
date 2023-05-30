# Stable Diffusion Prompt Guide (AUTOMATIC1111)

Prompts using () and []
---

When you place the keyword within square brackets (), it amplifies its strength by a factor of 1.1. This can be represented as (keyword:1.1).

Enclose the keyword in square brackets [] to decrease the strength of the keyword by a factor of 0.9. This is the same as (keyword:0.9)

Picture sizes:
---

- Wallpaper: 768x432
- iPhone 13: 354x768 (1080x2340)

## Generate an anime avatar from a given picture while retaining the origin:

Steps: 20, Sampler: Euler a, CFG scale: 7, Seed: -1, Size: 512x512, Model hash: 34dccb9e60, Model: v2-1_512-nonema-pruned, Denoising strength: 0.33

```
Positive:
(anime drawing:0.3) of (a woman), glasses, smiling, (dark grey hair)

Negative:
* old woman
```

## Generate an anime avatar from a given picture while retaining the origin (alternative):

Steps: 20, Sampler: Euler a, CFG scale: 7, Seed: -1, Size: 512x512, Model hash: 34dccb9e60, Model: v2-1_512-nonema-pruned, Denoising strength: 0.75

```
Positive:
portrait of a of a (woman), smiling, glasses, (grey bob hair), anime style

Negative:
old woman
```

## Generate a stylish hacker wallpaper:

Steps: 20, Sampler: Euler a, CFG scale: 7, Seed: -1, Size: 768x432, Model hash: 34dccb9e60, Model: v2-1_512-nonema-pruned

```
Positive:
hacker working at a computer, terminals, wallpaper

Negative:
(jpeg artifacts), (blurry)
```


## Generate a vertical picture for social media posts:

Steps: 20, Sampler: Euler a, CFG scale: 7, Seed: -1, Face restoration: CodeFormer, Size: 354x768, Model hash: 34dccb9e60, Model: v2-1_512-nonema-pruned, Version: v1.2.1

```
Positive:
a female hacker working at a computer, dark setup

Negative:
multiple persons, deformed hands
```