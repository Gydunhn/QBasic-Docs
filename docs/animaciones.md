# Animaciones en QBasic

La animación es una parte fundamental de los juegos y aplicaciones gráficas en QBasic. Este capítulo explora cómo crear animaciones eficientes, desde los conceptos básicos hasta técnicas más avanzadas.

## Conceptos Fundamentales

Todas las animaciones, desde las más rudimentarias hasta las secuencias más sofisticadas tipo Pixar, se crean de la misma manera: presentando rápidamente una serie de imágenes estáticas, llamadas **frames**. En la televisión, la tasa es de 30 fps (frames por segundo), y en las películas es de 24 fps.

### Persistencia de Visión

La razón por la que la animación funciona es debido a una propiedad llamada **persistencia de visión**. Cuando el ojo humano mira una imagen, esta se retiene en el cerebro durante un breve período de tiempo. Cuando otra imagen sigue rápidamente, el cerebro la convierte en movimiento percibiendo un cambio en la posición de los objetos en lugar de un simple cambio de imagen.

Algorítmicamente, esto se puede expresar como:

```texto
Bucle {
    Mostrar una Imagen
    Esperar un Poco
}
```

## Animación en QBasic

Al crear animaciones mediante programación, típicamente no queremos crear una serie de frames completos que se muestran en secuencia. Es más fácil programar los cambios de posición de objetos individuales. El algoritmo básico es:

```texto
Bucle {
    Mover a una nueva ubicación
    Dibujar un objeto
    Esperar un poco
    Borrar el objeto
}
```

## Animaciones con Texto

Para empezar, podemos desarrollar animaciones basadas en texto, que son sencillas de implementar pero ilustran perfectamente los principios básicos.

### Ejemplo Básico de Animación

Este es un programa simple que muestra un carácter moviéndose de izquierda a derecha en la pantalla:

```qbasic
'QBasic Animación #1
'Escrito por Peter Smith

COLOR , 1 'Establecer el color de fondo a AZUL

CLS       'Limpiar la pantalla a este color

COLOR 15
PRINT "Primera Animación de Peter"

FOR p = 1 TO 79
    GOSUB 1000 'Dibujar el objeto
    GOSUB 2000 'Esperar un poco...
    GOSUB 3000 'Borrar el objeto
NEXT p

COLOR 7
END

1000 'Dibujar el objeto
LOCATE 6, p
COLOR 14
PRINT CHR$(219)
RETURN

2000 'Pausa
FOR t = 1 TO 500
NEXT t
RETURN

3000 'Borrar el objeto
LOCATE 6, p
COLOR 1
PRINT CHR$(219)
RETURN
```

En este ejemplo:

- Utilizamos `COLOR , 1` para establecer el fondo azul
- El bucle `FOR` mueve nuestro objeto a través de la pantalla
- La subrutina 1000 dibuja el objeto en su nueva posición
- La subrutina 2000 crea una pausa para controlar la velocidad
- La subrutina 3000 borra el objeto antes de moverlo a una nueva posición

### Explicación del Código

1. **Dibujar el objeto**: Utilizamos `LOCATE` para posicionar el cursor y `PRINT CHR$(219)` para dibujar un bloque sólido. El color 14 es amarillo brillante.

2. **Pausa**: Un simple bucle de retardo que controla la velocidad de la animación. Cuanto mayor sea el valor final del bucle, más lenta será la animación.

3. **Borrar el objeto**: Dibujamos el mismo carácter pero en el color de fondo (1, azul), lo que efectivamente "borra" el objeto.

## Mejorando la Animación

Podemos mejorar esta animación básica de varias maneras:

### Movimiento en Dos Dimensiones

```qbasic
'Animación en 2D
FOR y = 1 TO 20
    FOR x = 1 TO 79
        LOCATE y, x
        COLOR 14
        PRINT CHR$(219)
        
        FOR t = 1 TO 100: NEXT t
        
        LOCATE y, x
        COLOR 1
        PRINT CHR$(219)
    NEXT x
NEXT y
```

Este código mueve el bloque a través de la pantalla en un patrón de izquierda a derecha, de arriba a abajo.

### Añadiendo Múltiples Objetos

