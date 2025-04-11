# Raycasting en QBasic

Autor: William Moores

## Introducción

¿Quieres crear un juego como DOOM? Con esta guía, explicaremos los fundamentos del raycasting, que te permitirá desarrollar juegos con perspectiva 3D en QBasic.

El concepto del raycasting es muy simple: imagina que tienes un puntero láser con una pequeña pantalla en la parte posterior que muestra el color y la distancia del objeto al que estás apuntando. ¡El raycasting funciona exactamente así! En QBasic, implementar esta técnica es más sencillo de lo que parece.

## Fundamentos del Raycasting

### El Mapa

Primero necesitamos un mapa para nuestro entorno. Podemos crearlo con un array bidimensional:

```qbasic
DIM map%(1 TO 20, 1 TO 20)
```

Este código crea un nivel de 20×20 bloques. Cada elemento del array representa una pared en nuestro demo (por ejemplo, `map%(3, 4) = 10` colocaría una pared en la posición (3,4) con color 10).

### El Concepto del Ray (Rayo)

La parte "ray" del raycasting funciona así:

1. La "cámara" emite rayos en diferentes direcciones
2. Cuando un rayo golpea una pared en nuestro mapa, la "cámara" registra:
   - El color de la pared que ha golpeado
   - La distancia a la pared
   - El rayo deja de avanzar

Es similar a un radar: emite ondas que rebotan en objetos sólidos, y luego calcula la distancia a esos objetos basándose en el tiempo que tardan las ondas en regresar.

## Implementación 2D

Antes de abordar el 3D, es útil visualizar el raycasting en 2D. En una demo 2D, podemos ver:

- Una vista superior de nuestro mapa
- Un pequeño cono que podemos rotar y mover (nuestra "cámara")
- Los rayos que salen de la cámara

Esta visualización 2D nos ayuda a entender cómo la cámara emite rayos y cómo estos interactúan con las paredes.

### Ejemplo de código 2D (2DRAY.BAS)

A continuación se presenta un esquema conceptual de la implementación 2D:

```qbasic
' Inicializar el mapa
DIM map%(1 TO 20, 1 TO 20)
' Llenar el mapa con paredes
FOR y = 1 TO 20
  FOR x = 1 TO 20
    IF x = 1 OR y = 1 OR x = 20 OR y = 20 THEN
      map%(x, y) = 14 ' Paredes exteriores en color 14
    ELSE
      map%(x, y) = 0 ' Espacio vacío
    END IF
  NEXT x
NEXT y
' Añadir algunas paredes interiores
map%(5, 5) = 12: map%(6, 5) = 12: map%(7, 5) = 12

' Variables para la cámara
camX = 10: camY = 10 ' Posición inicial
camAngle = 0 ' Ángulo inicial (en radianes)

' Bucle principal
DO
  ' Borrar pantalla
  CLS
  
  ' Dibujar mapa
  FOR y = 1 TO 20
    FOR x = 1 TO 20
      IF map%(x, y) > 0 THEN
        PSET (x * 8, y * 8), map%(x, y)
      END IF
    NEXT x
  NEXT y
  
  ' Dibujar cámara
  CIRCLE (camX * 8, camY * 8), 3, 15
  
  ' Dibujar rayos
  FOR rayAngle = camAngle - .3 TO camAngle + .3 STEP .02
    ' Emitir rayo
    rayX = camX
    rayY = camY
    rayDist = 0
    
    DO
      ' Avanzar rayo
      rayX = rayX + COS(rayAngle) * .1
      rayY = rayY + SIN(rayAngle) * .1
      rayDist = rayDist + .1
      
      ' Comprobar si el rayo golpea una pared
      mapX = INT(rayX)
      mapY = INT(rayY)
      
      IF mapX >= 1 AND mapX <= 20 AND mapY >= 1 AND mapY <= 20 THEN
        IF map%(mapX, mapY) > 0 THEN
          ' Rayo golpeó una pared
          LINE (camX * 8, camY * 8)-(rayX * 8, rayY * 8), map%(mapX, mapY)
          EXIT DO
        END IF
      ELSE
        ' Rayo salió del mapa
        EXIT DO
      END IF
    LOOP WHILE rayDist < 20
  NEXT rayAngle
  
  ' Controles de movimiento
  k$ = INKEY$
  IF k$ = CHR$(0) + "H" THEN ' Flecha arriba
    camX = camX + COS(camAngle) * .5
    camY = camY + SIN(camAngle) * .5
  ELSEIF k$ = CHR$(0) + "P" THEN ' Flecha abajo
    camX = camX - COS(camAngle) * .5
    camY = camY - SIN(camAngle) * .5
  ELSEIF k$ = CHR$(0) + "K" THEN ' Flecha izquierda
    camAngle = camAngle - .1
  ELSEIF k$ = CHR$(0) + "M" THEN ' Flecha derecha
    camAngle = camAngle + .1
  END IF
  
  ' Salir del programa
  IF k$ = CHR$(27) THEN EXIT DO
  
  ' Pequeña pausa
  _LIMIT 30
LOOP
```

## Implementación 3D

