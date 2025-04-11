# Técnicas

Programación estructurada, modularidad, comentarios.

## Técnicas Gráficas Avanzadas

Esta sección cubre técnicas avanzadas para manipular gráficos en QBasic, basadas en los tutoriales de Damian Nikodem ("Hacker").

### Manipulación de la Paleta

La paleta en QBasic es una serie de números donde se almacenan los valores de los colores. Al modificar la paleta, puedes cambiar los colores de una imagen sin alterar su estructura.

Para manipular la paleta, se utilizan comandos de entrada/salida directos al hardware:

```qbasic
' Cambiar un color en la paleta
OUT &H3C8, color     ' Seleccionar el color a modificar (0-255)
OUT &H3C9, rojo      ' Componente rojo (0-63)
OUT &H3C9, verde     ' Componente verde (0-63)
OUT &H3C9, azul      ' Componente azul (0-63)
```

Los valores de cada componente de color (rojo, verde, azul) deben estar entre 0 y 63.

Para guardar y cargar paletas desde archivos, puedes utilizar las siguientes subrutinas:

```qbasic
SUB guardarPaleta (archivo$)
  OPEN archivo$ FOR OUTPUT AS #1
  FOR x% = 0 TO 255
    CALL obtenerColor(x%, r%, g%, b%)
    PRINT #1, CHR$(r%); CHR$(g%); CHR$(b%);
  NEXT x%
  CLOSE #1
END SUB

SUB obtenerColor (Color%, Rojo%, Verde%, Azul%)
  OUT &H3C7, Color%
  Rojo% = INP(&H3C9)
  Verde% = INP(&H3C9)
  Azul% = INP(&H3C9)
END SUB

SUB cargarPaleta (archivo$)
  OPEN archivo$ FOR BINARY AS #1
  r$ = " "
  g$ = " "
  b$ = " "
  FOR x% = 0 TO 255
    GET #1, , r$: r% = ASC(r$)
    GET #1, , g$: g% = ASC(g$)
    GET #1, , b$: b% = ASC(b$)
    CALL cambiarColor(x%, r%, g%, b%)
  NEXT x%
  CLOSE #1
END SUB

SUB cambiarColor (Color%, Rojo%, Verde%, Azul%)
  OUT &H3C8, Color%
  OUT &H3C9, Rojo%
  OUT &H3C9, Verde%
  OUT &H3C9, Azul%
END SUB
```

### Gráficos Rápidos con Acceso Directo a Memoria

PSET es un comando relativamente lento para dibujar píxeles. Una alternativa mucho más rápida es escribir directamente en la memoria de vídeo:

```qbasic
SCREEN 13           ' Modo gráfico 320x200 con 256 colores
DEF SEG = &HA000    ' Apuntar al segmento de memoria de vídeo
x = 10              ' Posición X del píxel
y = 10              ' Posición Y del píxel
color = 30          ' Color del píxel
POKE (y * 320) + x, color  ' Colocar el píxel en la pantalla
```

El cálculo `(y * 320) + x` determina la posición exacta en la memoria de vídeo. En el modo 13h (SCREEN 13), cada línea tiene 320 píxeles, por lo que multiplicamos la coordenada Y por 320 para determinar la línea, y luego sumamos X para determinar la columna.

### Cargar Imágenes PCX

Las imágenes PCX son un formato común en la época de DOS. Para cargar una imagen PCX en QBasic, necesitamos manejar:

1. **La cabecera**: Contiene información sobre la imagen, como dimensiones
2. **Los datos de la imagen**: Comprimidos usando un esquema sencillo
3. **La paleta**: Almacenada al final del archivo

Este es un ejemplo simplificado para cargar imágenes PCX de 320x200 directamente a la memoria de vídeo:

```qbasic
SUB cargarPCX (archivo$)
  OPEN archivo$ FOR BINARY AS #1
  
  ' Saltar la cabecera de 128 bytes
  SEEK #1, 129
  
  DEF SEG = &HA000
  dirMem = 0
  
  DO UNTIL EOF(1) OR dirMem >= 64000  ' 320x200 = 64000 bytes
    GET #1, , dato$
    datoByte = ASC(dato$)
    
    ' Comprobar si es un byte de compresión
    IF datoByte >= 192 AND datoByte <= 255 THEN
      ' Obtener el contador de repetición
      contador = datoByte - 192
      
      ' Obtener el byte a repetir
      GET #1, , dato$
      repetir = ASC(dato$)
      
      ' Repetir el byte en memoria
      FOR i = 1 TO contador
        POKE dirMem, repetir
        dirMem = dirMem + 1
      NEXT i
    ELSE
      ' Byte sin comprimir
      POKE dirMem, datoByte
      dirMem = dirMem + 1
    END IF
  LOOP
  
  ' Cargar la paleta (los últimos 768 bytes)
  ' Si queda suficiente espacio en el archivo
  IF LOF(1) - 768 > 0 THEN
    SEEK #1, LOF(1) - 768 + 1
    
    FOR color = 0 TO 255
      GET #1, , r$: r = ASC(r$)
      GET #1, , g$: g = ASC(g$)
      GET #1, , b$: b = ASC(b$)
      
      ' Escalar valores de 0-255 a 0-63 para QBasic
      cambiarColor color, r \ 4, g \ 4, b \ 4
    NEXT color
  END IF
  
  CLOSE #1
END SUB
```

### Carga de Archivos BMP

Los archivos BMP son uno de los formatos de imagen más simples ya que no utilizan compresión. Esto los hace ideales para trabajar con ellos directamente en QBasic.

Un archivo BMP típico tiene una cabecera de 1078 bytes que contiene información esencial como las dimensiones de la imagen, la profundidad de color y la paleta. Podemos definir esta estructura utilizando el tipo de datos `TYPE` de QBasic:

```qbasic
TYPE BMPHeaderType       
    id AS STRING * 2      ' Debe ser "BM"
    size AS LONG          ' Tamaño de los datos
    rr1 AS INTEGER        ' Reservado
    rr2 AS INTEGER        ' Reservado
    offset AS LONG        ' Posición del inicio de los datos de píxeles
    horz AS LONG          ' Reservado
    wid AS LONG           ' Ancho de la imagen
    hei AS LONG           ' Alto de la imagen
    planes AS INTEGER     ' Planos de color
    bpp AS INTEGER        ' Bits por píxel (8 para una imagen de 256 colores)
    pakbyte AS LONG       ' Compresión
    imagebytes AS LONG    ' Ancho*Alto
    xres AS LONG          ' Resolución X
    yres AS LONG          ' Resolución Y
    colch AS LONG         ' Colores utilizados
    ic AS LONG            ' Colores importantes
    pal AS STRING * 1024  ' Paleta almacenada como <Azul, Verde, Rojo, 0>
END TYPE
```

#### Cargando la cabecera BMP

Para cargar un archivo BMP, primero debemos abrir el archivo en modo binario y leer la cabecera:

```qbasic
DIM BmpHeader AS BMPHeaderType

OPEN "imagen.bmp" FOR BINARY AS #1
GET #1, , BmpHeader

' Verificar que es un archivo BMP válido
IF BmpHeader.id <> "BM" THEN
    PRINT "No es un archivo BMP válido"
    CLOSE #1
    END
END IF
```

#### Configurando la paleta

Antes de mostrar la imagen, necesitamos establecer la paleta de colores:

```qbasic
SCREEN 13                           ' Modo gráfico 320x200, 256 colores

a$ = BmpHeader.pal                  ' La paleta se almacena en un string de 1024 caracteres

OUT &H3C8, 0                        ' Comenzar a escribir desde el Color 0
FOR i% = 1 TO 1024 STEP 4
    b% = ASC(MID$(a$, i%, 1)) \ 4   ' Azul (dividido por 4 para convertir de 0-255 a 0-63)
    g% = ASC(MID$(a$, i% + 1, 1)) \ 4 ' Verde
    r% = ASC(MID$(a$, i% + 2, 1)) \ 4 ' Rojo
                                    ' i% + 3 es cero (no se utiliza)
    OUT &H3C9, r%                   ' Establecer el color
    OUT &H3C9, g%
    OUT &H3C9, b%
NEXT
```

