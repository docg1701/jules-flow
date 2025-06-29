# Instruções Operacionais para Jules

Este documento detalha o fluxo de trabalho completo que você, Jules, deve seguir. Sua aderência estrita a estas fases e princípios é crucial para a organização e o sucesso do projeto.

---

### Princípios Fundamentais

1.  **Comunicação e Versionamento de Etapas Significativas**: Cada passo significativo que você toma deve ser claramente documentado e versionado. As alterações serão agrupadas e submetidas em um commit final para o branch ao concluir o plano de trabalho aprovado.
2.  **Gestão de Estado por Arquivos**: O estado do projeto é definido pela localização dos arquivos de tarefa (`task-XXX.md`) nos diretórios `jules-flow/backlog/`, `jules-flow/in_progress/`, `jules-flow/done/` e `jules-flow/failed/`.
3.  **Índice Central como Fonte da Verdade**: O arquivo `jules-flow/task-index.md` é a única fonte da verdade para o status e o histórico de todas as tarefas. Ele **deve ser atualizado** a cada mudança de estado de uma tarefa (ex: de `jules-flow/backlog/` para `jules-flow/in_progress/`).
4.  **Imutabilidade do Status no Arquivo da Tarefa**: O cabeçalho `status:` dentro do arquivo `.md` de uma tarefa é definido apenas no momento de sua criação (normalmente como `backlog`) e **não deve ser alterado manualmente por você**. O progresso é refletido no `jules-flow/task-index.md` e na movimentação do arquivo entre os diretórios.
5.  **Uso Correto do Docker Compose**: Ao interagir com Docker Compose, utilize sempre a sintaxe moderna `docker compose` (sem hífen). Para comandos que exigem interação com o daemon Docker e podem necessitar de privilégios elevados (ex: `docker compose up`, `docker compose down`, `docker build`), utilize `sudo docker compose ...` para garantir a execução correta no ambiente da VM, a menos que o `working-plan.md` ou a descrição da tarefa especifique explicitamente o contrário para um contexto particular ou se o `jules_bootstrap.sh` já configurar o Docker para execução sem `sudo`.
---

### O Fluxo de Trabalho em 7 Fases

#### Fase 1: Descoberta e Pesquisa

* **Objetivo**: Limpar o ambiente de trabalho, preparar o script de bootstrap da VM, identificar o escopo de pesquisa e buscar o conhecimento necessário.
* **Ação**:
    1.  **Análise Inicial de Dependências e Criação/Atualização do `jules_bootstrap.sh`**:
        *   Verifique se o arquivo `jules_bootstrap.sh` existe na raiz do projeto. Se não existir, crie-o com o conteúdo inicial:
            ```bash
            #!/bin/bash
            # Script de bootstrap para o ambiente de Jules
            # Adicione aqui os comandos para instalar dependências de sistema
            
            # Exemplo:
            # sudo apt-get update
            # sudo apt-get install -y curl git python3-pip
            
            echo "Bootstrap script concluído."
            ```
        *   Analise arquivos de manifesto do projeto (como `package.json`, `requirements.txt`, `Gemfile`, `pom.xml`, `build.gradle`, `Makefile`, scripts de CI em `.github/workflows` ou `.gitlab-ci.yml`, `Dockerfile` se presente) para inferir dependências de sistema comuns ou comandos de setup de ambiente. Use `read_files` para ler esses arquivos.
        *   Use `grep` nesses arquivos para buscar por padrões como `apt-get install`, `yum install`, `apk add`, ou menções a pacotes de sistema conhecidos.
        *   Para cada dependência de sistema inferida ou comando de setup relevante, adicione o comando de instalação correspondente ao `jules_bootstrap.sh`. Adicione os comandos antes da linha `echo "Bootstrap script concluído."`.
            *   Adicione comandos de forma a serem idempotentes, se possível (ex: `apt-get install -y` já lida com isso).
            *   Se o `jules_bootstrap.sh` já contiver comandos similares, evite duplicação desnecessária, mas prefira adicionar se não tiver certeza de que a dependência exata já está coberta.
        *   Esta etapa deve ser considerada como uma tentativa de "melhor esforço" para preparar o ambiente. Problemas de dependência ainda podem surgir durante a execução das tarefas.
    2.  **Limpeza do Ambiente de Tarefas**: Exclua todos os arquivos de tarefas (`.md`) dos diretórios `jules-flow/backlog/`, `jules-flow/in_progress/`, `jules-flow/done/`, `jules-flow/failed/`. Limpe também o conteúdo de `jules-flow/docs/reference/` (exceto `.gitkeep`).
    3.  **No arquivo `jules-flow/task-index.md`, preserve apenas o cabeçalho e limpe o restante da tabela**. O cabeçalho esperado é:
    	```markdown
    	# Índice de Tarefas Jules-Flow

    	| ID da Tarefa | Título | Tipo | Status | Prioridade | Dependências | Atribuído |
    	|--------------|--------|------|--------|------------|--------------|-----------|
    	```
    4.  **Análise do `jules-flow/working-plan.md` para Pesquisa**: Leia o `jules-flow/working-plan.md`. Identifique as áreas que exigem pesquisa de documentação.
    5.  **Criação das Tarefas de Pesquisa**: Crie `task`s do tipo `research` para cada tópico identificado. (Lembre-se de verificar a criação física de cada task de pesquisa em `jules-flow/backlog/` antes de adicioná-las ao `jules-flow/task-index.md`).
    6.  **Execução da Pesquisa**: Para cada `task` de pesquisa, execute a busca por documentação oficial e compile os resultados em arquivos de referência dentro de `jules-flow/docs/reference/`.

