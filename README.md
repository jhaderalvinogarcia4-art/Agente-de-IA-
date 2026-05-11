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

#### Instalación de dependencias 

```Powershell 
uv add pydantic openai
```


# Arquitectura de Grafos de Estado (Dia 2)

I. Protocolo de Instalación

Instalación de uv
```
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

II. Inicialización y Aislamiento

```Powershell: Inicialización
uv init
```

```Powershell: Creación de Entorno Virtual
uv venv
```


III. Inyección de Dependencias y Bloqueo de Versiones

```Powershell: Instalación de dependencias core
uv add pydantic openai langgraph langchain-openai
```

```Powershell: Bloqueo de versiones
uv lock
```

IV. Anatomía del Proyecto

| Archivo         | Nivel Técnico | Función Crítica |
|----------------|--------------|-----------------|
| agente_grafo.py | Core         | Implementación de la lógica de grafos y orquestación de nodos. |
| pyproject.toml  | Config       | Manifiesto de la startup. Define el estándar de ejecución. |
| uv.lock         | Security     | Garantiza que cada bit del entorno sea idéntico en cualquier máquina. |
| .gitignore      | DevOps       | Protege el repositorio de archivos binarios y secretos de entorno. |


V. Código Fuente Principal (agente_grafo.py)
```
import os
from typing import TypedDict, Literal
from langgraph.graph import StateGraph, END

// Definición del Estado
class EstadoAgente(TypedDict):
    pregunta: str
    decision: str
    respuesta: str

// Nodos del Sistema
def nodo_cerebro(state: EstadoAgente):
    print("\n[CEREBRO]: Evaluando necesidad de búsqueda...")
    if len(state["pregunta"]) > 20:
        return {"decision": "buscador"}
    return {"decision": "finalizador"}

def nodo_buscador(state: EstadoAgente):
    print("[BUSCADOR]: Ejecutando herramientas externas...")
    return {"respuesta": "Dato fresco obtenido (Simulado)."}

def nodo_finalizador(state: EstadoAgente):
    print("[FINALIZADOR]: Usando conocimiento interno...")
    return {"respuesta": "Respuesta basada en memoria local."}

// Orquestación del Grafo
workflow = StateGraph(EstadoAgente)
workflow.add_node("cerebro", nodo_cerebro)
workflow.add_node("buscador", nodo_buscador)
workflow.add_node("finalizador", nodo_finalizador)

workflow.set_entry_point("cerebro")

def router(state: EstadoAgente) -> Literal["buscador", "finalizador"]:
    return state["decision"]

workflow.add_conditional_edges("cerebro", router)
workflow.add_edge("buscador", END)
workflow.add_edge("finalizador", END)

app = workflow.compile()

if __name__ == "__main__":
    app.invoke({"pregunta": "Necesito saber el precio del Bitcoin ahora mismo"})
```

