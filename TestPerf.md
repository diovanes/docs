## 🧱 CHECKLIST — Testes de Performance e Resiliência em Soluções Cloud

### 🔹 1. Preparação do Ambiente

**Objetivo:** Garantir condições controladas e reproduzíveis.
**Ações:**

- [ ] Confirmar ambiente isolado de produção (staging, homolog, performance).
- [ ] Sincronizar versões de código, banco e configurações com produção.
- [ ] Desativar logs verbosos e tracing detalhado (apenas quando necessário).
- [ ] Configurar ferramentas de observabilidade (Prometheus, Grafana, New Relic, Datadog, CloudWatch).
- [ ] Validar limites de escalabilidade automática (HPA no Kubernetes, Auto Scaling Groups no AWS, VMSS no Azure).
- [ ] Garantir infraestrutura como código (Terraform, CloudFormation, Pulumi) para reprodutibilidade.

**⚠️ Ponto de Atenção:**
Erros comuns incluem testar com cache ativo, escalabilidade desligada ou em instâncias subdimensionadas.

---

### 🔹 2. Definição de Objetivos e Cenários

**Objetivo:** Medir o que realmente importa.
**Ações:**

- [ ] Definir metas de performance (ex: _p95 latency < 300ms_, _throughput > 2000 req/s_).
- [ ] Mapear fluxos críticos do sistema: login, pagamento, consulta de saldo, fechamento de lote, etc.
- [ ] Identificar limites funcionais e técnicos (CPU, memória, I/O, rede, conexões DB).
- [ ] Selecionar tipo de teste:

  - **Load Test:** capacidade nominal.
  - **Stress Test:** limite máximo antes da falha.
  - **Spike Test:** comportamento sob picos repentinos.
  - **Endurance Test:** estabilidade a longo prazo.
  - **Chaos Test:** resiliência a falhas (injetar erros, desligar serviços).

**⚠️ Ponto de Atenção:**
Em sistemas financeiros, simular transações realistas (payloads, volumes, dependências de API externas).

---

### 🔹 3. Ferramentas de Teste

**Objetivo:** Selecionar stack adequada.
**Ações:**

- [ ] Ferramentas de carga: **k6**, **JMeter**, **Gatling**, **Locust**.
- [ ] Orquestração: **Grafana k6 Operator**, **AWS Fargate**, **Azure Load Testing**, **Google Cloud Performance Testing**.
- [ ] Chaos Engineering: **Gremlin**, **LitmusChaos**, **Chaos Mesh**, **Azure Chaos Studio**.
- [ ] Testes de APIs: **Postman/Newman**, **Artillery**, **k6 REST/GraphQL scripts**.
- [ ] Monitoramento: **Prometheus + Grafana dashboards customizados**.

**⚠️ Ponto de Atenção:**
O teste deve ser automatizado via CI/CD (GitHub Actions, Jenkins, GitLab CI) com thresholds definidos — falhas automáticas se violar limites.

---

### 🔹 4. Execução dos Testes

**Objetivo:** Rodar cargas representativas com controle e visibilidade.
**Ações:**

- [ ] Aumentar gradualmente a carga até o limite esperado.
- [ ] Testar sob múltiplas zonas/regions para latência cross-region.
- [ ] Validar autoescalonamento horizontal e vertical.
- [ ] Injetar falhas controladas:

  - Derrubar pods ou serviços aleatórios.
  - Introduzir latência artificial (ex: _tc qdisc add delay_).
  - Desabilitar componentes de cache ou mensageria temporariamente.

- [ ] Coletar métricas em tempo real.

**⚠️ Ponto de Atenção:**
Monitorar uso de CPU, memória, filas, conexões de banco e _timeouts_ de downstream APIs.

---

### 🔹 5. Métricas de Performance

**Objetivo:** Quantificar eficiência e escalabilidade.
**Principais métricas:**

