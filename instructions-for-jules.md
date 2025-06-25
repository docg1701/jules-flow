# Instruções de Operação para Jules

Este documento detalha os princípios e o fluxo de trabalho que você, Jules, deve seguir estritamente para o desenvolvimento de projetos. Seu propósito é garantir um desenvolvimento transparente, rastreável, documentado e de alta qualidade, guiado por um plano pré-definido.

## Princípios Gerais

1.  **Comunicação Formal**: Você não deve usar emojis em nenhuma comunicação, documentação ou código. A comunicação deve ser clara, técnica e direta.
2.  **Formato de Arquivo Padrão**: Todos os arquivos de documentação, tarefas e relatórios que você criar devem ser escritos em Markdown e utilizar a extensão `.md`.
3.  **Commit Atômico por Tarefa**: Cada `task` concluída deve resultar em um único commit atômico. Este commit deve conter todas as alterações relacionadas àquela tarefa específica: modificações de código, a movimentação do arquivo da `task` entre as pastas de estado (`/in_progress/`, `/done/`, `/failed/`), e a atualização do `task-index.md`.
4.  **Escopo Estrito de Modificação**: Você está **estritamente proibido** de alterar, adicionar ou excluir qualquer arquivo que não esteja explicitamente listado na seção "Arquivos Relevantes" da `task` ativa. Esta é a sua regra mais crítica.

## O Fluxo de Trabalho Baseado em Plano

Seu trabalho é sempre guiado por um arquivo `jules-flow/working-plan.md`. Você deve seguir as fases abaixo em ordem, acionado pelos prompts fornecidos pelo desenvolvedor.

### Fase 1: Preparação e Decomposição do Plano

Esta fase é iniciada pelo "Prompt de Início dos Trabalhos".

* **Objetivo**: Preparar o ambiente, transformar o plano mestre em tarefas executáveis e gerar IDs únicos.
* **Ação**:
    1.  Execute a limpeza do ambiente de trabalho, conforme instruído no prompt. Crie os diretórios de estado, se não existirem: `/backlog/`, `/in_progress/`, `/done/`, `/failed/`.
    2.  Leia o arquivo `jules-flow/working-plan.md`.
    3.  Identifique e armazene o `timestamp` do branch, que está na primeira linha do plano.
    4.  Com base na seção "Passo a Passo da Execução", gere os arquivos `.md` para todas as `task`s listadas.
    5.  **Geração de IDs**: Para cada tarefa, gere um ID sequencial (ex: `task-001`, `task-002`, ...), preencha o campo `id` e use este ID para nomear o arquivo (ex: `task-001.md`).
    6.  Coloque todos os novos arquivos de `task` no diretório `jules-flow/backlog/`.
    7.  Crie o `task-index.md` inicial, listando todas as tarefas geradas com o status "backlog".

### Fase 2: Execução Inteligente e Dinâmica de Tarefas

Esta fase é iniciada pelo "Prompt para Continuar Trabalho no Branch". Sua lógica de trabalho é agora baseada em estado e dependências.

* **Objetivo**: Executar tarefas de forma autônoma, respeitando dependências e gerenciando estados de progresso e falha.
* **Ação**:
    1.  **Recuperação de Contexto (Otimizada)**:
        a. Verifique o diretório `/in_progress/`. Se houver uma tarefa, prossiga para a execução dela.
        b. Se `/in_progress/` estiver vazio, verifique o diretório `/failed/`. Se houver tarefas, informe o desenvolvedor, anuncie que o trabalho está bloqueado e aguarde novas instruções. Não prossiga.
        c. Se ambos estiverem vazios, prossiga para a seleção de uma nova tarefa.

    2.  **Seleção de Nova Tarefa (Baseada em Dependências)**:
        a. Analise o diretório `/backlog/`.
        b. Leia o `task-index.md` para saber o status de todas as tarefas.
        c. Identifique a primeira tarefa no backlog que não tenha dependências OU cujas tarefas listadas em `dependencies` já possuam o status "done" no `task-index.md`.
        d. Se nenhuma tarefa puder ser iniciada, informe ao desenvolvedor sobre o bloqueio de dependências e aguarde.
        e. Se uma tarefa for selecionada, anuncie qual será executada.

    3.  **Ciclo de Execução da Tarefa**:
        a. **Mover para "Em Progresso"**: Mova o arquivo da tarefa de `/backlog/` para `/in_progress/` e atualize seu status para "in_progress" no `task-index.md`. Realize um commit para registrar o início do trabalho.
        b. **Executar**: Realize as alterações de código, testes ou pesquisa, respeitando o "Escopo Estrito de Modificação".
        c. **Relatar**: Preencha detalhadamente a seção "Relatório de Execução" no arquivo da `task`.
        d. **Verificar Sucesso**: Execute os testes ou outras validações para confirmar se a tarefa foi concluída com sucesso.
        e. **Em caso de Sucesso**: Mova o arquivo da tarefa de `/in_progress/` para `/done/` e atualize seu status para "done" no `task-index.md`.
        f. **Em caso de Falha**: Mova o arquivo da tarefa de `/in_progress/` para `/failed/`, atualize seu status para "failed" no `task-index.md`, e preencha o "Relatório de Execução" com os logs de erro relevantes. Anuncie a falha e pare todo o trabalho subsequente.
        g. **Commit Atômico Final**: Realize o commit atômico referente à conclusão (sucesso ou falha) desta `task`.

### Fase 3: Geração de Relatório e Finalização

Esta fase é iniciada pelo "Prompt para Finalizar e Limpar o Branch".

* **Objetivo**: Consolidar os resultados, arquivar um relatório final e limpar o ambiente de trabalho.
* **Ação**:
    1.  **Verificação de Sanidade**: Antes de prosseguir, verifique se os diretórios `/in_progress/` e `/failed/` estão vazios. Se não estiverem, recuse a finalização e informe ao desenvolvedor que existem tarefas inacabadas ou com falha.
    2.  Compile os "Relatórios de Execução" de todas as `task`s em `/done/` em um único relatório abrangente.
    3.  Use o `timestamp` do branch (obtido na Fase 1) para nomear o relatório final e salvá-lo em `jules-flow/final-reports/report-<timestamp>.md`.
    4.  Execute a limpeza final: preserve a pasta `/final-reports/`, mas exclua o conteúdo de `/backlog/`, `/in_progress/`, `/done/`, `/failed/`, e `/docs/reference/`, e limpe os arquivos `task-index.md` e `working-plan.md`.
    5.  Realize o commit de finalização.

### Fase 4: Atualização da Documentação do Projeto

Esta fase é iniciada por um prompt específico e ocorre após a finalização bem-sucedida da Fase 3.

* **Objetivo**: Atualizar a documentação principal do projeto de forma controlada.
* **Ação**:
    1.  O fluxo para esta fase permanece o mesmo. Analise o relatório final e a lista de arquivos fornecida no prompt.
    2.  Crie uma única `task` de `documentation` para rastreabilidade, execute as modificações, preencha o relatório e realize o commit atômico.
