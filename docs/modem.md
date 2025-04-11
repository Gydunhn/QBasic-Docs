# Uso del Módem en QBasic

*Autor: Matthew River Knight (HORIZONS Interactive Entertainment)*

El módem ha sido posiblemente el dispositivo informático más influyente jamás creado. Su aparición ha transformado por completo el mundo de los negocios y la publicidad, ha permitido comunicaciones internacionales más económicas y eficientes, y ha tenido un enorme impacto tanto en profesionales como en aficionados a la informática.

Para muchos, el módem es una pequeña caja mágica que abre nuestro ordenador al mundo. En realidad, es muy sencillo de usar y, lo que es más importante, de programar.

## ¿Cómo funciona un módem?

El módem toma bytes de datos, los convierte en señales acústicas y los envía a través de la línea telefónica. Teóricamente, si pudieras silbar lo suficientemente rápido y con los tonos adecuados, podrías enviar un archivo mediante los sonidos que produces.

Básicamente, el ordenador envía un archivo a través del módem de la siguiente manera:

1. El módem envía un carácter ASCII para informar al otro ordenador que está enviando un archivo
2. Divide ese archivo en fragmentos y lo envía por bloques de bytes
3. El módem espera un carácter ASCII del otro ordenador confirmando que todo está bien, para enviar el siguiente fragmento

## Programando el módem en QBasic

Programar comunicaciones con el módem en QBasic es sorprendentemente sencillo. Solo necesitas 4 comandos básicos:

1. **OPEN COM**: Abre el módem para que lo use QBasic
2. **INPUT$**: Obtiene datos del módem
3. **LOC**: Indica si el módem ha recibido datos de una fuente externa
4. **PRINT #**: Envía datos al módem

### Estableciendo comunicación con el módem

Para empezar a trabajar con el módem, primero debes abrir un "camino" hacia él. Esto es similar a abrir un archivo, pero se utiliza la sentencia `OPEN COM`:

```qbasic
OPEN "COM2:2400,N,8,1,RB2048,TB2048" FOR RANDOM AS #1
```

Aunque parece complicado, es bastante simple. Lo único que debes tener en cuenta es:

- El número entre "COM" y ":" es el puerto COM en el que está conectado tu módem (COM1 o COM2)
- El número entre ":" y ",N" es la velocidad (baudios) del módem

QBasic solo puede acceder a los puertos COM 1 y 2. Si tu módem está en otro puerto, existen formas de modificar las direcciones de memoria de los puertos COM, pero eso es tema para otro artículo.

En cuanto a los baudios, QBasic no puede acceder a los puertos COM a velocidades superiores a 9600. Así que aunque tengas un módem de 56K, QBasic no podrá utilizarlo a más de 9600 baudios.

### Enviando comandos al módem

Para enviar datos a tu módem se utiliza la sentencia `PRINT #n`, donde n es el número de archivo (en el ejemplo anterior, 1). Sin embargo, todavía no estás conectado a nada, solo has creado una ruta entre tu programa y el módem.

Para que el módem se comunique con una fuente externa (como un BBS), debes indicarle que marque un número. Para ello, debes utilizar los comandos AT integrados en la memoria del módem:

| Comando del módem | Significado                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------- |
| ATDT###-###-####  | Marca el número ###-###-####                                                              |
| ATZ               | Cuelga el teléfono                                                                        |
| ATS0=#            | Espera a que alguien llame y el teléfono suene # veces, luego intenta conectar los módems |
| ATM2H1L#          | Establece el volumen del altavoz en # (1-3)                                               |

### Ejemplo: Marcador telefónico simple

Aquí tienes un programa simple que marcará un número telefónico:

```qbasic
CLS
PRINT "Abriendo una ruta hacia tu módem..."
OPEN "COM2:2400,N,8,1,RB7048,TB7048" FOR RANDOM AS #1
PRINT "Por favor, introduce el número de teléfono que deseas llamar"
INPUT PhoneNumber$
PRINT "Comunicándose con tu módem..."
PRINT #1, "ATDT"; PhoneNumber$
PRINT "¡Listo! Levanta el teléfono y habla."
PRINT "Presiona la tecla ESC para colgar."
DO
LOOP UNTIL INKEY$ = CHR$(27)
PRINT #1, "ATZ"
```

### Leyendo la información del módem

El mayor desafío en la programación de módems con QBasic es leer los datos que llegan. Para esto se utiliza la función `LOC`, que indica si el módem ha recibido algo:

```qbasic
LOC(n)  ' donde n es el número de archivo
```

LOC te indica dónde te encuentras en un archivo. ¿Archivo? ¡Pero estamos intentando acceder al módem! Como dije antes, los archivos y dispositivos funcionan de la misma manera. Pero con un módem, LOC te dice si ha recibido algo.