| Categoria                | Métrica                         | Meta / Observação                |
| ------------------------ | ------------------------------- | -------------------------------- |
| **Latência**             | p50, p95, p99                   | Respostas rápidas e consistentes |
| **Throughput**           | req/s ou transações/s           | Avaliar volume processado        |
| **Erro**                 | % de falhas, HTTP 5xx           | Tolerância ≤ 1%                  |
| **Recursos**             | CPU, memória, disco, I/O, rede  | Ideal < 80% utilização           |
| **Tempo de GC**          | Pausas do Garbage Collector     | < 100ms                          |
| **DB performance**       | Tempo médio de query, deadlocks | Avaliar queries lentas           |
| **Escalabilidade**       | Tempo de boot de instância/pod  | < 60s                            |
| **Custo por requisição** | (Custo total ÷ nº de reqs)      | Avaliar eficiência financeira    |

---

### 🔹 6. Métricas de Resiliência

**Objetivo:** Validar capacidade de recuperação e continuidade.
**Métricas:**

| Tipo                                  | Indicador                          | Meta                      |
| ------------------------------------- | ---------------------------------- | ------------------------- |
| **MTBF** (Mean Time Between Failures) | Intervalo médio entre falhas       | > 24h                     |
| **MTTR** (Mean Time to Recovery)      | Tempo médio de recuperação         | < 5min                    |
| **RTO** (Recovery Time Objective)     | Tempo máximo aceitável de parada   | conforme SLA              |
| **RPO** (Recovery Point Objective)    | Dados perdidos aceitáveis          | 0 em sistemas financeiros |
| **Erro tolerado**                     | Requests degradados mas não falhos | < 2%                      |

---

### 🔹 7. Análise dos Resultados

**Objetivo:** Extrair valor e orientar decisões.
**Ações:**

- [ ] Consolidar logs e métricas em dashboards (Grafana, CloudWatch, Datadog).
- [ ] Calcular tempo médio, percentis e variabilidade.
- [ ] Identificar _bottlenecks_ (CPU, DB, rede, locking).
- [ ] Correlacionar incidentes com logs de aplicação e tracing (OpenTelemetry, Jaeger).
- [ ] Documentar cada iteração: setup, resultados, ações corretivas.

**⚠️ Ponto de Atenção:**
Evite análises isoladas — correlacione latência com uso de CPU e tráfego de rede.

---

### 🔹 8. Ações Corretivas

**Objetivo:** Implementar melhorias.
**Ações:**

- [ ] Revisar _pool sizes_ (threads, conexões DB).
- [ ] Otimizar queries SQL e índices.
- [ ] Implementar caching e compressão de payloads.
- [ ] Avaliar filas assíncronas (Kafka, SQS, Pub/Sub).
- [ ] Configurar circuit breakers e retries (Resilience4j, Spring Cloud).
- [ ] Reduzir _cold starts_ em funções serverless.
- [ ] Validar observabilidade e alertas proativos.

---

### 🔹 9. Teste de Resiliência Avançado (Chaos Engineering)

**Objetivo:** Validar comportamento sob falhas reais.
**Ações:**

- [ ] Desligar serviços aleatórios (simulação de node failure).
- [ ] Cortar comunicação entre microserviços.
- [ ] Injetar lentidão em endpoints críticos.
- [ ] Reiniciar DBs, filas e caches sob carga.
- [ ] Validar observabilidade e _self-healing_.

**Métricas observadas:**

- Tempo de recuperação.
- Impacto no throughput.
- Percentual de requisições degradadas.
- Consistência dos dados após recuperação.

---

### 🔹 10. Relatório Final e Comunicação

**Objetivo:** Formalizar e comunicar os resultados.
**Conteúdo do relatório:**

- Objetivo dos testes.
- Ambiente e configurações usadas.
- Cenários executados.
- Métricas obtidas (tabelas e gráficos).
- _Bottlenecks_ identificados.
- Recomendações e plano de ação.
- Riscos e próximos passos.

**⚠️ Ponto de Atenção:**
Inclua sempre análise custo x benefício — nem sempre o melhor desempenho justifica o custo extra de infraestrutura.

---

## 📊 Recomendações Extras (Soluções Financeiras em Cloud)

- Ativar **circuit breakers** e **fallbacks** entre microserviços para evitar cascatas de falha.
- Usar **mensageria idempotente** (Kafka, RabbitMQ) para garantir consistência transacional.
- Criar **testes de rollback** e **reprocessamento de mensagens**.
- Usar **feature flags** para reduzir impacto de deploys em picos.
- Aplicar **rate limiting e throttling** para proteger APIs públicas.
- Medir **tempo de liquidação** (latência fim-a-fim entre origem e confirmação financeira).
- Auditar **integridade de logs e eventos** (impossível perder transação em sistemas regulados).

