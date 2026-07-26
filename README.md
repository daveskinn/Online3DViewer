# Online 3D Viewer

[![Build status](https://github.com/kovacsv/Online3DViewer/actions/workflows/build.yml/badge.svg)](https://github.com/kovacsv/Online3DViewer/actions/workflows/build.yml)
[![npm version](https://badge.fury.io/js/online-3d-viewer.svg)](https://badge.fury.io/js/online-3d-viewer)
[![DeepScan grade](https://deepscan.io/api/teams/16586/projects/19893/branches/524595/badge/grade.svg)](https://deepscan.io/dashboard#view=project&tid=16586&pid=19893&bid=524595)

Online 3D Viewer (https://3dviewer.net) is a free and open source web solution to visualize and explore 3D models in your browser. This repository contains the source code of the website and the library behind it.

[Live website](https://3dviewer.net) &nbsp;-&nbsp; [Website documentation](https://3dviewer.net/info) &nbsp;-&nbsp; [Developer documentation](https://kovacsv.github.io/Online3DViewer) &nbsp;-&nbsp; [Discord server](https://discord.gg/C7x9u833yN)

## Example

![Start Page](assets/images/3dviewer_net_start_page.png?raw=true)

[Check the live version!](https://3dviewer.net/#model=https://raw.githubusercontent.com/kovacsv/Online3DViewer/dev/test/testfiles/gltf/DamagedHelmet/glTF-Binary/DamagedHelmet.glb)

## Supported file formats

* **Import**: 3dm, 3ds, 3mf, amf, bim, brep, dae, fbx, fcstd, gltf, ifc, iges, step, stl, obj, off, ply, wrl.
* **Export**: 3dm, bim, gltf, obj, off, stl, ply.

## External Libraries

Online 3D Viewer uses these wonderful libraries: [three.js](https://github.com/mrdoob/three.js), [pickr](https://github.com/Simonwep/pickr), [fflate](https://github.com/101arrowz/fflate), [draco](https://github.com/google/draco), [rhino3dm](https://github.com/mcneel/rhino3dm), [web-ifc](https://github.com/tomvandig/web-ifc), [occt-import-js](https://github.com/kovacsv/occt-import-js).

## Self-Hosted Docker Deployment with Model Browser

This fork includes a custom Docker setup that serves Online3DViewer via nginx with a browsable model library.

### Features

- Browse CAD files stored on the server from a simple file browser page
- Click any file to open it directly in the viewer
- Supports subfolders
- Accessible remotely via Tailscale

### Directory Structure

The custom files are in the `docker/` folder:

- `Dockerfile` — builds an nginx image with the viewer and model browser
- `default.conf` — nginx config with autoindex and CORS headers
- `models.html` — custom file browser page

### Build

```bash
npm install
npm run build_website
cp website/index.html build/website/
cp -r website/assets build/website/
sed -i 's|../build/website_dev/|./|g' build/website/index.html

mkdir -p /your/deploy/path/site
cp -r build/website/* /your/deploy/path/site/
cp docker/models.html /your/deploy/path/site/
cp docker/Dockerfile /your/deploy/path/
cp docker/default.conf /your/deploy/path/
docker build -t local-3d-viewer /your/deploy/path/
```

### Docker Compose

```yaml
version: "3.8"
services:
  web-3d-viewer:
    image: local-3d-viewer:latest
    container_name: web-3d-viewer
    restart: unless-stopped
    ports:
      - "8088:80"
    volumes:
      - /your/models/folder:/usr/share/nginx/html/models
    labels:
      tsdproxy.enable: "true"
      tsdproxy.name: "3d-viewer"
      tsdproxy.container_port: "80"
```

### Adding Model Folders

Add additional volume mounts to the compose file:

```yaml
volumes:
  - /your/models/folder:/usr/share/nginx/html/models
  - /your/other/folder:/usr/share/nginx/html/models/other
```

### Notes

- The model browser is at `/models.html` (set as the nginx default index)
- Files open directly in the viewer via the `#model=` hash parameter
- Watchtower will not auto-update this image as it is built locally
