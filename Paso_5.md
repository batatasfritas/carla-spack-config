# Añadiendo más secciones

Para una buena gestión del ambiente mejor añadir más secciones con matrizes.
En nuestra instalación organizamos instalaciones por bibliotecas matematicas
o python, para minimizar instalaciones diferentes del mismo paquete.

En las definiciones añadiremos:

```yaml
    - blas_gcc: ['openblas@0.3.26']
    - mpi_gcc: ['mpich@4.0.3']
    - python_gcc: ['python@3.11.7']

    - codigos_python:
      - py-pip
      - py-urllib3

    - codigos_python_blas:
      - py-numpy
      - py-pandas@1.5.3

    - codigos_mpi:
      - fftw ++mpi
      - scotch ++mpi

    - codigos_mpi_blas_python:
      - cp2k ++mpi
      - gromacs ++mpi
```

en la sección de packages añadiremos:

```yaml
    fftw:
      prefer: ['+mpi', '+openmp']
      
    openblas:
      prefer: ['threads=openmp']
```

para darle preferencia a ciertas opciones y en las specs las matrices
correspondientes:

```yaml
    - matrix:
      - [$codigos_python]
      - [$^python_gcc]
      - [$%compilador]

    - matrix:
      - [$codigos_python_blas]
      - [$^blas_gcc]
      - [$^python_gcc]
      - [$%compilador]

    - matrix:
      - [$codigos_mpi]
      - [$^mpi_gcc]
      - [$%compilador]

    - matrix:
      - [$codigos_mpi_blas_python]
      - [$^blas_gcc]
      - [$^python_gcc]
      - [$^mpi_gcc]
      - [$%compilador]
```

Si borramos las dos specs (de gromacs y py-pandas) que teniamos declaradas
directamente y que ya estan en sus matrices, nos queda ahi un perl que podemos
poner en los codigos_base. Añadimos también python en los codigos base, para
tenerlo explicitamente en la lista.

Hora de concretizar nuestra stack...
