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

# Implementación de Lógica (Dia 3)

Resumen
En esta fase, el sistema evoluciona de una ejecución secuencial a una arquitectura de grafos con ramificación lógica. Se ha integrado un componente de evaluación (Router) que analiza la carga semántica de la entrada para determinar la ruta de procesamiento más eficiente, optimizando la latencia y el uso de recursos computacionales.


VI. Implementación Técnica

````
from typing import TypedDict, Literal
from langgraph.graph import StateGraph, END

class AgentState(TypedDict):
    input_data: str
    routing_decision: str
    output: str

def brain_node(state: AgentState):
    """Analiza la complejidad de la tarea y decide la ruta."""
    print(f"\n[SISTEMA]: Evaluando complejidad de la tarea...")
    
    if len(state["input_data"]) > 35:
        return {"routing_decision": "deep_process"}
    return {"routing_decision": "standard_process"}

def deep_processing_node(state: AgentState):
    """Nodo para tareas que requieren mayor capacidad de cómputo."""
    print("[NODO_DEEP]: Ejecutando procesamiento de alta intensidad...")
    return {"output": "Resultado generado mediante análisis profundo."}

def standard_processing_node(state: AgentState):
    """Nodo para respuestas de baja latencia."""
    print("[NODO_STD]: Ejecutando procesamiento estándar...")
    return {"output": "Resultado generado mediante acceso directo."}


def build_logic_graph():
    workflow = StateGraph(AgentState)
    
  from typing import TypedDict, Literal
from langgraph.graph import StateGraph, END

class AgentState(TypedDict):
    input_data: str
    routing_decision: str
    output: str

def brain_node(state: AgentState):
    """Analiza la complejidad de la tarea y decide la ruta."""
    print(f"\n[SISTEMA]: Evaluando complejidad de la tarea...")
    
    if len(state["input_data"]) > 35:
        return {"routing_decision": "deep_process"}
    return {"routing_decision": "standard_process"}

def deep_processing_node(state: AgentState):
    """Nodo para tareas que requieren mayor capacidad de cómputo."""
    print("[NODO_DEEP]: Ejecutando procesamiento de alta intensidad...")
    return {"output": "Resultado generado mediante análisis profundo."}

def standard_processing_node(state: AgentState):
    """Nodo para respuestas de baja latencia."""
    print("[NODO_STD]: Ejecutando procesamiento estándar...")
    return {"output": "Resultado generado mediante acceso directo."}

def build_logic_graph():
    workflow = StateGraph(AgentState)

    workflow.add_node("brain", brain_node)
    workflow.add_node("deep_worker", deep_processing_node)
    workflow.add_node("std_worker", standard_processing_node)
    
    workflow.set_entry_point("brain")
    
    workflow.add_conditional_edges(
        "brain",
        lambda state: state["routing_decision"],
        {
            "deep_process": "deep_worker",
            "standard_process": "std_worker"
        }
    )
    
    workflow.add_edge("deep_worker", END)
    workflow.add_edge("std_worker", END)
    
    return workflow.compile()

if __name__ == "__main__":
    agent_system = build_logic_graph()
    
    input_test = {"input_data": "Requerimiento complejo de análisis de datos para sistema autónomo"}
    print("--- INICIANDO SISTEMA LÓGICO ---")
    agent_system.invoke(input_test)
 ````

# Testing Determinista (Dia 4)

### Stack 
- Observabilidad: LangSmith para el trazado (tracing) distribuido de nodos.
- Orquestación: LangGraph para la gestión de estados inmutables.
- Entorno: Variables de entorno ($env:LANGCHAIN_PROJECT) para segmentación de telemetría.

### eval_benchmark.py
````
import time
from sales_agent_v3 import build_sales_workflow

benchmark_data = [
    ("Hola", "standard_close"),
    ("Quiero comprar ya", "standard_close"),
    ("Necesito una auditoria tecnica completa para OTI Systems en Lima", "market_research"),
    ("IA", "standard_close"),
    ("Podrian enviarme una propuesta detallada para implementar vision computacional en KRAKK", "market_research"),
    ("Precio de los drones", "standard_close"),
    ("Busco automatizar el flujo de ventas de una empresa con 50 empleados", "market_research"),
    ("Informacion general", "standard_close"),
    ("Analisis de mercado para sistemas de defensa en el sector seguridad", "market_research"),
    ("Gracias por la ayuda", "standard_close")
]

def run_evaluation():
    agent = build_sales_workflow()
    stats = {"exito": 0, "fallo": 0}
    
    print("INICIANDO BENCHMARK EN LANGSMITH: Dia-4-Agente IA")
    
    for i, (query, expected) in enumerate(benchmark_data, 1):
        try:
            print(f"Prueba {i}/10: Procesando {query[:30]}")
            
            start_time = time.time()
            result = agent.invoke({"lead_query": query})
            duration = time.time() - start_time
            
            actual = result.get("action_type")
            
            if actual == expected:
                stats["exito"] += 1
                status = "PASS"
            else:
                stats["fallo"] += 1
                status = f"FAIL (Esperaba {expected}, obtuvo {actual})"
            
            print(f"Resultado: {status} | Latencia: {duration:.2f}s")
            
        except Exception as e:
            stats["fallo"] += 1
            print(f"ERROR en Prueba {i}: {str(e)}")

    print("EVALUACION FINALIZADA")
    print(f"Tasa de Exito: {(stats['exito']/10)*100}%")
    print(f"Exitos: {stats['exito']} | Fallos: {stats['fallo']}")

if __name__ == "__main__":
    run_evaluation()

````
### EJECUTA
````
python eval_benchmark.py
````

## Métricas de Éxito

| Indicador             | Valor       |
|:----------------------|:------------|
| Accuracy (Precisión)  | 100%        |
| Latencia Media        | 0.04 s      |
| Tasa de Error         | 0%          |
| Integridad de Estado  | Verificada  |

## Trazabilidad y Logs
Cada interacción en el grafo fue registrada en LangSmith bajo el proyecto "Dia-4-Agente IA". Esto nos permite auditar:

- Flow Graph: Visualización de la ruta exacta que tomó el lead.
- Time-per-Node: Identificación de cuellos de botella en la lógica de calificación.
- Payload Inspection: Análisis de los diccionarios de estado (SalesState) transferidos entre nodos.


