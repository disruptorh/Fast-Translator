# Fast Translator

Traducción offline con CTranslate2 y asistente de IA local vía Ollama, ambos
conectados a un atajo de teclado global: seleccionas texto en cualquier
aplicación, pulsas el atajo y el resultado sustituye la selección.

<p align="center">
  <a href="https://github.com/disruptorh/Fast-Translator/releases/latest/download/Fast_translator.zip">
    <img alt="Descargar" src="https://img.shields.io/badge/%E2%AC%87%20Download-latest%20release-2f6feb?style=for-the-badge&logo=github&logoColor=white">
  </a>
  <a href="https://github.com/disruptorh/Fast-Translator/releases/latest">
    <img alt="Versiones" src="https://img.shields.io/github/v/release/disruptorh/Fast-Translator?label=release&style=flat&logo=github&logoColor=white">
  </a>
  <a href="./LICENSE">
    <img alt="Licencia" src="https://img.shields.io/badge/licencia-MIT-blue?style=flat">
  </a>
</p>

---

## 🖼️ Capturas

<div align="center">
<table>
<tr>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/installed%20languages.png" alt="Paquetes instalados" style="max-width:100%;">
<p align="center"><em>Paquetes instalados</em></p>
</td>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/aviable%20languages.png" alt="Paquetes disponibles" style="max-width:100%;">
<p align="center"><em>Paquetes disponibles</em></p>
</td>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/ai%20providers.png" alt="Ajustes de IA" style="max-width:100%;">
<p align="center"><em>Ajustes de IA</em></p>
</td>
</tr>
</table>
</div>

## ✨ Qué hace

