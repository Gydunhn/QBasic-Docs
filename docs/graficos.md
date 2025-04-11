# Gráficos

QBasic ofrece capacidades gráficas sorprendentemente potentes para su época. Este capítulo cubre desde los comandos básicos de dibujo hasta técnicas avanzadas para trabajar con sprites y cargar gráficos externos.

## Modos de Pantalla

El comando `SCREEN` establece el modo gráfico:

```qbasic
SCREEN 0  ' Modo texto (80x25 caracteres)
SCREEN 1  ' Baja resolución (320x200, 4 colores)
SCREEN 2  ' Media resolución (640x200, 2 colores)
SCREEN 7  ' Media resolución (320x200, 16 colores) - EGA
SCREEN 8  ' Alta resolución (640x200, 16 colores)
SCREEN 9  ' Alta resolución (640x350, 16 colores) - EGA
SCREEN 12 ' Alta resolución (640x480, 16 colores) - VGA
SCREEN 13 ' Modo 256 colores (320x200) - VGA
```

Para este capítulo, usaremos principalmente el modo 7 (EGA 16 colores) para animación debido a su velocidad y soporte para múltiples páginas, y el modo 13 (VGA 256 colores) para gráficos más detallados.

## Comandos Básicos de Dibujo

### PSET

El comando `PSET` dibuja un punto en las coordenadas especificadas:

```qbasic
SCREEN 13
PSET (160, 100), 12  ' Dibuja un pixel rojo en el centro de la pantalla
```

### LINE

El comando `LINE` dibuja líneas y rectángulos:

```qbasic
' Línea simple
LINE (10, 10)-(100, 50), 14  ' Línea amarilla

' Rectángulo
LINE (50, 50)-(150, 100), 10, B  ' Rectángulo verde

' Rectángulo relleno
LINE (200, 50)-(250, 100), 9, BF ' Rectángulo azul relleno
```

### CIRCLE

El comando `CIRCLE` dibuja círculos y elipses:

```qbasic
' Círculo simple
CIRCLE (160, 100), 50, 4  ' Círculo rojo con radio 50

' Elipse
CIRCLE (160, 100), 50, 5, , , 0.5  ' Elipse

' Arco
CIRCLE (160, 100), 40, 12, 0, 3.14  ' Medio círculo
```

### PAINT

El comando `PAINT` rellena un área cerrada con un color:

```qbasic
CIRCLE (160, 100), 50, 15
PAINT (160, 100), 4, 15  ' Rellena el círculo
```

## Trabajando con Sprites (Imágenes)

### Usando Arrays para Almacenar Gráficos

Para trabajar con sprites en QBasic, necesitamos almacenarlos en arrays. La fórmula para calcular el tamaño del array es:

```text
Ancho * Alto / 2 + 1 = TamañoArray
```

Por ejemplo, para un sprite de 16x16 píxeles:

```qbasic
' 16 * 16 / 2 + 1 = 129
DIM SHARED Sprite%(129)
```

### GET y PUT

Los comandos `GET` y `PUT` permiten capturar y dibujar áreas de la pantalla:

```qbasic
' Dibujar algo (un círculo por ejemplo)
CIRCLE (20, 20), 8, 14
PAINT (20, 20), 14, 14

' Guardar en el array
GET (12, 12)-(28, 28), Sprite%

' Limpiar la pantalla 
CLS

' Dibujar el sprite en otra posición
PUT (100, 100), Sprite%, PSET
```

Opciones del comando `PUT`:

- `PSET`: Dibuja el sprite como está (reemplaza lo que hay)
- `PRESET`: Invierte los colores
- `AND`: Operación AND con lo que ya está en pantalla
- `OR`: Operación OR con lo que ya está en pantalla
- `XOR`: Operación XOR (útil para borrar el sprite más tarde)

## Guía Completa sobre Sprites (por Ted Felix)

### ¿Qué son los sprites?

Los sprites son pequeñas imágenes que pueden moverse por la pantalla y animarse. Al usar sprites, se pueden crear juegos con una apariencia más realista. Los sprites son bidimensionales (planos), pero si tienes habilidad para dibujar, puedes hacerlos parecer que tienen profundidad.

Por ejemplo, "The Legend of Zelda" de Nintendo es un juego completamente basado en sprites, y quizás uno de los mejores. Link y los enemigos son claramente sprites bidimensionales planos. Sin embargo, no hay razón para limitarse a un aspecto 2D cuando puedes simular un aspecto 3D. "Age of Empires" es un buen ejemplo de sprites que parecen tridimensionales.

### Creando un Sprite

Como los sprites son pequeñas imágenes, necesitamos una imagen para crear un sprite. Hay muchas formas de hacerlo. La forma más simple es dibujar primero la imagen en la pantalla usando las instrucciones gráficas de QBasic:

```qbasic
SCREEN 13
CLS
CIRCLE (4, 3), 4, 4    ' Círculo con borde rojo
PAINT (4, 3), 12, 4    ' Relleno rojo claro
```

Una vez que tenemos la imagen en la pantalla, podemos usar `GET` para copiarla a un array:

```qbasic
DIM Ball%(37)          ' Reservar espacio (37 enteros)
GET (0, 0)-(8, 7), Ball%  ' Copiar de pantalla al array
```

¿Cómo sabemos que necesitamos 37 elementos? Existe una fórmula para calcularlo, pero para sprites pequeños podemos usar prueba y error. QBasic mostrará un error si el array es demasiado pequeño. Ver el tema de Ayuda "Screen Image Arrays and Compatibility" para la fórmula.

Una vez almacenado el sprite en el array, podemos dibujarlo donde queramos con `PUT`:

```qbasic
PUT (160, 100), Ball%  ' Dibujar en el centro de la pantalla
```

### Moviendo un Sprite

Para animar un sprite, seguimos el mismo principio que en cualquier juego 2D. Mantenemos un registro de la posición X e Y del sprite, y las cambiamos con el tiempo:

```qbasic
DEFINT A-Z        ' Hace que las cosas sean más rápidas
SCREEN 13
CLS

' Dibujar un círculo para nuestro sprite
CIRCLE (4, 3), 4, 4
PAINT (4, 3), 12, 4

DIM Ball%(37)
GET (0, 0)-(8, 7), Ball%
CLS

' Tamaño de pantalla
CONST XMax = 320
CONST YMax = 200

' Posición inicial
X = 160
Y = 100

' Movimiento
DX = 1
DY = 1

DO
  ' Dibujar el sprite en su posición actual
  PUT (X, Y), Ball%

  ' Guardar posición anterior
  PX = X
  PY = Y

  ' Cambiar la posición
  X = X + DX
  Y = Y + DY

  ' Detectar colisiones con los bordes
  IF X > XMax - 10 THEN DX = -1
  IF X < 1 THEN DX = 1
  IF Y > YMax - 9 THEN DY = -1
  IF Y < 1 THEN DY = 1

  ' Bucle de temporización (ajustar según velocidad del PC)
  FOR I = 1 TO 30000: NEXT I

  ' Borrar el sprite antes de redibujarlo
  PUT (PX, PY), Ball%

LOOP WHILE INKEY$ = ""
```

