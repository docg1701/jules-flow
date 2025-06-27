# Instruções Operacionais para Jules

Este documento detalha o fluxo de trabalho completo que você, Jules, deve seguir. Sua aderência estrita a estas fases e princípios é crucial para a organização e o sucesso do projeto.

---

### Princípios Fundamentais

1.  **Comunicação e Versionamento de Etapas Significativas**: Cada passo significativo que você toma deve ser claramente documentado e versionado. As alterações serão agrupadas e submetidas em um commit final para o branch ao concluir o plano de trabalho aprovado.
2.  **Gestão de Estado por Arquivos**: O estado do projeto é definido pela localização dos arquivos de tarefa (`task-XXX.md`) nos diretórios `/backlog/`, `/in_progress/`, `/done/` e `/failed/`.
3.  **Índice Central como Fonte da Verdade**: O arquivo `task-index.md` é a única fonte da verdade para o status e o histórico de todas as tarefas. Ele **deve ser atualizado** a cada mudança de estado de uma tarefa (ex: de "backlog" para "in_progress").
4.  **Imutabilidade do Status no Arquivo da Tarefa**: O cabeçalho `status:` dentro do arquivo `.md` de uma tarefa é definido apenas no momento de sua criação (normalmente como `backlog`) e **não deve ser alterado manualmente por você**. O progresso é refletido no `task-index.md` e na movimentação do arquivo entre os diretórios.
---

### O Fluxo de Trabalho em 6 Fases

#### Fase 1: Descoberta e Pesquisa

* **Objetivo**: Limpar o ambiente de trabalho, identificar o escopo de pesquisa e buscar o conhecimento necessário.
* **Ação**:
    1.  **Limpeza do Ambiente**: Exclua todos os arquivos dos diretórios `/backlog/`, `/in_progress/`, `/done/`, `/failed/` e `/docs/reference/`.
    2.  **No arquivo `task-index.md`, preserve apenas o cabeçalho e limpe o restante da tabela**. O cabeçalho esperado é:
    	```markdown
    	# Índice de Tarefas Jules-Flow

    	| ID da Tarefa | Título | Tipo | Status | Prioridade | Dependências | Atribuído |
    	|--------------|--------|------|--------|------------|--------------|-----------|
    	```
    3.  **Análise do Plano**: Leia o `working-plan.md`. Identifique as áreas que exigem pesquisa de documentação.
    4.  **Criação das Tarefas de Pesquisa**: Crie `task`s do tipo `research` para cada tópico identificado.
    5.  **Execução da Pesquisa**: Para cada `task` de pesquisa, execute a busca por documentação oficial e compile os resultados em arquivos de referência dentro de `jules-flow/docs/reference/`.

#### Fase 2: Preparação e Decomposição do Plano

* **Objetivo**: Converter o `working-plan.md` em tarefas executáveis.
* **Ação**:
    1.  Leia o `working-plan.md` para entender o escopo completo.
    2.  Decomponha o plano em uma série de `task`s atômicas (como `development`, `test`, `documentation`), utilizando o `templates/task-template.md`. Se os passos do `working-plan.md` (conforme orientação no `README.md` para o Prompt de Planejamento) já representarem objetivos de médio porte, cada um desses passos pode gerar múltiplas tasks (ex: uma `development` para a lógica principal, uma `test` para validá-la, e uma `documentation` se aplicável). Se um passo do `working-plan.md` já for suficientemente atômico para uma única task (ex: 'Criar uma task de `research` para X'), a decomposição pode ser uma tradução direta para uma task.
    3.  Para cada `task`, preencha cuidadosamente o cabeçalho YAML com `id`, `title`, `type`, `dependencies`, etc.
    4.  Salve cada nova `task` no diretório `jules-flow/backlog/`.
    5.  **Garantir o Cabeçalho do Índice**: Antes de popular o `jules-flow/task-index.md`, verifique se ele contém o cabeçalho da tabela. Se o arquivo estiver vazio ou o cabeçalho estiver ausente, escreva a seguinte estrutura no início do arquivo:
        ```markdown
        # Índice de Tarefas Jules-Flow

        | ID da Tarefa | Título | Tipo | Status | Prioridade | Dependências | Atribuído |
        |--------------|--------|------|--------|------------|--------------|-----------|
        ```
    6.  Popule o `jules-flow/task-index.md` com as informações de todas as tarefas criadas nesta fase e as tarefas de `research` da Fase 1, garantindo que todas tenham o status inicial "backlog".
    7.  Ao final desta fase, anuncie que o setup foi concluído e informe qual será a primeira `task` a ser executada na fase seguinte.

#### Fase 3: Execução Inteligente e Dinâmica de Tarefas
* **Objetivo**: Executar as tarefas de forma ordenada e segura.
* **Ação**: Repetidamente selecione e execute a próxima tarefa disponível do `/backlog/` até que o backlog esteja vazio ou ocorra uma interrupção (falha, necessidade de replanejamento).

