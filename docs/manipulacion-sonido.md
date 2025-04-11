# Manipulación de Sonido en QBasic

Este artículo proporciona información detallada sobre la manipulación de sonido digital en QBasic, incluyendo cómo reproducir diferentes formatos de archivos de sonido y los detalles técnicos sobre las estructuras de estos formatos.

## Fundamentos del Sonido Digital

Un archivo de sonido digital básicamente consiste en una serie de valores de volumen. En la PC, generalmente un valor de 128 representa el silencio. Los valores más alejados de 128 (en cualquier dirección) son más fuertes según su distancia de este punto medio. Los valores 0 y 255 representan los volúmenes más altos.

Es importante destacar que 128 no es necesariamente un silencio absoluto. Cuando se hace una grabación, siempre hay ruido de fondo, por lo que lo que puede sonar como silencio suele estar entre 126-130.

## Reproducción de Sonido en Hardware Básico

### DAC en Puerto Paralelo (LPT)

Para reproducir archivos de sonido digital (VOC, WAV, SND, etc.) en un DAC conectado al puerto paralelo:

1. Lee un byte del archivo
2. Envíalo al puerto paralelo
3. Repite con el siguiente byte

Para obtener la dirección I/O del puerto paralelo:

- LPT1: Lee la palabra en la ubicación de memoria 40h:8h
- LPT2: 40h:0Ah
- LPT3: 40h:0Ch
- LPT4 (en no-PS/2): 40h:0Eh

### Altavoz Interno de la PC

Reproducir sonido digital en el altavoz interno es más complicado porque este componente no fue diseñado para ese propósito. Para reproducir sonido, primero debes configurar correctamente el chip temporizador:

```qbasic
' Configurar el chip temporizador
OUT 43, &HB6
OUT 42, &HFF
OUT 42, 0
OUT 43, &H90
a = INP(61)
a = a OR 3
OUT 61, a
```

Después de esta configuración, para reproducir el sonido:

1. Obtén un byte del archivo de sonido
2. Divide el byte por un "valor de desplazamiento" (shift value)
3. Envía este nuevo byte al puerto 42h
4. Repite para todo el archivo

El "valor de desplazamiento" varía según el equipo. Depende del tamaño del altavoz y otros factores. Generalmente, un valor de 4 funciona en todas las computadoras. Cuanto menor sea el valor de desplazamiento, más alto será el volumen de reproducción, pero un valor demasiado pequeño causará distorsión.

Después de reproducir el sonido, debes restaurar el chip temporizador:

```qbasic
' Restaurar el chip temporizador
OUT 43, &HB6
a = INP(61)
a = a AND &HFC
OUT 61, a
```

## Formatos de Archivo de Sonido

### Archivos SAM (Amiga)

Los archivos SAM no contienen un encabezado o estructura específica; son archivos de sonido "raw" (sin procesar). Lo importante a recordar es que utilizan valores "signed" (con signo), lo que significa que cuando el bit 7 está activado, el número es negativo.

Para convertir un archivo signed a unsigned:

1. Lee un byte del archivo original
2. Suma 128 a ese byte
3. Escribe el resultado en un nuevo archivo

En el mundo Amiga, un byte de 0 equivale al silencio, mientras que -128 y +128 son los volúmenes más altos. En la PC, 0 y 255 son los volúmenes más altos, y 128 equivale al silencio.

### Formato de Archivo WAV

```
BYTE(S)        CONTENIDO NORMAL              PROPÓSITO/DESCRIPCIÓN
-----------------------------------------------------------------------
00 - 03        "RIFF"                        Bloque de identificación
04 - 07        ???                           Entero largo que indica el
                                             tamaño del archivo en bytes,
                                             incluyendo el encabezado
08 - 11        "WAVE"                        Identificador de formato
12 - 15        "fmt "                        Identificador de formato
16 - 19        16, 0, 0, 0                   Tamaño del encabezado hasta este punto
20 - 21        1, 0                          Etiqueta de formato
22 - 23        1, 0                          Canales
24 - 27        ???                           Frecuencia de muestreo (samples por segundo)
28 - 31        ???                           Bytes promedio por segundo
32 - 33        1, 0                          Alineación de bloque
34 - 35        8, 0                          Bits por muestra (8, 12 o 16)
36 - 39        "data"                        Marcador que precede a los datos reales
40 - 43        ???                           Número de bytes en la muestra
```

