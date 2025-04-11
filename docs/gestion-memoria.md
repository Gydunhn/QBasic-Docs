# Gestión de Memoria en QBasic

Autor original: Thav (Raul Carolus)

Este artículo aborda los problemas comunes de memoria en QBasic, especialmente los errores "Out of stack space" (Sin espacio en la pila) y "Out of memory" (Sin memoria). Entender cómo funciona la memoria en QBasic ayudará a evitar estos errores y a optimizar tus programas.

## La Pila de Ejecución (Runtime Stack)

Todo programa en QBasic utiliza un espacio en la pila de ejecución. Esta pila no almacena variables o datos del programa, sino información que controla la ejecución del mismo.

### Registros de Activación

Cada vez que tu programa se ejecuta o llama a una función o subrutina, se crea un "registro de activación" (Activation Record) que se añade a la pila. Estos registros contienen:

- **Dirección de retorno**: Indica a qué línea del programa debe volver cuando la función o subrutina termine de ejecutarse
- **Parámetros**: Los valores que se pasan a la función o subrutina
- **Variables de retorno**: El espacio para almacenar el valor que devuelve una función

Por ejemplo, si llamas a una subrutina `Compute` desde la línea 156 de tu programa:

```qbasic
CALL Compute(first%, last%)
```

Se crea un registro de activación con:

- Dirección de retorno: 157 (la línea siguiente a la llamada)
- Parámetros: `first%` y `last%`

### Error "Out of stack space"

Este error ocurre cuando la pila de ejecución se llena por completo. La causa más común es una función recursiva (que se llama a sí misma) sin una condición de salida:

```qbasic
SUB BlowStack()
  CALL BlowStack()  ' Se llama a sí misma sin condición de salida
END SUB
```

Este código agotará rápidamente el espacio en la pila, ya que cada llamada añade un nuevo registro de activación sin eliminar ninguno.

### Consejos para evitar errores de pila

1. **Mantén el bucle principal en la sección principal** del programa, no en una subrutina.
2. **Evita llamar a muchas subrutinas o funciones desde dentro de otras** subrutinas o funciones.
3. **Evita usar GOSUB** - generalmente es considerado una mala práctica.

Si necesitas más espacio para la pila, puedes usar la instrucción `CLEAR`, pero recuerda que esto no resolverá problemas de "Out of Memory" relacionados con las variables.

## Variables y Tipos de Datos

### Tipos básicos de datos

- **Integer (`%`)**: 2 bytes, valores entre -32768 y 32767
- **Long (`&`)**: 4 bytes, valores entre -2,147,483,648 y 2,147,483,647
- **Single (`!`)**: 4 bytes, precisión simple, rango aproximado ±1.401298 E-45 a ±3.402823 E+38
- **Double (`#`)**: 8 bytes, precisión doble, rango aproximado ±4.940656 D-324 a ±1.7976931 D+308
- **String (`$`)**: 4 bytes para el descriptor y 1 byte por cada carácter, longitud máxima de 32767 bytes

### Estructuras de datos avanzadas

#### Arrays (Arreglos)

Los arrays son colecciones de variables del mismo tipo bajo un solo nombre:

```qbasic
DIM scores(0 TO 100) AS INTEGER  ' Crea 101 variables INTEGER
```

Para acceder a un elemento:

```qbasic
scores(57) = 95  ' Asigna 95 al elemento 57 del array
```

También puedes usar variables como índices:

```qbasic
scores(topscore) = 100  ' La variable topscore debe ser INTEGER
```

#### Types (Tipos personalizados)

Los tipos personalizados (similares a los "structs" en C) permiten agrupar diferentes tipos de datos bajo un solo nombre:

```qbasic
TYPE PersonalInfoType
  theName AS STRING * 10
  age AS INTEGER
  address1 AS STRING * 20
  address2 AS STRING * 20
END TYPE
```

Para usar un tipo personalizado:

```qbasic
DIM info AS PersonalInfoType

info.theName = "John"
info.age = 23
```

También puedes crear arrays de tipos personalizados:

```qbasic
DIM info(3000) AS PersonalInfoType
info(55).theName = "Jane"
```

## Técnicas de optimización de memoria

### Uso de STRING * 1 como byte

En lugar de usar un INTEGER de 2 bytes para almacenar valores entre 0 y 255, puedes usar `STRING * 1` que solo ocupa 1 byte:

```qbasic
DIM flags AS STRING * 1
flags = CHR$(65)  ' Almacena el valor ASCII 65 (letra 'A')
value = ASC(flags)  ' Recupera el valor numérico (65)
```

Esta técnica es útil para ahorrar memoria en arrays grandes o al trabajar con archivos binarios mediante `BSAVE` y `BLOAD`.

### Uso de TYPES para variables globales

Si tienes muchas variables globales relacionadas, es mejor agruparlas en un tipo personalizado:

```qbasic
TYPE GameInfoType
  currentMap AS INTEGER
  oldMap AS INTEGER
  playerX AS INTEGER
  playerY AS INTEGER
END TYPE

DIM SHARED gameInfo AS GameInfoType
```

Esto organiza mejor tu código y facilita el paso de múltiples variables relacionadas entre subrutinas.

## Optimización de strings

El manejo de strings en QBasic puede consumir mucha memoria debido al "recolector de basura" ineficiente. Algunas prácticas para optimizar:

1. **Predimensiona los strings** cuando sea posible:

   ```qbasic
   DIM text AS STRING * 80  ' String de longitud fija de 80 caracteres
   ```

2. **Reutiliza variables de string** en lugar de crear nuevas:

   ```qbasic
   ' Mejor
   temp$ = "Hola "
   temp$ = temp$ + "Mundo"
   
   ' Menos eficiente en memoria
   greeting$ = "Hola "
   fullGreeting$ = greeting$ + "Mundo"
   ```

3. **Libera memoria de strings** periódicamente en programas largos:

   ```qbasic
   a$ = SPACE$(0)  ' Libera la memoria usada por a$
   ```

## Consejos finales

- Usa el tipo de datos más pequeño que se ajuste a tus necesidades
- Optimiza las estructuras de datos para minimizar el desperdicio de memoria
- Si necesitas trabajar con grandes cantidades de datos, considera usar archivos en lugar de mantener todo en memoria
- Evita la recursión profunda para prevenir errores de pila
- Utiliza `CLEAR` para ajustar el espacio de pila disponible si es necesario

Entender estos conceptos te ayudará a crear programas QBasic más eficientes y estables, evitando los frustrantes errores de memoria.
