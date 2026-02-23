Sunshine RockChip Fork
======================

.. warning:: This fork is for Rockchip platform only and has only been tested on RK3566 chip!

**Note on Performance:**
Although a dedicated FFmpeg version for Rockchip is used and can find the rkmpp encoder,
it currently occupies CPU significantly, similar to not using rkmpp hardware encoding at all.

Build Instructions
===================

First, install build dependencies:

.. code-block:: bash

   sudo apt update
   sudo apt install -y \
      git meson cmake pkg-config gcc libasound2-dev libdrm-dev ninja-build libv4l-dev \
      libboost-all-dev
   sudo apt-get install -y \
      build-essential gcc-10 g++-10 libayatana-appindicator3-dev libxtst-dev wget \
      libavdevice-dev libcap-dev libcurl4-openssl-dev libdrm-dev libevdev-dev \
      libminiupnpc-dev libnotify-dev libnuma-dev libopus-dev libpulse-dev \
      libssl-dev libva-dev libwayland-dev libx11-dev libxcb-shm0-dev \
      libxcb-xfixes0-dev libxcb1-dev libxfixes-dev libxrandr-dev

Then, build rkmpp and rkrga modules:

.. code-block:: bash

   git clone https://github.com/HermanChen/mpp.git rkmpp
   cd rkmpp
   mkdir ./build && cd build
   mkdir -p rkmpp/rkmpp_build && cd rkmpp/rkmpp_build
   cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
      -DBUILD_SHARED_LIBS=ON -DBUILD_TEST=OFF ..
   sudo make -j$(nproc)
   sudo make install

.. code-block:: bash

   git clone -b jellyfin-rga --depth=1 https://github.com/nyanmisaka/rk-mirrors.git rkrga
   cd rkrga
   meson setup ./ rkrga_build --prefix=/usr --libdir=lib --buildtype=release \
      -Dcpp_args=-fpermissive -Dlibdrm=false -Dlibrga_demo=false
   meson configure rkrga_build
   sudo ninja -C rkrga_build install

Get specific ffmpeg build from lvxingye/build-deps repo:

.. code-block:: bash

   wget https://github.com/lvxingye/build-deps/releases/download/ubuntu-20.04/ffmpeg.tar.gz
   tar -xvf ./ffmpeg.tar.gz
   mv ./ffmpeg_build ./_deps/ffmpeg

Clone this fork:

.. code-block:: bash

   git clone https://github.com/lvxingye/Sunshine_rk.git

Configure with CMake (disable CUDA and enable Rockchip support):

.. code-block:: bash

   cd Sunshine_rk
   cmake -B build/ -S ./ \
      -DSUNSHINE_ENABLE_CUDA=OFF \
      -DSUNSHINE_ENABLE_ROCKCHIP=ON \
      -DFFMPEG_PREPARED_BINARIES="`pwd`/_deps/ffmpeg/" \
      -DSUNSHINE_ASSETS_DIR=share/sunshine \
      -DSUNSHINE_EXECUTABLE_PATH=/usr/bin/sunshine

