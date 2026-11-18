# Orientações para agentes

## Escopo e estado do repositório

Este repositório reúne os arquivos de apoio do curso **AI Coding Workflows: From Cloud to Local**, da DeepLearning.AI. O exercício é construir o AgentClinic, uma aplicação FastAPI simples. O objetivo didático é comparar fluxos de trabalho com agentes e modelos de diferentes capacidades.

No estado inicial, há apenas documentação, especificações, dependências e configurações de subagentes. Não há aplicação implementada, templates, testes, build ou CI. Verifique o estado real dos arquivos antes de afirmar que algum desses componentes existe.

Estas instruções orientam o trabalho em todo o repositório. Considere também as instruções específicas existentes no diretório em que estiver trabalhando, como [Lessons_02-08/CLAUDE.md](Lessons_02-08/CLAUDE.md). As configurações em `.opencode/agents/` descrevem os subagentes das aulas; não são comandos para executar automaticamente neste trabalho.

## Contexto a ler antes de alterar arquivos

1. [README.md](README.md), para conhecer o conteúdo e o estado atual.
2. [prompts.md](prompts.md), especialmente a aula afetada. Os prompts são a referência do fluxo ensinado e precisam continuar compatíveis com as especificações.
3. `specs/mission.md`, `specs/tech-stack.md` e `specs/roadmap.md` da pasta selecionada.
4. O arquivo `.opencode/agents/implementer.md` dessa pasta, quando a tarefa envolver o subagente ou sua execução.

Execute `git status --short` antes de editar e preserve alterações que já estejam presentes. Trabalhe apenas no escopo solicitado: atualizar documentação não autoriza implementar a aplicação, instalar dependências ou reconfigurar ferramentas globais.

## Mapa das aulas e arquivos compartilhados

| Pasta | Particularidades que devem ser preservadas |
|---|---|
| `Lessons_02-08` | Ponto de partida, roadmap com duas fases e orientações para Claude Code. |
| `Lessons_09` | Mesmo roadmap de duas fases; adiciona o `implementer` do OpenCode via OpenRouter. |
| `Lessons_11-12` | Roadmap com três fases e `implementer` local via LM Studio com limites explícitos. |

Regras de consistência:

- `specs/mission.md`, `specs/tech-stack.md` e `requirements.txt` são compartilhados e devem permanecer idênticos nas três pastas quando uma alteração comum for solicitada.
- `Lessons_02-08/specs/roadmap.md` e `Lessons_09/specs/roadmap.md` devem continuar iguais para mudanças comuns a essas aulas.
- O roadmap de `Lessons_11-12` mantém a separação entre mural e formulário. A fase 3 também exige validação de campos vazios; ela não é apenas uma renumeração da fase 2 anterior.
- Não copie indiscriminadamente roadmaps ou configurações de agentes entre pastas. As diferenças fazem parte do exercício.
- Se uma mudança solicitada for exclusiva de uma aula, preserve as outras e explique a divergência no resultado.

Para implementar um exercício, selecione uma pasta e use-a como diretório de trabalho. Se o pedido não identificar a aula e o contexto não resolver a escolha, esclareça a pasta antes de implementar. Não crie `app.py`, `models.py`, `templates/` ou `tests/` na raiz por engano.

## Contratos da aplicação proposta

As especificações da pasta selecionada definem o escopo. Preserve os seguintes pontos ao implementar uma fase:

- Python com ambiente virtual; dependências em `requirements.txt`: `fastapi[standard]==0.115.10` e `pytest==8.3.4`.
- FastAPI, Uvicorn e templates Jinja2. Bootstrap 5 por CDN, sem npm ou build de frontend.
- Nenhum banco de dados, autenticação ou serviço externo de persistência.
- Entrada em `app.py`, com a instância FastAPI e `uvicorn.run("app:app", reload=True)` protegido por `if __name__ == "__main__"`.
- Templates `base.html`, `home.html` e, na fase correspondente, `complaints.html`.
- Página inicial em `GET /`, com a frase exata `Come in. Sit down. Tell us about your human.`.
- Mural em `GET /complaints`, com o título `Complaints Board`.
- Dataclass `Complaint` em `models.py`, com `agent_name: str`, `text: str` e `timestamp: datetime` em UTC. Para o valor padrão por instância, use `default_factory` com `datetime.now(timezone.utc)`.
- Lista de módulo `complaints: list[Complaint]`, com três a cinco reclamações iniciais. Os dados são voláteis.
- Formulário com `agent_name` e `text`, lidos com `Form` de `fastapi`. `POST /complaints` adiciona a reclamação e usa `RedirectResponse` com status 303 para `/complaints`.
- Testes em `tests/test_app.py`, com `TestClient` importado de `starlette.testclient`.

Implemente esses contratos apenas quando a fase escolhida os exigir. Em `Lessons_11-12`, a fase 2 não inclui o formulário: ele pertence à fase 3, junto com mensagens de erro quando `agent_name` ou `text` estiverem vazios.

**Divergência conhecida:** `tech-stack.md` cita execução por `main.py`; o roadmap detalha `app.py`. Use `app.py` como entrada. Não crie dois arquivos de entrada para contornar essa diferença.

