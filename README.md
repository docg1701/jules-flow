# Jules-Flow System README

## Propósito do Sistema

Jules-Flow é um sistema de gerenciamento de microtarefas projetado para ser operado pelo agente de IA, Jules, sob a supervisão de um Desenvolvedor humano. O sistema é inteiramente baseado em arquivos Markdown e visa estruturar e rastrear o trabalho de desenvolvimento realizado por Jules de forma transparente, documentada e com alta qualidade.

## Fluxo do Sistema

O fluxo de trabalho no Jules-Flow é centrado em arquivos de tarefa em Markdown e organizado em fases sequenciais para garantir robustez e consistência. Cada épico de desenvolvimento é decomposto em tarefas que seguem este fluxo:

1.  **Fase 1: Descoberta e Pesquisa**
    * Antes de qualquer desenvolvimento, uma `TASK` é criada para pesquisar a documentação mais recente das tecnologias a serem utilizadas. O resultado é compilado em um arquivo de referência salvo em `/jules-flow/docs/reference/`, garantindo que o trabalho seja baseado em informações atuais.

2.  **Fase 2: Execução de Desenvolvimento**
    * Com base na pesquisa e nos requisitos da tarefa, Jules realiza as alterações de código necessárias no branch `jules`. Ao concluir, um "Relatório de Execução" detalhado é preenchido no arquivo da `TASK`.

3.  **Fase 3: Testes Automatizados**
    * Após a implementação, uma nova `TASK` é criada para escrever e executar testes automatizados (unitários, de integração, etc.). Isso valida que as novas funcionalidades funcionam como esperado e que não há regressões no código existente.

4.  **Fase 4: Atualização da Documentação**
    * Ao final de um ciclo de desenvolvimento, uma `TASK` é executada para atualizar toda a documentação do projeto (`README.md`, `ROADMAP.md`, etc.), garantindo que ela permaneça sincronizada com o estado atual do código.

5.  **Fase 5: Verificação de Inconsistências**
    * Como etapa final de um bloco de trabalho, uma `TASK` de revisão é executada para analisar o conjunto de alterações, identificar possíveis inconsistências ou conflitos e, se necessário, criar novas tarefas para correção.

## Estrutura de Pastas

* `/jules_flow/`: Diretório raiz do sistema Jules-Flow.
    * `/backlog/`: Contém tarefas pendentes (arquivos `.md`).
    * `/done/`: Contém tarefas concluídas (arquivos `.md`).
    * `/docs/`: Contém documentação gerada pelo Jules-Flow.
        * `/reference/`: Arquivos de referência criados durante a fase de pesquisa.
    * `/templates/`: Contém o modelo `task_template.md` para novas tarefas.
    * `README.md`: Este arquivo.
    * `INSTRUCTIONS_FOR_JULES.md`: Instruções operacionais para o agente Jules.
    * `TASK_INDEX.md`: Índice e relatório de progresso de todas as tarefas.
