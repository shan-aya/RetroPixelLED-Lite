# 🕹️ Integración con Batocera (Modo Arcade Lite)

El **Modo Arcade** en la versión Lite permite que tu matriz LED funcione como una marquesina dinámica. El panel detectará qué juego estás lanzando en **Batocera** y mostrará automáticamente su logo en formato BMP de 24 bits.

#### Aprovechamiento de Recursos (Scraping)
La principal ventaja de este sistema es que **utiliza las imágenes que ya has scrapeado en Batocera** (marquesinas/wheel art). El script de PowerShell se encarga de buscarlas, redimensionarlas y convertirlas automáticamente.

## 1. La Filosofía de Visualización (Jerarquía)
A diferencia del modo galería, el modo Arcade busca contenido específico. Para que el panel nunca esté vacío, el sistema utiliza una lógica de "cascada" de archivos:

1.  **Marquesina del Juego:** Busca el archivo específico del juego (ej: `mslug.bmp`).
2.  **Logo del Sistema:** Si no existe el juego, busca `logo.bmp` en la carpeta del sistema.
3.  **Imagen Default:** Si nada de lo anterior existe, muestra el recurso maestro en `/Arcade/default.bmp`.

## 2. Preparación de Assets (Script de PowerShell)
Para que el ESP32 encuentre los archivos de forma instantánea entre miles de juegos, el sistema utiliza una **Búsqueda Binaria**. Para ello, los archivos deben estar indexados y procesados correctamente.

### 2.1 🛠️ Cómo usar el Script (Ejecutar Script Marquesinas)
El script se encuentra en la carpeta `Batocera/tools` del proyecto. Consta de dos archivos `Ejecutar Script Marquesinas.bat` y `Script.ps1`.

1.  **Conecta la SD** de tu Retro Pixel LED al PC.
2.  **Ejecuta el archivo** `Ejecutar Script Marquesinas.bat` (Lanzador para evitar bloqueos de Windows).
3.  **Configuración de rutas:**
    * **Origen:** Introduce la ruta de tus ROMs de Batocera (ej: `\\192.168.1.119\share\roms`).
    * **Destino:** Introduce la ruta `C:\Export_Arcade` o directamente la letra de la unidad SD por ejemplo `F:\`. Se recomienda la ruta `C:\Export_Arcade` el script será más rápido.
4.  **Selección de Sistema:** El script detectará automáticamente qué sistemas tienen un archivo `gamelist.xml`. Puedes elegir procesar uno solo por su número, varios o **Todos (0)**.
5.  **Copiar:** Si seleccionaste la ruta `C:\Export_Arcade` copia la carpeta  `arcade` y todo su contenido a la raiz de la SD, como se indica en siguiente punto.
   
### ¿Qué hace el script automáticamente?
* **Redimensionado:** Convierte tus marquesinas originales a **128x32 píxeles**.
* **Formato:** Fuerza el color a **BMP de 24 bits** (formato compatible con el driver DMA del ESP32).
* **Índices .txt:** Genera archivos de texto (ej: `neogeo.txt`) ordenados alfabéticamente. Estos archivos son los que lee el ESP32 para saber qué archivos existen sin explorar toda la SD.

> [!CAUTION]
> **Acceso por Red (Samba):**
> Si al intentar acceder a la ruta `ej-> \\192.168.1.119\share\roms` Windows te solicita credenciales, utiliza las que trae Batocera por defecto:
> * **Usuario:** `root`
> * **Contraseña:** `linux`

## 3. Estructura de archivos en la SD

Para que la integración funcione correctamente, la tarjeta SD debe mantener el siguiente orden jerárquico tras ejecutar el script Marquesinas:

* **`/arcade/default.bmp`** (Imagen de reserva general si falla todo lo demás. Esta debe ser creada por el usuario).
* **`/arcade/sistema.txt`** (Índice de juegos generado, ej: `neogeo.txt`).
* **`/arcade/sistema/logo.bmp`** (Logo que se muestra si falta la marquesina específica del juego.Esta debe ser creada por el usuario).
* **`/arcade/sistema/rom_name.bmp`** (Marquesina del juego procesada, ej: `mslug.bmp`).

Como usuario, tu única tarea manual de personalización es:
1.  **Logo del Sistema:** Añadir un archivo `logo.bmp` en la carpeta de cada sistema (ej. `/Arcade/mame/logo.bmp`).
2.  **Imagen Default:** Añadir un archivo `default.bmp` en la raíz de la carpeta `/Arcade/` como fondo de emergencia.

A futuro se creará un script que le daras una imagen y automaticamente la redimensionara a 128x32 pixeles.

#### Ejemplo visual de carpetas:
```
📂 arcade/
├── 📄 default.bmp
├── 📄 neogeo.txt
├── 📄 mame.txt
├── 📂 neogeo/
│   ├── 📄 logo.bmp
│   ├── 📄 mslug.bmp
│   └── 📄 kof98.bmp
└── 📂 mame/
    ├── 📄 logo.bmp
    └── 📄 pacman.bmp
