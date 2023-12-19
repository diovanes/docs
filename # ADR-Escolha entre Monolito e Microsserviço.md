# ADR: Escolha entre Monolito e Microsserviço para Arquitetura de Projetos

## **Título:**
Escolha entre Monolito e Microsserviço para Arquitetura de Projetos

## **Data:**
12/03/2023

## **Status:**
Proposta

## **Contexto:**

Ao projetar uma solução complexa, o arquiteto de soluções se depara com a decisão fundamental sobre a arquitetura a ser adotada: monolito ou microsserviço. O contexto envolve:

- **Complexidade do Projeto:** Avaliar a complexidade da aplicação em termos de domínio de negócios, escalabilidade e requisitos não funcionais.

- **Equipe e Habilidades:** Considerar a expertise da equipe em lidar com arquiteturas específicas e as habilidades necessárias para implementação e manutenção.

- **Evolução Contínua:** Antecipar a necessidade de evolução constante e implementação de novos recursos.

## **Decisão:**

Diante do contexto, a decisão é baseada em uma árvore de decisão mais abrangente que considera vários fatores-chave. A escolha entre monolito e microsserviço dependerá das características do projeto:

```
1. Complexidade do Projeto
│
├── Simples ou Moderada
│   └── Monolito
│
├── Complexa
│   ├── Equipe Experiente em Microsserviços
│   │   └── Microsserviços
│   ├── Equipe Inexperiente ou Sem Preferência
│   │   ├── Escalabilidade é Crítica
│   │   │   └── Microsserviços
│   │   └── Simplicidade Operacional é Crítica
│   │       └── Monolito
│   └── Avaliar Trade-offs (ver Consequências)
```

### **Exemplos:**
1. **Simples ou Moderada:** Uma aplicação de blog pessoal ou uma loja online simples pode se beneficiar de um monolito para simplicidade.
2. **Complexa:** Um sistema bancário que requer escalabilidade e manuseio de diferentes domínios de negócios pode se beneficiar de microsserviços.
    - **Equipe Experiente em Microsserviços:** Uma equipe experiente pode tirar proveito da flexibilidade e desacoplamento dos microsserviços.
    - **Equipe Inexperiente ou Sem Preferência:**
        - **Escalabilidade é Crítica:** Se a escalabilidade é vital, mesmo com uma equipe inexperiente, os microsserviços podem ser a escolha.
        - **Simplicidade Operacional é Crítica:** Se a simplicidade operacional é mais crucial, um monolito pode ser preferível.

## **Consequências:**

**Monolito:**
1. **Simplicidade Operacional:** Menos complexidade operacional, facilitando o deploy e a manutenção.
2. **Escalabilidade Vertical:** Escalabilidade limitada, exigindo a adição de recursos ao servidor único.

**Microsserviço:**
1. **Desacoplamento:** Alta flexibilidade e desacoplamento entre serviços, permitindo evolução independente.
2. **Complexidade Operacional:** Maior complexidade operacional devido à necessidade de orquestração e gerenciamento de vários serviços.

## **Conclusão:**

A escolha entre monolito e microsserviço depende da complexidade do projeto, da experiência da equipe e das preferências específicas. A árvore de decisão mais abrangente fornece uma estrutura detalhada para orientar a escolha, considerando diferentes cenários e requisitos. Avaliar os trade-offs, considerando as consequências específicas para o contexto do projeto, é crucial para uma decisão informada.

**Recomendações:**
- Avaliar constantemente a evolução do projeto e a adequação da arquitetura escolhida.
- Investir em automação e ferramentas de orquestração ao escolher microsserviços para mitigar complexidades operacionais.
- Manter uma comunicação clara com a equipe e stakeholders durante todo o processo de decisão e implementação.

Esta decisão é uma escolha estratégica que deve ser revisada à medida que o projeto evolui e novos requisitos emergem. A árvore de decisão abrangente fornece uma estrutura detalhada para orientar a escolha entre monolito e microsserviço, permitindo uma arquitetura alinhada aos objetivos do projeto.