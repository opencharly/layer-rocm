# rocm

AMD ROCm HIP runtime, OpenCL ICD, and GPU management tooling for OpenCharly
images.

The `rocm` candy installs the AMD ROCm user-space stack from the Fedora system
repos: `rocm-hip-runtime` (the HIP runtime), `rocm-opencl` (the OpenCL ICD),
`rocm-clinfo` (the `clinfo` diagnostic), and `rocm-smi` (the GPU management
tool). It exports `ROCM_PATH=/usr` and keeps the host video/render groups via
`keep-groups`, so a passed-through AMD GPU (`/dev/kfd` + `/dev/dri/renderD*`) is
usable.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `rocm` |
| Packages | `rocm-hip-runtime`, `rocm-opencl`, `rocm-clinfo`, `rocm-smi` |
| Binaries | `/usr/bin/clinfo`, `/usr/bin/rocm-smi` |
| Env | `ROCM_PATH=/usr` |
| Security | `group_add: [keep-groups]` |
| Distros | Fedora |

## Host requirements

AMD GPU support requires `/dev/kfd`, `/dev/dri/renderD*` render nodes, the user in
the `video` and `render` groups, and the `amdgpu` kernel driver loaded. Run
`charly doctor` to verify detection and `charly udev install` to set up device
permissions.

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-amd-app:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-rocm:v2026.239.1631'
      - my-app
```

Then, inside the built image:

```bash
clinfo --list     # ROCm OpenCL platforms
rocm-smi          # device temperature and utilization
echo $ROCM_PATH   # /usr
```

The candy's `plan:` asserts the installed packages, the two binaries, and
`ROCM_PATH`; live GPU enumeration is asserted only on a host with a real AMD GPU
passed through.

## Layout

- `charly.yml` — the `rocm:` candy entity (the `env:`, `security:`,
  `distro.fedora:` package section, and the `check:` probes) and the embedded
  `rocm-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-distros:rocm`
- NVIDIA counterpart: `/charly-distros:nvidia`, `/charly-distros:cuda`
- Device management: `/charly-automation:udev`, `/charly-core:charly-doctor`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
