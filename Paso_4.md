# Creando definitions para ahorrarse un montón de redundancia.

Ya hemos simplificado la config con la sección packages que define que blas
tiene que ser openblas y fftw la fuente de los FFT.

Pero seguimos teniendo un %gcc@11.4.0 en cada spec. Podemos hacer mejor.

## Definiciones

Definiciones son arrays de yaml. Permiten simplificar un ambiente. En vez
de cada linea con todos los detalles, una matriz permite definirlo todo con
poca redundancia.

```yaml
  definitions:
    - compilador: ['gcc@11.4.0 target=x86_64_v3']

    - codigos_base:
      - bzip2
      - cmake +ncurses
      - git
      - gzip

  specs:
    - matrix:
      - [$codigos_base]
      - [$%compilador]
```

Un spack concretize con la stack anterior más esta sección añadida
concretiza los 7 programas.

### Definiciones adicionales

No hay limites para lo que se podría hacer con las definiciones. Una opción
seria añadir un nuevo compilador y crear toda una sección basada en eso.

```yaml
  definitions:
    - compilador: ['gcc@11.4.0 target=x86_64_v3']
    - compilador_nuevo: ['gcc@12.3.0 target=x86_64_v3']
    - cn_install: ['gcc@12.3.0 %gcc@11.4.0 target=x86_64_v3']

    - mpi_cn: ['openmpi@5.0.3']

  specs:
    - matrix:
      - [$cn_install]

    - matrix:
      - [$mpi_cn]
      - [$%compilador_nuevo]
```

Se puede instalar esta stack más completa, va a añadir ~150 programas...
