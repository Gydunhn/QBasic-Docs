# Funciones

QBasic ofrece dos tipos de funciones: las incorporadas (built-in) y las definidas por el usuario. Las funciones permiten reutilizar código y modularizar la lógica del programa.

## Funciones Incorporadas

QBasic incluye numerosas funciones predefinidas para operaciones matemáticas, manipulación de cadenas y otras tareas comunes.

### Funciones Matemáticas

| Función  | Descripción                  | Ejemplo                   |
| -------- | ---------------------------- | ------------------------- |
| `ABS(x)` | Valor absoluto               | `ABS(-5)` devuelve `5`    |
| `INT(x)` | Parte entera                 | `INT(3.7)` devuelve `3`   |
| `FIX(x)` | Trunca hacia cero            | `FIX(-3.7)` devuelve `-3` |
| `SGN(x)` | Signo (-1, 0, 1)             | `SGN(-5)` devuelve `-1`   |
| `SQR(x)` | Raíz cuadrada                | `SQR(16)` devuelve `4`    |
| `RND`    | Número aleatorio entre 0 y 1 | `RND`                     |

#### Funciones Trigonométricas

| Función  | Descripción          | Ejemplo                      |
| -------- | -------------------- | ---------------------------- |
| `SIN(x)` | Seno (x en radianes) | `SIN(0)` devuelve `0`        |
| `COS(x)` | Coseno               | `COS(0)` devuelve `1`        |
| `TAN(x)` | Tangente             | `TAN(0)` devuelve `0`        |
| `ATN(x)` | Arcotangente         | `ATN(1)` devuelve `0.785398` |

Para convertir entre grados y radianes:

```qbasic
radianes = grados * 3.14159 / 180
grados = radianes * 180 / 3.14159
```

#### Funciones Logarítmicas y Exponenciales

| Función  | Descripción       | Ejemplo                     |
| -------- | ----------------- | --------------------------- |
| `LOG(x)` | Logaritmo natural | `LOG(2.71828)` devuelve `1` |
| `EXP(x)` | Exponencial (e^x) | `EXP(1)` devuelve `2.71828` |

### Funciones de Cadena

| Función                    | Descripción                     | Ejemplo                                  |
| -------------------------- | ------------------------------- | ---------------------------------------- |
| `LEN(s$)`                  | Longitud de la cadena           | `LEN("QBasic")` devuelve `6`             |
| `LEFT$(s$, n)`             | n caracteres desde la izquierda | `LEFT$("QBasic", 1)` devuelve `"Q"`      |
| `RIGHT$(s$, n)`            | n caracteres desde la derecha   | `RIGHT$("QBasic", 5)` devuelve `"Basic"` |
| `MID$(s$, start, len)`     | Subcadena                       | `MID$("QBasic", 2, 3)` devuelve `"Bas"`  |
| `INSTR([start,] s1$, s2$)` | Posición de s2$ en s1$          | `INSTR("QBasic", "as")` devuelve `3`     |
| `UCASE$(s$)`               | Convierte a mayúsculas          | `UCASE$("QBasic")` devuelve `"QBASIC"`   |
| `LCASE$(s$)`               | Convierte a minúsculas          | `LCASE$("QBasic")` devuelve `"qbasic"`   |
| `LTRIM$(s$)`               | Elimina espacios a la izquierda | `LTRIM$("  QB  ")` devuelve `"QB  "`     |
| `RTRIM$(s$)`               | Elimina espacios a la derecha   | `RTRIM$("  QB  ")` devuelve `"  QB"`     |
| `SPACE$(n)`                | Genera n espacios               | `SPACE$(3)` devuelve `"   "`             |
| `STRING$(n, c)`            | Genera n caracteres c           | `STRING$(3, "X")` devuelve `"XXX"`       |

### Funciones de Conversión

