# Preguntas Frecuentes (FAQ)

Esta sección responde a las preguntas más comunes sobre QBasic, su instalación, uso y compatibilidad.

## Preguntas Generales

### ¿Qué es QBasic?

QBasic es un entorno de desarrollo integrado (IDE) y lenguaje de programación basado en BASIC, incluido con MS-DOS 5.0 y versiones posteriores. Es una versión simplificada de Microsoft QuickBASIC, destinada principalmente a principiantes y programadores aficionados.

### ¿Cuál es la diferencia entre QBasic y QuickBASIC?

QBasic es una versión limitada y gratuita de QuickBASIC 4.5, con las siguientes diferencias principales:

- QBasic no incluye un compilador (sólo intérprete)
- QBasic no puede crear archivos ejecutables (.EXE)
- QBasic tiene menos bibliotecas y funcionalidades avanzadas
- QBasic venía incluido gratis con MS-DOS, mientras que QuickBASIC era un producto comercial

### ¿Puede QBasic funcionar en Windows 10/11?

QBasic no funciona nativamente en Windows moderno, pero puede ejecutarse utilizando:

- Emuladores de DOS como DOSBox
- Máquinas virtuales con MS-DOS
- Alternativas modernas como QB64 que son compatibles con la sintaxis de QBasic

## Instalación y Configuración

### ¿Dónde puedo obtener QBasic?

QBasic venía incluido con MS-DOS 5.0-6.22. Puedes encontrar copias archivadas en:

- [WinWorld PC - QBasic 1.x](https://winworldpc.com/product/qbasic/1x)
- [Archive.org - Microsoft QuickBasic](https://archive.org/details/microsoft-quick-basic-1.00)

### ¿Cómo puedo ejecutar QBasic en mi computadora moderna?

La forma más común es utilizando DOSBox:

1. Instala DOSBox desde [dosbox.com](https://www.dosbox.com/)
2. Crea una carpeta para QBasic
3. Copia los archivos de QBasic (QBASIC.EXE, QBASIC.HLP)
4. Configura DOSBox para montar esa carpeta
5. Ejecuta `QBASIC.EXE` desde DOSBox

También puedes usar QB64, una versión moderna de QBasic compatible con sistemas actuales.

### ¿Cuáles son los requisitos de sistema para QBasic original?

QBasic original requiere:

- DOS 5.0 o superior
- 512 KB de RAM (se recomiendan 640 KB)
- Disco duro con al menos 1.5 MB de espacio libre
- Procesador 8088 o superior (funciona mejor con 286 o superior)

## Programación en QBasic

### ¿Cómo puedo comenzar a programar en QBasic?

1. Inicia QBasic desde DOSBox o tu entorno preferido
2. Escribe un programa simple, por ejemplo:

   ```qbasic
   PRINT "Hola Mundo"
   ```

3. Presiona F5 para ejecutar el programa
4. Utiliza el menú File > Save para guardar tu programa

### ¿Hay un límite en el tamaño de los programas en QBasic?

Sí, QBasic tiene un límite aproximado de 160 KB para el código fuente. Si necesitas crear programas más grandes, considera usar:

- Múltiples módulos de código
- QuickBASIC en lugar de QBasic
- Una implementación moderna como QB64

### ¿Cómo puedo depurar mi programa en QBasic?

QBasic ofrece herramientas básicas de depuración:

- F8 para ejecutar el programa paso a paso
- F9 para establecer/quitar puntos de interrupción
- F10 para ejecutar una subrutina completa
- Puedes agregar la instrucción `PRINT` para mostrar valores durante la ejecución
- Utiliza la ventana Watch para monitorear variables (Alt+F5, Add)

### ¿QBasic soporta gráficos?

Sí, QBasic incluye comandos para gráficos básicos:

- `SCREEN` para establecer el modo gráfico
- `LINE`, `CIRCLE`, `PSET` para dibujar formas básicas
- `PUT` y `GET` para manipular áreas de la pantalla
- `PAINT` para rellenar áreas
- Consulta la sección [Gráficos](graficos.md) para más detalles

## Preguntas Técnicas Avanzadas

### ¿Cómo crear un archivo EXE?

Para convertir un programa BASIC en un archivo EXE que se ejecute sin necesidad de otro programa, necesitas un compilador. Hay varios disponibles, comerciales y shareware. Normalmente un compilador tiene más características que un intérprete, incluyendo la posibilidad de crear TSRs e incorporar código ensamblador en el programa BASIC.

### ¿Cómo implementar soporte para ratón?

Para esto, se deben realizar llamadas al controlador del ratón. Puedes crear tu propio cursor de ratón en modo texto y hacer que los botones en la pantalla funcionen mediante llamadas a interrupciones del sistema.

```qbasic
' Ejemplo simplificado de inicialización del ratón
SUB InitMouse (present%)
    DEF SEG = 0
    present% = 0
    INT86old 51, 0, 0, 0, 0, 0, 0, present%, 0
END SUB
```

### ¿Cómo leer un directorio del disco?

Para hacer esto, es necesario llamar a interrupciones BIOS. Se pueden implementar rutinas para listar archivos y directorios mediante la interrupción 21h.

```qbasic
' Ejemplo simplificado para listar archivos
SUB ListFiles (path$)
    DIM DTA AS STRING * 43
    DEF SEG = VARSEG(DTA)
    POKE VARPTR(DTA) + 21, 0  ' Atributos de archivo normales
    ' Código para llamar INT 21h, función 4Eh
END SUB
```

### ¿Cómo mostrar archivos BMP o GIF?

Para cargar imágenes BMP, debes leer la cabecera del archivo, decodificar la información y enviarla a la pantalla. Existen rutinas específicas para diferentes formatos de archivo:

```qbasic
' Ejemplo parcial para cargar un BMP
SUB LoadBMP (filename$)
    OPEN filename$ FOR BINARY AS #1
    ' Leer cabecera BMP
    ' Decodificar datos
    ' Mostrar en pantalla
    CLOSE #1
END SUB
```

### ¿Cómo capturar CTRL-BREAK y CTRL-ALT-DEL?

Esto se hace utilizando la sentencia `ON KEY x GOSUB`, así:

```qbasic
KEY 15, CHR$(&H04) + CHR$(70)           'CTRL-BREAK
KEY 16, CHR$(&H04) + CHR$(&H08) + CHR$(83) 'CTRL-ALT-DEL
ON KEY(15) GOSUB 100
ON KEY(16) GOSUB 200

DO
  ' Código principal
LOOP

100 PRINT "CTRL-BREAK presionado."
RETURN

200 PRINT "CTRL-ALT-DEL presionado."
RETURN
```

Otra forma de deshabilitar CTRL-BREAK es:

```qbasic
' DESHABILITAR CTRL-BREAK:                                              
DIM brk$(3)
' Primero guardar los vectores actuales:                                  
DEF SEG=0: FOR i=108 TO 111: brk$(i-108)=STR$(PEEK(i)): NEXT       
' Luego insertar nuevos vectores de interrupción:                                 
POKE 108,83: POKE 109,255: POKE 110,0: POKE 111,240: DEF SEG       

' RESTAURAR CTRL-BREAK:                                              
DEF SEG=0: FOR i=108 TO 111: POKE i,VAL(brk$(i-108)): NEXT: DEF SEG
```

### ¿Cómo devolver un código de error (Errorlevel) desde mi programa?

Debes DECLARAR una rutina no documentada en la biblioteca LIB/QLB predeterminada:

```qbasic
DECLARE SUB SalirConCodigo ALIAS "_EXIT" (N AS INTEGER)
' Usar CALL SalirConCodigo(valorRetorno%) en lugar de END o SYSTEM
```

El valor pasado debe estar entre 0 y 255, inclusive.

### ¿Cómo usar la SoundBlaster?

Para utilizar la SoundBlaster, debes hacer llamadas directas al hardware o utilizar rutinas de acceso:

```qbasic
' Reproducir un sonido simple en SB
SUB PlaySound (frequency%, duration%)
    OUT &H220, &H10 ' Puerto y comando específicos para SoundBlaster
    ' Más código para configurar tono y duración
END SUB
```

### ¿Cómo leer un archivo .DBF?

Los archivos DBF (dBase III+ o IV) tienen una estructura específica con cabecera y registros. Puedes leerlos abriendo el archivo en modo binario y procesando sus estructuras internas.

### ¿Cómo controlar COM3?

Para usar COM3, a veces es necesario intercambiar los puertos COM2 y COM3:

```qbasic
' Ejemplo para intercambiar COM2 y COM3
SUB SwapCOM23
    DEF SEG = 0
    SWAP PEEK(&H0402), PEEK(&H0404)
    SWAP PEEK(&H0403), PEEK(&H0405)
END SUB
```

### ¿Cómo convertir un número a binario y extraer un bit?

```qbasic
' Convertir a binario
FUNCTION Binary$ (n%)
    DIM b$(16)
    FOR i% = 15 TO 0 STEP -1
        IF n% AND 2^i% THEN
            b$(i%) = "1"
        ELSE
            b$(i%) = "0"
        END IF
    NEXT
    Binary$ = ""
    FOR i% = 15 TO 0 STEP -1
        Binary$ = Binary$ + b$(i%)
    NEXT
END FUNCTION

' Extraer un bit específico (0-15)
FUNCTION GetBit% (n%, bit%)
    GetBit% = SGN(n% AND 2^bit%)
END FUNCTION
```

### ¿Cómo hacer un cronometraje preciso en BASIC?

Para cronometraje preciso, puedes acceder directamente al reloj del sistema:

```qbasic
' Cronometraje preciso
FUNCTION PreciseTimer#
    DEF SEG = &H40
    low% = PEEK(&H6C)
    high% = PEEK(&H6D)
    overf% = PEEK(&H6E)
    PreciseTimer# = (65536# * overf% + 256# * high% + low%) / 18.2
END FUNCTION
```

### ¿Cómo hacer una verificación CRC?

El código CRC (Cyclic Redundancy Check) permite verificar la integridad de los datos:

```qbasic
' Función CRC-16 simplificada
FUNCTION CRC16% (data$)
    DIM i%, j%
    DIM crc%
    crc% = 0
    FOR i% = 1 TO LEN(data$)
        crc% = crc% XOR (ASC(MID$(data$, i%, 1)) * 256)
        FOR j% = 0 TO 7
            IF (crc% AND &H8000) THEN
                crc% = ((crc% * 2) XOR &H1021)
            ELSE
                crc% = crc% * 2
            END IF
        NEXT j%
    NEXT i%
    CRC16% = crc% AND &HFFFF
END FUNCTION
```

### ¿Cómo reiniciar la computadora desde un programa BASIC?

Esto se hace mediante una instrucción de salida directa al hardware:

```qbasic
' Reiniciar la computadora
OUT &H64, &HFE
```

### ¿Cómo imprimir gráficos con BASIC?

Para imprimir gráficos, es necesario enviar comandos específicos a la impresora:

```qbasic
' Ejemplo simplificado para imprimir gráficos
SUB PrintGraphics (filename$)
    ' Inicializar la impresora en modo gráfico
    LPRINT CHR$(27) + "G";  ' Comando ESC G para modo gráfico en algunas impresoras
    ' Cargar y enviar datos gráficos
END SUB
```

### ¿Cómo hacer una entrada de texto controlada en BASIC?

Para entrada de texto con control completo, necesitas implementar tu propia rutina:

```qbasic
FUNCTION Edit$ (row%, col%, length%, text$)
    ' Implementación de edición de texto con control completo
    ' Manejo de teclas de cursor, inserción/sobreescritura, etc.
END FUNCTION
```

### ¿Cómo leer y escribir directamente en la pantalla de texto?

Los caracteres se almacenan en la memoria de video usando dos bytes por carácter:

```qbasic
' Acceso directo a la memoria de video
DEF SEG = &HB800
POKE 0, 65      ' Colocar una A en la esquina superior izquierda
POKE 1, 1       ' Colorearla de azul
X = PEEK(157)   ' Obtener valor ASCII del carácter en la esquina superior derecha
```

### ¿Cómo convertir un archivo BASIC interpretado a formato ASCII?

GW-BASIC guarda los archivos en formato comprimido, a menos que especifiques `,A` después del nombre del archivo. Si no tienes GW-BASIC, puedes usar RB versión 1.58 para convertir los archivos a ASCII.

## Compatibilidad y Alternativas

### ¿Qué alternativas modernas existen para QBasic?

Varias alternativas modernas mantienen la compatibilidad con QBasic:

- [QB64](https://qb64.com/) - Muy compatible con QBasic pero con capacidades modernas
- [FreeBASIC](https://freebasic.net/) - Compatible con sintaxis QBasic pero más cercano a C/C++
- [SmallBASIC](https://smallbasic.sourceforge.net/) - Intérprete BASIC liviano con sintaxis similar

### ¿Puedo convertir mis programas de QBasic a otros lenguajes?

No existe una herramienta automática completa para convertir código QBasic a lenguajes modernos, pero:

- QB64 permite usar código QBasic original y compilarlo para sistemas modernos
- Existen guías para migrar manualmente de QBasic a Python, JavaScript y otros lenguajes
- Las estructuras lógicas son similares en muchos lenguajes, lo que facilita la conversión manual

### ¿Los programas hechos en QBasic funcionan en QB64?

En general, sí. QB64 mantiene una alta compatibilidad con QBasic, con algunas excepciones:

- Algunas instrucciones específicas de hardware pueden comportarse diferente
- QB64 tiene extensiones y características adicionales que no existen en QBasic
- Muy raramente, puede haber pequeñas diferencias de comportamiento en algunas funciones

## Recursos y Comunidad

### ¿Existen comunidades activas de QBasic?

Sí, aunque más pequeñas que en años anteriores:

- [QB64 Forum](https://forum.qb64.org/)
- [Reddit r/qbasic](https://www.reddit.com/r/qbasic/)
- [Pete's QBasic Site](http://www.petesqbsite.com/)
- [QB64 Discord](https://discord.gg/sb4UPvEaXQ)

### ¿Dónde puedo encontrar ejemplos de código QBasic?

Puedes encontrar ejemplos en:

- [QBasic.net](https://www.qbasic.net/)
- [Pete's QBasic Site](http://www.petesqbsite.com/)
- [Foros de QB64](https://forum.qb64.org/)

### ¿Cómo puedo aprender más sobre QBasic?

Consulta estos recursos:

- La ayuda integrada de QBasic (presiona F1)
- El libro "Microsoft QBasic: Programming for the Absolute Beginner"
- Tutoriales en [QBasic.net](https://www.qbasic.net/en/qbasic-tutorials/beginner/qbasic-beginner-1.htm)
- Videos tutoriales de QBasic en YouTube
- La documentación completa en este sitio

## Códigos de Error

Los siguientes códigos de error pueden aparecer durante la ejecución de programas en QBasic. Conocerlos te ayudará a depurar y solucionar problemas en tus programas.

| Código | Mensaje                    |     | Código | Mensaje                       |
| ------ | -------------------------- | --- | ------ | ----------------------------- |
| 1      | NEXT without FOR           |     | 37     | Argument-count mismatch       |
| 2      | Syntax error               |     | 38     | Array not defined             |
| 3      | RETURN without GOSUB       |     | 40     | Variable required             |
| 4      | Out of DATA                |     | 50     | FIELD overflow                |
| 5      | Illegal function call      |     | 51     | Internal error                |
| 6      | Overflow                   |     | 52     | Bad file name or number       |
| 7      | Out of memory              |     | 53     | File not found                |
| 8      | Label not defined          |     | 54     | Bad file mode                 |
| 9      | Subscript out of range     |     | 55     | File already open             |
| 10     | Duplicate definition       |     | 56     | FIELD statement active        |
| 11     | Division by zero           |     | 57     | Device I/O error              |
| 12     | Illegal in direct mode     |     | 58     | File already exists           |
| 13     | Type mismatch              |     | 59     | Bad record length             |
| 14     | Out of string space        |     | 61     | Disk full                     |
| 16     | String formula too complex |     | 62     | Input past end of file        |
| 17     | Cannot continue            |     | 63     | Bad record number             |
| 18     | Function not defined       |     | 64     | Bad file name                 |
| 19     | No RESUME                  |     | 67     | Too many files                |
| 20     | RESUME without error       |     | 68     | Device unavailable            |
| 24     | Device timeout             |     | 69     | Communication-buffer overflow |
| 25     | Device fault               |     | 70     | Permission denied             |
| 26     | FOR without NEXT           |     | 71     | Disk not ready                |
| 27     | Out of paper               |     | 72     | Disk-media error              |
| 29     | WHILE without WEND         |     | 73     | Feature unavailable           |
| 30     | WEND without WHILE         |     | 74     | Rename across disks           |
| 33     | Duplicate label            |     | 75     | Path/File access error        |
| 35     | Subprogram not defined     |     | 76     | Path not found                |

### Manejo de Errores

Para controlar estos errores en tus programas, puedes utilizar las sentencias `ON ERROR GOTO`:

```qbasic
ON ERROR GOTO ErrorHandler

' Código principal del programa
END

ErrorHandler:
  SELECT CASE ERR
    CASE 53
      PRINT "Error: Archivo no encontrado"
    CASE 61
      PRINT "Error: Disco lleno"
    CASE ELSE
      PRINT "Error #"; ERR; " en línea"; ERL
  END SELECT
RESUME NEXT
```