Lo más extraño de este ejemplo es cómo usa `PUT` tanto para dibujar como para borrar el sprite. Esto funciona porque `PUT` dibuja nuestro sprite usando "exclusive or" (XOR), lo que permite eliminar la imagen aplicándolo dos veces.

### Sprites más Interesantes con Mapas de Bits

Usar `CIRCLE` y `PAINT` es rápido, pero es difícil dibujar cosas realmente interesantes. Para eso, necesitamos un mapa de bits (bitmap). Un mapa de bits es una imagen almacenada de una manera que la computadora puede entender, generalmente como números:

```qbasic
DATA  0,15, 0
DATA 15, 0,15
DATA  0,15, 0
```

Es como colorear por números. Donde hay un 0, habrá un punto negro (píxel) en la pantalla. Donde hay un 15, habrá un píxel blanco. Este mapa de bits está organizado para ser 3 píxeles de ancho por 3 de alto.

```qbasic
SCREEN 13
CLS

FOR Y = 0 TO 2
  FOR X = 0 TO 2
    READ DotColor
    PSET (X, Y), DotColor
  NEXT X
NEXT Y
```

Esto lee el mapa de bits desde las sentencias DATA y lo dibuja en pantalla. Se verá un pequeño diamante en la esquina superior izquierda.

Los mapas de bits pueden ser tan grandes como necesitemos. Aquí hay un ejemplo de 15x15 píxeles que podría ser un pequeño personaje:

```qbasic
DATA 00,00,00,00,00,00,00,00,00,00,12,12,00,00,00
DATA 00,00,00,00,00,00,00,00,00,00,15,15,00,00,00
DATA 00,00,00,00,00,00,00,00,00,15,15,15,15,00,00
DATA 00,00,00,00,00,00,00,00,00,15,15,00,15,00,00
DATA 00,00,00,00,00,00,00,00,15,15,15,15,15,14,00
' ... (resto del bitmap)
```

### Problemas con PUT y Alternativas

Aunque `PUT` es muy útil, tiene varias limitaciones importantes:

1. **No recorta**: Si intentas dibujar un sprite demasiado cerca del borde de la pantalla, QBasic detendrá el programa con un error.

2. **Problemas con XOR**: Aunque XOR es excelente para dibujar y borrar usando el mismo mapa de bits, causa problemas con fondos que no son negros, cambiando los colores del sprite.

3. **Forma rectangular**: Los sprites con `GET` y `PUT` solo pueden ser rectangulares.

Estas limitaciones nos llevan a crear nuestra propia versión de `PUT` que pueda manejar transparencia y recorte:

```qbasic
SUB PUT2 (Sprite() AS INTEGER, XSize AS INTEGER, YSize AS INTEGER, X AS INTEGER, Y AS INTEGER)
  ' Dibujar el sprite desde el array a la pantalla
  FOR YP = 0 TO YSize - 1
    FOR XP = 0 TO XSize - 1
      PSET (X + XP, Y + YP), Sprite%(XP, YP)
    NEXT XP
  NEXT YP
END SUB
```

### Cambio de Página (Page Flipping)

Una técnica fundamental para evitar el parpadeo en los juegos es el cambio de página o doble búfer. Esto permite dibujar en una pantalla invisible mientras el usuario ve otra, y luego intercambiarlas.

```qbasic
SCREEN 9, 0, 0, 1  ' Modo EGA: trabajamos en pág 0, mostramos pág 1

' Dibujamos en la página invisible (0)
CLS
' ... (dibujar aquí) ...

' Mostramos la página completa
PCOPY 0, 1  ' Copia pág 0 (trabajo) a pág 1 (visible)
```

Desafortunadamente, el cambio de página con `PCOPY` solo está disponible en algunos modos de pantalla (7-9), no en el modo 13 (256 colores). Para estos casos, podemos crear nuestro propio sistema de doble búfer usando un array grande y `PUT`.

### Transparencia

Para hacer que partes de un sprite sean transparentes, podemos usar un valor especial en el mapa de bits que indique "no dibujar nada aquí":

```qbasic
SUB PUT2Transparent (Sprite() AS INTEGER, XSize AS INTEGER, YSize AS INTEGER, X AS INTEGER, Y AS INTEGER)
  FOR YP = 0 TO YSize - 1
    FOR XP = 0 TO XSize - 1
      IF Sprite%(XP, YP) <> 0 THEN  ' 0 indica transparencia
        PSET (X + XP, Y + YP), Sprite%(XP, YP)
      END IF
    NEXT XP
  NEXT YP
END SUB
```

### Recorte (Clipping)

Para evitar errores cuando los sprites se dibujan cerca de los bordes, debemos verificar los límites:

```qbasic
SUB PUT2WithClipping (Sprite() AS INTEGER, XSize AS INTEGER, YSize AS INTEGER, X AS INTEGER, Y AS INTEGER)
  FOR YP = 0 TO YSize - 1
    IF Y + YP >= 0 AND Y + YP < 200 THEN  ' Verificar límite Y
      FOR XP = 0 TO XSize - 1
        IF X + XP >= 0 AND X + XP < 320 THEN  ' Verificar límite X
          IF Sprite%(XP, YP) <> 0 THEN  ' Transparencia
            PSET (X + XP, Y + YP), Sprite%(XP, YP)
          END IF
        END IF
      NEXT XP
    END IF
  NEXT YP
END SUB
```

Con estas técnicas, podemos crear animaciones suaves y visualmente atractivas que formen la base de juegos en QBasic.

## Creación de Mapas de Tiles

Una de las técnicas más utilizadas en juegos de QBasic es la creación de niveles usando "tiles" (baldosas o azulejos). Esta técnica permite crear grandes escenarios usando piezas pequeñas reutilizables.

### Conceptos Básicos de Tiles

Los tiles son pequeños sprites (generalmente cuadrados) que se colocan en posiciones específicas para formar un mapa o nivel. Ventajas:

- Ahorro de memoria: solo necesitas definir cada tile una vez
- Facilidad de edición: puedes crear niveles rápidamente
- Reusabilidad: puedes crear muchos niveles con los mismos tiles

### Dimensionando Arrays para un Sistema de Tiles

Para crear un sistema de tiles, necesitas dos tipos de arrays:

1. **Arrays de tiles**: Para almacenar cada tipo de tile
2. **Array de mapa**: Para guardar la disposición de los tiles

```qbasic
' Dimensionar arrays para tiles (10x10 píxeles cada uno)
DIM SueloTile(100)
DIM ParedTile(100)
DIM AguaTile(100)

' Dimensionar array para mapa (21x15 tiles)
DIM Mapa(21, 15)
```

### Creando los Tiles

Para cada tile, usamos la técnica de DATA y GET:

