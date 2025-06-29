# Jules-Flow System
![icone-jules-flow](icone-jules-flow.webp)

## Propósito do Sistema

Jules-Flow é um sistema de gerenciamento de microtarefas projetado para ser operado pelo agente de IA, Jules, sob a supervisão de um Desenvolvedor humano. O sistema é inteiramente baseado em arquivos Markdown e visa estruturar e rastrear o trabalho de desenvolvimento realizado por Jules de forma transparente, documentada e com alta qualidade.

## Adicionando o Jules-Flow ao seu Projeto

Siga estes passos para realizar uma cópia "dura" do sistema para dentro do seu repositório.

**Passo 1: Clonar o Repositório de Referência**

Primeiro, clone o repositório oficial do `jules-flow` em um diretório de desenvolvimento geral (fora do seu projeto), como `~/dev`.

```bash
# Navegue até seu diretório de desenvolvimento
cd ~/dev

# Clone o repositório
git clone https://github.com/docg1701/jules-flow.git
```

**Passo 2: Copiar os Arquivos para o seu Projeto**

Agora, copie os arquivos do clone de referência para um diretório `jules-flow` dentro da raiz do seu projeto.

```bash
# Navegue para a raiz do seu projeto (ex: ~/dev/meu-projeto)
cd ~/dev/meu-projeto

# Crie o diretório jules-flow e copie o conteúdo para ele
mkdir -p jules-flow
cp -r ~/dev/jules-flow/* jules-flow/

# É crucial remover o histórico git do jules-flow
rm -rf jules-flow/.git
```
**Passo 3: Registrar os Arquivos no seu Projeto**

Finalmente, adicione os novos arquivos ao controle de versão do seu projeto.

```bash
git add jules-flow/
git commit -m "feat: Adiciona o sistema Jules-Flow diretamente ao projeto"
git push
```

## Removendo o Jules-Flow do Projeto

### Como Remover o Jules-Flow

Para remover completamente o Jules-Flow do seu projeto, use o comando abaixo.

```bash
# Remove o diretório e todo o seu conteúdo
rm -rf jules-flow/

# Adiciona a remoção ao controle de versão e faz o commit
git add .
git commit -m "refactor: Remove a integração com o Jules-Flow"
git push
```

## O Fluxo de Trabalho Orientado a Tarefas

O trabalho com Jules é organizado em um ciclo de vida dinâmico para cada nova funcionalidade, que ocorre em seu próprio branch. O processo é guiado por um plano mestre (`working-plan.md`) e executado através de tarefas atômicas que gerenciam seu próprio estado. Jules opera seguindo uma série de fases, conforme detalhado no `instructions-for-jules.md` e referenciado nos prompts de execução.

### Artefatos Chave Gerenciados por Jules

Durante suas operações, Jules interage com e gerencia diversos arquivos importantes, tanto dentro da estrutura interna do Jules-Flow quanto na raiz do seu projeto:

*   **`AGENTS.md` (na raiz do projeto alvo):** No início de suas operações (Fase 1), Jules garante que um arquivo `AGENTS.md` exista na raiz do seu projeto. Se não houver, ele é copiado de um modelo (`templates/AGENTS.md` do sistema Jules-Flow). Se já existir, Jules tentará mesclar diretrizes importantes do modelo. Este arquivo fornece instruções e preferências de codificação específicas do projeto para Jules.
*   **`VISION.md` (na raiz do projeto alvo):** Durante a Fase 2, Jules cria ou atualiza um arquivo `VISION.md`. Este documento, baseado no `working-plan.md` e na análise do código, serve como uma referência de alto nível para o objetivo do projeto, arquitetura pretendida, principais funcionalidades e tecnologias chave.
*   **`./jules_bootstrap.sh` (na raiz do projeto alvo):** Script de configuração do ambiente da VM, detalhado abaixo.
*   **`working-plan.md` (interno ao Jules-Flow):** O plano de trabalho mestre, gerado pelo Desenvolvedor em colaboração com o Gemini, que guia todas as ações de Jules.
*   **`task-index.md` (interno ao Jules-Flow):** O índice central que rastreia o status e o histórico de todas as tarefas.
*   **Arquivos de Tarefa (`task-XXX.md`, internos ao Jules-Flow):** Unidades de trabalho individuais localizadas nos diretórios `backlog/`, `in_progress/`, `done/`, e `failed/`.
*   **`docs/reference/` (interno ao Jules-Flow):** Contém artefatos de pesquisa e documentação técnica consultada ou gerada por Jules.
*   **`final-reports/` (interno ao Jules-Flow):** Arquiva os relatórios consolidados ao final de um ciclo de trabalho.

