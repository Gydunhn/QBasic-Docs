# Sonido

QBasic ofrece varias instrucciones para reproducir sonidos a través del altavoz del PC. Estas capacidades van desde simples pitidos hasta melodías complejas y efectos de sonido personalizados.

## Comandos Básicos de Sonido

### BEEP

La instrucción más simple para producir sonido en QBasic es `BEEP`. Produce un pitido corto con una frecuencia y duración predeterminadas:

```qbasic
BEEP  ' Produce un pitido estándar
```

Este comando es útil para alertas o confirmaciones simples.

### SOUND

El comando `SOUND` ofrece control directo sobre la frecuencia y duración de un tono:

```qbasic
SOUND frecuencia, duración
```

- **frecuencia**: valor en Hertz (Hz) entre 37 y 32767
- **duración**: valor en "ticks" (aproximadamente 18.2 ticks = 1 segundo)

Ejemplos:

```qbasic
SOUND 440, 18   ' Nota La (A4) durante aproximadamente 1 segundo
SOUND 261, 9    ' Nota Do (C4) durante medio segundo
SOUND 1000, 5   ' Tono de 1000 Hz durante un cuarto de segundo
```

Tabla de frecuencias para notas musicales (octava 4):

| Nota     | Frecuencia (Hz) |
| -------- | --------------- |
| C4 (Do)  | 261.63          |
| D4 (Re)  | 293.66          |
| E4 (Mi)  | 329.63          |
| F4 (Fa)  | 349.23          |
| G4 (Sol) | 392.00          |
| A4 (La)  | 440.00          |
| B4 (Si)  | 493.88          |

### Efectos Sonoros con SOUND

Puedes crear diversos efectos de sonido combinando múltiples llamadas a `SOUND`:

```qbasic
' Efecto de alarma
FOR i = 1 TO 5
    SOUND 800, 5
    FOR delay = 1 TO 500: NEXT delay
    SOUND 600, 5
    FOR delay = 1 TO 500: NEXT delay
NEXT i

' Efecto de sirena
FOR freq = 500 TO 1500 STEP 50
    SOUND freq, 1
NEXT freq
FOR freq = 1500 TO 500 STEP -50
    SOUND freq, 1
NEXT freq

' Explosión
FOR i = 1 TO 20
    f = 200 + RND * 800
    d = 1 + RND * 5
    SOUND f, d
NEXT i
```

## El Comando PLAY

El comando `PLAY` es la herramienta más potente en QBasic para crear música. Utiliza una cadena con un lenguaje musical similar a BASIC que permite especificar notas, duraciones, octavas y más.

### Sintaxis Básica

```qbasic
PLAY "cadena_musical"
```

### Notas Musicales

Las notas se representan con letras de A a G (también pueden usarse sus equivalentes en solfeo):

```qbasic
PLAY "CDEFGAB"   ' Escala de Do a Si
PLAY "C D E F G A B"  ' Los espacios se ignoran
```

### Duraciones de Notas

Se especifican con un número después de la nota:

```qbasic
PLAY "C4 D4 E4 F4"  ' Notas de 1/4 (negras)
PLAY "C8 D8 E8 F8"  ' Notas de 1/8 (corcheas)
PLAY "C2 D2"        ' Notas de 1/2 (blancas)
PLAY "C1"           ' Nota completa (redonda)
```

Valores comunes:

- 1: Redonda
- 2: Blanca
- 4: Negra
- 8: Corchea
- 16: Semicorchea
- 32: Fusa

### Octavas

Puedes cambiar la octava usando "O" seguido de un número del 0 al 6:

```qbasic
PLAY "O3 C O4 C O5 C"  ' Do en tres octavas diferentes
```

También puedes subir o bajar octavas temporalmente:

```qbasic
PLAY "C < C < C"  ' Cada "<" baja una octava
PLAY "C > C > C"  ' Cada ">" sube una octava
```

### Sostenidos y Bemoles

Se indican con "#" (sostenido) y "-" (bemol):

```qbasic
PLAY "C C# D D# E F F# G G# A A# B"  ' Escala cromática ascendente
PLAY "C D- D E- E F G- G A- A B- B"  ' Otra forma de escala cromática
```

### Tempo

Controla la velocidad de reproducción con "T" seguido de un número entre 32 y 255:

```qbasic
PLAY "T120 CDEFGAB"  ' Tempo moderado (120 es un valor común)
PLAY "T200 CDEFGAB"  ' Tempo más rápido
PLAY "T60 CDEFGAB"   ' Tempo más lento
```