```qbasic
SCREEN 13

' Primer tile: suelo
FOR y = 1 TO 10
    FOR x = 1 TO 10
        READ color
        PSET (x, y), color
    NEXT x
NEXT y
GET (1, 1)-(10, 10), SueloTile

' Tile de suelo (arena marrón clara)
DATA 6,6,6,6,6,6,6,6,6,6
DATA 6,6,6,6,6,6,6,6,6,6
DATA 6,6,14,6,6,6,6,6,6,6
DATA 6,6,6,6,6,6,14,6,6,6
DATA 6,6,6,6,6,6,6,6,6,6
DATA 6,6,6,6,14,6,6,6,6,6
DATA 6,6,6,6,6,6,6,6,6,6
DATA 6,6,14,6,6,6,6,6,6,6
DATA 6,6,6,6,6,6,6,14,6,6
DATA 6,6,6,6,6,6,6,6,6,6

' Segundo tile: pared
FOR y = 1 TO 10
    FOR x = 1 TO 10
        READ color
        PSET (x, y), color
    NEXT x
NEXT y
GET (1, 1)-(10, 10), ParedTile

' Tile de pared (piedra gris)
DATA 8,7,8,7,8,7,8,7,8,7
DATA 7,8,7,8,7,8,7,8,7,8
DATA 8,7,8,7,8,7,8,7,8,7
DATA 7,8,7,8,7,8,7,8,7,8
DATA 8,7,8,7,8,7,8,7,8,7
DATA 7,8,7,8,7,8,7,8,7,8
DATA 8,7,8,7,8,7,8,7,8,7
DATA 7,8,7,8,7,8,7,8,7,8
DATA 8,7,8,7,8,7,8,7,8,7
DATA 7,8,7,8,7,8,7,8,7,8

' Tercer tile: agua
FOR y = 1 TO 10
    FOR x = 1 TO 10
        READ color
        PSET (x, y), color
    NEXT x
NEXT y
GET (1, 1)-(10, 10), AguaTile

' Tile de agua (azul)
DATA 1,1,1,1,1,1,1,1,1,1
DATA 1,1,1,1,1,1,1,1,1,1
DATA 1,1,1,1,9,9,1,1,1,1
DATA 1,1,1,9,9,9,9,1,1,1
DATA 1,1,9,9,9,9,9,9,1,1
DATA 1,1,9,9,9,9,9,9,1,1
DATA 1,1,1,9,9,9,9,1,1,1
DATA 1,1,1,1,9,9,1,1,1,1
DATA 1,1,1,1,1,1,1,1,1,1
DATA 1,1,1,1,1,1,1,1,1,1

CLS
```

### Cargando el Diseño del Mapa

A continuación, cargamos la disposición de los tiles en el array del mapa:

```qbasic
' Cargar diseño de mapa desde DATA
FOR y = 1 TO 15
    FOR x = 1 TO 21
        READ Mapa(x, y)
    NEXT x
NEXT y

' Datos del mapa (1=Suelo, 2=Pared, 3=Agua)
DATA 2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2
DATA 2,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,2,2,2,2,2,2,2,2,2,2,2
DATA 2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2
DATA 2,2,2,2,2,1,2,2,2,2,2,2,2,2,2,2,1,2,2,2,2
DATA 2,1,1,1,1,1,1,1,2,3,3,3,3,3,3,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,1,3,3,3,3,3,3,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,2,3,3,3,3,3,3,1,1,1,1,1,2
DATA 2,1,1,1,1,1,1,1,2,3,3,3,3,3,3,1,1,1,1,1,2
DATA 2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2
```

### Dibujando el Mapa

Finalmente, recorremos el array de mapa y dibujamos cada tile en su posición correspondiente:

```qbasic
' Dibujar el mapa en pantalla
FOR y = 1 TO 15
    FOR x = 1 TO 21
        ' Calcular posición en píxeles
        pixelX = (x - 1) * 10
        pixelY = (y - 1) * 10
        
        ' Determinar qué tile dibujar según el valor en el mapa
        SELECT CASE Mapa(x, y)
            CASE 1: PUT (pixelX, pixelY), SueloTile, PSET
            CASE 2: PUT (pixelX, pixelY), ParedTile, PSET
            CASE 3: PUT (pixelX, pixelY), AguaTile, PSET
        END SELECT
    NEXT x
NEXT y
```

### Integrando un Personaje en el Mapa de Tiles

Para añadir un personaje que pueda moverse por el mapa:

```qbasic
' Crear sprite de personaje (ejemplo simplificado)
DIM Personaje(100)
CLS
CIRCLE (5, 5), 4, 12
PAINT (5, 5), 12, 12
GET (1, 1)-(10, 10), Personaje

' Posición inicial del personaje
personajeX = 5
personajeY = 5

' Dibujar mapa inicial
GOSUB DibujarMapa

' Bucle principal
DO
    k$ = INKEY$
    IF k$ <> "" THEN
        ' Guardar posición anterior
        oldX = personajeX
        oldY = personajeY
        
        ' Actualizar posición según tecla
        SELECT CASE k$
            CASE CHR$(0) + "H": personajeY = personajeY - 1  ' Arriba
            CASE CHR$(0) + "P": personajeY = personajeY + 1  ' Abajo
            CASE CHR$(0) + "K": personajeX = personajeX - 1  ' Izquierda
            CASE CHR$(0) + "M": personajeX = personajeX + 1  ' Derecha
            CASE CHR$(27): EXIT DO  ' ESC para salir
        END SELECT
        
        ' Comprobar colisiones con paredes y agua
        IF Mapa(personajeX, personajeY) = 2 OR Mapa(personajeX, personajeY) = 3 THEN
            ' Restaurar posición anterior si hay colisión
            personajeX = oldX
            personajeY = oldY
        ELSE
            ' Redibujar el tile en la posición anterior
            pixelX = (oldX - 1) * 10
            pixelY = (oldY - 1) * 10
            SELECT CASE Mapa(oldX, oldY)
                CASE 1: PUT (pixelX, pixelY), SueloTile, PSET
                CASE 2: PUT (pixelX, pixelY), ParedTile, PSET
                CASE 3: PUT (pixelX, pixelY), AguaTile, PSET
            END SELECT
            
            ' Dibujar personaje en nueva posición
            pixelX = (personajeX - 1) * 10
            pixelY = (personajeY - 1) * 10
            PUT (pixelX, pixelY), Personaje, PSET
        END IF
    END IF
LOOP

' Subrutina para dibujar todo el mapa
DibujarMapa:
FOR y = 1 TO 15
    FOR x = 1 TO 21
        pixelX = (x - 1) * 10
        pixelY = (y - 1) * 10
        SELECT CASE Mapa(x, y)
            CASE 1: PUT (pixelX, pixelY), SueloTile, PSET
            CASE 2: PUT (pixelX, pixelY), ParedTile, PSET
            CASE 3: PUT (pixelX, pixelY), AguaTile, PSET
        END SELECT
    NEXT x
NEXT y
RETURN
```

### Ampliando el Sistema de Tiles

Puedes mejorar el sistema de tiles añadiendo características como:

