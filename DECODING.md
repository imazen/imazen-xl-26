# Decoding the files

Start from the `train` branch. `validate` and `test` are holdouts: do not use
them for fitting, tuning, threshold selection or selecting codec defaults.
Related sources and every rendition of an image keep the same bucket.
The repository README gives the branches available in this repository.

## Git LFS in the XL repository

The ordinary originals and JXL repositories contain image bytes directly.
`imazen-xl-26` uses Git LFS for its image files. Install Git LFS before cloning,
or fetch the image bytes after an existing clone:

```sh
git lfs install
git lfs pull
```

Its committed `.lfsconfig` selects the public R2-backed LFS endpoint. A small
text file beginning `version https://git-lfs.github.com/spec/v1` is a pointer;
run `git lfs pull` to retrieve the image it names.

## PNG

Use a PNG decoder that preserves the stored bit depth and colour metadata.
The source-colour (`p3`) collection also contains sRGB and Adobe RGB images;
its name does not mean every file is Display P3. Honour the ICC/cICP or sRGB
signalling instead of interpreting every RGB buffer as sRGB.

For example, ImageMagick can read a PNG into a PPM for inspection:

```sh
magick input.png output.ppm
```

Keep the PNG when testing metadata: a PPM does not carry its colour profile,
provenance or HDR signalling. The `.hdr.png` files hold 16-bit PQ-encoded
samples in Display P3, with a 203-nit diffuse white. They need an HDR-aware
display path or an explicit tone map for an SDR preview. Reading 16-bit RGB
through an 8-bit-only image API loses precision.

## JPEG XL

The files were checked with libjxl 0.12.0, jxl-rs and zenjxl. With libjxl's
decoder tools:

```sh
jxlinfo input.jxl
djxl input.jxl output.png --num_threads=1
```

Lossless JXL files have the same samples as their PNG twins. Files whose
manifest form is `jpeg-in-jxl` instead retain JPEG coefficients and JPEG
reconstruction data. Reconstruct those with:

```sh
djxl input.jxl reconstructed.jpg --num_threads=1
```

The reconstructed JPEG must match `original_sha256`. JPEG pixel decoders may
differ in rounding; use the recorded decoder when comparing its pixel hash.
The `jhgm` box carries a separate gain-map image and ISO 21496-1 parameters.
A normal SDR decode does not apply the gain map. Use a decoder that implements
gain-map HDR reconstruction, or the explicit PQ twin where supplied.

## Originals and hashes

Originals retain their encoded format and orientation metadata. Apply the
orientation when comparing them with the rendered layers. HEIC, UltraHDR JPEG
and DNG require their respective decoders; DNGs have no rendered twins here.

`sha256` hashes encoded file bytes. `pixels_sha256` is a renderer-specific
sample-buffer hash, not a hash of another encoded PNG or JPEG. For cross-decoder
tests, the [readback tool documentation](https://github.com/imazen/imazen-26/tree/main/tools/jxl-decode-check)
defines the separate XXH3-64 tables: native channel layout, tightly packed rows,
displayed orientation, and little-endian samples above 8 bits.

See the [validation record](https://github.com/imazen/imazen-26/blob/main/benchmarks/render_v4_validation_2026-10-15.md)
for reader-specific limits and the checks performed on the stored files.
