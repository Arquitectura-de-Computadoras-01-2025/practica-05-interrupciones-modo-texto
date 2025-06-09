summary: Laboratorio 05 - Interrupciones de Modo Texto
id: laboratorio-05-arquitectura
categories: Ensamblador, Direccionamiento, Thinkercad, Laboratorio
status: Published
authors: Ing. Gabriela Reynosa, Erika Paz, Kevin Escobar

# Laboratorio 05: Interrupciones de modo texto

---

## Objetivos

1. **Comprender el uso de interrupciones del BIOS para controlar el modo de texto:** Aprender a activar y manejar el modo de texto en una aplicación DOS mediante interrupciones específicas del BIOS.
2. **Gestionar la visualización y navegación entre diferentes páginas de texto:** Practicar cómo cambiar entre múltiples páginas de texto utilizando interrupciones del BIOS para mostrar diferentes mensajes en distintas etapas del programa.

---

## Introducción a las Interrupciones

Las interrupciones son un mecanismo fundamental en la arquitectura de las computadoras, diseñadas para mejorar la eficiencia y la capacidad de respuesta del sistema. En términos simples, una interrupción es una señal que recibe el procesador para indicar que un evento o una acción debe ser tratada de inmediato, suspendiendo temporalmente el flujo de trabajo actual del programa principal para ejecutar una rutina de servicio de interrupción (ISR).

### Propósito de las Interrupciones

El propósito principal de las interrupciones es permitir que el procesador responda a eventos tanto internos como externos de manera rápida y eficiente. Estos eventos pueden incluir la finalización de una operación de entrada/salida, un error de hardware, o una solicitud de atención inmediata por parte del software.

### Tipos de Interrupciones

Las interrupciones se clasifican generalmente en dos categorías:

1. **Interrupciones de Hardware**: Son generadas por el hardware externo, como periféricos, para comunicar que necesitan atención. Un ejemplo común es la interrupción generada por un teclado cuando se presiona una tecla.
2. **Interrupciones de Software**: También conocidas como excepciones, son generadas por el software, usualmente por el sistema operativo, para gestionar situaciones especiales como errores de división por cero, accesos inválidos a la memoria, o solicitudes de servicios del sistema operativo a través de llamadas al sistema.

### Flujo de una Interrupción

Cuando se genera una interrupción, el flujo típico de procesamiento es el siguiente:

1. El procesador suspende la ejecución del programa actual.
2. Se guarda el estado actual del programa (por ejemplo, los registros) para asegurar que puede reanudarse después sin pérdida de información.
3. Se ejecuta una rutina de servicio de interrupción específica, que está diseñada para tratar el tipo de interrupción que se ha producido.
4. Una vez completada la rutina, el estado del programa se restaura y la ejecución del programa principal se reanuda como si nunca hubiera sido interrumpida.

### Importancia en la Programación en Ensamblador

En la programación en ensamblador, las interrupciones son especialmente relevantes porque permiten a los programadores gestionar directamente y con gran detalle cómo y cuándo el procesador debe responder a distintos tipos de solicitudes y eventos, otorgando un control preciso sobre la ejecución del hardware.

Esta introducción establece una base para entender cómo las interrupciones en modo texto utilizan estos principios para operar eficientemente dentro de un sistema computacional. A continuación, profundizaremos en las interrupciones específicas en modo texto y su aplicación práctica.

---

## Interrupciones en modo texto

### ➡️ INT 10h / 00h – Establecer modo texto o video

Inicia el modo texto o video, dependiendo del argumento que se indique en el registro AL.

- **AH:** 00h. Selecciona la función para establecer el modo texto o video.
- **AL:** Define el modo específico (00h para modo texto de 40x25, 03h para 80x25, 13h para modo gráfico).

```nasm
MOV AH, 00h
MOV AL, 03h       ; Modo texto 80x25
INT 10h           ; Ejecutar interrupción
```

### ➡️ INT 10h / 01h – Establecer forma del cursor

Modifica la apariencia del cursor en pantalla, estableciendo sus límites superior e inferior.

- **AH:** 01h. Función para definir la forma del cursor.
- **CH:** Parte superior del cursor. Define la línea de inicio.
- **CL:** Parte inferior del cursor. Define la línea final.

```nasm
MOV AH, 01h
MOV CH, 0         ; Línea de inicio del cursor
MOV CL, 7         ; Línea final del cursor
INT 10h           ; Ejecutar interrupción
```

### ➡️ INT 10h / 02h – Establecer posición del cursor

Mueve el cursor a la posición especificada por las coordenadas de fila (DH) y columna (DL).

- **AH:** 02h. Función para mover el cursor.
- **BH:** Número de página (usualmente 0, pero se puede usar hasta la página 7).
- **DH:** Fila deseada.
- **DL:** Columna deseada.

```nasm
MOV AH, 02h
MOV BH, 0
MOV DH, 10        ; Fila
MOV DL, 20        ; Columna
INT 10h           ; Ejecutar interrupción
```

### ➡️ INT 10h / 03h – Leer posición actual del cursor

Obtiene la posición actual del cursor en términos de fila y columna.

