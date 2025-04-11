# Buenas Prácticas en QBasic

## Escribiendo Código Comprensible

Siempre es un desafío escribir código robusto, rápido y optimizado, mientras se mantiene lo suficientemente estructurado para ser legible por ti y por otros programadores en el futuro. Aunque QBasic es considerado un lenguaje relativamente simple, los programas pueden volverse complejos rápidamente si no se aplican buenas prácticas de programación.

### Nomenclatura

Elegir nombres adecuados para los elementos de tu código puede hacerlo mucho más comprensible. Los nombres deben indicar exactamente qué es el elemento:

- **Para funciones o procedimientos**: Comienza el nombre con un verbo, como en "ObtenerEspacioLibre" o "DibujarBorde".
- **Para variables**: El nombre debe indicar lo que contiene, como en "EspacioLibreDisco" o "ContadorFilas".
- **Prefijos de tipo**: Puedes añadir un prefijo de tipo como "n" o "ent" para variables enteras (por ejemplo, "nIndiceArray"), aunque esto no siempre es necesario.

La capitalización de la primera letra de cada palabra en el nombre puede hacerlo más legible. Evita usar una sola letra para los nombres de variables, excepto para índices de bucles (como `i`, `j`, `k`).

```qbasic
' Mal ejemplo
FOR a = 1 TO 10
  b = b + a
NEXT a

' Buen ejemplo
FOR indice = 1 TO NumeroElementos
  SumaTotal = SumaTotal + indice
NEXT indice
```

### Constantes con Nombre

Utiliza constantes con nombre en lugar de valores literales. Por ejemplo, en lugar de escribir:

```qbasic
FOR i = 1 TO 10
  ' código
NEXT i
```

Es mejor escribir:

```qbasic
CONST CantidadFilas = 10
FOR i = 1 TO CantidadFilas
  ' código
NEXT i
```

Usar constantes con nombre tiene dos ventajas principales:

1. Es más fácil entender qué representa el valor.
2. Los cambios futuros en el código serán más sencillos, ya que solo necesitas modificar el valor en un lugar.

### Comentarios

Los comentarios son lo que la mayoría de los programadores dejan para el futuro, y ese es el error, ¡porque luego no lo harán! Siempre añade comentarios mientras estás programando. Describe todo en tu código siguiendo estas reglas:

1. **Añade comentarios antes o junto a las declaraciones de variables** para describir qué son y/o cómo se utilizan en el código.

   ```qbasic
   DIM CantidadElementos AS INTEGER  ' Cantidad total de elementos en el array
   DIM Matriz(100, 100) AS INTEGER   ' Matriz que almacena los valores de altura del terreno
   ```

2. **Añade comentarios antes de cada bloque** para describir su propósito.

   ```qbasic
   ' Calcular el promedio de los elementos
   SumaTotal = 0
   FOR i = 1 TO NumeroElementos
     SumaTotal = SumaTotal + Valores(i)
   NEXT i
   Promedio = SumaTotal / NumeroElementos
   ```

3. **Comenta las partes complicadas del código**. Esto es muy importante, porque generalmente utilizas algunos trucos de codificación para hacer el código mejor y más rápido, pero más tarde no entenderás cómo funciona.

   ```qbasic
   ' Usamos XOR para intercambiar variables sin usar una variable temporal
   a = a XOR b
   b = a XOR b
   a = a XOR b
   ```

4. **Escribe un bloque de comentarios al principio de cada procedimiento**, describe sus entradas y salidas, qué hace y/o cómo funciona. No entres en detalles de la rutina, sin embargo – deberían describirse dentro del procedimiento.

   ```qbasic
   ' ******************************************
   ' SUB DibujarCuadro(x1, y1, x2, y2, color)
   ' Entradas: x1,y1 - Coordenada superior izquierda
   '           x2,y2 - Coordenada inferior derecha
   '           color - Color del cuadro (1-15)
   ' Salida: Ninguna
   ' Función: Dibuja un cuadro en la pantalla usando caracteres ASCII
   ' ******************************************
   SUB DibujarCuadro(x1, y1, x2, y2, color)
   ```

5. Algunos programadores quieren tener un historial de su trabajo. Pueden querer añadir un bloque de comentarios al principio de los procedimientos y módulos con información como la fecha de la última actualización, correcciones de errores, etc.

### Formato

El formato adecuado del código también lo hace más comprensible. Algunas formas comunes de formateo adecuado son:

1. **Indenta los bloques anidados**. Esto es realmente necesario para distinguir bloques anidados entre sí. Por ejemplo, si tienes tres bucles FOR anidados con cuatro bloques IF dentro de ellos, sin indentar el código te confundirás fácilmente con los bloques.

   ```qbasic
   ' Mal ejemplo
   FOR i = 1 TO 10
   FOR j = 1 TO 10
   IF Matriz(i, j) > 0 THEN
   PRINT "Positivo"
   ELSE
   PRINT "No positivo"
   END IF
   NEXT j
   NEXT i

   ' Buen ejemplo
   FOR i = 1 TO 10
     FOR j = 1 TO 10
       IF Matriz(i, j) > 0 THEN
         PRINT "Positivo"
       ELSE
         PRINT "No positivo"
       END IF
     NEXT j
   NEXT i
   ```

2. **Estructura tu código en párrafos**. Mantén líneas relacionadas juntas y deja un espacio entre dos párrafos de código relacionado. Si no has hecho algo así en tu código, inténtalo. Entonces verás cómo tu código se vuelve mucho más comprensible con este pequeño consejo.

   ```qbasic
   ' Inicialización de variables
   x = 0
   y = 0
   contador = 0
   
   ' Procesamiento principal
   INPUT "Ingrese un valor: ", valor
   WHILE valor > 0
     suma = suma + valor
     contador = contador + 1
     INPUT "Ingrese otro valor (0 para terminar): ", valor
   WEND
   
   ' Mostrar resultados
   PRINT "La suma es: "; suma
   IF contador > 0 THEN
     PRINT "El promedio es: "; suma / contador
   ELSE
     PRINT "No se ingresaron valores positivos"
   END IF
   ```

3. **Declara todas las variables al principio** del módulo o procedimiento. Esto facilita encontrar la declaración de cada variable. No declarar las variables es un gran error que hace que tu código sea menos optimizado y menos estructurado.

   ```qbasic
   SUB ProcesarDatos
     ' Declaración de variables
     DIM i AS INTEGER
     DIM suma AS SINGLE
     DIM promedio AS SINGLE
     DIM valor AS INTEGER
     DIM contador AS INTEGER
     
     ' Código del procedimiento...
   END SUB
   ```

## Técnicas de Codificación

Podemos llamarlo el arte de programar: codificar de manera que facilite los cambios futuros. ¿Cómo debemos organizar nuestro código? A continuación, se discuten algunos de los conceptos más importantes de la programación.

### Programación Modular

Siempre escribe tu código de manera modular. Esto es muy importante por varias razones:

1. **Reutilización**: Los módulos independientes pueden utilizarse en más de un proyecto. Supongamos que has escrito un programa que necesita algunos cálculos financieros. Si colocas el código que realiza estos cálculos en un módulo independiente, puedes utilizarlo en otro programa sin hacer cambios.

2. **Organización**: Dividir tu código en partes más pequeñas hace que tu proyecto esté más organizado. Un proyecto bien organizado tiene un gran potencial para ser ampliado en cualquier momento.

3. **Flexibilidad de enfoque**: La programación modular facilita tanto la programación de arriba hacia abajo como de abajo hacia arriba:
   - Los programadores que prefieren trabajar de arriba hacia abajo pueden escribir las partes principales del código en el módulo principal, mientras declaran procedimientos que se codificarán en otros módulos más tarde.
   - Quienes prefieren trabajar de abajo hacia arriba pueden escribir módulos independientes uno por uno, y como escriben partes relacionadas juntas, es más fácil seguir el rastro de su código.

4. **Trabajo en equipo**: La programación modular también facilita la programación de un proyecto grande en equipo.

En QBasic, puedes implementar la programación modular de varias maneras:

```qbasic
' Archivo: PRINCIPAL.BAS
' Módulo principal del programa

' Inclusión de otros módulos usando $INCLUDE
'$INCLUDE: 'UTILIDAD.BI'
'$INCLUDE: 'GRAFICO.BI'

' Declaraciones de variables globales
DIM SHARED configuracion AS configuracionType

' Programa principal
SUB Main
  InicializarPrograma
  MostrarMenuPrincipal
  FinalizarPrograma
END SUB

' Llamada al programa principal
Main
END
```

