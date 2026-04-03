# Laboratorio: Exploración con DEBUG en DOSBox
## Unidad 3 — Manejo del DEBUG | Ingeniería de Sistemas 
**Estudiante:** Andrea Valentina Rivera Fernandez  
**Repositorio:** rivera-post1-u3

---

## Descripción del laboratorio

El presente laboratorio tiene como objetivo configurar el entorno DOSBox
en el equipo del estudiante, acceder al depurador DEBUG y utilizar los
comandos R, D, F y U para inspeccionar registros, rellenar y volcar bloques
de memoria, y desensamblar código en memoria.

---

## Prerrequisitos utilizados

- DOSBox instalado en macOS
- Carpeta de trabajo creada en: ~/doswork/
- Subcarpeta del laboratorio: LAB3POST1

---

## Parte A — Configuración del entorno

Se montó la carpeta ~/doswork/ como unidad C: virtual en DOSBox mediante
el comando MOUNT. Luego se creó la subcarpeta LAB3POST1 y se inició el
depurador DEBUG desde el prompt de DOS.

Comandos ejecutados:
```
MOUNT C /Users/TU_USUARIO/doswork
C:
MD LAB3POST1
CD LAB3POST1
DEBUG
```

---

## Parte B — Inspección de registros con R

### Checkpoint 1 — CP1_registros.png

El comando R muestra el estado completo del procesador. Al iniciar DEBUG
sin argumentos, los registros AX, BX, CX, DX se encuentran en cero,
SP apunta a 0xFFFE y los cuatro registros de segmento (DS, ES, SS, CS)
apuntan al mismo párrafo de memoria asignado por el DOS para el PSP.
El IP se ubica en 0x0100, que corresponde a la primera dirección ejecutable
tras el PSP.

Se modificó el registro AX cargando el valor 0x1234 usando el comando
R AX, demostrando que la modificación es selectiva y no altera el resto
del estado del procesador.

![Checkpoint 1 - Estado de registros](capturas/CP1_registros.png)

---

## Parte C — Volcado de memoria con D y relleno con F

### Checkpoint 2 — CP2_volcado_memoria.png

El comando D muestra la memoria en tres columnas:
1. La dirección de memoria en formato SEGMENTO:OFFSET (hexadecimal).
2. Los valores de cada byte en hexadecimal, separados en dos grupos de 8.
3. La representación ASCII de esos bytes (se muestra un punto cuando el
   byte no corresponde a un carácter imprimible, es decir, fuera del rango
   0x20–0x7E).

Se rellenaron 64 bytes (0x40) a partir de DS:0200 con el patrón AB CD EF
usando el comando F 200 L40 AB CD EF. El patrón se repite cíclicamente
en las cuatro filas del volcado. La columna ASCII muestra puntos porque
0xAB, 0xCD y 0xEF son valores fuera del rango imprimible.

Al explorar el área del PSP (D 0 L20) se observaron los bytes CD 20 al
inicio, correspondientes a la instrucción INT 20 que el DOS coloca como
mecanismo de terminación controlada.

![Checkpoint 2 - Volcado de memoria](capturas/CP2_volcado_memoria.png)

---

## Parte D — Desensamblado con U

### Checkpoint 3 — CP3_ensamblado_desensamblado.png

Se ensambló un programa de cuatro instrucciones en CS:0100 usando el
comando A 100 y se verificó su correcta codificación con U 100 109.
Se observó la correspondencia directa entre cada mnemónico y sus bytes:

| Instrucción   | Bytes       | Descripción                          |
|---------------|-------------|--------------------------------------|
| MOV AX, 0005  | B8 05 00    | Carga 5 en AX (little-endian)        |
| MOV BX, 0003  | BB 03 00    | Carga 3 en BX (little-endian)        |
| ADD AX, BX    | 03 C3       | Suma BX a AX                         |
| INT 20        | CD 20       | Termina el programa                  |

El programa completo ocupa 10 bytes en memoria. Se comprueba que en el
modo real no existe distinción entre código y datos: el procesador interpreta
como instrucción cualquier byte al que apunte CS:IP.

![Checkpoint 3 - Ensamblado y desensamblado](capturas/CP3_ensamblado_desensamblado.png)

---

## Conclusiones

El laboratorio permitió al estudiante comprender el funcionamiento interno
del procesador x86 en modo real mediante el uso del depurador DEBUG.
Se evidenció cómo los registros almacenan el estado del procesador y cómo
pueden ser inspeccionados y modificados de forma selectiva. El volcado de
memoria reveló la estructura del PSP y la forma en que el DEBUG representa
los datos en hexadecimal y ASCII. El ensamblado y desensamblado de un
programa simple demostró la correspondencia directa entre instrucciones
y bytes de código máquina, así como el principio fundamental de que en
el modo real cualquier byte puede ser interpretado como instrucción.