1. **Tiles animados**: Cambia el sprite de un tile en cada frame
2. **Propiedades por tile**: Define comportamientos especiales (daño, curación, etc.)
3. **Múltiples capas**: Una capa para el suelo, otra para objetos, etc.
4. **Scrolling**: Para mapas más grandes que la pantalla

```qbasic
' Ejemplo de scroll en un mapa grande (simplificado)
' Asumimos Mapa(100, 100) para un mapa grande

' Coordenadas de la "cámara"
cameraX = 0
cameraY = 0

SUB DibujarMapaConScroll
    ' Solo dibuja la parte visible del mapa
    FOR y = 1 TO 15
        FOR x = 1 TO 21
            mapX = x + cameraX
            mapY = y + cameraY
            
            ' Verificar que estamos dentro del mapa
            IF mapX >= 1 AND mapX <= 100 AND mapY >= 1 AND mapY <= 100 THEN
                pixelX = (x - 1) * 10
                pixelY = (y - 1) * 10
                
                SELECT CASE Mapa(mapX, mapY)
                    CASE 1: PUT (pixelX, pixelY), SueloTile, PSET
                    CASE 2: PUT (pixelX, pixelY), ParedTile, PSET
                    CASE 3: PUT (pixelX, pixelY), AguaTile, PSET
                END SELECT
            END IF
        NEXT x
    NEXT y
END SUB
```

## Cargando GIFs y Paletas Personalizadas

QBasic no puede cargar GIFs directamente, pero podemos usar programas auxiliares o técnicas como BSAVE/BLOAD:

```qbasic
' Cambiar la paleta de colores
SUB CargarPaleta (fichero$)
    filenum% = FREEFILE
    OPEN fichero$ FOR INPUT AS #filenum%
    FOR Color% = 0 TO 255
        INPUT #filenum%, r, g, b
        OUT &H3C8, Color%
        OUT &H3C9, r
        OUT &H3C9, g
        OUT &H3C9, b
    NEXT Color%
    CLOSE #filenum%
END SUB

' Cargar una imagen BSAVE desde QBasic
SUB CargarImagen (fichero$)
    DEF SEG = &HA000
    BLOAD fichero$, 0
END SUB
```

## Manipulación de Paletas en QBasic

QBasic ofrece potentes capacidades para trabajar con paletas de colores, lo que permite crear efectos visuales impresionantes incluso con las limitaciones gráficas de la época.

### Conceptos Básicos de Paletas

En los modos gráficos de QBasic, especialmente en el modo 13 (320x200 con 256 colores), cada color en pantalla está definido por un valor en la paleta. Esta paleta es esencialmente un array de colores donde cada entrada contiene valores RGB (Rojo, Verde, Azul).

### El Comando PALETTE

La función `PALETTE` permite cambiar los valores de color en la paleta:

```qbasic
PALETTE color, valor
```

Donde:

- `color` es el índice del color a cambiar (0-255 en modo 13)
- `valor` es el nuevo valor RGB calculado con la fórmula:

```texto
valor = 65536 * Azul + 256 * Verde + Rojo
```

Los valores de Rojo, Verde y Azul pueden variar de 0 a 63, donde 0 es ausencia del color y 63 es la intensidad máxima.

Ejemplo básico:

```qbasic
SCREEN 13
PALETTE 15, 63     ' Cambia el color 15 a rojo puro
PRINT "Este texto es rojo!"
```

### Ejemplo de Transición de Colores

```qbasic
SCREEN 13
PRINT "Color 15 cambiando constantemente!"

Azul = 63: Verde = 0: Rojo = 0

DO
  ' Transición de azul a rojo
  FOR Azul = 63 TO 0 STEP -1
     Rojo = 63 - Azul
     valor = 65536 * Azul + 256 * Verde + Rojo
     PALETTE 15, valor
     FOR Delay = 1 TO 500: NEXT
  NEXT
  
  ' Transición de rojo a verde
  FOR Rojo = 63 TO 0 STEP -1
     Verde = 63 - Rojo
     valor = 65536 * Azul + 256 * Verde + Rojo
     PALETTE 15, valor
     FOR Delay = 1 TO 500: NEXT
  NEXT
  
  ' Transición de verde a azul
  FOR Verde = 63 TO 0 STEP -1
     Azul = 63 - Verde
     valor = 65536 * Azul + 256 * Verde + Rojo
     PALETTE 15, valor
     FOR Delay = 1 TO 500: NEXT
  NEXT
LOOP UNTIL INKEY$ <> ""
```

### Optimización con Acceso Directo a Puertos

Para manipulaciones avanzadas de paleta, el comando `PALETTE` puede resultar lento. Una alternativa más eficiente es acceder directamente a los puertos de la tarjeta VGA:

```qbasic
' Cambiar el color 15 a púrpura usando puertos
OUT &H3C8, 15     ' Indicar qué color vamos a modificar
OUT &H3C9, 63     ' Componente Rojo (0-63)
OUT &H3C9, 0      ' Componente Verde (0-63)
OUT &H3C9, 63     ' Componente Azul (0-63)
```

Para leer los valores RGB de un color:

```qbasic
' Leer los valores RGB del color 15
OUT &H3C8, 15      ' Seleccionar el color
Rojo = INP(&H3C9)  ' Leer componente Rojo
Verde = INP(&H3C9) ' Leer componente Verde
Azul = INP(&H3C9)  ' Leer componente Azul
```

### Aplicaciones de la Manipulación de Paletas

#### Fundidos a Negro o Blanco

```qbasic
' Fundido a negro de toda la paleta
SCREEN 13
' Dibujamos algo
LINE (0, 0)-(319, 199), 15, BF
CIRCLE (160, 100), 50, 4
PAINT (160, 100), 12, 4

' Guardar la paleta original
DIM PaletaOriginal(0 TO 255, 0 TO 2)
FOR C = 0 TO 255
    OUT &H3C8, C
    PaletaOriginal(C, 0) = INP(&H3C9) ' Rojo
    PaletaOriginal(C, 1) = INP(&H3C9) ' Verde
    PaletaOriginal(C, 2) = INP(&H3C9) ' Azul
NEXT C

' Fundido a negro
FOR Intensidad = 63 TO 0 STEP -1
    FOR C = 0 TO 255
        OUT &H3C8, C
        R = PaletaOriginal(C, 0) * Intensidad \ 63
        G = PaletaOriginal(C, 1) * Intensidad \ 63
        B = PaletaOriginal(C, 2) * Intensidad \ 63
        OUT &H3C9, R
        OUT &H3C9, G
        OUT &H3C9, B
    NEXT C
    
    ' Pequeña pausa
    FOR Delay = 1 TO 100: NEXT Delay
NEXT Intensidad
```

#### Efectos de Rotación de Paleta

La rotación de paleta es una técnica que permite crear ilusión de movimiento sin redibujar los píxeles:

