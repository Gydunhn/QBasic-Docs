# Instalación y Configuración

QBasic es un software antiguo diseñado para MS-DOS, por lo que instalar y ejecutarlo en sistemas operativos modernos requiere el uso de emuladores o herramientas específicas.

## Obtener QBasic

QBasic venía incluido en MS-DOS 5.0 hasta MS-DOS 6.22, así como en algunas versiones tempranas de Windows. Puedes encontrar copias archivadas en:

- [WinWorld PC - QBasic 1.x](https://winworldpc.com/product/qbasic/1x)
- [Archive.org - Microsoft QuickBasic](https://archive.org/details/microsoft-quick-basic-1.00)

Los archivos principales que necesitas son:

- `QBASIC.EXE` - El entorno de desarrollo integrado
- `QBASIC.HLP` - El archivo de ayuda

## Instalación en sistemas modernos

### Usando DOSBox

[DOSBox](https://www.dosbox.com/) es un emulador de DOS que te permite ejecutar QBasic en sistemas operativos modernos.

1. **Instala DOSBox**:
   - Windows: Descarga el instalador desde [dosbox.com](https://www.dosbox.com/download.php?main=1)
   - macOS: `brew install dosbox` con Homebrew
   - Linux: `sudo apt install dosbox` (Ubuntu/Debian) o equivalente

2. **Configura DOSBox**:
   - Crea una carpeta en tu sistema para los archivos de QBasic (por ejemplo, `C:\QBasic` en Windows)
   - Copia `QBASIC.EXE` y `QBASIC.HLP` a esta carpeta
   - Abre el archivo de configuración de DOSBox (normalmente en `~/.dosbox/dosbox-0.74.conf` en Linux/macOS o `C:\Users\[Usuario]\AppData\Local\DOSBox\dosbox-0.74.conf` en Windows)
   - Añade al final:

     ```text
     [autoexec]
     mount c C:\QBasic
     c:
     ```

     (Ajusta la ruta según la ubicación de tu carpeta)

3. **Ejecuta QBasic**:
   - Inicia DOSBox
   - Ejecuta `QBASIC.EXE` escribiendo `qbasic` y presionando Enter

### Alternativa: QB64

[QB64](https://qb64.com/) es una versión moderna de QBasic compatible con sistemas actuales.

1. **Descarga QB64**:
   - Visita [https://qb64.com/](https://qb64.com/) y descarga la versión para tu sistema operativo

2. **Instalación**:
   - Windows: Descomprime el archivo descargado y ejecuta `qb64.exe`
   - macOS/Linux: Descomprime el archivo y ejecuta `./qb64`

QB64 ofrece compatibilidad con programas QBasic originales pero añade características modernas como soporte para resoluciones más altas, más memoria, y la capacidad de compilar a ejecutables nativos.

## Configuración del entorno de desarrollo

### En QBasic original (vía DOSBox)

1. **Ajustes de pantalla**:
   - Presiona Alt+Enter para cambiar entre pantalla completa y ventana
   - Ajusta la resolución de DOSBox en el archivo de configuración si es necesario

2. **Opciones de QBasic**:
   - Dentro de QBasic, accede al menú `Options` para configurar:
     - `Display...`: Cambia colores y disposición del editor
     - `Environment...`: Configura tabulaciones y otros aspectos

### En QB64

1. **Opciones de visualización**:
   - Menú `View > Options` para ajustar colores, fuentes y comportamiento del editor

2. **Carpetas de trabajo**:
   - Configura las carpetas por defecto desde el menú `File > Preferences`

## Recursos adicionales

- [Guía de DOSBox](https://www.dosbox.com/wiki/Main_Page) - Wiki oficial con instrucciones detalladas
- [QB64 Phoenix Edition Wiki](https://qb64phoenix.com/wiki/index.php) - Documentación actualizada para QB64