#### Fase 2: Preparação e Decomposição do Plano

* **Objetivo**: Converter o `jules-flow/working-plan.md` em tarefas executáveis, incluindo a geração de uma visão geral do projeto.
* **Ação**:
    1.  Leia o `jules-flow/working-plan.md` para entender o escopo completo.
    2.  **Geração da Tarefa `VISION.md`**:
        *   Crie automaticamente uma nova `task` (ex: `task-VIS`) do tipo `documentation` (ou `vision_generation`) no diretório `jules-flow/backlog/`.
        *   **Título:** "Gerar/Atualizar VISION.md com base no working-plan.md e análise do código existente".
        *   **Descrição:** "Analisar o `jules-flow/working-plan.md` atual e, se aplicável, o código existente no repositório (especialmente se for uma atualização e não um projeto do zero). Com base nisso, gerar ou atualizar o arquivo `VISION.md` na raiz do projeto. Este arquivo deve detalhar:\n                *   O objetivo geral do projeto (conforme `working-plan.md`).\n                *   A arquitetura principal pretendida ou existente.\n                *   Uma descrição das principais funcionalidades ou módulos que serão desenvolvidos/afetados, conforme inferido do `working-plan.md`.\n                *   Quaisquer princípios de design ou tecnologias chave mencionadas ou implícitas no `working-plan.md`.\n                *   Os principais fluxos de interação ou dados esperados.\n                O `VISION.md` servirá como um documento de referência de alto nível para guiar o desenvolvimento subsequente."
        *   **Prioridade:** `high`.
        *   **Arquivos Relevantes:** `jules-flow/working-plan.md`, `VISION.md` (para criação/atualização na raiz do projeto), e potencialmente os principais diretórios de código fonte para análise contextual (ex: `src/`, `app/`).
        *   **Critérios de Aceitação:** Um arquivo `VISION.md` é criado/atualizado na raiz do projeto contendo as seções descritas.
        *   Adicione esta `task-VIS` ao `jules-flow/task-index.md` com status `backlog` e verifique sua criação física em `jules-flow/backlog/`.
    3.  Decomponha o plano em uma série de `task`s atômicas (como `development`, `test`, `documentation`), utilizando o `jules-flow/templates/task-template.md`. Se os passos do `jules-flow/working-plan.md` (conforme orientação no `README.md` para o Prompt de Planejamento) já representarem objetivos de médio porte, cada um desses passos pode gerar múltiplas tasks (ex: uma `development` para a lógica principal, uma `test` para validá-la, e uma `documentation` se aplicável). Se um passo do `jules-flow/working-plan.md` já for suficientemente atômico para uma única task (ex: 'Criar uma task de `research` para X'), a decomposição pode ser uma tradução direta para uma task.
    4.  Para cada `task` (excluindo a `task-VIS` já criada), preencha cuidadosamente o cabeçalho YAML com `id`, `title`, `type`, `dependencies`, etc. (Se aplicável, preencha o campo `parent_plan_objective_id` com o identificador do objetivo ou passo do `jules-flow/working-plan.md` do qual esta tarefa foi derivada).
    5.  Salve cada nova `task` no diretório `jules-flow/backlog/` usando `create_file_with_block`.
    6.  **Verificar Criação Física das Tasks**: Para cada task que você tentou criar no passo anterior (e incluindo a `task-VIS`), use `ls('jules-flow/backlog/')` e verifique se o arquivo `.md` correspondente à task existe fisicamente. Mantenha uma lista mental ou temporária apenas das tasks cuja criação física foi confirmada. Se alguma task não foi criada, isso será mencionado no anúncio final desta fase (Passo 9).
    7.  **Garantir o Cabeçalho do Índice**: Antes de popular o `jules-flow/task-index.md`, verifique se ele contém o cabeçalho da tabela. Se o arquivo estiver vazio ou o cabeçalho estiver ausente, escreva a seguinte estrutura no início do arquivo:
        ```markdown
        # Índice de Tarefas Jules-Flow

        | ID da Tarefa | Título | Tipo | Status | Prioridade | Dependências | Atribuído |
        |--------------|--------|------|--------|------------|--------------|-----------|
        ```
    8.  Popule o `jules-flow/task-index.md` com as informações de todas as **tasks confirmadas fisicamente no passo 6 desta fase** (incluindo a `task-VIS`), e também com as tarefas de `research` da Fase 1 (cuja criação física também deve ter sido verificada e apenas as confirmadas adicionadas ao índice), garantindo que todas tenham o status inicial "backlog".
    9.  Ao final desta fase, anuncie que o setup foi concluído e informe qual será a primeira `task` a ser executada na fase seguinte. Se houver discrepâncias na criação de tasks (identificadas no Passo 6), mencione-as claramente ao usuário.

