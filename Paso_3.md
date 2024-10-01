# Crear un ambiente

spack env create carla2024-env
vi /home/carla2024/spack_stack/v1/environments/carla2024-env/spack.yaml

Remplazar:
- view: true
- unify: true
=>

view: false
unify: false

## Añadiendo una primera spec
spec:
  - perl %gcc@11.4.0

spack -e carla2024-env concretize

Como ya habiamos instalado fftw, ya está todo instalado para Perl

## Añadiendo una segunda spec
spec:
  - gromacs %gcc@11.4.0
  - perl %gcc@11.4.0

spack -e carla2024-env concretize

Añade una spec nueva. Una vez que está hecho, podemos
spack -e carla2024-env install

## Se puede dejar un rato y cancelarla después que vean que todo
## anda

## Añadiendo py-pandas
  - py-pandas@1.5.3 %gcc@11.4.0

spack -e carla2024-env concretize

Escogimos una versión más antigua para evitar una dependencia de pandas 2.
El paquete llvm en la cache Spack tiene 2 GB...

## Uniformizando la estructura

Ya tenemos un OpenBlas, mantengamos ese en todo el ambiente.

Hay que editar el ambiente Spack y añadir sección packages
```yaml
  packages:
    blas:
      require: ['openblas']

    fftw-api:
      require: ['fftw']
```

Una vez hecho, se puede lanzar un concretize. Como ya teníamos una
concretización, en realidad spack no hace nada.
