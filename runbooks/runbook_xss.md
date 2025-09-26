# 🛠️ Runbook de Resposta a Incidentes: Cross-Site Scripting (XSS)

**Alvo:** Aplicação Web (Node.js)  
**Ameaça:** Ataque de Injeção de Código do Lado do Cliente (XSS)  
**Versão:** 1.01

---

## 🎯 Objetivo e Severidade

- **Objetivo:** Fornecer um guia passo a passo para conter e erradicar uma vulnerabilidade XSS (Stored ou Reflected), minimizando o risco de roubo de cookies de sessão, credenciais ou defacement.
- **Severidade:** **ALTA** — potencial para roubo de sessão de usuários e administradores (account takeover).

### 📈 Métricas

| Métrica | Meta |
|--------|------|
| Tempo de Contenção | < 30 minutos |
| Tempo de Erradicação | < 4 horas |

---

## 🔍 2. Fase de DETECÇÃO & ANÁLISE (NIST)

**Gatilho:** Confirmação de incidente via fontes de monitoramento.

| Passo | Ação | Responsável |
|-------|------|-------------|
| 2.1 | Alerta SIEM — Recebimento do alerta de alta severidade do Wazuh/ELK (ALERTA WAF XSS) | Analista SOC / Ops |
| 2.2 | Validação (WAF) — Confirmar hit da regra XSS (e.g., OWASP CRS 941xxx) no log do WAF/Nginx | Analista SOC |
| 2.3 | Identificar Fonte — Extrair IP de origem e URL/parâmetro que acionaram o alerta | Analista SOC |
| 2.4 | Classificação — Confirmar tipo de XSS: Stored (persistente) ou Reflected (temporário) | Analista SOC |

---

## ⛔ 3. Fase de CONTENÇÃO (NIST)

**Objetivo:** Parar o ataque imediatamente e limitar os danos.

| Passo | Ação | Responsável |
|-------|------|-------------|
| 3.1 | Bloqueio no Perímetro — Bloquear IP malicioso via NACL ou regra customizada no WAF (validar falso positivo) | Ops / DevSec |
| 3.2 | Desativação Temporária — Se XSS Stored: desabilitar funcionalidade afetada (ex: comentários) | Ops / Dev |
| 3.3 | Backup e Snapshot — Criar snapshot do host da aplicação para forense/rollback | Ops |
| 3.4 | Notificação — Comunicar liderança e Dev sobre contenção e início da erradicação | Liderança / SOC |

---

## 🧹 4. Fase de ERRADICAÇÃO & RECUPERAÇÃO (NIST)

### 🔧 Erradicação (Causa Raiz)

| Passo | Ação | Responsável |
|-------|------|-------------|
| 4.1 | Análise de Código — Revisar código da URL/funcionalidade afetada | Dev |
| 4.2 | Correção (Sanitização) — Implementar Output Encoding ou Sanitização de Input | Dev |
| 4.3 | Teste Unitário — Criar teste que invalide a payload XSS original | DevSec |
| 4.4 | Limpeza de Dados (Stored) — Remover código malicioso persistente do PostgreSQL | Dev / DBA |

### 🔄 Recuperação (Retorno ao Normal)

| Passo | Ação | Responsável |
|-------|------|-------------|
| 4.5 | Deploy (Hotfix) — Aplicar correção no ambiente de produção | Ops |
| 4.6 | Validação — Testar acesso de usuário comum e verificar logs do WAF/SIEM | Analista SOC |
| 4.7 | Retorno do Serviço — Reativar funcionalidade desabilitada | Ops |

---

## 📚 5. Pós-Incidente (Lições Aprendidas)

- 🧠 **Reunião Post-Mortem:** Analisar tempo de resposta e eficácia do runbook com times de Ops e Dev
- 🔄 **Atualização de Defesas:** Se o WAF falhou na detecção inicial, atualizar regras do ModSecurity
- 🛡️ **Roadmap:** Priorizar implementação de **Content Security Policy (CSP)** na aplicação para mitigar XSS nativamente

---

> Este runbook deve ser revisado a cada 6 meses ou após qualquer incidente real para garantir sua eficácia e alinhamento com o ambiente atual.