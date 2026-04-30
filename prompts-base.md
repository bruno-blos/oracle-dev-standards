# Prompts Base

> Templates fundamentais para tarefas técnicas pontuais.
> Pressupõe que o Claude tem acesso ao `CLAUDE.md` do repositório.

---

## Contexto mínimo

Para conversas em ferramentas que **não têm acesso automático** ao `CLAUDE.md` (ex: chat web sem o repositório anexado), inicie com:

```
Estou desenvolvendo no contexto SFT (Oracle 11g, Forms 6.0.8.27.0, APEX 20.1).
Siga os padrões do repositório oracle-dev-standards (CLAUDE.md, docs/).

Restrições:
- Não execute commit; apenas me forneça o código para revisão.
- Não invente nomes de tabelas/colunas; pergunte se faltar informação.
- Aponte inconsistências antes de finalizar.
- Aponte impacto em tabelas com replicação.
- Aponte triggers que precisam ser desabilitadas antes da execução.
```

Em ambientes onde o repositório está conectado, omita esse bloco — basta lembrar o Claude de seguir o `CLAUDE.md` se ele desviar.

---

## TEMPLATE 1 — Geração de código PL/SQL

```
<objetivo>
Preciso de [package/function/procedure/trigger] que [descrição funcional].
</objetivo>

<modulo>
[ ] Industrial (owner SFT)
[ ] Varejo retaguarda (owner CORPORATIVO)
[ ] Varejo PDV (owner PDV)
[ ] Outro: ___
</modulo>

<estruturas>
Tabelas envolvidas:
- TABELA_X (colunas relevantes: ...)
- TABELA_Y (...)
</estruturas>

<regras_negocio>
1. [regra 1]
2. [regra 2]
</regras_negocio>

<saida_esperada>
- Código completo seguindo padrão do CLAUDE.md (caixa baixa, indentação 2,
  nomes descritivos, prefixo correto, owner correto)
- Indicar onde devo fazer o commit (não inclua no código)
- Listar premissas que você assumiu
- Indicar nome do arquivo (.pck/.fnc/.prc/.trg) conforme padrão
</saida_esperada>
```

---

## TEMPLATE 2 — Debugging / análise de erro

```
<problema>
Erro: [ORA-XXXXX: mensagem completa]
Ao executar: [contexto - rotina, trigger, batch, tela do Forms, página APEX]
</problema>

<codigo_envolvido>
[colar trecho relevante]
</codigo_envolvido>

<o_que_ja_tentei>
- [tentativa 1 e resultado]
- [tentativa 2 e resultado]
</o_que_ja_tentei>

<pedido>
1. Liste causas prováveis em ordem de probabilidade
2. Para cada causa, indique como confirmar (query, log, teste)
3. Sugira a correção respeitando os padrões do CLAUDE.md
4. Forneça o código sem commit
5. Aponte se a correção exige desabilitar triggers ou afeta replicação
</pedido>
```

---

## TEMPLATE 3 — Tuning de performance (Oracle 11g)

```
<query_lenta>
[colar SQL]
</query_lenta>

<plano_execucao>
[colar EXPLAIN PLAN se tiver]
</plano_execucao>

<dados>
- Volume aproximado: TABELA_X tem ~N linhas
- Índices existentes: [listar]
- Tempo atual: [X segundos]
- Tempo alvo: [Y segundos]
- Owner: [SFT / CORPORATIVO / etc.]
</dados>

<pedido>
1. Aponte os gargalos prováveis
2. Sugira otimizações em ordem de impacto
3. Para cada sugestão, indique trade-offs
4. Considere apenas recursos disponíveis no 11g
   (sem MATCH_RECOGNIZE, sem APPROX_COUNT_DISTINCT, sem In-Memory, etc.)
5. Se sugerir índice novo, indique tablespace correta (sft_indices,
   sft_varejo_indices, etc.) conforme owner
6. Se sugerir alteração estrutural, informe se afeta replicação
</pedido>
```

---

## TEMPLATE 4 — Conversão Forms → APEX

