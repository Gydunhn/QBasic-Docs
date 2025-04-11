# Instrucciones Básicas

Las instrucciones básicas de QBasic son los comandos fundamentales que permiten realizar operaciones esenciales como entrada/salida, asignación de variables y control del flujo de ejecución.

## Entrada y Salida

### PRINT

La instrucción `PRINT` muestra información en la pantalla:

```qbasic
PRINT "Hola Mundo"            ' Muestra texto
PRINT variable                ' Muestra el valor de una variable
PRINT "Valor:"; variable      ' Muestra texto y variable juntos
PRINT "A"; "B"; "C"           ' Usa punto y coma para juntar sin espacios
PRINT "X", "Y", "Z"           ' Usa coma para separar en columnas
PRINT TAB(10); "Tabulado"     ' Tabula a la posición 10
PRINT SPC(5); "Espaciado"     ' Añade 5 espacios
```

Para imprimir sin salto de línea al final, termina con punto y coma:

```qbasic
PRINT "Esto no salta línea";
PRINT " y continúa aquí"
```

### INPUT

La instrucción `INPUT` solicita datos al usuario:

```qbasic
INPUT "¿Cuál es tu nombre? ", nombre$
INPUT "Introduce tu edad: ", edad%
```

Para evitar que aparezca el signo de interrogación:

```qbasic
INPUT; "Introduce un valor: ", valor
```

### LINE INPUT

Para leer una línea completa incluyendo comas y otros caracteres especiales:

```qbasic
LINE INPUT "Introduce una frase: ", frase$
```

## Asignación de Variables

La asignación de valores a variables se realiza con el operador `=`:

```qbasic
nombre$ = "Juan"
edad% = 25
pi# = 3.14159
```

La instrucción `LET` es opcional en QBasic:

```qbasic
LET contador = contador + 1  ' Equivalente a: contador = contador + 1
```

## Control de Pantalla

### CLS

Limpia la pantalla:

```qbasic
CLS
```

### LOCATE

Posiciona el cursor en una fila y columna específicas:

```qbasic
LOCATE 10, 20  ' Posiciona en la fila 10, columna 20
PRINT "Texto posicionado"
```

### COLOR

Cambia los colores de texto y fondo:

```qbasic
COLOR 14, 1  ' Texto amarillo (14) sobre fondo azul (1)
PRINT "Texto coloreado"
```

## Temporización

### SLEEP

Pausa la ejecución del programa durante un número específico de segundos:

```qbasic
PRINT "Espera 2 segundos..."
SLEEP 2
PRINT "¡Listo!"
```

## Declaración de Variables

### DIM

Declara y reserva espacio para variables simples y arrays:

```qbasic
DIM nombre AS STRING
DIM edades(10) AS INTEGER      ' Array de una dimensión
DIM matriz(5, 5) AS SINGLE     ' Array de dos dimensiones
DIM vector(1 TO 100) AS DOUBLE ' Array con índice personalizado
```

### REDIM

Redimensiona un array dinámico (poco común en QBasic, más usado en QuickBASIC):

```qbasic
DIM SHARED miArray() AS INTEGER
REDIM miArray(tamano)
```

### SHARED

Declara variables compartidas entre el programa principal y las subrutinas:

```qbasic
DIM SHARED valorGlobal AS INTEGER
```

## Control de Programa

### END

Finaliza la ejecución del programa:

```qbasic
PRINT "Fin del programa"
END
```

### STOP

Detiene la ejecución del programa y muestra un mensaje:

```qbasic
IF error THEN
    PRINT "Se ha producido un error"
    STOP
END IF
```

### SYSTEM

Sale de QBasic y vuelve al sistema operativo:

```qbasic
SYSTEM
```

## Comentarios

### REM y '

Añade comentarios al código:

```qbasic
REM Esto es un comentario
' Esto también es un comentario
```

## Generación de Números Aleatorios

### RANDOMIZE

Inicializa el generador de números aleatorios:

```qbasic
RANDOMIZE TIMER  ' Usa el reloj del sistema como semilla
```

### RND

Genera un número aleatorio entre 0 y 1:

```qbasic
numeroAleatorio = RND
enteroAleatorio = INT(RND * 100) + 1  ' Número entre 1 y 100
```
