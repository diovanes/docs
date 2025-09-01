Crie um due diligence para analise técnica de uma grande solução de biometria, crie um check-list de perguntas que devem ser realizadas para garantir que a solução atenda os padrões de qualidade se segurança. O check-list deve ser segmentado por áreas, como arquitetura, segurança, infraestrutura, banco dados, etc. Inclua questões relevantes na analise, aprofundando em todos os segmentos analisados.

A seguir está um framework de due diligence técnica para uma solução de biometria em larga escala. O material está organizado em check‑lists por domínio, com ênfase em padrões internacionais, boas práticas de arquitetura e segurança de referência. Use como roteiro de entrevistas com o fornecedor/equipe interna e como base para evidências documentais.

# 1) Estratégia e requisitos de negócio

- Quais são os casos de uso (verificação 1:1, identificação 1\:N, deduplicação, watchlist, on‑device vs. server‑side)? O sistema opera em “closed‑set” ou “open‑set”?
- Quais são metas mensuráveis (SLOs) de acurácia, latência, throughput, disponibilidade e custo por transação?
- Existem métricas regulatórias/contratuais (SLA) e penalidades por não conformidade? Como são auditadas?

# 2) Biométrico e ciência de dados (precisão, viés e robustez)

- Quais modalidades: face, dedo, íris, voz, palma, comportamento? Há “fusion” entre modalidades?
- Acurácia: apresentar FAR/FRR/EER, curvas ROC/DET e análise por subgrupos demográficos (sexo, faixa etária, fototipo, acessórios). Metodologia de teste aderente à ISO/IEC 19795‑1? ([ISO][1])
- Resultados independentes: o algoritmo consta nas avaliações do NIST (FRTE/FRVT para face; MINEX III para templates de digitais)? Pedir ID de submissão/report e comparativos. ([NIST Pages][2], [NIST Publications][3], [NIST][4])
- Liveness/PAD: quais técnicas e resultados contra “presentation attacks” (foto, vídeo, máscara, impressão 3D)? Exigir métricas APCER/BPCER e adesão a ISO/IEC 30107‑3. ([ISO][5])
- Qualidade de amostra: como é medido (NFIQ 2.0 para digitais; ISO/IEC 29794‑5 para face)? Existem thresholds e recaptura automática? ([NIST][6], [ISO][1])
- Drift/aging: há monitoramento de degradação ao longo do tempo (ex.: aging em face)? Estratégia de “re‑enrolment” e retreino? Evidências de testes NIST de envelhecimento, quando aplicável. ([NEC Global][7])
- Explainability/observabilidade de modelo: que atributos/qualidade influenciam a decisão? Há “score calibration” e faixas operacionais por cenário?

# 3) Padrões de dados biométricos e interoperabilidade

- Formatos de template e troca de dados aderem a ISO/IEC 19794‑2 (minúcias de digitais) e/ou à geração mais recente 39794‑x (face, dedo, íris)? Pedir amostras binárias/XML e schemas. ([ISO][8])
- Qualidade e avaliação de face conforme ISO/IEC 29794‑5 (2025)? Processos para recusa/recaptura. ([ISO][1])
- Suporte a CBEFF (ISO/IEC 19785) e BioAPI (ISO/IEC 19784) para portabilidade e integração com outros AFIS/ABIS? ([FIDO Alliance][9], [W3C][10])

# 4) Proteção do template e privacidade por design

- O projeto segue ISO/IEC 24745 (proteção da informação biométrica)? Há “template protection” (ex.: cancelable biometrics ou biometric cryptosystems) e impossibilidade de reconstrução do traço? ([ISO][11])
- Criptografia de dados em repouso e em trânsito; módulos e cofres criptográficos com validação FIPS 140‑3/HSM/KMS? Rotação de chaves e segregação (dados x chaves). ([NIST Computer Security Resource Center][12])
- Minimização e separação lógica: dados biométricos separados de PII? Tokenização/pseudonimização?
- Retenção e descarte: política de prazos e “revogação” (ex.: exclusão do template e dos metadados derivados).

# 5) Conformidade regulatória (Brasil e padrões globais)

- LGPD: tratamento de dados biométricos como “dados pessoais sensíveis” (art. 5º, II). Qual a base legal (consentimento, cumprimento de obrigação legal, tutela da saúde, etc.) e o RasP (RoPA)? Existe RIPD/DPIA formal? ([Portal da Câmara dos Deputados][13], [Mattos Filho][14])
- Diretrizes/agenda da ANPD sobre biometria (tomada de subsídios; expectativas sobre DPIA e riscos elevados). ([Data Privacy Brasil Research][15], [BSA][16])
- NIST SP 800‑63B (biometria não pode ser fator único; PAD obrigatório; limiares mínimos e detecção de “rate‑limiting” de tentativas). Como a solução mapeia IAL/AAL/FAL? ([templates.machinezoo.com][17])
- Certificações de segurança e privacidade (ISO/IEC 27001; 27701; SOC 2). Evidências e escopo.

# 6) Arquitetura de software (macro e tática)