---

Se quiser, posso gerar uma **planilha Excel automática** com todas essas etapas — com colunas de “Status”, “Responsável”, “Data de Execução” e “Observações” — pronta para uso em auditorias ou squads DevOps.
Quer que eu monte esse arquivo?

===================================

# ✅ Plano de Testes de Performance & Resiliência — Pipeline JSON + Mensageria + DB Relacional

## 0) Escopo do sistema sob teste (SUT)

- **Entrada:** arquivos `.json` (até 100.000 registros/arquivo).
- **Processamento:** pipeline sequenciado; orquestrador define **ordem** e **paralelismo**; todos os estágios comunicam via **mensageria** (pub/sub, filas, DLQ).
- **Saída:** **1 registro por linha** em banco relacional (INSERT/UPSERT).
- **Garantias desejadas:** alta vazão, baixa latência p95/p99, **idempotência** (sem efeito colateral em reprocesso), **reprocessamento seguro**, **zero perda** e **zero duplicação** (ou duplicação detectada e saneada).

---

## 1) Preparação e premissas

- **Ambiente isolado** (staging/performance) espelhando produção (mesmas versões de serviços, schemas, índices, limites).
- **Dados sintéticos controlados**: gerador que cria arquivos com padrões realistas (payloads válidos/ inválidos, chaves repetidas, ordem variada).
- **Observabilidade ligada**: métricas e logs por estágio (latência, throughput, lag de filas, taxas de retry, DLQ, commit/rollback DB).
- **Controle de cache e autoescalonamento**: documentar políticas de HPA/ASG e limites de conexões com o DB.
- **Flag de idempotência** ativa em todos os handlers que possam reprocessar mensagens/registros.

**Pontos de atenção**

- Não testar com caches frios num ciclo e quentes em outro sem registrar isso.
- Garantir `unique key`/`upsert` no DB para deduplicação (ver **Seção 5.3**).

---

## 2) Objetivos e SLAs sugeridos

> Adapte números ao seu negócio—o formato é o que importa.

- **Throughput**: ≥ _X_ mil registros/min (pico ≥ _Y_ mil).
- **Latência fim-a-fim p95** (receber arquivo → persistir todas as linhas): ≤ _N_ min p95; ≤ _M_ min p99.
- **Erros irrecuperáveis**: ≤ 0,1% (com roteamento para DLQ e _playback_).
- **Perda de dados**: 0.
- **Duplicação**: 0 visível (ou ≤ _DPPM_ com auto-saneamento).
- **MTTR** após falha de nó/serviço: ≤ 5 min até retomada de processamento.
- **Reprocesso**: idempotente (sem linhas extras nem corrupção).

---

## 3) Modelos de carga (workloads)

1. **Nominal (steady load)**

   - 10 arquivos × 100k cada (1 milhão de registros); chegada suave (ex.: 1 arquivo/3 min).

2. **Pico (spike)**

   - 10 arquivos simultâneos × 100k cada (chegada em 30s).

3. **Endurance (soak)**

   - 6–12 horas contínuas, 1–2 arquivos/min (volumetria total 30–100 milhões).

4. **Explosão de pequenos**

   - 2.000 arquivos × 2k registros (testa overhead de orquestração/escala).

5. **Misto com falhas**

   - Mistura nominal + injeção de falhas (rede, nó, latência, throttle do DB) a cada _X_ minutos.

---

## 4) Paralelismo e orquestração

### 4.1 Testes de paralelismo do pipeline

- **Objetivo:** validar que _N_ workers escalam quase linearmente até o ponto de saturação (CPU, DB, rede ou broker).
- **Cenários:**

  - **Escada de paralelismo:** N={1, 2, 4, 8, 16, 32}. Meça throughput e p95 a cada degrau.
  - **Limited by DB:** max connections/transactions deliberadamente baixos para medir fila interna e backpressure.
  - **Limited by broker:** reduza prefetch/partições para forçar gargalo na mensageria.
  - **Bound por IO:** aumente tamanho de payload/serialização para ver impacto na CPU/GC.

**Métricas alvo**