#### Mostrando la imagen

Una particularidad de los archivos BMP es que las imágenes se almacenan invertidas verticalmente. Por eso, al leer los píxeles, debemos recorrer las filas de abajo hacia arriba:

```qbasic
' Método básico (lento) - un píxel a la vez
DIM Pixel AS STRING * 1              ' Nuestro "byte" de píxel
iHeight% = BmpHeader.hei - 1         ' Restar 1 para la posición real en pantalla
iWidth% = BmpHeader.wid - 1

FOR y% = iHeight% TO 0 STEP -1       ' Cuenta regresiva para la imagen invertida
    FOR x% = 0 TO iWidth%
        GET #1, , Pixel              ' Leer un píxel (byte)
        PSET (x%, y%), ASC(Pixel)    ' Dibujar el píxel
    NEXT x%
NEXT y%
```

#### Optimización para mayor velocidad

El método anterior funciona, pero es muy lento. Podemos mejorar significativamente el rendimiento leyendo una línea completa de píxeles a la vez:

```qbasic
' Método optimizado - una línea a la vez
Pixel$ = SPACE$(BmpHeader.wid)      ' Crear un buffer para una línea completa

iHeight% = BmpHeader.hei - 1
iWidth% = BmpHeader.wid - 1

FOR y% = iHeight% TO 0 STEP -1
    GET #1, , Pixel$                ' Leer una línea completa de una vez
    
    FOR x% = 0 TO iWidth%
        PSET (x%, y%), ASC(MID$(Pixel$, x% + 1, 1))
    NEXT x%
NEXT y%
```

Esta optimización puede hacer que la carga de imágenes sea hasta 8 veces más rápida, especialmente en computadoras más lentas.

#### Método ultra-rápido con acceso directo a memoria

Para una velocidad aún mayor, se puede copiar directamente los datos de la imagen a la memoria de vídeo. Esto requiere conocimientos sobre interrupciones DOS y acceso directo a la memoria, pero ofrece el mejor rendimiento posible:

```qbasic
' Método ultra-rápido con acceso directo a memoria
DECLARE SUB InterruptX (intnum AS INTEGER, inreg AS RegTypeX, outreg AS RegTypeX)

TYPE RegTypeX
    ax    AS INTEGER
    bx    AS INTEGER
    cx    AS INTEGER
    dx    AS INTEGER
    bp    AS INTEGER
    si    AS INTEGER
    di    AS INTEGER
    flags AS INTEGER
    ds    AS INTEGER
    es    AS INTEGER
END TYPE

DIM inregs AS RegTypeX, outregs AS RegTypeX
Pixel$ = SPACE$(BmpHeader.wid)

DEF SEG = &HA000                    ' Segmento de memoria de vídeo
iHeight% = BmpHeader.hei - 1

FOR y% = iHeight% TO 0 STEP -1
    offset% = y% * 320              ' Calcular posición en la memoria de vídeo
    
    ' Configurar registros para INT 21h (DOS), función 3Fh (Leer desde archivo)
    inregs.ax = &H3F00              ' Función 3Fh
    inregs.bx = FREEFILE            ' Handle del archivo
    inregs.cx = BmpHeader.wid       ' Número de bytes a leer
    inregs.dx = offset%             ' Offset en memoria donde escribir
    inregs.ds = &HA000              ' Segmento de memoria
    
    ' Llamar a la interrupción DOS
    InterruptX &H21, inregs, outregs
NEXT y%
```

Este último método es el más rápido, pero requiere una comprensión avanzada de interrupciones y memoria en DOS.

### Algoritmos Aleatorios para Gráficos Realistas