- Estilo arquitetural: monólito modular ou microserviços? Domínios e limites (DDD). Modelagem C4 disponível?
- Padrões de resiliência: circuit breaker, bulkhead, timeout, retry com backoff e idempotência nos fluxos de captura/matching; filas assíncronas com outbox/“exactly‑once” lógico. Como isso é testado em caos‑engineering?
- API Gateway e service mesh: mTLS, autenticação mútua, rotação de certificados, rate‑limit por cliente e por endpoint; canary/blue‑green e feature flags.
- Estado e cache: políticas de cache dos embeddings/templates, coerência e invalidação; TTL e “warm‑up”.

# 7) Segurança de aplicações e APIs

- Aderência a OWASP ASVS e OWASP API Security Top 10 (autenticação forte, controle de autorização por recurso, proteção a BOLA/IDOR, validação de entrada, proteção a scraping). Evidências de SAST/DAST/IAST. ([GitHub][18], [OWASP][19])
- Gestão de segredos (vault), rotação e “no‑secret‑in‑code”. Proteções contra SSRF, deserialização e injeções.
- Mecanismos anti‑automação: rate limiting adaptativo, proof‑of‑work/Device Attestation onde couber.
- Registro/auditoria imutável (WORM/append‑only) sem armazenar imagens brutas desnecessariamente; redaction e controle de acesso a logs.

# 8) Mobile/edge e SDKs

- No iOS, o matching ocorre no dispositivo (Secure Enclave) ou no servidor? Há “biometric prompt” e atestação do dispositivo? ([Apple Support][20])
- No Android, uso do Android Keystore com chave “hardware‑backed” e StrongBox quando disponível; chaves “user‑auth‑bound”; detecção de root/jailbreak; proteções contra emuladores. ([Android Developers][21], [Android Open Source Project][22])
- Se a estratégia é passwordless, há suporte a FIDO2/WebAuthn (plataforma e cross‑platform authenticators), reduzindo necessidade de armazenar templates no servidor? ([OWASP Mobile Application Security][23], [ISO][24])
- Liveness on‑device: cobertura para ataques digitais (tela) e físicos (máscaras); coleta passiva/ativa; falsos bloqueios aceitáveis por UX.

# 9) Dados, bancos e armazenamento

- Modelagem: separação entre cadastros, templates, metadados e trilhas de auditoria; chaves distintas (KMS/HSM) por domínio de dados. FIPS 140‑3 para módulos criptográficos. ([NIST Publications][25])
- Banco de templates: qual engine (RDBMS/NoSQL/objeto)? Índices e busca vetorial/biométrica (1\:N) suportam criptografia em repouso e “queryable encryption”/particionamento por região?
- Backups: criptografados, com testes periódicos de restore, RPO/RTO, e retenção aderente à LGPD.
- Replicação multi‑AZ/região; estratégia anti “hot‑spot” em grandes galerias (sharding e “gallery partitioning”).

# 10) Infraestrutura, redes e SRE

- Topologia (on‑prem, cloud, híbrida, multirregião). Segmentação de rede, WAF, DDoS, IDS/IPS.
- Provisionamento infra‑as‑code; hardening CIS; varreduras de configuração.
- Observabilidade: métricas de engenharia (latência P95/P99, fila, erros), de negócio (taxa de captura aceita/recaptura, taxas de PAD) e de qualidade biométrica (ex.: distribuição NFIQ e score de face). Orçamentos de erro/SLOs e runbooks de incidentes.
- Testes de carga (picos), stress e “soak”. Estratégia para bursts (fila + backpressure).

# 11) Ciclo de vida seguro e supply chain

- SDLC: SAST/DAST, SCA, pentest periódico, política de branches, “two‑person rule”, assinaturas de artefatos.
- SBOM gerado e entregue (SPDX ou CycloneDX), política de vulnerabilidades e proveniência atestada (SLSA nível‑alvo). ([SPDX][26], [cyclonedx.org][27], [SLSA][28])
- Controle de dependências nativas (ex.: OpenCV, ONNX Runtime, frameworks de DL) e de modelos (hash/assinatura, cadeia de confiança).

# 12) Operações, suporte e governança

- Playbooks de suporte N2/N3, tempos de resposta e escalonamento. Treinamento de operadores para coleta adequada (reduz FRR, melhora PAD).
- Gestão de incidentes de privacidade (planos e comunicação à ANPD quando aplicável). Exercícios de mesa.
- Data governance: DPO indicado, políticas de acesso e revisões periódicas; direitos do titular (acesso, eliminação, portabilidade). ([Serviços e Informações do Brasil][29])

---

## Check‑list prático de perguntas por área

### A. Arquitetura

1. Desenho C4 disponível? Como os bounded contexts estão separados (DDD)?
2. Há API Gateway e service mesh com mTLS, rate limiting, circuit breaker e timeouts padronizados?
3. Quais fluxos são síncronos vs. assíncronos? Existe transactional outbox para integração?
4. Estratégia de versionamento de API e compatibilidade de templates?
5. Estratégia de “canary/blue‑green” e feature flags para mudar limiares (PAD, score) sem downtime?

### B. Biométrico e desempenho

