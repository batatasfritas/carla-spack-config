# Primeros pasos

La primera cosa que hay que hacer es definir una carpeta para las
configuraciones de Spack. La opción más común para eso es en `etc/spack`
adentro de la carpeta de Spack, pero eso no es sino una opción. Presentaremos
una opción más genérica, que va mejor con el container.

Se puede usar directamente la carpeta `spack_stack` que teníamos antes, pero
creando una carpeta adentro se puede organizar mejor las cosas en los pasos
siguientes.

## Una carpeta para las configuraciones

Proponemos entonces algo como `mkdir -p spack_stack/spack-config`. Ahí se
debe crear un archivo `config.yaml` donde se define la raiz de la instalación.
Algo como:

```yaml
config:
  install_tree:
    root: /home/<user>/spack_stack/v1/opt/spack
```

Aquí el v1 es un intento de versionar la stack. Una futura stack podría hacerse
en un v2.
Atención que el usuario cambia dependiendo de si están en docker (carla2024) o
usando apptainer.

Para garantizar que Spack va a leer la carpeta que tiene este config.yaml se
debe lanzar el comando:

```bash
export SPACK_SYSTEM_CONFIG_PATH=/path/para/spack_stack/stack-config
# o, si uno ya está en esa carpeta
export SPACK_SYSTEM_CONFIG_PATH=$PWD
```

Si todo va bien, cuando uno lanza el comando:

```bash
spack config blame config | grep root
/home/carla2024/spack_stack/spack_config/config.yaml:3      root: /home/carla2024/spack_stack/v1/opt/spack
```

el path que aparece es el que definimos. Si el path es `$spack/opt/spack`
hay un error, probablemente de ortografia, en el `config.yaml`.

## Añadiendo el compilador y un build cache

En seguida lanzemos una secuencia de 3 comandos:

```bash
spack compiler find /usr
spack mirror add v0.22.2 https://binaries.spack.io/v0.22.2
spack buildcache keys --install --trust
```

El primer va a descubrir los compiladores gcc, g++ y gfortran para poder seguir
adelante con instalaciones futuras.
Los otros dos añaden un build cache de Spack, para ir a buscar ciertos
programas ya compilados. Son utiles sobretodo en el contexto de una demo
con un container que se lanza en un laptop. No tendríamos tiempo de compilar
casi nada.

Escogiendo bien paquetes que instalaremos podemos instalar una stack que
demuestre lo que se puede hacer con spack solo con el buildcache. Esa es
la razón por la cual el compilador que usamos en estos ejemplos es el
`gcc-11.4.0`, el compilador de base de Ubuntu 22.04, ya que hay mucho
software pre-compilado para esto.

## Instalando nuestro primer programa

Para terminar esta sección, instalemos un primer programa, solo para probar
que todo va bien. Lanzemos una concretización en primer lugar:

```bash
spack spec -Il fftw %gcc@11.4.0
```

Spack empieza por buscar unos archivos donde hay la palabra clingo, antes de
pensar un rato y devolver una concretización. Se ve que va a instalar
`fftw +mpi` con `mpich` como provedor MPI, entre otras dependencias.

Instalemos FFTW entonces:

```bash
spack install fftw %gcc@11.4.0
[+] /usr (external glibc-2.35-a7drdl4tlx4bu3mzhor75pskvd3pdot6)
==> Installing gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd [2/41]
==> Fetching https://binaries.spack.io/v0.22.2/build_cache/linux-ubuntu22.04-x86_64_v3-gcc-11.4.0-gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd.spec.json.sig
gpg: Signature made Thu Jul  4 18:15:16 2024 UTC
gpg:                using RSA key D2C7EB3F2B05FA86590D293C04001B2E3DB0C723
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: Good signature from "Spack Project Official Binaries <maintainers@spack.io>" [ultimate]
==> Fetching https://binaries.spack.io/v0.22.2/build_cache/linux-ubuntu22.04-x86_64_v3/gcc-11.4.0/gcc-runtime-11.4.0/linux-ubuntu22.04-x86_64_v3-gcc-11.4.0-gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd.spack
==> Extracting gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd from binary cache
==> gcc-runtime: Successfully installed gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd
  Search: 0.00s.  Fetch: 9.71s.  Install: 0.70s.  Extract: 0.66s.  Relocate: 0.03s.  Total: 10.41s
[+] /home/carla2024/spack_stack/v1/opt/spack/linux-ubuntu22.04-x86_64_v3/gcc-11.4.0/gcc-runtime-11.4.0-f47qm6qeplqyahc4zhfpfdnf5mo6gxvd
...
...
==> Installing fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273 [41/41]
==> Fetching https://binaries.spack.io/v0.22.2/build_cache/linux-ubuntu22.04-x86_64_v3-gcc-11.4.0-fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273.spec.json.sig
gpg: Signature made Thu Jul  4 17:16:56 2024 UTC
gpg:                using RSA key D2C7EB3F2B05FA86590D293C04001B2E3DB0C723
gpg: Good signature from "Spack Project Official Binaries <maintainers@spack.io>" [ultimate]
==> Fetching https://binaries.spack.io/v0.22.2/build_cache/linux-ubuntu22.04-x86_64_v3/gcc-11.4.0/fftw-3.3.10/linux-ubuntu22.04-x86_64_v3-gcc-11.4.0-fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273.spack
==> Extracting fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273 from binary cache
==> fftw: Successfully installed fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273
  Search: 0.00s.  Fetch: 9.87s.  Install: 0.50s.  Extract: 0.44s.  Relocate: 0.05s.  Total: 10.37s
[+] /home/carla2024/spack_stack/v1/opt/spack/linux-ubuntu22.04-x86_64_v3/gcc-11.4.0/fftw-3.3.10-bl3rhyjttkxvrvhmjj6tvvovy7hx2273
```

Para los que tengan acceso a un cluster y que aún así hayan añadido el build
cache, si quieren compilar algo pueden intentar `fftw ~~mpi %gcc@11.4.0`.

Para los que solo tienen un laptop y que quieran compilar algo, intenten con
`tmux %gcc@11.4.0`. Probablemente queda instalado en menos de 2 minutos.