- Throughput vs N de workers (curva de ganho).
- **Utilização DB** (TPS, lock waits, I/O), **lag de fila**, **taxa de reentrega**.
- **Ponto de saturação**: 1º componente a 80–90%.

### 4.2 Ordem e consistência

- Se a **ordem por arquivo** for requisito, valide:

  - Particionamento por _fileId_ (garantir consumo ordenado por partição ou _sequence key_).
  - Processamento **intra-arquivo**: chunks paralelos mas commit ordenado? Documentar regra e testar.

---

## 5) Integridade de dados: perda, duplicação, idempotência

### 5.1 Perda de dados

- **Como medir**

  - `count(input_registros)` vs `count(db_linhas)` + `count(DLQ)` + `count(em processamento)` → **tem que fechar 100%**.
  - Checksum por arquivo: `sum(hash(businessKey))` comparado entre entrada e saída (descontando inválidos que foram para DLQ).

- **Casos de teste**

  1. Interromper consumidor no meio do arquivo; religar e verificar reconciliação = 0 perda.
  2. Falha de rede entre estágio e DB com retry → nenhuma linha perdida, sem duplicar.
  3. Queda do orquestrador com mensagens em voo → após _re-delivery_, reconciliação fecha.

### 5.2 Duplicações

- **Fontes comuns**

  - Entrega **at-least-once** do broker, _timeouts_ de commit, _retry_ após _ACK_ tardio, reprocesso de arquivo.

- **Estratégias de contenção**

  - **Idempotency Key** por registro (ex.: `businessKey` ou `hash(payload canônico)`).
  - **UPSERT** (chave única no DB) em vez de `INSERT` simples.
  - **Tabelas de dedupe** com TTL curto para gravações recentes.

- **Casos de teste**

  1. Reenvio do **mesmo arquivo** (mesmo `fileId`) → 0 novas linhas no DB.
  2. Reenvio de **arquivo idêntico com novo `fileId`** → se negócio exige dedupe global, 0 novas linhas; caso contrário, duplicação esperada e medida.
  3. Mensagem duplicada pelo broker (forçada via script) → 0 linhas extras (UPSERT deve segurar).
  4. Conflito competitivo (2 workers tentam gravar mesma chave) → **violação única** no DB deve ser tratada com retry controlado (e não travar fila).

### 5.3 Idempotência (reprocesso seguro)

- **Requisitos**

  - Toda operação de escrita deve ser **idempotente**: múltiplas tentativas produzem o **mesmo** estado final.
  - **Side effects** (auditoria, eventos derivados) também precisam de idempotência (ex.: outbox transacional).

- **Casos de teste**

  1. _Replay_ do lote inteiro (mesmo `fileId`) 3 vezes → contagem final no DB não muda.
  2. _Replay_ parcial (do registro _k_ ao _k+n_) → estado final consistente.
  3. Conexão cai após `INSERT` mas antes do ACK → serviço reenvia; DB segura via **unique key**.

**SQL de apoio (exemplo genérico)**

```sql
-- duplicatas por business_key
SELECT business_key, COUNT(*)
FROM tabela_saida
GROUP BY business_key HAVING COUNT(*) > 1;

-- conferência por arquivo
SELECT file_id, COUNT(*)
FROM tabela_saida
GROUP BY file_id;

-- diferença entre input esperado e output realizado (usar staging_input para referência)
SELECT i.file_id, (i.qtd - o.qtd) AS faltantes
FROM inputs_esperados i
LEFT JOIN (
  SELECT file_id, COUNT(*) AS qtd FROM tabela_saida GROUP BY file_id
) o USING (file_id);
```

---

## 6) Testes de resiliência (Chaos & fault injection)

### 6.1 Falhas de infraestrutura

- **Queda de nó/worker**: matar 10–30% dos pods no pico; verificar MTTR, auto-rebalance do broker e **ausência de perda/duplicação**.
- **Partição de rede** entre estágio e DB por 2–5 min: medir backlog, retries, _throttling_ e recuperação.
- **Degradação do DB** (latência +200%, conexões máx. atingidas): confirmar backpressure no pipeline (sem _timeout storm_).

### 6.2 Falhas funcionais

- **Poison messages**: registros inválidos que sempre falham → roteamento para **DLQ** com metadados (fileId, offset, erro).
- **Falhas intermitentes** (ex.: 5xx aleatórios em serviço de enriquecimento): política de retry exponencial + jitter; **sem efeito de tempestade**.

