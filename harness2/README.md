# Harness²: agente melhor sem treinar o modelo

Passo a passo do Reel do @hugodevqa. O repo é o [google-research/harness2](https://github.com/google-research/harness2), do Google Research, licença Apache 2.0. O README avisa que não é um produto oficial do Google.

Harness é tudo que fica em volta do modelo: prompt de sistema, guardrails, memória, orientação de ferramentas, subagentes, skills, scripts e plugins. O Harness² ajusta essas peças sozinho, com os pesos do modelo fixos. Em cada tarefa ele propõe mudanças, testa cada uma na mesma tarefa e junta o que funcionou, comparando como as versões se comportaram, sem gabarito. Depois guarda as lições para a próxima tarefa.

Segundo o README, uma rodada só melhorou GLM-5 e Gemini 3.5 Flash em até 11,5 pontos no WorkBuddy-Bench (escritório, web e código), 4,5 pontos no JobBench e 11,8 pontos percentuais na taxa parcial do LAB (jurídico).

## O que você precisa

- Linux e Python 3.12 (o pacote também aceita 3.13), com [uv](https://docs.astral.sh/uv/getting-started/installation/).
- Para os benchmarks com modelo: Node.js 20 ou mais novo, Google Cloud CLI e um projeto na Vertex AI. Essas rodadas custam API.
- WorkBuddy-Bench também pede Docker; LAB pede Pandoc e Poppler.

## 1. Rode a demo local (sem chave)

A demo roda os dois modos de recursão com editores determinísticos e execuções reais de Python. Não precisa de credencial de modelo.

```bash
git clone https://github.com/google-research/harness2.git
cd harness2
uv venv --python 3.12 .venv
source .venv/bin/activate
uv pip install -e '.[render]'
harness2-demo --output runs/demo --k 2
```

O resultado fica em `runs/demo`.

## 2. Rode um benchmark com modelo

Configure o modelo:

```bash
cp .env.example .env
# coloque o projeto da Vertex AI e os IDs dos modelos no .env
gcloud auth application-default login
```

Comece com uma tarefa só. Este exemplo do README instala o LAB, roda uma tarefa, avalia e gera o relatório:

```bash
harness2-setup lab
harness2-check --bench lab
harness2 --bench lab --k 1 --mode parallel --only-domains antitrust-competition --max-tasks 1
harness2-judge --bench lab --k 1 --mode parallel
harness2-report --bench lab --k 1 --mode parallel
```

- `--k` é quantas rodadas de ajuste ele faz por tarefa.
- `--mode parallel` testa várias versões lado a lado; `--mode sequential` refina uma versão em passos seguidos.
- `--substrate cx` troca o executor padrão (OpenCode) pelo Codex.
- `--dry-run` no `harness2` mostra as tarefas escolhidas sem chamar o modelo.

Para retomar, rode o mesmo comando de novo. Os resultados ficam em `runs/`.

## 3. Isole a máquina

O executor roda com a internet liberada, e o Codex roda com o sandbox desligado. A documentação conta que, num teste de validação, um agente do Codex baixou os critérios de correção da própria tarefa sem ninguém pedir.

- Rode numa VM ou máquina separada, sem suas chaves e seus projetos.
- Depois do setup, bloqueie no firewall ou no DNS os hosts que a documentação lista (`huggingface.co`, `hf.co`, `*.hf.co`, `github.com` e `raw.githubusercontent.com`).

## 4. Leve a ideia pro seu agente

Isto é sugestão minha, não está no repo. Mesmo sem rodar o Harness², dá para fazer o ciclo dele à mão no seu agente de código:

1. Escolha uma tarefa que o agente faz mal.
2. Mude uma peça só: o prompt do projeto (`CLAUDE.md`, `AGENTS.md`), uma skill ou um subagente.
3. Rode a mesma tarefa de novo e compare as duas execuções.
4. Fique com a versão que se saiu melhor e anote o porquê.

Antes de trocar de modelo, vale tentar isso.

## Limites

Os números são de benchmarks do README do Google Research, não de um teste meu. Benchmark não garante o mesmo ganho no seu projeto.
