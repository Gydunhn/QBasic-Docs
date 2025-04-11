# Uso del Ratón en QBasic

*Última actualización: 8 de marzo de 2002*  
*Autor original: Antoni Gual*

Este tutorial explica cómo utilizar el ratón en modos VGA estándar de QBasic. Los modos SVGA y Mode-X están fuera del alcance de este documento. La información es válida tanto para QB 4.5 como para QBasic 1.1.

## Formas de usar el ratón en programas QBasic

Hay dos formas de implementar el uso del ratón:

1. **La forma fácil**: Usar QB4.5 con una biblioteca que lo soporte (Future, DQB, CosmoX, Rellib, Zephyr, etc.)
2. **La forma difícil**: Implementarlo manualmente a través de interrupciones

Este tutorial se centra en la segunda opción, explicando cómo hacerlo desde cero.

## Conceptos básicos sobre interrupciones

Una interrupción es una instrucción especial del procesador que llama a una función. Existen dos tipos:

- **Interrupciones de hardware**: Son disparadas eléctricamente por el hardware cuando necesita atención del procesador (teclado, puertos, disco, etc.)
- **Interrupciones de software**: Son la API de DOS, la forma de acceder a las funciones del sistema operativo. Casi todas las funciones de DOS se realizan a través de llamadas a interrupciones.

El ratón no es parte nativa de DOS, sino un complemento que debe cargarse en CONFIG.SYS (en DOS puro). Para usarlo, debemos llamar a las interrupciones adecuadas, cargar los registros del procesador con los valores apropiados y leer los resultados que devuelven.

## Habilitando interrupciones en QB 4.5

Para usar interrupciones en QB 4.5:

1. Configura los directorios en IDE (Options/Directories) hacia la ruta donde está instalado el compilador
2. Abre QB con el parámetro `/l` para cargar la biblioteca predeterminada:

   ```batch
   myqbpath\qb mysource /l
   ```

3. Utiliza la función `INTERRUPTX` que se carga en la biblioteca para gestionar las llamadas a interrupciones
4. Incluye el archivo QB.BI en tu programa para definir el tipo RegType:

   ```qbasic
   '$INCLUDE:'QB.BI'
   ```

## Habilitando interrupciones en QBasic 1.1

QBasic 1.1 no soporta bibliotecas, por lo que necesitamos cargar una rutina en ensamblador previamente creada:

```qbasic
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

SUB INTERRUPTX (intnum AS INTEGER, InReg AS RegTypeX, OutReg AS RegTypeX)
'standard interrupt call compatibility with QBasic
'some  static variables  
STATIC a() AS LONG, bReady AS INTEGER
'If assembler array not created, create it
IF NOT bReady THEN
    'don't change anything
    I =50:DIM a(1 TO I ) AS LONG
    a(1) = &H53EC8B55: a(2) = &H1E575651: a(3) = &H5E8B9C06: a(4) = &HA078B0E
    a(5) = &HC70774E4: a(6) = &HE9FFFF07: a(7) = &HEC8300A1: a(8) = &HB3F88A0A
    a(9) = &HE85E89CD: a(10) = &HCBEA46C7: a(11) = &H74253C90: a(12) = &H75263C04
    a(13) = &HEA46C714: a(14) = &H46C701E8: a(15) = &HC7CB00EC: a(16) = &H2C2EE46
    a(17) = &HF046C7: a(18) = &H85E8B90: a(19) = &H5E8B37FF: a(20) = &HE37FF06
    a(21) = &H50008FB8: a(22) = &HE85E8D16: a(23) = &H8BDA8C53: a(24) = &H378B0A5E
    a(25) = &H8E0C5E8B: a(26) = &H10448B1F: a(27) = &H75FFFF3D: a(28) = &H50C28B02
    a(29) = &H3D12448B: a(30) = &H275FFFF: a(31) = &HC08EC28B: a(32) = &H5C8B048B
    a(33) = &H44C8B02: a(34) = &H8B06548B: a(35) = &H748B0C7C: a(36) = &H9CCB1F0A
    a(37) = &H83EC8B55: a(38) = &H1E5620C5: a(39) = &H89E476C5: a(40) = &H25C8904
    a(41) = &H89044C89: a(42) = &H7C890654: a(43) = &H12448C0C: a(44) = &H8F10448F
    a(45) = &H448F0A44: a(46) = &HE448F08: a(47) = &H9D0EC483: a(48) = &H5E5F1F07
    a(49) = &HCA5D5B59: a(50) = &H9165000A
   'Checksum, can be ommited 
    S1 = 0: S2 = 0: p = VARPTR(a(1)): DEF SEG = VARSEG(a(1))
      FOR I  = 0 TO 199
        S1 = (S1 + PEEK(p + I )) MOD 255: S2 = (S2 + S1) MOD 255
    NEXT I 
    IF S1 OR S2 THEN ERROR 2: intnum = -1: EXIT SUB ' Checksum Error
   'End of the checksum  
    bReady = -1
END IF
'This is where we call our assembler interrupt calling function
DEF SEG = VARSEG(a(1))
    CALL ABSOLUTE(intnum, VARSEG(InReg), VARPTR(InReg),VARSEG(OutReg), VARPTR(OutReg),0)
END SUB
```