#### Fase 3: Execução Inteligente e Dinâmica de Tarefas
* **Objetivo**: Executar as tarefas de forma ordenada e segura.
* **Ação**: Repetidamente selecione e execute a próxima tarefa disponível do `jules-flow/backlog/` até que o backlog esteja vazio ou ocorra uma interrupção (falha, necessidade de replanejamento).

* **Seleção da Próxima Tarefa**:
    1.  Consulte o `jules-flow/task-index.md` para identificar todas as tarefas no `jules-flow/backlog/` cujas `dependencies` listadas já estão com status "done".
    2.  Dentre estas tarefas disponíveis, selecione aquela com a maior prioridade. A ordem de prioridade é: `high`, `medium`, `low`.
    3.  Se houver múltiplas tarefas disponíveis com a mesma prioridade mais alta, use o ID da tarefa como critério de desempate (processe o menor ID primeiro).
    4.  Se nenhuma tarefa estiver disponível (todas as tarefas no backlog possuem dependências não concluídas), anuncie que não há tarefas prontas para execução e aguarde. (Isso pode indicar um ciclo de dependências ou um erro no planejamento).

* **Ciclo de Execução da Tarefa**:
  Uma vez que uma tarefa (`task-XXX.md`) é selecionada:
  1. **Mover para "Em Progresso"**:
     a.  Leia o conteúdo da `task-XXX.md` de `jules-flow/backlog/`.
     b.  Crie um novo arquivo `jules-flow/in_progress/task-XXX.md` com esse conteúdo.
     c.  Use `ls('jules-flow/in_progress/')` para verificar se `task-XXX.md` foi criado com sucesso.
     d.  **Somente se a verificação em (c) for positiva**, delete o arquivo original `jules-flow/backlog/task-XXX.md`. Caso contrário, relate um erro crítico na movimentação e pare, solicitando intervenção do usuário.
     e.  Atualize o status da `task-XXX` para `in_progress` no `jules-flow/task-index.md`.
     f.  Este passo marca o início formal do trabalho na tarefa.
  2. **Consulta de Conhecimento**: Antes de iniciar as modificações, verifique o arquivo `VISION.md` (se existir na raiz do projeto) para obter um entendimento contextual do projeto. Em seguida, verifique o diretório `jules-flow/docs/reference/`. Analise os arquivos de referência existentes para determinar se algum deles é relevante para a `task` atual.
  3. **Executar**: Realize as alterações de código, testes ou pesquisa.
     *   **Princípio do Escopo Estrito de Modificação**: Você está estritamente proibido de alterar, mover ou excluir qualquer arquivo que não esteja explicitamente listado na seção "Arquivos Relevantes" da `task` ativa. Siga este princípio rigorosamente.
     *   **Necessidade de Pesquisa Dinâmica**: Se, durante a execução desta tarefa (especialmente `development` ou `fix`), você identificar a necessidade de uma pesquisa aprofundada que não foi prevista na Fase 1 e que é crucial para o progresso:
         1.  Documente a necessidade de pesquisa no campo `execution_details` do "Relatório de Execução" da tarefa atual (`jules-flow/in_progress/task-XXX.md`).
         2.  Crie uma nova `task` (ex: `task-ZZZ.md`) do tipo `research` no diretório `jules-flow/backlog/` (verifique sua criação física).
         3.  No `jules-flow/task-index.md`, adicione a nova task `task-ZZZ` com status "backlog" e adicione o ID `task-ZZZ` à lista de `dependencies` da tarefa atual (`task-XXX`).
         4.  No arquivo da tarefa atual (`jules-flow/in_progress/task-XXX.md`), adicione o ID `task-ZZZ` ao campo `discovered_research_needed: []` no cabeçalho YAML.
         5.  **Mover Tarefa Atual para Espera**:
             a.  Leia o conteúdo de `jules-flow/in_progress/task-XXX.md`.
             b.  Crie `jules-flow/backlog/task-XXX.md` com esse conteúdo.
             c.  Verifique com `ls('jules-flow/backlog/')` a criação de `task-XXX.md`.
             d.  **Somente se verificado**, delete `jules-flow/in_progress/task-XXX.md`. Senão, reporte erro crítico.
             e.  Atualize o status de `task-XXX` no `jules-flow/task-index.md` para `paused_research` (ou `paused_dependency` se preferir um termo mais genérico).
         6.  Anuncie a situação ao usuário (via `message_user`), explicando a criação da nova tarefa de pesquisa `task-ZZZ` e que a tarefa original `task-XXX` foi pausada/devolvida ao backlog até a conclusão da pesquisa.
         7.  A próxima tarefa a ser selecionada seguirá a lógica normal (considerando dependências e prioridade), que provavelmente será a `task-ZZZ`.
  4. **Relatar**: Preencha a seção "Relatório de Execução" no arquivo da `task` (em `jules-flow/in_progress/task-XXX.md`). Concentre-se em detalhar `outcome` (sucesso/falha), `outcome_reason` (se falha), `reference_documents_consulted` e `execution_details` (com um log do que foi feito, decisões, etc.). Campos como `start_time`, `end_time`, `duration_minutes` e `files_modified` podem ser preenchidos pela plataforma de execução; revise-os e complemente ou corrija se necessário e se a informação estiver disponível para você. Certifique-se de listar quaisquer documentos de referência consultados no **"Passo 2"** (Consulta de Conhecimento).
  5. **Verificar Sucesso**: Execute os testes ou outras validações para confirmar se a tarefa foi concluída com sucesso.
  6. **Em caso de Sucesso**:
     1.  **Mover para "Concluído"**:
         a.  Leia o conteúdo da `task-XXX.md` de `jules-flow/in_progress/`.
         b.  Crie um novo arquivo `jules-flow/done/task-XXX.md` com esse conteúdo.
         c.  Use `ls('jules-flow/done/')` para verificar se `task-XXX.md` foi criado com sucesso.
         d.  **Somente se a verificação em (c) for positiva**, delete o arquivo original `jules-flow/in_progress/task-XXX.md`. Caso contrário, relate um erro crítico na movimentação e pare.
         e.  Atualize o status da `task-XXX` para `done` no `jules-flow/task-index.md`.
     2.  **Geração Automática de Tarefa de Teste**: Se a tarefa concluída for do tipo `development`, crie automaticamente uma nova tarefa do tipo `test` (verifique sua criação física em `jules-flow/backlog/`). O título deve ser "Testes para a task-XXX", onde XXX é o ID da tarefa de desenvolvimento. Na descrição, detalhe que o objetivo é validar a funcionalidade recém-implementada. Coloque esta nova tarefa no diretório `jules-flow/backlog/` e adicione-a ao `jules-flow/task-index.md`.
  7.  **Em caso de Falha**:
      *   Analise o log de erro no "Relatório de Execução" da `task-XXX.md` (em `jules-flow/in_progress/`).
      *   **Sub-fluxo: Falha por Dependência de Sistema Ausente:** Se a tarefa for do tipo `test` ou `development` (ou qualquer tipo que execute comandos externos) e o log de erro indicar claramente um comando não encontrado ou uma biblioteca de sistema ausente (ex: "comando_X: not found", "error while loading shared libraries: libXYZ.so.0"):
          1.  Tente identificar o nome do pacote de sistema faltante com base na mensagem de erro.
          2.  Se um pacote for identificado:
              a.  Adicione o comando de instalação apropriado (ex: `sudo apt-get install -y nome_do_pacote`) ao final do arquivo `jules_bootstrap.sh` (antes da linha `echo "Bootstrap script concluído."`). Certifique-se de não duplicar comandos se possível.
              b.  Preencha o "Relatório de Execução" da tarefa atual (`task-XXX.md`) com os detalhes da falha, o pacote identificado e a ação de atualização do `jules_bootstrap.sh`.
              c.  **Mover Tarefa para Espera de Ambiente**:
                  i.  Leia o conteúdo de `jules-flow/in_progress/task-XXX.md`.
                  ii. Crie `jules-flow/backlog/task-XXX.md` com esse conteúdo.
                  iii.Verifique com `ls('jules-flow/backlog/')` a criação de `task-XXX.md`.
                  iv. **Somente se verificado**, delete `jules-flow/in_progress/task-XXX.md`. Senão, reporte erro crítico.
                  v.  Atualize o status da `task-XXX` no `jules-flow/task-index.md` para `paused_environment`.
              d.  Anuncie a falha ao usuário, informando o ID da tarefa, a dependência ausente identificada, que o `jules_bootstrap.sh` foi atualizado, e que **a VM precisa ser reiniciada com o novo script de bootstrap** para que a tarefa possa ser retomada com sucesso. Exemplo: "Atenção: A tarefa `task-ABC` falhou devido à ausência da dependência '[pacote]'. Atualizei `jules_bootstrap.sh` para incluí-la. Por favor, reinicie a sessão de trabalho para que a VM seja provisionada com o bootstrap atualizado, e então a tarefa `task-ABC` (atualmente `paused_environment`) poderá ser re-executada."
              e.  Pare o trabalho subsequente e aguarde a intervenção do usuário (re-início da sessão/VM).
          3.  Se nenhum pacote específico puder ser identificado claramente, ou se a falha persistir após uma tentativa de correção de bootstrap, proceda com o fluxo de falha padrão abaixo.
      *   **Fluxo de Falha Padrão:** Se não for uma falha de dependência de sistema tratável conforme acima, ou se a tentativa de correção de bootstrap não resolver:
          1.  **Mover para "Falha"**:
              a.  Leia o conteúdo da `task-XXX.md` de `jules-flow/in_progress/`.
              b.  Crie um novo arquivo `jules-flow/failed/task-XXX.md` com esse conteúdo.
              c.  Use `ls('jules-flow/failed/')` para verificar se `task-XXX.md` foi criado com sucesso.
              d.  **Somente se a verificação em (c) for positiva**, delete o arquivo original `jules-flow/in_progress/task-XXX.md`. Caso contrário, relate um erro crítico na movimentação.
          2.  Atualize o status da `task-XXX` para `failed` no `jules-flow/task-index.md`.
          3.  Preencha o "Relatório de Execução" (no arquivo agora em `jules-flow/failed/`) com os logs de erro relevantes.
          4.  Anuncie a falha e o ID da tarefa que falhou, e pare todo o trabalho subsequente. Se estiver executando um lote de tarefas, a falha de uma tarefa interrompe o processamento do restante do lote.
  8.  **Registro de Conclusão da Tarefa**: Documente o resultado da tarefa. Todas as alterações de tarefas concluídas com sucesso serão incluídas no commit final do branch, criado ao submeter o plano de trabalho completo usando a ferramenta `submit`.
  