```qbasic
'Animación con múltiples objetos
DIM x(3), y(3), dx(3), dy(3)

'Inicializar posiciones y direcciones
FOR i = 1 TO 3
    x(i) = INT(RND * 78) + 1
    y(i) = INT(RND * 20) + 1
    dx(i) = IIF(RND < 0.5, -1, 1)
    dy(i) = IIF(RND < 0.5, -1, 1)
NEXT i

DO
    'Borrar objetos en posición anterior
    FOR i = 1 TO 3
        LOCATE y(i), x(i)
        COLOR 1
        PRINT CHR$(219)
    NEXT i
    
    'Actualizar posiciones
    FOR i = 1 TO 3
        x(i) = x(i) + dx(i)
        y(i) = y(i) + dy(i)
        
        'Rebotar en los bordes
        IF x(i) <= 1 OR x(i) >= 79 THEN dx(i) = -dx(i)
        IF y(i) <= 1 OR y(i) >= 22 THEN dy(i) = -dy(i)
    NEXT i
    
    'Dibujar objetos en nueva posición
    FOR i = 1 TO 3
        LOCATE y(i), x(i)
        COLOR 14
        PRINT CHR$(219)
    NEXT i
    
    'Pausa
    FOR t = 1 TO 500: NEXT t
LOOP UNTIL INKEY$ <> ""
```

Este ejemplo crea tres objetos que se mueven independientemente y rebotan en los bordes de la pantalla.

## Animación con Gráficos

Para animaciones más avanzadas, podemos usar los comandos gráficos de QBasic como `CIRCLE`, `LINE`, `PSET`, `GET` y `PUT`.

### Usando GET y PUT

```qbasic
SCREEN 13
DIM sprite(50)

'Crear sprite
CIRCLE (10, 10), 5, 14
PAINT (10, 10), 14, 14

'Guardar en array
GET (5, 5)-(15, 15), sprite

'Animar
CLS
FOR x = 0 TO 310
    PUT (x, 100), sprite, XOR  'Dibujar
    FOR t = 1 TO 100: NEXT t   'Pausa
    PUT (x, 100), sprite, XOR  'Borrar
NEXT x
```

Este código mueve un círculo amarillo a través de la pantalla en modo gráfico.

## Técnicas Avanzadas

### Doble Búfer

Para eliminar el parpadeo, podemos usar la técnica de doble búfer:

```qbasic
SCREEN 9, , 0, 0  'Activar página 0 (invisible)
CLS

'Dibujar en página invisible
CIRCLE (100, 100), 50, 14
PAINT (100, 100), 14, 14

'Mostrar página invisible
SCREEN 9, , 1, 0
```

### Animación con Sprites Múltiples

```qbasic
SCREEN 13
DIM sprite1(50), sprite2(50), sprite3(50)

'Crear sprites
CIRCLE (10, 10), 5, 14
PAINT (10, 10), 14, 14
GET (5, 5)-(15, 15), sprite1

CIRCLE (10, 10), 5, 12
PAINT (10, 10), 12, 12
GET (5, 5)-(15, 15), sprite2

CIRCLE (10, 10), 5, 10
PAINT (10, 10), 10, 10
GET (5, 5)-(15, 15), sprite3

'Animar cambiando los sprites
CLS
FOR x = 0 TO 310
    SELECT CASE x MOD 3
        CASE 0: PUT (x, 100), sprite1, PSET
        CASE 1: PUT (x, 100), sprite2, PSET
        CASE 2: PUT (x, 100), sprite3, PSET
    END SELECT
    
    FOR t = 1 TO 100: NEXT t
    CLS
NEXT x
```

Este ejemplo alterna entre tres sprites diferentes para crear una animación más compleja.

## Optimización de la Velocidad

Para mejorar el rendimiento de las animaciones:

1. **Usar tipos de datos enteros** cuando sea posible (DEFINT A-Z)
2. **Minimizar la redefinición de sprites** en cada frame
3. **Optimizar las rutinas de pausa** (WAIT &H3DA, 8 para sincronizar con el refresco vertical)
4. **Utilizar el modo XOR** para dibujar/borrar sprites cuando sea apropiado
5. **Limitar la zona de redibujado** a solo lo que ha cambiado

## Conclusión

La animación en QBasic sigue los mismos principios fundamentales que cualquier otra forma de animación: mostrar una secuencia de imágenes estáticas lo suficientemente rápido para crear la ilusión de movimiento. Desde simples animaciones de texto hasta complejas animaciones gráficas con múltiples sprites, QBasic ofrece herramientas versátiles para crear experiencias visuales dinámicas.

Las técnicas presentadas aquí pueden combinarse y extenderse para crear animaciones más complejas, juegos interactivos y presentaciones visuales atractivas.
