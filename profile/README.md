# 🌸 PQRS Uniminuto - Arquitectura Agéntica para Floristería Madrid Cundinamarca
## 👥 Equipo


| Nombre Completo | Correo Electrónico |
| --- | --- |
| **Edgardo Samuel Barraza Verdesoto** | [edgardo.barraza.v@uniminuto.edu.co](mailto:edgardo.barraza.v@uniminuto.edu.co) |
| **Ericka Alexandra Jimenez Rodriguez** | [erjimenez@uniminuto.edu](mailto:erjimenez@uniminuto.edu) |
| **José Danilo Sánchez Torres** | [jose.sanchez.t@uniminuto.edu](mailto:jose.sanchez.t@uniminuto.edu) |
| **Daniela Alejandra Segura Lazcarro** | [daniela.segura-l@uniminuto.edu.co](mailto:daniela.segura-l@uniminuto.edu.co) |

## 🎯 Propósito

Sistema inteligente para análisis y gestión de **PQRS** (Peticiones, Quejas, Reclamos y Sugerencias) del sector floricultor en Madrid, Cundinamarca.

### Problema que resuelve
- 📊 **Volumen**: Múltiples fuentes (Excel, APIs B2B, Correos)
- 🧠 **Complejidad**: Texto no estructurado + datos numéricos
- ⚡ **Velocidad**: Respuesta rápida vs análisis profundo
- 💰 **Costo**: Modelos cloud vs inferencia local

## 🏗️ Arquitectura

### Arquitectura de Componentes (PQRS Floristería)

| Componente | Repositorio | Contenido Principal | Tecnología | Escalamiento / Uso |
| :--- | :--- | :--- | :--- | :--- |
| **1: Ingesta + Validación** | `floristeria/pqrs-ingestion` | <ul><li>Dagster pipelines (jobs/schedules/sensors)</li><li>Conectores a fuentes (Excel, APIs, Email)</li><li>Validadores Pydantic (reglas de negocio)</li></ul> | Python + Dagster + Pydantic | Batch / Event-driven |
| **2: Almacenamiento** | `floristeria/pqrs-storage` | <ul><li>Esquemas DWH (PostgreSQL/BigQuery)</li><li>Configuración Vector DB (RedisVL/Qdrant)</li><li>Migraciones (Alembic)</li><li>Clientes de acceso unificados</li></ul> | SQL + Redis + Python clients | Persistencia gestionada |
| **3: Núcleo Agéntico** | `floristeria/pqrs-agentic` | <ul><li>FastAPI app (endpoints REST)</li><li>Triaje inteligente (selector ML/rules)</li><li>Modelos ML tradicionales (XGBoost, RF)</li><li>CrewAI agents (extractor, clasificador, consolidador)</li><li>Agent Factory (instanciación dinámica)</li></ul> | FastAPI + CrewAI + scikit-learn + Ollama client | Stateless, múltiples réplicas CPU |
| **4: Evolución + LLM** | `floristeria/pqrs-llm` | <ul><li>Ollama server config (docker-compose)</li><li>Fine-tuning pipelines (transformers + LoRA)</li><li>Exportación de pesos</li><li>Model registry (versiones)</li></ul> | Ollama + Python (transformers, peft) + Docker | GPU preferible, procesos offline |

---

### Componente Transversal

| Librería Compartida | Repositorio | Contenido Principal | Modo de Uso |
| :--- | :--- | :--- | :--- |
| **pqrs-core** | `floristeria/pqrs-core` | <ul><li>Contratos/eventos (Pydantic models)</li><li>Interfaces ABC (ports)</li><li>Utilidades comunes</li></ul> | `pip install floristeria-pqrs-core` <br>*(Instalada en los 4 componentes principales)* |

