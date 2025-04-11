# Sintaxis

La sintaxis de QBasic sigue las convenciones generales de la familia de lenguajes BASIC, con algunas particularidades propias. Esta sección cubre las reglas fundamentales para escribir código QBasic válido.

## Estructura de un programa

Un programa en QBasic tiene la siguiente estructura general:

```qbasic
' Declaraciones (opcional)
DECLARE SUB MiSubrutina (parametro1 AS INTEGER, parametro2 AS STRING)
DIM variables AS tipo

' Programa principal
CLS
PRINT "Hola Mundo"
MiSubrutina 10, "texto"
END

' Subrutinas y funciones
SUB MiSubrutina (x AS INTEGER, texto AS STRING)
    PRINT x; texto
END SUB
```

## Tipos de datos

QBasic ofrece varios tipos de datos nativos:

| Tipo       | Descripción                    | Rango                          | Espacio  | Ejemplo                |
| ---------- | ------------------------------ | ------------------------------ | -------- | ---------------------- |
| INTEGER    | Entero de 16 bits              | -32,768 a 32,767               | 2 bytes  | `DIM a AS INTEGER`     |
| LONG       | Entero de 32 bits              | -2,147,483,648 a 2,147,483,647 | 4 bytes  | `DIM b AS LONG`        |
| SINGLE     | Punto flotante                 | ±3.4E-38 a ±3.4E+38            | 4 bytes  | `DIM c AS SINGLE`      |
| DOUBLE     | Punto flotante doble precisión | ±1.7E-308 a ±1.7E+308          | 8 bytes  | `DIM d AS DOUBLE`      |
| STRING     | Cadena de caracteres           | Máximo 32,767 caracteres       | Variable | `DIM e AS STRING`      |
| STRING * n | Cadena de longitud fija        | n caracteres                   | n bytes  | `DIM f AS STRING * 10` |

### Declaración de variables

Las variables se pueden declarar con:

```qbasic
DIM miVariable AS tipo   ' Declaración explícita
DIM miArray(10) AS tipo  ' Array de una dimensión
DIM miMatriz(5, 5) AS tipo  ' Array de dos dimensiones
```

También puedes usar sufijos para definir el tipo:

- `%` para INTEGER: `contador%`
- `&` para LONG: `numeroGrande&`
- `!` para SINGLE: `decimal!`
- `#` para DOUBLE: `pi#`
- `$` para STRING: `nombre$`

## Operadores

### Operadores aritméticos

| Operador | Descripción     | Ejemplo                      |
| -------- | --------------- | ---------------------------- |
| +        | Suma            | `a = b + c`                  |
| -        | Resta           | `a = b - c`                  |
| *        | Multiplicación  | `a = b * c`                  |
| /        | División        | `a = b / c`                  |
| \        | División entera | `a = 7 \ 2` (resultado: 3)   |
| MOD      | Módulo (resto)  | `a = 7 MOD 2` (resultado: 1) |
| ^        | Exponenciación  | `a = 2 ^ 3` (resultado: 8)   |
| -        | Negación        | `a = -b`                     |

### Operadores relacionales

| Operador | Descripción       | Ejemplo          |
| -------- | ----------------- | ---------------- |
| =        | Igual a           | `IF a = b THEN`  |
| <>       | Distinto a        | `IF a <> b THEN` |
| <        | Menor que         | `IF a < b THEN`  |
| >        | Mayor que         | `IF a > b THEN`  |
| <=       | Menor o igual que | `IF a <= b THEN` |
| >=       | Mayor o igual que | `IF a >= b THEN` |

### Operadores lógicos

| Operador | Descripción     | Ejemplo                   |
| -------- | --------------- | ------------------------- |
| AND      | Y lógico        | `IF a > 0 AND b > 0 THEN` |
| OR       | O lógico        | `IF a > 0 OR b > 0 THEN`  |
| NOT      | Negación lógica | `IF NOT a = 0 THEN`       |
| XOR      | O exclusivo     | `result = a XOR b`        |
| EQV      | Equivalencia    | `result = a EQV b`        |
| IMP      | Implicación     | `result = a IMP b`        |

## Constantes

Se pueden definir constantes con la instrucción `CONST`:

```qbasic
CONST PI = 3.14159
CONST NOMBRE$ = "QBasic"
CONST MAX_ELEMENTOS = 100
```

## Comentarios

Los comentarios en QBasic se indican con apóstrofe o con la palabra clave `REM`:

```qbasic
' Esto es un comentario
REM Esto también es un comentario
```

## Continuación de línea

Para continuar una línea larga en la siguiente, se usa el carácter `_`:

```qbasic
PRINT "Esta es una línea muy larga que continúa " + _
      "en la siguiente línea"
```

## Convenciones de nombres

- Los nombres de variables, subrutinas y funciones son insensibles a mayúsculas/minúsculas: `Contador` es igual a `CONTADOR` o `contador`
- Pueden contener letras, números y algunos caracteres especiales como el guion bajo
- Deben comenzar con una letra
- No pueden contener espacios ni usar palabras reservadas
- La longitud máxima es de 40 caracteres
