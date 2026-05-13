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
````
from typing import TypedDict, Literal
from langgraph.graph import StateGraph, END

class SalesState(TypedDict):
    """Representa el estado del pipeline de ventas."""
    lead_query: str
    action_type: str
    final_offer: str

def qualification_node(state: SalesState):
    """Evalúa el perfil del lead y decide el flujo de trabajo."""
    print(f"\n[ANALISTA]: Calificando interés del lead: '{state['lead_query'][:30]}...'")
    
    if len(state["lead_query"]) > 30:
        return {"action_type": "market_research"}
    return {"action_type": "standard_close"}

def research_node(state: SalesState):
    """Simula la activación de herramientas de búsqueda para leads Premium."""
    print("[INVESTIGADOR]: Extrayendo datos competitivos y precios actualizados...")
    return {"final_offer": "Propuesta personalizada basada en datos de mercado actuales."}

def closing_node(state: SalesState):
    """Genera una respuesta rápida para leads de alta conversión."""
    print("[VENDEDOR]: Generando oferta directa con catálogo interno...")
    return {"final_offer": "Catálogo estándar enviado: Precios fijos y promociones activas."}

def build_sales_workflow():
    workflow = StateGraph(SalesState)

    workflow.add_node("qualifier", qualification_node)
    workflow.add_node("researcher", research_node)
    workflow.add_node("closer", closing_node)

    workflow.set_entry_point("qualifier")

    workflow.add_conditional_edges(
        "qualifier",
        lambda state: state["action_type"],
        {
            "market_research": "researcher",
            "standard_close": "closer"
        }
    )

    workflow.add_edge("researcher", END)
    workflow.add_edge("closer", END)

    return workflow.compile()

if __name__ == "__main__":
    sales_agent = build_sales_workflow()
    
    lead_input = {
        "lead_query": "Busco una solución corporativa de IA para 50 empleados en Lima"
    }
    
    print("--- INICIANDO PIPELINE DE VENTAS ---")
    sales_agent.invoke(lead_input)
  ````