1. Qual a base de treinamento/validação (proveniência, diversidade, consentimento)?
2. Quais as métricas alvo por caso de uso (FMR/FNMR/EER) e por demografia? Relatórios ISO/IEC 19795‑1 fornecidos? ([ISO][1])
3. Quais os resultados públicos no NIST FRTE/FRVT ou MINEX III (IDs e datas)? ([NIST Pages][2], [NIST][4])
4. Estratégia de PAD conforme ISO/IEC 30107‑3; valores APCER/BPCER; cobertura contra ataques conhecidos. ([ISO][5])
5. Qualidade de amostra (NFIQ 2 para digitais; ISO/IEC 29794‑5 para face) e taxa de recaptura. ([NIST][6], [ISO][1])

### C. Dados, formatos e interoperabilidade

1. Quais formatos: ISO/IEC 19794‑2 (minúcias), 39794‑5 (face), 39794‑4 (finger image)? Amostras e conformidade. ([ISO][8])
2. Suporte a CBEFF e BioAPI? Planos de migração entre provedores (evitar vendor lock‑in). ([FIDO Alliance][9], [W3C][10])
3. Como é feita a exportação de templates e a re‑inscrição segura em outro sistema?

### D. Segurança de informação

1. Criptografia em trânsito (TLS 1.2+) e em repouso (AES‑GCM). Módulos com validação FIPS 140‑3? Gestão de chaves em HSM/KMS? ([NIST Computer Security Resource Center][12])
2. Gestão de segredos e rotação; segregação de funções e “least privilege”.
3. Conformidade ASVS/API Top 10; resultados de SAST/DAST/pen tests. ([GitHub][18], [OWASP][19])
4. Logs/auditoria imutáveis, sem dados biométricos brutos; retenção e acesso monitorado.
5. Se há pagamentos/integrações financeiras, PCI DSS v4.0.1 aplicável? ([Middlebury][30])

### E. Privacidade e LGPD

1. Qual a base legal para tratar dado biométrico sensível e como é obtido o consentimento quando exigido? ([Portal da Câmara dos Deputados][13])
2. Existe RIPD/DPIA para os fluxos de alto risco? Referência à orientação da ANPD. ([BSA][16])
3. Políticas de minimização, retenção e descarte; processos de atendimento a direitos do titular. ([Serviços e Informações do Brasil][29])
4. Transferência internacional de dados: onde ficam armazenados templates/PII? Há cláusulas contratuais específicas?

### F. Mobile/edge

1. iOS: uso do Secure Enclave; chaves não exportáveis; atestação do dispositivo; biometria nunca sai do enclave. ([Apple Support][20])
2. Android: uso do Keystore/StrongBox, chaves “user‑auth‑bound”; políticas para root/jailbreak; proteções anti‑hooking. ([Android Developers][21], [Android Open Source Project][22])
3. Suporte a WebAuthn/FIDO2 quando aplicável. ([OWASP Mobile Application Security][23])

### G. Infraestrutura e redes

1. Desenho de VPC/VNet, sub‑redes privadas, WAF e DDoS.
2. Multi‑AZ/região, RTO/RPO e plano de desastre.
3. Hardening CIS, escaneamento contínuo e patching.
4. Observabilidade (traces, logs, métricas), SLOs e orçamentos de erro.

### H. Banco de dados e armazenamento

1. Criptografia de campo/coluna para templates; segregação de chaves; “envelope encryption”.
2. Estratégias de particionamento/sharding para galerias 1\:N; índices e busca aproximada.
3. Backups criptografados, testes de restore e retenção aderente à LGPD.
4. Controles de acesso refinados (ABAC/RBAC) e mascaramento dinâmico de dados.

### I. SDLC, supply chain e compliance

1. Pipeline com assinaturas, revisões, “two‑person rule” e dependabot/SCA.
2. SBOM entregue (SPDX/CycloneDX) e meta de SLSA (≥2 para build e provenance). ([SPDX][26], [cyclonedx.org][27], [SLSA][28])
3. Política de vulnerabilidades, janelas de manutenção e comunicação transparente.

### J. Operações e suporte

1. Suporte 24x7? MTTR esperado? Runbooks e exercícios de incidentes.
2. Treinamento de operadores/coleta (melhora NFIQ/qualidade de face). ([NIST][6], [ISO][1])
3. Roadmap e política de EOL para SDKs e formatos.

---

## Artefatos e evidências a solicitar (amostras)

- Relatórios de desempenho em conformidade com ISO/IEC 19795‑1 (EER/ROC/DET por cenário e demografia). ([ISO][1])
- Relatório de PAD conforme ISO/IEC 30107‑3 com APCER/BPCER por ataque. ([ISO][5])
- Evidências FRTE/FRVT e/ou MINEX III (link/ID de submissão, data e versão do algoritmo). ([NIST Pages][2], [NIST][4])
- Amostras de template em ISO/IEC 19794‑2 e/ou 39794‑5 e documentação de mapeamento de campos. ([ISO][8])
- Política de proteção de template aderente à ISO/IEC 24745. ([ISO][11])
- Políticas LGPD (base legal, RoPA, RIPD/DPIA, retenção e descarte). ([Mattos Filho][14])
- Certificados ISO 27001/27701, SOC 2 e (se aplicável) evidências PCI DSS v4.0.1. ([Middlebury][30])
- Arquitetura detalhada, SLOs e runbooks (incidentes e privacidade).