```
<origem_forms>
Tela: [nome.fmb]
Bloco principal: [nome]
Funcionalidade: [descrição]
Triggers principais:
- WHEN-NEW-FORM-INSTANCE: [lógica]
- WHEN-VALIDATE-ITEM em X: [lógica]
- KEY-COMMIT: [lógica]
- Outras: [...]
</origem_forms>

<destino_apex>
Aplicação: [nome/número]
Página alvo: [nova / existente nº __]
Comportamentos a preservar:
- [comportamento 1]
- [comportamento 2]
</destino_apex>

<pedido>
1. Sugira a estrutura da página APEX (regions, items, processes,
   dynamic actions)
2. Para cada trigger Forms, indique o equivalente APEX (DA, Process,
   Validation, Computation) e justifique
3. Aponte limitações ou pontos de atenção da conversão
4. Use APIs APEX 20.1 (não use recursos de 21+)
5. Para captura de usuário em logs, use o padrão do CLAUDE.md:
   nvl(sys_context('APEX$SESSION','APP_USER'), user)
6. Pergunte antes de assumir comportamentos não óbvios
</pedido>
```

---

## TEMPLATE 5 — Documentação técnica

```
<objetivo>
Gerar documentação para [package/procedure/processo].
</objetivo>

<audiencia>
[ ] Outros desenvolvedores (manutenção)
[ ] Analistas de negócio
[ ] Suporte N1
[ ] Usuários finais
</audiencia>

<material_fonte>
[colar código ou descrição do processo]
</material_fonte>

<formato>
- [ ] Markdown
- [ ] Texto corrido
- [ ] Tópicos
Tamanho: [aproximado]
Incluir: propósito, entradas, saídas, exceções, exemplo de uso,
         dependências (tabelas, packages, parâmetros)
Não incluir: [o que evitar]
</formato>
```

---

## TEMPLATE 6 — Análise / decisão técnica

```
<situacao>
Estou avaliando [decisão - ex: usar Materialized View vs. tabela auxiliar
atualizada por job, ou fazer X em Forms vs. migrar para APEX].
</situacao>

<criterios>
Em ordem de importância:
1. [critério 1 - ex: performance de leitura]
2. [critério 2 - ex: simplicidade de manutenção]
3. [critério 3 - ex: tempo de implementação]
</criterios>

<pedido>
1. Compare alternativas pelos critérios listados
2. Considere restrições do ambiente (11g, Forms 6, APEX 20.1)
3. Aponte riscos de cada opção
4. Faça uma recomendação justificada
5. Indique se faltam informações para uma decisão melhor
</pedido>
```

---

## TEMPLATE 7 — Code review

```
<codigo_para_revisar>
[colar código]
</codigo_para_revisar>

<foco_da_revisao>
[ ] Aderência ao CLAUDE.md (nomenclatura, owner, indentação)
[ ] Correção funcional
[ ] Performance
[ ] Tratamento de exceções
[ ] Segurança (SQL injection, privilégios, escape em APEX)
[ ] Manutenibilidade
[ ] Impacto em replicação
</foco_da_revisao>

<pedido>
Para cada problema encontrado:
1. Linha/trecho
2. Severidade (crítico / importante / sugestão)
3. Por que é problema (citar regra do CLAUDE.md se aplicável)
4. Como corrigir (sem commit)
</pedido>
```

---

## TEMPLATE 8 — Comunicação (email, documento, mensagem)

```
<situacao>
Preciso escrever [tipo de mensagem] para [destinatário].
</situacao>

<contexto>
[o que aconteceu / o que motivou]
</contexto>

<objetivo>
Quero que o destinatário [aja, entenda, decida...]
</objetivo>

<tom>
[formal / direto / cordial / técnico]
</tom>

<restricoes>
- Tamanho: [curto / médio / detalhado]
- Evitar: [jargão / acusações / promessas firmes...]
</restricoes>
```

---

## Dicas gerais de uso

1. **Comece pelo template, mas adapte.** Remova seções que não se aplicam.
2. **Itere.** Se a primeira resposta não foi ideal, refine com:
   - "Considere também [aspecto X]"
   - "Refaça assumindo que [premissa Y]"
   - "Revise procurando aderência ao docs/objetos.md"
3. **Peça revisão final** para tarefas críticas:
   - "Antes de finalizar, revise procurando: COMMIT escondido,
     nomenclatura fora do padrão, EXECUTE IMMEDIATE em DDL,
     impacto em replicação não declarado."
4. **Salve seus melhores prompts** como variações pessoais quando algo funcionar bem.
