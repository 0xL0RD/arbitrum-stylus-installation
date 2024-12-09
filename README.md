# arbitrum-stylus-installation

Durante el fin de semana del 5 de diciembre de 2024 hemos hecho en 42 Madrid
por Fundación Teléfonica un hackathon con Arbitrum Stylus.

En 42 estudiamos C y C++ fundamentalmente. Por lo que la idea del hackathon es
acercar a los estudiantes de 42 la posiblidad de hacer contratos inteligentes
en esos lenguajes sobre la L2 Arbitrum.

El fin de semana empezó con unos talleres de introducción: uno para el set up
del entorno de programación, y otro para el desarrollo en Arbitrum Stylus para
el contrato inteligente y React para el frontend.

La diversidad de sistemas operativos y entornos entre los asistentes complica
el set up inicial. Lo cual, por otra parte, es esencial para que los
programadores novicios nos introduzcamos en un mundo nuevo como la blockchain
y smart contracts.

Este proyecto pretende ser una guía inicial "for dummies" para la configuración
inicial de todas las dependencias.

El proceso más sencillo -según mi opinión- es configurar una máquina virtual,
con la última versión de Ubuntu.


## CRYTO risks

IMPORTANTE: En los proyectos de desarrollo con claves privadas se debe tener
cuidados con el software malicioso.

En esta guia comparto un enlace a la máquina virtual ya configurada; pero, por
lo dicho, es mucho más seguro que sigas esta guía para configurar la tuya.


## Ordenadores de 42 Madrid

Lo ideal, hubiera sido poder usar para el hackathon los PCs de 42. El problema
que se plantea es que no hay privilegios de root, y que solo tenemos disponible
5gB. Todo ello complita el set up inicial. No obstante, intentaré dar algunos
trucos -si me da tiempo-.

En la guía hago referencia a posibles problemas. Muchos de ellos tratan de
resolver las limitaciones de configurar el entorno de trabajo en los ordenadores
de 42 Madrid.

No obstante, hay que tener en cuenta que solo disponeblos de 5 gigas, pero
la instalación de Rust (por el toolchain) ocupa más de 1,5 gigas; Stylus ocupa
otro tanto, por lo que es recomendable instalarlos en un USB drive...

También se pueden instalar en el "goinfre", pero como sabemos, es un directorio
temporal y en cualquier momento nos lo pueden borrar.


## Pasos en general

La máquina virtual ya configurada, lista para desarrollar puede descargarse del
enlace de Google Drive de abajo. Solo le falta instalar el IDE de tu
preferencia:
https://drive.google.com/drive/folders/1ODEypUfMFd4YbbvdzSEQL3rbeGkfARTe?usp=sharing

Los pasos para el set up de desarrollo son, en general, los siguientes:

1. Instalación de la máquina virtual de Ubuntu.

2. Docker.

3. Rust, cargo y rustup.

4. Wasm-ld (LLVM)

5. Wasm-strip (wabt)

6. Cargo-Stylus

7. Comprobación

8. NodeJS


## 1. Instalación de la máquina virtual de Ubuntu.

Para la instalación inicial de Ubuntu con VirtualBox solo tienes que seguir
estos pasos (guía oficial):
https://ubuntu.com/tutorials/how-to-run-ubuntu-desktop-on-a-virtual-machine-using-virtualbox#1-overview

Después de toda nueva instalación de un linux, es aconsejable actualizar:

`sudo apt update`

`sudo apt upgrade`

Instalamos `cc` -lo require Stylus-:

`sudo apt install gcc`

## 2. Docker.

Una vez instalada la máquina virtual con Ubuntu, hay que instalar Docker.

Docker no está disponible en los repositorias de Ubuntu, por lo que hay que
añadirlos, siguiendo la guía oficial de Docker, por ejemplo, usando el método
"Install using the apt repository":

https://docs.docker.com/engine/install/ubuntu/

Para comprobar que se ha instalado correctamente, ejecutamos:

` sudo docker run hello-world`


## 3. Rust, cargo y rustup.

La guía oficial para instalar Rust está disponible aquí:
https://www.rust-lang.org/tools/install

Normalmente, es suficiente con ejecutar:

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

Para comprobar que se ha instalado correctamente, en una NUEVA shell (para
que se actualice el PATH), ejecutamos:

`rustc --version`


### Ordendadores de 42

Para instalar Rust en goinfre o en un pendrive, podéis seguir esta guía:
https://github.com/rust-lang/rustup/issues/618

Consiste en setear la variable `CARGO_HOME` con el path donde queremos instalar.