### 6.3 Recuperação e reprocesso

- Procedimento **runbook**:

  - (1) Identificar causa, (2) drenar fila ou isolar partição, (3) corrigir, (4) **replay** seguro (por `fileId` ou _offset range_), (5) reconciliação SQL, (6) “go/ no-go” para liberar DLQ.

**Métricas de resiliência**

- **MTTR**, **tamanho do backlog** vs tempo, **taxa de retry**, **DLQ inflow/outflow**, **tempo para normalizar p95** após falha.

---

## 7) Performance end-to-end

### 7.1 Tipos de teste

- **Load**: capacidade nominal com _thresholds_ de aprovação (p95, throughput).
- **Stress**: achar o colapso controlado (onde filas explodem, DB satura, timeouts).
- **Spike**: 10 arquivos gigantes ao mesmo tempo → medir _autoscaling lag_.
- **Endurance**: 6–12h para detectar vazamentos de memória, GC long pause, crescimento de conexões.

### 7.2 Métricas obrigatórias

| Categoria  | Métrica                                          | Como analisar                                          |
| ---------- | ------------------------------------------------ | ------------------------------------------------------ |
| Latência   | p50/p95/p99 por estágio e fim-a-fim por arquivo  | Quedas de cauda longa indicam contenção (DB, GC, lock) |
| Throughput | registros/s e arquivos/h                         | Curva vs paralelismo; identificar ponto de saturação   |
| Broker     | lag por fila/tópico/partição, reentregas         | Lag crescente = gargalo downstream                     |
| DB         | TPS, locks, filas de espera, I/O, índices usados | Se p95 aumenta com DB estável → gargalo é antes        |
| Erros      | % 4xx/5xx por estágio, DLQ rate                  | 4xx: dados; 5xx: sistema                               |
| Custo      | $ por 1M registros                               | Otimizar autoescalonamento e batch size                |

---

## 8) Qualidade de persistência (relacional)

### 8.1 Chaves e índices

- **Unique** sobre `business_key` (ou `fileId+recordId`), suportando **UPSERT** (ex.: `INSERT ... ON CONFLICT DO UPDATE`).
- Índices em colunas de consulta/report (datas, status, fileId).
- Tuning de **batch size** de escrita (ex.: 500–2.000 linhas por transação) para equilibrar TPS/lock/redo log.

### 8.2 Transações

- **Atomicidade por bloco**: em falha, _rollback_ limpa parcial.
- **Outbox transacional** (se publicar eventos após persistir).
- **Deadlocks**: simular concorrência em mesma `business_key` e validar retries com _backoff_.

---

## 9) Cenários de teste detalhados (passo a passo)

### C1 — Pipeline nominal (100k)

- **Dado** 1 arquivo com 100k registros válidos.
- **Quando** processado com N=8 workers.
- **Então** p95 ≤ SLA, `count(DB)=100k`, `DLQ=0`, duplicações=0.

### C2 — Parallel scale-up

- Mesmos dados, repetir com N={1,2,4,8,16,32}.
- **Esperado**: ganho quase linear até saturar; registrar **componente limitante**.

### C3 — Pico simultâneo (10×100k)

- **Quando** chega 1M de registros quase ao mesmo tempo.
- **Então** autoscaling aciona, lag no broker volta ao normal ≤ _T_ min; sem perda ou duplicação.

### C4 — Reenvio do mesmo arquivo (idempotência)

- **Quando** o mesmo arquivo (mesmo `fileId`) é reprocessado 3 vezes.
- **Então** `count(DB)` não aumenta; delta=0.

### C5 — Reenvio de arquivo idêntico com _novo_ `fileId`

- **Se** dedupe global for requisito: delta=0.
- **Se não for**: delta=+100k (com rastreabilidade por `fileId`).

### C6 — Consumer crash

- **Quando** 30% dos consumidores caem no meio do processamento.
- **Então** MTTR ≤ 5 min; backlog estabiliza; reconciliação fecha 100%.

### C7 — DB lento + limite de conexões

- **Quando** adiciona +200% de latência no DB e reduz conexões máx.
- **Então** pipeline faz backpressure, sem tempestade de timeouts; sem perda/dup.