### Configuração do Ambiente da VM com `jules_bootstrap.sh`

Para garantir que Jules opere em um ambiente de máquina virtual (VM) com todas as dependências de sistema necessárias, o sistema Jules-Flow gerencia o script `./jules_bootstrap.sh`.

*   **Localização e Propósito:** Reside na raiz do seu repositório e contém comandos shell para instalar pacotes de sistema.
*   **Gerenciamento por Jules (Fase 1):** Jules analisa arquivos do projeto (ex: `package.json`, `Dockerfile`) para inferir dependências e as adiciona ao `jules_bootstrap.sh`. Se não existir, Jules o cria.
*   **Atualização Dinâmica (Fase 3):** Se uma tarefa falhar por uma dependência de sistema ausente, Jules tentará adicionar o comando de instalação ao script e o notificará.
*   **Ação Crítica do Usuário:** **Você DEVE configurar a interface do Agente Jules (em jules.google.com) para usar este script `jules_bootstrap.sh` como o "Environment setup script" para o seu repositório.** Isso garante que cada nova sessão de trabalho da VM seja provisionada corretamente. Se Jules atualizar o script, aprove o commit e instrua Jules a retomar o trabalho; a nova VM usará o bootstrap atualizado.
*   **Conteúdo Inicial (Exemplo):**
    ```bash
    #!/bin/bash
    # Script de bootstrap para o ambiente de Jules
    sudo apt-get update
    # Adicione aqui outros comandos de instalação, ex:
    # sudo apt-get install -y curl git python3-pip
    echo "Bootstrap script concluído."
    ```

### Fases Operacionais de Jules

O trabalho de Jules é dividido nas seguintes fases principais, conforme detalhado no `instructions-for-jules.md` e acionado pelos prompts na seção "Guia de Prompts Essenciais":

1.  **Fase de Planejamento (Humano + Gemini):**
    *   O desenvolvedor humano, atuando como arquiteto, colabora com o Gemini para analisar o código e discutir a implementação. Esta fase culmina na geração do arquivo `working-plan.md` (que será colocado dentro da estrutura do Jules-Flow, por exemplo, na raiz ou em `jules-flow/`).

2.  **Fases de Execução por Jules (acionadas por prompts):**

    *   **Fase 1: Descoberta e Pesquisa**
        *   Jules inicia limpando o ambiente de tarefas. Ele configura o `AGENTS.md` na raiz do seu projeto. Em seguida, analisa o projeto para inferir dependências de sistema, criando ou atualizando o script `./jules_bootstrap.sh`. Finalmente, ele identifica no `working-plan.md` áreas que exigem pesquisa, cria tarefas para isso e armazena os resultados em `docs/reference/`.

    *   **Fase 2: Preparação e Decomposição do Plano**
        *   Nesta fase, Jules converte o `working-plan.md` em tarefas executáveis. Uma tarefa importante é a criação/atualização do `VISION.md` na raiz do projeto. As demais etapas do `working-plan.md` são decompostas em tarefas atômicas (ex: `development`, `test`, `documentation`) e registradas no `task-index.md`.

    *   **Fase 3: Execução Inteligente e Dinâmica de Tarefas**
        *   Jules processa as tarefas do `backlog/` com base em dependências e prioridades. Antes de executar uma tarefa, ele consulta o `VISION.md` e os documentos em `docs/reference/`. Cada tarefa é movida para `in_progress/` e, ao ser concluída, para `done/` (com um relatório de execução detalhado dentro do arquivo da tarefa) ou `failed/`. Tarefas de `development` bem-sucedidas geram automaticamente tarefas de `test`. Falhas podem pausar o fluxo e, em casos de dependências de sistema ausentes, levar à atualização do `jules_bootstrap.sh`.

    *   **Fase 4: Geração de Relatório e Finalização**
        *   Após todas as tarefas serem concluídas, Jules compila um relatório final (salvo em `final-reports/`) a partir dos detalhes de execução das tarefas concluídas. Os diretórios de trabalho (`backlog`, `in_progress`, `done`, `failed`) e o `task-index.md` são limpos (preservando seu cabeçalho), preparando para o próximo ciclo.

    *   **Fase 5: Atualização da Documentação do Projeto**
        *   Com base no trabalho realizado, Jules identifica os impactos na documentação oficial do projeto, cria tarefas de `documentation` e realiza as atualizações necessárias.

    *   **Fase 6: Verificação de Inconsistências e Refatoração**
        *   Jules analisa o código produzido em busca de inconsistências, duplicação ou oportunidades de refatoração, criando e executando tarefas de `refactor` ou `fix` conforme necessário.

    *   **Fase 7: Desenvolvimento Conjunto Humano-Jules (Modo Co-Dev)**
        *   Um modo de trabalho altamente interativo onde o desenvolvedor e Jules colaboram sincronamente em tarefas específicas. Detalhado na seção "Desenvolvimento Conjunto Humano-Jules (Modo Co-Dev)".