```
## 4. Configuración en Batocera (Comunicación)
Para que Batocera notifique al panel cada vez que cambias de juego, utilizaremos scripts de usuario.

### A. Configurar la IP del Panel
1. **Usa un editor avanzado:** Abre los archivos `pixel_start.sh`, `pixel_stop.sh` y `pixel_off.sh` con **Notepad++**, **VS Code** o **Sublime Text**.
2. **Cambia la IP:** Busca la línea del comando y sustituye la IP de ejemplo por la IP de tu ESP32.
3. **Verifica el formato Unix:** En Notepad++, asegúrate de que en la esquina inferior derecha indique **Unix (LF)**. Si dice Windows (CRLF), ve a *Editar > Conversión de fin de línea > Convertir a Formato Unix (LF)*.
4. **Guarda los cambios.**

> [!CAUTION]
>  No utilices el Bloc de Notas básico de Windows, ya que cambiaría el formato de fin de línea a Windows (CRLF) y el script dejará de funcionar en Batocera.

### B. Ubicación de los Scripts
Copia los archivos de la carpeta `/Batocera/tools/Script/` a la siguiente ruta en tu sistema Batocera (vía red Samba):
`\\IP_DE_TU_BATOCERA\share\system\configs\emulationstation\scripts`

Organiza los archivos en estas subcarpetas:
* `/game-start/pixel_start.sh` (Se activa al lanzar un juego).
* `/game-end/pixel_stop.sh` (Se activa al salir al menú).

### C. Asignación de Permisos de Ejecución
  Es **obligatorio** otorgar permisos de ejecución a los archivos mediante una consola SSH (como PuTTY). Ejecuta los siguientes comandos:

  1. **Conéctate por SSH:** Abre PuTTY, introduce la IP de tu Batocera en "Host name" y pincha en "Open".
     <img width="453" height="444" alt="Putty Configuración" src="https://github.com/user-attachments/assets/2eec17d6-36b0-4ef5-bea6-b4d30aa8ee01" />

  2. **Identificate:** Usa el usuario `root` y contraseña `linux`.
     <img width="661" height="519" alt="Putty login" src="https://github.com/user-attachments/assets/46308f5f-d01a-4495-97f7-e8c07bc3915f" />

  3. **Otorgar permisos a los script:** Copia y pega (clic derecho en PuTTY para pegar) es romendable enviarlos de un en uno:
     <img width="862" height="516" alt="Putty permisos" src="https://github.com/user-attachments/assets/9d38f2d1-4065-40ad-a100-7651da94c1af" />
      ```bash
      # Comandos para otorgar permisos de ejecución
      chmod +x /userdata/system/configs/emulationstation/scripts/game-start/pixel_start.sh 
      chmod +x /userdata/system/configs/emulationstation/scripts/game-end/pixel_stop.sh
      ```
  4. **Verificar permisos de los script:** Copia y pega (clic derecho en PuTTY para pegar) es romendable enviarlos de un en uno:
     <img width="871" height="516" alt="Putty verificar permisos" src="https://github.com/user-attachments/assets/863665be-9eb3-42c8-a1a4-06f39dfbb7ba" />
      ```bash
      # Comandos para verificar permisos de ejecución:
      ls -l /userdata/system/configs/emulationstation/scripts/game-start/pixel_start.sh
      ls -l /userdata/system/configs/emulationstation/scripts/game-end/pixel_stop.sh
      ```
      **Ejemplo de permisos de ejecución correctos:**-rwxr-xr-x 1 root root 320 ene 10 13:18 /userdata/system/configs/emulationstation/scripts/game-end/pixel_stop.sh

## 5. Funcionamiento en Tiempo Real

* **Al lanzar un juego:** Batocera envía el sistema y el nombre de la ROM. El panel busca en el índice y muestra la marquesina correspondiente.
* **Al salir del juego:** Batocera envía el comando `STOP`. El panel interrumpe el modo Arcade y vuelve automáticamente a la reproducción de **GIFs** o al **Reloj**.
* **Gestión de Errores:** Gracias a la lógica de cascada, si el juego es nuevo y aún no has procesado su BMP, el panel mostrará el logo del sistema, manteniendo siempre una estética profesional en tu recreativa.

> [!CAUTION]
> Cada vez que añadas nuevos juegos o hagas un "Scrape" en Batocera, **debes volver a ejecutar el script de PowerShell** en tu PC para actualizar los índices y las imágenes en la SD. Sin este paso, el ESP32 no sabrá que los nuevos archivos existen.

## 6. Configuración Crítica: IP Fija para el ESP32

Para que el modo **🕹️ Arcade** de Batocera funcione siempre correctamente, es fundamental que el ESP32 mantenga siempre la misma dirección IP.

> [!TIP]
> **Asignar IP fija al ESP32:** > Los scripts de Batocera envían las órdenes (como cambiar el GIF al lanzar un juego) a una dirección IP específica que tú configuras manualmente. Si el router reinicia y le asigna una IP distinta al ESP32, la comunicación se cortará y el panel dejará de actualizarse.
>
> **¿Cómo hacerlo?**
> 1. Accede a la configuración de tu router.
> 2. Busca la sección de **DHCP Estático** o **Asignación de IP por MAC**.
> 3. Vincula la dirección MAC de tu ESP32 con la IP que hayas escrito en tus scripts (ej: `192.168.1.109`).
> 4. Dado que cada router es diferente, si tienes dudas busca en Google: *"Cómo asignar IP fija [modelo de tu router]"*.
