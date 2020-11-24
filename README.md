# Docker Image for testing Ansible deployment using Docker with Molecule Docker, by [FEROX](https://ferox.yt)

![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/frxyt/molecule-dind.svg)
![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/frxyt/molecule-dind.svg)
![Docker Pulls](https://img.shields.io/docker/pulls/frxyt/molecule-dind.svg)
![GitHub issues](https://img.shields.io/github/issues/frxyt/docker-molecule-dind.svg)
![GitHub last commit](https://img.shields.io/github/last-commit/frxyt/docker-molecule-dind.svg)

> This image packages Cron, Docker, Logrotate, Python, OpenSSH-Server, Sudo and SystemD.
> It can be used to test Ansible deployments using Docker within Molecule Docker.
> The aim of this image is to be as close as possible as a random VM Debian Buster box that can be found on any cloud hosting provider.

* Docker Hub: https://hub.docker.com/r/frxyt/molecule-dind
* GitHub: https://github.com/frxyt/docker-molecule-dind

## Docker Hub Image

**`frxyt/molecule-dind`**

## Usage

1. Create your Ansible role: `molecule init role <name>`
1. `cd <name>`
1. Edit `molecule.yml`:
   ```yml
   ---
   dependency:
     name: galaxy
   driver:
     name: docker
   platforms:
     - name: molecule
       image: frxyt/molecule-dind:latest
       privileged: true
   provisioner:
     name: ansible
   verifier:
     name: ansible
   ```
1. If you need a specific user, you can also edit `Dockerfile.j2` and add these lines:
   ```Dockerfile
   RUN set -ex; \
       groupadd -r debian; \
       useradd --no-log-init -r -g debian debian; \
       adduser debian sudo; \
       echo 'debian ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/debian;
   USER debian
   ```
1. Add a file `prepare.yml`:
   ```yml
   ---
   - name: Prepare
     hosts: all
     #vars:
     #  ansible_become: true
     tasks:
       - name: Start Docker
         service:
           name: docker
           state: started
    ```
1. If you use a specific user, edit also `converge.yml` to add these lines (and make sure they are uncommented in `prepare.yml`) :
   ```yml
   vars:
     ansible_become: true
   ```
1. `molecule create`
1. `molecule converge`
1. `molecule login`
1. `molecule destroy`

## Build & Test

```sh
docker build -f Dockerfile -t frxyt/molecule-dind:latest .
docker run --rm -it --privileged frxyt/molecule-dind:latest bash
service docker start
docker run --rm -it debian bash
```

## License

This project and images are published under the [MIT License](LICENSE).

```
MIT License

Copyright (c) 2020 FEROX YT EIRL, www.ferox.yt <devops@ferox.yt>
Copyright (c) 2020 Jérémy WALTHER <jeremy.walther@golflima.net>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```