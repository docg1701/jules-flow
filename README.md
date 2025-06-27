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

O trabalho com Jules é organizado em um ciclo de vida dinâmico para cada nova funcionalidade, que ocorre em seu próprio branch. O processo é guiado por um plano mestre e executado através de tarefas atômicas que gerenciam seu próprio estado.

### Configuração do Ambiente da VM com `jules_bootstrap.sh`

Para garantir que Jules opere em um ambiente de máquina virtual (VM) com todas as dependências de sistema necessárias para compilar, testar e executar o projeto, o sistema Jules-Flow gerencia um script de bootstrap chamado `jules_bootstrap.sh`.

*   **Localização:** Este script reside (ou será criado por Jules) na raiz do seu repositório: `./jules_bootstrap.sh`.
*   **Propósito:** Contém comandos shell (primariamente `apt-get install` para sistemas baseados em Debian/Ubuntu) para instalar pacotes de sistema.
*   **Gerenciamento por Jules:**
    *   Na Fase 1 de suas operações, Jules analisará os arquivos do projeto (ex: `package.json`, `requirements.txt`, `Dockerfile`) para inferir dependências de sistema e adicioná-las ao `jules_bootstrap.sh`.
    *   Se, durante a execução de tarefas, Jules detectar a falta de uma dependência de sistema que cause uma falha, ele tentará adicionar o comando de instalação correspondente ao `jules_bootstrap.sh` e o notificará.
*   **Ação Crítica do Usuário:**
    *   **Você DEVE configurar a interface do Agente Jules (em jules.google.com) para usar este script `jules_bootstrap.sh` como o "Environment setup script" para o seu repositório.** Isso garantirá que, toda vez que Jules iniciar uma nova sessão de trabalho (e uma nova VM for provisionada), o script será executado, preparando o ambiente corretamente.
    *   Se Jules atualizar o `jules_bootstrap.sh` devido a uma dependência descoberta, ele o informará. Para que a mudança tenha efeito, você precisará aprovar o commit contendo o `jules_bootstrap.sh` atualizado e, em seguida, instruir Jules a tentar novamente a tarefa pausada (ou reiniciar o fluxo de trabalho). A nova VM será criada com o bootstrap atualizado.
*   **Conteúdo Inicial (Exemplo):** Se não existir, Jules criará `jules_bootstrap.sh` com um conteúdo base como:
    ```bash
    #!/bin/bash
    # Script de bootstrap para o ambiente de Jules
    sudo apt-get update
    # Adicione aqui outros comandos de instalação, ex:
    # sudo apt-get install -y curl git python3-pip
    echo "Bootstrap script concluído."
    ```
    Jules então adicionará outros comandos `sudo apt-get install -y ...` conforme necessário.

### Fase 1: Planejamento (Humano + Gemini)

O desenvolvedor humano, atuando como arquiteto, colabora com o Gemini para analisar o código e discutir a implementação. Esta fase culmina na geração de um arquivo `jules-flow/working-plan.md`, que contém o roteiro detalhado para o agente Jules.

### Fase 2: Execução (Agente Jules)

Em um novo branch, o agente Jules é acionado. Ele lê o `working-plan.md`, prepara o ambiente de trabalho e decompõe o plano em `task`s atômicas no diretório `/backlog/`. A partir daí, ele executa as tarefas de forma inteligente, seguindo esta lógica:
1.  **Seleção por Dependência**: Escolhe a próxima tarefa do `/backlog/` cujas dependências já foram concluídas.
2.  **Gestão de Estado**: Move a tarefa para `/in_progress/` ao iniciar e para `/done/` ou `/failed/` ao terminar, atualizando sempre o `task-index.md`.
3.  **Geração Automática de Testes**: Ao concluir com sucesso uma tarefa do tipo `development`, Jules cria automaticamente uma tarefa de `test` correspondente no `/backlog/`, garantindo a cobertura de testes.
4.  **Gestão de Falhas**: Se uma tarefa falha, o trabalho é pausado até que uma nova tarefa de correção seja criada, garantindo que nenhum problema seja ignorado.