* **Seleção da Próxima Tarefa**:
    1.  Consulte o `task-index.md` para identificar todas as tarefas no `/backlog/` cujas `dependencies` listadas já estão com status "done".
    2.  Dentre estas tarefas disponíveis, selecione aquela com a maior prioridade. A ordem de prioridade é: `high`, `medium`, `low`.
    3.  Se houver múltiplas tarefas disponíveis com a mesma prioridade mais alta, use o ID da tarefa como critério de desempate (processe o menor ID primeiro).
    4.  Se nenhuma tarefa estiver disponível (todas as tarefas no backlog possuem dependências não concluídas), anuncie que não há tarefas prontas para execução e aguarde. (Isso pode indicar um ciclo de dependências ou um erro no planejamento).

* **Ciclo de Execução da Tarefa**:
  Uma vez que uma tarefa é selecionada:
  1. **Mover para "Em Progresso"**: Mova o arquivo da tarefa de `/backlog/` para `/in_progress/` e atualize seu status para "in_progress" no `task-index.md`. Este passo marca o início formal do trabalho na tarefa.
  2. **Consulta de Conhecimento**: Antes de iniciar as modificações, verifique o diretório `jules-flow/docs/reference/`. Analise os arquivos de referência existentes para determinar se algum deles é relevante para a `task` atual.
  3. **Executar**: Realize as alterações de código, testes ou pesquisa.
     *   **Princípio do Escopo Estrito de Modificação**: Você está estritamente proibido de alterar, mover ou excluir qualquer arquivo que não esteja explicitamente listado na seção "Arquivos Relevantes" da `task` ativa. Siga este princípio rigorosamente.
     *   **Necessidade de Pesquisa Dinâmica**: Se, durante a execução desta tarefa (especialmente `development` ou `fix`), você identificar a necessidade de uma pesquisa aprofundada que não foi prevista na Fase 1 e que é crucial para o progresso:
         1.  Documente a necessidade de pesquisa no campo `execution_details` do "Relatório de Execução" da tarefa atual.
         2.  Crie uma nova `task` (ex: `task-ZZZ.md`) do tipo `research` no diretório `/backlog/`. Detalhe o objetivo da pesquisa, as questões a serem respondidas e por que ela é necessária para a tarefa original.
         3.  No `task-index.md`, adicione a nova task `task-ZZZ` com status "backlog" e adicione o ID `task-ZZZ` à lista de `dependencies` da tarefa atual.
         4.  No arquivo da tarefa atual (em `/in_progress/`), adicione o ID `task-ZZZ` ao campo `discovered_research_needed: []` no cabeçalho YAML.
         5.  Mova a tarefa atual de `/in_progress/` de volta para `/backlog/` (seu status no `task-index.md` deve ser atualizado para "backlog" ou, idealmente, para um novo status "paused_dependency" se o sistema suportar, para indicar que está aguardando uma dependência recém-adicionada).
         6.  Anuncie a situação ao usuário (via `message_user`), explicando a criação da nova tarefa de pesquisa `task-ZZZ` e que a tarefa original foi pausada/devolvida ao backlog até a conclusão da pesquisa.
         7.  A próxima tarefa a ser selecionada seguirá a lógica normal (considerando dependências e prioridade), que provavelmente será a `task-ZZZ` se ela não tiver outras dependências.
  4. **Relatar**: Preencha a seção "Relatório de Execução" no arquivo da `task`. Concentre-se em detalhar `outcome` (sucesso/falha), `outcome_reason` (se falha), `reference_documents_consulted` e `execution_details` (com um log do que foi feito, decisões, etc.). Campos como `start_time`, `end_time`, `duration_minutes` e `files_modified` podem ser preenchidos pela plataforma de execução; revise-os e complemente ou corrija se necessário e se a informação estiver disponível para você. Certifique-se de listar quaisquer documentos de referência consultados no **"Passo 2"** (Consulta de Conhecimento).
  5. **Verificar Sucesso**: Execute os testes ou outras validações para confirmar se a tarefa foi concluída com sucesso.
  6. **Em caso de Sucesso**:
     1.  Mova o arquivo da tarefa de `/in_progress/` para `/done/` e atualize seu status para "done" no `task-index.md`.
     2.  **Geração Automática de Tarefa de Teste**: Se a tarefa concluída for do tipo `development`, crie automaticamente uma nova tarefa do tipo `test`. O título deve ser "Testes para a task-XXX", onde XXX é o ID da tarefa de desenvolvimento. Na descrição, detalhe que o objetivo é validar a funcionalidade recém-implementada. Coloque esta nova tarefa no diretório `/backlog/` e adicione-a ao `task-index.md`.
  7.  **Em caso de Falha**: Mova o arquivo da tarefa de `/in_progress/` para `/failed/`, atualize seu status para "failed" no `task-index.md`, e preencha o "Relatório de Execução" com os logs de erro relevantes. Anuncie a falha e o ID da tarefa que falhou, e pare todo o trabalho subsequente. Se estiver executando um lote de tarefas, a falha de uma tarefa interrompe o processamento do restante do lote.
  8.  **Registro de Conclusão da Tarefa**: Documente o resultado da tarefa. Todas as alterações de tarefas concluídas com sucesso serão incluídas no commit final do branch, criado ao submeter o plano de trabalho completo usando a ferramenta `submit`.
  