Para leer lo que está recibiendo el módem, utilizas `INPUT$(x,y)`, donde:

- x es el número de bytes a obtener
- y es el número del archivo/dispositivo abierto

El valor x debería ser SIEMPRE 1. Sé que esto significa que solo se puede leer 1 carácter en cada pasada, pero de esta manera se lee CADA carácter, y no se omite ninguno. Si estuvieras recibiendo una transmisión de 11 bytes, y x fuera 2, solo se leerían los primeros 10 caracteres (porque 10 es múltiplo de 2). La última parte se omitiría. Esta es la forma para comunicaciones NORMALES. ¡Mantén x como 1!

## Programa completo de comunicaciones

Este es un programa completo de comunicaciones con módem que te permite conectarte a cualquier BBS e interactuar con él:

```qbasic
CLS
PRINT "Programa de comunicaciones simple en QBasic."
PRINT "¿Qué puerto COM utiliza tu módem?"
INPUT ">", port$
baud$ = "9600" '9600 debería funcionar bien con la mayoría de los módems. Si tienes
              'uno más antiguo, usa 2400.
'Abre ese puerto COM.
OPEN "COM" + port$ + ":" + baud$ + ",N,8,1,RB2048,TB2048" FOR RANDOM AS #1

PRINT "OPCIONES:"
PRINT "1-Llamar a otro ordenador"
PRINT "2-Esperar una llamada"
PRINT "3-Salir"
DO
a = VAL(INKEY$)
LOOP UNTIL a >= 1 AND a <= 3
IF a = 3 THEN CLOSE : SYSTEM
IF a = 2 THEN GOTO wait

PRINT "¿Número a llamar?"
INPUT ">", number$
PRINT #1, "ATDT" + number$  'Indica al módem que marque el número.
GOTO chat
wait:
PRINT #1, "ATS0=1"         'Indica al módem que se conecte después de 1 timbre.

'Cuando un módem se conecta, devuelve "CONNECT ####"
'El siguiente bloque de código espera hasta que el módem se conecte antes de continuar

a$ = ""
DO
IF LOC(1) THEN a$ = a$ + INPUT$(1, 1) 'Si hay algo en el módem, añádelo a a$
LOOP UNTIL INSTR(a$, "CONNECT") 'Espera hasta que los módems se hayan conectado.

chat:
'Si estabas esperando una llamada, se imprimirán muchos caracteres ASCII
'en la pantalla. No te preocupes, son solo los ordenadores sincronizándose y
'comunicándose. Además, no verás lo que escribes.

CLS
PRINT "Ahora estás listo para chatear, presiona ESC para salir."
DO
t$ = INKEY$
IF LEN(t$) THEN PRINT #1, t$    'Si escribiste algo, envíalo al módem
                                'esto será enviado por el módem al otro
                                'ordenador
IF LOC(1) THEN r$ = INPUT$(1, 1) 'Si hay algo que recibir, guárdalo en r$

IF LEN(r$) THEN PRINT r$;       'Si r$ <> "" entonces imprímelo. ";" significa que
                                'no se inicia una nueva línea
LOOP UNTIL t$ = CHR$(27)        'Continúa haciendo esto hasta que se presione ESC
PRINT #1, "ATZ"                 'Indica al módem que cuelgue
CLOSE                           'Cierra la sentencia COM abierta
```

## Limitaciones y consideraciones

- QBasic solo puede acceder a los puertos COM 1 y 2
- La velocidad máxima es de 9600 baudios
- Este programa básico no incluye capacidades de carga o descarga de archivos
- Al esperar una llamada, se mostrarán muchos caracteres ASCII en la pantalla durante la sincronización

## Conclusión

¡Y eso es todo! ¿Fácil, verdad? Ahora tienes un programa genial que puedes usar para hablar con tus amigos a través del módem en QBasic.

La programación de comunicaciones con módem en QBasic es mucho más sencilla de lo que podría parecer inicialmente. Con solo cuatro comandos fundamentales (`OPEN COM`, `INPUT$`, `LOC` y `PRINT #`), puedes crear programas funcionales para comunicarte con otros ordenadores a través de la línea telefónica.

Este conocimiento básico te permitirá experimentar con las comunicaciones por módem y sentar las bases para desarrollar aplicaciones más complejas que incluyan transferencia de archivos y otros protocolos de comunicación.

Antes de dejarte experimentar con tus nuevos conocimientos, me gustaría extender mi agradecimiento a las siguientes personas que me han ayudado de alguna manera con mi programación en general: LordAcidus, Petter Holmberg, Tek, ZKman, y por último, pero no menos importante, Christian Garms. Chicos, me habéis ayudado mucho.

¡Feliz hacking para todos!

*Este artículo apareció originalmente en The BASIX Fanzine, Número 17, en abril de 2000.*
