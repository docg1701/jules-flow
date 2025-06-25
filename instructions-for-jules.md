# Instruções Operacionais para Jules

Este documento detalha o fluxo de trabalho completo que você, Jules, deve seguir. Sua aderência estrita a estas fases e princípios é crucial para a organização e o sucesso do projeto.

---

### Princípios Fundamentais

1.  **Comunicação via Commits**: Cada passo significativo que você toma deve ser registrado através de um commit atômico e bem descrito.
2.  **Gestão de Estado por Arquivos**: O estado do projeto é definido pela localização dos arquivos de tarefa (`task-XXX.md`) nos diretórios `/backlog/`, `/in_progress/`, `/done/` e `/failed/`.
3.  **Índice Central**: O arquivo `task-index.md` é a única fonte da verdade para o status e o histórico de todas as tarefas. Mantenha-o sempre atualizado.
4.  **Escopo Estrito de Modificação**: Você está estritamente proibido de alterar, mover ou excluir qualquer arquivo que não esteja explicitamente listado na seção "Arquivos Relevantes" da `task` ativa.
5.  **Imutabilidade do Status no YAML**: O cabeçalho `status:` dentro do arquivo `.md` da tarefa é definido apenas no momento de sua criação (normalmente como `backlog`) e **não deve ser alterado por Jules**. O status de progresso real da tarefa é gerenciado exclusivamente pela movimentação do arquivo da tarefa entre os diretórios `/backlog/`, `/in_progress/`, `/done/` e `/failed/`, e refletido no `task-index.md`.
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
    3.  **Commit de Preparação**: Execute um commit com a mensagem "Setup: Limpeza do ambiente de trabalho para novo branch.".
    4.  **Análise do Plano**: Leia o `working-plan.md`. Extraia e armazene o timestamp do branch, que está na primeira linha. Identifique as áreas que exigem pesquisa de documentação.
    5.  **Criação das Tarefas de Pesquisa**: Crie `task`s do tipo `research` para cada tópico identificado.
    6.  **Execução da Pesquisa**: Para cada `task` de pesquisa, execute a busca por documentação oficial e compile os resultados em arquivos de referência dentro de `jules-flow/docs/reference/`.

#### Fase 2: Preparação e Decomposição do Plano

* **Objetivo**: Converter o `working-plan.md` em tarefas executáveis.
* **Ação**:
    1.  Leia o `working-plan.md` para entender o escopo completo.
    2.  Decomponha o plano em uma série de `task`s atômicas (como `development`, `test`, `documentation`), utilizando o `templates/task-template.md`.
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

### Fase 3: Execução Inteligente e Dinâmica de Tarefas

* **Objetivo**: Executar as tarefas de forma ordenada e segura.
* **Ação**: Siga o ciclo de execução para cada tarefa, uma de cada vez.
* **Ciclo de Execução da Tarefa**:
  a.  **Mover para "Em Progresso"**: Mova o arquivo da tarefa de `/backlog/` para `/in_progress/` e atualize seu status para "in_progress" no `task-index.md`. Realize um commit para registrar o início do trabalho.
  b.  **Consulta de Conhecimento**: Antes de iniciar as modificações, verifique o diretório `jules-flow/docs/reference/`. Analise os arquivos de referência existentes para determinar se algum deles é relevante para a `task` atual.
  c.  **Executar**: Realize as alterações de código, testes ou pesquisa, respeitando o "Escopo Estrito de Modificação".
  d.  **Relatar**: Preencha detalhadamente a seção "Relatório de Execução" no arquivo da `task`. Certifique-se de listar quaisquer documentos de referência consultados na etapa 'b'.
  e.  **Verificar Sucesso**: Execute os testes ou outras validações para confirmar se a tarefa foi concluída com sucesso.
  f.  **Em caso de Sucesso**:
      i.  Mova o arquivo da tarefa de `/in_progress/` para `/done/` e atualize seu status para "done" no `task-index.md`.
      ii. **Geração Automática de Tarefa de Teste**: Se a tarefa concluída for do tipo `development`, crie automaticamente uma nova tarefa do tipo `test`. O título deve ser "Testes para a task-XXX", onde XXX é o ID da tarefa de desenvolvimento. Na descrição, detalhe que o objetivo é validar a funcionalidade recém-implementada. Coloque esta nova tarefa no diretório `/backlog/` e adicione-a ao `task-index.md`.
  g.  **Em caso de Falha**: Mova o arquivo da tarefa de `/in_progress/` para `/failed/`, atualize seu status para "failed" no `task-index.md`, e preencha o "Relatório de Execução" com os logs de erro relevantes. Anuncie a falha e pare todo o trabalho subsequente.
  h.  **Commit Atômico Final**: Realize o commit atômico referente à conclusão (sucesso ou falha) desta `task`.

#### Fase 4: Geração de Relatório e Finalização

* **Objetivo**: Consolidar o trabalho e limpar o ambiente.
* **Ação**:
    1.  Verifique se os diretórios `/backlog/`, `/in_progress/` e `/failed/` estão vazios.
    2.  Compile um relatório final (`final-report-YYYYMMDDHHMMSS.md`) a partir das seções "Relatório de Execução" de todas as tarefas em `/done/`.
    3.  Salve o relatório em `jules-flow/final-reports/`.
    4.  Exclua os diretórios de estado (`/backlog/`, `/in_progress/`, `/done/`, `/failed/`) e o `task-index.md`.

#### Fase 5: Atualização da Documentação do Projeto

* **Objetivo**: Manter a documentação do projeto sincronizada com as mudanças.
* **Ação**:
    1.  Com base no relatório final, identifique os impactos na documentação.
    2.  Crie uma `task` do tipo `documentation`.
    3.  Execute a tarefa, atualizando os arquivos de documentação necessários (ex: `README.md`, `docs/guides/*`).

#### Fase 6: Verificação de Inconsistências e Refatoração

* **Objetivo**: Garantir a qualidade e a consistência do código antes do merge.
* **Ação**:
    1.  Execute uma análise completa do código modificado.
    2.  Verifique se há inconsistências, duplicação de código ou oportunidades de refatoração.
    3.  Crie `task`s de `refactor` ou `fix` se necessário e execute-as.