---

## Observações finais de arquitetura

- Prefira **interoperabilidade por padrão** (CBEFF/BioAPI + ISO/IEC 19794/39794), **proteção forte de templates** (ISO/IEC 24745) e **qualidade de amostra automatizada** (NFIQ 2/ISO 29794‑5) para reduzir FRR e recapturas. ([FIDO Alliance][9], [W3C][10], [ISO][11], [NIST][6])
- Para **autenticação de usuário final**, avalie **FIDO2/WebAuthn** (quando aplicável) para tirar o servidor da posse de biometria do usuário, mantendo‑a no hardware seguro do dispositivo. ([OWASP Mobile Application Security][23])
- Trate criptografia e chaves como **sistemas críticos** (FIPS 140‑3/HSM/KMS), e adote **ASVS/API Top 10** em APIs/SDKs. ([NIST Computer Security Resource Center][12], [GitHub][18], [OWASP][19])
- Exija **SBOM** (SPDX/CycloneDX) e maturidade **SLSA** para reduzir risco de supply chain. ([SPDX][26], [cyclonedx.org][27], [SLSA][28])

Se quiser, adapto o check‑list a um RFP/RFI e crio uma planilha de pontuação ponderada por critério (acurácia, PAD, LGPD, interoperabilidade, TCO) para seleção objetiva de fornecedores.

[1]: https://www.iso.org/standard/81005.html?utm_source=chatgpt.com 'ISO/IEC 29794-5:2025 - Face image data'
[2]: https://pages.nist.gov/frvt/html/frvt11.html?utm_source=chatgpt.com 'Face Recognition Technology Evaluation (FRTE) 1:1 Verification'
[3]: https://nvlpubs.nist.gov/nistpubs/ir/2022/NIST.IR.8439.pdf?utm_source=chatgpt.com 'Ongoing Face Recognition Vendor Test (FRVT)'
[4]: https://www.nist.gov/itl/iad/btg/minutiae-interoperability-exchange-minex-iii?utm_source=chatgpt.com 'Minutiae Interoperability Exchange (MINEX) III'
[5]: https://www.iso.org/standard/50867.html?utm_source=chatgpt.com 'ISO/IEC 19794-5:2011 - Biometric data interchange formats'
[6]: https://www.nist.gov/services-resources/software/nfiq-2?utm_source=chatgpt.com 'NFIQ 2 | NIST'
[7]: https://www.nec.com/en/press/202504/global_20250409_01.html?utm_source=chatgpt.com 'NEC Face Recognition Ranks First in NIST Accuracy Testing'
[8]: https://www.iso.org/standard/50864.html?utm_source=chatgpt.com 'ISO/IEC 19794-2:2011 - Biometric data interchange formats'
[9]: https://fidoalliance.org/specs/fido-v2.2-rd-20230321/fido-client-to-authenticator-protocol-v2.2-rd-20230321.html?utm_source=chatgpt.com 'Client to Authenticator Protocol (CTAP)'
[10]: https://www.w3.org/TR/webauthn-2/?utm_source=chatgpt.com 'Web Authentication: An API for accessing Public Key ...'
[11]: https://www.iso.org/obp/ui/es/?utm_source=chatgpt.com 'ISO/IEC 29794-5:2025(en), Information technology'
[12]: https://csrc.nist.gov/pubs/fips/140-3/final?utm_source=chatgpt.com 'FIPS 140-3, Security Requirements for Cryptographic Modules'
[13]: https://www2.camara.leg.br/legin/fed/lei/2018/lei-13709-14-agosto-2018-787077-publicacaooriginal-156212-pl.html?utm_source=chatgpt.com 'LEI Nº 13.709, DE 14 DE AGOSTO DE 2018 - Publicação Original'
[14]: https://www.mattosfilho.com.br/wp-content/uploads/2025/01/mattos-filho-guia-pratico-para-agentes-de-tratamento.pdf?utm_source=chatgpt.com '[PDF] Guia prático para agentes de tratamento | Mattos Filho'
[15]: https://www.dataprivacybr.org/a-tomada-de-subsidios-sobre-dados-biometricos-da-anpd-um-guia-para-participacao-social-efetiva/?utm_source=chatgpt.com 'A tomada de subsídios sobre dados biométricos da ANPD'
[16]: https://www.bsa.org/files/policy-filings/20250625bsacommentsanpdbiometrics_pt.pdf?utm_source=chatgpt.com '[PDF] Consulta da ANPD sobre Dados Biométricos'
[17]: https://templates.machinezoo.com/iso-19794-1-2011?utm_source=chatgpt.com 'ISO/IEC 19794-1:2011 Summary - Fingerprint template formats'
[18]: https://raw.githubusercontent.com/OWASP/ASVS/v4.0.3/4.0/OWASP%20Application%20Security%20Verification%20Standard%204.0.3-en.pdf?utm_source=chatgpt.com 'Application Security Verification Standard 4.0.3 - GitHub'
[19]: https://owasp.org/API-Security/editions/2023/en/0x11-t10/?utm_source=chatgpt.com 'OWASP Top 10 API Security Risks – 2023'
[20]: https://support.apple.com/guide/security/secure-enclave-sec59b0b31ff/web?utm_source=chatgpt.com 'Secure Enclave'
[21]: https://developer.android.com/privacy-and-security/keystore?utm_source=chatgpt.com 'Android Keystore system | Security'
[22]: https://source.android.com/docs/security/best-practices/hardware?utm_source=chatgpt.com 'Hardware security best practices'
[23]: https://mas.owasp.org/MASVS/?utm_source=chatgpt.com 'OWASP MASVS - OWASP Mobile Application Security'
[24]: https://www.iso.org/standard/27001?utm_source=chatgpt.com 'ISO/IEC 27001:2022 - Information security management ...'
[25]: https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-3.pdf?utm_source=chatgpt.com 'Security Requirements for Cryptographic Modules'
[26]: https://spdx.dev/use/specifications/?utm_source=chatgpt.com 'Specifications'
[27]: https://cyclonedx.org/?utm_source=chatgpt.com 'CycloneDX Bill of Materials Standard | CycloneDX'
[28]: https://slsa.dev/?utm_source=chatgpt.com 'SLSA • Supply-chain Levels for Software Artifacts'
[29]: https://www.gov.br/saude/pt-br/acesso-a-informacao/lgpd?utm_source=chatgpt.com 'Lei Geral de Proteção de Dados Pessoais (LGPD) - Portal Gov.br'
[30]: https://www.middlebury.edu/sites/default/files/2025-01/PCI-DSS-v4_0_1.pdf?utm_source=chatgpt.com 'Payment Card Industry Data Security Standard'

