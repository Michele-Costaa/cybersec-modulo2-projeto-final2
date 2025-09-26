# 🛡️ Proposta de Consultoria Blue Team

**Cliente:** LojaZeta  
**Consultor:** Michele Costa  
**Data:** 2025-09-26

---

## 📄 1. Sumário Executivo

A LojaZeta, em fase de crescimento e operando em IaaS com stack Nginx/Node.js/PostgreSQL, enfrenta riscos imediatos devido à baixa visibilidade de logs e à exposição a ataques de nível de aplicação (SQLi e XSS), confirmados por incidentes recentes.

O pequeno time de Ops e o orçamento limitado exigem uma abordagem 80/20, focada em quick wins de alto impacto.

**Proposta:**  
Arquitetura de Defesa em Profundidade baseada em Open Source (ModSecurity/Wazuh) e reforço de Controles de Identidade.

**Ganhos Esperados:**

- ✅ **Contenção Imediata (30 dias):** Bloqueio de 90% dos ataques L7 via WAF  
- 📊 **Visibilidade Centralizada:** Redução do MTTD via SIEM  
- 🔐 **Redução do Risco de Exfiltração:** Isolamento do Banco de Dados + PoLP

### 🔝 Recomendações Prioritárias (80/20)

| Prioridade | Recomendação | Justificativa |
|-----------|--------------|----------------|
| 1 (WAF) | Implementar WAF (ModSecurity CRS) em modo Bloqueio (`SecRuleEngine On`) | Resposta direta aos incidentes SQLi/XSS reportados |
| 2 (IAM) | MFA obrigatório + PoLP nas contas da nuvem e banco de dados | Prevenção contra credenciais fracas ou vazadas |
| 3 (SIEM) | Deploy do Wazuh Agent + configuração do servidor Wazuh/ELK | Centralização de logs e alertas acionáveis |

---

## 🎯 2. Escopo e Metodologia

### 2.1 Escopo

Proposição de arquitetura de defesa e plano operacional de segurança para infraestrutura de E-commerce (Nginx, Node.js, PostgreSQL) em IaaS, com foco em segurança em camadas e visibilidade de eventos.

### 2.2 Metodologia

Baseada no **NIST Cybersecurity Framework (CSF)** e **NIST SP 800-61** para Resposta a Incidentes.

---

## 🧱 3. Arquitetura de Defesa (Camadas)

Diagrama disponível em `diagrama/arquitetura.mmd`.

- **Camada Web:** WAF (ModSecurity/OWASP CRS) contra SQLi e XSS  
- **Camada de Rede:** NACLs e Security Groups isolando o PostgreSQL  
- **Camada Host/Endpoint:** Hardening + Wazuh Agent para FIM/rootkits  
- **Camada Identidade:** MFA obrigatório + PoLP nas contas administrativas

---

## 📡 4. Monitoramento & SIEM

Uso do stack **Wazuh/ELK (Open Source)** para centralização de logs e detecção de intrusão.

### 4.1 Fontes de Log Prioritárias

| Fonte de Log | Prioridade | Use Case |
|--------------|------------|----------|
| WAF (ModSecurity) | ALTA | Regras 94xxx (SQLi, XSS) |
| Aplicação (Node.js) | ALTA | Autenticação e erros suspeitos |
| Sistema Operacional (Linux) | MÉDIA | SSH, Sudo, iptables, FIM |
| Banco de Dados (PostgreSQL) | MÉDIA | Queries de alto volume e comandos DBA |

### 4.2 Correlações e Alertas

- 🔥 **SQLi Cluster:** 5+ eventos WAF do mesmo IP em 60s  
- 🔓 **Brute-Force Sucedido:** Falhas seguidas de sucesso  
- 🛠️ **Violação de FIM:** Alterações em arquivos críticos

### 4.3 KPIs e Métricas

| KPI | Objetivo | Propósito |
|-----|----------|-----------|
| MTTD | < 15 min | Eficácia do SIEM |
| MTTR | < 60 min | Tempo de contenção |
| % Cobertura de Logs | 100% em APP/DB | Monitoramento completo |

---

## 🚨 5. Resposta a Incidentes (NIST IR)

Baseado nas 4 fases do **NIST SP 800-61**:

| Fase | Ação | Foco |
|------|------|------|
| 1. Preparação | Instalar WAF/SIEM + Runbooks | Controles |
| 2. Detecção & Análise | Validação de alertas | SIEM (Wazuh) |
| 3. Contenção/Recuperação | Execução de Runbooks | IP block, hotfix, backup |
| 4. Lições Aprendidas | Post-Mortem | Roadmap/processos |

📁 Runbooks disponíveis:

- `runbooks/runbook_sqli.md`  
- `runbooks/runbook_xss.md`

---

## 🗺️ 6. Roadmap (30/90/180 dias)

| Prazo | Recomendação | Detalhe | Responsável |
|-------|--------------|---------|-------------|
| 30 dias | WAF (ModSecurity) | Modo Bloqueio no Nginx | Ops/Consultoria |
| 30 dias | IAM/PoLP | MFA + políticas ajustadas | Ops |
| 90 dias | SIEM (Wazuh/ELK) | Instalação + alertas | Ops/Consultoria |
| 90 dias | Patch Management | Processo com MTTP de 7 dias | Ops |
| 180 dias | Segmentação + Backup | Isolamento Dev/QA + teste de restauração | Ops |
| 180 dias | Pentest Básico | OWASP ZAP ou serviço externo | Liderança |

---

## ⚠️ 7. Riscos, Custos e Assunções

| Item | Descrição | Mitigação |
|------|-----------|-----------|
| Risco 1 | Falso Positivo (WAF) | Monitoramento + regras OWASP CRS |
| Risco 2 | Custo Infraestrutura | VM otimizada + cold storage |
| Assunção 1 | Time Dedicado | Treinamento e suporte nos primeiros 30 dias |

---

## ✅ 8. Conclusão

A LojaZeta possui a base para construir uma postura defensiva robusta. Ao adotar a arquitetura proposta e priorizar **Visibilidade (SIEM)** e **Contenção Imediata (WAF/IAM)**, a empresa reduzirá drasticamente o risco de incidentes web, protegendo sua reputação e seus dados.

### Próximos Passos

- ✔️ Aprovação do Roadmap de 30/90/180 dias  
- 🚀 Kick-off da implementação do WAF e Wazuh Agent

### Critérios de Sucesso

- 📉 Redução de 95% nos alertas de SQLi/XSS após 60 dias  
- ⏱️ MTTD < 15 min para alertas críticos  
- 💾 Sucesso no teste de restauração de backup (180 dias)

---