| Función   | Descripción                     | Ejemplo                       |
| --------- | ------------------------------- | ----------------------------- |
| `STR$(n)` | Convierte número a cadena       | `STR$(123)` devuelve `" 123"` |
| `VAL(s$)` | Convierte cadena a número       | `VAL("123")` devuelve `123`   |
| `ASC(s$)` | Valor ASCII del primer carácter | `ASC("A")` devuelve `65`      |
| `CHR$(n)` | Carácter del valor ASCII        | `CHR$(65)` devuelve `"A"`     |
| `HEX$(n)` | Convierte a hexadecimal         | `HEX$(255)` devuelve `"FF"`   |
| `OCT$(n)` | Convierte a octal               | `OCT$(8)` devuelve `"10"`     |

## Funciones Definidas por el Usuario

### Función FUNCTION

Se define una función con la palabra clave `FUNCTION` y se finaliza con `END FUNCTION`:

```qbasic
FUNCTION Cuadrado (numero)
    Cuadrado = numero * numero
END FUNCTION
```

Para utilizar la función:

```qbasic
PRINT Cuadrado(5)    ' Muestra 25
x = Cuadrado(3) + 1  ' x = 10
```

Las funciones pueden tener múltiples parámetros:

```qbasic
FUNCTION Hipotenusa (a, b)
    Hipotenusa = SQR(a * a + b * b)
END FUNCTION

PRINT Hipotenusa(3, 4)  ' Muestra 5
```

### Función DEF FN

Una forma más simple pero menos flexible de crear funciones:

```qbasic
DEF FNCubo(x) = x * x * x
PRINT FNCubo(3)  ' Muestra 27
```

## Subrutinas

Las subrutinas, a diferencia de las funciones, no devuelven valores pero pueden modificar los parámetros pasados por referencia:

```qbasic
DECLARE SUB Intercambiar (a AS INTEGER, b AS INTEGER)

a% = 5
b% = 10
CALL Intercambiar(a%, b%)
PRINT a%, b%   ' Muestra 10 5

SUB Intercambiar (a AS INTEGER, b AS INTEGER)
    temp = a
    a = b
    b = temp
END SUB
```

La palabra `CALL` es opcional:

```qbasic
Intercambiar a%, b%   ' Equivalente a CALL Intercambiar(a%, b%)
```

## Parámetros por Valor vs. por Referencia

Por defecto, los parámetros se pasan por referencia. Para pasar por valor, se utilizan paréntesis:

```qbasic
DECLARE SUB ModificarValor (a AS INTEGER)
DECLARE SUB IntentarModificar (a AS INTEGER)

x% = 5
ModificarValor x%         ' Pasa por referencia
PRINT x%                  ' Muestra 10
IntentarModificar (x%)    ' Pasa por valor
PRINT x%                  ' Sigue mostrando 10

SUB ModificarValor (a AS INTEGER)
    a = a * 2
END SUB

SUB IntentarModificar (a AS INTEGER)
    a = a * 2    ' Sólo modifica la copia local
END SUB
```

## Ejemplo Completo

```qbasic
DECLARE FUNCTION Factorial! (n AS INTEGER)
DECLARE SUB MostrarTabla (base AS INTEGER, hasta AS INTEGER)

CLS
INPUT "Introduce un número para calcular su factorial: ", num%

PRINT "El factorial de"; num%; "es"; Factorial(num%)
PRINT
INPUT "¿Quieres ver una tabla de factoriales? (S/N) ", respuesta$

IF UCASE$(respuesta$) = "S" THEN
    PRINT
    MostrarTabla num%, 10
END IF

FUNCTION Factorial (n AS INTEGER)
    IF n <= 1 THEN
        Factorial = 1
    ELSE
        Factorial = n * Factorial(n - 1)
    END IF
END FUNCTION

SUB MostrarTabla (base AS INTEGER, hasta AS INTEGER)
    PRINT "Tabla de factoriales:"
    PRINT "Número", "Factorial"
    PRINT "------", "---------"
    
    FOR i = 1 TO hasta
        PRINT i, Factorial(i)
    NEXT i
END SUB
```

Este ejemplo muestra cómo se pueden combinar funciones y subrutinas en un programa QBasic completo, incluyendo el uso de recursividad en la función `Factorial`.