### Estructura general de carpetas
```

github.com/floristeria-madrid/
│
├── pqrs-core/                      # Librería base (dependencia de todos)
│   ├── floristeria_pqrs/
│   │   ├── contracts/              # Eventos canónicos
│   │   │   ├── ingestion.py
│   │   │   ├── validation.py
│   │   │   └── results.py
│   │   ├── ports/                  # Interfaces ABC
│   │   │   ├── data_source.py
│   │   │   ├── agent_strategy.py
│   │   │   └── storage_backend.py
│   │   └── utils/
│   └── pyproject.toml
│
├── pqrs-ingestion/                 # COMPONENTE 1
│   ├── dagster/
│   │   ├── jobs/
│   │   │   ├── ingest_excel.py
│   │   │   ├── ingest_apis.py
│   │   │   └── ingest_emails.py
│   │   ├── schedules.py
│   │   └── sensors.py
│   ├── connectors/
│   │   ├── excel_reader.py
│   │   ├── api_b2b_client.py
│   │   └── email_imap.py
│   ├── validators/
│   │   ├── schemas.py
│   │   └── business_rules.py
│   ├── docker-compose.yml
│   └── requirements.txt
│
├── pqrs-storage/                   # COMPONENTE 2
│   ├── dwh/
│   │   ├── migrations/             # Alembic
│   │   ├── models/
│   │   └── queries/
│   ├── vector/
│   │   ├── redis_config.py
│   │   └── embeddings.py
│   ├── clients/
│   │   ├── dwh_client.py
│   │   └── vector_client.py
│   └── docker-compose.yml
│
├── pqrs-agentic/                   # COMPONENTE 3
│   ├── api/
│   │   ├── app.py
│   │   ├── routes/
│   │   │   ├── process.py
│   │   │   └── health.py
│   │   └── dependencies.py
│   ├── triage/
│   │   ├── selector.py
│   │   ├── value_metrics.py
│   │   └── features.py
│   ├── ml_models/
│   │   ├── models/
│   │   │   ├── cost_regression.pkl
│   │   │   └── churn_classifier.pkl
│   │   └── predictors/
│   ├── crew_agents/
│   │   ├── extractor.py
│   │   ├── classifier.py
│   │   ├── consolidator.py
│   │   └── factory.py
│   ├── Dockerfile
│   └── requirements.txt
│
├── pqrs-llm/                       # COMPONENTE 4
│   ├── ollama/
│   │   ├── docker-compose.yml
│   │   └── modelfiles/
│   ├── training/
│   │   ├── fine_tune.py
│   │   ├── export_weights.py
│   │   └── data_prep.py
│   ├── registry/
│   │   └── versions.json
│   └── scripts/
│       └── deploy_model.sh
│
└── README.md                       # Documentación general
    ├── arquitectura/
    ├── despliegue/
    └── desarrolladores
```


### Conexion

```
Flujo de datos:

  pqrs-ingestion ──(Kafka/Redis)──► pqrs-agentic
       │                                  │
       │ (escribe)                         │ (consulta)
       ▼                                  ▼
  pqrs-storage ◄──────────────────────────┘
       │
       │ (lectura para training)
       ▼
  pqrs-llm

Dependencias:

  pqrs-ingestion ──► pqrs-core (librería)
  pqrs-storage   ──► pqrs-core (librería)
  pqrs-agentic   ──► pqrs-core (librería)
  pqrs-llm       ──► pqrs-core (librería)

  pqrs-agentic ──► pqrs-storage (cliente DB)
  pqrs-agentic ──► pqrs-llm (cliente Ollama)
```

### Repositorios

### Arquitectura de Componentes y Ubicación (PQRS Floristería)

