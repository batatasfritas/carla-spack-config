# Compilando tu primera stack (Daniel Jana, CARLA 2024)

Para esta sessión más interactiva hay dos planes posibles:

- bajar spack de github y hacerlo todo de manera independiente
- bajar una imagem docker que les proponemos y tener un ambiente un poco más
  controlado.

La primera opción tal vez sea un poco más complicada por que implica que la
máquina ya tenga las dependencias necesarias. La segunda opción tiene todo
lo que hace falta y funciona con docker o apptainer. Podría usarse en el
laptop, o en un nodo de un cluster a que tengan acceso.

## Bajando Spack de github

Para empezar no hay que hacer sino:

```bash
git clone -b releases/v0.22 --depth 1 https://github.com/spack/spack.git
source spack/share/spack/setup-env.sh
```

El primer comando baja spack (~60 MB) y el segundo lo activa en la sessión
del usuario. Spack funciona en user-land, no necesita acceso root.

## Spack en un container

También creamos un container Ubuntu 22.04 (~220 MB) para que puedan hacer
estos ejercícios en sus laptops, o en un centro HPC que puedan utilizar.

### Container en un docker

Con docker se puede bajar la imagen con el comando:

```bash
docker pull registry.c4science.ch/carla2024/carla2024_ubuntu:latest
```

Les proponemos lanzar el container con una variación de los comandos:

```bash
mkdir spack_stack
docker run -it --user carla2024 --mount type=bind,source=./spack_stack,target=/home/carla2024/spack_stack carla2024_ubuntu
```

La opción `--user` se propone para que quede claro que Spack funciona en una
cuenta de usuario normal y no necesita acceso root.

### Container en un apptainer

Para bajar el container con apptainer hay que hacer:

```bash
apptainer pull docker://registry.c4science.ch/carla2024/carla2024_ubuntu:latest
```

En nuestro caso, esto baja la imagen y crea un archivo `.sif` que se puede
lanzar con Apptainer.
La manera de lanzarlo con apptainer va a cambiar un poco de un cluster al
otro. Para lanzar una sessión de apptainer de 1h con 20 cores en uno de
nuestros clusters no toca sino:

```bash
mkdir spack_stack
srun -c 20 -t 1:00:00 --pty apptainer run --writable-tmpfs --cleanenv \
  -H spack_stack:/home/$USER carla2024_ubuntu_latest.sif bash
source /opt/software/spack/share/spack/setup-env.sh
```

## Creando tu propio container

Para los que quieran crear su propio container, aqui está la Dockerfile que
utilizamos:

```Dockerfile
FROM ubuntu:22.04
RUN apt update
RUN apt install -y gcc g++ gfortran bzip2 curl fdisk file git gnupg2 iproute2 libaprutil1 libelf1 mercurial nano python3 python3-pip subversion unzip vim zstd
RUN rm -rf /var/lib/apt/lists/*
RUN mkdir -p /opt/software/externals
RUN git clone --depth=1 -b releases/v0.22 https://github.com/spack/spack.git /opt/software/spack
RUN useradd -m -s /bin/bash carla2024
RUN chown -R carla2024. /opt/software
RUN echo "source /opt/software/spack/share/spack/setup-env.sh" >> /home/carla2024/.bashrc
RUN echo "cd" >> /home/carla2024/.bashrc
```
