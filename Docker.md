# AdGuard Home - Docker

&nbsp;
<p align="center">
  <img src="https://cdn.adguard.com/public/Adguard/Common/adguard_home.svg" width="300px" alt="AdGuard Home" />
</p>
<h3 align="center">Privacy protection center for you and your devices</h3>
<p align="center">
  Free and open source, powerful network-wide ads & trackers blocking DNS server.
</p>

<br />

<p align="center">
    <img src="https://cdn.adguard.com/public/Adguard/Common/adguard_home.gif" width="800" />
</p>

1. [Introduction](#introduction)
2. [Quick Start](#quickstart)
3. [How to update to a newer version](#update)
4. [Running dev builds](#unstable)
5. [Supported tags / architectures](#tags)
6. [Additional configuration](#configuration)

## <a id="introduction"></a> Introduction

AdGuard Home is a network-wide software for blocking ads & tracking. After you set it up, it'll cover ALL your home devices, and you don't need any client-side software for that. You can learn more about it in our [official Github repository](https://github.com/AdguardTeam/AdGuardHome).

## <a id="quickstart"></a> Quick Start

#### 1. Pull the Docker image

This command will pull the latest stable version:

```bash
docker pull adguard/adguardhome
```

#### 2. Create directories for persistent configuration and data

The image exposes two volumes for data/configuration persistence. You should create a **data** directory on a suitable volume on your host system, e.g. `/my/own/workdir`, and a **config** directory on a suitable volume on your host system, e.g. `/my/own/confdir`.

#### 3. Create and run the container

Use the following command to create a new container and run AdGuard Home:
```bash
docker run --name adguardhome -v /my/own/workdir:/opt/adguardhome/work -v /my/own/confdir:/opt/adguardhome/conf -p 53:53/tcp -p 53:53/udp -p 67:67/udp -p 68:68/tcp -p 68:68/udp -p 80:80/tcp -p 443:443/tcp -p 853:853/tcp -p 3000:3000/tcp -d adguard/adguardhome
```

> Don't forget to use your own **data** and **config** directories!

Now you can open the browser and navigate to http://127.0.0.1:3000/ to control your AdGuard Home service.

#### 4. Control the container

* Start: `docker start adguardhome`
* Stop: `docker stop adguardhome`
* Remove: `docker rm adguardhome`

## <a id="update"></a> How to update to a newer version

1. Pull the new version from Docker Hub
    ```bash
    docker pull adguard/adguardhome
    ```

2. Stop and remove currently running container (assuming the container is named `adguardhome`):
    ```bash
    docker stop adguardhome
    docker rm adguardhome
    ```

3. Create and start the container using the new image:
    ```bash
    docker run --name adguardhome -v /my/own/workdir:/opt/adguardhome/work -v /my/own/confdir:/opt/adguardhome/conf -p 53:53/tcp -p 53:53/udp -p 67:67/udp -p 68:68/tcp -p 68:68/udp -p 80:80/tcp -p 443:443/tcp -p 853:853/tcp -p 3000:3000/tcp -d adguard/adguardhome
    ```

## <a id="unstable"></a> Running dev builds

If you want to be on the bleeding edge, you might want to run the image from the `edge` tag. It is automatically synced with the repository `master` branch.

In order to use it, simply replace `adguard/adguardhome` with `adguard/adguardhome:edge` in every command from the quick start.

```bash
docker pull adguard/adguardhome:edge
```

## <a id="tags"></a> Supported tags / architectures

`adguard/adguardhome` image is built for different architectures and supports the following tags:

* `latest` - latest **stable** build from the last tagged release.
* `edge` - latest build from the repository **master** branch, may be unstable.
* `$version` - specific release e.g. `v0.92`.

### Tags for different architectures

* **ARM64** - 64bit ARM build
  * `arm64-latest`
  * `arm64-edge`
* **ARMHF** - 32bit ARM build
  * `armhf-latest`
  * `armhf-edge`
* **i386** - x86 build
  * `i386-latest`
  * `i386-edge`
* **AMD64** - x86_64 build **default** 
  * `latest`
  * `edge`

## <a id="configuration"></a> Additional configuration

Upon the first execution, a file named `AdGuardHome.yaml` will be created, with default values written in it. You can modify the file while your AdGuard Home container is not running. Otherwise, any changes to the file will be lost because the running program will overwrite them.

Settings are stored in [YAML format](https://en.wikipedia.org/wiki/YAML), possible parameters that you can configure are listed on [this page](https://github.com/AdguardTeam/Adguardhome/wiki/Configuration).