# imazen-xl-26

imazen-26 is a corpus for testing image codecs and image processing.

This repository holds all three DNG originals and every public layer of images
with a file exceeding GitHub's 100 MiB limit. DNG 11444 joins the larger
DNGs here so the ordinary originals and JXL sets have identical membership.
These ids are excluded from the ordinary repositories. Image bytes use Git LFS.

Version 2026.10.19: 6 images,
22 files, 1174235176 encoded bytes.

| branch | images | files | bytes |
|---|---:|---:|---:|
| `train` | 5 | 21 | 1049617040 |
| `validate` | 1 | 1 | 124618136 |
| `test` | 0 | 0 | 0 |

`main` contains documentation. Each data branch contains only its own bucket.
Related sources and all renditions share a bucket. Do not train or tune on
`validate` or `test`; keep both out of fitting and default selection.

```sh
git clone --branch train --single-branch --depth 1 https://github.com/imazen/imazen-xl-26.git
```

See [DECODING.md](DECODING.md) for decoder commands, colour/HDR handling and hashes.

`manifest.tsv` identifies each file, its bucket, encoded SHA-256, licence and
source provenance. The image licences differ; the Unsplash files retain the
[Unsplash License](https://unsplash.com/license), while the other files are
public domain or CC0 as specified per row. `provenance_json` preserves attribution
and source URLs. The corpus tooling's software licence does not replace these licences.

Layers: jxl-p3 (5), jxl-srgb (3), originals (6), png-p3 (5), png-srgb (3).

Source and validation: [imazen-26](https://github.com/imazen/imazen-26).