### Formato de Archivo VOC (Creative Labs)

```
BYTE(S)        CONTENIDO NORMAL              PROPÓSITO/DESCRIPCIÓN
-----------------------------------------------------------------------
00 - 19        "Creative Voice File", 26     Bloque de identificación
20 - 21        26, 00                        Desplazamiento al primer bloque de datos
22 - 23        10, 1                         Número de versión (1.10)
24 - 25        41, 17                        Dígitos de verificación
```

Después del encabezado (a partir del byte 26), el archivo VOC contiene bloques de datos. Hay ocho tipos, numerados de 0 a 7:

#### BLOQUE 0 - "END BLOCK"

- Byte 1: '0' para denotar el tipo "fin de bloque"
- Este bloque marca el final del archivo VOC

#### BLOQUE 1 - "DATA BLOCK"

- Byte 1: '1' para denotar el tipo "bloque de datos"
- Bytes 2-4: Longitud del bloque
- Byte 5: Frecuencia de muestreo (calculada como 1000000 / (256-b5))
- Byte 6: Tipo de compresión (0=sin compresión, 1=4 bits, 2=2 bits, 3=1 bit)
- Byte 7: Comienzan los datos reales de la muestra

#### BLOQUE 2 - "MORE DATA BLOCK"

- Byte 1: '2' para denotar el tipo "más datos"
- Bytes 2-4: Longitud del bloque
- Byte 5: Comienzan los datos de la muestra
- Utiliza la misma frecuencia de muestreo y compresión que el bloque de datos anterior

#### BLOQUE 3 - "SILENCE BLOCK"

- Byte 1: '3' para denotar el tipo "bloque de silencio"
- Bytes 2-4: Longitud del bloque (generalmente 3)
- Bytes 5-6: Duración del silencio
- Byte 7: Frecuencia de muestreo

#### BLOQUE 4 - "MARKER BLOCK"

- Byte 1: '4' para denotar el tipo "bloque marcador"
- Bytes 2-4: Longitud del bloque
- Bytes 5-6: Valor del marcador

#### BLOQUE 5 - "MESSAGE BLOCK"

- Byte 1: '5' para denotar el tipo "bloque de mensaje"
- Bytes 2-4: Longitud del bloque
- Bytes 5-?: Mensaje en texto ASCII
- Byte ?: 0, para denotar fin del texto

#### BLOQUE 6 - "REPEAT BLOCK"

- Byte 1: '6' para denotar el tipo "bloque de repetición"
- Bytes 2-4: Longitud del bloque
- Bytes 5-6: Número de veces que los datos deben repetirse

#### BLOQUE 7 - "END REPEAT BLOCK"

- Byte 1: '7' para denotar el tipo "fin de bloque de repetición"
- Bytes 2-4: Longitud del bloque (siempre cero)

### Formato de Archivo MOD (Protracker)

El formato MOD (Module) originario del Amiga es un formato de música tracker con las siguientes secciones:

```
Offset   Bytes   Descripción
------   -----   -----------
0        20      Nombre de la canción (con bytes nulos al final)
```

Información para las muestras 1-31:

```
Offset   Bytes   Descripción
------   -----   -----------
20       22      Nombre de la muestra 1 (rellenado con bytes nulos)
42       2       Longitud de la muestra 1 en palabras (multiplicar por 2 para obtener longitud en bytes)
44       1       Los 4 bits inferiores son el valor de ajuste fino (signed)
45       1       Volumen para la muestra 1 (rango 0-64 decimal)
46       2       Punto de repetición para la muestra 1 (en palabras)
48       2       Longitud de repetición para la muestra 1 (en palabras)
```

La información para las siguientes 30 muestras continúa de manera similar.

```
Offset   Bytes   Descripción
------   -----   -----------
950      1       Longitud de la canción (rango 1-128)
951      1       Este byte se establece en 127
952      128     Posiciones de la canción 0-127
1080     4       Las cuatro letras "M.K." - Identificador
1084     1024    Datos para el patrón 00
```

