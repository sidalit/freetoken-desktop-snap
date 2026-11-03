<div align="center">

<img src="snap/gui/freetoken-desktop.png" alt="FreeToken Desktop logo" width="128" />

# FreeToken Desktop

**Manage local language models from your desktop.**

[![freetoken-desktop](https://snapcraft.io/freetoken-desktop/badge.svg)](https://snapcraft.io/freetoken-desktop)
[![Base: core24](https://img.shields.io/badge/Base-core24-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core24)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architecture: amd64](https://img.shields.io/badge/Architecture-amd64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for [FreeToken Desktop](https://www.flashml.ai/), a control panel for local language models and inference.

</div>

---

## Getting Started

<a href="https://snapcraft.io/freetoken-desktop"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

```bash
sudo snap install freetoken-desktop
freetoken-desktop
```

Use **Install** in the application to download its engine, then download a model and start it. Local inference requires a supported NVIDIA GPU and driver; the engine and models need several gigabytes of free disk space.

To use the [freetoken](https://snapcraft.io/freetoken) snap's engine and models instead, configure the shared backend below before launching the desktop.

## Configure

Configure the engine and models in the application. Settings, downloads and caches stay in `~/snap/freetoken-desktop/common` across snap updates.

| Setting | Values | Default |
| --- | --- | --- |
| `backend` | `standalone`, `shared` | `standalone` |

### Shared backend

Run these commands as the desktop user. The shared backend requires snapd's experimental user-service support:

```bash
sudo snap set system experimental.user-daemons=true
sudo snap install freetoken+cuda
sudo snap set freetoken daemon.enabled=false
sudo install -d -o "$(id -u)" -g "$(id -g)" -m 0700 \
  /var/snap/freetoken/common/server /var/snap/freetoken/common/models
sudo snap connect freetoken-desktop:server freetoken:server
sudo snap connect freetoken-desktop:models freetoken:models
snap start --user --enable freetoken.backend
sudo snap set freetoken-desktop backend=shared
freetoken-desktop
```

The desktop downloads models to `/var/snap/freetoken/common/models` and controls the shared engine. Use one desktop user for this setup. Engine updates come from `snap refresh freetoken`; the application's **Install** button is for standalone mode. Switch back with `sudo snap set freetoken-desktop backend=standalone`.

The shared backend is experimental: the desktop's remote connection is supported, but its separate engine-installation screens still need checking. If it insists on installing another engine, report that screen instead of installing it.

In standalone mode, for models on removable storage:

```bash
sudo snap connect freetoken-desktop:removable-media
```

## Limitations

- Linux amd64 only. Host coding agents and tools cannot be launched from the confined application.
- GPU kernels outside upstream's precompiled coverage may require a CUDA compiler, which is not included.
- Update the desktop with `sudo snap refresh freetoken-desktop`; its built-in desktop updater cannot replace snap files. In standalone mode, engine updates remain available through the application.
- The desktop source and redistribution license are not provided with the upstream download; the recipe uses its official `.deb`.

## Build

```bash
git clone https://github.com/sidalit/freetoken-desktop-snap.git
cd freetoken-desktop-snap
sudo snap install snapcraft --classic
snapcraft pack
```

Builds use the pinned desktop release; there is no desktop HEAD build without public source.

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/freetoken-desktop-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/FlashML-org/FreeToken/issues).

## License

Snap packaging: [MIT](LICENSE).
