# Monte um fluxo com IA que se revisa toda semana

Passo a passo do carrossel do @hugodevqa. Num domingo, voltando de viagem sozinho, liguei o Grok no viva-voz pra pensar um projeto. A conversa foi além da ideia e terminou mudando o jeito que eu trabalho com IA nos meus projetos.

Não é receita de ferramenta: dá para montar o mesmo esquema trocando cada peça pela que você já usa.

## O que você precisa

| Peça | Para quê |
|---|---|
| Um repositório Git | Fonte única de código, schemas e assets |
| Um RAG que rode na sua máquina | Docs, decisões e código para os agentes consultarem |
| Um kanban | Cards que funcionam como contrato para os agentes |
| Um modelo local (escolhi Qwen e Gemma) | O trabalho do dia a dia, sem pagar por token |
| Um bot que rode toda semana (o meu é o Wren, no Grok) | A revisão semanal |

## 1. Pense em voz alta e feche com documento

Converse com a IA sem roteiro. Antes de encerrar, peça dois arquivos:

```text
Resume em tópicos tudo o que a gente decidiu nesta conversa.
Depois escreve o pedido pronto pra próxima IA que vai fazer a primeira tarefa.
```

Comigo saíram o resumo das decisões e um pedido de arte, que outra IA executou na mesma noite.

## 2. Uma fonte única

Código, schemas e assets ficam no mesmo repositório. Se o agente precisa de um arquivo, ele está lá, e não solto no seu computador.

## 3. RAG local com tudo o que o agente precisa saber

Indexe a documentação, as decisões e o código num RAG que roda na sua máquina. O meu fica no Mac Mini. Toda decisão nova entra no índice, e o agente consulta antes de agir. Assim você para de repetir o mesmo contexto em toda conversa.

## 4. Card rígido = contrato

Agente só pega tarefa com o card completo. Um modelo:

```text
Título: [verbo + resultado]
Contexto: [por que existe e onde está a decisão no RAG]
Entrada: [arquivos, dados e links]
Saída: [o que fica pronto e onde]
Critério de aceite:
- [ ] [algo que dá para conferir]
Como testar: [comando ou passo]
Fora do escopo: [o que não mexer]
```

Se você não consegue escrever o critério de aceite, a tarefa ainda não está pronta para ninguém pegar, nem o agente.

## 5. Cada máquina com um papel

| Máquina | Papel |
|---|---|
| Mac Mini | Desenvolve, com IA local |
| Wren (bot no Grok) | Testa e revisa a semana |
| MacBook | Painel de validação |
| Nuvem | Só quando o local não dá conta |

Local primeiro por dois motivos: custo e privacidade.

## 6. Revisão semanal com um bot

Toda semana o Wren lê o que eu entreguei e me devolve:

- melhorias de fluxo;
- skills novas ou ajustadas;
- agentes que vale criar;
- uma revisão de segurança e vulnerabilidades.

Modelo de pedido para montar o seu:

```text
Você é o revisor semanal dos meus projetos.
Entrada: as entregas desta semana (PRs, commits e documentos): [lista ou link].
Devolva em tópicos curtos:
1. Fluxo: o que eu fiz à mão e dá para automatizar.
2. Skills: o que criar ou ajustar.
3. Agentes: qual tarefa merece um agente próprio.
4. Segurança: dependência com falha conhecida, segredo exposto, permissão sobrando.
Só proponha. Não mude nada sem a minha aprovação.
```

## 7. Melhore o harness aos poucos

Harness é tudo o que fica em volta do modelo, como skills, agentes, memória e ferramentas. Da conversa saíram duas regras para ele: usar mais RAG, para os agentes saberem o que já foi decidido, e melhorar os pacotes de skills e agentes. Na prática, instale só o que você usa e deixe a revisão semanal apontar o que sobra.

## Cuidados

- O bot revisor só propõe. Quem aprova é você.
- Não coloque segredo no RAG nem no bot: chave, senha e `.env` ficam de fora.
- Revisão de segurança feita por IA aponta suspeitas. Confirme antes de corrigir.
