# TP3 — Módulos de kernel

### Grupo: Apache Tevez

### Profesores:

- Miguel Angel Solinas

- Javier Jorge

## Integrantes

| Nombre                            | Correo Electrónico                |
| --------------------------------- | --------------------------------- |
| Facundo Emanuel Avila Diaz Moreno | facundo.avila.027@mi.unc.edu.ar   |
| Candela Abigail Vergara           | candela.vergara@mi.unc.edu.ar     |
| Joaquín Alejandro Salinas         | joaquin.salinas.874@mi.unc.edu.ar |

---

## Desafío 1
### ¿Qué es el checkinstall y para qué sirve?
El `checkinstall` es un programa que reemplaza al `make install` y en lugar de instalar los archivos sueltos por el sistema, crea un paquete .deb (o .rpm). De esta manera se puede desinstalar limpiamente después con el gestor de paquetes y tener un registro de qué archivos instaló el programa.

## Desafío #1

### ¿Qué es checkinstall y para qué sirve?
`checkinstall` es una herramienta que reemplaza al `make install` tradicional. 
En vez de copiar archivos sueltos por el sistema (lo cual es difícil de desinstalar 
limpiamente), intercepta la instalación y crea un paquete `.deb` (en Debian/Ubuntu) 
o `.rpm` (en RedHat/Fedora) que luego instala automáticamente.

Esto permite:
- Desinstalar el software limpiamente con el gestor de paquetes (`apt remove` o `dnf remove`)
- Tener un registro de qué archivos instaló el programa
- Distribuir el software empaquetado a otras máquinas

Es especialmente útil cuando se quiere instalar software que no está en los 
repositorios oficiales y se compila desde el código fuente.

### Empaquetando un Hello World con checkinstall

Se creó un programa `hello.c` mínimo y su `Makefile` correspondiente:

**hello.c**
\```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
\```

**Makefile**
\```makefile
all:
    gcc -o hello hello.c

install:
    cp hello /usr/local/bin/hello

clean:
    rm -f hello
\```

Se compiló y empaquetó con checkinstall:
\```bash
make
sudo checkinstall --pkgname=hello --pkgversion=1.0 --nodoc
\```

El resultado fue el paquete `hello_1.0-1_amd64.deb` instalado en el sistema,
verificable con:
\```bash
dpkg -l | grep hello
\```

![](/tp4/Img/1.png)

### Revisar la bibliografía para impulsar acciones que permitan mejorar la seguridad del kernel, concretamente: evitando cargar módulos que no estén firmados. rootkits ?

Un rootkit es un módulo malicioso que se instala en el kernel para tener control total del sistema de forma oculta. Para prevenirlos, Linux implementa la firma de módulos junto con Secure Boot.

El mecanismo funciona así:
- Cada módulo debe estar firmado con una clave privada
- El kernel verifica la firma contra las claves públicas registradas
- Si secure Boot está habilitado y el módulo no tiene firma válida, la carga falla directamente

Cuando un módulo se carga sin firma (como ocurre con mimodulo.ko), el kernel lo indica con:
`module verification failed: signature and/or required key missing - tainting kernel`

La palabra "tainting" indica que el kernel fue "contaminado" con 
código no verificado, lo cual es una señal de advertencia de seguridad.