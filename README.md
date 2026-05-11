# Agente IA: Arquitectura y Desarrollo (Día 1)

El objetivo es transicionar de modelos de lenguaje pasivos a sistemas agentes autónomos. Este proyecto implementa una infraestructura determinista sobre modelos probabilísticos.


I. Stack Tecnológico 

| Herramienta  | Función Crítica           | Por qué es mejor que el estándar                                                                 |
|-------------|--------------------------|--------------------------------------------------------------------------------------------------|
| uv          | Orquestador de Entorno   | A diferencia de pip, está escrito en Rust y garantiza entornos reproducibles entre dev y prod.  |
| Pydantic    | Validación de Esquemas   | Actúa como contrato de datos, evitando que errores o alucinaciones corrompan el sistema.        |
| Python 3.12 | Motor de Ejecución       | Ofrece mejoras en rendimiento, gestión de memoria y tipado moderno.                             |



II. Protocolo de Instalación y Configuración del Entorno

Ejecutar en **PowerShell** desde la raíz del proyecto:

####  Instalación de uv
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

#### Inicialización del proyecto
```Powershell
uv init
```

#### Creación del entorno virtual
```Powershell
uv venv
```


III. Inyección de Dependencias y Bloqueo de Versiones

Instalación declarativa de librerías (genera automáticamente uv.lockpara asegurar reproducibilidad):

Instalación de dependencias core

Powershell 
uv add pydantic openai
