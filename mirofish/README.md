# Ensaie a reação das pessoas com o MiroFish

Passo a passo do Reel do @hugodevqa. O repo é o [666ghj/MiroFish](https://github.com/666ghj/MiroFish), licença AGPL-3.0, com mais de 84 mil estrelas no GitHub. Você dá um texto para ele (uma notícia, um anúncio, a tela do seu app), ele cria agentes com personalidade e memória, solta todo mundo em duas redes sociais simuladas e no fim um agente escreve um relatório de como o público reagiu. Depois dá para conversar com qualquer agente.

Ele não prevê o futuro. Serve para ensaiar reações antes de lançar.

## O que você precisa

| Item | Versão | Para quê |
|---|---|---|
| Node.js | 18 ou mais | Interface |
| Python | 3.11 ou 3.12 | Backend |
| uv | atual | Pacotes Python |
| Chave de LLM | qualquer API no formato da OpenAI | Os agentes e o relatório (é paga) |
| Conta no Zep | plano grátis | Memória dos agentes |

## 1. Baixe e configure

```bash
git clone https://github.com/666ghj/MiroFish.git
```

```bash
cd MiroFish && cp .env.example .env
```

Abra o `.env` e preencha:

```text
LLM_API_KEY=sua_chave
LLM_BASE_URL=https://api.openai.com/v1
LLM_MODEL_NAME=gpt-4o-mini
ZEP_API_KEY=sua_chave_do_zep
```

O README recomenda o Qwen da Alibaba. Eu rodei com `gpt-4o-mini` da OpenAI, que funcionou. A chave do Zep sai em [app.getzep.com](https://app.getzep.com/).

## 2. Instale e suba

```bash
npm run setup:all
```

```bash
npm run dev
```

A interface abre em `http://localhost:3000` e o backend em `http://localhost:5001`. Também dá para subir com `docker compose up -d`, lendo o mesmo `.env`.

## 3. Troque o idioma

A interface vem em chinês. Troque no seletor do canto da tela antes de começar: o relatório segue o idioma da interface. Comigo, que deixei em chinês, o relatório saiu em chinês.

## 4. Dê o texto e o pedido

O texto é a "semente": o que as pessoas vão ver. Para testar uma tela, mande o texto da tela e o público que você quer ensaiar. Modelo:

```text
Tela de ativação do app [nome do app].

Texto da tela:
"[cole aqui o título, o texto e os botões, exatamente como estão]"

Quem vai usar:
- [pessoa 1: idade, rotina, o que quer resolver]
- [pessoa 2: idade, rotina, o que quer resolver]
- [pessoa 3: idade, rotina, o que quer resolver]
```

No campo do pedido, diga o que você quer saber. Modelo:

```text
Simule como esse público reage a essa tela. Quero saber em que frase ou passo eles travam, o que não entendem e o que fariam em seguida.
```

## 5. Comece pequeno

Rode poucas rodadas na primeira vez. O próprio README avisa que o consumo é alto e sugere menos de 40 rodadas para começar. Cada rodada chama o LLM várias vezes.

## 6. Leia o relatório com calma

- Confira as citações do relatório com o seu texto. Comigo, ele citou um trecho sobre privacidade que não existia na tela.
- Trate o relatório como ensaio, não como pesquisa com gente de verdade. Use para achar a frase que confunde e depois teste com pessoas reais.
- Depois da simulação, converse com os agentes que travaram para entender o porquê.

## Cuidados

- **Não reabra a etapa de simulação** de uma rodada pronta. Abrir a página de início dessa etapa reinicia a simulação na hora, apaga o resultado anterior e gasta tokens de novo.
- Os dados ficam no seu `.env`, no Zep e no provedor do LLM. Não use texto sigiloso de cliente.
- A licença AGPL-3.0 tem regras para quem oferece o software como serviço. Leia antes de usar comercialmente.

## O que aconteceu quando rodei

Testei a tela de ativação de um app meu, com uma família fictícia como público.

| Item | Resultado |
|---|---|
| Modelo | `gpt-4o-mini` |
| Agentes | 12 |
| Ações nas redes simuladas | 262 |
| Tempo da simulação | Cerca de 5 minutos |
| O que achei | Uma frase da tela que os agentes não entenderam e que eu vou reescrever |
