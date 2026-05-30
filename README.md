# 🌸 PQRS Uniminuto - Arquitectura Agéntica para Floristería Madrid Cundinamarca
## 👥 Equipo

- **Edgardo Samuel Barraza Verdesoto** - [edgardo.barraza.v@uniminuto.edu.co](mailto:edgardo.barraza.v@uniminuto.edu.co)
- **Ericka Alexandra Jimenez Rodriguez** - [erjimenez@uniminuto.edu](mailto:erjimenez@uniminuto.edu)
- **José Danilo Sánchez Torres** - [jose.sanchez.t@uniminuto.edu](mailto:jose.sanchez.t@uniminuto.edu)
- **Daniela Alejandra Segura Lazcarro** - [daniela.segura-l@uniminuto.edu.co](mailto:daniela.segura-l@uniminuto.edu.co)
## 🎯 Propósito

Sistema inteligente para análisis y gestión de **PQRS** (Peticiones, Quejas, Reclamos y Sugerencias) del sector floricultor en Madrid, Cundinamarca.

### Problema que resuelve
- 📊 **Volumen**: Múltiples fuentes (Excel, APIs B2B, Correos)
- 🧠 **Complejidad**: Texto no estructurado + datos numéricos
- ⚡ **Velocidad**: Respuesta rápida vs análisis profundo
- 💰 **Costo**: Modelos cloud vs inferencia local

## 🏗️ Arquitectura

COMPONENTES PRINCIPALES (4 repositorios):

  ┌─────────────────────────────────────────────────────────────────┐
  │  COMPONENTE 1: INGESTA + VALIDACIÓN                             │
  │  📦 Repo: floristeria/pqrs-ingestion                            │
  │                                                                  │
  │  Contiene:                                                       │
  │  • Dagster pipelines (jobs/schedules/sensors)                   │
  │  • Conectores a fuentes (Excel, APIs, Email)                    │
  │  • Validadores Pydantic (reglas de negocio)                     │
  │                                                                  │
  │  Tecnología: Python + Dagster + Pydantic                        │
  │  Escalamiento: Batch / Event-driven                             │
  └─────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────┐
  │  COMPONENTE 2: ALMACENAMIENTO                                   │
  │  📦 Repo: floristeria/pqrs-storage                              │
  │                                                                  │
  │  Contiene:                                                       │
  │  • Esquemas DWH (PostgreSQL/BigQuery)                           │
  │  • Configuración Vector DB (RedisVL/Qdrant)                     │
  │  • Migraciones (Alembic)                                        │
  │  • Clientes de acceso unificados                                │
  │                                                                  │
  │  Tecnología: SQL + Redis + Python clients                       │
  │  Escalamiento: Persistencia gestionada                         │
  └─────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────┐
  │  COMPONENTE 3: NÚCLEO AGÉNTICO                                  │
  │  📦 Repo: floristeria/pqrs-agentic                              │
  │                                                                  │
  │  Contiene:                                                       │
  │  • FastAPI app (endpoints REST)                                 │
  │  • Triaje inteligente (selector ML/rules)                       │
  │  • Modelos ML tradicionales (XGBoost, RF)                       │
  │  • CrewAI agents (extractor, clasificador, consolidador)        │
  │  • Agent Factory (instanciación dinámica)                       │
  │                                                                  │
  │  Tecnología: FastAPI + CrewAI + scikit-learn + Ollama client   │
  │  Escalamiento: Stateless, múltiples réplicas CPU                │
  └─────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────┐
  │  COMPONENTE 4: EVOLUCIÓN + LLM                                  │
  │  📦 Repo: floristeria/pqrs-llm                                  │
  │                                                                  │
  │  Contiene:                                                       │
  │  • Ollama server config (docker-compose)                        │
  │  • Fine-tuning pipelines (transformers + LoRA)                  │
  │  • Exportación de pesos                                          │
  │  • Model registry (versiones)                                   │
  │                                                                  │
  │  Tecnología: Ollama + Python (transformers, peft) + Docker      │
  │  Escalamiento: GPU preferible, procesos offline                 │
  └─────────────────────────────────────────────────────────────────┘

COMPONENTE TRANSVERSAL (1 librería compartida):

  ┌─────────────────────────────────────────────────────────────────┐
  │  LIBRERÍA COMPARTIDA                                            │
  │  📦 Repo: floristeria/pqrs-core                                 │
  │                                                                  │
  │  Contiene:                                                       │
  │  • Contratos/eventos (Pydantic models)                          │
  │  • Interfaces ABC (ports)                                       │
  │  • Utilidades comunes                                            │
  │                                                                  │
  │  Uso: pip install floristeria-pqrs-core en los 4 componentes    │
  └─────────────────────────────────────────────────────────────────┘


### Estructura general de carpetas

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



### Conexion

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
