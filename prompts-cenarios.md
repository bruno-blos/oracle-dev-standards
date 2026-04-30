# Prompts para Cenários Específicos

> Templates para cenários recorrentes do dia a dia.
> Pressupõe conhecimento dos padrões do `CLAUDE.md` e referências em `docs/`.

---

## TEMPLATE 9 — Relatórios APEX

```
<tipo_de_relatorio>
[ ] Interactive Report (somente leitura, filtros, exportação)
[ ] Interactive Grid (edição inline)
[ ] Classic Report
[ ] Chart (tipo: bar / line / pie / ...)
</tipo_de_relatorio>

<aplicacao_pagina>
Aplicação: [número/nome]
Página: [novo / existente nº __]
</aplicacao_pagina>

<fonte_de_dados>
- Tabela(s) / view(s) envolvida(s):
  - SCHEMA.TABELA_X (colunas relevantes: ...)
- Volume estimado: [linhas]
- Joins necessários: [descrever]
</fonte_de_dados>

<requisitos_funcionais>
- Colunas exibidas: [lista]
- Filtros: [quais, fixos ou pelo usuário]
- Ordenação padrão: [coluna e direção]
- Agrupamentos / totais: [descrever]
- Permissões: [quem vê o quê]
</requisitos_funcionais>

<requisitos_visuais>
- Formatação condicional: [ex: linhas vermelhas se status = X]
- Links para outras páginas: [coluna -> destino]
- Botões de ação: [editar, excluir, exportar, custom]
</requisitos_visuais>

<pedido>
1. Sugira estrutura da página (regions, items, processes)
2. Forneça SQL otimizado para 11g, usando nome do objeto
   (não sinônimo) e prefixando com owner
3. Indique configurações específicas dos atributos da region
4. Aponte cuidados de performance (especialmente em IR com volume)
5. Pergunte antes de assumir comportamentos não óbvios
</pedido>
```

---

## TEMPLATE 10 — Manutenção de packages legados

```
<tipo_de_tarefa>
[ ] Análise de impacto antes de alterar
[ ] Refactor para legibilidade/manutenção
[ ] Adição de funcionalidade em package existente
[ ] Identificação de código morto
</tipo_de_tarefa>

<package_atual>
Owner.nome: [SFT.SFT_PCK_NOME]
[colar spec e/ou body relevante]
</package_atual>

<contexto_de_uso>
- Onde é chamado: [Forms, APEX, jobs, outros packages]
- Frequência: [estimativa]
- Criticidade: [alta / média / baixa]
</contexto_de_uso>

<mudanca_pretendida>
[descrever o que precisa mudar e por quê]
</mudanca_pretendida>

<pedido>
1. Liste dependências aparentes (chamadas internas, tabelas acessadas,
   possíveis chamadores externos via Forms/APEX/jobs)
2. Sugira queries em DBA_DEPENDENCIES / DBA_SOURCE para confirmar
   chamadores reais
3. Aponte riscos da mudança (efeitos colaterais, contratos quebrados)
4. Sugira estratégia de refactor em passos pequenos e testáveis
5. Forneça código alterado preservando comportamento existente,
   seguindo padrões do docs/objetos.md
6. Indique pontos que merecem teste específico antes do deploy
7. Pergunte sobre comportamentos ambíguos antes de assumir
</pedido>
```

---

## TEMPLATE 11 — Integrações

```
<tipo_de_integracao>
[ ] Consumo de web service (SOAP / REST via UTL_HTTP)
[ ] Exposição de web service
[ ] Leitura de arquivo (CSV / TXT / XML / posicional)
[ ] Geração de arquivo
[ ] Job agendado (DBMS_SCHEDULER / DBMS_JOB)
[ ] Integração via tabela intermediária / fila
</tipo_de_integracao>

<especificacao_externa>
[colar contrato: WSDL, exemplo de payload, layout do arquivo,
endpoint, autenticação esperada]
</especificacao_externa>

<requisitos>
- Frequência / volume: [tempo real / batch / N por dia]
- Tratamento de erro: [retry, fila de erro, notificação]
- Logging: [tabela de log, nível de detalhe]
- Idempotência: [necessária? como garantir?]
- Janela de execução: [horário, tempo máximo]
</requisitos>

<restricoes_ambiente>
- ACL configurada para o host: [sim / não / não sei]
- Wallet para HTTPS: [sim / não / não sei]
- Diretório Oracle (DIRECTORY): [nome ou "preciso criar"]
- Privilégios necessários: [listar]
</restricoes_ambiente>

<pedido>
1. Sugira arquitetura da integração (fluxo, objetos envolvidos,
   tabela de log se aplicável - seguindo padrão lsft_)
2. Liste pré-requisitos (ACL, wallet, directory, grants) para solicitar ao DBA
3. Forneça código PL/SQL com tratamento de erro e logging,
   seguindo padrões de package do docs/objetos.md
4. Não inclua commits; indique onde devo colocá-los
5. Aponte riscos específicos do 11g (limitações UTL_HTTP, DBMS_CRYPTO antigo)
6. Sugira estratégia de teste isolado antes da integração
</pedido>
```