Crie um novo topico para o checklis com o tema custos e sustentação. Incluindo questões relevantes para analise de custos de cloud e infraestrutura, suporte a clientes, sustentação. As questões devem ser organizadas por temas, segmentas e detalhadas.

Perfeito. Vou criar um novo tópico **Custos e Sustentação** para integrar ao checklist de due diligence, com foco em custos de nuvem/infrastrutura, suporte ao cliente e sustentação da operação. Estruturei por subtemas:

---

## **Custos e Sustentação**

### 1. Custos de Cloud e Infraestrutura

- Qual é o **modelo de precificação** (pay-as-you-go, licenciamento por usuário, por transação, ou assinatura fixa)?
- Há **estimativa clara de TCO** considerando licenças, storage, rede, instâncias de processamento, balanceadores e tráfego de dados?
- Existem **ferramentas nativas de otimização** (auto-scaling, reserved instances, spot instances) para reduzir custos?
- Como é feito o **capacity planning**? Existe simulação de picos (por exemplo, campanhas de onboarding massivo)?
- Quais são os custos de **alta disponibilidade e disaster recovery** (multi-AZ, multi-region)?
- Como é tratada a **custos de compliance** (auditorias, certificações ISO/IEC 27001/27701, PCI DSS, LGPD)?

### 2. Custos de Sustentação Técnica

- Qual o custo de **suporte técnico (N1, N2, N3)**? Está incluso ou é cobrado separadamente?
- Há **SLA de resposta e resolução** com penalidades financeiras em caso de não cumprimento?
- Custos de **manutenção evolutiva** (novos releases, upgrades de SDKs, suporte a novos OS/device)?
- Custos de **customização/integradores**: há previsão ou só suporte “as is”?
- Qual o custo de **testes de carga e certificação periódica** exigidos por normativos (ex.: bancos centrais, CNJ)?

### 3. Sustentação de Operações

- Existe **NOC/SOC 24x7**? Qual o custo associado?
- Há **monitoramento proativo** de disponibilidade e qualidade biométrica (NFIQ, PAD metrics)?
- Quais os custos e responsabilidades de **infra observability** (logs, métricas, APM, storage de logs)?
- Plano de **ciclo de vida de produto**: qual o tempo médio de suporte por versão de SDK/API? Há custos para extensão de suporte?
- Quais os custos de **resiliência operacional**: exercícios de disaster recovery, simulações de falha, “chaos testing”?

### 4. Suporte a Clientes Finais (UX e Sustentação de Negócio)

- Existe **canal 24x7 de suporte a clientes finais** (chatbot, telefone, e-mail)? Está incluso no contrato?
- Como são tratados **picos de chamados**? Há custos adicionais por volume?
- Existe **portal de autoatendimento e documentação pública** (APIs, SDKs, guias de integração)?
- Há custos extras para **treinamentos, workshops e capacitação** de equipes técnicas e de negócio?
- Existe modelo de **co-suporte** com o cliente (treinamento de times internos para reduzir dependência do fornecedor)?

---

Deseja que eu **adicione esse novo tópico na planilha de RFP** já criada, criando também pesos específicos para “Custos e Sustentação” (ex.: 20%) para equilibrar a análise com os outros critérios?

crie um novo topico abordando os times e custos de desenvolvimento, detalhando tamanho de equipes, qualificações dos times, cargos de maior relevancia, uso de times terceirizados.

Ótimo ponto. A análise de **times e custos de desenvolvimento** é crítica em uma due diligence técnica, pois impacta diretamente a qualidade, velocidade de evolução e sustentabilidade da solução biométrica. Segue o novo tópico segmentado:

---

