# Jules-Flow System

## Propósito do Sistema

Jules-Flow é um sistema de gerenciamento de microtarefas projetado para ser operado pelo agente de IA, Jules, sob a supervisão de um Desenvolvedor humano. O sistema é inteiramente baseado em arquivos Markdown e visa estruturar e rastrear o trabalho de desenvolvimento realizado por Jules de forma transparente, documentada e com alta qualidade.

## Integração e Gerenciamento com Git

Para garantir um gerenciamento de dependências limpo e eficiente, a integração do Jules-Flow ao seu projeto deve ser feita utilizando **Git Submodules**. Este método vincula o repositório do Jules-Flow ao seu projeto de forma que o Git os reconheça como entidades separadas, facilitando atualizações e colaboração.

### 1. Adicionando o Jules-Flow ao seu Projeto

Este é o primeiro passo para integrar o sistema. Ele deve ser executado uma única vez na raiz do repositório do seu projeto.

1.  **Adicione o Submódulo**: Execute o comando abaixo, substituindo a URL pelo endereço do seu repositório `jules-flow`.
    ```bash
    git submodule add https://github.com/docg1701/jules-flow.git jules-flow
    ```
    * Este comando clona o repositório `jules-flow` para um diretório de mesmo nome e cria um arquivo `.gitmodules` para rastrear o vínculo.

2.  **Comite a Integração**: Registre a adição do submódulo no seu projeto principal.
    ```bash
    git add jules-flow .gitmodules
    git commit -m "feat: Integra o sistema Jules-Flow como submódulo"
    ```

### 2. Clonando um Projeto que já usa o Jules-Flow

Quando um novo desenvolvedor (ou você mesmo, em outra máquina) for clonar um projeto que já contém o submódulo, um passo extra é necessário para inicializar e baixar o conteúdo do `jules-flow`.

* **Opção A (Recomendada): Clonar o repositório e o submódulo de uma só vez.**
    ```bash
    git clone --recurse-submodules https://github.com/docg1701/jules-flow.git
    ```

* **Opção B: Se você já clonou o projeto sem o submódulo.**
    ```bash
    # Inicializa os submódulos locais
    git submodule init

    # Baixa as alterações do repositório do submódulo
    git submodule update
    ```

### 3. Atualizando o Jules-Flow

Para buscar a versão mais recente do `jules-flow` (após correções ou novas funcionalidades serem adicionadas a ele), siga estes passos:

1.  **Navegue até o diretório do submódulo e puxe as alterações.**
    ```bash
    cd jules-flow
    git pull origin main
    ```

2.  **Volte ao projeto principal e comite a atualização.** O Git registrará que o submódulo foi atualizado para uma nova versão (um novo commit).
    ```bash
    cd ..
    git add jules-flow
    git commit -m "chore: Atualiza o submódulo Jules-Flow para a versão mais recente"
    ```

### 4. Removendo o Jules-Flow do Projeto

Se precisar desintegrar o sistema, o processo é o seguinte:

1.  **Desinicialize e remova o submódulo.**
    ```bash
    # Desinicializa o submódulo, removendo a entrada do .git/config
    git submodule deinit -f jules-flow

    # Remove o diretório do submódulo e seus registros no git
    git rm -f jules-flow
    ```

2.  **Remova os metadados restantes.** Exclua o diretório `.git/modules/jules-flow` se ele ainda existir.

3.  **Comite a remoção.**
    ```bash
    git commit -m "refactor: Remove a integração com o Jules-Flow"
    ```

### 5. Enviando Alterações para o Repositório Remoto (Push)

Após realizar qualquer uma das operações acima (adicionar, atualizar ou remover o submódulo) e comitá-las, o envio para o repositório remoto (`origin`) funciona como de costume:

```bash
git push
```

## O Fluxo de Trabalho Simplificado

O trabalho com Jules é organizado em um ciclo de vida de três fases claras para cada nova funcionalidade, que ocorre em seu próprio branch (`jules-<timestamp>`).

### Fase 1: Planejamento (Humano + Gemini)

O desenvolvedor humano, atuando como arquiteto, colabora com o Gemini para analisar o código e discutir a implementação de uma nova funcionalidade. Esta fase culmina na geração de um arquivo `jules-flow/working-plan.md`, que contém o roteiro detalhado para o agente Jules.

### Fase 2: Execução (Agente Jules)

Em um novo branch, o agente Jules é acionado. Ele lê o `working-plan.md`, prepara o ambiente de trabalho, decompõe o plano em `task`s atômicas no `backlog/`, e as executa em sequência. O progresso é rastreado através do `task-index.md`.

### Fase 3: Finalização (Agente Jules)

Após todas as `task`s serem concluídas, Jules é acionado uma última vez para consolidar os relatórios de execução de cada `task` em um único relatório final. Este relatório é arquivado em `jules-flow/final-reports/`, e todos os artefatos de trabalho temporários (`backlog/`, `done/`, etc.) são removidos, deixando o branch limpo e pronto para revisão.

