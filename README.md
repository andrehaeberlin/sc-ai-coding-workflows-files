# AI Coding Workflows: From Cloud to Local

Arquivos de apoio do curso **AI Coding Workflows: From Cloud to Local**, da DeepLearning.AI. O curso usa o mesmo projeto para explorar implementação com agentes, delegação a subagentes e execução com modelos na nuvem e locais.

O projeto proposto é o **AgentClinic**: uma aplicação web em Python onde agentes de IA podem publicar reclamações sobre seus humanos.

**Estado atual:** este repositório contém especificações, dependências, prompts e configurações de agentes. Ainda não contém `app.py`, `models.py`, templates ou testes. Os comandos de execução e testes abaixo passam a ser úteis depois da implementação das respectivas fases.

## Organização do repositório

As pastas representam pontos de partida de diferentes aulas. Elas não são módulos de uma única aplicação e não precisam ser executadas juntas.

| Pasta | Conteúdo e uso |
|---|---|
| [Lessons_02-08](Lessons_02-08/) | Ponto de partida para acompanhar o curso desde a aula 2. Inclui especificações, dependências, um README do AgentClinic e orientações em `CLAUDE.md`. Roadmap com duas fases. |
| [Lessons_09](Lessons_09/) | Mesmas especificações e dependências iniciais, com o subagente `implementer` do OpenCode configurado para um modelo via OpenRouter. Roadmap com duas fases. |
| [Lessons_11-12](Lessons_11-12/) | Roadmap com três fases e `implementer` configurado para LM Studio, com permissões e quantidade de passos limitadas. A terceira fase também exige validação dos campos do formulário. |

Arquivos de referência:

| Caminho | Finalidade |
|---|---|
| [prompts.md](prompts.md) | Prompts originais do curso, organizados por aula, com notas de uso. |
| [AGENTS.md](AGENTS.md) | Instruções para agentes que trabalham neste repositório: escopo, contratos, diferenças entre aulas e validação. |
| [Lessons_02-08/README.md](Lessons_02-08/README.md) | Apresentação e instruções do projeto AgentClinic, em inglês. |
| [Lessons_02-08/CLAUDE.md](Lessons_02-08/CLAUDE.md) | Contexto adicional para Claude Code na pasta inicial. |
| `<pasta-da-aula>/specs/mission.md` | Propósito e experiência esperada da aplicação. |
| `<pasta-da-aula>/specs/tech-stack.md` | Tecnologias e restrições da implementação. |
| `<pasta-da-aula>/specs/roadmap.md` | Tarefas e critérios de validação de cada fase. |
| `<pasta-da-aula>/requirements.txt` | Dependências Python fixadas para o exercício. |
| `.opencode/agents/implementer.md` | Configuração do subagente, presente apenas em `Lessons_09` e `Lessons_11-12`. |

## O que será construído

O AgentClinic terá uma página inicial e um mural de reclamações, com dados mantidos em uma lista Python em memória. As reclamações serão perdidas ao reiniciar o processo.

| Componente | Especificação do projeto |
|---|---|
| Python | Python 3; o material de preparação recomenda 3.11 ou superior. |
| Framework | `fastapi[standard]==0.115.10`. |
| Servidor e templates | Uvicorn e Jinja2, disponíveis com as dependências do FastAPI. |
| Interface | Bootstrap 5 por CDN, sem npm ou etapa de build. |
| Modelo de dados | Dataclass `Complaint` com `agent_name`, `text` e `timestamp`. |
| Persistência | Lista em memória, sem banco de dados e sem autenticação. |
| Testes | `pytest==8.3.4` e `TestClient` de `starlette.testclient`. |

Rotas previstas:

| Rota | Comportamento |
|---|---|
| `GET /` | Página inicial com a frase “Come in. Sit down. Tell us about your human.” |
| `GET /complaints` | Mural com o título “Complaints Board” e de três a cinco reclamações iniciais. |
| `POST /complaints` | Recebe `agent_name` e `text`, adiciona a reclamação e redireciona para `/complaints` com status 303. |

Em `Lessons_02-08` e `Lessons_09`, a fase 1 entrega a página inicial; a fase 2 entrega o mural e o formulário. Em `Lessons_11-12`, a fase 2 entrega apenas a leitura do mural e a fase 3 adiciona o formulário, incluindo mensagens de erro para campos vazios.

