# Programación Modular en QBasic

En artículos anteriores, discutimos el uso de módulos en la programación estructurada, pero no entramos en detalles sobre cómo utilizarlos en la programación con QBasic. En este artículo, hablaremos sobre cómo usar diferentes tipos de módulos para organizar correctamente proyectos grandes.

## Dos Tipos de Módulos

En QBasic, tenemos módulos y archivos de inclusión. Basados en su uso, llamamos al primer grupo "Módulos de Código" y al otro "Módulos de Inclusión".

### Módulos de Inclusión

En la tradición de QBasic, estos archivos generalmente tienen la extensión `.BI`. Los módulos de inclusión se utilizan principalmente para contener declaraciones de procedimientos y funciones, variables, variables globales (declaradas usando la instrucción COMMON), tipos definidos por el usuario, etc. Los módulos de inclusión son muy útiles para compartir declaraciones. Aquí hay una lista de algunos de sus usos más importantes:

1. **Declaración de procedimientos**: Cuando tienes un módulo de código que contiene varios procedimientos, debes declarar estos procedimientos en cualquier módulo donde quieras utilizarlos. Puedes colocar todas las declaraciones en un módulo de inclusión (que puedes nombrar igual que el módulo de código pero con extensión `.BI`). Luego, en cada módulo donde quieras usar los procedimientos, puedes poner un meta-comando `$INCLUDE` en la parte superior de ese módulo.

2. **Compartir tipos definidos por el usuario**: Esto significa que no necesitas declarar los tipos definidos por el usuario en cada módulo que los utilice. Simplemente coloca las declaraciones en un módulo de inclusión e inclúyelo en tus módulos de código.

3. **Variables globales**: Cuando quieres tener una variable global accesible en todos los módulos, necesitas declarar las variables en todos los módulos usando una instrucción COMMON. Aquí nuevamente puedes colocar las instrucciones COMMON en un módulo de inclusión e incluirlo en todos los demás módulos.

Hay un punto importante sobre los módulos de inclusión: **No coloques comandos ejecutables en un módulo de inclusión**. Hacerlo puede causar errores si colocas algunos comandos no ejecutables (como DECLARE, COMMON, etc.) después de la instrucción include.

### Módulos de Código

Los módulos de código son los medios para dividir el código discutidos en el artículo "Técnicas de Codificación". Las ideas principales sobre el uso de estos módulos se pueden encontrar en ese artículo. Estos archivos, que en la tradición de QBasic tienen extensión `.BAS`, forman el cuerpo principal de tu proyecto.

## Cómo implementarlo en QBasic

Hemos hablado mucho sobre módulos. Pero, ¿cómo se pueden usar estas técnicas en QBasic? Aquí te mostraré cómo QBasic gestiona múltiples módulos (recordando que las versiones anteriores de QBasic no admiten módulos).

Cada proyecto de QBasic contiene un módulo principal que tiene el punto de inicio del programa. Este código de inicio es el código a nivel de módulo del módulo principal. Otros módulos no deben tener código a nivel de módulo (de hecho, pueden tenerlo, pero el código nunca se ejecutará). Los procedimientos en otros módulos son llamados desde el módulo principal (o desde un procedimiento al que llama el módulo principal).

### Creación de un proyecto multi-módulo

Para crear un proyecto de múltiples módulos desde el entorno de QBasic:

1. Abre tu módulo (o créalo)
2. Desde el menú Archivo, elige "Cargar Archivo" o "Crear Archivo"
3. Ingresa (o elige) el módulo que deseas agregar al proyecto y selecciona OK
4. El módulo se agregará al proyecto

Puedes cambiar entre módulos eligiendo "Subs" desde el menú Ver o presionando F2.

Cuando agregas un módulo al proyecto, QBasic crea (si es necesario) un archivo de proyecto con el mismo nombre que el módulo principal y extensión `.MAK`, y agrega el nombre de los módulos a él. Luego, cada vez que abres el módulo principal, QBasic encuentra y abre los otros módulos (el archivo de proyecto debe estar en la misma carpeta que el módulo principal).