### Pausas

Las pausas o silencios se indican con "P" seguido de la duración:

```qbasic
PLAY "C4 P4 E4 P4 G4"  ' Do, pausa, Mi, pausa, Sol
```

### Legato y Staccato

- "L": Conecta las notas (legato)
- "N": Separa las notas (normal) - por defecto
- "S": Notas cortas y separadas (staccato)

```qbasic
PLAY "L CDEFGAB"  ' Legato
PLAY "N CDEFGAB"  ' Normal
PLAY "S CDEFGAB"  ' Staccato
```

### Duración Relativa y Punto

- "MS" - Establece la duración predeterminada
- "." - Aumenta la duración de la nota en un 50% (como el punto en notación musical)

```qbasic
PLAY "MS8"        ' Establece la duración predeterminada a 1/8
PLAY "C D E F"    ' Todas las notas son corcheas
PLAY "C. D. E. F."  ' Notas punteadas (50% más largas)
```

### Ejemplos Completos

#### Escala de Do Mayor

```qbasic
PLAY "T120 O4 C D E F G A B > C"
```

#### Melodía Simple (Cumpleaños Feliz)

```qbasic
PLAY "T120 O4 C8 C8 D4 C4 F4 E4"
PLAY "C8 C8 D4 C4 G4 F4"
PLAY "C8 C8 > C4 A4 F4 E4 D4"
PLAY "A#8 A#8 A4 F4 G4 F4"
```

#### Melodía con Variables

Puedes combinar varias cadenas musicales, incluso utilizando variables:

```qbasic
parte1$ = "T120 O4 C8 E8 G4"
parte2$ = "E8 G8 > C4"
PLAY parte1$ + parte2$
```

## Técnicas Avanzadas

### Reproducción de Varias Voces

QBasic permite tocar hasta 3 voces simultáneamente usando "MB" (música en segundo plano):

```qbasic
PLAY "MB T120 O4 C4 E4 G4 > C2"  ' Inicia reproducción en segundo plano
PLAY "MB O3 E4 G4 B4 > E2"       ' Segunda voz (simultánea)
PLAY "O3 G4 B4 > D4 G2"          ' Tercera voz y espera a que todo termine
```

### Creación de una Biblioteca de Sonidos

```qbasic
' Biblioteca de efectos de sonido
SUB EfectoExplosion
    FOR i = 1 TO 20
        SOUND 100 + RND * 200, 1
    NEXT i
    FOR i = 1 TO 10
        SOUND 75 + RND * 100, 2
    NEXT i
END SUB

SUB EfectoLaser
    FOR f = 800 TO 2000 STEP 200
        SOUND f, 1
    NEXT f
END SUB

SUB EfectoPowerUp
    FOR f = 200 TO 800 STEP 20
        SOUND f, 1
    NEXT f
END SUB
```

### Integración de Sonido en Juegos

```qbasic
' Ejemplo: Integración de sonido en un juego simple
DO
    k$ = INKEY$
    SELECT CASE k$
        CASE CHR$(32)  ' Espacio - disparo
            EfectoLaser
        CASE "x", "X"  ' X - explosión
            EfectoExplosion
        CASE "p", "P"  ' P - power-up
            EfectoPowerUp
        CASE CHR$(27)  ' ESC - salir
            EXIT DO
    END SELECT
LOOP
```

## Limitaciones y Soluciones

### Limitaciones del Altavoz del PC

- Sonido monofónico (una frecuencia a la vez por canal)
- Calidad de sonido limitada
- Sin control de volumen

### Soluciones y Alternativas

1. **Tarjetas de Sonido**: Algunas versiones extendidas de QBasic permiten acceso a tarjetas de sonido para mejor calidad
2. **QB64**: Esta versión moderna de QBasic ofrece capacidades de sonido avanzadas, incluyendo reproducción de archivos WAV y MP3
3. **Librerías Externas**: Para QBasic original, existían librerías de terceros que permitían acceso a hardware de sonido avanzado

## Recursos y Referencias

Para profundizar en programación de sonido en QBasic:

1. Experimenta con el comando PLAY para crear tus propias melodías
2. Estudia partituras simples y conviértelas a notación de QBasic
3. Analiza ejemplos de juegos que usan efectos de sonido

El sonido es un componente esencial para crear aplicaciones y juegos atractivos, incluso con las limitaciones del hardware de la época de QBasic.
