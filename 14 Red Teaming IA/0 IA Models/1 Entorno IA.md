# Creando entorno de IA

Tags: #IA 

## Instalación de entorno y miniconda

```powershell 
❯ Set-ExecutionPolicy RemoteSigned -scope CurrentUser # Allow scripts to run
❯ irm get.scoop.sh | iex 
❯ scoop bucket add extras
❯ scoop install miniconda3
❯ conda --version           # Cerrar y abrir de nuevo Powershell para ejecutar este comando 
```

```powershell 
❯ conda init
❯ conda config --add channels defaults
❯ conda config --add channels conda-forge
❯ conda config --add channels nvidia # only needed if you are on a PC that has a nvidia gpu
❯ conda config --add channels pytorch
❯ conda config --set channel_priority strict

(base)❯ conda config --set auto_activate_base false   # Desactivar (base)

(base)❯ conda create -n ai python=3.11         # Crear un entorno llamado 'ai'
(base)❯ conda activate ai                      # Activar el entorno llamado 'ai'
(base)❯ conda deactivate                       # Descativar el entorno 

# Dentro del entorno 
(ai)❯ conda install -y numpy scipy pandas scikit-learn matplotlib seaborn transformers datasets tokenizers accelerate evaluate optimum huggingface_hub nltk category_encoders
(ai)❯ conda install -y pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
(ai)❯ pip install requests requests_toolbelt

# Actualizaciones 
❯ conda update --all
❯ conda update -n base -c defaults conda
```