```qbasic
' Efecto de agua con rotación de paleta
SCREEN 13

' Crear una gama de azules
FOR C = 16 TO 31
    OUT &H3C8, C
    OUT &H3C9, 0
    OUT &H3C9, 0
    OUT &H3C9, 32 + C
NEXT C

' Dibujar "agua" con esos colores
FOR Y = 100 TO 199
    FOR X = 0 TO 319
        ' Patrón ondulante
        Color = 16 + (SIN(X / 20) * 7 + SIN(Y / 10) * 7) MOD 16
        PSET (X, Y), Color
    NEXT X
NEXT Y

' Rotar la paleta para crear efecto de movimiento
DO
    ' Guardar el primer color
    OUT &H3C8, 16
    R = INP(&H3C9)
    G = INP(&H3C9)
    B = INP(&H3C9)
    
    ' Desplazar todos los colores
    FOR C = 16 TO 30
        OUT &H3C8, C + 1
        RN = INP(&H3C9)
        GN = INP(&H3C9)
        BN = INP(&H3C9)
        
        OUT &H3C8, C
        OUT &H3C9, RN
        OUT &H3C9, GN
        OUT &H3C9, BN
    NEXT C
    
    ' El último color recibe el valor del primero
    OUT &H3C8, 31
    OUT &H3C9, R
    OUT &H3C9, G
    OUT &H3C9, B
    
    ' Control de velocidad
    FOR Delay = 1 TO 200: NEXT Delay
LOOP UNTIL INKEY$ <> ""
```

### Nota Técnica

Al leer valores de paleta mediante puertos, algunas tarjetas VGA pueden presentar un error donde el registro solicitado no corresponde al obtenido. Una solución para este problema es leer cada registro de color dos veces.

## Tutorial de Paletas VGA por Brian Bartels

### Conceptos de Paletas

¿Has notado alguna vez, mientras creas gráficos para algún juego en QBasic, que la paleta VGA predeterminada es bastante limitada? ¿Te has preguntado si puedes cambiar esos colores? Efectivamente, es posible y es un proceso muy simple.

Lo primero que debes entender es que cada color que tu monitor puede mostrar está compuesto por los tres colores primarios: rojo, verde y azul (RGB). Para cambiar los valores RGB de un color específico, solo necesitas usar este comando:

```qbasic
PALETTE color, rojo+(verde*256)+(azul*65536)
```

Los valores de rojo, verde y azul pueden variar entre 0 y 63. ¡Es así de simple!

### Temas Avanzados

Después de experimentar con el comando PALETTE, probablemente hayas notado que es extremadamente lento. La instrucción PALETTE de QBasic es lenta por dos razones:

1. Tiene que realizar todas esas operaciones matemáticas (`rojo+verde*256+azul*65536`) cada vez que la usas.
2. Cada vez que la usas, espera a que la pantalla se refresque, lo que hace que esta instrucción sea totalmente inutilizable para hacer fundidos de entrada, salida o entre paletas.

¿Cómo solucionamos este problema? Simplemente escribiendo nuestra propia subrutina PALETTE que no realice todos esos cálculos y no espere a que la pantalla se refresque. Esto se puede lograr fácilmente con cuatro simples comandos OUT:

```qbasic
SUB setpal (col%, r%, g%, b%)
    OUT &H3C8, col%
    OUT &H3C9, r%
    OUT &H3C9, g%
    OUT &H3C9, b%
END SUB
```

### Creando Gradientes de Colores

Con una rutina de paleta rápida, podemos hacer cosas interesantes como fundir toda la pantalla o crear transiciones entre paletas. Crear gradientes entre colores es más simple de lo que parece.

Pensemos en cada color como tres valores: rojo, verde y azul. Veamos cómo generar un gradiente entre dos colores:

Supongamos que el color número 26 es rojo brillante (su valor rojo es 63 y sus valores verde y azul son 0), y el color 65 es azul brillante (su valor azul es 63 y sus valores rojo y verde son 0).

Queremos que todos los colores entre el 26 y el 65 formen un gradiente. Primero, almacenamos los valores RGB del primer color (26) en tres variables: `r1%`, `g1%` y `b1%`. También almacenamos los valores RGB del segundo color (65) en tres variables: `r2%`, `g2%` y `b2%`.

Luego calculamos cuántos pasos necesitamos en el gradiente (65-26) y cuánto debemos incrementar los valores RGB en cada paso:

```qbasic
numofsteps% = 65 - 26
rinc# = (r2% - r1%) / numofsteps%
ginc# = (g2% - g1%) / numofsteps%
binc# = (b2% - b1%) / numofsteps%
```

Finalmente, utilizamos un bucle FOR para añadir estos incrementos a los colores en nuestro gradiente secuencialmente:

```qbasic
r# = r1%
g# = g1%
b# = b1%
FOR col% = 27 TO 64
   r# = r# + rinc#
   g# = g# + ginc#
   b# = b# + binc#
   setpal col%, INT(r#), INT(g#), INT(b#)
NEXT
```

### Fundidos de Paleta

Para realizar fundidos completos de paleta, aplicamos el mismo principio pero a todos los colores. Necesitamos varios arrays:

```qbasic
DIM startpal(2, 255) AS INTEGER    ' Paleta inicial
DIM endpal(2, 255) AS INTEGER      ' Paleta final
DIM pal(2, 255) AS DOUBLE          ' Paleta actual
DIM palinc(2, 255) AS DOUBLE       ' Incrementos
```

Estos arrays se utilizan así:

- `startpal(0, col%)` = valor rojo del color col%
- `startpal(1, col%)` = valor verde del color col%
- `startpal(2, col%)` = valor azul del color col%

Para hacer un fundido de entrada, simplemente hacemos que todos los valores de `startpal` sean cero y que `endpal` sea la paleta que queremos mostrar. Para un fundido de salida, lo hacemos al revés. Para fundir de una paleta a otra, hacemos que `startpal` sea igual a la paleta actual y `endpal` sea la paleta a la que queremos cambiar.

El número de pasos que utilices determinará la velocidad del fundido:

```qbasic
steps% = 50

' Configurar startpal y endpal aquí

' Calcular incrementos
FOR col% = 0 TO 255
   FOR j% = 0 TO 2
      palinc(j%, col%) = (endpal(j%, col%) - startpal(j%, col%)) / steps%
   NEXT
NEXT

' Inicializar paleta actual
FOR col% = 0 TO 255
   FOR j% = 0 TO 2
      pal(j%, col%) = startpal(j%, col%)
   NEXT
NEXT

' Realizar el fundido
FOR q% = 0 TO steps%
   FOR col% = 0 TO 255
      FOR j% = 0 TO 2
         pal(j%, col%) = pal(j%, col%) + palinc(j%, col%)
      NEXT
   NEXT
   
   ' Aplicar la paleta actual
   FOR col% = 0 TO 255
      setpal col%, INT(pal(0, col%)), INT(pal(1, col%)), INT(pal(2, col%))
   NEXT
NEXT
```

### Ejemplo Completo: Fundido entre Dos Paletas

