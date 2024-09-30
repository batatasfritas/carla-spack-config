# Añadiendo opciones a la configuración

Spack se toma muchas libertades y a veces hay que imponerle restricciones.

## Restringiendo el target de compilación
Un ejemplo de eso viene de la concretización de tmux (o fftw) que propusimos
como compilación al final del paso anterior.

```bash
spack spec tmux
Concretized
--------------------------------
 -  tmux@3.4%gcc@11.4.0~static~utf8proc build_system=autotools arch=linux-ubuntu22.04-skylake
 -      ^bison@3.8.2%gcc@11.4.0~color build_system=autotools arch=linux-ubuntu22.04-x86_64_v3
[+]          ^m4@1.4.19%gcc@11.4.0+sigsegv build_system=autotools patches=9dc5fbd,bfdffa7 arch=linux-ubuntu22.04-x86_64_v3
[+]              ^diffutils@3.10%gcc@11.4.0 build_system=autotools arch=linux-ubuntu22.04-x86_64_v3
[+]                  ^libiconv@1.17%gcc@11.4.0 build_system=autotools libs=shared,static arch=linux-ubuntu22.04-x86_64_v3
...
```

A `bison`, que esta en la build cache, Spack lo propone en versión x86_64_v3.
Pero como no hay tmux en el build cache, Spack se propuso a compilarlo
especificamente para la arquitectura del procesador (skylake en nuestro caso).
No hay ningun problema con eso, pero para sermos consistentes con lo que hay
en la build cache, vamos a imponerle una restricción a Spack. En la carpeta
`spack-config` vamos a crear un archivo `packages.yaml` con el contenido:

```yaml
packages:
  all:
    target: ['x86_64_v3']
```

El mismo `spack spec` de arriba tendría ahora una
`arch=linux-ubuntu22.04-x86_64_v3`. Escogiendo los targets permite que, en un
ambiente con variadas arquitecturas, instalar las mismas cosas desde un solo
servidor, por ejemplo. También permite garantizar que Spack no mezcla
arquitecturas diferentes, si se quiere mantener una uniformidad.

## Restringiendo los compiladores

Spack puede instalar automaticamente compiladores que no estén instalados
todavía. Eso permitiria instalar un software con un compilador diferente
sin tener que hacer `spack install gcc@13.2.0 %gcc@11.4.0`, seguido de un
`spack compiler find`. Pero, un error significaria instalar un nuevo compilador
sin querer.

Para evitarnos problemas, añadiremos `install_missing_compilers: false` al
archivo `config.yaml` que creamos en el paso anterior.

## Carpeta para los ambientes Spack

Aprovechemos que tenemos al `config.yaml` para definir una carpeta para los
ambiente Spack. Normalmente se crean adentro de la carpeta de spack, pero
las pondremos junto con lo demás para que no se pierda (y para mostrar que
también eso se puede aislar de la instalación de spack).
Añadiremos una linea `environments_root` para ponerlos en una carpeta
diferente del default.

Al final de todo esto, tendremos un archivo con el contenido:

```yaml
config:
  install_missing_compilers: false
  install_tree:
    root: /home/carla2024/spack_stack/v1/opt/spack
  environments_root: /home/carla2024/spack_stack/v1/environments
```

Si creamos ahora un ambiente spack con el nombre test, tendremos esto como
respuesta:

```bash
spack env create test
==> Created environment test in: /home/carla2024/spack_stack/v1/environments/test
==> Activate with: spack env activate test
```