---

## TEMPLATE 12 — Triggers Forms

```
<tela_forms>
Arquivo: [nome.fmb]
Bloco: [nome]
Item / nível: [item específico ou nível do bloco/form]
</tela_forms>

<trigger_alvo>
[ ] WHEN-NEW-FORM-INSTANCE
[ ] WHEN-NEW-BLOCK-INSTANCE
[ ] WHEN-NEW-RECORD-INSTANCE
[ ] WHEN-NEW-ITEM-INSTANCE
[ ] WHEN-VALIDATE-ITEM
[ ] WHEN-VALIDATE-RECORD
[ ] POST-QUERY
[ ] PRE-INSERT / PRE-UPDATE / PRE-DELETE
[ ] KEY-COMMIT
[ ] KEY-NEXT-ITEM
[ ] Outra: ___
</trigger_alvo>

<comportamento_desejado>
[descrever em linguagem natural]
</comportamento_desejado>

<contexto_da_tela>
- Itens relacionados envolvidos: [listar]
- Blocos relacionados (master/detail): [descrever]
- LOVs envolvidas: [se aplicável]
- Variáveis globais ou de package usadas: [listar]
</contexto_da_tela>

<pedido>
1. Forneça o código PL/SQL da trigger
2. Indique nível correto (form / block / item) e momento
3. Use built-ins compatíveis com Forms 6.0.8.27.0
4. Trate FORM_TRIGGER_FAILURE quando aplicável
5. Aponte interações com outras triggers que podem causar conflito
6. Sugira como testar (cenários positivos e negativos)
7. Pergunte antes de assumir comportamentos não óbvios
</pedido>
```

---

## TEMPLATE 13 — Dynamic Actions e processos APEX

```
<tipo>
[ ] Dynamic Action (cliente — JavaScript / PL/SQL via AJAX)
[ ] Page Process (servidor)
[ ] Validation
[ ] Computation
[ ] Branch
</tipo>

<pagina_apex>
Aplicação: [nº/nome]
Página: [nº]
Tipo: [form / report / blank / wizard / ...]
Items relevantes: [P1_X, P1_Y, ...]
Regions relevantes: [nome / static ID]
</pagina_apex>

<evento_ou_momento>
- Quando deve disparar: [ex: change em P1_X / Page Submit /
  Before Header / quando botão Y é clicado]
- Condição: [ex: somente se P1_TIPO = 'A']
</evento_ou_momento>

<acao_desejada>
[descrever em linguagem natural]
Exemplos:
- "Mostrar/ocultar region"
- "Recalcular total e atualizar item"
- "Validar duplicidade no banco antes de submit"
- "Atualizar combo dependente"
</acao_desejada>

<dados_envolvidos>
- Items que entram: [lista]
- Items que saem (Page Items to Return): [lista]
- Tabelas consultadas/alteradas: [lista, com owner]
</dados_envolvidos>

<pedido>
1. Indique a configuração da DA/Process (event, selection type,
   true/false actions, server-side condition)
2. Para PL/SQL, forneça código sem commit
3. Para JS, use APIs APEX 20.1 (apex.item, apex.server.process,
   apex.message) — não use recursos de 21+
4. Aponte cuidados de segurança (escape de bind variables,
   session state protection)
5. Para captura de usuário em logs, use o padrão do CLAUDE.md:
   nvl(sys_context('APEX$SESSION','APP_USER'), user)
6. Sugira como debugar (Debug mode, console)
</pedido>
```

---

## TEMPLATE 14 — Scripts DDL/DML

> Importante: este template referencia diretamente as regras de
> `docs/tabelas.md` e `docs/scripts.md`. O Claude deve seguir
> rigorosamente os padrões lá descritos.

