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

Lo ideal, hubiera sido poder usar los PCs de 42 para el hackathon. El problema
que se plantea es que no hay privilegios de root, y que solo tenemos disponible
5gB. Todo ello complita el set up inicial. No obstante, intentaré dar algunos
trucos -si me da tiempo-.

## CRYTO risks

IMPORTANTE: En los proyectos de desarrollo con claves privadas se debe tener
cuidados con el software malicioso.

En esta guia comparto un enlace a la máquina virtual ya configurada; pero, por
lo dicho, es mucho más seguro que sigas esta guía para configurar la tuya.

## Pasos en general

La máquina virtual ya configurada, lista para desarrollar puede descargarse del
enlace de Google Drive de abajo. Solo le falta instalar el IDE de tu
preferencia:
https://drive.google.com/drive/folders/1WZWzLG8VFqFWu2QpfwL-HyCpaFfqvkRU?usp=sharing

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


## 4. Wasm-ld (LLVM)

Para linkear, necesitamos tener el binario `wasm-ld`, que viene con LLVM.

En Ubuntu 24, es suficiente con:

`sudo apt install lld`

Después de la instalación, comprobamos que ya tenemos ese comando disponible:

`wasm-ld`

(Dará un error diciendo que no le hemos pasado archivos a linkear; es correcto)


## 5. Wasm-strip (wabt)

Una vez más, para resolver la dependencia, instalamos con apt:

`sudo apt install wabt`


## 6. Cargo-Stylus

La instalación es muy sencilla -en principio-. Solo hay que ejecutar el
siguiente commando:

`cargo install cargo-stylus`

IMPORTANTE: Cuando se ejecuta dentro de un proyecto, puede instalar para la
versión de Rust que indica la toolchain de ese proyecto. Por ello, es
recomendable ejecutar el comando señalado FUERA del proyecto.

La instalación de los "crates" de Rust suele hacerse en el directorio:

`$HOME/.cargo`


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
`make`


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
