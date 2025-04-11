# Manejo de Archivos

QBasic ofrece un conjunto completo de instrucciones para trabajar con archivos, permitiendo leer y escribir datos en almacenamiento permanente. Esta funcionalidad es esencial para crear programas que puedan guardar información entre sesiones.

## Conceptos Básicos

### Tipos de Acceso a Archivos

QBasic soporta varios modos de acceso a archivos:

- **Secuencial**: Lectura/escritura lineal del archivo desde el principio
- **Aleatorio**: Acceso directo a registros específicos en cualquier posición
- **Binario**: Lectura/escritura de datos sin formato

### Canales de Archivo (File Handles)

Cada archivo abierto en QBasic usa un número de canal. Puedes especificar un número o usar `FREEFILE` para obtener el siguiente disponible.

## Trabajando con Archivos Secuenciales

### Abrir y Cerrar Archivos

```qbasic
' Abrir un archivo para OUTPUT (escritura)
OPEN "datos.txt" FOR OUTPUT AS #1

' Escribir algunos datos
PRINT #1, "Hola Mundo"
PRINT #1, 123, 456, "texto"

' Cerrar archivo
CLOSE #1

' Abrir para INPUT (lectura)
OPEN "datos.txt" FOR INPUT AS #1

' Leer datos
INPUT #1, texto$
INPUT #1, a, b, c$

' Cerrar archivo
CLOSE #1
```

También puedes usar `FREEFILE` para obtener el siguiente canal disponible:

```qbasic
fileNum% = FREEFILE
OPEN "datos.txt" FOR OUTPUT AS #fileNum%
```

### Modos de Apertura

- `OUTPUT`: Crea un nuevo archivo (borra si existe)
- `INPUT`: Abre para lectura
- `APPEND`: Añade al final de un archivo existente

### Lectura y Escritura

#### PRINT

Escribe datos a un archivo secuencial:

```qbasic
PRINT #1, "Texto"
PRINT #1, variable
PRINT #1, a; b; c       ' Separados por espacios
PRINT #1, a, b, c       ' Separados por tabulaciones
```

#### INPUT

Lee datos formateados de un archivo secuencial:

```qbasic
INPUT #1, a$           ' Lee texto hasta coma o fin de línea
INPUT #1, a%, b#, c$   ' Lee múltiples variables
```

#### LINE INPUT

Lee una línea completa (incluyendo comas, comillas, etc.):

```qbasic
LINE INPUT #1, texto$  ' Lee toda la línea
```

#### EOF

Comprueba si se ha llegado al final del archivo:

```qbasic
OPEN "datos.txt" FOR INPUT AS #1

DO WHILE NOT EOF(1)
    LINE INPUT #1, texto$
    PRINT texto$
LOOP

CLOSE #1
```

### Ejemplo: Guardar y Cargar un Registro

```qbasic
TYPE Persona
    Nombre AS STRING * 30
    Edad AS INTEGER
    Salario AS DOUBLE
END TYPE

DIM p AS Persona

' Guardar registros
SUB GuardarDatos(archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR OUTPUT AS #fileNum%
    
    p.Nombre = "Juan Pérez"
    p.Edad = 35
    p.Salario = 45000.5
    
    PRINT #fileNum%, p.Nombre
    PRINT #fileNum%, p.Edad
    PRINT #fileNum%, p.Salario
    
    CLOSE #fileNum%
END SUB

' Cargar registros
SUB CargarDatos(archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR INPUT AS #fileNum%
    
    LINE INPUT #fileNum%, p.Nombre
    INPUT #fileNum%, p.Edad
    INPUT #fileNum%, p.Salario
    
    CLOSE #fileNum%
    
    PRINT "Nombre: "; p.Nombre
    PRINT "Edad: "; p.Edad
    PRINT "Salario: "; p.Salario
END SUB
```

## Archivos de Acceso Aleatorio

Los archivos de acceso aleatorio permiten leer y escribir registros en cualquier posición del archivo.

### Definición de Estructura

```qbasic
TYPE Empleado
    ID AS INTEGER
    Nombre AS STRING * 30
    Departamento AS STRING * 20
    Salario AS SINGLE
END TYPE

DIM E AS Empleado
```

### Abrir y Cerrar Archivos Aleatorios

```qbasic
' Abrir archivo aleatorio
OPEN "empleados.dat" FOR RANDOM AS #1 LEN = LEN(E)

' Cerrar archivo
CLOSE #1
```

### Lectura y Escritura

#### PUT

Escribe un registro en la posición especificada:

```qbasic
E.ID = 101
E.Nombre = "Juan Pérez"
E.Departamento = "Contabilidad"
E.Salario = 45000!

PUT #1, E.ID, E  ' Guarda en la posición E.ID
```

#### GET

Lee un registro de la posición especificada:

```qbasic
GET #1, 101, E   ' Lee el registro en posición 101
PRINT E.Nombre, E.Departamento, E.Salario
```

### Ejemplo: Base de Datos Simple

