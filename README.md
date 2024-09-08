# VapourSynth-BM3DCUDA-Universal

Copyright© 2021 WolframRhodium

Universal, crossplatform fork of VapourSynth-BM3DCUDA, CPU implementation only.

## Description

This folk of VapourSynth-BM3DCUDA has been adapted for cross-platform compatibility and is exclusively available for CPU-based platforms. It removes all GPU-related implementations (CUDA, etc.) and focuses on utilizing the SIMD everywhere library to ensure compatibility across virtually all platforms with a C compiler. The primary purpose of this adaptation is to maintain compatibility with the numerous existing wrappers and interfaces initially designed for the BM3DCUDA version.

- The CPU version has been ported to be compatible with nearly all platforms and CPU architectures where a C compiler is available. This includes but is not limited to Windows, macOS, Linux, and various UNIX-like systems; Arm64, X86_64 and other CPUs.

- By utilizing the SIMD everywhere library, this version automatically uses SIMD when availiable to improve performance.

- Please check [VapourSynth-BM3D](https://github.com/HomeOfVapourSynthEvolution/VapourSynth-BM3D), [VapourSynth-BM3DCUDA](https://github.com/WolframRhodium/VapourSynth-BM3DCUDA).

## Requirements

- A working C compiler.

## Parameters

```python3
bm3dcpu.BM3D(clip clip[, clip ref=None, float[] sigma=3.0, int[] block_step=8, int[] bm_range=9, int radius=0, int[] ps_num=2, int[] ps_range=4, bint chroma=False, int device_id=0, bool fast=True, int extractor_exp=0])
```

- clip:

    The input clip. Must be of 32 bit float format. Each plane is denoised separately if `chroma` is set to `False`. Data of unprocessed planes is undefined. Frame properties of the output clip are copied from it.

- ref:

    The reference clip. Must be of the same format, width, height, number of frames as `clip`.

    Used in block-matching and as the reference in empirical Wiener filtering, i.e. `bm3d.Final` / `bm3d.VFinal`:

    ```python3
    basic = core.bm3dcpu.BM3D(src, radius=0)
    final = core.bm3dcpu.BM3D(src, ref=basic, radius=0)

    vbasic = core.bm3dcpu.BM3D(src, radius=radius_nonzero).bm3d.VAggregate(radius=radius_nonzero)
    vfinal = core.bm3dcpu.BM3D(src, ref=vbasic, radius=r).bm3d.VAggregate(radius=r)
    
    # alternatively, using the v2 interface
    basic_or_vbasic = core.bm3dcpu.BM3Dv2(src, radius=r)
    final_or_vfinal = core.bm3dcpu.BM3Dv2(src, ref=basic_or_vbasic, radius=r)
    ```

    corresponds to the followings (ignoring color space handling and other differences in implementation), respectively

    ```python3
    basic = core.bm3d.Basic(clip)
    final = core.bm3d.Final(basic, ref=src)

    vbasic = core.bm3d.VBasic(src, radius=r).bm3d.VAggregate(radius=r, sample=1)
    vfinal = core.bm3d.VFinal(src, ref=vbasic, radius=r).bm3d.VAggregate(radius=r)
    ```

- sigma:
    The strength of denoising for each plane.

    The strength is similar (but not strictly equal) as `VapourSynth-BM3D` due to differences in implementation. (coefficient normalization is not implemented, for example)

    Default `[3,3,3]`.

- block_step, bm_range, radius, ps_num, ps_range:

    Same as those in `VapourSynth-BM3D`.

    If `chroma` is set to `True`, only the first value is in effect.

    Otherwise an array of values may be specified for each plane (except `radius`).
    
    **Note**: It is generally not recommended to take a large value of `ps_num` as current implementations do not take duplicate block-matching candidates into account during temporary searching, which may leads to regression in denoising quality. This issue is not present in `VapourSynth-BM3D`.

    **Note2**: Lowering the value of "block_step" will be useful in reducing blocking artifacts at the cost of slower processing.

- chroma:

    CBM3D algorithm. `clip` must be of `YUV444PS` format.

    Y channel is used in block-matching of chroma channels.

    Default `False`.

## Notes

- `bm3d.VAggregate` should be called after temporal filtering, as in `VapourSynth-BM3D`. Alternatively, you may use the `BM3Dv2()` interface for both spatial and temporal denoising in one step.

## Compilation

```bash
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release -D VAPOURSYNTH_INCLUDE_DIRECTORY=/path/to/vapoursynth_headers

cmake --build build --config Release
```