## Primera llamada a interrupción

Una vez habilitada la capacidad de usar interrupciones, podemos hacer la primera llamada:

```qbasic
DIM SHARED Regs as RegtypeX  ' Define la variable compartida

Regs.ax = 0                  ' Función 0: inicializa ratón y detecta si está presente
CALL INTERRUPTX(&H33, Regs, Regs)  ' Llama a la interrupción 33h con los parámetros

' Verifica resultados
IF Regs.ax THEN
    PRINT "Ratón habilitado, tiene "; Regs.bx; " botones."
ELSE
    PRINT "No se detectó ratón, o el controlador no está cargado"
END IF
```

## Funciones principales del ratón (INT 33h)

### Función 0 - Reiniciar ratón/Verificar instalación

```
Entrada:
AX = 00

Retorno:
AX = 0000  controlador no instalado
     FFFF  controlador instalado
BX = número de botones
```

Esta función reinicia el ratón a los valores predeterminados:

- El cursor se posiciona en el centro de la pantalla
- El cursor se reinicia y se oculta
- No se habilitan interrupciones
- Umbral de doble velocidad establecido en 64 mickeys por segundo
- Relación mickey a píxel horizontal (8 a 8)
- Relación mickey a píxel vertical (16 a 8)
- Se establecen anchura y altura máximas para el modo de vídeo

### Función 1 - Mostrar cursor del ratón

```
Entrada:
AX = 01

No retorna valores
```

Incrementa el contador del cursor; el cursor se muestra si el contador es cero. El valor predeterminado del contador es -1, por lo que el cursor está inicialmente oculto.

### Función 2 - Ocultar cursor del ratón

```
Entrada:
AX = 02

No retorna valores
```

Decrementa el contador del cursor; oculta el cursor si el contador no es cero.

Hay dos razones principales para ocultar el cursor:

- No deseas que el usuario lo vea
- En modos gráficos, el controlador del ratón guarda la imagen detrás del cursor. Si no ocultas el cursor antes de actualizar una imagen, cuando el cursor se mueva, restaurará la imagen anterior a tu actualización. Por lo tanto: ocultar ratón → actualizar → mostrar ratón.

### Función 3 - Obtener posición y estado de botones

```
Entrada:
AX = 03

Retorno:
CX = posición horizontal (X) (0..639)
DX = posición vertical (Y) (0..199)
BX = estado de botones:    
     bit 0: botón izquierdo (1 = presionado)
     bit 1: botón derecho (1 = presionado)
     bits 2-15: sin usar
```

Una forma fácil de usar el ratón es consultar regularmente esta función.

### Función 4 - Establecer posición del cursor

```
Entrada:
AX = 4
CX = posición horizontal
DX = posición vertical

No retorna valores
```

- Después de inicializar el ratón, el cursor está en el centro de la pantalla
- La posición debe estar dentro del rango del modo de vídeo actual
- La posición puede redondearse para ajustarse a la resolución del modo de pantalla

### Función 7 - Establecer límites horizontales

```
Entrada:
AX = 7
CX = posición horizontal mínima
DX = posición horizontal máxima

No retorna valores
```

- Restringe el movimiento horizontal del ratón a una ventana
- Si el valor mínimo es mayor que el máximo, se intercambian

### Función 8 - Establecer límites verticales

```
Entrada:
AX = 8
CX = posición vertical mínima
DX = posición vertical máxima

No retorna valores
```