## Ejemplo de Estructura de Proyecto Modular

A continuación se muestra un ejemplo de cómo podrías organizar un proyecto QBasic con múltiples módulos:

### Archivo: GLOBAL.BI (Módulo de Inclusión)

```qbasic
' Declaraciones globales para todo el proyecto
TYPE ConfiguracionType
  RutaArchivos AS STRING * 64
  ModoGrafico AS INTEGER
  ColorFondo AS INTEGER
END TYPE

' Variables globales compartidas
COMMON SHARED Configuracion AS ConfiguracionType
COMMON SHARED ArchivoActual AS STRING * 64
```

### Archivo: GRAFICOS.BI (Módulo de Inclusión)

```qbasic
' Declaraciones de procedimientos gráficos
DECLARE SUB InicializarGraficos (Modo%)
DECLARE SUB DibujarCuadro (x1%, y1%, x2%, y2%, Color%)
DECLARE SUB DibujarMenu ()
DECLARE FUNCTION DetectarTarjetaVGA% ()
```

### Archivo: PRINCIPAL.BAS (Módulo de Código Principal)

```qbasic
' Inclusión de módulos
'$INCLUDE: 'GLOBAL.BI'
'$INCLUDE: 'GRAFICOS.BI'
'$INCLUDE: 'ARCHIVOS.BI'

' Módulo principal del programa
SUB Main
  ' Inicialización
  InicializarPrograma
  
  ' Bucle principal
  DO
    opcion = MostrarMenuPrincipal()
    SELECT CASE opcion
      CASE 1: ProcesarArchivos
      CASE 2: EditarConfiguracion
      CASE 3: MostrarAyuda
      CASE 0: ' Salir
    END SELECT
  LOOP UNTIL opcion = 0
  
  ' Finalización
  FinalizarPrograma
END SUB

' Inicializa los componentes del programa
SUB InicializarPrograma
  ' Inicializar variables
  Configuracion.RutaArchivos = "C:\DATOS\"
  Configuracion.ModoGrafico = 1
  Configuracion.ColorFondo = 1
  
  ' Inicializar gráficos
  InicializarGraficos Configuracion.ModoGrafico
END SUB

' Finaliza el programa y libera recursos
SUB FinalizarPrograma
  ' Código de finalización...
END SUB

' Punto de entrada principal
Main
END
```

### Archivo: GRAFICOS.BAS (Módulo de Código para Gráficos)

```qbasic
'$INCLUDE: 'GLOBAL.BI'
'$INCLUDE: 'GRAFICOS.BI'

' Inicializa el modo gráfico
SUB InicializarGraficos (Modo%)
  SCREEN Modo%
  CLS
  COLOR Configuracion.ColorFondo
END SUB

' Dibuja un cuadro en la pantalla
SUB DibujarCuadro (x1%, y1%, x2%, y2%, Color%)
  LINE (x1%, y1%)-(x2%, y2%), Color%, B
END SUB

' Dibuja el menú en pantalla
SUB DibujarMenu ()
  ' Código para dibujar menú...
END SUB

' Detecta si hay tarjeta VGA disponible
FUNCTION DetectarTarjetaVGA%
  ' Código para detectar hardware...
  DetectarTarjetaVGA% = -1 ' True
END FUNCTION
```

## Conclusión

Organizar tu proyecto en módulos es un excelente método para mantener tu código estructurado y manejable. Si no has probado este método hasta ahora, inténtalo en tu próximo proyecto y observa los resultados. La programación modular permite:

- Mayor claridad y organización del código
- Reutilización de componentes en diferentes proyectos
- Desarrollo en equipo más eficiente
- Mantenimiento más sencillo del código

> *Autor original: Homayoon.P.A.*  
> *Fecha original: 5 de diciembre de 2002*  
> *Publicado originalmente en Sepent Technologies.*
