# Projeto – Trading Bot Binance com Freqtrade

Sistema de trade automatizado para **Binance Spot**, usando **Freqtrade** como engine principal e uma camada própria de **backend (FastAPI)** + **frontend (React)** para orquestração, análises avançadas e IA.

Foco do projeto:

- Robustez e confiabilidade na execução de ordens.
- Análises ricas em tempo real.
- Interface simples, clara e fácil de configurar.
- Segurança máxima das chaves (apenas **trade spot**, sem saque).

---

## Arquitetura (resumo)

Componentes:

- **Freqtrade** – engine que fala com a Binance e executa ordens.
- **Backend (FastAPI)** – API própria para gerenciar bots, regras extras, IA e análises.
- **Frontend (React)** – painel web para controlar e visualizar os bots.
- **SQLite** – banco de dados local para configurações e logs.

Tudo roda dentro de **um único container Docker**, pensado para desenvolvimento local em **MacBook Pro M1 (ARM)**.

Detalhes completos em: [`ARCHITECTURE.md`](./ARCHITECTURE.md)

---

## Estrutura de pastas (inicial)

```text
.
├── backend/              # FastAPI (API, IA, orquestração)
├── frontend/             # React (painel web)
├── freqtrade_config/     # config.json, hyperopt, etc.
├── strategies/           # estratégias do Freqtrade
├── docker/               # Dockerfile, entrypoint e scripts
├── ARCHITECTURE.md       # arquitetura do sistema
├── TASKS.md              # backlog / tarefas
├── README.md             # este arquivo
└── .env.example          # exemplo de variáveis de ambiente