**Nota sobre Adaptação do Plano:** Caso Jules identifique, durante a execução, a necessidade de uma alteração estratégica significativa no `working-plan.md`, ele poderá iniciar um "Processo Especial: Adaptação de Plano em Execução", detalhado no `instructions-for-jules.md`. Isso envolve pausar o trabalho, criar uma tarefa `replan` para revisão do desenvolvedor e aguardar um `working-plan.md` atualizado.

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

**ATENÇÃO:** Antes de utilizar pela primeira vez o Prompt 2.1, inicie um jules-taks com um prompt inicial simples como "Analise este repositório" para que o Jules compreenda todo o contexto do projeto, faça o boot no VM com segurança. Depois dessa etapa preliminar, os prompts abaixo funcionam (quase) sem falhar.

**Prompt 2.1: Início dos Trabalhos no Branch**
```markdown
Olá, Jules. Estamos iniciando o trabalho em um novo branch. Sua missão é preparar o ambiente e iniciar o ciclo de trabalho.

Execute as **Fases 1 e 2** do arquivo `jules-flow/instructions-for-jules.md` sequencialmente para colocar o projeto em estado de pronto para a execução das tarefas.
```

**Prompt 2.2: Continuar Trabalho (Individual ou em Lote)**

Use este comando para instruir Jules a executar as tarefas pendentes. Ele possui duas variações.

*Para executar apenas a próxima tarefa disponível:*
```markdown
Olá, Jules. É hora de retomar seu trabalho em um novo branch.

Execute a **Fase 3** do arquivo `jules-flow/instructions-for-jules.md` para processar a próxima tarefa disponível no backlog.
```

*Para executar um lote de tarefas (ex: 3 tarefas em sequência):*
```markdown
Olá, Jules. É hora de retomar seu trabalho em um novo branch.

Execute a **Fase 3** do arquivo `jules-flow/instructions-for-jules.md` **3 vezes em sequência**, processando um lote de tarefas do backlog sem pausas entre elas.
```
Nota: Se qualquer tarefa em um lote falhar, Jules pausará toda a execução do lote após finalizar a tarefa que falhou (movendo-a para `/failed/`) e aguardará novas instruções, conforme a Fase 3, Passo 7 do `instructions-for-jules.md`.

**Prompt 2.3: Finalizar e Limpar o Branch**

Use este prompt após todas as tarefas terem sido concluídas com sucesso.

```markdown
Olá, Jules. O trabalho de desenvolvimento foi concluído.

Execute a **Fase 4** do arquivo `jules-flow/instructions-for-jules.md` para gerar o relatório final e limpar o ambiente de trabalho.
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

Execute a **Fase 6** do arquivo `jules-flow/instructions-for-jules.md` para realizar uma revisão de qualidade no código produzido e identificar possíveis melhorias.
```

**Prompt 2.6: Analisar e Corrigir Tarefa com Falha**

Use este prompt quando uma tarefa falhar e o trabalho for bloqueado.

```markdown
Olá, Jules. Identificamos que a tarefa `task-XXX` falhou.

Siga o procedimento de tratamento de falhas descrito na **Fase 3 (passo 7)** do arquivo `jules-flow/instructions-for-jules.md` para analisar o problema. Em seguida, crie uma nova task de correção.
```

## Estrutura de Diretórios

* `/jules-flow/`: Diretório raiz do sistema Jules-Flow.
    * `/backlog/`: Contém tarefas pendentes (arquivos `.md`).
    * `/in_progress/`: Contém a tarefa que está sendo executada no momento.
    * `/done/`: Contém tarefas concluídas com sucesso.
    * `/failed/`: Contém tarefas que falharam durante a execução.
    * `/final-reports/`: Contém os relatórios finais consolidados de cada etapa de trabalho.
    * `/docs/reference/`: Contém artefatos de pesquisa e documentação técnica consultada ou gerada por Jules (ex: documentação de APIs, resultados de tasks de research).
    * `/templates/`: Contém o modelo `task-template.md` para novas tarefas.
    * `README.md`: Este arquivo.
    * `instructions-for-jules.md`: Instruções operacionais para o agente Jules.
    * `task-index.md`: Índice e relatório de progresso de todas as tarefas.
    * `working-plan.md`: O plano de trabalho mestre gerado na fase de planejamento.

## Licença

Este projeto está licenciado sob os termos da Licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.