Build Sunshine (it's preferred to use 2 processes to avoid OOM):

.. code-block:: bash

   cd build
   ninja -C ./ -j2

Install Sunshine:

.. code-block:: bash

   sudo env "PATH=$PATH" ninja install




Overview
========
LizardByte has the full documentation hosted on `Read the Docs <https://sunshinestream.readthedocs.io/>`__.

About
-----
Sunshine is a self-hosted game stream host for Moonlight.
Offering low latency, cloud gaming server capabilities with support for AMD, Intel, and Nvidia GPUs for hardware
encoding. Software encoding is also available. You can connect to Sunshine from any Moonlight client on a variety of
devices. A web UI is provided to allow configuration, and client pairing, from your favorite web browser. Pair from
the local server or any mobile device.

System Requirements
-------------------

.. warning:: This table is a work in progress. Do not purchase hardware based on this.

**Minimum Requirements**

+------------+------------------------------------------------------------+
| GPU        | AMD: VCE 1.0 or higher, see `obs-amd hardware support`_    |
|            +------------------------------------------------------------+
|            | Intel: VAAPI-compatible, see: `VAAPI hardware support`_    |
|            +------------------------------------------------------------+
|            | Nvidia: NVENC enabled cards, see `nvenc support matrix`_   |
+------------+------------------------------------------------------------+
| CPU        | AMD: Ryzen 3 or higher                                     |
|            +------------------------------------------------------------+
|            | Intel: Core i3 or higher                                   |
+------------+------------------------------------------------------------+
| RAM        | 4GB or more                                                |
+------------+------------------------------------------------------------+
| OS         | Windows: 10+ (Windows Server not supported)                |
|            +------------------------------------------------------------+
|            | macOS: 12+                                                 |
|            +------------------------------------------------------------+
|            | Linux/Debian: 11 (bullseye)                                |
|            +------------------------------------------------------------+
|            | Linux/Fedora: 38+                                          |
|            +------------------------------------------------------------+
|            | Linux/Ubuntu: 20.04+ (focal)                               |
+------------+------------------------------------------------------------+
| Network    | Host: 5GHz, 802.11ac                                       |
|            +------------------------------------------------------------+
|            | Client: 5GHz, 802.11ac                                     |
+------------+------------------------------------------------------------+

**4k Suggestions**

+------------+------------------------------------------------------------+
| GPU        | AMD: Video Coding Engine 3.1 or higher                     |
|            +------------------------------------------------------------+
|            | Intel: HD Graphics 510 or higher                           |
|            +------------------------------------------------------------+
|            | Nvidia: GeForce GTX 1080 or higher                         |
+------------+------------------------------------------------------------+
| CPU        | AMD: Ryzen 5 or higher                                     |
|            +------------------------------------------------------------+
|            | Intel: Core i5 or higher                                   |
+------------+------------------------------------------------------------+
| Network    | Host: CAT5e ethernet or better                             |
|            +------------------------------------------------------------+
|            | Client: CAT5e ethernet or better                           |
+------------+------------------------------------------------------------+

**HDR Suggestions**

+------------+------------------------------------------------------------+
| GPU        | AMD: Video Coding Engine 3.4 or higher                     |
|            +------------------------------------------------------------+
|            | Intel: UHD Graphics 730 or higher                          |
|            +------------------------------------------------------------+
|            | Nvidia: Pascal-based GPU (GTX 10-series) or higher         |
+------------+------------------------------------------------------------+
| CPU        | AMD: todo                                                  |
|            +------------------------------------------------------------+
|            | Intel: todo                                                |
+------------+------------------------------------------------------------+
| Network    | Host: CAT5e ethernet or better                             |
|            +------------------------------------------------------------+
|            | Client: CAT5e ethernet or better                           |
+------------+------------------------------------------------------------+

Integrations
------------

.. image:: https://img.shields.io/github/actions/workflow/status/lizardbyte/sunshine/CI.yml.svg?branch=master&label=CI%20build&logo=github&style=for-the-badge
   :alt: GitHub Workflow Status (CI)
   :target: https://github.com/LizardByte/Sunshine/actions/workflows/CI.yml?query=branch%3Amaster

.. image:: https://img.shields.io/github/actions/workflow/status/lizardbyte/sunshine/localize.yml.svg?branch=nightly&label=localize%20build&logo=github&style=for-the-badge
   :alt: GitHub Workflow Status (localize)
   :target: https://github.com/LizardByte/Sunshine/actions/workflows/localize.yml?query=branch%3Anightly

.. image:: https://img.shields.io/readthedocs/sunshinestream.svg?label=Docs&style=for-the-badge&logo=readthedocs
   :alt: Read the Docs
   :target: http://sunshinestream.readthedocs.io/

Support
-------

Our support methods are listed in our
`LizardByte Docs <https://lizardbyte.readthedocs.io/en/latest/about/support.html>`__.

Downloads
---------

.. image:: https://img.shields.io/github/downloads/lizardbyte/sunshine/total.svg?style=for-the-badge&logo=github
   :alt: GitHub Releases
   :target: https://github.com/LizardByte/Sunshine/releases/latest

.. image:: https://img.shields.io/docker/pulls/lizardbyte/sunshine.svg?style=for-the-badge&logo=docker
   :alt: Docker
   :target: https://hub.docker.com/r/lizardbyte/sunshine

.. image:: https://img.shields.io/badge/dynamic/xml.svg?color=orange&label=Winget&style=for-the-badge&prefix=v&query=%2F%2Ftr%5B%40id%3D%27winget%27%5D%2Ftd%5B3%5D%2Fspan%2Fa&url=https%3A%2F%2Frepology.org%2Fproject%2Fsunshine%2Fversions&logo=microsoft
   :alt: Winget Version
   :target: https://github.com/microsoft/winget-pkgs/tree/master/manifests/l/LizardByte/Sunshine

Stats
------
.. image:: https://img.shields.io/github/stars/lizardbyte/sunshine.svg?logo=github&style=for-the-badge
   :alt: GitHub stars
   :target: https://github.com/LizardByte/Sunshine

.. _nvenc support matrix: https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new
.. _obs-amd hardware support: https://github.com/obsproject/obs-amd-encoder/wiki/Hardware-Support
.. _VAAPI hardware support: https://www.intel.com/content/www/us/en/developer/articles/technical/linuxmedia-vaapi.html
