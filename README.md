# LI-Practica2-5
## Practica 2: Programa en ARM64 que lee un número del usuario y determina si es par o impar.
### Codigo:
```
// Autor: DE HARO RAMIREZ ISAAC
// Fecha: 2025-04-02
// Descripción: Programa en ARM64 que lee un número del usuario y determina si es par o impar.

.global _start

.section .data
    mensaje_pedir:  .asciz "Ingresa un número: "
    mensaje_par:    .asciz "El número es PAR.\n"
    mensaje_impar:  .asciz "El número es IMPAR.\n"

.section .bss
    num: .skip 10   // Buffer para almacenar el número ingresado

.section .text
_start:
    // Mostrar mensaje para pedir el número
    mov x0, 1                  // stdout
    ldr x1, =mensaje_pedir      // Dirección del mensaje
    mov x2, 18                 // Tamaño del mensaje
    mov x8, 64                 // syscall write
    svc 0

    // Leer número del usuario
    mov x0, 0                  // stdin
    ldr x1, =num               // Buffer donde se almacenará el input
    mov x2, 10                 // Tamaño máximo a leer
    mov x8, 63                 // syscall read
    svc 0

    // Convertir cadena a número
    ldr x1, =num               // Dirección del buffer
    bl str_to_int              // Llamada a la función de conversión
    mov x3, x0                 // Guardamos el número en x3

    // Determinar si es par o impar
    and x4, x3, 1              // Verifica el bit menos significativo (par = 0, impar = 1)
    cbz x4, es_par             // Si x4 es 0, salta a es_par
    b es_impar                 // Si no, salta a es_impar

es_par:
    mov x0, 1                  // stdout
    ldr x1, =mensaje_par       // Mensaje de número par
    mov x2, 17                 // Tamaño del mensaje
    mov x8, 64                 // syscall write
    svc 0
    b exit

es_impar:
    mov x0, 1                  // stdout
    ldr x1, =mensaje_impar     // Mensaje de número impar
    mov x2, 19                 // Tamaño del mensaje
    mov x8, 64                 // syscall write
    svc 0

exit:
    mov x8, 93                 // syscall exit
    mov x0, 0
    svc 0

// -------------------------
// Función: str_to_int
// Convierte una cadena de caracteres ASCII en un número entero
// -------------------------
str_to_int:
    mov x0, 0                  // Inicializamos el resultado en 0
    mov x2, 10                 // Factor de multiplicación (base 10)
loop:
    ldrb w3, [x1], 1           // Cargar byte y avanzar el puntero
    cmp w3, '0'                // Si el caracter es menor que '0'
    blt done                   // Salimos del bucle
    cmp w3, '9'                // Si el caracter es mayor que '9'
    bgt done                   // Salimos del bucle
    sub w3, w3, '0'            // Convertimos de ASCII a valor numérico
    mul x0, x0, x2             // Multiplicamos el resultado actual por 10
    add x0, x0, x3             // Sumamos el nuevo dígito
    b loop                     // Repetimos el bucle
done:
    ret
```