**Argumentos de entrada:**

- **AH:** 03h. Función para obtener la posición actual del cursor.
- **BH:** Número de página (usualmente 0).

**Argumentos de salida:**

- **DH:** Fila
- **DL:** Columna

```nasm
MOV AH, 03h
MOV BH, 0
INT 10h           ; Ejecutar interrupción
; Los valores de posición se retornarán en DH (fila) y DL (columna)
```

### ➡️ INT 10h / 05h – Establecer página activa

Cambia la página visual actual en la pantalla de modo texto.

- **AH:** 05h. Selecciona la página visual en pantalla.
- **AL:** Número de página.

```nasm
MOV AH, 05h
MOV AL, 0         ; Número de página
INT 10h           ; Ejecutar interrupción
```

### ➡️ INT 10h / 08h – Leer carácter y su atributo

Lee el carácter y su atributo en la posición actual del cursor.

**Argumentos de entrada:**

- **AH:** 08h. Lee el carácter y su atributo en la posición actual del cursor.
- **BH:** Número de página (usualmente 0, pero se puede usar hasta la página 7).

**Argumentos de salida:**

- **AH:** Atributo leído
- **AL:** Carácter leído

```nasm
MOV AH, 08h
MOV BH, 0
INT 10h           ; Ejecutar interrupción
; El carácter leído se retorna en AL y el atributo en AH
```

### ➡️ INT 10h / 09h – Escribir carácter y su atributo

Escribe un carácter con un atributo de color específico en la posición actual del cursor, repitiéndolo según se especifique.

El “atributo” de un carácter es un byte que usan las instrucciones de BIOS para determinar el color del fondo y el frente del carácter que se desea mostrar en pantalla. Este byte se divide en dos partes, desde el bit 0 hasta el 3 para definir el color del frente y desde el bit 4 hasta el 7 para el del fondo. Este byte se pone en un registro antes de llamar la interrupción, por lo general es en BL. De la siguiente manera:

![Colors](./images/colors.png)

Esta configuración de IRGB, se refiere a Intensidad, Rojo, Verde y Azul, puede representar 16 colores de la siguiente manera:

![Untitled](./images/colors-table.png)

Esta interrupción se utiliza de la siguiente manera:

- **AH:** 09h. Escribe un carácter y su atributo en la posición actual del cursor.
- **AL:** Código ASCII del carácter.
- **BH:** Número de página (0 hasta 7).
- **BL:** Atributo del carácter.
- **CX:** Número de veces que se repetirá el carácter.

```nasm
MOV AH, 09h
MOV AL, 'A'
MOV BH, 0
MOV BL, 07h       ; Atributo (color)
MOV CX, 1         ; Repetir una vez
INT 10h           ; Ejecutar interrupción
```

### ➡️ INT 16h / 00h – Leer carácter desde el teclado

Retorna el siguiente carácter en el buffer del teclado; Si no hay uno disponible, espera a que
aparezca uno.

**Argumentos de entrada:**

- **AH**: 00h. Indica que se desea leer el carácter.

**Argumentos de salida:**

- **AH**: BIOS scan code de la tecla presionada.
- **AL**: Carácter ASCII correspondiente a la tecla presionada.

```nasm
MOV AH, 00h         ; Establece la función para leer el carácter
INT 16h             ; Llama a la interrupción del teclado
; AH ahora contiene el código de escaneo del BIOS
; AL ahora contiene el carácter ASCII leído
```

### ➡️ INT 21h / 09h – Escribir cadena de caracteres en la consola

Escribe una cadena de caracteres terminada en '$' en la consola de MS-DOS.

- **AH:** 09h. Función para escribir una cadena de texto en la consola.
- **DX:** Dirección de la memoria donde inicia la cadena de texto (debe terminar con '$').

```nasm
; Definir la cadena de caracteres que será escrita en consola
cadena DB 'Hola mundo$'  ; Cadena a escribir, terminada con '$'

; Cargar los valores en los registros para la interrupción
MOV AH, 09h
MOV DX, cadena        ; Carga la dirección de inicio de la cadena en el registro DX
INT 21h               ; Ejecutar interrupción
```

---

## Ejemplo práctico

Este programa en Assembler, diseñado para ejecutarse en un entorno DOS, demuestra cómo comparar un número predefinido con el valor 5 y visualizar mensajes apropiados en el modo texto según el resultado de la comparación (mayor, menor o igual). El ejercicio involucra inicializar el modo de texto, posicionar el cursor, gestionar la visualización de mensajes en diferentes páginas de texto, y esperar la interacción del usuario mediante teclas. Este enfoque ayuda a los estudiantes a entender la manipulación de la pantalla y la entrada desde el teclado en la programación a nivel de sistema.

### 1. Configuración Inicial y Declaración de Datos:

```nasm
org   100h

section .data
msgMayor db 'El digito es mayor que 5$'
msgMenor db 'El digito es menor que 5$'
msgIgual db 'El digito es igual a 5$'
msgFin db 'Fin del programa$'
```

- **Directiva `org 100h`**: Define el punto de inicio del programa en la memoria, específicamente para un archivo COM en DOS.
- **Sección `.data`**: Declara los mensajes que se mostrarán basados en el resultado de la comparación del número con 5, y al final del programa.

