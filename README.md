# Integração Contínua

Principais processos:

- Execução de testes
- Linter (padronização do código)
- Verificação de qualidade do código
- Verificações de segurança
- Geração de artefatos prontos para o processo de deploy
- Identificação da próxima versão a ser gerada no software
- Geração de tags e releases

Ferramentas populares para CI:

- Jenkins
- **Github Actions**
- Circle CI
- AWS Code Build
- Azure DevOps
- Google Cloud Build
- GitLab Pipelines / CI

Configuração feita atráves de arquivos .yml criados em .github/workflows.

Actions: https://github.com/marketplace?type=actions

## Código Fonte

https://github.com/tmansur/fc2.0-ci-go

## Iniciando CI com GitHub Actions

Link da documentação: https://docs.github.com/pt/actions

Criação do arquivo de configuração da pipeline de CI no diretório `.github/workflows/`.

```YAML
#ci.yaml

name: ci-golang-workflow #nome da pipeline
on:
  push:
    branches: [ main ]  #processo de CI vai rodar toda vez que for feito um push na branch main
jobs: #jobs que serão executados
  check-application:
    name: run on Ubuntu #nome do job
    runs-on: ubuntu-latest #onde a aplicação será executada
    steps:
      - uses: actions/checkout@v4 #action para fazer checkout do fonte a ser utilizado
      - uses: actions/setup-go@v5 #action de configuração de um ambiente com Golang
        with:
          go-version: 1.15
          cache: false #desabilita o cache das actions
      #run é utilizado para executar comandos
      - run: go test ./exemplo-go #executa os testes do projeto
      - run: go run ./exemplo-go/math.go #executa o
```

### Configurando a branch

Alterando o branch principal para develop:

`Settings | General | Default branch -> develop`

Ativar status check (só faz o merge se a pipeline rodar com sucesso) e bloquear o commit direto na branch:

`Settings | Branches | Add branch ruleset`

- Ruleset name: ci-rules
- Enforcement Status: Active
- Target branches: develop e main
- Require status checks to pass
- Require branches to be up to date before merging
- Status checks that are required: run on Ubuntu (nome do job da pipeline de CI criada)
- Block force pushes

### Strategy Matrix

Permite executar os testes da pipeline em diferentes versões da linguagem, basta configurar o atributo `strategy` dentro da configuração do job no arquivo YAML e mudar a versão no atributo `step` para usar a estratégia configurada.

```YAML
jobs:
  check-application:
    # ...
    strategy:
      matrix:
        go: ["1.14", "1.15"]
    # ...
    steps:
      # ...
      with:
        go-version: ${{ matrix.go }}
```

> [!IMPORTANT]
> Quando colocamos versões diferentes da linguagem para a pipeline executar, é como se existissem duas pipelines, cada uma com nome `"job name" + "(versão da linguagem)"`.
> Por isso, precisamos mudar a configuração de "Require status check to pass" no "rulesets" configurado para adicionar no campo "Status checks that are required" `run on Ubuntu (1.14)` e `run on Ubuntu (1.15)`e remover `run on Ubuntu`.
