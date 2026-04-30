# Templates de Prompts para Claude

> Pasta com templates de prompts para uso eficiente do Claude (Claude.ai, Claude Code e Claude Desktop) no contexto de desenvolvimento Oracle SFT.

## Propósito

Esta pasta complementa o repositório `oracle-dev-standards`. Enquanto a raiz do repositório define **como o código deve ser escrito** (padrões, nomenclatura, owners, scripts), esta pasta define **como conversar com o Claude** para produzir esse código com qualidade.

Os templates aqui são genéricos quanto ao prompt, mas pressupõem que o Claude já tem acesso ao `CLAUDE.md` da raiz — onde estão as regras absolutas (versões, owners, nomenclatura, padrões de código).

## Arquivos

| Arquivo | Conteúdo | Quando usar |
|---|---|---|
| `prompts-base.md` | Templates fundamentais (PL/SQL, debug, tuning, code review, documentação) | Tarefas técnicas pontuais |
| `prompts-cenarios.md` | Templates específicos (relatórios APEX, packages legados, integrações, triggers Forms, Dynamic Actions, DDL/DML) | Cenários recorrentes do dia a dia |
| `prompts-servico-chamado.md` | Templates para fluxo de serviços com DF e chamados de manutenção | Início de qualquer demanda formal |

## Como usar

1. **Identifique o tipo de tarefa.** Migração com DF? Use `prompts-servico-chamado.md`. Bug pontual? Idem. Tarefa técnica isolada (ex: tunar uma query)? Use `prompts-base.md` ou `prompts-cenarios.md`.

2. **Copie o template adequado.** Cole na conversa com o Claude e preencha as seções marcadas com `[...]`.

3. **Não repita o que já está no `CLAUDE.md`.** Se o Claude tem acesso ao repositório, ele já conhece as regras de nomenclatura, owners, tablespaces, etc. Os prompts apenas direcionam o trabalho.

4. **Itere.** Os templates são ponto de partida. Refine-os conforme descobrir o que funciona melhor para cada tipo de tarefa.

## Boas práticas

- **Sempre validar a análise antes da implementação.** Os templates de serviço/chamado já fazem isso por padrão (Fase 1 antes da Fase 2).
- **Conferir COMMIT escondido.** Mesmo com a regra explícita no `CLAUDE.md`, vale revisar visualmente o código gerado, especialmente em blocos `EXCEPTION`.
- **Apontar inconsistências.** Se o Claude gerar algo que conflita com o `CLAUDE.md` (ex: usar nomenclatura errada, sugerir `EXECUTE IMMEDIATE` para `ALTER TABLE`), interrompa e peça correção referenciando o arquivo de padrão.

## Referências

- [`../CLAUDE.md`](../CLAUDE.md) — Padrões globais (lido automaticamente pelo Claude)
- [`../docs/tabelas.md`](../docs/tabelas.md) — Manutenção de tabelas
- [`../docs/objetos.md`](../docs/objetos.md) — Packages, Functions, Procedures, Triggers
- [`../docs/scripts.md`](../docs/scripts.md) — Padrões de scripts SQL
- [`../docs/parametros.md`](../docs/parametros.md) — Criação de parâmetros
- [`../templates/`](../templates/) — Templates de código (não de prompts)
