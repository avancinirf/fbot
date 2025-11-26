# ARCHITECTURE

## Visão geral

Este projeto é um sistema de trade automatizado para **Binance Spot**, focado em:

- **Robustez** e execução confiável de ordens.
- **Análises em tempo real** (preço, indicadores, métricas customizadas).
- **Bots configuráveis** com modos de operação simulada e real.
- **IA como assistente de análise e tomada de decisão.**

A engine principal de trade será o **Freqtrade**, e o sistema terá:

- **Backend** próprio em FastAPI (orquestração, IA, regras extras).
- **Frontend** em React (painel de controle, dashboards).
- **Freqtrade** rodando no mesmo container, em modo dry-run ou live.
- **Banco de dados** SQLite (persistência simples e local).

Todo o ambiente roda em um **único container Docker**, pensado para desenvolvimento local em **MacBook Pro M1 (ARM)**.

---

## Componentes

### 1. Freqtrade (engine de trade)

Responsável por:

- Conectar na **Binance Spot**.
- Executar ordens (buy/sell) de forma robusta.
- Gerenciar estratégias, backtesting e dry-run.
- Registrar trades, ordens, saldos e performance.

Configuração:

- Usa diretório `freqtrade_config/` para `config.json`, hyperopt, etc.
- Usa `strategies/` para estratégias customizadas (incluindo as derivadas de `freqtrade-strategies`).

Modos de execução:

- **DRY_RUN**: simulação com saldo virtual, mas usando dados reais da exchange.
- **LIVE_SPOT**: operação real com saldo da conta Spot da Binance.

> Regra de segurança: as chaves da Binance serão configuradas **sem permissão de saque**, apenas **trade spot**.

---

### 2. Backend (FastAPI)

Responsável por:

- Expor uma API REST para o frontend (e potenciais integrações futuras).
- Orquestrar bots lógicos (configurações, modos, parâmetros).
- Conversar com o Freqtrade (via API/webserver, CLI ou acesso a DB/logs).
- Implementar **regras extras de segurança** (limites, bloqueios, whitelists).
- Implementar **camada de IA** para:
  - análise de mercado,
  - sumarização de sinais,
  - recomendações de configuração de bots.

Principais conceitos:

- **Bot lógico**:
  - Nome, descrição.
  - Modo: `SIMULATED` (via DRY_RUN ou simulação própria) ou `REAL_MARKET_SPOT` (via Freqtrade live).
  - Configuração de pares, risco, exposição máxima.
  - Estado: `CREATED`, `RUNNING`, `PAUSED`, `STOPPED`.

- **Análises**:
  - Métricas de mercado (preço atual, variação, volume, RSI, EMAs, etc).
  - Dados de performance (PnL, drawdown, número de trades).
  - Insights gerados por IA (ex.: “mercado lateral”, “tendência de alta”, etc).

Persistência:

- Banco **SQLite** local (por simplicidade e portabilidade).
- Usado para:
  - Configuração de bots lógicos.
  - Logs complementares do backend.
  - Cache de análises / métricas.

---

### 3. Frontend (React)

Responsável por:

- Interface gráfica para:

  - Listar e gerenciar bots (criar, editar, pausar, remover).
  - Visualizar status (saldo, PnL, exposição, pares ativos).
  - Ver **análises em tempo real** (preços, indicadores, sinais).
  - Exibir explicações e recomendações da IA.

- Comunicação com o backend via HTTP/REST (e, futuramente, WebSockets para dados em tempo real).

---

### 4. Docker (monolítico)

Um único container Docker contendo:

- Python + dependências (Freqtrade, FastAPI, libs de IA).
- Node/Yarn/NPM (para o frontend) ou build estático servido por um servidor simples.
- Supervisão de processos (ex.: script `entrypoint.sh` ou `supervisord`) para subir:
  - `freqtrade` (engine),
  - `uvicorn` (backend),
  - frontend (servidor dev ou arquivos estáticos).

Características:

- Imagem compatível com **ARM (Mac M1)**.
- Execução local (sem necessidade de outros containers).
- Exposição típica:
  - Porta `8080` (exemplo) para o backend.
  - Porta `3000` (exemplo) para o frontend.
  - Porta do webserver do Freqtrade (se exposta) apenas internamente ao container.

---

## Fluxos principais

### 1. Configuração de um bot

1. Usuário cria um bot via frontend → backend.
2. Backend salva no SQLite (configuração lógica).
3. Backend traduz a configuração em:
   - Ajustes na config do Freqtrade (pares, stake, risco, etc),
   - Ou escolha de uma estratégia específica.
4. Backend inicia ou reinicia o processo do Freqtrade para esse modo (ou controla via API do Freqtrade).

### 2. Execução de trades

1. Freqtrade monitora pares configurados (via Binance Spot).
2. Sinais de buy/sell são gerados pela **estratégia**.
3. Ordens são enviadas para a Binance (live) ou simuladas (dry-run).
4. Freqtrade registra resultados (trades, lucros, perdas).
5. Backend consome esses dados (via API ou DB/logs) e expos para o frontend.

### 3. Análises em tempo real + IA

1. Backend coleta:
   - dados do Freqtrade (trades, PnL),
   - dados de mercado (preços, indicadores),
   - estado dos bots.
2. Backend gera:
   - métricas agregadas,
   - resumos,
   - recomendações de risco/ajuste de estratégia (via IA).
3. Frontend exibe em dashboards, com atualizações periódicas ou em tempo real.

---

## Segurança

- API key da Binance:
  - Permissões: **apenas Spot Trade**, **sem saque**.
  - Idealmente com **restrição de IP**.
- Variáveis de ambiente carregadas via `.env` (nunca commitadas).
- Backend validando limites e regras, por exemplo:
  - exposição máxima por bot,
  - valor máximo por trade,
  - bloqueio de alguns pares.

---

## Tecnologias principais

- **Engine de trade:** Freqtrade
- **Backend:** FastAPI (Python)
- **Frontend:** React (TypeScript ou JavaScript)
- **Banco:** SQLite
- **Containerização:** Docker (monolítico)
- **Exchange alvo:** Binance Spot (via Freqtrade)
- **IA:** Integração futura com LLMs (via API) ou modelos locais, encapsulada no backend

---

## Roadmap de arquitetura (alto nível)

1. Fase 1 – Infraestrutura mínima:
   - Docker com Freqtrade + FastAPI.
   - Freqtrade em DRY_RUN com Binance Testnet.
   - Endpoint `/health` no backend.

2. Fase 2 – Orquestração de bots:
   - CRUD de bots lógicos.
   - Integração mínima backend ↔ Freqtrade (start/stop/status).

3. Fase 3 – Frontend básico:
   - Tela de bots.
   - Visualização de status / PnL.

4. Fase 4 – Análises em tempo real + IA:
   - Métricas e indicadores avançados.
   - Resumos e recomendações geradas por IA.

5. Fase 5 – Refinos e extensões:
   - WebSocket para dados em tempo real.
   - Suporte a múltiplas exchanges (opcional, futuro).
   - Mais estratégias otimizadas (freqtrade-strategies + custom).
