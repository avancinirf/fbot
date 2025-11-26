# TASKS

Este arquivo concentra as tarefas organizadas por fases.  
A ideia é sempre manter as próximas ações claras e simples.

---

## Fase 1 – Infraestrutura mínima (Freqtrade + FastAPI + Docker)

- [ ] Criar estrutura de pastas do projeto:
  - [ ] `backend/`
  - [ ] `frontend/`
  - [ ] `freqtrade_config/`
  - [ ] `strategies/`
  - [ ] `docker/`

- [ ] Adicionar `ARCHITECTURE.md`, `TASKS.md`, `README.md`, `.env.example`.

- [ ] Docker:
  - [ ] Criar `docker/Dockerfile` base (Python + Freqtrade + FastAPI).
  - [ ] Adicionar script de entrypoint para subir:
    - [ ] Freqtrade (em DRY_RUN).
    - [ ] Backend FastAPI (uvicorn).

- [ ] Backend:
  - [ ] Criar app FastAPI mínimo (`backend/main.py`).
  - [ ] Implementar `/health` (status básico do sistema).

- [ ] Freqtrade:
  - [ ] Criar `freqtrade_config/config.json` mínimo para Binance Testnet (DRY_RUN).
  - [ ] Confirmar que `freqtrade` sobe dentro do container sem erros.

---

## Fase 2 – Orquestração de bots (backend ↔ Freqtrade)

- [ ] Modelagem de “bot lógico” no backend (SQLite):
  - [ ] Criar migrations ou modelos (SQLAlchemy ou similar).
  - [ ] Campos mínimos: `id`, `name`, `description`, `mode`, `pairs`, `risk_params`, `status`.

- [ ] Endpoints backend:
  - [ ] `GET /bots` – listar bots.
  - [ ] `POST /bots` – criar bot.
  - [ ] `GET /bots/{id}` – detalhes.
  - [ ] `PATCH /bots/{id}` – editar.
  - [ ] `POST /bots/{id}/start` – iniciar bot (conectar com Freqtrade).
  - [ ] `POST /bots/{id}/stop` – parar bot.

- [ ] Integração com Freqtrade:
  - [ ] Definir forma de controle inicial (API/webserver ou comandos internos).
  - [ ] Implementar leitura de status do Freqtrade.
  - [ ] Mapear um bot lógico → config/estratégia do Freqtrade.

---

## Fase 3 – Frontend básico

- [ ] Inicializar projeto React em `frontend/`.
- [ ] Tela principal:
  - [ ] Listagem de bots (consumir `GET /bots`).
  - [ ] Formulário simples de criação de bot (consumir `POST /bots`).
  - [ ] Botões de `start` / `stop` (consumir endpoints do backend).

- [ ] Adicionar layout simples:
  - [ ] Navbar com logo/nome do sistema.
  - [ ] Área de conteúdo para lista de bots e status.

---

## Fase 4 – Análises em tempo real + IA

- [ ] Backend:
  - [ ] Endpoint para métricas de mercado e performance do bot.
  - [ ] Lógica para coletar dados do Freqtrade (trades, PnL, exposição).
  - [ ] Cálculo de indicadores (RSI, EMAs, etc.).
  - [ ] Integração com IA (LLM via API) para gerar:
    - [ ] Resumo do estado do mercado.
    - [ ] Recomendações de ajustes de bot (texto).

- [ ] Frontend:
  - [ ] Dashboard com métricas por bot.
  - [ ] Cards com insights da IA.

- [ ] WebSocket (opcional, pode ser fase 5):
  - [ ] Configurar canal de atualização em tempo quase real.
  - [ ] Atualizar dashboard sem precisar de refresh manual.

---

## Fase 5 – Refinos e extensões

- [ ] Melhorar segurança:
  - [ ] Validação de limites de exposição.
  - [ ] Bloqueio de alguns pares por configuração.
  - [ ] Logs de auditoria de ações críticas.

- [ ] Estratégias:
  - [ ] Trazer e adaptar algumas `freqtrade-strategies`.
  - [ ] Estruturar pasta `strategies/` com padrão bem definido.
  - [ ] Documentar como adicionar novas estratégias.

- [ ] UX:
  - [ ] Melhorar UX do frontend (filtros, busca, indicadores visuais).
  - [ ] Adicionar páginas de histórico de trades e PnL.

---

## Notas de trabalho

- Sempre que uma tarefa for concluída, marcar `[x]`.
- Novas tarefas devem ser adicionadas aqui com pequenas descrições.
- Usar este arquivo como “kanban textual” para guiar o desenvolvimento.
