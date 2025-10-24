# Trabalho Final - Prática

## Instruções iniciais

Para instruções de como rodar a aplicação, consulte o arquivo [README_OLD.md](README_OLD.md).  
Este documento contém as informações específicas do Trabalho Final.

---

## Grupo e Participantes

- Luiz Massa
- Iago Lima
- Lucas Cristiano Dantas
- Eric Neder

---

## Projeto Escolhido: OWASP Juice Shop

Optamos por utilizar o OWASP Juice Shop como aplicação vulnerável alvo das práticas de DevSecOps.

### Justificativa da escolha

- É um dos projetos oficiais da OWASP mais populares para aprendizado e prática em segurança.
- Contém várias vulnerabilidades conhecidas, que vão de simples até avançadas.
- É uma aplicação moderna (Node.js + Angular), aproximando-se de cenários reais.
- Possui documentação extensa e suporte da comunidade, facilitando a integração com ferramentas de segurança.
- Pode ser facilmente executado em containers via Docker, simplificando o ambiente de testes.

---

## Implementação do Pre-Commit Hook

Como primeiro passo prático do modelo Shift Left Security, configuramos o uso do pre-commit com um conjunto inicial de validações de segurança e qualidade.  
O arquivo de configuração está presente no repositório como:  
`.pre-commit-config.yaml`

---

### Arquivo `.pre-commit-config.yaml`

```yaml
repos:
  # 1. Verifica se há chaves/segredos expostos
  - repo: https://github.com/zricethezav/gitleaks
    rev: v8.18.4
    hooks:
      - id: gitleaks
        name: Detectar segredos sensíveis
        args: ["--verbose"]

  # 2. Checa formatação e padrões de código (JavaScript/Node)
  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v2.7.1
    hooks:
      - id: prettier
        name: Padronizar código JS/TS/JSON

  # 3. Bloqueia commits grandes demais (evita leaks massivos)
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: check-added-large-files
        name: Evitar arquivos grandes (>1MB) no commit
        args: ["--maxkb=1000"]

  # 4. Detecta arquivos com credenciais em texto plano (por nome)
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: detect-private-key
        name: Evitar commit de chaves privadas
```

## Justificativa de cada validação

### 1. Gitleaks

- **Objetivo:** Garantir que não sejam incluídos no repositório segredos sensíveis (chaves de API, tokens, senhas, certificados).
- **Justificativa:** Vazamento de segredos em commits é uma das falhas mais críticas e comuns; detectar isso no momento do commit evita exposição permanente no histórico.
- **Execução:** O `gitleaks` roda no pre-commit e, se encontrar um padrão que configura segredo, bloqueia o commit até que o problema seja corrigido.

### 2. Prettier

- **Objetivo:** Padronizar a formatação de arquivos (JS, TS, JSON, etc.).
- **Justificativa:** Formatação consistente reduz "ruído" em diffs, facilita revisão de código e aumenta a legibilidade do projeto.
- **Execução:** O `prettier` é executado no pre-commit; ele pode reformatar arquivos automaticamente. Se modificar arquivos, será necessário dar `git add` nos arquivos alterados e tentar o commit novamente.

### 3. check-added-large-files

- **Objetivo:** Bloquear o commit de arquivos adicionados acima de um limite (ex.: > 1 MB).
- **Justificativa:** Evita incluir dumps, binários pesados ou dados sensíveis acidentalmente no repositório, preservando performance e segurança.
- **Execução:** O hook verifica o tamanho dos arquivos adicionados e falha o commit se algum exceder o limite configurado (parâmetro `--maxkb` no hook).

### 4. detect-private-key

- **Objetivo:** Detectar e impedir o commit de chaves privadas ou arquivos que pareçam ser chaves (ex.: id_rsa, .pem, .pfx).
- **Justificativa:** Chaves privadas em repositórios comprometem identidades e acessos; esse controle reduz risco de comprometimento imediato.
- **Execução:** O hook analisa padrões de arquivos e conteúdo; ao detectar correspondência, o commit é bloqueado até remoção ou correção.

---

## Orientações de execução dos hooks

1. Instale o `pre-commit` (globalmente ou no virtualenv):

   ```bash
   pip install pre-commit
   ```

2. Instale os hooks definidos no repositório:

   ```bash
   pre-commit install
   ```

3. Para rodar manualmente todos os hooks em todos os arquivos:

   ```bash
   pre-commit run --all-files
   ```

4. Observações práticas:
   - Se um hook (por exemplo o `prettier`) alterar arquivos, faça `git add` nos arquivos modificados e tente o commit novamente.
   - Mensagens de falha dos hooks costumam indicar exatamente o problema (ex.: "secret detected in file X" ou "file Y exceeds max size"); use essas mensagens para corrigir antes de re-commit.
   - Para CI, garanta que a mesma versão dos hooks (ou ferramentas equivalentes) seja executada no pipeline para evitar divergências entre ambiente local e servidor.

## Pipelines CI/CD Implementadas

### 01-Secret-Scanning.yml

- Objetivo: Detectar segredos expostos no código automaticamente.
- Ferramentas: Gitleaks + Trivy.
- Descrição: Analisa commits e histórico de branches, gerando relatórios detalhados.
- Falha o pipeline se segredos forem encontrados, garantindo prevenção proativa.

### 02-SonarQube.yml

- Objetivo: Executar análise SAST local (self-contained).
- Ferramentas: SonarQube.
- Descrição: Analisa código fonte, detecta bugs, code smells, vulnerabilidades e cobertura de testes.
- Relatórios detalhados são gerados no summary do GitHub Actions.
- Falha o pipeline se métricas críticas não forem atendidas (ex.: vulnerabilidades, bugs MAJOR/CRITICAL, cobertura < 60%).

### 03-Dependency-Scan.yml

- Objetivo: Detectar vulnerabilidades em dependências Node.js.
- Ferramenta: npm audit (análise direta do package.json).
- Descrição: Verifica pacotes declarados no projeto sem instalação, gera resumo de todas as vulnerabilidades.
- Falha o pipeline se forem encontradas vulnerabilidades HIGH/CRITICAL.
- Relatórios completos são enviados como artifact para referência futura.

---

## Entregas Técnicas

- **README.md:** instruções de execução e visão da arquitetura.
- **Pipelines CI/CD:** automatizados, cobrindo pelo menos 3 técnicas de segurança (pre-commit hook, secret scan, SAST e dependency scan).
- **Código da infraestrutura:** IaC (Docker Compose, Kubernetes, Terraform, etc.).
- **Relatórios de ferramentas de segurança:** O relatório com as evidências de falhas detectadas está [disponível aqui](relatorio-execucao.md).

---

## Entregas de Gestão

- **entrega-gestao.md:** Documento contendo as justificativas para adoção das técnicas de DevSecOps e da Política de Segurança e suas boas práticas. Esse documento pode ser acessado [diretamente aqui](entrega-gestao.md) e também estará disponível em formato PDF e será entregue em conjunto com o link deste repositório.

---
