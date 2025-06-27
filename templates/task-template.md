---
id: task-XXX
title: "Título da Tarefa"
type: development | test | research | documentation | fix | refactor # Tipos de tarefa padrão
status: backlog # Status inicial no arquivo da task. Status real é gerenciado no task-index.md e pode incluir: backlog | in_progress | done | failed | paused_environment | paused_research | paused_replan
priority: medium # (Valores: high | medium | low. Default: medium)
dependencies: [] # Lista de IDs de tarefas das quais esta depende, ex: ["task-001", "task-002"]
parent_plan_objective_id: "" # Opcional: ID/Número do objetivo no working-plan.md do qual esta task foi derivada
discovered_research_needed: [] # Lista de IDs de tasks de pesquisa criadas dinamicamente durante a execução desta task
assigned_to: Jules
created_by: Jules
created_at: YYYY-MM-DDTHH:MM:SSZ
updated_at: YYYY-MM-DDTHH:MM:SSZ
tags: []
description: |
  Descrição detalhada da tarefa. O que precisa ser feito? Quais são os critérios de aceitação?

# Não modificar esta seção manualmente. Jules irá preenchê-la.
# ---------------------------------------------------------------
# RELATÓRIO DE EXECUÇÃO (Preenchido por Jules ao concluir/falhar)
# ---------------------------------------------------------------
# outcome: success | failure
# outcome_reason: "" # (Opcional, em caso de falha)
# start_time: YYYY-MM-DDTHH:MM:SSZ # (Preenchido preferencialmente pela plataforma de execução, se disponível)
# end_time: YYYY-MM-DDTHH:MM:SSZ # (Preenchido preferencialmente pela plataforma de execução, se disponível)
# duration_minutes: 0 # (Preenchido preferencialmente pela plataforma de execução, se disponível)
# files_modified: # (Preenchido preferencialmente pela plataforma de execução, se disponível)
#   - caminho/para/arquivo1.ext
#   - caminho/para/arquivo2.ext
# reference_documents_consulted: # Documentos de docs/reference/ que foram consultados
#   - nome_do_documento1.md
# execution_details: |
#   Log detalhado do que foi feito, incluindo comandos executados, decisões tomadas,
#   e quaisquer observações relevantes. Em caso de falha, incluir logs de erro.
# ---------------------------------------------------------------
---

## Arquivos Relevantes (Escopo da Tarefa)
* `caminho/para/arquivo_a_ser_modificado.ext`
* `caminho/para/outro_arquivo_relevante.ext`

## Critérios de Aceitação
1. Critério 1.
2. Critério 2.

## Observações Adicionais
(Qualquer informação extra que possa ser útil para Jules.)
