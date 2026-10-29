# Laboratorio de diseño digital para clase: Verilog, VHDL y yosys en una VM de Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml)

Un **laboratorio de diseño digital** listo para usar para enseñar Verilog y VHDL, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde colegios y centros educativos dan a cada estudiante sus propias máquinas y su terminal en el navegador. Cada estudiante recibe una VM de Ubuntu con **Icarus Verilog**, **GHDL** y **yosys**, instalados desde el archivo de Ubuntu. Simula circuitos con un banco de pruebas, lee una tabla de verdad y una **forma de onda** como texto, termina un sumador completo en Verilog y en VHDL y un contador de 4 bits con habilitación y reinicio síncrono, y cuenta las puertas que yosys saca de él. No se instala nada en el ordenador del estudiante y no hace falta ninguna placa.

## Qué hace el estudiante

El laboratorio deja una carpeta `~/hdl` con los archivos de trabajo y un `README.md` en la carpeta personal que explica qué hacer sin dar ninguna respuesta. Tiene cinco partes:

1. Un circuito combinacional en Verilog: leer la tabla de verdad de un semisumador dado y escribir después `full_adder.v`.
2. Un reloj y una forma de onda: un contador dado guarda sus señales en un archivo VCD, que se lee como filas de texto con `vcd2txt` o se dibuja como imagen con `vcd2svg` y se abre en el puerto 8000 del laboratorio.
3. El mismo sumador completo en VHDL con los tres pasos de GHDL, analizar, elaborar y ejecutar: `full_adder.vhd`.
4. Un contador con habilitación y reinicio síncrono, que termina el estudiante: `counter4.v`.
5. Puertas: `hdl gates counter4.v counter4` hace que yosys cuente las celdas que necesita un diseño, lo que muestra que un biestable recuerda un bit y que un latch es un error.

`hdl reset ARCHIVO` devuelve un archivo a como se entregó. Una lección de Basics de la plataforma se trabaja en este laboratorio y deja el sumador completo y el contador al estudiante.

## Cómo se califica

El laboratorio tiene una **comprobación**, un script que califica una VM entregada sin que el docente tenga que leerla. Se ejecuta sobre una copia de la máquina guardada, tras un arranque limpio. Necesita `full_adder.v`, `full_adder.vhd` y `counter4.v` en `~/hdl`, y rechaza un archivo Verilog que llame a una tarea del sistema que termina la simulación o sale de ella (`$finish`, `$stop`, `$fatal`, `$fopen`, `$system` y otras, también en comentarios) y un archivo VHDL que use `textio`, `std.env` o `severity failure`. Después compila cada diseño con **bancos de pruebas que el estudiante nunca ve**. Cada uno contiene un modelo de referencia y lo compara con el diseño, el contador antes y después de cada flanco de reloj, de modo que se detecta un reinicio que no es síncrono. El banco de pruebas imprime una línea de resultado con un número que la comprobación sortea de nuevo en cada ejecución, así que un diseño no puede imprimir por sí mismo la línea correcta. Por último, yosys debe leer los diseños **sin ningún latch**, y el contador necesita biestables y no más celdas que un diseño sencillo. La primera línea que imprime dice qué no está bien.

Lo que no hace: no lee las formas de onda ni las respuestas a las preguntas del README, no coloca ni enruta nada para una placa, y un estudiante con root en su propia VM aún podría manipular los simuladores. Es una comprobación para la clase, no una prueba.

## Qué contiene el archivo

`hdl-lab.lab.yaml` es una **definición de laboratorio de VM** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la VM de cada estudiante: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: 8000, que muestra las imágenes de formas de onda que dibuja el laboratorio; todo lo demás es la terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la VM de cada estudiante: instala `iverilog`, `ghdl` y `yosys` desde el archivo de Ubuntu (unos minutos), escribe los comandos de ayuda `hdl`, `vcd2txt` y `vcd2svg`, los archivos de trabajo, el README y una unidad de systemd que sirve las imágenes |
| `check` | el script de calificación, que se ejecuta como root sobre una copia del disco guardado tras un arranque limpio; su estado de salida es el resultado y `0` aprueba |

## Usarlo en la plataforma

**Para ponerlo como tarea a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de VM Ubuntu**.
2. Pulsa el ejemplo de Diseño digital, o pega el contenido de `hdl-lab.lab.yaml`. Usar un ejemplo reemplaza el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada estudiante tiene su propia VM, que se conserva entre sesiones.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un estudiante en la lista de la clase: arranca una copia de su máquina guardada que no se guarda de vuelta, y muestra si aprobó y qué imprimió. Al estudiante no se le dice el resultado.

La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-hdl-lab) en el manual, su [guía de laboratorios de VM Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cuentan el resto.

¿Aún no tienes cuenta de centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) hasta 50 estudiantes, y [registrarse](https://mitsurutech.co.uk/es/signup) solo pide una dirección de correo.

## Comprobar el archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es un YAML válido con las claves `cpus`, `memory`, `disk`, `setup` y `check`, y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna VM: eso se hace en las máquinas de la plataforma. La instalación, los comandos de la lección de Basics y la comprobación se ejecutaron en un contenedor Ubuntu 26.04 que hacía de VM, así que conviene probarlo una vez en una VM de estudiante ya arrancada antes de una clase.

## Qué no funciona en un laboratorio

La VM llega al archivo de paquetes de Ubuntu, PyPI, npm y GitHub, y a nada más. Todo lo que necesita este laboratorio sale del archivo de Ubuntu. Una VM de terminal no tiene escritorio, así que no hay visor gráfico de formas de onda: se leen como texto o como imagen en el puerto 8000. Ubuntu 26.04 trae GHDL con el backend de GCC, por lo que `ghdl -e` escribe un ejecutable junto a los archivos.

## Acerca de

Hecho por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para colegios y centros educativos, con laboratorios de contenedores, laboratorios de VM, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) y [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

[Icarus Verilog](https://steveicarus.github.io/iverilog/) (GPL-2.0 o posterior), [GHDL](https://github.com/ghdl/ghdl) (GPL-2.0 o posterior) y [yosys](https://yosyshq.net/yosys/) (ISC) se instalan desde el archivo de Ubuntu cuando arranca una VM; aquí no se distribuye nada de ellos.
