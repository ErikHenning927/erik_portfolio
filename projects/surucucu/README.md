# Surucucu — Assistente Pessoal Multi-Agente de IA

Surucucu é um ecossistema de assistente pessoal multi‑agente projetado para automação de rotinas pessoais e corporativas. A arquitetura prioriza durabilidade, auditabilidade e observabilidade para lidar com fluxos long‑running que envolvem chamadas a LLMs, ingestão de documentos (PDF/OFX), conciliação bancária e integração com serviços externos (Google Calendar, ERPs, APIs de e‑commerce).

Arquitetura resumida

- API Gateway (FastAPI): entrada única para requests, validação com Pydantic e enfileiramento inicial.
- Orquestrador (LangGraph): grafos de execução com checkpoints duráveis, replay e políticas de retry/backoff.
- Agentes isolados por domínio: cada agente implementa uma skill com inputs/outputs tipados via PydanticAI.
- Mensageria & Persistência: PostgreSQL para state store e fila inicial (SELECT FOR UPDATE SKIP LOCKED), Redis para locks e dedupe, e opcionalmente Kafka para alto throughput.
- Retrieval & RAG: embeddings armazenados em PGVector/Qdrant, com gating e scoring para seleção de evidências.
- Observabilidade: OpenTelemetry para traces, Prometheus/Grafana para métricas e logs correlacionados por run_id.

Decisões e trade‑offs

- Checkpoints duráveis (LangGraph) adicionam latência e custo de storage, mas permitem replay, investigação e redução de reexecuções custosas — indicado para fluxos críticos.
- Postgres como fila inicial é operacionalmente simples e barato; Kafka se justifica quando o throughput e particionamento forem essenciais.
- Preferir idempotência e sagas compensatórias em integrações com ERPs (SAP, VTEX) em vez de buscar exactly‑once extremo a nível infra.
- Paralelismo de agentes aumenta throughput, mas multiplica custos de tokens (LLMs); usar estratégias de agregação/score para reduzir chamadas.
- Validação forte (PydanticAI) reduz parsing errors e viagens de ida-e-volta com LLMs.

Observações operacionais

- Implementar timeouts por agente, retry budgets e circuit breakers para evitar loops e explosões de custo.
- Telemetria por run: token_cost, latency por node, success_rate e loop_count.
- Testes: mocks de LLM em unit tests, replay de logs reais em integração e teste de caos para latência/timeouts.

Resultado esperado

Um sistema resiliente e auditável, com boa capacidade de investigação de falhas e controle de custos. A complexidade operacional aumenta, mas o trade‑off justifica‑se quando se exige confiança em fluxos long‑running e integrações críticas.