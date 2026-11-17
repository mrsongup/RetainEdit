以下是可直接粘贴到 GitHub 的英文版本，未修改任何文件。

**GitHub Description**

```text
RetainEdit: Text-guided retinal lesion editing with adaptive spatial–frequency source reuse to preserve anatomy, texture, and global appearance, without user-provided masks at inference.
```

**README.md**

```markdown
# RetainEdit

**RetainEdit: Preserving Anatomy and Texture in Retinal Lesion Editing**

RetainEdit is a framework for text-guided retinal lesion editing. Given a source fundus photograph and an editing instruction, it aims to synthesize the requested lesions while preserving existing retinal anatomy, fine textures, and overall image appearance.

The central challenge is that new lesions and structures that should remain unchanged, such as fine blood vessels, can occupy the same local neighborhood. Excessive reuse of the source image can suppress lesion generation, while insufficient reuse can introduce unintended structural changes and background drift.

RetainEdit addresses this conflict by learning **where, at which frequency scales, and how strongly to reuse source information** within a frozen image editor. At inference, it requires only the source image and text instruction, without user-provided editing or protection masks.

[Paper](manuscript/main.pdf) · [LaTeX Source](manuscript/main.tex)

## Method Overview

RetainEdit learns continuous spatial and frequency-dependent weights that control source latent reinjection during denoising.

1. **Same-state conditional comparisons.** At selected sampling steps, the editor predicts under the editing instruction and two preservation conditions at the same noisy state. Their differences provide cues about the requested edit, preservation drift, and sensitivity to preservation wording.

2. **Adaptive spatial–frequency control.** A learned controller uses these cues to predict source-reuse weights across spatial locations and three groups of two-level Haar wavelet coefficients: fine details (`H1`), coarser details (`H2`), and low-frequency appearance (`L2`).

3. **Selective source reinjection.** Source coefficients are mixed into the current clean latent prediction according to the learned weights. The corrected prediction is then returned to the original sampling process.

Training uses region-specific weak supervision: reliable addition regions follow the lesion-editing target, while protected regions follow the source image. Uncertain regions are treated separately. The generative backbone remains frozen, and the controller is trained using cached predictions rather than backpropagating through complete sampling trajectories.

## Backbones and Evaluation

The project studies RetainEdit with:

- **Qwen-Image-Edit**, using its instruction-based editing pipeline.
- **FLUX.1-dev**, using FlowEdit as the editing pipeline.

The main fundus editing setup uses source images, editing instructions, and synthetic references from Med-Banana, with 2,000 training sources, 32 development sources, and 200 test sources. Frozen transfer is evaluated on 27 IDRiD fundus images and 50 PIE-Bench natural images.

Evaluation covers pixelwise, structural, perceptual, and frequency-domain preservation using RGB MAE, SSIM, LPIPS, and Haar-band errors.

## Results Reported in the Current Manuscript

In the Qwen comparison, RetainEdit reduces RGB MAE by **24.75%** and LPIPS by **27.68%** relative to the base editor.

| Method | RGB MAE ↓ | SSIM ↑ | LPIPS ↓ |
| --- | ---: | ---: | ---: |
| Base editor | 0.017687 | 0.869795 | 0.127560 |
| RetainEdit | 0.013310 | 0.894819 | 0.092247 |

These metrics measure source preservation. Completion of the requested edit is assessed separately. Results should be interpreted with the configuration and evaluation region recorded for each experiment; historical medical-LoRA experiments and original-backbone experiments are distinct settings.

## Repository Contents

| Directory | Contents |
| --- | --- |
| `manuscript/` | Paper sources, bibliography, and compiled manuscript |
| `materials/implementation/` | Implementation snapshots of the controller, source reuse, training, and evaluation components |
| `experiments/` | Experimental protocols, configurations, analyses, and result records |
| `figures/` | Paper figures and editable figure assets |
| `tables/` | Result tables and supporting evidence |
| `outputs/` | Qualitative galleries, human-review tools, and exported reports |
| `tools/` | Scripts for manuscript building, table preparation, and validation |

## Code Status

This repository currently contains research materials and implementation snapshots. It is not yet packaged as a standalone training and inference release.

Some scripts and historical experiment records depend on the original research environment. Refer to the accompanying experiment documentation for configuration details and provenance.
```