### 2. Flujo Principal del Programa:

```nasm
section .text

main:
    CALL IniciarModoTexto
    
    MOV BH, 0d ; Establece la página 0
    CALL CentrarCursor
    
    CALL CompararNumero
    
    CALL EsperarTecla
    
    CALL CambiarPagina
    
    MOV BH, 1d ; Cambia a la página 1
    CALL CentrarCursor
    
    CALL ImprimirFin
    
    CALL EsperarTecla
    
    INT 20h
```

- **`IniciarModoTexto`**: Configura el modo de texto 80x25.
- **`CentrarCursor`**: Posiciona el cursor en el centro de la pantalla.
- **`CompararNumero`**: Realiza la comparación del número almacenado en `AL` con 5.
- **`EsperarTecla` y `CambiarPagina`**: Espera la presión de una tecla y cambia la página del modo texto para mostrar nuevos mensajes.
- **`ImprimirFin`**: Muestra el mensaje de finalización del programa.

### 3. Subrutinas Detalladas:

- **IniciarModoTexto**:
    
    ```nasm
    IniciarModoTexto:
        MOV AH, 0h
        MOV AL, 03h ; Establece modo texto 80x25
        int 10h ; Llama a la interrupción del BIOS para cambiar el modo texto
        RET
    ```
    
    Configura el modo de texto con 80 columnas y 25 filas.
    
- **CentrarCursor**:
    
    ```nasm
    CentrarCursor:
        MOV AH, 02h
        MOV DH, 10 ; Fila central
        MOV DL, 25 ; Columna central
        int 10h ; Llama a la interrupción del BIOS para posicionar el cursor
        RET
    ```
    
    Posiciona el cursor en la fila 10, columna 25, aproximadamente en el centro.
    
- **CambiarPagina**:
    
    ```nasm
    CambiarPagina:
        MOV AH, 05h
        MOV AL, 01H ; Cambia a la página 1
        INT 10h
        RET
    ```
    
    Cambia a la página 1 del modo texto para mostrar mensajes adicionales.
    
- **CompararNumero**:
    
    ```nasm
    CompararNumero:
        MOV AL, 5d
        CMP AL, 5
        JA mayor
        JB menor
        JE igual
        RET
    ```
    
    Carga el número 5 en `AL`, compara con 5, y salta a las etiquetas correspondientes según si es mayor, menor o igual.
    
- **EsperarTecla**:
    
    ```nasm
    EsperarTecla:
        MOV AH, 00h
        INT 16h
        RET
    ```
    
    Espera una tecla, utilizando la interrupción del teclado `INT 16h`.
    
- **Manejo de Resultados de Comparación (`mayor`, `menor`, `igual`)**:
Utiliza `INT 21h` con `AH = 09h` para mostrar mensajes según el resultado de la comparación.
    
    ```nasm
    mayor:
        MOV AH, 09h
        MOV DX, msgMayor
        INT 21h
        RET
    menor:
        MOV AH, 09h
        MOV DX, msgMenor
        INT 21h
        RET
    igual:
        MOV AH, 09h
        MOV DX, msgIgual
        INT 21h
        RET
    ```
    
- **ImprimirFin**:
    
    ```nasm
    ImprimirFin:
        MOV AH, 09h
        MOV DX, msgFin
        INT 21h
        RET
    ```
    
    Muestra "Fin del programa" en la página 1.
    

<aside>
💡 Verificar el funcionamiento del programa cambiando el valor a comparar con un número mayor a 5 y con un número menor a 5.
</aside>

---

## Indicaciones de entrega

- La entrega se realizará a través de GitHub Classroom, en el repositorio asignado para las [prácticas de laboratorio](https://classroom.github.com/a/p3Yq-RKA).
- Crear una carpeta llamada "`Laboratorio-05`" dentro del repositorio. Esta carpeta será el contenedor para los archivos de esta práctica.
- Dentro de la carpeta "`Laboratorio-05`", crear un archivo llamado **`desarrollo.asm`**. En este archivo, colocar todos los ejemplos y ejercicios desarrollados durante la práctica de laboratorio.
- Crear un segundo archivo llamado **`tarea.asm`** dentro de la carpeta "`Laboratorio-05`". Este archivo debe contener la solución a la tarea propuesta.

```
└── Laboratorio-05
    ├── desarrollo.asm
    └── tarea.asm
```

- Realizar dos commits separados:
    - **Primer Commit:** Subir el archivo **`desarrollo.asm`** una vez completado el desarrollo durante la práctica.
    - **Segundo Commit:** Subir el archivo **`tarea.asm`** una vez completada la tarea propuesta.
- Copiar el enlace de su repositorio en el entregable llamado “[Nota] Laboratorio 04 - Instrucciones de salto y comparación” correspondiente a la práctica de laboratorio que está colocado en el e-campus.

---

## Rúbrica

| Criterio | Porcentaje |
| --- | --- |
| Desarrollo | 50% |
| Tarea | 40% |
| Ayuda necesitada | 10% |
| **Total** | **100%** |