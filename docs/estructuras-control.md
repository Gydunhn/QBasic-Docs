# Estructuras de Control

Las estructuras de control permiten alterar el flujo de ejecución de un programa. QBasic ofrece diversas estructuras para toma de decisiones y bucles.

## Estructuras Condicionales

### IF-THEN-ELSE

La estructura `IF-THEN-ELSE` permite ejecutar código basado en una condición:

```qbasic
' Forma simple de una línea
IF edad >= 18 THEN PRINT "Mayor de edad"

' Forma de bloque
IF nota >= 5 THEN
    PRINT "Aprobado"
ELSE
    PRINT "Suspendido"
END IF

' Forma múltiple con ELSEIF
IF nota < 5 THEN
    PRINT "Suspendido"
ELSEIF nota < 7 THEN
    PRINT "Aprobado"
ELSEIF nota < 9 THEN
    PRINT "Notable"
ELSE
    PRINT "Sobresaliente"
END IF
```

### SELECT CASE

La estructura `SELECT CASE` es útil cuando hay múltiples condiciones sobre una misma variable:

```qbasic
SELECT CASE opcion
    CASE 1
        PRINT "Seleccionaste la opción 1"
    CASE 2
        PRINT "Seleccionaste la opción 2"
    CASE 3, 4
        PRINT "Seleccionaste la opción 3 o 4"
    CASE 5 TO 10
        PRINT "Seleccionaste un número entre 5 y 10"
    CASE IS > 10
        PRINT "Seleccionaste un número mayor que 10"
    CASE ELSE
        PRINT "Opción no válida"
END SELECT
```

## Bucles

### FOR-NEXT

El bucle `FOR-NEXT` permite repetir código un número específico de veces:

```qbasic
' Bucle básico de 1 a 10
FOR i = 1 TO 10
    PRINT i;
NEXT i

' Con incremento personalizado
FOR i = 0 TO 100 STEP 10
    PRINT i;
NEXT i

' Con decremento
FOR i = 10 TO 1 STEP -1
    PRINT i;
NEXT i
```

Se pueden anidar bucles `FOR-NEXT`:

```qbasic
FOR i = 1 TO 5
    FOR j = 1 TO 3
        PRINT i; ","; j
    NEXT j
NEXT i
```

### WHILE-WEND

El bucle `WHILE-WEND` repite código mientras una condición sea verdadera:

```qbasic
contador = 1
WHILE contador <= 5
    PRINT contador
    contador = contador + 1
WEND
```

### DO-LOOP

Más flexible que `WHILE-WEND`, el bucle `DO-LOOP` tiene varias formas:

```qbasic
' Evalúa la condición al inicio (como WHILE-WEND)
contador = 1
DO WHILE contador <= 5
    PRINT contador
    contador = contador + 1
LOOP

' Evalúa la condición al final
contador = 1
DO
    PRINT contador
    contador = contador + 1
LOOP WHILE contador <= 5

' Con UNTIL (hasta que)
contador = 1
DO UNTIL contador > 5
    PRINT contador
    contador = contador + 1
LOOP

' UNTIL al final
contador = 1
DO
    PRINT contador
    contador = contador + 1
LOOP UNTIL contador > 5
```

## Control de Flujo Adicional

### GOTO

Salta a una etiqueta específica:

```qbasic
PRINT "Inicio"
GOTO Saltar
PRINT "Esto no se imprimirá"
Saltar:
PRINT "Después del salto"
```

> Nota: El uso de `GOTO` generalmente se desaconseja en favor de estructuras de control más estructuradas.

### GOSUB-RETURN

Llama a una subrutina y regresa:

```qbasic
PRINT "Programa principal"
GOSUB Subrutina
PRINT "De vuelta en el programa principal"
END

Subrutina:
PRINT "Esta es una subrutina"
RETURN
```

> Nota: En QBasic moderno, es preferible usar `SUB` y `FUNCTION` en lugar de `GOSUB`.

### EXIT

Sale de un bucle antes de tiempo:

```qbasic
FOR i = 1 TO 100
    PRINT i
    IF i = 10 THEN EXIT FOR
NEXT i

DO
    INPUT "Introduce un número (0 para salir): ", num
    IF num = 0 THEN EXIT DO
    PRINT "El cuadrado es:"; num * num
LOOP
```

## Ejemplo Combinado

Este ejemplo muestra varias estructuras de control trabajando juntas:

```qbasic
CLS
INPUT "¿Cuántos números quieres procesar? ", cantidad

FOR i = 1 TO cantidad
    PRINT "Número"; i
    INPUT "Introduce un valor: ", valor
    
    IF valor < 0 THEN
        PRINT "El valor es negativo"
    ELSEIF valor = 0 THEN
        PRINT "El valor es cero"
    ELSE
        ' Procesar números positivos
        SELECT CASE valor
            CASE 1 TO 10
                PRINT "Valor entre 1 y 10"
            CASE 11 TO 100
                PRINT "Valor entre 11 y 100"
            CASE ELSE
                PRINT "Valor mayor que 100"
        END SELECT
        
        ' Mostrar todos los divisores
        PRINT "Divisores:";
        divisores = 0
        
        j = 1
        DO WHILE j <= valor
            IF valor MOD j = 0 THEN
                PRINT j;
                divisores = divisores + 1
            END IF
            j = j + 1
        LOOP
        
        PRINT
        PRINT "Total de divisores:"; divisores
    END IF
    
    PRINT
NEXT i

PRINT "Programa finalizado"
```
