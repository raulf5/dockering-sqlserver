# Install Docker

### Windows

#### Enable BIOS Virtualization (Just Version 10 Home)

* [ ] Go to BIOS and activate Virtualization

#### Install Linux for Windows (Except Pro Versions)

* [ ] Install wsl

```bash
wsl --install
```

* [ ] Restart Windows
* [ ] start wsl from menu
* [ ] configure username and password

#### Install docker desktop

* [ ] Download Docker Desktop from [this link](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)
* [ ] Install package
* [ ] Check docker from terminal

```bash
docker version
#should return the docker version
 Engine:
 Version:          26.1.3
 API version:      1.45 (minimum version 1.24)

```

### Linux

* [ ] Set up Docker's package repository. See step one of [Install using the `apt` repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).
* [ ] Download latest [DEB package](https://desktop.docker.com/linux/main/amd64/149282/docker-desktop-4.30.0-amd64.deb?utm_source=docker\&utm_medium=webreferral\&utm_campaign=docs-driven-download-linux-amd64).
* [ ] Install the package with apt as follows:

```bash
$ sudo apt-get update
$ sudo apt-get install ./docker-desktop-<version>-<arch>.deb
```

{% hint style="info" %}
_More Info at_ [_https://docs.docker.com/desktop/install/ubuntu/_](https://docs.docker.com/desktop/install/ubuntu/)
{% endhint %}

### Mac

* [ ] Download the installer using the download buttons at the top of the page, or from the [release notes](https://docs.docker.com/desktop/release-notes/).
* [ ] Double-click `Docker.dmg` to open the installer, then drag the Docker icon to the **Applications** folder. By default, Docker Desktop is installed at `/Applications/Docker.app`.

