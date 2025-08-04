
# mdc-powershell-handbook

Guia completo do Microsoft Defender for Cloud via PowerShell CLI.  
Documentação profissional, exemplos práticos, automação, compliance e troubleshooting para especialistas de Azure Security.

---

## Índice

- [Por que usar CLI e não só o portal?](#por-que-usar-cli-e-não-só-o-portal)
- [Instalação e pré-requisitos](#instalação-e-pré-requisitos)
- [Listando todos os comandos disponíveis](#listando-todos-os-comandos-disponíveis)
- [Tabela de comandos principais](#tabela-de-comandos-principais)
- [Exemplos práticos](#exemplos-práticos)
- [Boas práticas e troubleshooting](#boas-práticas-e-troubleshooting)
- [Referências oficiais](#referências-oficiais)

---

## Por que usar CLI e não só o portal?

- **Automação:** Execute tarefas massivas e repetíveis em escala, sem limitação de interface.
- **Governança:** Exporte históricos, gere provas de compliance e audite configurações.
- **Resposta a incidentes:** Implemente resposta automatizada (SOAR/SIEM), coleta de provas e inventários.
- **Velocidade:** Monte scripts e pipelines imunes a erro humano.
- **Ambientes restritos:** CLI funciona em ambientes onde o portal pode estar bloqueado.

> **Resumo:** CLI é o superpoder do especialista em Defender for Cloud.

---

## Instalação e pré-requisitos

```powershell
# Requer PowerShell 7.x ou superior
Install-Module Az.Security -Scope CurrentUser -Force

# Autentique-se no Azure
Connect-AzAccount

# Selecione a subscription (caso tenha mais de uma)
Set-AzContext -Subscription "<ID-ou-Nome-da-Subscription>"
```

---

## Listando todos os comandos disponíveis

Sempre descubra todos os recursos do módulo (recomendo rodar após cada update):

```powershell
Get-Command -Module Az.Security
```

**Exemplo de saída real:**

```
CommandType Name                                        Version    Source
----------- ----                                        -------    ------
Alias       Get-AzRegulatoryComplianceAssessment         1.8.0      Az.Security
Alias       Set-AzSecurityAutomation                    1.8.0      Az.Security
Function    Get-AzSecurityApiCollection                 1.8.0      Az.Security
Function    Get-AzSecurityConnector                     1.8.0      Az.Security
...
Cmdlet      Get-AzSecurityAlert                         1.8.0      Az.Security
Cmdlet      Set-AzSecurityAlert                         1.8.0      Az.Security
Cmdlet      Get-AzSecurityAssessment                    1.8.0      Az.Security
...
```

> 💡 **Dica:**  
> Use `Get-Command -Module Az.Security | Where-Object Name -like '*Alert*'` para filtrar comandos de interesse.

### Exemplo visual do comando rodando:

![Saída do Get-Command -Module Az.Security](./images/az-security-get-command-example.png)

---

## Tabela de comandos principais

| Cmdlet                                   | O que faz                                                    | Quando usar                                   | Exemplo rápido                                         |
|-------------------------------------------|--------------------------------------------------------------|-----------------------------------------------|--------------------------------------------------------|
| `Get-AzSecurityAlert`                     | Lista alertas do Defender for Cloud                          | Monitorar incidentes, resposta automatizada   | `Get-AzSecurityAlert -Location brazilsouth`            |
| `Set-AzSecurityAlert`                     | Atualiza status de um alerta                                 | Encerrar/tratar incidentes via script         | `Set-AzSecurityAlert -Status Resolved -Name <alert>`   |
| `Get-AzSecurityAssessment`                | Consulta avaliações de postura de segurança                  | Compliance, benchmarks, auditoria             | `Get-AzSecurityAssessment`                             |
| `Get-AzSecuritySubAssessment`             | Detalha findings de assessments                              | Auditoria detalhada, gap analysis             | `Get-AzSecuritySubAssessment -AssessmentName <id>`     |
| `Get-AzRegulatoryComplianceStandard`      | Lista padrões regulatórios CIS, PCI, etc.                    | Compliance, auditoria                         | `Get-AzRegulatoryComplianceStandard`                   |
| `Get-AzRegulatoryComplianceControl`       | Lista controles de compliance de cada padrão                  | Auditoria                                     | `Get-AzRegulatoryComplianceControl -StandardName <x>`  |
| `Get-AzRegulatoryComplianceAssessment`    | Detalha avaliações de controles                              | Auditoria fina, provas para órgãos externos   | `Get-AzRegulatoryComplianceAssessment -ControlName <x>`|
| `Get-AzSecurityAutomation`                | Lista automações configuradas (SOAR, workflows)               | Resposta automática a alertas                 | `Get-AzSecurityAutomation`                             |
| `New-AzSecurityAutomation`                | Cria automações customizadas                                 | Automatizar resposta, integração SIEM/SOAR    | `New-AzSecurityAutomation ...`                         |
| `Get-AzSecurityPricing`                   | Mostra planos Defender ativos                                | Auditoria de custos e cobertura               | `Get-AzSecurityPricing`                                |
| `Set-AzSecurityPricing`                   | Ativa/ajusta planos Defender em recursos                     | Onboarding, otimização de custos/licenças     | `Set-AzSecurityPricing -Name StorageAccounts -Tier Standard` |
| `Get-AzSecuritySecureScore`               | Mostra o Secure Score do ambiente                            | Medir evolução da segurança                   | `Get-AzSecuritySecureScore`                            |
| `Get-AzSecuritySecureScoreControl`        | Detalha os controles do Secure Score                         | Compliance, controles específicos             | `Get-AzSecuritySecureScoreControl`                     |
| ...                                       | ...                                                          | ...                                           | ...                                                    |

> *Consulte a lista completa sempre com `Get-Command -Module Az.Security`.*

---

## Exemplos práticos

### 1. Listar todos os alertas de segurança ativos na subscription:
```powershell
Get-AzSecurityAlert
```

### 2. Exportar avaliações de compliance para CSV:
```powershell
Get-AzSecurityAssessment | Export-Csv -Path C:	emp\defender_assessments.csv -NoTypeInformation
```

### 3. Habilitar Defender para Storage Accounts:
```powershell
Set-AzSecurityPricing -Name StorageAccounts -Tier Standard
```

### 4. Obter Secure Score e controles:
```powershell
Get-AzSecuritySecureScore
Get-AzSecuritySecureScoreControl
```

---

## Boas práticas e troubleshooting

- **Use sempre `Connect-AzAccount` e selecione a subscription correta.**
- **Automatize relatórios e inventários periódicos.**
- **Filtre comandos por área de interesse (`Get-Command | Where-Object ...`).**
- **Combine scripts PowerShell com Azure DevOps, GitHub Actions ou Logic Apps.**
- **Use o CLI para exportar provas de compliance e auditar configurações.**
- **Sempre rode `Get-Help <NomeDoComando> -Full` para ver todos os exemplos de uso e parâmetros.**

---

## Referências oficiais

- [Documentação Az.Security (Microsoft Docs)](https://learn.microsoft.com/powershell/module/az.security/)
- [Defender for Cloud Docs](https://learn.microsoft.com/azure/defender-for-cloud/)
- [CLI reference — Azure Docs](https://learn.microsoft.com/cli/azure/)

---

> Documentação mantida por [SeuNome/GitHub].  
> Colabore, envie PRs, compartilhe automações reais!
