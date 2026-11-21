Surucucu — Assistente Pessoal Multi-Agente de IA

Plataforma Full Stack multi-agente projetada para automação pessoal e corporativa com foco em durabilidade, observabilidade e experiência humana-in-the-loop.

Visão geral

Surucucu unifica agentes especializados (finanças, conciliação bancária, agenda, carreira/LinkedIn) orquestrados por um controlador de workflows, com APIs REST e interfaces de bot (Telegram) e web/mobile.

Principais componentes

- Orquestração multi-agente com checkpoints e replay para fluxos long-running.
- API Gateway (FastAPI) para entrada única de requests e validação com Pydantic.
- State store durável em PostgreSQL, filas leves, Redis para locks e dedupe.
- RAG e embeddings (PGVector / Qdrant) para recuperação de contexto.
- Observabilidade com OpenTelemetry, métricas e traces integrados a Grafana.

Destaques técnicos

- Arquitetura orientada a eventos com garantia de idempotência.
- Checks duráveis para troubleshooting e retomada de runs sem recomputação.
- Integrações nativas com serviços bancários (conciliação OFX/PDF) e agendas externas (Google/iCal).

Tecnologias

Python, FastAPI, LangGraph (orquestração), PostgreSQL, Redis, PGVector/Qdrant, React/React Native, Telegram Bot API, OpenTelemetry.

Instalação (resumo)

1. Clonar o repositório.
2. Criar env e instalar dependências.
3. Configurar variáveis de ambiente (DB, keys de APIs, endpoints de mensageria).
4. Rodar migrations e iniciar serviços.

Nota de atualização

- 2026-11-21: removida referência textual a nomes de arquivos de imagem (ex.: surucucu.png) do README do projeto para evitar dependência de caminhos/nomes estáticos. Imagens poderão ser gerenciadas via assets externos ou CDN quando necessário.
