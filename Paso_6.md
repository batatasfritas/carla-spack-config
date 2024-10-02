# Los modulos de software

Ya hemos instalado varios paquetes, pero hasta ahora no tenemos como acceder a
los programas. Para eso utilizaremos modulos lmod, que ya instalamos en un paso
anterior.

Creamos un archivo modules.yaml en el `$SPACK_SYSTEM_CONFIG_PATH` con el
contenido (remplazar el user si necesario):

```yaml
modules:
  default:
    enable: [lmod]
    roots:
      lmod: /home/carla2024/spack_stack/v1/modulos
    lmod:
      hash_length: 0
 
      core_compilers:
        - gcc@11.4.0
 
      all:
        suffixes:
          '+mpi': mpi
          '+openmp': openmp
 
      hide_implicits: true
 
      exclude:
        - lmod
```

Una vez hecho, lanzamos el comando `spack module lmod refresh` que se propone
a crear los módulos. La opción `hide_implicits` permite evitar que muchos de
los programas que són dependencias sin interés para el usuario aparezcan al
final.

Este comando creó unos módulos que todavía no están visibles. Para eso
necesitamos lanzar dos comandos más:
```bash
source $(spack location -i lmod)/lmod/lmod/init/bash
module use /home/$USER/spack_stack/v1/modulos/linux-ubuntu22.04-x86_64/Core
```

Ahí, un `module av` ya nos muestra los varios módulos que instalamos.
Spack crea un árbol de módulos y las cosas compiladas con soporte MPI no
aparecen sino después de activar el módulo MPI correpondente. Si hacemos
`module load mpich/4.0.3` ya veremos a Gromacs.

## Cosas a no hacer
Aquí hicimos instalaciones directas como `spack install tmux` y otras en el
ambiente. Si miran, el módulo tmux está presente, aún que no lo añadieramos
al ambiente. No mezclar una instalación de prueba, con la de producción para
evitarse estos problemas.

Aqui, por limitaciones de la buildcache, tenemos varias versiones de python,
mpich, etc. Aún que eso no sea un problema para una dependencia como libxml2,
o libffi, para los programas para los cuales queremos módulos, conviene
minimizar la complejidad. Spack no deja activar dos módulos de mpich al
tiempo, lo que impediria ciertas combinaciones de programas. Eso se consigue
jugando con los `require`, `prefer`, o añadiendo dependencias especificas.
Pero eso implica crear una configuración que no existe en el buildcache y
compilaciones demasiado largas para esta introducción...
