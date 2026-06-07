# Aravis Fake Camera Subset

This directory is a trimmed copy of `aravisproject/aravis` focused on the fake GigE Vision camera simulator.

Retained parts:

- `src/arvfakecamera.*`, `src/arvgvfakecamera.*`, and `src/arvfakegvcamera.c`
- Fake camera GenICam XML: `src/arv-fake-camera.xml`
- Core Aravis support code needed by the fake camera path: buffers, streams, devices, interfaces, GenICam parser nodes, GigE Vision packet/network helpers, resources, and Meson configuration

Removed parts:

- Viewer, GStreamer plugin, tests, docs, translations, scripts, module code, CI metadata, and Git history
- USB3Vision, V4L2, GenTL, schema validation utility, and general command-line tools unrelated to the fake camera executable

The Meson build has been narrowed to produce the Aravis library subset and the `arv-fake-gv-camera` executable only.

License: LGPL-2.1-or-later. See `COPYING`.