```qbasic
CONST MAX_EMPLEADOS = 100

TYPE Empleado
    ID AS INTEGER
    Nombre AS STRING * 30
    Departamento AS STRING * 20
    Salario AS SINGLE
END TYPE

DIM E AS Empleado

' Crear archivo si no existe
SUB InicializarBD
    OPEN "empleados.dat" FOR RANDOM AS #1 LEN = LEN(E)
    CLOSE #1
END SUB

' Añadir un empleado
SUB AgregarEmpleado(id%, nombre$, departamento$, salario!)
    OPEN "empleados.dat" FOR RANDOM AS #1 LEN = LEN(E)
    
    E.ID = id%
    E.Nombre = nombre$
    E.Departamento = departamento$
    E.Salario = salario!
    
    PUT #1, id%, E
    
    CLOSE #1
END SUB

' Buscar un empleado
SUB BuscarEmpleado(id%)
    OPEN "empleados.dat" FOR RANDOM AS #1 LEN = LEN(E)
    
    GET #1, id%, E
    
    IF E.ID = id% THEN
        PRINT "ID: "; E.ID
        PRINT "Nombre: "; RTRIM$(E.Nombre)
        PRINT "Departamento: "; RTRIM$(E.Departamento)
        PRINT "Salario: "; E.Salario
    ELSE
        PRINT "Empleado no encontrado"
    END IF
    
    CLOSE #1
END SUB
```

## Archivos Binarios

Los archivos binarios permiten leer y escribir datos en su formato "crudo", sin conversión.

```qbasic
' Escribir matriz en archivo binario
SUB GuardarMatrizBinaria(matriz%(), archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR BINARY AS #fileNum%
    
    filas% = UBOUND(matriz, 1)
    columnas% = UBOUND(matriz, 2)
    
    ' Guardar dimensiones
    PUT #fileNum%, , filas%
    PUT #fileNum%, , columnas%
    
    ' Guardar datos
    FOR i = 1 TO filas%
        FOR j = 1 TO columnas%
            PUT #fileNum%, , matriz%(i, j)
        NEXT j
    NEXT i
    
    CLOSE #fileNum%
END SUB

' Leer matriz desde archivo binario
SUB CargarMatrizBinaria(matriz%(), archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR BINARY AS #fileNum%
    
    ' Leer dimensiones
    GET #fileNum%, , filas%
    GET #fileNum%, , columnas%
    
    REDIM matriz%(filas%, columnas%)
    
    ' Leer datos
    FOR i = 1 TO filas%
        FOR j = 1 TO columnas%
            GET #fileNum%, , matriz%(i, j)
        NEXT j
    NEXT i
    
    CLOSE #fileNum%
END SUB
```

## Funciones y Comandos Adicionales

### LOC y LOF

- `LOC(n)`: Devuelve la posición actual en el archivo
- `LOF(n)`: Devuelve el tamaño del archivo en bytes

```qbasic
PRINT "Tamaño del archivo: "; LOF(1); " bytes"
PRINT "Posición actual: "; LOC(1)
```

### SEEK

Cambia la posición actual en el archivo:

```qbasic
SEEK #1, 1   ' Posiciona al inicio del archivo
```

### KILL

Elimina un archivo:

```qbasic
KILL "archivo.txt"
```

### NAME

Renombra un archivo:

```qbasic
NAME "viejo.txt" AS "nuevo.txt"
```

### MKDIR y RMDIR

Crea y elimina directorios:

```qbasic
MKDIR "datos"
RMDIR "datos"
```

### CHDIR

Cambia el directorio actual:

```qbasic
CHDIR "c:\proyectos"
```

## Técnicas Avanzadas

### Encriptación Simple de Archivos

```qbasic
' Encripta/desencripta un archivo con XOR
SUB EncriptarArchivo(origen$, destino$, clave%)
    OPEN origen$ FOR BINARY AS #1
    OPEN destino$ FOR BINARY AS #2
    
    tamaño& = LOF(1)
    
    FOR i& = 1 TO tamaño&
        GET #1, , b%
        b% = b% XOR clave%
        PUT #2, , b%
    NEXT i&
    
    CLOSE #1, #2
END SUB
```

### Guardado de Sprites en Archivos

```qbasic
' Guardar array de sprite en archivo
SUB GuardarSprite(sprite%(), archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR BINARY AS #fileNum%
    
    ' Obtener tamaño del sprite
    tamaño% = UBOUND(sprite)
    
    ' Guardar tamaño
    PUT #fileNum%, , tamaño%
    
    ' Guardar datos del sprite
    FOR i = 0 TO tamaño%
        PUT #fileNum%, , sprite%(i)
    NEXT i
    
    CLOSE #fileNum%
END SUB

' Cargar array de sprite desde archivo
SUB CargarSprite(sprite%(), archivo$)
    fileNum% = FREEFILE
    OPEN archivo$ FOR BINARY AS #fileNum%
    
    ' Leer tamaño
    GET #fileNum%, , tamaño%
    
    ' Dimensionar array
    REDIM sprite%(tamaño%)
    
    ' Leer datos
    FOR i = 0 TO tamaño%
        GET #fileNum%, , sprite%(i)
    NEXT i
    
    CLOSE #fileNum%
END SUB
```

## Manejo de Errores

Es importante manejar posibles errores al trabajar con archivos:

```qbasic
ON ERROR GOTO ManejadorErrores

OPEN "datos.txt" FOR INPUT AS #1
' Código para procesar el archivo
CLOSE #1
END

ManejadorErrores:
    IF ERR = 53 THEN ' Archivo no encontrado
        PRINT "El archivo no existe"
    ELSEIF ERR = 70 THEN ' Permiso denegado
        PRINT "No se puede acceder al archivo"
    ELSE
        PRINT "Error: "; ERR
    END IF
    RESUME Next
```

## Recomendaciones de Buenas Prácticas

1. **Siempre cierra tus archivos**: Usar `CLOSE` para cada archivo abierto
2. **Comprueba si los archivos existen** antes de intentar leerlos
3. **Usa FREEFILE** para obtener automáticamente números de canal
4. **Maneja errores** relacionados con los archivos
5. **Libera recursos** abriendo archivos solo cuando sea necesario
6. **Haz copias de seguridad** antes de sobrescribir datos importantes