Los algoritmos aleatorios pueden dar un aspecto más natural y realista a tus gráficos. Por ejemplo, para crear una textura de césped:

```qbasic
SCREEN 13
RANDOMIZE TIMER

' Crear una textura de césped de 15x15 píxeles
FOR x = 1 TO 15
  FOR y = 1 TO 15
    ' Elegir entre diferentes tonos de verde y marrón
    z = INT(RND * 8) + 1
    color = z + 186  ' Colores 187-194 (ajustar según la paleta)
    
    PSET (x, y), color
  NEXT y
NEXT x
```

Este enfoque añade variación y sutileza a tus gráficos. Puedes aplicar técnicas similares para crear:

- Texturas de terreno (tierra, arena, roca)
- Efectos de cielo y nubes
- Efectos de luz y sombra
- Degradados con transiciones naturales

### Consejos para Mejorar los Gráficos

1. **Usa paletas personalizadas**: Crea paletas específicas para tus escenas para obtener colores más realistas.

2. **Combina técnicas de acceso directo a memoria** con algoritmos aleatorios para gráficos rápidos y naturales.

3. **Simula profundidad** utilizando variaciones de color y gradientes sutiles.

4. **Estudia imágenes reales** para entender cómo la aleatoriedad afecta a las texturas naturales.

5. **Optimiza tus rutinas gráficas** para mantener un rendimiento aceptable, especialmente en bucles anidados.

## Técnicas de Programación 3D

Esta sección explora los conceptos básicos para crear gráficos tridimensionales en QBasic, basada en el trabajo de Aaron Severn.

### Conceptos Básicos del Espacio 3D

Para programar en 3D, es fundamental comprender cómo representar el espacio tridimensional. En un sistema de coordenadas 3D tenemos:

- **Eje X**: Representa el movimiento horizontal (izquierda/derecha)
- **Eje Y**: Representa el movimiento vertical (arriba/abajo)
- **Eje Z**: Representa la profundidad (cerca/lejos)

En la programación 3D básica, usamos esta representación gráfica del espacio:

```text
                 Z             Donde Y es el eje horizontal
                 |                   Z es el eje vertical
                 |                   X es el eje que sale de la pantalla
                 |φ
                 |_______            θ es el ángulo en el plano XY
                 /       Y           φ es el ángulo desde el eje Z
                / θ 
               / 
              /
             X
```

### Proyección 3D a 2D

El principal desafío de la programación 3D es representar un objeto tridimensional en una pantalla bidimensional. La técnica básica consiste en dividir las coordenadas por la distancia al observador (valor Z), creando así un efecto de perspectiva.

Las fórmulas para convertir coordenadas 3D a 2D son:

```qbasic
x2D = 256 * (x3D / (z3D + zCentro)) + xCentro
y2D = 256 * (y3D / (z3D + zCentro)) + yCentro
```

Donde:

- `(x2D, y2D)` es el punto a dibujar en la pantalla
- `(x3D, y3D, z3D)` es el punto en el espacio 3D
- `xCentro` y `yCentro` representan el centro de la pantalla (generalmente 160,100 en modo 13h)
- `zCentro` representa la distancia del observador (generalmente 256)

El valor 256 en las ecuaciones ajusta la escala para obtener un tamaño adecuado en pantalla.

### Rotaciones 3D

Para rotar objetos en el espacio 3D, necesitamos aplicar transformaciones matemáticas a cada punto. Estas transformaciones utilizan funciones trigonométricas para calcular las nuevas posiciones.

Las fórmulas para rotar un punto en 3D son:

```qbasic
xR = -xO * SIN(θ) + yO * COS(θ)
yR = -xO * COS(θ) * SIN(φ) - yO * SIN(θ) * SIN(φ) - zO * COS(φ) + p
zR = -xO * COS(θ) * COS(φ) - yO * SIN(θ) * COS(φ) + zO * SIN(φ)
```

Donde:

