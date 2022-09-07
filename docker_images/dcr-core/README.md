# dcr-core: Document Content Recognition API Image

This image supports the use of a Docker container for operational use of the DCR-CORE API in an Ubuntu environment.

### Table of Contents

**[1. Installed core components](#installed)**<br>
**[2. Getting started with the `dcr-core` container](#starting_container)**<br>
**[3. Getting started with the `dcr-core` library](#starting_library)**<br>

----

## <a name="installed"></a> 1. Installed core components

With the following command you can check in detail which software versions are included in the Docker image:

    apt list --installed

---

### Version 0.9.7

| Component     | Version         | Remark               | Status |
|---------------|-----------------|----------------------|--------|
| asdf          | v0.10.2         | base version         |        | 
| CMake         | 3.22.1          | base version         |        | 
| curl          | 7.81.0          | base version         |        | 
| GCC & G++     | 11.2.0          | base version         |        |
| Git           | 2.34.1          | base version         |        | 
| GNU Autoconf  | 2.71            | base version         |        | 
| GNU Automake  | 1.16.5          | base version         |        | 
| GNU make      | 4.3             | base version         |        | 
| OpenSSL       | 1.1.1o          |                      |        | 
| Pandoc        | 2.19.2          |                      |        | 
| PDFlib TET    | 5.3             |                      |        | 
| Poppler       | 22.02.0         |                      |        | 
| procps-ng     | 3.3.17          | base version         |        | 
| Python3       | 3.10.7          |                      |        |
| Tesseract OCR | 5.2.0-22-g0daf1 | base version         |        |
| TeX Live      | 2022            | base version         |        | 
| Ubuntu        | 22.04.1 LTS     | base version [jammy] |        | 
| Vim           | 8.2.3995        | base version         |        |
| wget          | 1.21.2          | base version         |        | 

----

## <a name="starting_container"></a> 2. Getting started with the `dcr-core` container

### Creating and running a new container
(Assuming the path prefix for the local data directory mapping is `d:/TempMan`)

    docker run -it --name dcr-core -v d:/TempMan:/dcr-core/data/inbox_prod konnexionsgmbh/dcr-core:0.9.7

### Restarting the container

    docker start dcr-core

### Check the container is running

    docker ps

### To access a running container

    docker attach --detach-keys="ctrl-a" dcr-core 

### Stopping a running container

    docker stop dcr-core

## <a name="starting_library"></a> 3. Getting started with the  `dcr-core` library

### Starting Python in the Virtual Environment (inside the `dcr-core`container)

    python3 -m pipenv run python3

### Make the `dcr_core` module available

    from dcr_core import cls_process

### Create an instance of the `Process` class

    process = cls_process.Process()

### Process document files

    process.document("data/inbox_prod/<file name>")
    ...
