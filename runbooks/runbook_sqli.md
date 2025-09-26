# 🛠️ Runbook de Resposta a Incidentes: SQL Injection (SQLi)

**Alvo:** Banco de Dados (PostgreSQL) via Aplicação Web (Node.js)  
**Ameaça:** Ataque de Injeção de Comandos ao DB  
**Versão:** 1.01

---

## 🎯 Objetivo e Severidade

- **Objetivo:** Fornecer um guia passo a passo para conter e erradicar um ataque de SQL Injection, minimizando o risco de exfiltração de dados (informações de clientes ou credenciais).
- **Severidade:** **CRÍTICA** — risco direto de violação de dados, exigindo resposta imediata.

### 📈 Métricas

| Métrica | Meta |
|--------|------|
| Tempo de Contenção | < 15 minutos |
| Tempo de Erradicação | < 2 horas |

---

## 🔍 2. Fase de DETECÇÃO & ANÁLISE (NIST)

**Gatilho:** Confirmação de incidente via fontes de monitoramento.

| Passo | Ação | Responsável |
|-------|------|-------------|
| 2.1 | Alerta SIEM (Cluster) — Recebimento do alerta de alta severidade do Wazuh/ELK (SQLi Cluster ou WAF Hit 942xxx) | Analista SOC / Ops |
| 2.2 | Validação (WAF/Log) — Confirmar tentativa de injeção nos logs do WAF e PostgreSQL | Analista SOC |
| 2.3 | Identificar Escopo — Extrair IP de origem, payload SQL e conta de serviço afetada | Analista SOC |
| 2.4 | Classificação — Confirmar se houve exfiltração de dados (DATA LOSS) e comunicar jurídico | Liderança / SOC |

---

## ⛔ 3. Fase de CONTENÇÃO (NIST)

**Objetivo:** Bloquear acesso ao banco e proteger os dados restantes.

| Passo | Ação | Responsável |
|-------|------|-------------|
| 3.1 | Bloqueio Imediato (IP) — Bloquear IP malicioso no NACL e WAF | Ops / DevSec |
| 3.2 | Contenção no Host DB — Bloquear porta 5432 no SG, permitindo acesso apenas da App | Ops |
| 3.3 | Rotacionar Credenciais — Alterar senha da conta Node.js usada no PostgreSQL | Dev / DBA |
| 3.4 | Notificação — Comunicar liderança e Dev sobre contenção e risco de exfiltração | Liderança / SOC |

---

## 🧹 4. Fase de ERRADICAÇÃO & RECUPERAÇÃO (NIST)

### 🔧 Erradicação (Causa Raiz)

| Passo | Ação | Responsável |
|-------|------|-------------|
| 4.1 | Análise de Código — Revisar código Node.js da funcionalidade explorada | Dev |
| 4.2 | Correção — Substituir concatenação por Prepared Statements | Dev |
| 4.3 | Teste da Correção — Criar teste que valide tratamento seguro da payload SQLi | DevSec |

### 🔄 Recuperação (Retorno ao Normal)

| Passo | Ação | Responsável |
|-------|------|-------------|
| 4.4 | Limpeza de Dados — Restaurar DB a partir de backup seguro (se necessário) | DBA |
| 4.5 | Deploy (Hotfix) — Aplicar correção no ambiente de produção | Ops |
| 4.6 | Validação — Testar acesso ao DB e verificar logs do SIEM | Analista SOC |
| 4.7 | Retorno ao Serviço — Remover bloqueio temporário do SG | Ops |

---

## 📚 5. Pós-Incidente (Lições Aprendidas)

- 🧠 **Reunião Post-Mortem Crítica:** Analisar ausência de Prepared Statements no código
- 🔐 **Princípio do Menor Privilégio (DB):** Garantir que a conta Node.js tenha apenas `SELECT` e `INSERT` — nunca `DROP` ou `ALTER`
- 🧪 **Roadmap:** Adicionar scanning periódico de vulnerabilidades (DAST/SAST) como prioridade

---

> Este runbook deve ser revisado a cada 6 meses ou após qualquer incidente real para garantir sua eficácia e alinhamento com o ambiente atual.