# Fast Translator

<div align="center">

**⚡ Traducción offline y asistente de IA local, a un atajo de teclado**

[![Latest Release](https://img.shields.io/github/v/release/disruptorh/Fast-Translator?style=for-the-badge&label=Download&color=blue)](https://github.com/disruptorh/Fast-Translator/releases/latest)

[**📥 Descargar el último `.deb`**](https://github.com/disruptorh/Fast-Translator/releases/latest)

![Licencia](https://img.shields.io/badge/License-MIT-yellow.svg)

</div>

---

## 🖼️ Screenshots

<div align="center">
<table>
<tr>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/installed%20languages.png" alt="Installed Languages" style="max-width:100%;">
<p align="center"><em>Paquetes instalados</em></p>
</td>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/aviable%20languages.png" alt="Available Packages" style="max-width:100%;">
<p align="center"><em>Paquetes disponibles</em></p>
</td>
<td width="33%">
<img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/ai%20providers.png" alt="AI Settings" style="max-width:100%;">
<p align="center"><em>Ajustes de IA</em></p>
</td>
</tr>
</table>
</div>

---

## 🎥 AI Demonstration

<div align="center">
  <img src="https://raw.githubusercontent.com/disruptorh/Fast-Translator/main/img/AI_Demonstration.gif" width="33%" alt="AI Demonstration">
</div>

---

## ✨ Features

- 🌐 **Traducción offline** — motor [CTranslate2](https://github.com/OpenNMT/CTranslate2) con los modelos de [Argos Translate](https://github.com/argosopentech/argos-translate) empaquetados en local. **49 idiomas / 98 pares** disponibles en `index.json`.
- 🤖 **IA local con Ollama** — explanations contextuales con modelos servidos por [Ollama](https://ollama.com) en `localhost`. Sin API keys, sin nube.
- 🎭 **Roles de IA** — define un system prompt por rol y asígnale su propio atajo (`RoleManager`, config persistente).
- ⌨️ **Atajo global** — selecciona texto en cualquier aplicación, pulsa el atajo y el resultado **sustituye la selección**.
- 🔗 **Traducción en cadena** — `language_graph.cpp` calcula la ruta más corta entre dos idiomas que no tienen modelo directo, pasando por un idioma intermedio.
- 📦 **Gestor gráfico** — descarga, instala y borra paquetes de modelo; configura pares, atajos y modelos de IA.
- ⚡ **CUDA opcional** — CTranslate2 se compila con GPU si hay `nvcc` + cuDNN; si no, CPU con oneDNN.

### Dependencias de los dos binarios

| Binario | Qué hace | Herramientas |
|---|---|---|
| `fast-translator` | Traduce o consulta a la IA el texto del portapapeles y lo escribe de vuelta | `xclip`, `xdotool`, `libnotify` |
| `fast-translator-manager` | GUI wxWidgets: paquetes, atajos, modelos, roles | wxWidgets 3.2 |

---

## 📥 Installation (Recommended)

The easiest way to install is using the `.deb` package for Debian/Ubuntu based systems.

1. **Download** the latest `.deb` file from the [**Releases Page**](https://github.com/disruptorh/Fast-Translator/releases/latest).
2. **Install** via terminal:
   ```bash
   sudo apt install ./fast-translator_*.deb
   ```
   *(Or simply double-click the file to install with your software center)*

El paquete instala los modelos en `/usr/share/fast-translator/packages/`.

---

## 🚀 Usage Guide

Once installed, the application is designed to be invisible and ready at your fingertips.

### 1️⃣ Configura el atajo (primera vez)

```bash
fast-translator-manager
```
1. Ve a la pestaña **Settings** / **Shortcut**.
2. Elige primero un par de idiomas (el comando depende del par).
3. Pulsa **"Set Shortcut"**: el gestor **copia el comando al portapapeles** y abre los ajustes de teclado de tu escritorio (detecta **KDE**, **GNOME** o **XFCE**; en el resto te muestra las instrucciones a mano).
4. Pega el comando en un atajo nuevo, p. ej. `Ctrl+Alt+T`.

Hay tres atajos independientes: atajo de traducción, atajo de IA y atajo de IA por rol.

### 2️⃣ Traducir y preguntar a la IA

1. **Selecciona cualquier texto** en tu pantalla (navegador, documento, editor).
2. **Pulsa tu atajo**.
3. El texto seleccionado se **sustituye** por el resultado: la traducción, o la respuesta de la IA si hay un modelo de Ollama activo.

### 3️⃣ Configurar la IA (Ollama)

1. Instala y arranca [Ollama](https://ollama.com) (`ollama serve`).
2. En el gestor, pulsa **Refresh**: lista los modelos disponibles en `localhost:11434`.
3. Elige modelo y prompt de rol; pulsa **Set Shortcut** para el atajo.

Si Ollama no responde, la herramienta devuelve
`Error: Failed to connect to Ollama. Is it running? (ollama serve)`.

### Modo test (sin portapapeles ni X11)

Útil por SSH o para depurar:

```bash
fast-translator --test "hola mundo" es:en
echo "hola mundo" | fast-translator --test es:en
fast-translator --ollama <modelo>
```

---

## 🛠️ Building from Source

### Dependencias

El instalador automático hace todo (paquetes de sistema, SentencePiece y
CTranslate2 desde fuente, detectando CUDA):

```bash
sudo ./scripts/install_dependencies.sh
```

En Debian/Ubuntu instala `build-essential cmake git curl wget pkg-config
libcurl4-openssl-dev libwxgtk3.2-dev xclip xdotool libnotify-bin libdnnl-dev
gcc-12 g++-12`. Compila **CTranslate2 con `-DBUILD_CLI=OFF -DWITH_MKL=OFF
-DWITH_DNNL=ON`** y parchea su `CMakeLists.txt` con
`-allow-unsupported-compiler` para GCC 12 + CUDA 12. Si no hay `nvcc`, CPU.

### Build nativo

```bash
git clone https://github.com/disruptorh/Fast-Translator.git
cd Fast-Translator
./build_native.sh          # o: ./build_native.sh clean
```

También a mano:

```bash
cmake -B build -S . -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release --parallel
```

### Build del `.deb`

```bash
./build_deb.sh    # fast-translator v1.0.2, amd64 → dist/
```

`build_deb.sh` llama primero a `install_dependencies.sh` y empaqueta un glibc
"bundled" para portabilidad.

---

## 🗂️ Estructura

```
src/
  main.cpp              # CLI: portapapeles → traducción/IA → sustitución
  gui_main.cpp          # wxWidgets: gestor de paquetes, atajos, roles, modelos
  translation.{h,cpp}   # wrapper de CTranslate2 (load_model, translate)
  language_graph.{h,cpp}# grafo de idiomas + BFS para traducción en cadena
  ollama.{h,cpp}        # cliente HTTP de la API /api/generate
  role_manager.{h,cpp}  # roles de IA (system prompts) persistidos
  response_processor.*  # limpieza de la respuesta del modelo
  tokenizer.h           # interfaz Tokenizer
  tokenizer_sp.h        # SentencePiece
  tokenizer_bpe.{h,cpp} # BPE legacy
  utils.{h,cpp}         # portapapeles, notificaciones, DE, rutas
  json.hpp              # nlohmann/json (header-only)
index.json              # catálogo de paquetes Argos Translate
scripts/install_dependencies.sh
```

La lógica de atajos está duplicada a propósito en `gui_main.cpp`
(`OnSetShortcut`, `OnAISetShortcut`, `OnAIRoleSetShortcut`): comparten la misma
lógica de detección de escritorio, pero cada una prefija su comando.

---

## 📝 Notas

- **No hay tests automatizados.**
- La comunicación con Ollama es HTTP plano contra `OLLAMA_BASE_URL`
  (localhost), sin TLS: solo es aceptable porque el servidor es local.
- Los binarios se compilan como `Fast_translator` / `Fast_translator_manager`,
  pero se **instalan** como `fast-translator` y `fast-translator-manager`. Es lo
  que Expecta el atajo global, así que usa el nombre con guion.

---

## License

MIT — ver [LICENSE](LICENSE).

---

<div align="center">
Made with ❤️ for the open-source community
</div>