## **Times e Custos de Desenvolvimento**

### 1. Estrutura e Tamanho da Equipe

- Qual é o **tamanho total da equipe de desenvolvimento** dedicada à solução biométrica?
- O time é **exclusivo** para este produto ou compartilha esforços com outros projetos?
- Existe **proporção entre desenvolvedores, QA, DevOps e especialistas em IA/biometria**?
- Qual a **rotatividade histórica** (turnover) da equipe?

### 2. Qualificações e Competências

- Quais são as **habilidades essenciais presentes** na equipe (Machine Learning, IA, segurança, DevOps, mobile SDKs)?
- Os desenvolvedores e arquitetos possuem **certificações relevantes** (ex.: AWS/GCP/Azure, CISSP, ISO 27001, FIDO Certified)?
- Existem **pesquisadores ou cientistas de dados especializados em biometria** (com experiência em NIST, ISO/IEC, papers científicos)?
- Há práticas de **treinamento contínuo** em segurança, privacidade e padrões biométricos internacionais?

### 3. Cargos de Maior Relevância

- **Arquiteto de Solução/Enterprise Architect**: responsável por garantir padrões e escalabilidade.
- **Líder de Segurança (CISO/Head de Security by Design)**: responsável pela privacidade, LGPD, ISO 27001.
- **Engenheiro de Machine Learning / Biometrics Scientist**: especializado em redes neurais e modelos biométricos.
- **Product Owner/Manager**: garante alinhamento com roadmap regulatório e de mercado.
- **DevOps/SRE**: responsável por automação, disponibilidade e observabilidade da plataforma.
- **QA Automation**: garante testes de regressão, PAD e performance sob carga.

### 4. Times Terceirizados e Parcerias

- Existem **equipes terceirizadas ou consultorias** no desenvolvimento/manutenção?
- Quais áreas são terceirizadas (ex.: backend, mobile, liveness, infraestrutura cloud)?
- Como é feito o **controle de qualidade e auditoria** sobre fornecedores terceiros?
- Existe contrato de **NDA e compliance de segurança** para terceiros?
- Há riscos de **vendor lock-in** ou dependência crítica de times externos?

### 5. Custos de Desenvolvimento e Sustentação do Time

- Qual o **custo médio mensal** da equipe (payroll + benefícios + terceirizados)?
- Existe um **modelo de rate card** (junior, pleno, sênior, especialista) para transparência de custos?
- Quais são os **custos de P\&D** (pesquisa e desenvolvimento) associados à biometria?
- Há custos extras para **atualizações críticas**, adequação regulatória (ex.: LGPD, ANPD, PCI DSS, NIST)?
- Como são tratados os **custos de escalabilidade do time** (ex.: onboarding rápido de mais desenvolvedores em picos)?

---

Você deseja que eu **adicione esse novo tópico também na planilha de RFP/RFI** junto ao de **Custos e Sustentação**, criando pesos de avaliação (ex.: 15% para Times e Custos de Desenvolvimento)?

Apresente uma explicação detalhada de um dos topicos e subtopicos abordados, descrevendo as melhores recomendação para o um cenário ideal. Apresente uma explicação e descrição de todas as siglas utilizadas. A explicação deve estar associada a cada uma dos tópicos e deve vir abaixo dos mesmo.

A seguir aprofundo o tópico **2) Biométrico e ciência de dados (precisão, viés e robustez)**. Para cada subitem, trago: explicação, recomendações para um cenário ideal e a lista de siglas usadas (com definições).

# 2) Biométrico e ciência de dados (precisão, viés e robustez)

## • Modalidades: face, dedo, íris, voz, palma, comportamento. Há “fusion” entre modalidades?

**Explicação e recomendações (cenário ideal)**
Selecione a(s) modalidade(s) em função do risco e do contexto de uso (onboarding remoto, acesso físico, autenticação contínua). Defina claramente se haverá **fusão** de sinais (ex.: face + PAD + geolocalização ou dedo + PAD) e em que camada: “early fusion” (no sensor/feature) ou “late fusion” (no score/decisão). Em soluções nacionais de grande escala, a estratégia típica é operar com uma modalidade principal (ex.: face) e manter uma modalidade auxiliar para “step-up” quando o risco sobe (ex.: digitais/íris), calibrando pesos de fusão por cenário. Registre as políticas de fallback e recaptura para garantir UX e disponibilidade.

**Siglas**

- **Fusion**: combinação de sinais biométricos (ou sinais contextuais) para melhorar precisão e robustez.

---

## • Acurácia: apresentar FAR/FRR/EER, curvas ROC/DET e análise por subgrupos demográficos. Teste conforme ISO/IEC 19795-1.

**Explicação e recomendações (cenário ideal)**
Exija avaliação independente e relatórios com **FAR** (taxa de falsas aceitações), **FRR** (taxa de falsas rejeições) e **EER** (ponto em que FAR=FRR), além de curvas **ROC/DET** para cada caso de uso e ambiente (iluminação, câmera, indoor/outdoor). Padronize os ensaios segundo **ISO/IEC 19795-1** (métricas, desenho experimental e reporte), e **sempre** inclua recortes por demografia para detectar variações de desempenho entre grupos (sexo, idade, raça/fototipo), conforme práticas evidenciadas por relatórios do **NIST FRVT**. Defina “operating points” (limiares de score) específicos a cada risco (verificação 1:1 x identificação 1\:N), com SLOs de latência/throughput. ([ISO][1], [NIST][2], [NIST Publications][3], [NIST Pages][4], [Wikipedia][5], [myslu.stlawu.edu][6])