- **Traducción offline** — motor [CTranslate2](https://github.com/OpenNMT/CTranslate2) con los modelos de [Argos Translate](https://github.com/argosopentech/argos-translate) instalados en local. `index.json` cataloga **98 pares** de paquetes.
- **Traducción en cadena** — `language_graph.cpp` construye un grafo de idiomas desde los paquetes instalados y calcula la ruta más corta entre dos idiomas que no tienen modelo directo, pasando por un idioma intermedio.
- **IA local con Ollama** — consulta a la API `/api/generate` de [Ollama](https://ollama.com) en `http://localhost:11434`. Sin API keys, sin nube.
- **Roles de IA** — cada rol guarda su propio *system prompt* y su procesador de respuesta (`RoleManager`, config persistente).
- **Gestor gráfico** — descarga, instala y borra paquetes de modelo; configura pares de idiomas, atajos y modelos de IA.
- **CUDA opcional** — CTranslate2 se compila con GPU si hay `nvcc` y cuDNN; si no, CPU con oneDNN.

### Los dos binarios

| Binario | Qué hace | Dependencias en runtime |
|---|---|---|
| `fast-translator` | Traduce o consulta a la IA el texto del portapapeles y lo escribe de vuelta | `xclip`, `xdotool`, `libnotify` |
| `fast-translator-manager` | GUI wxWidgets: paquetes, atajos, modelos, roles | wxWidgets 3.2 |

En el código fuente se compilan como `Fast_translator` y
`Fast_translator_manager`; el paquete `.deb` los instala como `fast-translator` y
`fast-translator-manager`. El atajo global espera esos nombres con guion, así que
usa siempre el nombre con guion.

---

## 📥 Descarga rápida

El asset de la release es un `.zip` (`Fast_translator.zip`). No es ejecutable
directamente: guárdalo, descomprímelo y lanza los binarios que contenga.

```bash
# 1. Descargar el .zip de la última release
curl -L -o Fast_translator.zip https://github.com/disruptorh/Fast-Translator/releases/latest/download/Fast_translator.zip

# 2. Descomprimir y ver qué trae
unzip -o Fast_translator.zip && ls -l
```

Si lo que quieres es la instalación completa (binarios + gestor gráfico +
paquetes de modelos ya resueltos), el camino reproducible es el `.deb`, que
genera `build_deb.sh` (ver [Build del `.deb`](#-build-del-deb)).

---

## 🚀 Uso rápido

El programa está pensado para ser invisible y estar siempre listo. Traduce lo que
tienes seleccionado, sin ventana de entrada.

### 1. Configura el atajo (primera vez)

```bash
# Abrir el gestor gráfico (o el lanzador del menú de aplicaciones)
fast-translator-manager
```

1. En **Settings** / **Shortcut**, elige primero el par de idiomas (el comando del
   atajo depende del par).
2. Pulsa **Set Shortcut**: el gestor copia el comando al portapapeles y abre los
   ajustes de teclado de tu escritorio. Detecta **KDE**, **GNOME** y **XFCE**; en
   el resto muestra las instrucciones a mano.
3. Pega el comando en un atajo nuevo, por ejemplo `Ctrl+Alt+T`.

Hay tres atajos independientes: atajo de traducción, atajo de IA y atajo de IA por
rol.

### 2. Traducir

1. Selecciona texto en cualquier aplicación (navegador, editor, documento).
2. Pulsa el atajo.
3. El texto seleccionado se sustituye por la traducción.

### 3. Preguntar a la IA (Ollama)

1. Instala y arranca [Ollama](https://ollama.com) (`ollama serve`).
2. En el gestor, pulsa **Refresh** para listar los modelos de `localhost:11434`.
3. Elige modelo (y rol, si quieres) y pulsa **Set Shortcut**.

Si Ollama no responde, la herramienta devuelve
`Error: Failed to connect to Ollama. Is it running? (ollama serve)`.

---

## 📦 Compilar desde código

### Requisitos

- Linux x86-64 (Debian/Ubuntu para el instalador automático de dependencias).
- CMake **3.26** o superior (`cmake_minimum_required(VERSION 3.26)`).
- Compilador C++17.
- GPU NVIDIA opcional, pero recomendada: si hay `nvcc`, CTranslate2 se compila
  con soporte CUDA.

**Aviso honesto: las dependencias son pesadas.** SentencePiece y CTranslate2 se
compilan desde fuente (con `make -j$(nproc)`), y CTranslate2 se parchea para que
GCC 12 funcione con CUDA 12. Cuenta con eso antes de lanzar el build.

### Clonar

```bash
# 1. Clonar el repositorio
git clone https://github.com/disruptorh/Fast-Translator.git
cd Fast-Translator
```

### Dependencias

```bash
# 2. Instalar dependencias (paquetes del sistema + SentencePiece y CTranslate2 desde fuente)
sudo ./scripts/install_dependencies.sh
```

Qué hace `scripts/install_dependencies.sh`:

- Instala con `apt` solo lo que falte de: `build-essential cmake git curl wget
  pkg-config libcurl4-openssl-dev libwxgtk3.2-dev xclip xdotool libnotify-bin
  libdnnl-dev gcc-12 g++-12`.
- Si `pkg-config` no encuentra SentencePiece, clona
  `https://github.com/google/sentencepiece.git`, lo compila en modo `Release` e
  instala en `/usr/local`.
- Si no encuentra `libctranslate2.so` en `/usr/local/lib` ni `/usr/lib`, clona
  `https://github.com/OpenNMT/CTranslate2.git`, parchea su `CMakeLists.txt`
  añadiendo `-allow-unsupported-compiler` a `CUDA_NVCC_FLAGS` (GCC 12 es
  técnicamente más nuevo que el máximo oficialmente soportado por CUDA 12), y
  compila con `CC=gcc-12 CXX=g++-12` y
  `-DCMAKE_BUILD_TYPE=Release -DBUILD_CLI=OFF -DWITH_MKL=OFF -DWITH_DNNL=ON`.
- **Autodetección de CUDA**: si encuentra `nvcc` compila con
  `-DWITH_CUDA=ON` y busca cabeceras `cudnn.h` para activar `-DWITH_CUDNN=ON`. Si
  no las encuentra, avisa y compila con `-DWITH_CUDNN=OFF` (más lento). Sin
  `nvcc`, la build es solo CPU.

El script usa `sudo` para `apt-get` y `make install`, así que te pedirá contraseña.

### Compilar

```bash
# 3. Build completo (dependencias + CMake + compilación)
./build_native.sh
```

`build_native.sh` llama a `install_dependencies.sh`, exporta
`PKG_CONFIG_PATH=/usr/local/lib/pkgconfig` y `LD_LIBRARY_PATH=/usr/local/lib`, y
configura `build/` en modo `Release`. Si existe `$HOME/vcpkg/scripts/buildsystems/vcpkg.cmake`
lo pasa como `CMAKE_TOOLCHAIN_FILE`; si no, avisa y sigue con las librerías del
sistema.

```bash
# 3b. Borrar build/ y empezar de cero
./build_native.sh clean
```

A mano, si ya tienes las dependencias instaladas:

```bash
cmake -B build -S . -DCMAKE_BUILD_TYPE=Release && cmake --build build --config Release --parallel
```

Qué compila `CMakeLists.txt`:

- `Fast_translator` (CLI): `pkg_check_modules(sentencepiece REQUIRED)`,
  `pkg_check_modules(protobuf REQUIRED)`, `find_package(CURL REQUIRED)` y enlace
  directo a `/usr/local/lib/libctranslate2.so`. Si `find_package(CUDAToolkit
  QUIET)` encuentra CUDA, añade `CUDA::cudart` y `CUDA::cublas`.
- `Fast_translator_manager` (GUI): solo si `find_package(wxWidgets CONFIG QUIET)`
  encuentra wxWidgets; enlaza `wx::core`, `wx::base`, `wx::net` y CURL. Sin
  wxWidgets el target simplemente no se crea y se avisa por consola.
- Todo se enlaza con `-static-libgcc -static-libstdc++` para portabilidad.

### Ejecutar los tests

```bash
# No hay tests automatizados en el repositorio
```

No existe suite de tests. La comprobación práctica es el modo `--test` de la CLI,
que no toca el portapapeles ni X11:

```bash
./build/Fast_translator --test "hola mundo" es:en
```

### Ejecutar la aplicación

```bash
# Traducción de lo que tengas seleccionado (necesita X11 + xclip + xdotool)
./build/Fast_translator es:en

# Modo IA con un modelo de Ollama servido en localhost
./build/Fast_translator --ollama llama3.2

# Gestor gráfico (si se compiló, es decir, si había wxWidgets)
./build/Fast_translator_manager
```

El par de idiomas se escribe `origen:destino` (por ejemplo `es:en`). Si el par
directo no existe entre los paquetes instalados, `language_graph.cpp` busca la
ruta más corta por un idioma intermedio.

Los modelos se buscan en este orden: `packages/` junto al ejecutable,
`../packages`, `packages` y `../packages` relativos al directorio de trabajo.
El `.deb` deja un symlink de `/usr/share/fast-translator/packages` a
`/usr/lib/fast-translator/packages` para que el primer caso funcione.

### Build del `.deb`

```bash
# 4. Empaquetar .deb (llama antes a install_dependencies.sh)
./build_deb.sh
```

Qué hace `build_deb.sh`:

1. Llama a `scripts/install_dependencies.sh` y compila en `build-deb/` en
   `Release` (también usa el toolchain de vcpkg si existe).
2. Copia los ejecutables a `usr/lib/fast-translator/` y **bundlea las librerías**:
   recorre la salida de `ldd` de ambos binarios, copia cada `.so`, añade el
   dynamic loader que devuelve `readelf -l` (campo `interpreter`) y copia
   explícitamente `/usr/local/lib/libctranslate2.so*` y
   `/usr/local/lib/libsentencepiece.so*`.
3. Elimina el `RPATH` de todo lo empaquetado con `patchelf --remove-rpath`.
4. Crea wrappers en `/usr/bin` (`fast-translator` y `fast-translator-manager`) que
   ejecutan el binario con el loader y las librerías del paquete
   (`ld-linux-x86-64.so.2 --library-path /usr/lib/fast-translator ...`) y, si el
   proceso falla, muestran el log con `zenity`, `kdialog` o `notify-send`.
5. Copia los paquetes de modelo de `$HOME/.local/share/argos-translate/packages`
   y de `./packages` a `usr/share/fast-translator/packages/`.
6. Genera los `.desktop` (`fast-translator-manager.desktop` y
   `fast-translator.desktop`), el icono SVG en
   `usr/share/icons/hicolor/128x128/apps/`, `DEBIAN/control` y un `postinst` que
   explica el uso.
7. Empaqueta con `dpkg-deb --build --root-owner-group` y deja el `.deb` en `dist/`.

Metadatos del paquete: nombre `fast-translator`, versión **1.0.2**, arquitectura
`amd64`, `Depends: xclip, zenity`, `Recommends: libcurl4 | libcurl3`,
`Suggests: ollama`.

```bash
# 5. Instalar el .deb generado
sudo dpkg -i dist/fast-translator_1.0.2_amd64.deb && sudo apt-get install -f
```

---

## 🧰 Comandos útiles / Opciones

### CLI (`Fast_translator`)

```bash
# Traducir la selección del portapapeles de español a inglés
./build/Fast_translator es:en

# Consultar a un modelo de Ollama en vez de traducir
./build/Fast_translator --ollama llama3.2
```

Flags que acepta `main.cpp`:

| Flag | Efecto |
|---|---|
| `es:en` | Par de idiomas `origen:destino` de la traducción. |
| `--test`, `-t` | Modo debug: no toca portapapeles ni X11. Imprime trazas `[DEBUG]`/`[ERROR]` por stderr. |
| `--ollama`, `-o` | Consulta a Ollama en lugar de traducir. |
| `--role`, `-r` | Con `--ollama`, carga de la config el *system prompt* y el procesador de respuesta del rol indicado. |

Modos de `--test` (los tres funcionan; el parser decide si `argv[2]` es texto o
un par de idiomas buscando `:`):

```bash
# Texto y par de idiomas explícitos
./build/Fast_translator --test "hola mundo" es:en

# Texto por stdin
echo "hola mundo" | ./build/Fast_translator --test es:en

# Solo texto (sin par de idiomas)
./build/Fast_translator --test "hola mundo"
```

### Gestión de paquetes

El gestor gráfico es la vía normal para instalar paquetes:

```bash
# 1. Abrir el gestor
fast-translator-manager
```

La GUI descarga el `.zip` del catálogo de `index.json` y lo descomprime con
`unzip -o` en el directorio de paquetes. El mismo catálogo se puede consultar
sin arrancar nada:

```bash
# Ver cuántos pares hay disponibles
python3 -c "import json;print(len(json.load(open('index.json'))))"
```

---

## 🗂️ Estructura del proyecto

```text
.
├── CMakeLists.txt            # Targets Fast_translator y Fast_translator_manager
├── CMakeLists_deb.txt        # Variante de CMakeLists para la build del .deb
├── build_native.sh           # Dependencias + CMake + compilación (acepta `clean`)
├── build_deb.sh              # Empaquetado .deb 1.0.2 amd64 con librerías bundleadas
├── index.json                # Catálogo de paquetes Argos Translate (98 pares)
├── LICENSE                   # MIT
├── img/                      # Capturas y GIF de demostración
├── packages/                 # Paquetes de modelo ya descargados
│   └── en_de/                #   metadata.json + model/ + sentencepiece.model + stanza/
├── scripts/
│   └── install_dependencies.sh  # apt + SentencePiece + CTranslate2 desde fuente
└── src/
    ├── main.cpp              # CLI: portapapeles → traducción/IA → sustitución
    ├── gui_main.cpp          # wxWidgets: paquetes, atajos, roles, modelos
    ├── translation.{h,cpp}   # wrapper de CTranslate2 (load_model, translate)
    ├── language_graph.{h,cpp}# grafo de idiomas + BFS para traducción en cadena
    ├── ollama.{h,cpp}        # cliente HTTP de la API /api/generate y /api/tags
    ├── role_manager.{h,cpp}  # roles de IA (system prompts) persistidos
    ├── response_processor.*  # limpieza de la respuesta del modelo
    ├── tokenizer.h           # interfaz Tokenizer
    ├── tokenizer_sp.h        # SentencePiece
    ├── tokenizer_bpe.{h,cpp} # BPE legacy
    ├── utils.{h,cpp}         # portapapeles, notificaciones, DE, rutas
    └── json.hpp              # nlohmann/json (header-only)
```

La lógica de atajos está duplicada a propósito en `gui_main.cpp`
(`OnSetShortcut`, `OnAISetShortcut`, `OnAIRoleSetShortcut`): comparten la misma
detección de escritorio, pero cada una prefija su propio comando.

---

## 🔐 Seguridad y privacidad

- Todo el texto se procesa **en local**. La traducción no sale de la máquina.
- La comunicación con Ollama es **HTTP plano** contra `http://localhost:11434`,
  sin TLS. Solo es aceptable porque el servidor es local; si expones Ollama a la
  red, cambia la URL y añade autenticación por tu cuenta.
- Los modelos de Ollama son los que tú instalas. Fast Translator solo los
  consulta; no descarga ni ejecuta modelos por su cuenta.
- Los atajos globales ejecutan un binario local con el texto seleccionado, así
  que el atajo es código con privilegios de tu usuario de sesión.

---

## 📄 Licencia

MIT — ver [LICENSE](LICENSE).