| Componente | Repositorio (`📦`) | Ubicación Local Sugerida | Contenido Principal | Tecnología | Escalamiento / Uso |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1: Ingesta + Validación** | `floristeria/pqrs-ingestion` | `https://github.com/pqrs-uniminuto/pqrs-ingestion` | <ul><li>Dagster pipelines (jobs/schedules/sensors)</li><li>Conectores a fuentes (Excel, APIs, Email)</li><li>Validadores Pydantic (reglas de negocio)</li></ul> | Python + Dagster + Pydantic | Batch / Event-driven |
| **2: Almacenamiento** | `floristeria/pqrs-storage` | `https://github.com/pqrs-uniminuto/pqrs-storage` | <ul><li>Esquemas DWH (PostgreSQL/BigQuery)</li><li>Configuración Vector DB (RedisVL/Qdrant)</li><li>Migraciones (Alembic)</li><li>Clientes de acceso unificados</li></ul> | SQL + Redis + Python clients | Persistencia gestionada |
| **3: Núcleo Agéntico** | `floristeria/pqrs-agentic` | `https://github.com/pqrs-uniminuto/pqrs-agentic` | <ul><li>FastAPI app (endpoints REST)</li><li>Triaje inteligente (selector ML/rules)</li><li>Modelos ML tradicionales (XGBoost, RF)</li><li>CrewAI agents (extractor, clasificador, consolidador)</li><li>Agent Factory (instanciación dinámica)</li></ul> | FastAPI + CrewAI + scikit-learn + Ollama client | Stateless, múltiples réplicas CPU |
| **4: Evolución + LLM** | `floristeria/pqrs-llm` | `https://github.com/pqrs-uniminuto/pqrs-llm` | <ul><li>Ollama server config (docker-compose)</li><li>Fine-tuning pipelines (transformers + LoRA)</li><li>Exportación de pesos</li><li>Model registry (versiones)</li></ul> | Ollama + Python (transformers, peft) + Docker | GPU preferible, procesos offline |

### Componente Transversal

| Librería Compartida | Repositorio (`📦`) | Ubicación Local Sugerida | Contenido Principal | Modo de Uso |
| :--- | :--- | :--- | :--- | :--- |
| **pqrs-core** | `floristeria/pqrs-core` | `https://github.com/pqrs-uniminuto/pqrs-core` | <ul><li>Contratos/eventos (Pydantic models)</li><li>Interfaces ABC (ports)</li><li>Utilidades comunes</li></ul> | `pip install floristeria-pqrs-core` <br>*(Instalada en los 4 componentes principales)* |

### Diagrama

