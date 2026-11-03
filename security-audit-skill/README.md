# Auditoria de segurança com a skill da Cloudflare

Passo a passo do Reel do @hugodevqa. A skill é a [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill), licença MIT. Ela transforma seu agente em auditor: mapeia o projeto, solta caçadores isolados, manda cada achado para outro agente tentar derrubar e gera um relatório.

Use só em código seu ou com autorização.

## O que você precisa

- Um agente de código com subagentes. Eu rodei no Claude Code.
- Node.js, para os validadores da skill.
- Crédito ou plano com folga. A skill gasta bastante: veja os números no fim.

## 1. Instale a skill

Na raiz do projeto:

```bash
npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit --agent claude-code --yes
```

Isso copia a skill para `.claude/skills/security-audit` e cria um `skills-lock.json`. Em outro agente, veja as opções com `npx skills --help`.

A skill roda com as permissões do seu agente. Leia o `SKILL.md` antes de usar.

## 2. Peça a auditoria

Abra o agente na pasta do projeto e peça. O pedido padrão do README é este:

```text
security audit this codebase
```

Para a primeira vez, recomendo limitar o gasto com o perfil `quick` e um teto de agentes:

```text
security audit this codebase with the quick profile and a budget of 22 agents
```

Os perfis são `quick`, `standard` e `deep`. O `quick` faz uma rodada de caça e uma crítica de cobertura. Ele é parcial por definição.

## 3. Veja o resultado

Por padrão, tudo sai fora do seu repositório, em `~/security-audit-skill/<repo>/run-1`:

| Arquivo | O que tem |
|---|---|
| `REPORT.md` | Resumo, achados, notas de melhoria e cobertura |
| `NEEDS-VALIDATION.md` | Pendências, com caminho no código e como resolver cada uma |
| `FINDINGS-DETAIL.md` | Detalhe dos achados confirmados |
| `findings.json` | Registros estruturados |
| `coverage-ledger.json` | O que foi coberto e o que ficou de fora |

## 4. Entenda os três resultados

- **Confirmado:** tem caminho completo no código e resultado observado.
- **Pendente (`needs_validation`):** o caminho existe no código, mas falta um fato para fechar. Não tem severidade e não é vulnerabilidade comprovada.
- **Rejeitado:** outro agente derrubou a suspeita.

Sem sandbox, a skill não executa o código do projeto. Quando rodei, tudo ficou como pendente por causa disso. Para confirmar, ela exige sandbox com rede desligada, ambiente limpo, escrita restrita e limites de recursos.

## 5. Trate as pendências

Cada pendência traz um plano local e, quando faz sentido, uma conferência para o dono do sistema fazer. Comece pelas que alcançam o seu cenário real: uma pendência de vários usuários pode não importar se só você usa o sistema.

## 6. Rode de novo

Uma rodada não acha tudo. Nos testes da Cloudflare, uma rodada pegou cerca de metade do que rodadas repetidas acharam no total. Rodadas novas leem o resultado anterior e vão atrás do que faltou.

## O que aconteceu quando rodei

Rodei em um projeto meu em Next.js com 267 arquivos, no perfil `quick`, só lendo o código.

| Item | Resultado |
|---|---|
| Agentes | 21 |
| Tokens dos agentes | Cerca de 4 milhões |
| Tempo | Cerca de 30 minutos |
| Pendentes | 10 |
| Confirmados | 0 |
| Rejeitados | 0 |
| Áreas adiadas para outra rodada | 7 |

Os números são de uma rodada e mudam com o tamanho do projeto.

## Limites

A skill não garante código seguro, não acha todas as falhas e não substitui pentest.