Cada nota se almacena como 4 bytes, y las cuatro notas en cada posición del patrón se almacenan una tras otra.

Información para cada nota:

```
Byte 1             Byte 2      Byte 3             Byte 4
------------------  ---------   -----------------   ----------
Cuatro bits        12 bits      Cuatro bits         Comando
superiores del     para el      inferiores del      de efecto
número de muestra  período      número de muestra
```

### Archivos SND (Sounder)

```
BYTE(S)        CONTENIDO NORMAL              PROPÓSITO/DESCRIPCIÓN
-----------------------------------------------------------------------
00 - 01        0, 0                          Bits por muestra (normalmente 0 para 8 bits)
02 - 03        ???                           Frecuencia de muestreo
04 - 05        ???                           Volumen de reproducción
06 - 07        4, 0                          Valor de desplazamiento (shift)
```

### Archivos SND (Sound Tool)

```
BYTE(S)        CONTENIDO NORMAL              PROPÓSITO/DESCRIPCIÓN
-----------------------------------------------------------------------
00 - 05        "SOUND", 26                   Identificación
08 - 11        ???                           Número de bytes en la muestra
12 - 15        ???                           Primer byte a reproducir
16 - 19        ???                           Último byte a reproducir
20 - 21        ???                           Frecuencia de muestreo
22 - 23        ???                           Bits por muestra
24 - 25        ???                           Volumen de reproducción
26 - 27        4, 0                          Valor de desplazamiento
28 - 123       ???                           Nombre de la muestra (termina con ASCII 0)
```

## Ejemplo de Código para Reproducir Sonido

A continuación se presenta un ejemplo simplificado de cómo reproducir un archivo WAV sin comprimir en el altavoz interno de la PC:

```qbasic
' Abrir el archivo WAV
OPEN "sonido.wav" FOR BINARY AS #1

' Saltar el encabezado (primeros 44 bytes generalmente)
SEEK #1, 45

' Configurar el chip temporizador
OUT 43, &HB6
OUT 42, &HFF
OUT 42, 0
OUT 43, &H90
a = INP(61)
a = a OR 3
OUT 61, a

' Reproducir el sonido
shiftValue = 4 ' Ajustar según sea necesario
DO WHILE NOT EOF(1)
  ' Leer un byte del archivo
  byte$ = INPUT$(1, #1)
  value = ASC(byte$)
  
  ' Dividir por el valor de desplazamiento
  value = value \ shiftValue
  
  ' Enviar al puerto del altavoz
  OUT 42, value
  
  ' Pequeña pausa para la frecuencia de muestreo
  ' (esto debería ajustarse según la frecuencia del archivo)
  FOR delay = 1 TO 8: NEXT delay
LOOP

' Restaurar el chip temporizador
OUT 43, &HB6
a = INP(61)
a = a AND &HFC
OUT 61, a

' Cerrar el archivo
CLOSE #1
```

## Consejos para Trabajar con Sonido en QBasic

1. **Frecuencia de muestreo**: Cuanto mayor sea la frecuencia, mejor será la calidad del sonido, pero también requerirá más recursos. Las frecuencias comunes son 22 KHz, 11 KHz, 7.33 KHz y 5.5 KHz.

2. **Altavoz interno vs DAC**: El altavoz interno tiene limitaciones significativas en la calidad de sonido. Un DAC en el puerto paralelo ofrece mejor calidad.

3. **Conversión signed a unsigned**: Recuerda que los formatos Amiga (MOD, SAM) utilizan valores signed, mientras que los formatos PC suelen usar unsigned.

4. **Valor de desplazamiento (shift)**: Experimenta con diferentes valores (2-6) para encontrar el mejor equilibrio entre volumen y distorsión para tu sistema.

5. **Timing**: La reproducción de sonido en tiempo real requiere un timing preciso. En sistemas más rápidos, es posible que necesites agregar retrasos adicionales para mantener la frecuencia de muestreo correcta.

## Conclusión

Aunque QBasic no fue diseñado específicamente para la manipulación de sonido avanzada, con las técnicas adecuadas es posible reproducir una variedad de formatos de sonido digital. Los programadores experimentados pueden incluso crear aplicaciones con efectos de sonido y música de fondo utilizando estas técnicas.