**Siglas**

- **FAR** (_False Accept Rate_): probabilidade de aceitar um impostor.
- **FRR** (_False Reject Rate_): probabilidade de rejeitar um legítimo.
- **EER** (_Equal Error Rate_): ponto em que FAR=FRR (indicador de equilíbrio de erros).
- **ROC** (_Receiver Operating Characteristic_): curva TPR vs. FPR em diferentes limiares.
- **DET** (_Detection Error Tradeoff_): curva de trade-off de erros em escala normal/gaussiana.
- **ISO/IEC 19795-1**: norma de ensaios de desempenho biométrico (métricas, métodos, reporte).
- **NIST FRVT** (_Face Recognition Vendor Test_): série de avaliações independentes de algoritmos de face conduzidas pelo NIST (inclui relatórios de demografia).

---

## • Resultados independentes: algoritmo em NIST FRVT (face) e MINEX III (digitais)?

**Explicação e recomendações (cenário ideal)**
Requisite IDs e datas de submissão do fornecedor aos programas do **NIST** relevantes à modalidade: **FRVT** (face) e **MINEX III** (conformidade e interoperabilidade de templates de digitais). Use esses resultados para comparar precisão, estabilidade e interoperabilidade contra o estado-da-arte; priorize fornecedores com track-record consistente ao longo do tempo e com submits recentes. ([NIST Publications][3], [NIST][7])

**Siglas**

- **NIST** (_National Institute of Standards and Technology_): instituto norte-americano que conduz avaliações independentes.
- **FRVT** (_Face Recognition Vendor Test_): avaliação independente de algoritmos de reconhecimento facial.
- **MINEX III** (_Minutiae Interoperability Exchange_): avaliação de conformidade/interoperabilidade de templates de digitais (INCITS 378) para PIV.

---

## • Liveness/PAD: técnicas e resultados contra “presentation attacks”. Métricas APCER/BPCER. Aderência a ISO/IEC 30107-3.

**Explicação e recomendações (cenário ideal)**
Implemente **PAD** (detecção de ataque de apresentação) com cobertura para ataques digitais (foto/vídeo em tela) e físicos (máscaras, moldes, impressões 3D). Exija ensaios em laboratório conforme **ISO/IEC 30107-3**, com reporte padronizado das taxas **APCER** (erros ao classificar ataques como autênticos) e **BPCER** (erros ao classificar autênticos como ataques). Adote limiares específicos por canal (mobile/web/quiosque) e monitore em produção as distribuições dessas métricas. Planeje atualização contínua de modelos e listas de artefatos (novos vetores de ataque). ([ISO][8])

**Siglas**

- **PAD** (_Presentation Attack Detection_): detecção de ataques no ponto de captura.
- **APCER** (_Attack Presentation Classification Error Rate_): taxa de erro ao aceitar um ataque.
- **BPCER** (_Bona Fide Presentation Classification Error Rate_): taxa de erro ao rejeitar um legítimo.
- **ISO/IEC 30107-3**: norma para ensaios e reporte de PAD.

---

## • Qualidade de amostra: NFIQ 2.0 (digitais); ISO/IEC 29794-5 (face). Thresholds e recaptura automática.

**Explicação e recomendações (cenário ideal)**
Garanta que a qualidade da amostra seja medida **na borda** (SDK/sensor) e **no servidor**. Para digitais, use **NFIQ 2** para pontuar qualidade e acionar recaptura automaticamente quando abaixo do limiar. Para face, adote critérios de **ISO/IEC 29794-5** (enquadramento, foco, iluminação, pose) e implemente feedback em tempo real ao usuário (ex.: “aproxime”, “centralize”, “mais luz”). Mantenha dashboards de qualidade por canal, dispositivo e localização para guiar ações de UX e treinamento. ([NIST][9], [NIST Publications][10], [ISO][11], [eab.org][12])

**Siglas**

- **NFIQ 2** (_NIST Fingerprint Image Quality_): software/métrica do NIST para qualificar imagens de digitais.
- **ISO/IEC 29794-5**: norma de qualidade de imagem de face (critérios e métricas).

---

## • Drift/aging: monitoramento de degradação ao longo do tempo; estratégia de re-enrolment e retreino.

**Explicação e recomendações (cenário ideal)**
Implemente monitoramento contínuo de **drift** (mudança de dados/ambiente) e de **aging** (mudanças naturais no rosto/traço). Acompanhe EER/FAR/FRR por coortes de tempo e dispare **re-enrolment** (nova captura) quando a qualidade ou o score médio cair sob limiares definidos. Em modelos próprios, mantenha **pipelines de retreino** com validação cega e “shadow mode” antes de promover modelos. (A avaliação demográfica dos FRVT evidencia variações que também podem emergir ao longo do tempo; trate-as como requisito de monitoramento.) ([NIST Publications][3], [NIST Pages][4])