#### Fase 4: Geração de Relatório e Finalização

* **Objetivo**: Consolidar o trabalho e limpar o ambiente.
* **Ação**:
    1.  Verifique se os diretórios `jules-flow/backlog/`, `jules-flow/in_progress/` e `jules-flow/failed/` estão vazios.
    2.  Compile um relatório final (`final-report-YYYYMMDDHHMMSS.md`) a partir das seções "Relatório de Execução" de todas as tarefas em `jules-flow/done/`.
    3.  Salve o relatório em `jules-flow/final-reports/`.
    4.  Limpe o conteúdo dos diretórios de estado (`jules-flow/backlog/`, `jules-flow/in_progress/`, `jules-flow/done/`, `jules-flow/failed/`), excluindo todos os arquivos de tarefa (`.md`) de dentro deles. Preserve os próprios diretórios e quaisquer arquivos `.gitkeep` que eles contenham. Limpe o conteúdo do arquivo `jules-flow/task-index.md`, preservando o arquivo em si e o cabeçalho da tabela para o próximo ciclo de trabalho.

#### Fase 5: Atualização da Documentação do Projeto

* **Objetivo**: Manter a documentação do projeto sincronizada com as mudanças.
* **Ação**:
    1.  Com base no relatório final (e no conteúdo das tarefas em `jules-flow/done/`), identifique os impactos e os arquivos de documentação do projeto que necessitam de atualização.
    2.  Crie uma `task` do tipo `documentation`. Na descrição da tarefa, liste os arquivos de documentação que você identificou para atualização e um resumo das mudanças planejadas para cada um.
    3.  Execute a tarefa, atualizando os arquivos de documentação listados.

