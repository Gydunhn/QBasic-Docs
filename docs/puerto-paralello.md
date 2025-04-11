# Programación del Puerto Paralelo en QBasic

Si has construido alguna de las interfaces en mi página de circuitos y ahora quieres saber cómo utilizarlas, esta página es para ti. Esta es una introducción simple a la programación del puerto paralelo en QBasic, QuickBasic o un lenguaje similar. Ten en cuenta que la mayoría de los conceptos en esta página también se pueden aplicar a GWBASIC.

## Secciones del Puerto Paralelo

El puerto paralelo se compone de tres secciones diferentes: líneas de datos, líneas de control y líneas de estado.

- **Líneas de Datos**: 8 líneas que son el medio principal para obtener información del puerto.
- **Líneas de Control**: 4 salidas que proporcionan señales de control a la impresora.
- **Líneas de Estado**: 5 entradas que permiten a la impresora comunicar errores, falta de papel y estado ocupado al PC.

### Direcciones del Puerto

| Sección           | Dirección (Decimal) | Dirección (Hex) |
| ----------------- | ------------------- | --------------- |
| Líneas de Datos   | 888                 | 378h            |
| Líneas de Control | 890                 | 37Ah            |
| Líneas de Estado  | 889                 | 379h            |

## Comandos Básicos

Para operar el puerto, se utilizan dos comandos: `OUT` e `INP`.

### Comando OUT

El comando `OUT` se utiliza para enviar un número que representa el patrón binario de las salidas físicas en el puerto.

```basic
OUT [puerto], [número]
```

#### Ejemplos de Uso

```basic
'set port to 00000000
OUT 888, 0
'set port to 10000000
OUT 888, 1
'set port to 10110000
OUT 888, 11
```

### Comando INP

El comando `INP` se utiliza para leer el estado de las líneas de estado.

```basic
[variable] = INP([puerto])
```

#### Ejemplo de Uso

```basic
PortNum% = INP(889)
```

## Subrutina para Controlar el Puerto

Para mantener el estado de un bit mientras se cambia el estado de otro, se puede definir una subrutina:

```basic
SUB OutPort(PortAddress%, OutNum%)
    PortState% = INP(PortAddress%)
    PortNum% = PortState% + OutNum%
    OUT PortAddress%, PortNum%
    IF OutNum% = 0 THEN OUT PortAddress%, 0
END SUB
```

## Función para Leer el Estado de un Bit

Para conocer el estado de un bit específico, se puede usar la siguiente función:

```basic
FUNCTION BitStatus(PortAddress%, BitYouWant%) AS INTEGER
    ' Lógica para determinar el estado del bit
END FUNCTION
```

## Conclusión

Este documento no es exhaustivo y no cubre temas avanzados como puertos bidireccionales o DMA. Sin embargo, proporciona una base sólida para comenzar a trabajar con el puerto paralelo en QBasic.

---

Escrito por Aaron. Originalmente publicado en: [aaroncake.net](http://www.aaroncake.net/electronics/qblpt.htm)