```
<tipo_de_script>
[ ] Criação de novos objetos (tabela, índice, sequence, view, package)
[ ] Alteração de estrutura (ALTER TABLE, novo índice, etc.)
[ ] Migração / carga de dados (INSERT em massa, UPDATE em lote)
[ ] Limpeza / deprecação de objetos
</tipo_de_script>

<owner_e_modulo>
Owner: [SFT / CORPORATIVO / PDV / LOG / etc.]
Módulo: [Industrial / Varejo retaguarda / Varejo PDV / Outro]
</owner_e_modulo>

<objetivo>
[descrever a finalidade de negócio]
</objetivo>

<especificacao>
Para criação de tabela:
- Nome: [seguindo prefixo do owner — ex: sft_, lsft_, tc_]
- Colunas: [nome, tipo, nullable, default, descrição]
- PK: [colunas]
- FKs: [coluna -> tabela.coluna]
- Índices adicionais: [colunas e tipo]
- Tabela tem replicação? [sim / não / não sei]
- Tabela tem trigger de log? [sim / não]

Para migração de dados:
- Origem: [tabela / arquivo / sistema]
- Destino: [tabela]
- Volume estimado: [linhas]
- Regras de transformação: [descrever]
- Tratamento de duplicidade: [ignorar / atualizar / falhar]
- Tabela alvo tem replicação? [sim / não]
</especificacao>

<requisitos_nao_funcionais>
- Janela de execução: [tempo disponível]
- Reversível: [precisa de script de rollback?]
- Ambiente alvo: [DEV / HML / PRD]
</requisitos_nao_funcionais>

<pedido>
1. Forneça scripts em ordem de execução numerada
2. SEPARE OBRIGATORIAMENTE em arquivos distintos:
   - .tab para DDL de tabela/FK/índice
   - .sql para DML e blocos PL/SQL
   - .pck/.fnc/.prc/.trg/.seq/.vw conforme objeto
3. Use SEMPRE separador `/` (nunca `;`)
4. NUNCA use EXECUTE IMMEDIATE para ALTER TABLE, FKs ou sequences
5. Comentários em .tab/.fk/.seq apenas com `--` (nunca `/* */`)
6. Tablespace correta conforme owner (ver CLAUDE.md):
   - SFT/ESTOQUE/PCP/etc → sft_dados / sft_indices
   - CORPORATIVO/PDV → sft_varejo_dados / sft_varejo_indices
   - LOG → logs
   - LOG_VAREJO → logs_varejo
7. Crie sinônimo público (exceto triggers de banco)
8. Libere grants para R_MENU e owners que usam (exceto triggers)
9. Para grants entre owners, use procedures padrão (sft_grant_corporativo,
   sft_grant_log, etc.) — nunca execute immediate 'grant...'
10. Para tabelas com replicação, envolver DML em
    `if tc_pck_util.fnc_matriz then ... end if;`
11. Para alteração que envolve coluna nova com default em tabela com
    trigger de log: desabilitar triggers antes, reabilitar depois
12. Para tabela de log (lsft_), incluir os 6 campos padrão no início
13. Inclua scripts de validação (counts, checks) antes/depois quando aplicável
14. Forneça script de rollback quando aplicável
15. Aponte impactos (locks, tempo estimado, espaço)
16. Sinalize comandos que exigem privilégio de DBA
17. NÃO inclua COMMIT no código — indique onde devo colocá-lo
18. Pergunte antes de assumir nomes ou regras não declaradas
</pedido>
```

---

## Dicas específicas

**Para APEX (templates 9 e 13):** sempre informe número da página e
static IDs/names dos elementos. Isso evita respostas genéricas.

**Para Forms (template 12):** Forms 6 não tem alguns built-ins modernos.
Se o Claude sugerir algo de versões superiores, peça alternativa
compatível com 6.0.8.27.0.

**Para integrações (template 11):** confirme com o DBA os pré-requisitos
de ambiente (ACL, wallet, directory) antes de implementar — código
correto pode falhar por falta dessas configurações.

**Para refactor (template 10):** peça análise de impacto SEMPRE antes
do refactor. Em packages legados é comum haver chamadores não documentados,
especialmente em telas Forms antigas.

**Para DDL/DML (template 14):** mesmo com a regra explícita de não-commit,
revise visualmente o script gerado — peça também:
> "Revise o script procurando: COMMIT escondido, ALTER TABLE em mesmo
> arquivo de PL/SQL, EXECUTE IMMEDIATE em DDL, ponto e vírgula como
> separador, owner errado para o módulo, tablespace errada para o owner."