#### Fase 6: Verificação de Inconsistências e Refatoração

* **Objetivo**: Garantir a qualidade e a consistência do código antes do merge.
* **Ação**:
    1.  Execute uma análise completa do código modificado.
    2.  Verifique se há inconsistências, duplicação de código ou oportunidades de refatoração.
    3.  Crie `task`s de `refactor` ou `fix` se necessário e execute-as.

---

### Processo Especial: Adaptação de Plano em Execução

Este processo é acionado por Jules durante a **Fase 3 (Execução Inteligente e Dinâmica de Tarefas)** caso seja identificada uma necessidade de alteração significativa no `working-plan.md` que vá além da criação de simples tarefas de `fix` ou `research` pontuais.

* **Gatilho**: Jules identifica que o plano de trabalho original é inviável, subótimo, ou que novas informações requerem uma mudança estratégica na abordagem definida no `jules-flow/working-plan.md`.
* **Ação por Jules**:
    1.  **Pausar Tarefa Atual**: Se uma tarefa (`task-XXX.md`) estiver em `jules-flow/in_progress/`:
        *   Documente o motivo da pausa e a necessidade de replanejamento no "Relatório de Execução" da `task-XXX.md`.
        *   **Mover Tarefa para Espera de Replanejamento**:
            a.  Leia o conteúdo de `jules-flow/in_progress/task-XXX.md`.
            b.  Crie `jules-flow/backlog/task-XXX.md` com esse conteúdo.
            c.  Verifique com `ls('jules-flow/backlog/')` a criação de `task-XXX.md`.
            d.  **Somente se verificado**, delete `jules-flow/in_progress/task-XXX.md`. Senão, reporte erro crítico.
            e.  Atualize o status de `task-XXX` no `jules-flow/task-index.md` para `paused_replan`.
    2.  **Criar Task de Replanejamento**:
        *   Crie uma nova tarefa (`task-YYY.md`) no diretório `jules-flow/backlog/` do tipo `replan` (verifique sua criação física).
        *   **Título da Task `replan`**: "Revisão e Adaptação Necessária do Working-Plan"
        *   **Descrição da Task `replan`**: Detalhe a análise que levou à necessidade de replanejamento, os problemas com o plano atual, e sugestões (se houver) para o novo plano. Indique claramente que o `jules-flow/working-plan.md` precisa ser atualizado pelo Desenvolvedor Humano.
        *   **Prioridade da Task `replan`**: `high`.
        *   Adicione esta task `replan` ao `jules-flow/task-index.md` com status "backlog".
    3.  **Notificar o Usuário**:
        *   Envie uma mensagem ao usuário (via ferramenta `message_user` ou outro mecanismo de notificação) informando sobre a criação da task `replan` (task-YYY), a pausa do trabalho na `task-XXX` e a necessidade de intervenção humana para atualizar o `jules-flow/working-plan.md`.
        *   Exemplo de mensagem: "Atenção: Identifiquei a necessidade de uma revisão estratégica do `jules-flow/working-plan.md` ao trabalhar na `task-XXX`. Criei a tarefa `task-YYY` (replan) com os detalhes. A tarefa `task-XXX` foi movida para `paused_replan`. O trabalho no plano atual está pausado até que o `jules-flow/working-plan.md` seja atualizado e as novas diretrizes sejam fornecidas."
    4.  **Aguardar Instruções**: Jules não deve prosseguir com outras tarefas do plano original (a menos que explicitamente instruído a fazê-lo em tarefas independentes) até que o processo de replanejamento seja concluído pelo usuário.
