Surucucu — Assistente Pessoal Multi-Agente de IA

Visão Geral
-----------

Surucucu é uma plataforma full‑stack de assistente pessoal multi‑agente projetada para orquestrar capacidades de LLMs, pipelines de dados e integrações em produção. O objetivo é unir durabilidade de estado, auditabilidade e uma UX human‑in‑the‑loop (Telegram/voz) mantendo custos e latências dentro de limites operacionais.

Arquitetura
----------

Componentes principais:

- API Gateway (FastAPI): camada de entrada responsável por autenticação, validação (Pydantic) e enfileiramento inicial de comandos.
- LangGraph: orquestrador de workflow que modela runs como grafos com checkpoints duráveis e capacidade de replay.
- Agentes/Skills: microserviços independentes que expõem skills tipadas com contratos Pydantic/PydanticAI.
- Message Bus & Durable Store: PostgreSQL é usado como fila inicial com SELECT FOR UPDATE para locks; Redis para deduplicação e locks leves; para workloads maiores, Kafka é recomendado.
- Retrieval & RAG: PGVector / Qdrant para embeddings e retrieval; gating de evidências antes de confiança no output do agente.
- Observabilidade: OpenTelemetry para traces e métricas; Prometheus + Grafana para dashboards; logs estruturados com correlação por run_id.

Decisões e trade‑offs
--------------------

- Checkpoints duráveis (LangGraph) facilitam replay e debugging, reduzindo custo de rerun, mas aumentam latência e requerem armazenamento adicional.
- Postgres como fila é uma solução operacionalmente simples para PoCs e tráfego moderado; Kafka ou RabbitMQ surgem ao crescer throughput e necessidade de particionamento.
- Preferência por idempotência e sagas compensatórias em integrações com ERPs em vez de buscar exactly‑once em nível infra, que é caro.
- Paralelismo controlado por budget: executar agentes em paralelo melhora throughput, porém multiplica custo de tokens; arquitetar uma estratégia de agregação por score/voting ajuda a equilibrar custo vs qualidade.

Operacional
----------

- Monitorar token_cost por run, latência por node, e success_rate; definir alertas para regressões (ex.: token_cost +20% ou loop_count anômalo).
- Fazer canary deployment para modelos LLM e mudanças em nodes críticos.
- Testes: unit para cada skill (mocks de LLM), integração com replay de runs reais e testes de caos para latência/timeout.

Conclusão
---------

Surucucu equilibra durabilidade, observabilidade e flexibilidade para PoC → produção. A arquitetura favorece testes e investigação de runs históricos, com trade‑offs de custo e latência que precisam ser monitorados e ajustados conforme o produto escala.