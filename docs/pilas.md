# Implementación de Pilas (Stacks) en QBasic

Una pila (stack) es una estructura de datos fundamental en la programación. Este artículo explica cómo implementar y utilizar una pila en QBasic.

## ¿Qué es una Pila?

Una pila es una colección de elementos que sigue el principio LIFO (Last In, First Out - Último en entrar, Primero en salir). Esto significa que el último elemento añadido a la pila será el primero en ser eliminado.

Las operaciones básicas de una pila son:

- **Push**: Añadir un elemento a la parte superior de la pila
- **Pop**: Eliminar y devolver el elemento superior de la pila
- **Verificar si está vacía**: Comprobar si la pila no contiene elementos

## Implementación en QBasic

Para implementar una pila en QBasic, podemos utilizar un array. Sin embargo, esto nos limitará a una pila de tamaño fijo (bounded stack) ya que los arrays en QBasic tienen dimensiones predefinidas.

### Componentes necesarios

1. Un array para almacenar los elementos de la pila
2. Una variable para rastrear la posición del elemento superior (top) de la pila
3. Una constante que defina la profundidad máxima de la pila
4. Funciones para las operaciones básicas de la pila

### Código de implementación

A continuación se presenta una implementación completa de una pila acotada en QBasic:

```qbasic
'  Implementación de una Pila Acotada (Bounded Stack)

DECLARE FUNCTION EmptyStack! ()    ' Función que verifica si la pila está vacía
DECLARE SUB InStack (X!)           ' Procedimiento para añadir un elemento a la pila
DECLARE FUNCTION OutStack! ()      ' Función para extraer un elemento de la pila

DIM SHARED DeepStack, TopStack     ' Variables globales para la profundidad y el tope de la pila

CONST True = -1
CONST False = NOT True

' Inicialización de la pila
DeepStack = 100                    ' Definir la profundidad máxima de la pila
DIM SHARED Stack(DeepStack)        ' Definir el array para la pila
TopStack = 0                       ' El tope se inicia en 0 (pila vacía)

' Función para verificar si la pila está vacía
FUNCTION EmptyStack
  IF TopStack = 0 THEN
    EmptyStack = True
  ELSE
    EmptyStack = False
  END IF
END FUNCTION

' Procedimiento para añadir un elemento a la pila (Push)
SUB InStack (X)
  IF TopStack = DeepStack THEN
    PRINT "Error: ¡La pila está llena!"
    STOP
  ELSE
    TopStack = TopStack + 1
    Stack(TopStack) = X
  END IF
END SUB

' Función para extraer un elemento de la pila (Pop)
FUNCTION OutStack
  IF TopStack = 0 THEN
    PRINT : PRINT "Error: ¡La pila está vacía!"
    STOP
  ELSE
    OutStack = Stack(TopStack)
    TopStack = TopStack - 1
  END IF
END FUNCTION
```

## Programa de demostración

El siguiente programa demuestra el uso de una pila acotada. Primero, añade una serie de números secuenciales a la pila y luego los extrae y los muestra en pantalla:

```qbasic
'  Programa de Demostración de una Pila Acotada

DECLARE FUNCTION EmptyStack! ()
DECLARE SUB InStack (X!)
DECLARE FUNCTION OutStack! ()

DIM SHARED DeepStack, TopStack

CONST True = -1
CONST False = NOT True

' Inicialización de la pila
DeepStack = 100                ' Definiendo la profundidad de la pila
DIM SHARED Stack(DeepStack)    ' Definiendo el array para la pila
TopStack = 0

' Demostración de la operación de la pila
CLS
PRINT "Demostración de una Pila Acotada"
PRINT "         (profundidad de la pila - 100)"
PRINT
INPUT "¿Número de elementos a añadir a la pila? "; NS

' Llenando la pila
FOR I = 1 TO NS
  CALL InStack(I)
NEXT I

' Extrayendo elementos de la pila
INPUT "¿Número de elementos a extraer de la pila? ", OS
FOR I = 1 TO OS
  IF NOT EmptyStack THEN
    X = OutStack
    PRINT X;
  ELSE
    PRINT : PRINT "¡La pila está vacía! ";
    PRINT I; "- elemento no puede ser recuperado."
    EXIT FOR
  END IF
NEXT I
END

FUNCTION EmptyStack
  IF TopStack = 0 THEN
    EmptyStack = True
  ELSE
    EmptyStack = False
  END IF
END FUNCTION

SUB InStack (X)
  IF TopStack = DeepStack THEN
    PRINT "Error: ¡La pila está llena!"
    STOP
  ELSE
    TopStack = TopStack + 1
    Stack(TopStack) = X
  END IF
END SUB

FUNCTION OutStack
  IF TopStack = 0 THEN
    PRINT : PRINT "Error: ¡La pila está vacía!"
    STOP
  ELSE
    OutStack = Stack(TopStack)
    TopStack = TopStack - 1
  END IF
END FUNCTION
```

## Explicación del programa

1. El programa solicita al usuario cuántos elementos desea añadir a la pila.
2. Añade los números del 1 al NS a la pila (operación Push).
3. Si NS es mayor que 100 (la profundidad máxima de la pila), mostrará un mensaje de desbordamiento (overflow) y detendrá el programa.
4. Luego solicita cuántos elementos desea extraer de la pila.
5. Extrae y muestra OS elementos de la pila (operación Pop).
6. Si se intenta extraer más elementos de los que contiene la pila, mostrará un mensaje indicando que la pila está vacía.

## Aplicaciones prácticas de las pilas

Las pilas son estructuras de datos muy útiles para diversas aplicaciones:

1. **Evaluación de expresiones matemáticas**: Utilizadas para evaluar expresiones en notación polaca inversa o para convertir entre diferentes notaciones.
2. **Control de recursión**: Los lenguajes de programación utilizan pilas para gestionar llamadas a funciones recursivas.
3. **Verificación de paréntesis balanceados**: Útil para verificar si las expresiones tienen sus paréntesis, corchetes y llaves correctamente balanceados.
4. **Algoritmos de búsqueda en profundidad**: Utilizados en la exploración de árboles y grafos.
5. **Historial de navegación**: Los navegadores web utilizan pilas para implementar el historial (botón "Atrás").

## Ampliaciones posibles

La implementación básica puede mejorarse de varias maneras:

1. **Pila dinámica**: Aunque QBasic tiene limitaciones, se podría implementar una pila que pueda crecer dinámicamente usando REDIM.
2. **Pila de diferentes tipos de datos**: Modificar la pila para almacenar diferentes tipos de datos utilizando tipos definidos por el usuario.
3. **Verificación de desbordamiento silenciosa**: En lugar de detener el programa, podría devolver un código de error.
4. **Implementación de operaciones adicionales**: Como Peek (ver el elemento superior sin extraerlo) o operaciones de búsqueda.

## Conclusión

Las pilas son estructuras de datos fundamentales que son relativamente fáciles de implementar en QBasic utilizando arrays. Aunque esta implementación está limitada por el tamaño fijo del array, proporciona todas las operaciones básicas de una pila y puede ser útil para muchas aplicaciones.

*Este artículo está basado en una publicación original en Peter Cooper's BASIX Fanzine, Número #1 de noviembre de 1995, escrito por Adrian, que posteriormente formó parte del libro "The Revolutionary Guide to QBasic" de Wrox Press.*