## 4. Wasm-ld (LLVM)

Para linkear, necesitamos tener el binario `wasm-ld`, que viene con LLVM.

En Ubuntu 24, es suficiente con:

`sudo apt install lld`

Después de la instalación, comprobamos que ya tenemos ese comando disponible:

`wasm-ld`

(Dará un error diciendo que no le hemos pasado archivos a linkear; es correcto)

### Ordendadores de 42

En los PCs de 42 no disponemos de wasm-ld. Pero podemos descargar los binarios
del repo oficial:
https://github.com/llvm/llvm-project/releases/tag/llvmorg-19.1.5

Los ".so" (share objects), hay que parsarselos por "LD_LIBRARY_PATH":
https://stackoverflow.com/questions/18547551/linux-so-file-not-found


## 5. Wasm-strip (wabt)

Una vez más, para resolver la dependencia, instalamos con apt:

`sudo apt install wabt`

### Ordendadores de 42

Necesitaremos compilar desde el repo oficial de wabt:
https://github.com/WebAssembly/wabt

Tal y como indica, solo es necesario:
$ git clone --recursive https://github.com/WebAssembly/wabt
$ cd wabt
$ git submodule update --init

$ mkdir build
$ cd build
$ cmake ..
$ cmake --build .


## 6. Cargo-Stylus

La instalación es muy sencilla -en principio-. Solo hay que ejecutar el
siguiente commando:

`cargo install cargo-stylus`

IMPORTANTE: Cuando se ejecuta dentro de un proyecto, puede instalar para la
versión de Rust que indica la toolchain de ese proyecto. Por ello, es
recomendable ejecutar el comando señalado FUERA del proyecto.

La instalación de los "crates" de Rust suele hacerse en el directorio:

`$HOME/.cargo`

La instalación puede dar el siguiente error:

` cargo:warning=Could not find directory of OpenSSL installation, and this `-sys` crate cannot proceed without this knowledge. If OpenSSL is installed and this crate had trouble finding it,  you can set the `OPENSSL_DIR` environment variable for the compilation process. See stderr section below for further information.`

Entonces, instalaremos pkg-config y los headers de OpenSSL:

`sudo apt install pkg-config`

`sudo apt install libssl-dev`

### Ordendadores de 42: USB drive

Si queremos instalar en un path específico (fuera de nuestro home), usarmos
`--root`:

`cargo install cargo-stylus --root /path/to/some/where`

### Ordendadores de 42: OpenSSL

En los PCs de 42 no tenemos los headers de OpenSSL. Para solucionarlo, podemos
modificar el ".toml" del crate de Stylus para que compile él mismo OpenSSL.
Es lo que se conoce como "vendored":
https://forums.freebsd.org/threads/rust-cargo-openssl-vendored-how-to-use-the-ssl-from-the-os.91483/

Consiste en añadir al ".toml" la dependencia:

`openssl = { version = "0.10", features = ["vendored"] }`

En mi PC, el ".toml" se encuentra en:
`/home/MYUSER/.cargo/registry/src/index.crates.io-6f17d22bba15001f/cargo-stylus-0.5.6/Cargo.toml`


## 7. Comprobación

Para comprobar que la instalación de Arbitrum Stylus ha sido correcta podemos
intentar compilar un smart contract, por ejemplo, el de Lemon Tips:
https://github.com/D3Portillo/arbitrum-stylus-starter-c

Los pasos son:

a) Git clone

b) Nos movemos al nuevo directorio (con `cd`)

b) IMPORTANTE: Inicializamos los submodulos:

`git submodule update --init --recursive`

c) Compilamos:

`make`

d) Si da error de `target wasm32 not found`, seteamos:

`cargo stylus check --wasm-file ./contract.wasm -e https://sepolia-rollup.arbitrum.io/rpc`

e) Comprobamos que sale VERDE:

`contract size: xxx B`


## 8. NodeJS

Para desarrollar el frontend, necesitamos `NodeJS`. La guía oficial es:
https://nodejs.org/en/download/package-manager/current

Elejimos: "Install Node.js [xxxxx (Current)] on [Linux] using [nvm]".

Con la versión última de hoy, nos dará los comandos siguientes:

`# installs nvm (Node Version Manager)`

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash`


`# download and install Node.js (you may need to restart the terminal)`

`nvm install 23`


`# verifies the right Node.js version is in the environment`

`node -v # should print 'v23.3.0'`


`# verifies the right npm version is in the environment`
`npm -v # should print '10.9.0'`