**Nota sobre Adaptação do Plano:** Caso Jules identifique, durante a execução (Fase 3), a necessidade de uma alteração estratégica significativa no `working-plan.md`, ele poderá iniciar um "Processo Especial: Adaptação de Plano em Execução", detalhado no `instructions-for-jules.md`. Isso envolve pausar o trabalho, criar uma tarefa `replan` para revisão do desenvolvedor e aguardar um `working-plan.md` atualizado.

## Guia de Prompts Essenciais

Este guia contém todos os prompts necessários para interagir com o Gemini e o Jules durante o ciclo de vida de desenvolvimento. **Nota:** Os números das Fases nos prompts de execução de Jules (ex: "Execute a Fase 3...") referem-se às fases operacionais de Jules descritas acima e detalhadas no `instructions-for-jules.md`.

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
2.  Ao receber o comando, gere o conteúdo completo para um arquivo chamado `working-plan.md`. (Este arquivo será colocado pelo desenvolvedor dentro da estrutura do Jules-Flow, por exemplo, na raiz ou em `jules-flow/` se o Jules-Flow estiver em um subdiretório do projeto).
3.  O conteúdo do arquivo deve seguir **rigorosamente** a estrutura abaixo, sem desvios:

---
# Plano de Trabalho para Jules
## Objetivo Geral
(Um resumo claro e conciso do objetivo técnico da funcionalidade, conforme discutido.)
## Passo a Passo da Execução para Jules
(Uma lista numerada de **objetivos técnicos de médio porte** ou **etapas principais** que Jules deve alcançar. Jules irá decompor cada um desses objetivos em tarefas técnicas mais atômicas (como `development`, `test`, `documentation`), se necessário. Exemplo de objetivo de médio porte: 'Implementar a funcionalidade de autenticação de usuários no backend', em vez de 'Modificar arquivo auth_controller.py para adicionar função X', 'Criar migration Y para tabela de usuários', etc. Detalhe o suficiente para guiar Jules, mas permita que ele gerencie a granularidade das tasks técnicas.)
---

**Regras Adicionais:**
* Sempre aguarde o comando `FINALIZE O PLANO` antes de gerar o artefato final.
* O "Passo a Passo da Execução para Jules" deve ser o mais detalhado possível, pois servirá como a única fonte de verdade para o agente de execução.
```

### 2. Prompts de Execução (para o Jules)

Use os prompts a seguir para gerenciar o trabalho de Jules no branch de desenvolvimento.

**ATENÇÃO:** Para a primeira interação com Jules em um novo clone ou ambiente de VM, recomenda-se um prompt inicial como 'Analise este repositório e o sistema Jules-Flow.' Isso permite que Jules carregue o contexto do projeto e da VM adequadamente antes de executar as fases de trabalho do Jules-Flow. Após esta inicialização, os prompts subsequentes tendem a ter maior taxa de sucesso.

**Prompt 2.1: Início dos Trabalhos no Branch**
```markdown
Olá, Jules. Estamos iniciando o trabalho em um novo branch. Sua missão é preparar o ambiente e iniciar o ciclo de trabalho.

Execute as **Fases 1 e 2** do arquivo `instructions-for-jules.md` sequencialmente para colocar o projeto em estado de pronto para a execução das tarefas.
```

**Prompt 2.2: Continuar Trabalho (Individual ou em Lote)**

Use este comando para instruir Jules a executar as tarefas pendentes. Ele possui duas variações.

*Para executar apenas a próxima tarefa disponível:*
```markdown
Olá, Jules. É hora de retomar seu trabalho em um novo branch.

