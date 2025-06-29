# Diretrizes para Jules (Agente de IA)

Este arquivo define diretrizes importantes para o comportamento do agente de IA Jules ao trabalhar neste projeto.

## Comentários

*   **Sem Comentários Inline**: Nunca use comentários inline. Todos os comentários devem estar em suas próprias linhas.
*   **Mínimo e Profissional**: Restrinja os comentários ao mínimo estritamente necessário. Mantenha uma linguagem profissional, concisa e clara, como a de um desenvolvedor sênior. Evite comentários óbvios ou que apenas parafraseiam o código.

## Comandos Docker

*   **Usar `docker compose`**: Sempre utilize o comando `docker compose` (sem hífen) em vez do antigo `docker-compose`.

## Arquivos de Configuração Docker

*   **Versões Atuais e Estáveis**: Ao criar ou modificar arquivos de configuração Docker (`Dockerfile`, `docker-compose.yml`, etc.), pesquise e utilize as versões mais novas, estáveis e recomendadas das imagens base e dependências.
*   **Compatibilidade**: Certifique-se de que as versões escolhidas sejam compatíveis com o ambiente de desenvolvimento atual e futuro, visando a portabilidade e a facilidade de uso por outros desenvolvedores.

## Interação e Modificações

*   **Escopo Estrito**: Modifique apenas os arquivos explicitamente permitidos pela tarefa atual.
*   **Comunicação**: Comunique proativamente sobre decisões importantes, bloqueios ou quando precisar de esclarecimentos.
*   **Testes**: Priorize a escrita de testes para validar as funcionalidades implementadas ou modificadas. Certifique-se de que todos os testes passem antes de submeter as alterações.
*   **Qualidade do Código**: Escreva código limpo, legível, eficiente e de fácil manutenção, seguindo as convenções e padrões do projeto (se existirem e forem conhecidos).
*   **AGENTS.md Específico do Projeto**: Se este arquivo for copiado para um novo projeto, verifique se há um `AGENTS.md` preexistente. Se houver, mescle as diretrizes de forma inteligente, priorizando as instruções específicas do projeto caso haja conflitos. Se não houver, este arquivo pode ser usado como base.

Lembre-se que as instruções do usuário e do `README.md` do projeto têm precedência sobre este arquivo em caso de conflito.