Para crear el efecto 3D, en lugar de dibujar los rayos directamente, dibujamos líneas verticales en la pantalla que corresponden a cada rayo emitido. La altura de cada línea se calcula basándose en la distancia a la pared: cuanto más cerca esté la pared, más alta será la línea.

La implementación 3D utiliza la misma lógica de raycasting que la versión 2D, pero representa los resultados de manera diferente para crear la ilusión de 3D.

### Ejemplo de código 3D (3DRAY.BAS)

A continuación se presenta un esquema conceptual de la implementación 3D:

```qbasic
' Inicializar el mapa (igual que en la versión 2D)
DIM map%(1 TO 20, 1 TO 20)
' ... código para inicializar el mapa ...

' Variables para la cámara
camX = 10: camY = 10
camAngle = 0
screenWidth = 320 ' Ancho de la pantalla

' Modo gráfico
SCREEN 13 ' 320x200, 256 colores

' Bucle principal
DO
  ' Preparar la pantalla
  LINE (0, 0)-(screenWidth, 100), 9, BF ' Cielo
  LINE (0, 100)-(screenWidth, 200), 2, BF ' Suelo
  
  ' Emitir rayos y dibujar paredes
  rayAngleStep = .6 / screenWidth ' Campo de visión dividido por el ancho de la pantalla
  
  FOR screenX = 0 TO screenWidth - 1
    ' Calcular ángulo del rayo
    rayAngle = camAngle - .3 + rayAngleStep * screenX
    
    ' Emitir rayo
    rayX = camX
    rayY = camY
    rayDist = 0
    hitColor = 0
    
    DO
      ' Avanzar rayo
      rayX = rayX + COS(rayAngle) * .05
      rayY = rayY + SIN(rayAngle) * .05
      rayDist = rayDist + .05
      
      ' Comprobar si el rayo golpea una pared
      mapX = INT(rayX)
      mapY = INT(rayY)
      
      IF mapX >= 1 AND mapX <= 20 AND mapY >= 1 AND mapY <= 20 THEN
        IF map%(mapX, mapY) > 0 THEN
          ' Rayo golpeó una pared
          hitColor = map%(mapX, mapY)
          EXIT DO
        END IF
      ELSE
        ' Rayo salió del mapa
        EXIT DO
      END IF
    LOOP WHILE rayDist < 20
    
    ' Calcular altura de la línea
    lineHeight = 200
    IF rayDist > 0 THEN
      ' Corregir el efecto "ojo de pez" (opcional)
      correctedDist = rayDist * COS(rayAngle - camAngle)
      lineHeight = 3000 / correctedDist
      IF lineHeight > 200 THEN lineHeight = 200
    END IF
    
    ' Calcular posición y de inicio y fin
    lineY1 = 100 - lineHeight / 2
    lineY2 = lineY1 + lineHeight
    
    ' Dibujar línea vertical (pared)
    IF hitColor > 0 THEN
      ' Oscurecer colores basado en la distancia
      shade = 15 - INT(rayDist)
      IF shade < 0 THEN shade = 0
      actualColor = hitColor - 8 + shade
      IF actualColor < hitColor - 8 THEN actualColor = hitColor - 8
      IF actualColor > hitColor THEN actualColor = hitColor
      
      LINE (screenX, lineY1)-(screenX, lineY2), actualColor
    END IF
  NEXT screenX
  
  ' Controles de movimiento (igual que en la versión 2D)
  ' ... código para mover la cámara ...
  
  ' Pequeña pausa
  _LIMIT 30
LOOP
```

## El Efecto "Ojo de Pez"

Un problema común con el raycasting simple es el efecto "ojo de pez", donde las paredes aparecen curvadas en lugar de rectas. Esto ocurre porque estamos midiendo la distancia directa desde la cámara a la pared, pero para una representación 3D más realista, necesitaríamos la distancia perpendicular al "plano de proyección".

Se puede corregir este efecto multiplicando la distancia por el coseno del ángulo entre el rayo y la dirección de la cámara, como se muestra en el ejemplo 3D.

## Mejoras Posibles

Con esta base de raycasting, puedes implementar varias mejoras:

1. **Texturas**: En lugar de usar colores sólidos, puedes mapear texturas en las paredes
2. **Iluminación**: Implementar luces en el entorno
3. **Sprites**: Añadir objetos y personajes como sprites 2D
4. **Suelos y Techos Texturizados**: Más allá del raycasting básico
5. **Colisiones**: Impedir que la cámara atraviese paredes

## Conclusión

El raycasting es una técnica poderosa que te permite crear entornos 3D convincentes incluso en un lenguaje como QBasic. Con este conocimiento, puedes comenzar a desarrollar juegos similares a los clásicos de los años 90 como Wolfenstein 3D y DOOM.

Como sugiere el autor original: "¡Piensa en ello, podrías añadir luces! Todo lo que tendrías que hacer es lo opuesto a una cámara. En lugar de leer el color, ¡podrías escribir el color!"

Los ejemplos proporcionados son intencionalmente simples para facilitar la comprensión, pero se pueden optimizar y expandir considerablemente para crear juegos completos.
