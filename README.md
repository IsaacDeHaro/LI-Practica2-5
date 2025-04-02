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

## Practica 3: Programa en ARM64 que solicita un número N y calcula la suma de los primeros N números naturales.
### Codigo:
```
// Autor: DE HARO RAMIREZ ISAAC
// Fecha: 2025-04-02
// Descripción: Programa en ARM64 que solicita un número N y calcula la suma de los primeros N números naturales.

.global _start

.section .data
    mensaje_pedir:  .asciz "Ingresa un número N: "
    mensaje_suma:   .asciz "La suma de los primeros N números es: "
    nueva_linea:    .asciz "\n"

.section .bss
    num: .skip 10   // Buffer para almacenar el número ingresado
    resultado: .skip 10 // Buffer para almacenar la suma convertida a ASCII

.section .text
_start:
    // Mostrar mensaje para pedir el número
    mov x0, 1                  // stdout
    ldr x1, =mensaje_pedir      // Dirección del mensaje
    mov x2, 20                 // Tamaño del mensaje
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

    // Calcular suma de los primeros N números naturales
    mov x4, 0                  // x4 almacenará la suma
    mov x5, 1                  // Contador desde 1 hasta N
suma_loop:
    cmp x5, x3                 // ¿x5 > N?
    bgt mostrar_resultado      // Si sí, terminamos
    add x4, x4, x5             // Suma actualizada
    add x5, x5, 1              // Incrementar contador
    b suma_loop                // Repetimos el bucle

mostrar_resultado:
    // Mostrar mensaje de resultado
    mov x0, 1
    ldr x1, =mensaje_suma
    mov x2, 34
    mov x8, 64
    svc 0

    // Convertir resultado a ASCII y mostrarlo
    mov x0, x4                 // Pasamos el resultado a x0
    ldr x1, =resultado         // Dirección del buffer de resultado
    bl int_to_str              // Convertimos el número a ASCII

    mov x0, 1
    ldr x1, =resultado
    mov x2, 10                 // Tamaño máximo del buffer
    mov x8, 64
    svc 0

    // Nueva línea
    mov x0, 1
    ldr x1, =nueva_linea
    mov x2, 1
    mov x8, 64
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

// -------------------------
// Función: int_to_str
// Convierte un número entero a una cadena de caracteres ASCII
// -------------------------
int_to_str:
    mov x2, 10                 // Base decimal
    add x1, x1, 9              // Posicionamos el puntero al final del buffer
    mov w3, 0x0A               // Código ASCII de nueva línea
    strb w3, [x1], -1          // Guardamos '\n' y retrocedemos

convert_loop:
    udiv x4, x0, x2            // x4 = x0 / 10
    msub x5, x4, x2, x0        // x5 = x0 - (x4 * 10) (Residuo)
    add x5, x5, '0'            // Convertir a ASCII
    strb w5, [x1], -1          // Guardar carácter en buffer y retroceder
    mov x0, x4                 // Actualizar x0 con el cociente
    cbz x0, conversion_done    // Si x0 == 0, terminamos
    b convert_loop             // Si no, seguimos dividiendo

conversion_done:
    ret
```

## Practica 4: Programa en ARM64 que solicita un número N y calcula su factorial.
### Codigo:
```
// Autor: DE HARO RAMIREZ ISAAC
// Fecha: 2025-04-02
// Descripción: Programa en ARM64 que solicita un número N y calcula su factorial.

.global _start

.section .data
    mensaje_pedir:  .asciz "Ingresa un número N: " // Ahora tiene ":" y un espacio
    mensaje_fact:   .asciz "El factorial de N es: "
    nueva_linea:    .asciz "\n"

.section .bss
    num: .skip 10   // Buffer para almacenar el número ingresado
    resultado: .skip 20 // Buffer para almacenar el resultado en ASCII

.section .text
_start:
    // Mostrar mensaje de solicitud con ": " al final
    mov x0, 1                  
    ldr x1, =mensaje_pedir     
    mov x2, 22                 
    mov x8, 64                 
    svc 0

    // Leer número del usuario
    mov x0, 0                  
    ldr x1, =num               
    mov x2, 10                 
    mov x8, 63                 
    svc 0

    // Convertir cadena a número
    ldr x1, =num               
    bl str_to_int              
    mov x3, x0                 // Guardamos el número en x3

    // Calcular factorial
    mov x4, 1                  // Inicializamos factorial en 1
    mov x5, x3                 // x5 será el contador (desde N hasta 1)

factorial_loop:
    cmp x5, 1                  // ¿x5 <= 1?
    ble mostrar_resultado      // Si sí, terminamos
    mul x4, x4, x5             // Multiplicamos factorial *= x5
    sub x5, x5, 1              // Decrementamos el contador
    b factorial_loop           // Repetimos el bucle

mostrar_resultado:
    // Mostrar mensaje de resultado
    mov x0, 1
    ldr x1, =mensaje_fact
    mov x2, 21
    mov x8, 64
    svc 0

    // Convertir resultado a ASCII y mostrarlo
    mov x0, x4                 
    ldr x1, =resultado         
    bl int_to_str              

    mov x0, 1
    ldr x1, =resultado
    mov x2, 20                 
    mov x8, 64
    svc 0

    // Nueva línea
    mov x0, 1
    ldr x1, =nueva_linea
    mov x2, 1
    mov x8, 64
    svc 0

exit:
    mov x8, 93                 
    mov x0, 0
    svc 0

// -------------------------
// Función: str_to_int (Convierte cadena ASCII a número)
// -------------------------
str_to_int:
    mov x0, 0                  
    mov x2, 10                 

loop:
    ldrb w3, [x1], 1           
    cmp w3, '0'                
    blt done                   
    cmp w3, '9'                
    bgt done                   
    sub w3, w3, '0'            
    mul x0, x0, x2             
    add x0, x0, x3             
    b loop                     

done:
    ret

// -------------------------
// Función: int_to_str (Convierte entero a ASCII)
// -------------------------
int_to_str:
    mov x2, 10                 
    add x1, x1, 19             
    mov w3, 0x0A               
    strb w3, [x1], -1          

convert_loop:
    udiv x4, x0, x2            
    msub x5, x4, x2, x0        
    add x5, x5, '0'            
    strb w5, [x1], -1          
    mov x0, x4                 
    cbz x0, conversion_done    
    b convert_loop             

conversion_done:
    ret
```

## Practica 5:
### Codigo:

## Asccinema:
