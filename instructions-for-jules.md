# Instruções de Operação para Jules

Este documento detalha os princípios e o fluxo de trabalho que você, Jules, deve seguir estritamente para o desenvolvimento de projetos. Seu propósito é garantir um desenvolvimento transparente, rastreável, documentado e de alta qualidade, guiado por um plano pré-definido.

## Princípios Gerais

1.  **Comunicação Formal**: Você não deve usar emojis em nenhuma comunicação, documentação ou código. A comunicação deve ser clara, técnica e direta.
2.  **Formato de Arquivo Padrão**: Todos os arquivos de documentação, tarefas e relatórios que você criar devem ser escritos em Markdown e utilizar a extensão `.md`.
3.  **Commit Atômico por Tarefa**: Cada `task` concluída deve resultar em um único commit atômico. Este commit deve conter todas as alterações relacionadas àquela tarefa específica: modificações de código, a atualização no arquivo da `task` movida para `/done/`, e a atualização do `task-index.md`.
4.  **Escopo Estrito de Modificação**: Você está **estritamente proibido** de alterar, adicionar ou excluir qualquer arquivo que não esteja explicitamente listado na seção "Arquivos Relevantes" da `task` ativa. Esta é a sua regra mais crítica.

## O Fluxo de Trabalho Baseado em Plano

Seu trabalho é sempre guiado por um arquivo `jules-flow/working-plan.md`, que é gerado pelo desenvolvedor humano antes do início das suas operações em um branch. Você deve seguir as fases abaixo em ordem, acionado pelos prompts fornecidos pelo desenvolvedor.

### Fase 1: Preparação e Decomposição do Plano

Esta fase é iniciada pelo "Prompt de Início dos Trabalhos".

* **Objetivo**: Preparar o ambiente e transformar o plano mestre em tarefas executáveis.
* **Ação**:
    1.  Execute a limpeza do ambiente de trabalho, conforme instruído no prompt.
    2.  Leia o arquivo `jules-flow/working-plan.md`.
    3.  Identifique e armazene o `timestamp` do branch, que está na primeira linha do plano.
    4.  Com base na seção "Passo a Passo da Execução", gere cada um dos arquivos `.md` para todas as `task`s listadas.
    5.  Coloque todos os novos arquivos de `task` no diretório `jules-flow/backlog/`.
    6.  Crie o `task-index.md` inicial, listando todas as tarefas geradas com o status "backlog".

### Fase 2: Execução Sequencial das Tarefas

Esta fase é iniciada pelo "Prompt para Continuar Trabalho no Branch", que pode especificar um número de tarefas a serem executadas em sequência.

* **Objetivo**: Implementar as modificações de código, testes e pesquisas conforme descrito em cada `task`, de forma individual ou em lote.
* **Ação**:
    1.  Verifique o prompt para um número (X) de tarefas a executar. Se nenhum número for fornecido, assuma o padrão X = 1.
    2.  Inicie um laço de repetição para executar até X tarefas.
    3.  A cada iteração do laço:
        a. Verifique se há tarefas no diretório `/backlog/`. Se estiver vazio, encerre o laço e informe que não há mais tarefas pendentes.
        b. Selecione a próxima `task` do diretório `/backlog/`, seguindo a ordem definida.
        c. Realize as alterações necessárias para cumprir os critérios de aceitação, respeitando o "Escopo Estrito de Modificação".
        d. Preencha detalhadamente a seção "Relatório de Execução" no arquivo da `task`.
        e. Mova o arquivo da `task` concluída de `/backlog/` para `/done/`.
        f. Atualize o status da tarefa para "done" no arquivo `task-index.md`.
        g. Realize o commit atômico referente à conclusão desta `task`.
    4.  Ao final do laço (após executar X tarefas ou esvaziar o backlog), apresente um resumo das tarefas concluídas e aguarde o próximo comando.

### Fase 3: Geração de Relatório e Finalização

Esta fase é iniciada pelo "Prompt para Finalizar e Limpar o Branch".

* **Objetivo**: Consolidar os resultados do trabalho, arquivar um relatório final e limpar o ambiente.
* **Ação**:
    1.  Verifique se todas as tarefas planejadas estão na pasta `/done/`.
    2.  Compile os "Relatórios de Execução" de todas as `task`s em um único relatório abrangente.
    3.  Use o `timestamp` do branch (obtido na Fase 1) para nomear o relatório final e salvá-lo em `jules-flow/final-reports/report-<timestamp>.md`.
    4.  Execute a limpeza final do branch: preserve a pasta `/final-reports/`, mas exclua o conteúdo de `/backlog/`, `/done/`, `/docs/reference/` e limpe os arquivos `task-index.md` e `working-plan.md`.
    5.  Realize o commit de finalização para registrar o arquivamento e a limpeza.

### Fase 4: Atualização da Documentação do Projeto

Esta fase é iniciada por um "Prompt para Atualização da Documentação" específico, após a finalização da Fase 3.

* **Objetivo**: Atualizar a documentação principal do projeto de forma controlada, com base no relatório final arquivado.
* **Ação**:
    1.  Leia o relatório final mais recente na pasta `jules-flow/final-reports/`.
    2.  Identifique a lista de arquivos de documentação a serem modificados, que foi fornecida pelo desenvolvedor diretamente no prompt. Se a lista estiver vazia, encerre a operação.
    3.  Para garantir a rastreabilidade, crie uma única `task` do tipo `documentation`. Copie a lista de arquivos do prompt para a seção "Arquivos Relevantes" desta `task`.
    4.  Com base na análise do relatório, edite os arquivos de documentação listados.
    5.  Preencha o "Relatório de Execução" na `task` de documentação, detalhando as alterações.
    6.  Mova a `task` para `/done/` e atualize (ou crie) um `task-index.md` para ela.
    7.  Realize um commit atômico com a mensagem "docs: Atualiza documentação do projeto com base no relatório <timestamp-do-relatorio>".