- Restringe el movimiento vertical del ratón a una ventana
- Si el valor mínimo es mayor que el máximo, se intercambian

### Función 9 - Definir cursor gráfico

```
Entrada:
AX = 9
BX = posición horizontal del punto caliente (-16 a 16)
CX = posición vertical del punto caliente (-16 a 16)
ES:DX = puntero a las máscaras de pantalla y cursor (mapa de bits de 16 bytes)

No retorna valores
```

- El cursor gráfico siempre es en blanco y negro
- Para el punto caliente, el origen de las coordenadas (0,0) es la esquina superior izquierda del mapa de bits
- ES:DX apunta a un array de 64 bytes:
  - bytes 0-31: mapa de bits de máscara de pantalla (16x16) 1 bit por píxel
  - bytes 31-63: mapa de bits de máscara del cursor (16x16) 1 bit por píxel

### Función A - Definir cursor de texto

```
Entrada:
AX = 0A
BX = 00 cursor de software o atributo
     01 cursor de hardware

Si es cursor de hardware:
CX = línea de inicio del cursor
DX = línea final del cursor

Si es cursor de software/atributo:
CX = máscara: atributo*256+carácter para AND
DX = máscara: atributo*256+carácter para XOR

No retorna valores
```

- El cursor de hardware es un rectángulo que puedes formatear como el cursor de texto en la instrucción LOCATE de QB
- El cursor de software/atributo permite hacer cursores coloridos

## Coordenadas usadas por las funciones del ratón

Las coordenadas del cursor utilizadas en las funciones de INT 33h no siempre corresponden a píxeles o caracteres en la pantalla. Tras obtener las coordenadas del cursor, deberás rescalarlas para que se ajusten a tus necesidades.

Las coordenadas a utilizar son:

- X en el rango 0-639 para todos los modos VGA
- Y dependiendo del modo de pantalla:
  - Modo 0 (50 líneas): Y = 0-399
  - Modo 0 (25 líneas), Modos 1, 2, 7, 8, 13: Y = 0-199
  - Modos 11, 12: Y = 0-479
  - Modos 9, 10: Y = 0-349

## Programa de ejemplo

```qbasic
'MOUSE DEMO
'Modificado de una publicación en QB45.COM por herman 

'Usuarios de QB4.5: Iniciar Qb con /lqb
'Usuarios de QBasic1.1: borrar la siguiente línea y pegar TYPE y SUB de arriba

'$INCLUDE:'QB.BI'
DIM SHARED Regs AS RegTypeX
CLS: SCREEN 13

Regs.Ax = 0: Mouse                                        'inicializarlo
IF Regs.Ax = 0 THEN PRINT "¡Ratón no presente!": END      'sin ratón > sin demo
Regs.Ax = 1: Mouse                                        'mostrarlo
DO                                                        'consultarlo           
    Regs.Ax = 3: Mouse                                   
    LOCATE , 0: 
    PRINT USING "X: ###  Y:###"; Regs.Cx; Regs.Dx;        'mostrar coordenadas
LOOP UNTIL Regs.Bx = 1                                    'hasta que se presione el botón izquierdo 
END

SUB Mouse                                                      
CALL INTERRUPTX(&H33, Regs, Regs)
END SUB
```

## Detección de clics y arrastre

- Para detectar clics, leer el bit 0 (botón izquierdo) y bit 1 (botón derecho) del registro BX
- Para detectar doble clic y arrastres, comparar el estado actual de los botones con el estado anterior:
  - Si hay un botón presionado, se ha movido desde la última consulta y el botón ya estaba presionado, es un arrastre
  - Si no se mueve y detectas presionado-no presionado-presionado de nuevo en un corto período de tiempo, es un doble clic

## Cursores personalizados

- Puedes crear tus propios cursores gráficos, similar a crear un sprite y su máscara, pero en un solo color
- Para un cursor multicolor, debes ocultar el cursor predeterminado y seguir colocando tu propio sprite donde debería estar el cursor del ratón
- En modos de texto, siempre tienes un cursor rectangular, solo puedes modificar su altura o color

## Conclusión

Con estas instrucciones, puedes implementar soporte para ratón en tus programas de QBasic. Una vez habilitado, simplemente sigue consultando su estado y compara las coordenadas devueltas por la función 3 con las coordenadas de los objetos en la pantalla para saber si el usuario hace clic sobre ellos.