**Siglas**

- **Drift**: mudança estatística nos dados (de entrada, distribuição de cenários, dispositivos).
- **Aging**: deterioração de desempenho por mudanças biométricas naturais ao longo do tempo.
- **Re-enrolment**: recaptura e substituição/atualização do template do usuário.
- **Shadow mode**: executar um novo modelo em paralelo, sem afetar decisões, para medir impacto real.

---

## • Explainability/observabilidade de modelo: atributos/qualidade que influenciam a decisão. “Score calibration” e faixas operacionais por cenário.

**Explicação e recomendações (cenário ideal)**
Mantenha **observabilidade de ML**: distribuição de scores por canal/dispositivo, correlação com métricas de qualidade (NFIQ/29794-5), curvas ROC por segmento e “**score calibration**” (probabilidade calibrada de correspondência) para permitir limiares diferentes por caso de uso. Registre “features de qualidade” usadas na decisão e audite periodicamente a ausência de vieses inesperados. Conecte essas métricas a SLOs (ex.: P95 de latência por tipo de câmera; EER máximo por processo), e use “canary/feature-flags” para promover novos limiares/modelos com rollback rápido. ([Wikipedia][5], [myslu.stlawu.edu][6])

**Siglas**

- **Observabilidade de ML**: telemetria que permite entender saúde e comportamento do modelo em produção.
- **Score calibration**: transformação do score do matcher em probabilidade calibrada (melhorando decisões por limiar).
- **SLO** (_Service Level Objective_): meta mensurável de serviço (p.ex., latência/precisão).
- **Canary**: liberação gradual de mudanças para parcela dos usuários, mitigando risco.

---

## Cenário ideal (resumo executivo para RFP/RFI)

1. **Relatórios de desempenho** padronizados por **ISO/IEC 19795-1**, com curvas **ROC/DET** e métricas por demografia; “operating points” por fluxo (1:1, 1\:N). ([ISO][1], [NIST][2], [Wikipedia][5], [myslu.stlawu.edu][6])
2. **Evidências independentes**: IDs/datas em **NIST FRVT** (face) e **MINEX III** (digitais). ([NIST Publications][3], [NIST][7])
3. **PAD** testado por **ISO/IEC 30107-3**, com **APCER/BPCER** e plano de atualização contra novos vetores de ataque. ([ISO][8])
4. **Qualidade de amostra** operacionalizada (feedback em tempo real) com **NFIQ 2** (digitais) e **ISO/IEC 29794-5** (face), incluindo recaptura automática. ([NIST][9], [NIST Publications][10], [ISO][11])
5. **Monitoramento contínuo** de drift/aging, com **re-enrolment** governado por critérios objetivos e **pipelines de retreino** sob “shadow mode”. ([NIST Pages][4])

Se quiser, aplico este detalhamento à sua planilha RFP/RFI (criando colunas de evidências exigidas por subitem e critérios mínimos para “go/no-go”).

[1]: https://www.iso.org/standard/73515.html?utm_source=chatgpt.com 'ISO/IEC 19795-1:2021 - Information technology'
[2]: https://www.nist.gov/document/3401mansfieldpanelibpcpdf?utm_source=chatgpt.com 'ISO/IEC 19795 Biometric Performance Testing and Reporting'
[3]: https://nvlpubs.nist.gov/nistpubs/ir/2019/nist.ir.8280.pdf?utm_source=chatgpt.com 'Face Recognition Vendor Test (FRVT), Part 3: Demographic ...'
[4]: https://pages.nist.gov/frvt/reports/demographics/nistir_8429.pdf?utm_source=chatgpt.com 'Face Recognition Vendor Test (FRVT) Part 8 - NIST Pages'
[5]: https://en.wikipedia.org/wiki/Receiver_operating_characteristic?utm_source=chatgpt.com 'Receiver operating characteristic'
[6]: https://myslu.stlawu.edu/~msch/biometrics/papers/adler-schuckers-2005-Composite-DETfinal.pdf?utm_source=chatgpt.com 'Calculation of a Composite DET Curve'
[7]: https://www.nist.gov/programs-projects/minutiae-interoperability-exchange-minex-overview?utm_source=chatgpt.com 'Minutiae Interoperability Exchange (MINEX) Overview | NIST'
[8]: https://www.iso.org/standard/79520.html?utm_source=chatgpt.com 'ISO/IEC 30107-3:2023 - Information technology'
[9]: https://www.nist.gov/services-resources/software/nfiq-2?utm_source=chatgpt.com 'NFIQ 2 | NIST'
[10]: https://nvlpubs.nist.gov/nistpubs/ir/2021/NIST.IR.8382.pdf?utm_source=chatgpt.com 'NFIQ 2 NIST Fingerprint Image Quality'
[11]: https://www.iso.org/standard/81005.html?utm_source=chatgpt.com 'ISO/IEC 29794-5:2025 - Face image data'
[12]: https://eab.org/files/events/2021-11-16-18_Face_Image_Quality/20211118_05_grother_NIST-Quality-Evaluations.pdf?ts=1755216000083&utm_source=chatgpt.com 'NIST Quality Evaluations ISO/IEC 29794-5'