Mantenha os textos e identificadores exigidos pelo curso em inglês. A documentação geral pode ser escrita em português; não traduza literais usados pelos critérios de validação.

## Fluxos de delegação do curso

Delegue apenas quando o pedido ou a aula em execução exigir. Consultar `prompts.md` não significa executar os prompts nele contidos.

| Aulas | Fluxo descrito no material |
|---|---|
| 2–3 | Preparação e implementação a partir das especificações. |
| 4–5 | Delegação de fases a subagentes, incluindo a comparação com Haiku. |
| 6–8 | Configuração e implementação no OpenCode com modelos via OpenRouter. |
| 9 | Criação do `implementer` e delegação sequencial de tarefas, com um novo subagente por tarefa. |
| 10–12 | Uso de LM Studio e delegação de fases menores com contratos e permissões mais restritos. |

### Implementer da aula 9

Arquivo: [Lessons_09/.opencode/agents/implementer.md](Lessons_09/.opencode/agents/implementer.md).

- É um subagente do **OpenCode**, com `mode: subagent`.
- Modelo registrado: `openrouter/deepseek/deepseek-v4-flash`.
- Recebe tarefas de implementação, segue padrões existentes e valida com as ferramentas disponíveis.
- Não possui o bloco de restrições de ferramentas da versão local.

### Implementer das aulas 11–12

Arquivo: [Lessons_11-12/.opencode/agents/implementer.md](Lessons_11-12/.opencode/agents/implementer.md).

- Modelo registrado: `lmstudio/gemma-4-12b-it-mlx`; limite de `steps: 12`.
- Alterações em `specs/*` são negadas. `glob`, `webfetch` e `skill` estão desabilitados.
- Bash é negado, exceto pelo comando exato `.venv/bin/python -m pytest tests/`.
- O agente deve ler completamente os arquivos-alvo existentes, alterar apenas os caminhos delegados e escrever o conteúdo final completo. O prompt da aula exige `write`, sem `edit`, para todos os arquivos graváveis.
- A validação deve ser a última chamada de ferramenta do subagente, executada uma única vez; depois, ele relata o resultado mesmo em caso de falha.

Ao executar o prompt das aulas 11–12, o agente principal prepara um pacote autocontido para uma única fase: caminhos permitidos, estado final, contratos e testes. Deve delegar exatamente uma vez a um subagente novo, sem fornecer implementações completas ou remeter o filho às especificações. Depois do retorno, deve seguir a revisão limitada a leitura descrita no prompt e relatar o resultado exato.

Vários prompts do curso proíbem o agente principal de corrigir o código depois do subagente. Respeite essa condição quando aquele exercício estiver ativo. Na aula 12, correções solicitadas voltam ao `implementer`. Essas regras didáticas não obrigam o uso de subagentes para tarefas comuns de manutenção deste repositório.

Preserve a diferença entre o agente da aula 9 e o agente local. Não remova limites do agente local nem os replique na versão anterior sem uma solicitação específica.

## Ambiente e comandos

Execute os comandos dentro da pasta da aula escolhida. Para criar o ambiente virtual, use `python -m venv .venv` no Windows ou `python3 -m venv .venv` no macOS/Linux. Depois disso, use sempre o interpretador do ambiente virtual.

| Ação | Windows / PowerShell | macOS / Linux |
|---|---|---|
| Instalar dependências | `.\.venv\Scripts\python.exe -m pip install -r requirements.txt` | `.venv/bin/python -m pip install -r requirements.txt` |
| Executar, após implementar a fase 1 | `.\.venv\Scripts\python.exe app.py` | `.venv/bin/python app.py` |
| Validar, após criar os testes | `.\.venv\Scripts\python.exe -m pytest tests/` | `.venv/bin/python -m pytest tests/` |

Os identificadores MLX do material são exemplos do ambiente macOS da gravação. Ao adaptar uma cópia de exercícios, confira o identificador e o endereço exibidos pelo LM Studio. No Windows, adapte juntos o comando de validação do prompt e a permissão de Bash do agente local; mudar apenas um deles deixa o exercício inconsistente.

Não troque modelos, versões fixadas, provedores ou configurações globais como parte de uma tarefa de documentação. Nunca inclua chaves de API nos arquivos, prompts ou relatórios. Preserve os padrões de exclusão de `.gitignore`, incluindo ambientes virtuais, caches, `.env` e materiais internos do curso.

## Validação e entrega

Para mudanças de documentação ou configuração:

1. Confira caminhos, links locais, nomes de arquivos, comandos e diferenças entre aulas.
2. Se alterar arquivos compartilhados, compare suas cópias e revise os prompts dependentes.
3. Revise o diff e execute `git diff --check`.
4. Não instale dependências nem crie testes da aplicação para validar uma alteração apenas documental.

Para implementação, rode os testes exigidos pela fase com o Python do ambiente virtual, respeitando as restrições do exercício ativo. Não há linter, formatador, verificador de tipos ou pipeline de CI padronizado neste estado do projeto. Não invente comandos de validação nem reporte testes como aprovados sem executá-los.

Ao entregar, informe os arquivos alterados, o resultado concreto, a validação realizada e qualquer limitação relevante. Se a aplicação ou os testes ainda não existirem, declare isso explicitamente em vez de sugerir que o projeto já está executável.