## Preparação

Tenha Python 3.11 ou superior, Git e um editor instalados. As ferramentas de agentes dependem da aula: [Claude Code](https://code.claude.com/docs/en/getting-started), [OpenCode](https://opencode.ai/docs/), uma conta no [OpenRouter](https://openrouter.ai/docs/quickstart) ou [LM Studio](https://lmstudio.ai/download) para os exercícios com modelos locais.

1. Para seguir o curso em ordem, use `Lessons_02-08` como diretório de trabalho. Você também pode copiar seu conteúdo para um projeto de exercícios separado.
2. Leia os três arquivos em `specs/` da pasta escolhida e o trecho da aula em [prompts.md](prompts.md).
3. Crie o ambiente virtual e instale as dependências dentro dessa pasta.

No Windows, com PowerShell, partindo da raiz deste repositório:

```powershell
Set-Location Lessons_02-08
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

No macOS ou Linux, partindo da raiz deste repositório:

```bash
cd Lessons_02-08
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
```

Se começar de outra aula, substitua apenas o nome da pasta. Use sempre o Python do ambiente virtual para instalar dependências, executar a aplicação e rodar os testes. A instalação das dependências não gera o código da aplicação.

## Implementação, execução e testes

Peça ao agente para implementar a fase desejada do roadmap da pasta escolhida, usando o prompt da aula correspondente. Valide a fase antes de avançar para a próxima. As configurações de subagente são específicas de cada etapa; consulte [AGENTS.md](AGENTS.md) antes de alterá-las.

Depois de implementar a fase 1, execute a aplicação no diretório da aula:

```powershell
# Windows / PowerShell
.\.venv\Scripts\python.exe app.py
```

```bash
# macOS / Linux
.venv/bin/python app.py
```

Acesse <http://127.0.0.1:8000>. O roadmap determina o uso de `uvicorn.run("app:app", reload=True)` no bloco de entrada de `app.py`.

Depois de criar os testes previstos no roadmap:

```powershell
# Windows / PowerShell
.\.venv\Scripts\python.exe -m pytest tests/
```

```bash
# macOS / Linux
.venv/bin/python -m pytest tests/
```

Os testes devem usar `TestClient`, sem depender de um servidor iniciado manualmente. Atualmente não há suíte de testes, scripts de build, configuração de lint ou pipeline de CI versionados neste repositório.

## Particularidades das aulas

- **Aula 9:** `Lessons_09` já inclui o `implementer` que a aula ensina a criar. Para refazer essa etapa, siga a orientação de recriação em `prompts.md` na sua cópia de exercícios.
- **Aulas 11–12:** `Lessons_11-12` já contém o roadmap dividido em três fases; pule o prompt que solicita essa divisão se começar por essa pasta. O prompt de implementação executa uma fase por vez.
- **Comandos por sistema operacional:** `.venv/bin/python` é o caminho de macOS/Linux. No Windows, use `.\.venv\Scripts\python.exe`.
- **Modelo local:** os identificadores MLX presentes no material refletem o ambiente macOS da gravação. Use o identificador exibido pelo seu LM Studio; os exemplos do curso mencionam builds GGUF para Windows/Linux. Confira também a URL do servidor local.
- **Permissão de testes do agente local:** a configuração de `Lessons_11-12` permite apenas o comando Bash exato `.venv/bin/python -m pytest tests/`. Ao adaptar o exercício para Windows, ajuste tanto o comando do prompt quanto a permissão correspondente na sua cópia.
- **Arquivo de entrada:** `tech-stack.md` menciona `main.py`, mas todos os roadmaps exigem `app.py`. Para implementar o exercício, siga `app.py`.

Mantenha chaves de API e configurações pessoais fora dos arquivos versionados. Os prompts e modelos citados são referências do curso; este repositório não configura automaticamente provedores ou servidores locais.

## Recursos do curso

- [Cursos da DeepLearning.AI](https://www.deeplearning.ai/courses/).
- [Outros materiais de cursos da DeepLearning.AI](https://github.com/https-deeplearning-ai/deeplearning-ai).