* **Ação pelo Desenvolvedor Humano**:
    1.  Analisar a task `replan` (task-YYY).
    2.  Atualizar o arquivo `jules-flow/working-plan.md` com a nova estratégia.
    3.  **Mover Task de Replanejamento para Concluído**:
        a.  Leia o conteúdo da `task-YYY.md` de `jules-flow/backlog/` (ou onde estiver).
        b.  Crie um novo arquivo `jules-flow/done/task-YYY.md` com esse conteúdo.
        c.  Use `ls('jules-flow/done/')` para verificar se `task-YYY.md` foi criado com sucesso.
        d.  **Somente se a verificação em (c) for positiva**, delete o arquivo original. Senão, reporte erro.
        e.  Atualize o status da `task-YYY` para `done` no `jules-flow/task-index.md`.
    4.  Instruir Jules a retomar o trabalho, o que pode envolver:
        *   Descartar tarefas antigas do `jules-flow/backlog/` que não são mais relevantes (instruindo Jules a deletá-las e atualizar o índice).
        *   Instruir Jules a executar uma "Fase 2 Parcial" (Preparação e Decomposição do Plano) focada apenas nas seções novas ou alteradas do `jules-flow/working-plan.md` para gerar novas tasks.
        *   Instruir Jules a continuar a Fase 3 com as tarefas novas e existentes.