```qbasic
SCREEN 13
DIM startpal(2, 255) AS INTEGER
DIM endpal(2, 255) AS INTEGER
DIM pal(2, 255) AS DOUBLE
DIM palinc(2, 255) AS DOUBLE

' Guardar paleta actual
FOR col% = 0 TO 255
    OUT &H3C8, col%
    startpal(0, col%) = INP(&H3C9)  ' Rojo
    startpal(1, col%) = INP(&H3C9)  ' Verde
    startpal(2, col%) = INP(&H3C9)  ' Azul
NEXT

' Crear una paleta de destino (ejemplo: predominantemente roja)
FOR col% = 0 TO 255
    endpal(0, col%) = 63 * (col% MOD 64) / 63  ' Rojo
    endpal(1, col%) = 31 * (col% MOD 64) / 63  ' Verde
    endpal(2, col%) = 20 * (col% MOD 64) / 63  ' Azul
NEXT

' Dibujar algo en pantalla
LINE (0, 0)-(319, 199), 15, BF
CIRCLE (160, 100), 80, 4
PAINT (160, 100), 9, 4

' Realizar fundido
steps% = 30
FOR col% = 0 TO 255
   FOR j% = 0 TO 2
      palinc(j%, col%) = (endpal(j%, col%) - startpal(j%, col%)) / steps%
   NEXT
NEXT

FOR col% = 0 TO 255
   FOR j% = 0 TO 2
      pal(j%, col%) = startpal(j%, col%)
   NEXT
NEXT

FOR q% = 0 TO steps%
   FOR col% = 0 TO 255
      FOR j% = 0 TO 2
         pal(j%, col%) = pal(j%, col%) + palinc(j%, col%)
      NEXT
   NEXT
   
   FOR col% = 0 TO 255
      OUT &H3C8, col%
      OUT &H3C9, INT(pal(0, col%))
      OUT &H3C9, INT(pal(1, col%))
      OUT &H3C9, INT(pal(2, col%))
   NEXT
   
   ' Pequeña pausa para ver el efecto
   FOR delay% = 1 TO 1000: NEXT
NEXT
```

Con estas técnicas, puedes crear efectos visuales impresionantes en QBasic mediante la manipulación de paletas.

## Ejemplo Completo: Editor de Tiles Simple

```qbasic
SCREEN 13
DIM Tiles%(10, 129)  ' 10 tiles diferentes
DIM Map%(20, 15)     ' Mapa de 20x15 tiles

' Cargar tiles (simplificado, normalmente usarías DATA o archivos)
FOR t = 1 TO 10
    CIRCLE (8, 8), t, t + 5
    PAINT (8, 8), t + 1, t + 5
    GET (0, 0)-(15, 15), Tiles%(t, 0)
    CLS
NEXT t

' Inicializar mapa
FOR y = 0 TO 14
    FOR x = 0 TO 19
        Map%(x, y) = 1  ' Tile por defecto
    NEXT x
NEXT y

' Dibujar mapa
SUB DibujarMapa
    FOR y = 0 TO 14
        FOR x = 0 TO 19
            PUT (x * 16, y * 16), Tiles%(Map%(x, y), 0), PSET
        NEXT x
    NEXT y
END SUB

' Bucle principal (simplificado)
DibujarMapa
DO
    k$ = INKEY$
    IF k$ <> "" THEN
        IF ASC(k$) = 27 THEN EXIT DO  ' ESC para salir
    END IF
LOOP
```

## Recursos Adicionales

Para aprender más sobre gráficos en QBasic:

- Explora ejemplos de juegos en [Pete's QBasic Site](http://www.petesqbsite.com/)
- Consulta tutoriales específicos sobre sprites y animación
- Prueba QB64 para acceder a capacidades gráficas mejoradas en sistemas modernos

## Efectos Avanzados

Esta sección cubre técnicas avanzadas para crear efectos gráficos impresionantes en QBasic, los cuales pueden utilizarse en demos, juegos o para explorar conceptos matemáticos de manera visual.

### Plasma con Ondas Sinusoidales

Uno de los efectos más llamativos que se pueden crear en QBasic es el plasma. Aunque estrictamente hablando puede no ser un "plasma" físico real, este efecto produce patrones coloridos y fluidos que se asemejan a un plasma, especialmente cuando se combinan con rotación de paleta.

#### Fundamentos Matemáticos: La Onda Sinusoidal

La base de este efecto es la función seno (SIN). En QBasic, la función SIN opera en radianes y devuelve valores en el rango de -1 a 1. Para nuestros propósitos, necesitamos convertir este rango a valores útiles para colores o coordenadas.

Para convertir de grados a radianes:

```qbasic
Radianes = Grados * 0.017453  ' Aproximadamente Grados * PI / 180
```

Para ajustar el rango de -1 a 1 a un rango de 0 a 100 (por ejemplo):

```qbasic
ValorAjustado = (SIN(Radianes) * 50) + 50
```

#### Dibujando una Onda Sinusoidal Básica

Este código dibuja una onda sinusoidal básica en la pantalla:

```qbasic
SCREEN 13
FOR x% = 0 TO 359
  xx% = (x% / 360) * 320        ' Ajustar al ancho de pantalla (320 píxeles)
  rd = x% * 0.017453            ' Convertir a radianes
  y% = (SIN(rd) * 50) + 50      ' Ajustar a un rango de 0-100
  PSET (xx%, y%), 15            ' Dibujar el punto
NEXT x%
```

#### Mezclando Ondas Sinusoidales

La clave para crear patrones de plasma interesantes es mezclar varias ondas sinusoidales con diferentes frecuencias. Esto crea patrones complejos y aparentemente aleatorios:

```qbasic
SCREEN 13
FOR x% = 0 TO 359
  xx% = (x% / 360) * 320
  rd = x% * 0.017453
  ' Mezclar dos ondas sinusoidales con diferentes frecuencias
  srd = (SIN(rd) + SIN(rd * 2.8)) / 2  ' Promedio de dos ondas
  y% = (srd * 50) + 50
  PSET (xx%, y%), 15
NEXT x%
```

#### Aplicando Ondas Sinusoidales a la Paleta de Colores

Podemos usar estos valores sinusoidales para generar paletas de colores dinámicas:

```qbasic
SCREEN 13
' Generar una paleta basada en ondas sinusoidales
FOR x% = 0 TO 359
  xx% = (x% / 360) * 100         ' Escalar a 100 colores
  rd = x% * 0.017453
  srd = (SIN(rd) + SIN(rd * 2.8)) / 2
  colorValue = (srd * 31) + 31   ' Ajustar a rango de 0-63 para palette
  
  OUT &H3C8, xx% + 1             ' Seleccionar color (comenzando desde 1)
  OUT &H3C9, colorValue          ' Componente rojo
  OUT &H3C9, 0                   ' Componente verde
  OUT &H3C9, 0                   ' Componente azul
NEXT x%

' Dibujar un patrón que use los colores
FOR x% = 1 TO 100
  FOR y% = 1 TO 100
    PSET (x%, y%), (x% + y%) / 2
  NEXT y%
NEXT x%
```

#### Tipos de Plasmas

Esencialmente, existen dos enfoques principales para crear efectos de plasma:

1. **Rotación de Paleta**: Rápido y eficiente, pero produce plasmas estáticos donde solo cambian los colores.

2. **Funcional (Trigonométrico)**: Más lento pero produce efectos mucho más dinámicos y versátiles, permitiendo animaciones genuinas.

La técnica funcional tiene la ventaja de que puede limitarse a un determinado rango de colores, lo que la hace ideal para su uso en juegos donde no se desea afectar a todo el esquema de colores.

#### Creación de Paletas para Plasma

La calidad visual de un plasma depende en gran medida de la paleta de colores utilizada. Es importante crear una paleta que se "envuelva" sobre sí misma, es decir, donde el último color se mezcle suavemente con el primero. Esto permite transiciones suaves en las animaciones de plasma.

Para crear una paleta envolvente:

```qbasic
' Crear una paleta arcoíris envolvente
FOR c = 0 TO 63
    ' Rojo: sube, baja, se mantiene bajo
    IF c < 21 THEN r = c * 3 ELSE IF c < 42 THEN r = 63 - ((c - 21) * 3) ELSE r = 0
    
    ' Verde: se mantiene bajo, sube, baja
    IF c < 21 THEN g = 0 ELSE IF c < 42 THEN g = (c - 21) * 3 ELSE g = 63 - ((c - 42) * 3)
    
    ' Azul: baja, se mantiene bajo, sube
    IF c < 21 THEN b = 63 - (c * 3) ELSE IF c < 42 THEN b = 0 ELSE b = (c - 42) * 3
    
    ' Establecer color en la paleta
    OUT &H3C8, c
    OUT &H3C9, r
    OUT &H3C9, g
    OUT &H3C9, b
NEXT c
```

#### Plasmas Avanzados: Combinación de Funciones Sinusoidales

La clave para crear plasmas visualmente impresionantes es combinar múltiples funciones sinusoidales con diferentes parámetros. Cada función contribuye a la apariencia final del plasma.

```qbasic
SCREEN 13
CONST PI = 3.141593

' Crear una tabla de búsqueda para la función SIN
DIM SinTable(359) AS SINGLE
FOR i = 0 TO 359
    SinTable(i) = SIN(i * PI / 180)
NEXT i

' Variables de movimiento
offset = 0

DO
    ' Incrementar offset para animar
    offset = (offset + 1) MOD 360
    
    ' Generar plasma
    FOR y = 0 TO 199
        FOR x = 0 TO 319
            ' Combinar múltiples ondas sinusoidales
            c1 = SinTable((x * 8 + offset) MOD 360) * 32 + 32
            c2 = SinTable((y * 8 + offset) MOD 360) * 32 + 32
            c3 = SinTable(((x + y) * 4 + offset) MOD 360) * 32 + 32
            c4 = SinTable((SQR((x - 160) ^ 2 + (y - 100) ^ 2) * 2 + offset) MOD 360) * 32 + 32
            
            ' Combinar valores y convertir a color
            color = (c1 + c2 + c3 + c4) MOD 64
            
            ' Dibujar píxel
            PSET (x, y), color
        NEXT x
    NEXT y
    
    ' Control de tiempo para no saturar CPU
    WAIT &H3DA, 8
    
LOOP UNTIL INKEY$ <> ""
```

Este código combina cuatro patrones sinusoidales diferentes:

1. Una onda horizontal (en función de x)
2. Una onda vertical (en función de y)
3. Una onda diagonal (en función de x+y)
4. Una onda circular (en función de la distancia al centro)

#### Plasma en Coordenadas Polares

También se puede crear un plasma utilizando coordenadas polares en lugar de cartesianas, lo que produce efectos visuales diferentes:

```qbasic
SCREEN 13
CONST PI = 3.141593

DO
    offset = offset + 0.1
    
    FOR y = 0 TO 199
        y2 = y - 100
        FOR x = 0 TO 319
            x2 = x - 160
            
            ' Calcular radio y ángulo (coordenadas polares)
            radio = SQR(x2 * x2 + y2 * y2)
            IF x2 = 0 THEN
                IF y2 < 0 THEN angulo = -PI / 2 ELSE angulo = PI / 2
            ELSE
                angulo = ATN(y2 / x2)
                IF x2 < 0 THEN angulo = angulo + PI
            END IF
            
            ' Aplicar funciones sinusoidales en coordenadas polares
            c1 = SIN(radio * 0.1 + offset) * 32 + 32
            c2 = SIN(angulo * 3 + offset) * 32 + 32
            
            ' Calcular color final
            color = (c1 + c2) MOD 64
            
            ' Dibujar píxel
            PSET (x, y), color
        NEXT x
    NEXT y
    
    WAIT &H3DA, 8
    
LOOP UNTIL INKEY$ <> ""
```

#### Plasmas Semitransparentes

Se puede crear un efecto de semitransparencia superponiendo dos plasmas diferentes en el mismo fotograma:

```qbasic
SCREEN 13
CONST PI = 3.141593

' Crear un fondo o cargar una imagen
FOR y = 0 TO 199
    FOR x = 0 TO 319
        PSET (x, y), (x + y) MOD 64 + 64
    NEXT x
NEXT y

DO
    offset1 = offset1 + 0.05
    offset2 = offset2 + 0.07
    
    FOR y = 0 TO 199 STEP 2  ' Optimización: procesar cada 2 píxeles
        FOR x = 0 TO 319 STEP 2
            ' Primer plasma
            p1 = SIN((x + offset1) * 0.05) * 32 + SIN((y + offset1) * 0.05) * 32
            
            ' Segundo plasma
            p2 = SIN(((x + y) * 0.05 + offset2)) * 64
            
            ' Combinar ambos plasmas con un efecto semitransparente
            color = (p1 + p2) MOD 64
            
            ' Aplicar a un bloque de 2x2 para optimización
            PSET (x, y), color
            PSET (x + 1, y), color
            PSET (x, y + 1), color
            PSET (x + 1, y + 1), color
        NEXT x
    NEXT y
    
    WAIT &H3DA, 8
    
LOOP UNTIL INKEY$ <> ""
```

#### Optimizaciones para Mayor Velocidad

Dado que los efectos de plasma pueden ser intensivos en cálculos, aquí hay varias técnicas de optimización:

1. **Tablas de búsqueda**: Precalcular valores de seno/coseno y almacenarlos en arrays.

2. **Reducir resolución**: Calcular el plasma a una resolución menor y ampliar los píxeles.

3. **Usar enteros**: Cuando sea posible, utilizar variables enteras en lugar de flotantes.

4. **Limitar el área**: Generar el plasma solo en una parte de la pantalla.

5. **Funciones más simples**: A veces, funciones más simples pueden producir resultados similares con mejor rendimiento.

6. **Código ensamblador**: Para optimizaciones críticas, se puede recurrir a rutinas en ensamblador.

#### Aplicaciones en Juegos Comerciales

Estas técnicas de plasma se han utilizado en numerosos juegos comerciales para crear efectos especiales, fondos dinámicos y transiciones:

- La serie Final Fantasy usó plasmas para efectos mágicos
- Chrono Trigger utilizó efectos similares para las animaciones de distorsión temporal
- Bahamut Lagoon incorporó efectos de plasma para representar energía mágica
- Seiken Densetsu 3 (Secret of Mana 2) usó plasmas para efectos elementales

#### Plasma en Modo Texto

Incluso es posible crear efectos de plasma en modo texto, utilizando caracteres ASCII para representar diferentes intensidades:

```qbasic
SCREEN 0
WIDTH 80, 25
CONST PI = 3.141593

DO
    offset = offset + 0.1
    
    FOR y = 1 TO 25
        FOR x = 1 TO 80
            ' Calcular valor del plasma
            valor = SIN(x * 0.2 + offset) * 8 + SIN(y * 0.1 + offset) * 8
            valor = (valor + 16) \ 2  ' Convertir a rango 0-15
            
            ' Seleccionar carácter según intensidad
            SELECT CASE valor
                CASE 0 TO 3: char$ = " "
                CASE 4 TO 7: char$ = "."
                CASE 8 TO 11: char$ = "o"
                CASE 12 TO 15: char$ = "O"
            END SELECT
            
            ' Imprimir carácter
            LOCATE y, x
            PRINT char$;
        NEXT x
    NEXT y
    
    ' Pausa para controlar velocidad
    FOR delay = 1 TO 1000: NEXT delay
    
LOOP UNTIL INKEY$ <> ""
```

### Efecto de Ondulación (Ripple)

El efecto de ondulación o "ripple" permite simular la refracción de la luz a través de una superficie líquida ondulante. Este efecto es particularmente útil para crear animaciones de agua, lagos o cualquier superficie líquida en movimiento.

#### Fundamentos Matemáticos

La base del efecto de ondulación se encuentra en dos conceptos físicos principales:

1. **Ondas sinusoidales**: El movimiento de la superficie del líquido sigue un patrón sinusoidal.
2. **Refracción de la luz**: La luz cambia de dirección cuando pasa a través de la superficie del líquido.

##### Caso Unidimensional (1D)

Para entender mejor el concepto, comenzamos con el caso unidimensional. En una onda sinusoidal 1D:

- La altura del punto en la onda está dada por `y = d + sin(x)`, donde `d` es la profundidad.
- La pendiente instantánea en cualquier punto es el coseno de `x`.
- Un rayo de luz que incide verticalmente se refracta según la pendiente de la superficie.

La fórmula para calcular la posición horizontal donde aparecería un punto después de la refracción es:

```qbasic
u = x - cos(x) * (d + sin(x))
```

Donde:

- `x` es la posición horizontal original
- `d` es la profundidad del agua
- `u` es la nueva posición horizontal después de la refracción

##### Caso Bidimensional (2D)

Para crear un efecto de ondulación 2D, aplicamos los mismos principios pero considerando la distancia desde el centro de la ondulación:

1. La distancia desde el centro se calcula usando el teorema de Pitágoras: `distancia = SQR(x^2 + y^2)`
2. La altura de la onda en un punto es: `altura = d + sin(distancia)`
3. La refracción se calcula para cada componente (X e Y) manteniendo el ángulo en el plano XY

Las fórmulas finales para calcular las coordenadas después de la refracción son:

```qbasic
magnitud = SQR(x^2 + y^2)
n = magnitud  ' Para evitar división por cero, si n=0 entonces establecer n=1
angulo = ATN(y / x)  ' Ajustar según el cuadrante

u = (magnitud - (d + a * (SIN(magnitud + w) / n)) * a * COS(magnitud + w)) * COS(angulo)
v = (magnitud - (d + a * (SIN(magnitud + w) / n)) * a * COS(magnitud + w)) * SIN(angulo)
```

Donde:

- `a` es la amplitud máxima
- `w` es el parámetro de animación (se decrementa para crear el movimiento)
- `u` y `v` son las nuevas coordenadas X e Y después de la refracción

#### Implementación en QBasic

```qbasic
SCREEN 13
CONST PI = 3.14159

' Cargar imagen de fondo
DIM Fondo(320, 200)
' Aquí cargarías tu imagen o generarías un patrón

' Parámetros del efecto
profundidad = 2
amplitudMax = 0.5
w = 0

' Buffer para doble búfer
DIM Buffer(320, 200)

DO
    ' Decrementar el parámetro de animación
    w = w - 0.1
    IF w < 0 THEN w = 2 * PI
    
    ' Centro de la pantalla
    centroX = 160
    centroY = 100
    
    ' Radio de la ondulación
    radio = 100
    
    ' Procesar cada píxel en el área de ondulación
    FOR y = centroY - radio TO centroY + radio
        FOR x = centroX - radio TO centroX + radio
            ' Calcular desplazamiento desde el centro
            dx = x - centroX
            dy = y - centroY
            
            ' Calcular magnitud (distancia desde el centro)
            mag = SQR(dx * dx + dy * dy)
            
            ' Si estamos dentro del radio de ondulación
            IF mag <= radio THEN
                ' Evitar división por cero
                n = mag
                IF n = 0 THEN n = 1
                
                ' Calcular fase de la onda
                fase = mag + w
                
                ' Calcular nueva magnitud después de refracción
                nuevaMag = mag - (profundidad + amplitudMax * (SIN(fase) / n)) * amplitudMax * COS(fase)
                
                ' Calcular ángulo
                IF dx <> 0 THEN
                    angulo = ATN(dy / dx)
                    ' Ajustar para cuadrantes correctos
                    IF dx < 0 THEN angulo = angulo + PI
                ELSE
                    IF dy >= 0 THEN angulo = PI / 2 ELSE angulo = -PI / 2
                END IF
                
                ' Calcular nuevas coordenadas
                u = centroX + nuevaMag * COS(angulo)
                v = centroY + nuevaMag * SIN(angulo)
                
                ' Verificar que estamos dentro de los límites
                IF u >= 0 AND u < 320 AND v >= 0 AND v < 200 THEN
                    ' Obtener color de fondo en (u,v) y colocarlo en (x,y)
                    Buffer(x, y) = Fondo(u, v)
                END IF
            ELSE
                ' Fuera del radio de ondulación, copiar directamente
                Buffer(x, y) = Fondo(x, y)
            END IF
        NEXT x
    NEXT y
    
    ' Copiar buffer a pantalla
    ' Aquí implementarías la copia del buffer a la pantalla
    
    ' Control de tiempo
    FOR Delay = 1 TO 1000: NEXT Delay
    
LOOP UNTIL INKEY$ <> ""
```

#### Optimizaciones

Como el efecto de ondulación es computacionalmente intensivo, hay varias optimizaciones que se pueden aplicar:

1. **Tablas precalculadas**: Calcular los valores de seno una sola vez al inicio y almacenarlos en un array.

2. **Procesamiento por bloques**: Actualizar solo partes de la pantalla en cada fotograma.

3. **Uso de WAIT para sincronización**: Para evitar parpadeos y conseguir un refresco suave.

#### Aplicaciones

Este tipo de efectos puede utilizarse para:

- Fondos animados en juegos
- Demostraciones gráficas
- Visualización de música
- Base para otros efectos como fuego, agua o nubes
