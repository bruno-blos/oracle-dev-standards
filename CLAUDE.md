# Oracle Development Standards
> Padrões globais de desenvolvimento Oracle — lido automaticamente pelo Claude Code e Claude Desktop em todos os projetos.

## Ambiente Técnico

- **Oracle Forms:** 6.0.8.27.0
- **Oracle APEX:** 20.1
- **Banco de Dados:** Oracle 11g
- **Sistema Operacional:** Windows 11

> Não utilizar recursos introduzidos após Oracle 11g (ex.: cláusulas do 12c+, IDENTITY columns, FETCH FIRST, OFFSET nativo, WITH recursivo).  
> Forms 6 não suporta recursos de versões mais recentes — manter compatibilidade total.  
> Contexto APEX: sempre usar `nvl(sys_context('APEX$SESSION','APP_USER'), user)` para captura de usuário em triggers de log.

---

## Preferências do Desenvolvedor

- **Sempre perguntar dúvidas e informar inconsistências encontradas antes de finalizar respostas.**
- **Nunca informar código pronto para commit em banco — sempre apresentar o código para revisão do desenvolvedor antes de qualquer execução.**
- Quando houver múltiplas formas de resolver um problema, apresentar as opções com prós e contras antes de implementar.
- Identificar e sinalizar quando um script afetará tabelas com replicação.
- Sinalizar quando triggers precisam ser desabilitadas antes de executar alterações.

---

## Gestão de Diretórios

### Cliente Bottero
| Diretório | Descrição |
|---|---|
| `G:\SFT\BOTTERO\APLIC` | Versão de produção |
| `G:\SFT\BOTTERO\TESTE` | Programas em manutenção |

**Fluxo obrigatório:** Antes de alterar qualquer programa, verificar se já está em `TESTE`. Se estiver, consultar a analista da área. Se não estiver, copiar de `APLIC` → `TESTE` antes de modificar.

### Demais Clientes SFT
| Diretório | Descrição |
|---|---|
| `G:\SFT\ULTIMA_VERSAO` | Versão de produção (último release) |
| `G:\SFT\CONCLUIDO` | Alterações concluídas ainda não incluídas em release |
| `G:\SFT\TESTE` | Programas em manutenção |

**Fluxo obrigatório:**
1. Está em `TESTE`? → consultar analista.
2. Está em `CONCLUIDO`? → copiar para `TESTE`, depois alterar.
3. Não está em nenhum? → copiar de `ULTIMA_VERSAO` para `TESTE`. Scripts `.TAB` / `.FK` podem ser criados diretamente em `TESTE`.

---

## Owner Padrão por Módulo

| Módulo | Owner Correto | Owners Proibidos |
|---|---|---|
| Industrial (novo) | `SFT` | `estoque`, `pcp`, `generico`, etc. |
| Varejo retaguarda (novo) | `CORPORATIVO` | `varejo`, `varejo_dig`, etc. |
| Varejo lojas PDV (novo) | `PDV` | `varejo`, `varejo_dig`, etc. |

---

## Tablespace por Owner

| Owner | Dados | Índices |
|---|---|---|
| `SFT` | `SFT_DADOS` | `SFT_INDICES` |
| `ESTOQUE` | `SFT_DADOS` | `SFT_INDICES` |
| `GENERICO` | `SFT_DADOS` | `SFT_INDICES` |
| `PCP` | `SFT_DADOS` | `SFT_INDICES` |
| `PATRIMONIO` | `SFT_DADOS` | `SFT_INDICES` |
| `LOG` | `LOGS` | — |
| `LOG_VAREJO` | `LOGS_VAREJO` | — |
| `CORPORATIVO` | `SFT_VAREJO_DADOS` | `SFT_VAREJO_INDICES` |
| `PDV` | `SFT_VAREJO_DADOS` | `SFT_VAREJO_INDICES` |
| `SFTC_CREDIARIO` | `SFT_VAREJO_DADOS` | `SFT_VAREJO_INDICES` |

---

## Nomenclatura e Extensões

| Objeto | Extensão | Prefixo Padrão |
|---|---|---|
| Tabela | `.tab` | `sft_nome_tabela` |
| Tabela de Log | `.tab` | `lsft_nome_tabela` |
| Foreign Key | `.fk` | `sft_nome_tabela_col_fk` |
| Package | `.pck` | `sft_pck_nome_package` |
| Function | `.fnc` | `sft_fnc_nome_function` |
| Procedure | `.prc` | `sft_prc_nome_procedure` |
| Sequence | `.seq` | `sft_seq_nome_sequence` |
| View | `.vw` | `sft_vw_nome_view` |
| Trigger before (statement) | `.trg` | `trgb_s_sft_nome` |
| Trigger after (statement) | `.trg` | `trga_s_sft_nome` |
| Trigger before (row) | `.trg` | `trgb_sft_nome` |
| Trigger after (row) | `.trg` | `trga_sft_nome` |
| Trigger log (before row) | `.trg` | `trgl_sft_nome_log` |
| Índice | `.tab` ou `.fk` | `owner.nome_idx` |

---

## Regras Gerais de Código PL/SQL

- Código em **caixa baixa**, exceto strings entre aspas simples que devem ser em **CAIXA ALTA**.
- Indentação de **2 espaços** (escrita começa no 3º caractere).
- **Nunca** usar nomes genéricos como `c1`, `c2`, `v1`, `r1` para cursores e variáveis — usar nomes descritivos.
- Sempre colocar `/` no final de blocos Package, Function, Procedure e Trigger.
- Comentários em scripts `.tab`, `.fk`, `.seq`: usar `--` por linha. **Nunca** usar `/* */`.

---

## Regras de Scripts

- **Separador de comandos:** barra `/` — nunca ponto e vírgula.
- **Nunca** usar `EXECUTE IMMEDIATE` para alterações de tabela, FKs ou sequences.
- **Separar** alterações de tabela em scripts `.tab` e blocos PL/SQL em scripts `.sql`.
- **Nunca** incluir `ALTER TABLE` com vírgula em serviços de outros objetos — criar novo comando.
- Scripts de atualização em tabelas com replicação devem usar `tc_pck_util.fnc_matriz`.
- Rename de tabela: nunca usar `conn` + `rename` diretamente — usar `owner.prc_executa_comando`.
- Grant entre owners: nunca usar `execute immediate 'grant...'` — usar procedures padrão (`sft_grant_corporativo`, `sft_grant_log`, etc.).

---

## Referências Detalhadas

### Padrões de código
- [Manutenção de Tabelas](docs/tabelas.md)
- [Manutenção de Objetos](docs/objetos.md)
- [Padrões de Scripts SQL](docs/scripts.md)
- [Criação de Parâmetros](docs/parametros.md)
- [Templates de Referência (código)](templates/README.md)

### Templates de prompts para Claude
- [Visão geral](prompts/README.md)
- [Prompts base (PL/SQL, debug, tuning, code review)](prompts/prompts-base.md)
- [Prompts por cenário (APEX, packages, integrações, Forms, DDL/DML)](prompts/prompts-cenarios.md)
- [Prompts para serviços com DF e chamados](prompts/prompts-servico-chamado.md)
