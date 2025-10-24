# Projeto Final DevSecOps - Entregas de Gestão

**Disciplina:** DevSecOps  
**Curso:** Especialização em Liderança Técnica  
**Empresa:** Juice Shop  
**Equipe:** Luiz Massa, Iago Lima, Lucas Cristiano Dantas e Eric Neder  
**Repositório Técnico:** [github.com/cesar-tech-leader-devsecops/juice-shop](https://github.com/cesar-tech-leader-devsecops/juice-shop)

## 1. Justificativa para Adoção de DevSecOps

A transformação digital traz consigo uma nova dinâmica de desenvolvimento de software: ciclos curtos de entrega, uso intensivo de automação e grande dependência de bibliotecas externas. Com isso, a segurança tornou-se um requisito essencial de qualidade e continuidade do negócio e não apenas uma etapa final de auditoria.

A adoção do modelo DevSecOps permite integrar segurança desde o início do ciclo de desenvolvimento (“Shift Left Security”), tornando-a parte da cultura e dos processos da organização. Essa abordagem reduz falhas, custos e riscos reputacionais, ao mesmo tempo em que mantém a agilidade e a eficiência operacional.

### 1.1. Problemas identificados

* Vazamento de dados sensíveis e dependência de bibliotecas vulneráveis.
* Ausência de práticas formais de segurança e monitoramento contínuo.
* Falta de integração entre as equipes de desenvolvimento, operações e segurança.
* Correção de vulnerabilidades apenas em fases tardias, elevando o custo de mitigação.

### 1.2. Benefícios esperados

* Automação da segurança no pipeline CI/CD.
* Redução de riscos e menor tempo de resposta a vulnerabilidades.
* Aumento da maturidade organizacional em segurança.
* Conformidade com normas de proteção de dados (ex: LGPD, GDPR).
* Melhoria na reputação da empresa e confiança dos usuários.

### 1.3. Fundamentação técnica

O pipeline DevSecOps do projeto integra práticas de segurança automatizadas, conforme boas práticas OWASP:

| Etapa | Descrição | Ferramenta |
| :--- | :--- | :--- |
| Pre-commit hook | Mecanismo de controle automatizado no Git, executado antes do commit das alterações. | Local |
| Secret Scanning | Pipeline responsável por identificar segredos sensíveis expostos no código ou histórico do repositório. | Gitleaks / Trivy |
| SAST | Pipeline responsável pela análise estática do código-fonte (SAST) para identificar vulnerabilidades, codesmells e problemas de qualidade. | SonarQube |
| Dependency Scan | Pipeline responsável por detectar vulnerabilidades em dependências Node.js. A análise é feita diretamente no package.json, sem necessidade de instalação. | NPM audit / Trivy SBOM |

Essas práticas garantem que a segurança seja incorporada desde o commit até a entrega em produção, evitando retrabalho e permitindo auditoria contínua.

## 2. Política de Segurança da Informação (PSI)

A presente política define as diretrizes para garantir que todas as entregas de software sigam padrões mínimos de segurança, rastreabilidade e conformidade dentro da cultura DevSecOps.

### 2.1. Diretrizes gerais

* **Segurança desde o início:** toda feature deve ser analisada quanto a impactos de segurança desde o planejamento.
* **Automação obrigatória:** pipelines CI/CD devem incluir estágios de SAST, SCA e Container Scanning antes de qualquer deploy.
* **Gerenciamento de segredos:** credenciais e tokens não devem ser versionados no código-fonte; devem ser armazenados em cofres seguros (ex: GitHub Secrets, Vault).
* **Infraestrutura como Código (IaC):** toda infraestrutura deve ser descrita e validada com ferramentas de análise de configuração e segurança.
* **Monitoramento contínuo:** logs, alertas e métricas de vulnerabilidade devem ser revisados periodicamente.
* **Atualizações regulares:** dependências e imagens devem ser revisadas semanalmente quanto a CVEs conhecidas.

### 2.2. Papéis e responsabilidades

| Função | Responsabilidades |
| :--- | :--- |
| Líder Técnico / Gestor DevSecOps | Garantir o cumprimento desta política e acompanhar métricas de vulnerabilidade e conformidade. |
| Desenvolvedores | Aplicar práticas seguras de codificação e revisar alertas de SAST/SCA nos pipelines. |
| Equipe de Operações | Validar configurações de infraestrutura, segredos e acessos. |
| Segurança da Informação (CISO ou equivalente) | Definir critérios de aceitação e priorização de riscos, e auditar relatórios periódicos. |

## 3. Metodologia de Avaliação e Mitigação de Riscos

Para cada entrega e release, será aplicada uma matriz de risco baseada nas recomendações OWASP.

### 3.1. Etapas do processo

1. **Identificação:** levantamento de vulnerabilidades detectadas por SAST, SCA, Container Scanning e IaC.
2. **Avaliação:** classificação de cada risco quanto à probabilidade e impacto.
3. **Tratamento:** definição de ações corretivas ou compensatórias.
4. **Aceitação:** validação pela equipe de segurança e líder técnico.
5. **Monitoramento:** acompanhamento contínuo via dashboards e relatórios automatizados.

### 3.2. Escala de classificação

| Nível | Probabilidade | Impacto | Ação |
| :--- | :--- | :--- | :--- |
| Crítico | Alta | Alta | Correção imediata e bloqueio de deploy |
| Alto | Média | Alta | Correção em até 48h |
| Médio | Média | Média | Correção programada em sprint seguinte |
| Baixo | Baixa | Baixo | Monitorar e reavaliar |

## 4. Recomendações e Próximos Passos

A Juice Shop deve adotar uma cultura de segurança colaborativa e transparente, onde todos os times (Dev, Sec e Ops) compartilham a responsabilidade pela segurança do produto.
Com isto, é recomendado a adoção das seguintes açõe:

* Realizar revisões semanais de segurança e retrospectivas ao fim de cada sprint de desenvolvimento com foco em vulnerabilidades.
* Promover treinamentos internos e workshops de boas práticas.
* Definir métricas de maturidade DevSecOps, como:
  * Percentual de builds com sucesso nos scans;
  * Tempo médio para correção de vulnerabilidades;
  * Quantidade de alertas críticos por release.

## 5. Conclusão

A adoção de DevSecOps na Juice Shop representa um investimento estratégico em segurança, qualidade e sustentabilidade do produto. Com a integração das práticas automatizadas e uma política clara de governança, a empresa reduz riscos, aumenta a confiança dos usuários e prepara o terreno para escalar suas soluções com segurança e agilidade.