- `(xO, yO, zO)` son las coordenadas originales del punto
- `(xR, yR, zR)` son las coordenadas rotadas
- `θ` (theta) es el ángulo de rotación en el plano XY
- `φ` (phi) es el ángulo desde el eje Z
- `p` es la distancia desde el centro del objeto (generalmente 1 para puntos en la superficie)

### Optimización con Tablas Trigonométricas

Las operaciones trigonométricas son costosas en términos de tiempo de procesamiento. Para optimizar, se recomienda crear tablas de senos y cosenos precalculados:

```qbasic
CONST PI = 3.141592
DIM s!(359)   ' Tabla de senos
DIM c!(359)   ' Tabla de cosenos

FOR i = 0 TO 359
   s!(i) = SIN(i * (PI / 180))
   c!(i) = COS(i * (PI / 180))
NEXT
```

Luego, en lugar de calcular `SIN(angulo)` y `COS(angulo)` cada vez, simplemente usamos `s!(angulo)` y `c!(angulo)`.

### Estructura de Datos para Objetos 3D

Para representar objetos 3D, es útil definir una estructura que contenga las coordenadas de cada punto:

```qbasic
TYPE punto3D
    x AS INTEGER    ' Coordenada X
    y AS INTEGER    ' Coordenada Y
    z AS INTEGER    ' Coordenada Z
    p AS INTEGER    ' Distancia desde el centro del objeto
END TYPE
```

### Ejemplo de Cubo Rotando en 3D

A continuación se presenta un ejemplo simplificado de un cubo rotando en 3D:

