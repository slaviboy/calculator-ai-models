# Calculator AI models

Handwriting recognition models for the Calculator Android app. Each model reads a handwritten maths expression
(fingers or stylus) and writes it as LaTeX, which the app turns into a calculation. Recognition runs entirely on the
phone. The app downloads the larger models from this repository's releases when a user chooses one, and your
handwriting is never uploaded.

## Models

| Model | Parameters | File | Status | Release |
|---|---:|---:|---|---|
| **Tiny** | 6.5 M | 6.8 MB | Available (also built into the app) | [`tiny-v1`](../../releases/tag/tiny-v1) |
| **Small** | 20 M | about 21 MB | Training | coming |
| **Medium** | 38 M | about 39 MB | Planned | coming |
| **Large** | 60 M | about 60 MB | Planned | coming |

Larger models read more accurately but take longer on the phone. Each release holds the model file (`<name>.bin`) and
its `SHA256SUMS`. The app only uses a file whose size and SHA-256 match the values pinned in that app version. So a
model becomes selectable in the app only after it has been tested with it, whatever this repository serves.

## How well they read

Scores come from the app's own recognition pipeline: tilt levelling, the calculator's symbols, and a beam search when
unsure. "Same result" counts a reading as right when it calculates to the same value as the true expression.

| Model | MathWriting test, calculator-like (222) | MathWriting valid, calculator-like (397) | Composed calculator expressions (2,000) |
|---|---:|---:|---:|
| Tiny | 76.1 % | 76.3 % | 88.1 % |

- **MathWriting sets:** expressions written by people that use only calculator notation. Per MathWriting's own notes,
  its validation and test sets are only partly by writers absent from training.
- **Composed set:** calculator expressions assembled from handwritten symbols of writers held out of training.
- **Misses:** about a third of the misses on the MathWriting sets are labels in notation a calculator does not use
  (thousands separators, unbalanced brackets, fragments).

## The models

All models share one design:
- **Encoder:** a DenseNet over the ink, which the app draws as a grayscale image at a fixed symbol height.
- **Decoder:** a Transformer that writes LaTeX tokens (a 249-token vocabulary).
- **Weights:** stored as int8 with per-tensor scales, in the `IKM1` format.
- **Engine:** the app reads the files with its own pure-Kotlin engine. No ML runtime is needed.

| Model | Decoder | Encoder (DenseNet) | Training |
|---|---|---|---|
| Tiny | 3 layers, width 256, 8 heads, feed-forward 1024 | growth 24, 16 layers per block | 8 epochs |
| Small | 6 layers, width 384, 8 heads, feed-forward 1536 | growth 32, 16 layers per block | 8 epochs |
| Medium | 6 layers, width 512, 8 heads, feed-forward 2048 | growth 40, 20 layers per block | 8 epochs |

They are trained on the same data:
- MathWriting's human-written training set;
- MathWriting's synthetic set;
- calculator expressions composed from single handwritten symbols (see Credits).

## Licence

The model weights are licensed under
**[Creative Commons Attribution-NonCommercial-ShareAlike 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)**
(full text in [LICENSE](LICENSE)).
- **Why this licence:** they are trained on MathWriting, which is licensed CC BY-NC-SA 4.0, so they keep its terms.
- **What it allows:** you may use and share them, and models derived from them, for **non-commercial** purposes only,
  with attribution and under the same licence.
- **The app:** the Calculator app that uses them is non-commercial (no ads, purchases or revenue).

## Credits

The models are trained with data from:
- **MathWriting 2024** (Google). Online handwritten mathematical expressions, human-written and synthetic, and single
  glyphs. Licensed CC BY-NC-SA 4.0; its LaTeX labels come from Wikipedia (CC BY-SA 4.0).
  Philippe Gervais, Anastasiia Fadeeva, Andrii Maksai, "MathWriting: A Dataset For Handwritten Mathematical
  Expression Recognition", 2024.
- **HWRT** (Martin Thoma; from Detexify and write-math.com). Online handwritten symbols, licensed ODbL
  ([Zenodo 50022](https://zenodo.org/records/50022)). Its symbols are used in composed calculator expressions.
- **UJI Pen Characters v2** (UCI Machine Learning Repository). Online handwritten characters by 60 writers, licensed
  CC BY 4.0. Its characters are used in composed calculator expressions.

## Releases

| Tag | File | Bytes | SHA-256 |
|---|---|---:|---|
| `tiny-v1` | `tiny.bin` | 6,754,555 | `629ef469da4b4a65ce1ba630f1d6cef067446f3bd4986f7713e3dbd1a962b265` |