```mermaid
flowchart TD
    title["<b>Arquitectura Agéntica Evolutiva e Híbrida para Análisis de PQRS (Floristeria Madrid Cundinamarca)</b>"]

    %% ============================================
    %% COMPONENTE 1: INGESTA + VALIDACIÓN
    %% REPOSITORIO: pqrs-ingestion
    %% ============================================
    subgraph Ingestion ["📦 REPO: pqrs-ingestion / Ingesta y Calidad de Datos"]
        direction LR
        F1[Excel / CSV<br/>Reclamos de Clientes]
        F2[APIs / Portales<br/>B2B Comercializadoras]
        F3[Correos / Adjuntos<br/>PQRS Nacional/Exportación]
        
        Dagster[[Dagster Pipeline<br/>Orquestador de Datos]]
        Val{{"Validador Pydantic<br/>¿Tiene Variedad/Finca/Lote?"}}
        
        F1 & F2 & F3 --> Dagster --> Val
    end

    %% ============================================
    %% COMPONENTE 2: ALMACENAMIENTO
    %% REPOSITORIO: pqrs-storage
    %% ============================================
    subgraph Storage ["📦 REPO: pqrs-storage / Persistencia e Históricos"]
        DWH[(Data Warehouse<br/>PostgreSQL / BigQuery)]
        VDB[(Vector DB<br/>RedisVL / Qdrant)]
    end
    
    Val -->|Datos Estructurados| DWH
    Val -.->|Embeddings de Texto| VDB

    %% ============================================
    %% COMPONENTE 3: NÚCLEO AGÉNTICO
    %% REPOSITORIO: pqrs-agentic
    %% ============================================
    subgraph Hexagon ["📦 REPO: pqrs-agentic / Núcleo de Aplicación Hexagonal"]
        direction TB
        PortIn[Puerto: Event Trigger]
        
        subgraph CoreLogic [Dominio Puro / Reglas de Negocio]
            ML[Select ML / Triaje Inteligente<br/>¿Numérico/Logístico o Cualitativo?]
            GDA[Dynamic Agent Factory<br/>Instanciador de Crew]
        end
        
        PortOut[Puerto: Agent Framework]
        
        PortIn --> CoreLogic --> PortOut
    end

    %% ============================================
    %% COMPONENTE 3 (continuación): ML + CREWAI
    %% Todo dentro de pqrs-agentic
    %% ============================================
    subgraph Seleccion ["Entorno Operativo (dentro de pqrs-agentic)"]
        ML -->|Opción Predictiva: Datos DWH| PathML[Modelos ML Tradicionales<br/>Regresión Costos / Scoring Churn]
        
        PortOut --> Crew[Adaptador CrewAI]
        Crew -->|Opción Cognitiva: Texto| CrewNetwork[Crew Especializado en Flores]
        
        subgraph CrewNetwork [Crew de Agentes Dinámicos]
            direction TB
            A11((Agente Extractor:<br/>Saca Lote, Variedad y Causa))
            A12((Agente Clasificador:<br/>Severidad y Riesgo Financiero))
            A21((Agente Consolidador:<br/>Genera Plan de Acción / Respuesta))
            
            A11 & A12 --> A21
        end
    end

    %% ============================================
    %% COMPONENTE 4: EVOLUCIÓN + LLM
    %% REPOSITORIO: pqrs-llm
    %% ============================================
    subgraph EvolutionaryPipeline ["📦 REPO: pqrs-llm / Evolución Cognitiva e Inferencia Local"]
        direction TB
        Train[Fine-Tuning Pipeline<br/>Entrenamiento con Histórico de PQRS]
        Weights[Exportación de Pesos<br/>Modelo Experto en Floricultura]
        OllamaServer[Servidor Ollama Local<br/>Llama3 / Mistral]
        
        DWH -->|Dataset de Reclamos| Train
        Train --> Weights -->|Despliegue On-Premises| OllamaServer
    end

    %% CONEXIONES Y RETROALIMENTACIÓN
    Val -.->|Notificación Kafka/Redis| PortIn
    VDB <-->|RAG: Precedentes del cliente/lote| PortIn
    PathML & A21 --> Outputs[(Resultados a DWH / Dashboards)]
    
    %% INFERENCIA LOCAL
    CrewNetwork <-->|Inferencia Local sin costo| OllamaServer

    %% ============================================
    %% LEYENDA DE REPOSITORIOS
    %% ============================================
    subgraph Legend [📁 Repositorios GitHub - pqrs-uniminuto]
        direction LR
        R1["✅ pqrs-ingestion"]
        R2["✅ pqrs-storage"]
        R3["✅ pqrs-agentic"]
        R4["✅ pqrs-llm"]
        R5["📦 pqrs-core (librería compartida)"]
    end

    %% ============================================
    %% ESTILOS
    %% ============================================
    style title fill:none,stroke:none,font-size:18px,font-weight:bold
    
    style Ingestion fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    style Storage fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    style Hexagon fill:#fff3e0,stroke:#e65100,stroke-width:2px
    style Seleccion fill:#fff3e0,stroke:#e65100,stroke-width:1px
    style EvolutionaryPipeline fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px
    style Legend fill:#f5f5f5,stroke:#9e9e9e,stroke-dasharray:5 5
    
    style Dagster fill:#009485,color:#fff
    style Val fill:#43a047,color:#fff
    style OllamaServer fill:#212121,color:#fff
    style Crew fill:#ff9900,color:#000
    style A11 fill:#3776ab,color:#fff
    style A12 fill:#3776ab,color:#fff
    style A21 fill:#e76f00,color:#fff
```