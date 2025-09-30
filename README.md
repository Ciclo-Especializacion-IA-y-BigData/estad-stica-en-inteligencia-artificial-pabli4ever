[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/rmTO4R92)
# Manual de instalación del entorno de trabajo para IA

*(Windows y Ubuntu)*

## 1. Introducción

Este manual explica cómo instalar y configurar un entorno de desarrollo de Inteligencia Artificial utilizando **Docker**, **Visual Studio Code** y un contenedor preconfigurado con las librerías necesarias.

---

## 2. Instalación de Docker

### 2.1. En Windows

1. Descargar **Docker Desktop**:
   🔗 [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
2. Ejecutar el instalador y marcar:

   * **Use the WSL 2 based engine** (recomendado).
3. Abrir Docker Desktop y verificar que está en ejecución.
4. Comprobar instalación en **PowerShell** o **CMD**:

   ```powershell
   docker --version
   docker compose version
   ```

⚠ **Requisitos previos**:

* Tener habilitado **WSL 2** y Ubuntu como distribución predeterminada:
  [https://learn.microsoft.com/es-es/windows/wsl/install](https://learn.microsoft.com/es-es/windows/wsl/install)

---

### 2.2. En Ubuntu (instalación oficial según Docker Docs)

#### 1️⃣ Configurar el repositorio oficial de Docker

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

#### 2️⃣ Instalar Docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### 3️⃣ Comprobar instalación

```bash
sudo docker run hello-world
```

#### 4️⃣ Permitir uso de Docker sin `sudo`

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Comprobar:

```bash
docker ps
docker run --rm hello-world
```

💡 **Nota:** Si falla por permisos:

```bash
ls -l /var/run/docker.sock
sudo chown root:docker /var/run/docker.sock
sudo chmod 660 /var/run/docker.sock
sudo systemctl restart docker
```

---

## 3. Instalación de Visual Studio Code

1. Descargar: [https://code.visualstudio.com/](https://code.visualstudio.com/)
2. Instalar y abrir VS Code.
3. Instalar extensiones:

   * **Dev Containers**
   * **Python**
   * **Jupyter**

---

## 4. Descarga de la imagen Docker

```bash
docker pull carlostessier/ceiabd:pythonbase_latest
```

---

## 5. Creación del repositorio

```
📂 workspaceAI
 ├── 📂 .devcontainer
 │    ├── devcontainer.json
 │    └── Dockerfile
 ├── 📂 .vscode
 │    └── extensions.json
 ├── requirements.txt
 ├── check_env.py
 └── test_libraries.ipynb
```

**devcontainer.json**

```json
{
    "name": "AI Dev Container",
    "image": "carlostessier/ceiabd:pythonbase_latest",   
    "settings": {
      "python.defaultInterpreterPath": "/usr/local/bin/python"
    },
    "postCreateCommand": "pip install -r requirements.txt"
}
```

**Dockerfile**

```dockerfile
FROM python:3.11-bullseye
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*
RUN pip install --upgrade pip && \
    pip install numpy pandas matplotlib scikit-learn tensorflow keras \
    seaborn shap xgboost lightgbm bokeh scipy nltk ipykernel panel
```

---

## 6. requirements.txt

```txt
numpy
pandas
matplotlib
scikit-learn
tensorflow
keras
seaborn
shap
xgboost
lightgbm
bokeh
scipy
nltk
ipykernel
panel
```

---

## 7. check\_env.py

```python
import importlib
libraries = [
    "numpy", "pandas", "matplotlib", "sklearn", "tensorflow", "keras",
    "seaborn", "shap", "xgboost", "lightgbm", "bokeh", "scipy", "nltk", "ipykernel", "panel"
]
print("🔍 Verificando librerías instaladas...\\n")
for lib in libraries:
    try:
        importlib.import_module(lib)
        print(f"✅ {lib} OK")
    except ImportError:
        print(f"❌ {lib} NO INSTALADA")
print("\\n✅ Comprobación finalizada")
```

---

## 8. test\_libraries.ipynb

Incluye:

* Información de entorno y GPU.
* Importación de librerías.
* Demo Pandas + Matplotlib.
* Ejemplos de `prueba.ipynb`.

---

## 9. Configurar VS Code con el contenedor

1. Abrir carpeta del proyecto en VS Code.
2. `Ctrl+Shift+P` → **Dev Containers: Open Folder in Container**.
3. Verificar kernel (`/usr/local/bin/python`).
4. Ejecutar:

   ```bash
   python check_env.py
   ```

   Y abrir `test_libraries.ipynb`.

---

## 10. Solución de problemas (Linux)

1. Verificar grupos:

   ```bash
   id -nG
   ```
2. Usar `newgrp docker` o reiniciar sesión.
3. Revisar permisos del socket:

   ```bash
   ls -l /var/run/docker.sock
   ```

   Debe ser `root:docker` con `660`.

---

## 11. Extensiones recomendadas (.vscode/extensions.json)

```json
{
    "recommendations": [
        "ms-vscode-remote.remote-containers",
        "ms-toolsai.jupyter",
        "ms-toolsai.jupyter-cell-tags",
        "ms-toolsai.jupyter-keymap",
        "ms-toolsai.jupyter-renderers",
        "ms-toolsai.jupyter-slideshow",
        "ms-python.vscode-pylance",
        "ms-python.python",
        "ms-python.debugpy",
        "ms-python.python-environment-manager"
    ]
}
```