### C8 — Poison messages

- **Dado** 2% registros inválidos.
- **Quando** processados.
- **Então** vão para DLQ com motivo; `DB` só contém válidos; relatório lista amostras e causas.

### C9 — Falha de rede intermitente (broker↔workers)

- **Quando** existirem quedas de 30–60s a cada 5 min.
- **Então** retry com _exponential backoff_; sem efeito cascata; sem perda de ACK.

### C10 — Reprocesso parcial por intervalo

- **Quando** reprocessar do offset 50k–70k do arquivo A.
- **Então** estado final consistente (sem duplicar; sem buracos).

### C11 — Conflito de escrita (race)

- **Dado** 2 workers recebem a mesma `business_key`.
- **Então** 1 grava, o outro recebe erro de unique e faz retry/backoff → sem duplicação.

### C12 — Endurance 12h

- **Quando** rodar carga contínua.
- **Então** sem vazamento de memória, sem crescimento infinito de conexões, p95 estável.

---

## 10) Táticas de instrumentação & validação

### 10.1 Métricas mínimas por estágio

- `records_in/records_out`, `latency_p95/p99`, `retries`, `dead_letter_count`, `consumer_lag`, `db_tps`, `db_errors`, `gc_pause_ms`.

### 10.2 Logs e tracing

- **Correlação** por `traceId`/`fileId`/`recordId`.
- **Sampling dinâmico**: aumentar amostra em erro.
- **Eventos de auditoria**: `received`, `validated`, `persisted`, `ack`.

### 10.3 Reconciliation automatizado

- Job que compara entrada × saída × DLQ após cada rodada e gera **relatório de integridade**:

  - Perdas = 0
  - Duplicações = 0 (ou lista controlada com _auto-fix_)
  - Registros inválidos catalogados por categoria de erro.

---

## 11) Critérios de aprovação (go/no-go)

- Atingir SLAs de **throughput** e **latência** nas cargas **nominal** e **pico**.
- **Perda** = 0; **duplicação** = 0 (ou ≤ alvo com rotina de saneamento automatizada).
- **MTTR** dentro do limite; **DLQ** com _playbook_ validado.
- **Idempotência** comprovada nos cenários C4/C10.
- **Endurance** sem degradação progressiva.

---

## 12) Relatório final (template)

1. **Resumo executivo**: atingiu SLAs? riscos residuais?
2. **Ambiente**: versões, sizing, configs (broker, DB, índices, HPA).
3. **Carga**: arquivos, volumetria, formato, distribuição temporal.
4. **Resultados**: tabelas de p50/p95/p99, throughput, lag, DB TPS, retries, DLQ.
5. **Integridade**: reconciliação, perdas, duplicações, inválidos.
6. **Resiliência**: MTTR, comportamento sob falhas, ações automáticas.
7. **Bottlenecks & root causes**: onde saturou, por quê, gráficos.
8. **Ações recomendadas**: tuning, refatorações, índices, políticas de retry/backoff, batch size, limites de conexão, dedupe.
9. **Próximos passos**: re-testes após correções, metas elevadas, ajuste de custo.

---

## 13) Dicas práticas (evitam dor de cabeça)

- **Defina a idempotency key oficialmente** (ex.: `business_key`; se não houver, `hash(normalized_payload)`), e **garanta UPSERT**.
- **Prefetch/Batch**: teste `prefetch` baixo vs alto e `batch size` de escrita no DB (500–2000) — quase sempre dá ganhos grandes.
- **Particionamento de fila por `fileId`** se precisar de **ordem dentro do arquivo**.
- **DLQ com metadados ricos** (erro, estágio, tentativa, offsets) e **playback por janela**.
- **Alarmes** por **lag**, **retries** e **p95**; alerta por **“taxa de duplicação > 0”**.
- **Scripts SQL de reconciliação** incorporados ao pipeline de validação pós-lote.

---

### Quer o “pacote operacional”?

Eu posso já te entregar uma **planilha de execução** (CSV/Excel) com:

- Caso de teste, Objetivo, Dados de entrada, Passos, Métricas a coletar, Critério de aceite, Resultado, Ação corretiva.
- E um **script SQL base** de reconciliação (parâmetros por `fileId`).

Se quiser, digo “sim” e eu já gero os arquivos agora.