Execute a **Fase 3** do arquivo `instructions-for-jules.md` para processar a próxima tarefa disponível no backlog.
```

*Para executar um lote de tarefas (ex: 3 tarefas em sequência):*
```markdown
Olá, Jules. É hora de retomar seu trabalho em um novo branch.

Execute a **Fase 3** do arquivo `instructions-for-jules.md` **3 vezes em sequência**, processando um lote de tarefas do backlog sem pausas entre elas.
```
Nota: Se qualquer tarefa em um lote falhar, Jules pausará toda a execução do lote após finalizar a tarefa que falhou (movendo-a para `failed/`) e aguardará novas instruções, conforme a Fase 3, Passo 7 do `instructions-for-jules.md`.

**Prompt 2.3: Finalizar e Limpar o Branch**

Use este prompt após todas as tarefas terem sido concluídas com sucesso.

```markdown
Olá, Jules. O trabalho de desenvolvimento foi concluído.

Execute a **Fase 4** do arquivo `instructions-for-jules.md` para gerar o relatório final e limpar o ambiente de trabalho.
```

**Prompt 2.4: Atualizar a documentação**

Use este prompt para iniciar a fase de atualização da documentação do projeto.

```markdown
Olá, Jules. Com base no trabalho concluído, sua missão agora é atualizar a documentação do projeto.

Execute a **Fase 5** do arquivo `instructions-for-jules.md` para analisar os impactos na documentação e realizar as atualizações necessárias.
```

**Prompt 2.5: Revisar o Trabalho do Branch**

Use este prompt para iniciar uma revisão de qualidade do código gerado.

```markdown
Olá, Jules. O trabalho de desenvolvimento foi concluído.

Execute a **Fase 6** do arquivo `instructions-for-jules.md` para realizar uma revisão de qualidade no código produzido e identificar possíveis melhorias.
```

**Prompt 2.6: Analisar e Corrigir Tarefa com Falha**

Use este prompt quando uma tarefa falhar e o trabalho for bloqueado.

```markdown
Olá, Jules. Identificamos que a tarefa `task-XXX` falhou.

Siga o procedimento de tratamento de falhas descrito na **Fase 3 (passo 7)** do arquivo `instructions-for-jules.md` para analisar o problema. Em seguida, crie uma nova task de correção.
```

**Prompt 2.7 (Opcional): Iniciar Reavaliação do Plano de Trabalho**

Use este prompt se você, Desenvolvedor, identificar a necessidade de alterar o `working-plan.md` antes que Jules o faça.

```markdown
Olá, Jules. Identifiquei a necessidade de reavaliar e possivelmente alterar o `working-plan.md` atual.

Por favor, inicie o "Processo Especial: Adaptação de Plano em Execução" conforme descrito em `instructions-for-jules.md`. Pause qualquer tarefa em progresso, crie a task `replan` com suas observações sobre o estado atual do plano (se houver) e aguarde a atualização do `working-plan.md` e minhas instruções para prosseguir.
```

**Prompt 2.8: Iniciar Modo de Desenvolvimento Conjunto (Co-Dev)**

Use este prompt para iniciar uma sessão de desenvolvimento colaborativo com Jules, onde você e o agente trabalham de forma síncrona no mesmo branch para desenvolver e depurar tarefas.

```markdown
Olá, Jules. Gostaria de iniciar o Modo de Desenvolvimento Conjunto (Co-Dev).