```qbasic
' Archivo: UTILIDAD.BI
' Módulo de utilidades con funciones comunes

' Tipo de datos para configuración
TYPE configuracionType
  nombreArchivo AS STRING * 64
  modoGrafico AS INTEGER
  colorTexto AS INTEGER
END TYPE

' Funciones de utilidad
SUB InicializarPrograma
  ' Código de inicialización
END SUB

FUNCTION LeerArchivo$(nombreArchivo$)
  ' Código para leer archivos
END FUNCTION

SUB GuardarArchivo(nombreArchivo$, datos$)
  ' Código para guardar archivos
END SUB
```

Es importante destacar que la mayoría de las ventajas de la programación modular están asociadas con que los módulos sean independientes. Por supuesto, al menos un módulo en cada proyecto no puede ser independiente, pero si todos los módulos dependen de otros, entonces hay poca ventaja en la programación modular.

### Programación Procedural

En la sección anterior, hablamos sobre el uso de módulos. Para estar lo suficientemente estructurado, el código en cada módulo debe dividirse en procedimientos. A diferencia de los módulos en un proyecto, los procedimientos en un módulo pueden usar otros procedimientos y, de hecho, es por eso que se colocan en un mismo módulo.

**Cada procedimiento debe tener una sola tarea**. Esto facilita la codificación de procedimientos, ya que es más fácil aplicar los cambios en el código. Cambias un procedimiento y los otros que lo utilizan funcionarán de la manera que deseas. Esta es la principal ventaja de la programación procedural.

En QBasic, los procedimientos se implementan mediante:

1. **SUB**: Para procedimientos que no devuelven un valor

   ```qbasic
   SUB DibujarCuadro (x1, y1, x2, y2, color)
     ' Dibujar un cuadro en la pantalla
     LINE (x1, y1)-(x2, y2), color, B
   END SUB
   ```

2. **FUNCTION**: Para procedimientos que devuelven un valor

   ```qbasic
   FUNCTION CalcularArea (ancho, alto)
     CalcularArea = ancho * alto
   END FUNCTION
   ```

Algunas buenas prácticas para procedimientos en QBasic:

1. **Nombres descriptivos**: Utiliza nombres que describan claramente lo que hace el procedimiento.

2. **Parámetros claros**: Define claramente los parámetros de entrada y salida.

3. **Tamaño adecuado**: Un procedimiento no debe ser demasiado largo. Si es muy extenso, considera dividirlo en procedimientos más pequeños.

4. **Cohesión**: Cada procedimiento debe realizar una tarea única y bien definida.

5. **Bajo acoplamiento**: Minimiza las dependencias entre procedimientos.

Ejemplo de una estructura procedural bien organizada:

```qbasic
' Programa principal
SUB Main
  ' Variables locales
  DIM opcion AS INTEGER
  
  ' Inicialización
  InicializarPrograma
  
  ' Bucle principal
  DO
    opcion = MostrarMenu()
    ProcesarOpcion opcion
  LOOP UNTIL opcion = 0
  
  ' Finalización
  FinalizarPrograma
END SUB

' Muestra el menú principal y devuelve la opción seleccionada
FUNCTION MostrarMenu()
  CLS
  PRINT "MENÚ PRINCIPAL"
  PRINT "=============="
  PRINT "1. Ingresar datos"
  PRINT "2. Procesar información"
  PRINT "3. Mostrar resultados"
  PRINT "0. Salir"
  PRINT
  INPUT "Seleccione una opción: ", opcion
  MostrarMenu = opcion
END FUNCTION

' Procesa la opción seleccionada
SUB ProcesarOpcion(opcion)
  SELECT CASE opcion
    CASE 1: IngresarDatos
    CASE 2: ProcesarInformacion
    CASE 3: MostrarResultados
    CASE 0: ' No hacer nada, salir
    CASE ELSE: MostrarError "Opción no válida"
  END SELECT
END SUB
```

### Combinando Módulos y Procedimientos

Un proyecto QBasic bien estructurado combina tanto la programación modular como la procedural:

1. **Define módulos para áreas funcionales**: Gráficos, entrada/salida, cálculos, interfaz de usuario, etc.

2. **Dentro de cada módulo, define procedimientos específicos**: Cada procedimiento realiza una tarea concreta dentro del ámbito del módulo.

3. **Establece interfaces claras entre módulos**: Define cómo interactúan los módulos entre sí, minimizando dependencias.

Esta aproximación estructurada te permitirá:

- Mantener tu código organizado y fácil de entender
- Reutilizar componentes en otros proyectos
- Facilitar modificaciones y ampliaciones futuras
- Trabajar en equipo de manera más eficiente en proyectos grandes

## Optimización de Código

// ... existing content ...