## Guia de Prompts Essenciais

Este guia contém todos os prompts necessários para interagir com o Gemini e o Jules durante o ciclo de vida de desenvolvimento.

### 1. Prompt de Planejamento (para o Gemini): cole este prompt na primeira interação com um novo chat do Gemini. Anexe ao chat o repositório github do projeto.
```markdown
Você é um assistente especialista em arquitetura de software e planejamento de projetos, operando especificamente dentro do ecossistema "Jules-Flow". Seu propósito é colaborar com um desenvolvedor humano para transformar um objetivo de alto nível em um plano de trabalho técnico, detalhado e acionável para um agente de IA subordinado chamado Jules.

Seu fluxo de trabalho é dividido em duas etapas distintas:

**Etapa 1: Análise e Discussão Colaborativa**
1.  Ao receber um objetivo do desenvolvedor, sua primeira ação é analisar toda a base de código fornecida para entender o contexto atual, as tecnologias utilizadas e os padrões existentes.
2.  Inicie uma discussão técnica e proativa com o desenvolvedor. Sugira as melhores práticas, bibliotecas relevantes, os arquivos que provavelmente precisarão ser modificados, a estratégia de testes e os possíveis desafios.
3.  O objetivo desta fase é refinar o plano de forma colaborativa até que o desenvolvedor esteja satisfeito com a abordagem.

**Etapa 2: Geração do Plano Formal**
1.  Esta etapa é acionada **apenas** quando o desenvolvedor fornecer o comando exato: `FINALIZE O PLANO`. Não gere o plano de trabalho antes de receber este comando.
2.  Ao receber o comando, gere o conteúdo completo para um arquivo chamado `jules-flow/working-plan.md`.
3.  O conteúdo do arquivo deve seguir **rigorosamente** a estrutura abaixo, sem desvios:

---
`# Plano de Trabalho - Branch: jules-YYYYMMDDHHMMSS`

`(O timestamp no formato ano-mês-dia-hora-minuto-segundo DEVE ser gerado por você.)`

`## Objetivo Geral`

`(Um resumo claro e conciso do objetivo técnico da funcionalidade, conforme discutido.)`

`## Passo a Passo da Execução para Jules`

`(Uma lista numerada de ações concretas, atômicas e sequenciais que o agente Jules deve executar. Cada passo deve ser uma instrução clara, como "Crie uma task do tipo 'development' para modificar o arquivo X" ou "Crie uma task do tipo 'test' para a função Y".)`
---

**Regras Adicionais:**
* Sempre aguarde o comando `FINALIZE O PLANO` antes de gerar o artefato final.
* O "Passo a Passo da Execução para Jules" deve ser o mais detalhado possível, pois servirá como a única fonte de verdade para o agente de execução.
```

### 2. Prompts de Execução (para o Jules)

Use os prompts a seguir para gerenciar o trabalho de Jules no branch de desenvolvimento.

**Prompt 2.1: Início dos Trabalhos no Branch**
```markdown
Olá, Jules. Estamos iniciando o trabalho em um novo branch. Sua missão é executar o plano de trabalho recém-criado. Siga rigorosamente esta sequência:

1.  **Limpeza do Ambiente de Trabalho**: Prepare o diretório `jules-flow` para o novo ciclo.
    * Exclua todos os arquivos das pastas `jules-flow/backlog/` e `jules-flow/done/`.
    * Limpe completamente o conteúdo do arquivo `jules-flow/task-index.md`.
    * Exclua todos os arquivos de `jules-flow/docs/reference/`.
    * Execute um commit com a mensagem "Setup: Limpeza do ambiente de trabalho para novo branch.".

2.  **Leitura e Decomposição do Plano**:
    * **Identificação do Branch**: Abra o arquivo `jules-flow/working-plan.md`. A primeira linha contém o nome do branch. Extraia e armazene este timestamp.
    * **Decomposição das Tarefas**: Com base no plano, crie todas as `task`s necessárias no `backlog/`.
    * Crie o `task-index.md` inicial com a lista de todas as tarefas.

3.  **Início da Execução**:
    * Anuncie que o setup foi finalizado, informe qual será a primeira `task` e comece o trabalho.
```

**Prompt 2.2: Continuar Trabalho (Individual ou em Lote)**

Use o prompt abaixo para executar apenas 1 task por vez.

```markdown
Olá, Jules. É hora de retomar seu trabalho neste branch. Para garantir a continuidade, siga os seguintes passos para se reorientar:

1.  **Recuperação de Contexto**: Releia `instructions-for-jules.md`, analise `task-index.md`, as pastas `backlog/` e `done/`, e o último commit.
2.  **Identificação da Próxima Ação**: Anuncie de forma clara se está retomando uma `task` parcial ou iniciando a próxima.
3.  **Execute**: Prossiga com a execução da ação que você planejou.
```

Use o prompt abaixo para executar X tasks em sequência e só então interagir com o usuário.


```markdown
Olá, Jules. É hora de retomar seu trabalho neste branch. **Execute as próximas X tarefas em sequência sem pausas.**

Para garantir a continuidade, siga os seguintes passos para se reorientar:

1.  **Recuperação de Contexto**: Releia `instructions-for-jules.md`, analise `task-index.md`, as pastas `backlog/` e `done/`, e o último commit.
2.  **Identificação da Próxima Ação**: Anuncie o início da execução do lote de X tarefas.
3.  **Execute**: Prossiga com a execução do lote de tarefas, conforme a lógica da "Fase 2" atualizada em suas instruções.
```

**Prompt 2.3: Finalizar e Limpar o Branch**
```markdown
Olá, Jules. Todo o trabalho de desenvolvimento neste branch foi concluído. Sua missão agora é finalizar o ciclo, gerar o relatório e limpar o ambiente. Execute os seguintes passos em ordem:

1.  **Geração do Relatório Final**:
    * Crie o diretório `jules-flow/final-reports/` se não existir.
    * Compile os "Relatórios de Execução" de todas as `task`s em `done/` em um único arquivo.
    * Recupere o timestamp do `jules-flow/working-plan.md` e salve o relatório como `jules-flow/final-reports/report-YYYYMMDDHHMMSS.md`.

2.  **Limpeza Final do Branch**:
    * Preserve o diretório `jules-flow/final-reports/`.
    * Exclua todos os arquivos de `jules-flow/backlog/`, `jules-flow/done/`, e `jules-flow/docs/reference/`.
    * Limpe os arquivos `jules-flow/task-index.md` e `jules-flow/working-plan.md`.

3.  **Commit de Finalização**: Execute um commit com a mensagem "Finalize: Arquivamento do relatório final e limpeza do branch.".
4.  **Anúncio de Conclusão**: Informe que o trabalho foi concluído e o branch está pronto para revisão.
```

**Prompt 2.4: Atualizar a documentação**
```markdown
Olá, Jules.

A fase de desenvolvimento foi concluída. Sua missão agora é atualizar a documentação do projeto com base no trabalho realizado.

Siga rigorosamente esta sequência:

1.  **Análise do Relatório**:
    * Localize e estude o relatório final mais recente na pasta `jules-flow/final-reports/`. Ele é sua única fonte da verdade para entender o que foi feito.

2.  **Escopo de Trabalho**:
    * A lista abaixo, definida por mim, representa o escopo exato do seu trabalho. Você só tem permissão para modificar os arquivos listados aqui.

    **Arquivos de Documentação a serem Atualizados:**
    - README.md
    - docs/guides/getting-started.md
    [COLOQUE AQUI, UM POR LINHA, O CAMINHO COMPLETO PARA CADA ARQUIVO DE DOCUMENTAÇÃO A SER ATUALIZADO. SE NENHUM ARQUIVO FOR LISTADO, NENHUMA AÇÃO DEVE SER TOMADA.]

3.  **Execução (se houver arquivos na lista acima)**:
    * **Rastreabilidade**: Crie uma única `task` do tipo `documentation` para este trabalho. Na seção "Arquivos Relevantes" da `task`, liste os mesmos arquivos que você está modificando.
    * **Modificação**: Com base na sua análise do relatório, edite os arquivos de documentação aprovados para que reflitam o estado atual do projeto de forma clara e técnica.
    * **Finalização**: Preencha o "Relatório de Execução" na `task` de documentação, detalhando as alterações. Mova a `task` para a pasta `/done/`, atualize o `task-index.md` e execute um commit atômico com a mensagem "docs: Atualiza a documentação do projeto.".
    * **Anúncio**: Informe que a documentação foi atualizada com sucesso.
```

## Estrutura de Pastas

* `/jules-flow/`: Diretório raiz do sistema Jules-Flow.
    * `/backlog/`: Contém tarefas pendentes (arquivos `.md`).
    * `/done/`: Contém tarefas concluídas (arquivos `.md`).
    * `/final-reports/`: Contém os relatórios finais consolidados de cada branch de trabalho.
    * `/docs/`: Contém documentação gerada pelo Jules-Flow.
        * `/reference/`: Arquivos de referência criados durante a fase de pesquisa.
    * `/templates/`: Contém o modelo `task-template.md` para novas tarefas.
    * `README.md`: Este arquivo.
    * `instructions-for-jules.md`: Instruções operacionais para o agente Jules.
    * `task-index.md`: Índice e relatório de progresso de todas as tarefas.
    * `working-plan.md`: O plano de trabalho mestre gerado na fase de planejamento.

## Licença

Este projeto está licenciado sob os termos da Licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.
