# Jules-Flow System
![icone-jules-flow](icone-jules-flow.webp)

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

## O Fluxo de Trabalho Orientado a Tarefas

O trabalho com Jules é organizado em um ciclo de vida dinâmico para cada nova funcionalidade, que ocorre em seu próprio branch (`jules-<timestamp>`). O processo é guiado por um plano mestre e executado através de tarefas atômicas que gerenciam seu próprio estado.

### Fase 1: Planejamento (Humano + Gemini)

O desenvolvedor humano, atuando como arquiteto, colabora com o Gemini para analisar o código e discutir a implementação. Esta fase culmina na geração de um arquivo `jules-flow/working-plan.md`, que contém o roteiro detalhado para o agente Jules.

### Fase 2: Execução (Agente Jules)

Em um novo branch, o agente Jules é acionado. Ele lê o `working-plan.md`, prepara o ambiente de trabalho e decompõe o plano em `task`s atômicas no diretório `/backlog/`. A partir daí, ele executa as tarefas de forma inteligente, seguindo esta lógica:
1.  **Seleção por Dependência**: Escolhe a próxima tarefa do `/backlog/` cujas dependências já foram concluídas.
2.  **Gestão de Estado**: Move a tarefa para `/in_progress/` ao iniciar e para `/done/` ou `/failed/` ao terminar, atualizando sempre o `task-index.md`.
3.  **Geração Automática de Testes**: Ao concluir com sucesso uma tarefa do tipo `development`, Jules cria automaticamente uma tarefa de `test` correspondente no `/backlog/`, garantindo a cobertura de testes.
4.  **Gestão de Falhas**: Se uma tarefa falha, o trabalho é pausado até que uma nova tarefa de correção seja criada, garantindo que nenhum problema seja ignorado.

### Fase 3: Finalização e Relatório (Agente Jules)

Após todas as `task`s serem concluídas com sucesso, Jules é acionado para consolidar os relatórios de execução em um único relatório final. Este é arquivado em `jules-flow/final-reports/`, e todos os artefatos de trabalho temporários são removidos, deixando o branch limpo e pronto para a revisão (Pull Request).

## Guia de Prompts Essenciais

Este guia contém todos os prompts necessários para interagir com o Gemini e o Jules durante o ciclo de vida de desenvolvimento.

### 1. Prompt de Planejamento (para o Gemini)

Cole este prompt na primeira interação com um novo chat do Gemini. 
Anexe ao chat o endereço completo do repositório github do projeto.

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
Olá, Jules. Estamos iniciando o trabalho em um novo branch. Sua missão é preparar o ambiente e iniciar o ciclo de trabalho.

Execute as **Fases 1 e 2** do arquivo `instructions-for-jules.md` sequencialmente para colocar o projeto em estado de pronto para a execução das tarefas.
```

**Prompt 2.2: Continuar Trabalho (Individual ou em Lote)**

Use este comando para instruir Jules a executar as tarefas pendentes. Ele possui duas variações.

*Para executar apenas a próxima tarefa disponível:*
```markdown
Olá, Jules. É hora de retomar seu trabalho neste branch.

Execute a **Fase 3** do arquivo `instructions-for-jules.md` para processar a próxima tarefa disponível no backlog.
```

*Para executar um lote de tarefas (ex: 3 tarefas em sequência):*
```markdown
Olá, Jules. É hora de retomar seu trabalho neste branch.

Execute a **Fase 3** do arquivo `instructions-for-jules.md` **3 vezes em sequência**, processando um lote de tarefas do backlog sem pausas entre elas.
```

**Prompt 2.3: Finalizar e Limpar o Branch**

Use este prompt após todas as tarefas terem sido concluídas com sucesso.

```markdown
Olá, Jules. O trabalho de desenvolvimento neste branch foi concluído.

Execute a **Fase 4** do arquivo `instructions-for-jules.md` para gerar o relatório final e limpar o ambiente de trabalho.
```

**Prompt 2.4: Atualizar a documentação**

Use este prompt para iniciar a fase de atualização da documentação do projeto.

```markdown
Olá, Jules. Com base no trabalho concluído, sua missão agora é atualizar a documentação do projeto.

Execute a **Fase 5** do arquivo `instructions-for-jules.md`, utilizando a lista de arquivos abaixo como escopo para suas modificações.

**Arquivos de Documentação a serem Atualizados:**
- README.md
- docs/guides/getting-started.md
[COLOQUE AQUI, UM POR LINHA, O CAMINHO COMPLETO PARA CADA ARQUIVO DE DOCUMENTAÇÃO A SER ATUALIZADO.]
```

**Prompt 2.5: Revisar o Trabalho do Branch**

Use este prompt para iniciar uma revisão de qualidade do código gerado.

```markdown
Olá, Jules. O desenvolvimento principal neste branch foi concluído.

Execute a **Fase 6** do arquivo `instructions-for-jules.md` para realizar uma revisão de qualidade no código produzido e identificar possíveis melhorias.
```

**Prompt 2.6: Analisar e Corrigir Tarefa com Falha**

Use este prompt quando uma tarefa falhar e o trabalho for bloqueado.

```markdown
Olá, Jules. Identificamos que a tarefa `task-XXX` falhou.

Siga o procedimento de tratamento de falhas descrito na **Fase 3 (passo g)** do arquivo `instructions-for-jules.md` para analisar o problema e criar uma nova tarefa de correção.
```

## Estrutura de Diretórios

* `/jules-flow/`: Diretório raiz do sistema Jules-Flow.
    * `/backlog/`: Contém tarefas pendentes (arquivos `.md`).
    * `/in_progress/`: Contém a tarefa que está sendo executada no momento.
    * `/done/`: Contém tarefas concluídas com sucesso.
    * `/failed/`: Contém tarefas que falharam durante a execução.
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