#### Fase 4: Geração de Relatório e Finalização

* **Objetivo**: Consolidar o trabalho e limpar o ambiente.
* **Ação**:
    1.  Verifique se os diretórios `/backlog/`, `/in_progress/` e `/failed/` estão vazios.
    2.  Compile um relatório final (`final-report-YYYYMMDDHHMMSS.md`) a partir das seções "Relatório de Execução" de todas as tarefas em `/done/`.
    3.  Salve o relatório em `jules-flow/final-reports/`.
    4.  Limpe o conteúdo dos diretórios de estado (`/backlog/`, `/in_progress/`, `/done/`, `/failed/`), excluindo todos os arquivos de tarefa (`.md`) de dentro deles. Preserve os próprios diretórios e quaisquer arquivos `.gitkeep` que eles contenham. Limpe o conteúdo do arquivo `task-index.md`, preservando o arquivo em si e o cabeçalho da tabela para o próximo ciclo de trabalho.

#### Fase 5: Atualização da Documentação do Projeto

* **Objetivo**: Manter a documentação do projeto sincronizada com as mudanças.
* **Ação**:
    1.  Com base no relatório final (e no conteúdo das tarefas em `/done/`), identifique os impactos e os arquivos de documentação do projeto que necessitam de atualização.
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

* **Gatilho**: Jules identifica que o plano de trabalho original é inviável, subótimo, ou que novas informações requerem uma mudança estratégica na abordagem definida no `working-plan.md`.
* **Ação por Jules**:
    1.  **Pausar Tarefa Atual**: Se uma tarefa estiver em `/in_progress/`:
        *   Documente o motivo da pausa e a necessidade de replanejamento no "Relatório de Execução" da tarefa.
        *   Mova a tarefa de volta para `/backlog/` e atualize seu status para "backlog" ou, preferencialmente, para um novo status "paused" no `task-index.md` (se o sistema de visualização do índice suportar ou se for decidido adicionar este status).
    2.  **Criar Task de Replanejamento**:
        *   Crie uma nova tarefa (`task-YYY.md`) no diretório `/backlog/` do tipo `replan`.
        *   **Título da Task `replan`**: "Revisão e Adaptação Necessária do Working-Plan"
        *   **Descrição da Task `replan`**: Detalhe a análise que levou à necessidade de replanejamento, os problemas com o plano atual, e sugestões (se houver) para o novo plano. Indique claramente que o `working-plan.md` precisa ser atualizado pelo Desenvolvedor Humano.
        *   **Prioridade da Task `replan`**: `high`.
        *   Adicione esta task `replan` ao `task-index.md` com status "backlog".
    3.  **Notificar o Usuário**:
        *   Envie uma mensagem ao usuário (via ferramenta `message_user` ou outro mecanismo de notificação) informando sobre a criação da task `replan` (task-YYY), a pausa do trabalho e a necessidade de intervenção humana para atualizar o `working-plan.md`.
        *   Exemplo de mensagem: "Atenção: Identifiquei a necessidade de uma revisão estratégica do `working-plan.md`. Criei a tarefa `task-YYY` (replan) com os detalhes. O trabalho no plano atual está pausado até que o `working-plan.md` seja atualizado e as novas diretrizes sejam fornecidas."
    4.  **Aguardar Instruções**: Jules não deve prosseguir com outras tarefas do plano original (a menos que explicitamente instruído a fazê-lo em tarefas independentes) até que o processo de replanejamento seja concluído pelo usuário.
* **Ação pelo Desenvolvedor Humano**:
    1.  Analisar a task `replan` (task-YYY).
    2.  Atualizar o arquivo `working-plan.md` com a nova estratégia.
    3.  Arquivar (mover para `/done/`) a task `replan` (task-YYY), atualizando seu status no `task-index.md`.
    4.  Instruir Jules a retomar o trabalho, o que pode envolver:
        *   Descartar tarefas antigas do `/backlog/` que não são mais relevantes.
        *   Instruir Jules a executar uma "Fase 2 Parcial" (Preparação e Decomposição do Plano) focada apenas nas seções novas ou alteradas do `working-plan.md` para gerar novas tasks.
        *   Instruir Jules a continuar a Fase 3 com as tarefas novas e existentes.