Por favor, execute a **Fase 7** do arquivo `instructions-for-jules.md` para preparar o ambiente para o trabalho colaborativo.
```

### 3. Desenvolvimento Conjunto Humano-Jules (Modo Co-Dev)

Esta modalidade é projetada para situações que exigem um ciclo de feedback rápido entre o desenvolvedor humano e Jules, especialmente útil para depuração complexa ou desenvolvimento iterativo fino de uma funcionalidade específica.

**Fluxo de Trabalho no Modo Co-Dev:**

1.  **Iniciação pelo Usuário**: O desenvolvedor humano invoca o Modo Co-Dev usando o "Prompt 2.8".
2.  **Preparação por Jules**:
    *   Jules analisa o estado atual do repositório e do sistema Jules-Flow (arquivos como `task-index.md`, estrutura de diretórios `backlog/`, etc.).
    *   Jules anuncia que está pronto para iniciar o Modo Co-Dev.
    *   Jules informa o nome do branch em que está trabalhando (ex: `feature/task-123-xyz`).
    *   Jules fornece ao usuário os comandos `git` necessários para que o usuário sincronize seu ambiente local com o de Jules:
        ```bash
        # Exemplo de comandos fornecidos por Jules ao usuário:
        # 1. Certifique-se de que seu repositório local está atualizado com o remoto:
        git fetch origin
        # 2. Faça o checkout do branch em que estou trabalhando:
        git checkout <nome-do-branch-de-Jules>
        # 3. Garanta que seu branch local está sincronizado com o meu último commit:
        git pull origin <nome-do-branch-de-Jules>
        ```
3.  **Seleção de Tarefa**:
    *   Jules lista as tarefas que não estão marcadas como `done` no `task-index.md` (ou seja, tarefas com status `backlog`, `in_progress`, `failed`, `paused_replan`, `paused_environment`, `paused_research`).
    *   O usuário escolhe uma tarefa específica para trabalhar em conjunto.
4.  **Ciclo Iterativo de Desenvolvimento e Depuração**:
    *   **Ação de Jules**:
        1.  Jules analisa os arquivos relevantes para a tarefa selecionada.
        2.  Jules realiza as modificações de código ou arquivos necessárias.
        3.  Jules explica detalhadamente as modificações realizadas.
        4.  Jules lista os arquivos que foram alterados.
        5.  Jules realiza o commit das modificações no branch atual. (Nota: Jules deve ser capaz de fazer commits parciais sem finalizar todo o plano de trabalho. Se a ferramenta `submit` for a única forma de commitar, este passo pode precisar ser adaptado para que Jules prepare o commit e instrua o usuário a finalizá-lo.)
    *   **Sincronização e Validação pelo Usuário**:
        1.  Jules instrui o usuário a executar `git pull origin <nome-do-branch-de-Jules>` para obter as últimas modificações.
        2.  Jules informa ao usuário quais comandos devem ser executados localmente (ex: rodar testes, compilar o projeto, iniciar um servidor de desenvolvimento, executar um script específico).
        3.  Jules especifica qual tipo de feedback deve ser enviado de volta (ex: saída completa do console, logs de erro específicos, screenshots de comportamento inesperado, etc.).
    *   **Análise e Correção por Jules**:
        1.  O usuário executa os comandos e envia o feedback solicitado para Jules.
        2.  Jules analisa o feedback.
        3.  Se problemas forem identificados, Jules tenta identificar a causa raiz e propõe/implementa as correções.
        4.  O ciclo retorna para "Ação de Jules", onde ele aplicará as correções e fará um novo commit.
5.  **Conclusão da Tarefa e do Modo Co-Dev**:
    *   Este ciclo iterativo continua até que a tarefa selecionada esteja funcionando conforme esperado e tanto o usuário quanto Jules concordem que ela está concluída.
    *   Para finalizar o Modo Co-Dev, o usuário pode emitir um comando como "Jules, a tarefa está concluída. Podemos finalizar o Modo Co-Dev."
    *   Jules então pode retornar ao seu fluxo de trabalho padrão (Fase 3 ou outra, conforme apropriado) ou aguardar novas instruções.

## Estrutura de Diretórios

* `/jules-flow/`: Diretório raiz do sistema Jules-Flow.
    * `/backlog/`: Contém tarefas pendentes (arquivos `.md`).
    * `/in_progress/`: Contém a tarefa que está sendo executada no momento.
    * `/done/`: Contém tarefas concluídas com sucesso.
    * `/failed/`: Contém tarefas que falharam durante a execução.
    * `/final-reports/`: Contém os relatórios finais consolidados de cada etapa de trabalho.
    * `/docs/reference/`: Contém artefatos de pesquisa e documentação técnica consultada ou gerada por Jules (ex: documentação de APIs, resultados de tasks de `research` da Fase 1).
    * `/templates/`: Contém modelos para novas tarefas (`task-template.md`) e para o arquivo de diretrizes do agente (`AGENTS.md`).
    * `README.md`: Este arquivo (o README do próprio sistema Jules-Flow).
    * `instructions-for-jules.md`: Instruções operacionais para o agente Jules.
    * `task-index.md`: Índice e relatório de progresso de todas as tarefas.
    * `working-plan.md`: O plano de trabalho mestre gerado na fase de planejamento.
    * `icone-jules-flow.webp`: Ícone do sistema (se aplicável, dentro da estrutura jules-flow).
    * `LICENSE`: Arquivo de licença do sistema Jules-Flow.

## Licença

Este projeto está licenciado sob os termos da Licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.