---

#### Fase 7: Desenvolvimento Conjunto Humano-Jules (Modo Co-Dev)

* **Objetivo**: Facilitar um ciclo de desenvolvimento e depuração altamente interativo entre o desenvolvedor humano e Jules, trabalhando no mesmo branch e com feedback rápido.
* **Gatilho**: O usuário invoca esta fase através do "Prompt 2.8: Iniciar Modo de Desenvolvimento Conjunto (Co-Dev)".
* **Ação**:

    1.  **Análise e Preparação Inicial**:
        *   Anuncie ao usuário: "Iniciando Modo de Desenvolvimento Conjunto (Co-Dev)."
        *   Realize uma breve análise do estado atual do repositório, incluindo a estrutura do diretório `jules-flow/` e o arquivo `jules-flow/task-index.md`.
        *   Obtenha o nome do branch Git atual em que você está operando. Você pode precisar usar um comando como `git rev-parse --abbrev-ref HEAD` ou similar através da ferramenta `run_in_bash_session`. Se não for possível obter o nome do branch de forma programática, informe ao usuário e peça que ele forneça o nome do branch.
        *   Anuncie ao usuário: "Estou pronto para o Modo Co-Dev. Estou trabalhando no branch: `<nome-do-branch>`."
        *   Instrua o usuário a preparar seu ambiente local com os seguintes comandos, substituindo `<nome-do-branch-de-Jules>` pelo nome do branch real:
            ```text
            Por favor, prepare seu ambiente local. Execute os seguintes comandos no seu terminal:
            1. Certifique-se de que seu repositório local está atualizado com o remoto:
               git fetch origin
            2. Faça o checkout do branch em que estou trabalhando:
               git checkout <nome-do-branch-de-Jules>
            3. Garanta que seu branch local está sincronizado com o meu último commit (se você já tinha o branch):
               git pull origin <nome-do-branch-de-Jules>
            Avise-me quando estiver pronto.
            ```
        *   Aguarde a confirmação do usuário de que ele está pronto.

    2.  **Seleção de Tarefa para Trabalho Conjunto**:
        *   Após a confirmação do usuário, leia o `jules-flow/task-index.md`.
        *   Liste todas as tarefas que **não** possuem o status `done`. Para cada tarefa, apresente o ID, Título e Status atual.
        *   Peça ao usuário: "Por favor, escolha o ID da tarefa na qual gostaria de trabalhar em conjunto."
        *   Aguarde a resposta do usuário com o ID da tarefa. Se o ID fornecido não for válido, informe e peça novamente.

    3.  **Ciclo Iterativo de Desenvolvimento e Depuração da Tarefa Selecionada**:
        *   Seja `task-CURRENT.md` a tarefa escolhida pelo usuário.
        *   **Jules: Análise e Modificação**
            1.  Anuncie: "Ok, vamos trabalhar na tarefa `<ID_da_tarefa_escolhida> - <Título_da_tarefa_escolhida>`."
            2.  Leia o conteúdo de `task-CURRENT.md` (do seu diretório correspondente: `jules-flow/backlog/`, `jules-flow/in_progress/`, ou `jules-flow/failed/`).
            3.  Analise os arquivos relevantes listados na tarefa e o objetivo da tarefa.
            4.  Realize as modificações de código, configuração ou outros arquivos necessários para progredir na tarefa.
            5.  Prepare uma mensagem para o usuário explicando:
                *   O que foi modificado.
                *   Por que foi modificado (racional).
                *   Uma lista dos arquivos que foram alterados/criados/excluídos.
            6.  **Commit das Modificações**:
                *   Use a ferramenta `run_in_bash_session` para adicionar as alterações ao staging do Git (ex: `git add .` ou `git add <arquivo_específico>`).
                *   Use a ferramenta `run_in_bash_session` para fazer um commit. A mensagem do commit deve ser descritiva, por exemplo: `git commit -m "Co-Dev: Progresso na task <ID_da_tarefa> - <breve descrição das mudanças>"`.
                *   **Importante**: Este commit é feito no seu ambiente (VM).
            7.  Envie a mensagem preparada (do passo 3.a.v) ao usuário. Adicione a seguinte instrução: "Fiz o commit das alterações. Por favor, execute `git pull origin <nome-do-branch-de-Jules>` no seu ambiente local para obter as últimas modificações."

        *   **Usuário: Sincronização, Execução e Feedback**
            1.  Aguarde o usuário confirmar que fez o `git pull` e está pronto para o próximo passo.
            2.  Instrua o usuário sobre quais comandos ele deve rodar no ambiente local dele para testar/validar as modificações. Seja específico. Por exemplo:
                *   "Por favor, execute `npm test` e me envie a saída completa do console."
                *   "Compile o projeto com `mvn clean install` e me informe se houve erros. Se sim, envie o log do erro."
                *   "Rode a aplicação e tente acessar a funcionalidade X. Descreva o comportamento e envie quaisquer erros do console do navegador ou logs do servidor."
            3.  Peça ao usuário para enviar o resultado (output de console, logs, descrição do comportamento, etc.).
            4.  Aguarde o feedback do usuário.

        *   **Jules: Análise do Feedback e Nova Iteração**
            1.  Analise cuidadosamente o feedback fornecido pelo usuário.
            2.  Se o feedback indicar que a tarefa foi concluída com sucesso ou o problema foi resolvido:
                *   Pergunte ao usuário: "Com base no seu feedback, parece que esta parte está funcionando. Você concorda que podemos considerar esta iteração/tarefa como concluída, ou há mais algo a fazer nesta tarefa específica?"
                *   Se o usuário concordar que a tarefa (ou o objetivo da iteração atual) está concluída:
                    *   Atualize o status da `task-CURRENT.md` para `done` no `jules-flow/task-index.md` e mova o arquivo para `jules-flow/done/` (se aplicável, conforme o estado original da tarefa).
                    *   Anuncie: "Ótimo! Marquei a tarefa `<ID_da_tarefa>` como 'done'. Gostaria de selecionar outra tarefa para o Modo Co-Dev, ou podemos finalizar a sessão de Co-Dev?"
                    *   Se o usuário quiser continuar com outra tarefa, volte para o Passo 2 (Seleção de Tarefa).
                    *   Se o usuário quiser finalizar, vá para o Passo 4 (Finalização do Modo Co-Dev).
            3.  Se o feedback indicar problemas, erros ou comportamento inesperado:
                *   Anuncie: "Entendido. Analisando o problema..."
                *   Tente identificar a causa raiz do problema com base no feedback.
                *   Volte para o início do **Passo 3.a (Jules: Análise e Modificação)** para implementar correções. O ciclo se repete.

    4.  **Finalização do Modo Co-Dev**:
        *   Este passo é alcançado se o usuário indicar que deseja finalizar a sessão de Co-Dev (após concluir uma tarefa ou a qualquer momento).
        *   Anuncie: "Modo de Desenvolvimento Conjunto (Co-Dev) finalizado. Retornando ao fluxo de trabalho padrão."
        *   Aguarde novas instruções do usuário (por exemplo, para iniciar outra fase do Jules-Flow).

    **Notas Importantes para o Modo Co-Dev**:
    *   **Commits Incrementais**: O objetivo é que Jules faça commits pequenos e frequentes no branch compartilhado. A ferramenta `submit` normalmente é usada para finalizar um conjunto maior de trabalho. Se `submit` for a única maneira de Jules criar um commit e "empurrar" para o usuário, o fluxo acima precisará ser ajustado. Idealmente, Jules usaria comandos `git` via `run_in_bash_session` para `add` e `commit` localmente na VM, e o usuário faria `pull` dessas alterações. O "push" do branch de Jules para o `origin` deve ser coordenado ou assumido como parte do ambiente de Jules.
    *   **Clareza nas Instruções**: É crucial que Jules seja muito claro nas instruções para o usuário (quais comandos rodar, qual feedback enviar).
    *   **Gerenciamento de Estado da Tarefa**: As tarefas ainda devem seguir o sistema de gerenciamento de estado do Jules-Flow (`task-index.md`, movimentação entre diretórios) na medida do possível, especialmente ao concluir uma tarefa.