```qbasic
DEFINT A-Z

' Definir tipo para puntos 3D
TYPE pnt
    x AS INTEGER        ' Coordenada X
    y AS INTEGER        ' Coordenada Y
    z AS INTEGER        ' Coordenada Z
    p AS INTEGER        ' Distancia desde centro
END TYPE

' Número de líneas para dibujar el cubo
numLineas = 11  ' 0 a 11 = 12 líneas

' Arreglos para almacenar coordenadas
DIM lO(numLineas, 1) AS pnt      ' Coordenadas originales
DIM lR(numLineas, 1) AS pnt      ' Coordenadas rotadas
DIM scrX(numLineas, 1)           ' Coordenada X en pantalla
DIM scrY(numLineas, 1)           ' Coordenada Y en pantalla
DIM oldX(numLineas, 1)           ' Coordenada X anterior (para borrar)
DIM oldY(numLineas, 1)           ' Coordenada Y anterior (para borrar)

' Tablas trigonométricas precalculadas
DIM s!(359)
DIM c!(359)
CONST PI = 3.141592

' Crear tablas de seno y coseno
FOR i = 0 TO 359
   s!(i) = SIN(i * (PI / 180))
   c!(i) = COS(i * (PI / 180))
NEXT

' Leer coordenadas del cubo (definidas al final)
FOR i = 0 TO numLineas
   READ lO(i, 0).x, lO(i, 0).y, lO(i, 0).z, lO(i, 0).p
   READ lO(i, 1).x, lO(i, 1).y, lO(i, 1).z, lO(i, 1).p
NEXT

' Configurar modo gráfico
SCREEN 13
CLS

' Configurar parámetros iniciales
xCentro = 160: yCentro = 100: zCentro = 256
theta = 0: phi = 0
thetaRot = 2: phiRot = 2

justStarted = 1
DO
  ' Para cada línea del cubo
  FOR i = 0 TO numLineas
     ' Guardar valores anteriores para borrado
     oldX(i, 0) = scrX(i, 0): oldY(i, 0) = scrY(i, 0)
     oldX(i, 1) = scrX(i, 1): oldY(i, 1) = scrY(i, 1)
    
     ' Rotar ambos puntos de cada línea
     lR(i, 0).x = -lO(i, 0).x * s!(theta) + lO(i, 0).y * c!(theta)
     lR(i, 0).y = -lO(i, 0).x * c!(theta) * s!(phi) - lO(i, 0).y * s!(theta) * s!(phi) - lO(i, 0).z * c!(phi) + lO(i, 0).p
     lR(i, 0).z = -lO(i, 0).x * c!(theta) * c!(phi) - lO(i, 0).y * s!(theta) * c!(phi) + lO(i, 0).z * s!(phi)

     lR(i, 1).x = -lO(i, 1).x * s!(theta) + lO(i, 1).y * c!(theta)
     lR(i, 1).y = -lO(i, 1).x * c!(theta) * s!(phi) - lO(i, 1).y * s!(theta) * s!(phi) - lO(i, 1).z * c!(phi) + lO(i, 1).p
     lR(i, 1).z = -lO(i, 1).x * c!(theta) * c!(phi) - lO(i, 1).y * s!(theta) * c!(phi) + lO(i, 1).z * s!(phi)

     ' Convertir de 3D a 2D
     IF (lR(i, 0).z + zCentro) <> 0 THEN
       scrX(i, 0) = 256 * (lR(i, 0).x / (lR(i, 0).z + zCentro)) + xCentro
       scrY(i, 0) = 256 * (lR(i, 0).y / (lR(i, 0).z + zCentro)) + yCentro
     END IF

     IF (lR(i, 1).z + zCentro) <> 0 THEN
       scrX(i, 1) = 256 * (lR(i, 1).x / (lR(i, 1).z + zCentro)) + xCentro
       scrY(i, 1) = 256 * (lR(i, 1).y / (lR(i, 1).z + zCentro)) + yCentro
     END IF
  NEXT i

  ' Borrar líneas anteriores
  WAIT &H3DA, 8  ' Sincronizar con refresco vertical para evitar parpadeo
  IF justStarted = 0 THEN
    FOR i = 0 TO numLineas
       LINE (oldX(i, 0), oldY(i, 0))-(oldX(i, 1), oldY(i, 1)), 0
    NEXT i
  END IF

  ' Dibujar nuevas líneas
  FOR i = 0 TO numLineas
     LINE (scrX(i, 0), scrY(i, 0))-(scrX(i, 1), scrY(i, 1)), 11
  NEXT i
 
  ' Actualizar ángulos para siguiente fotograma
  theta = (theta + thetaRot) MOD 360
  phi = (phi + phiRot) MOD 360

  justStarted = 0
LOOP UNTIL INKEY$ = CHR$(27)  ' Salir con ESC

' Datos para las líneas del cubo: formato (X1,Y1,Z1,p1, X2,Y2,Z2,p2)
DATA -50,50,50,1,50,50,50,1
DATA 50,-50,50,1,50,50,50,1
DATA 50,50,-50,1,50,50,50,1
DATA -50,-50,50,1,-50,50,50,1
DATA -50,50,-50,1,-50,50,50,1
DATA -50,-50,50,1,50,-50,50,1
DATA -50,50,-50,1,50,50,-50,1
DATA -50,-50,-50,1,50,-50,-50,1
DATA -50,-50,-50,1,-50,50,-50,1
DATA 50,-50,-50,1,50,-50,50,1
DATA 50,-50,-50,1,50,50,-50,1
DATA -50,-50,-50,1,-50,-50,50,1
```

### Consejos para Mejorar los Gráficos 3D

1. **Utiliza sincronización vertical** (WAIT &H3DA, 8) para evitar parpadeos al redibujar objetos 3D.

2. **Implementa la eliminación de caras ocultas** para que los objetos sólidos se vean más realistas.

3. **Utiliza tablas precalculadas** para todas las operaciones trigonométricas para mejorar significativamente el rendimiento.

4. **Aumenta la complejidad gradualmente**, empezando con objetos simples como cubos antes de pasar a geometría más compleja.

5. **Considera usar técnicas de doble búfer** para lograr animaciones más suaves y sin parpadeos.

6. **Añade iluminación simple** calculando el ángulo entre una superficie y una fuente de luz ficticia para sombrear objetos 3D.
