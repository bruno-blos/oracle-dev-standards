# Prompts para Serviços com DF e Chamados de Manutenção

> Templates para o fluxo formal de demandas: serviços (com Definição
> Funcional) e chamados (manutenção pontual).
> Pressupõe conhecimento dos padrões do `CLAUDE.md` e `docs/`.

---

## TEMPLATE A — Serviço com Definição Funcional (DF)

> Use quando receber um serviço novo com DF (migração Forms→APEX, nova
> funcionalidade, mudança estrutural). Trabalhe em **fases**, não tente
> resolver tudo num prompt só.

### FASE 1 — Análise inicial da DF

```
<servico>
Número: [SRV-XXXXX]
Tipo: [ ] Migração Forms→APEX
       [ ] Nova funcionalidade
       [ ] Alteração estrutural
       [ ] Outro: ___
Sistema/módulo afetado: [Industrial / Varejo retaguarda / PDV]
Owner principal: [SFT / CORPORATIVO / PDV / etc.]
</servico>

<verificacao_diretorios>
Antes de tudo, preciso verificar onde o programa está
(conforme regra do CLAUDE.md):
- Cliente: [ ] Bottero  [ ] Outros SFT
- Programa já está em TESTE? [sim / não / verificar]
- Se não está em TESTE, fonte: [APLIC / ULTIMA_VERSAO / CONCLUIDO]
</verificacao_diretorios>

<definicao_funcional>
[colar trechos relevantes da DF aqui — pode ser por partes]
</definicao_funcional>

<pedido_fase_1>
Antes de implementar qualquer coisa, faça uma análise inicial:

1. RESUMO: descreva em suas palavras o que entendeu da DF
   (vou validar se está alinhado com minha leitura)

2. ETAPAS: quebre o serviço em etapas executáveis e independentes,
   numeradas em ordem lógica. Para cada etapa indique:
   - O que precisa ser feito
   - Que tipo de objeto será criado/alterado
     (.tab, .fk, .pck, .fnc, .prc, .trg, .seq, .vw, página APEX, .fmb)
   - Owner correto (conforme módulo)
   - Pré-requisitos (depende de qual etapa anterior)

3. AMBIGUIDADES: liste pontos da DF pouco claros ou ambíguos.
   Para cada um, sugira a pergunta a ser feita ao analista.

4. INFORMAÇÕES FALTANTES: o que não está na DF mas é necessário
   (estrutura de tabelas, regras de negócio implícitas, integrações,
   permissões, parâmetros novos, comportamentos em casos limite).

5. RISCOS E DEPENDÊNCIAS:
   - Riscos técnicos (impacto em outros sistemas/telas, performance,
     compatibilidade com 11g/Forms 6/APEX 20.1)
   - Tabelas com replicação envolvidas
   - Triggers que precisarão ser desabilitadas
   - Dependências externas (DBA, infraestrutura, outras equipes)
   - Pontos de atenção específicos da migração Forms→APEX (se aplicável)

6. NÃO comece a gerar código ainda. Vou validar a análise primeiro
   e responder as ambiguidades antes de prosseguir para a Fase 2.
</pedido_fase_1>
```

### FASE 2 — Implementação por etapa

> Após validar a Fase 1, use este prompt para cada etapa.

```
<contexto_servico>
Continuando o serviço SRV-XXXXX.
Estamos na ETAPA [N]: [descrição da etapa]
Objeto a criar/alterar: [tipo + nome conforme nomenclatura do CLAUDE.md]
Owner: [...]
</contexto_servico>

<respostas_ambiguidades>
[colar aqui as respostas obtidas do analista para as dúvidas
levantadas na Fase 1, se aplicável a esta etapa]
</respostas_ambiguidades>

<estruturas_envolvidas>
[colar DDL ou descrição das tabelas/objetos envolvidos]
</estruturas_envolvidas>

<pedido>
1. Implemente esta etapa específica seguindo o que foi acordado
   na análise inicial
2. Siga rigorosamente os padrões do CLAUDE.md e docs/ correspondente:
   - docs/tabelas.md para .tab/.fk
   - docs/objetos.md para .pck/.fnc/.prc/.trg/.seq/.vw
   - docs/scripts.md para .sql
   - docs/parametros.md para parâmetros
3. Forneça o código sem commit, indicando onde devo colocá-lo
4. Indique nome correto do arquivo (extensão e prefixo)
5. Liste premissas que assumiu nesta implementação
6. Aponte o que mudou em relação ao planejado (se algo mudou)
7. Indique se esta etapa cria dependência para etapas posteriores
8. Aponte se afeta replicação ou exige desabilitar triggers
9. Pergunte antes de assumir comportamentos não óbvios
</pedido>
```

### FASE 3 — Checklist de testes

> Use ao final, depois de implementadas as etapas principais.

```
<contexto_servico>
Serviço SRV-XXXXX está com a implementação concluída.
Preciso preparar o roteiro de testes para HML/PRD.
</contexto_servico>

<resumo_implementado>
[breve resumo do que foi feito — pode listar as etapas concluídas]
</resumo_implementado>

<pedido>
Gere um checklist de testes estruturado contendo:

1. CENÁRIOS POSITIVOS (caminho feliz):
   - O que testar
   - Resultado esperado
   - Como validar (em tela, em banco, em log)

2. CENÁRIOS NEGATIVOS (validações, erros esperados):
   - Entrada inválida
   - Mensagem/comportamento esperado

3. EDGE CASES específicos:
   - Valores limite
   - Concorrência (se relevante)
   - Volume (se relevante)
   - Casos por cliente (se houver lógica via sft_fnc_parametro)

4. TESTES DE REGRESSÃO:
   - Funcionalidades existentes que podem ter sido impactadas
   - O que verificar em telas/processos relacionados

5. VALIDAÇÃO EM BANCO:
   - Queries de verificação antes/depois
   - Counts, integridade referencial, dados esperados
   - Verificação de logs (lsft_) gerados corretamente

6. VALIDAÇÃO DE REPLICAÇÃO (se aplicável):
   - Confirmar replicação na matriz e filiais
   - Checar se trigger de replicação foi recriada quando necessário

7. ROLLBACK:
   - Como reverter se algo der errado em PRD
   - Pontos de não-retorno (se houver)

Organize em formato de checklist (caixas marcáveis) para acompanhar
a execução.
</pedido>
```

---

## TEMPLATE B — Chamado de manutenção

> Use para chamados pontuais (bug em produção, ajuste, melhoria pequena).
> Mais enxuto que o Template A, mas com a mesma disciplina de validar
> a análise antes de codar.

### Análise do chamado

```
<chamado>
Número: [CH-XXXXX]
Tipo: [ ] Bug em produção
       [ ] Ajuste pontual de usuário
       [ ] Melhoria pequena (campo, validação, label)
Sistema/módulo: [Industrial / Varejo retaguarda / PDV]
Owner: [SFT / CORPORATIVO / PDV / etc.]
Ambiente afetado: [ ] Forms  [ ] APEX  [ ] Banco (job/package/proc)
Criticidade: [ ] Alta (impede operação)
             [ ] Média (contorno existe)
             [ ] Baixa (cosmético/melhoria)
</chamado>

<descricao_do_chamado>
[colar a descrição como veio do usuário/suporte]
</descricao_do_chamado>

<evidencias>
[colar prints, mensagens de erro, queries que reproduzem o problema,
exemplos de dados afetados]
</evidencias>

<o_que_ja_investiguei>
[opcional — se já olhou algum código, executou alguma query,
suspeita de algo específico]
</o_que_ja_investiguei>

<pedido>
1. ENTENDIMENTO: descreva em suas palavras o que entendeu do chamado
   e classifique o tipo real (às vezes vem como "bug" mas é ajuste
   de regra, ou vice-versa)

2. ANÁLISE:
   - Para BUG: liste causas prováveis em ordem de probabilidade,
     com forma de confirmar cada uma
   - Para AJUSTE/MELHORIA: liste o que precisa ser alterado e onde

3. AMBIGUIDADES E DÚVIDAS: o que está pouco claro e que perguntas
   eu deveria fazer ao usuário/analista antes de implementar

4. INFORMAÇÕES FALTANTES: estruturas de tabela, código atual, ou
   dados que você precisa ver para dar uma solução precisa

5. RISCOS E DEPENDÊNCIAS:
   - O que mais pode ser afetado pela alteração
   - Outras telas/processos que usam o mesmo código
   - Tabelas com replicação envolvidas
   - Triggers que precisarão ser desabilitadas
   - Cuidados específicos por estar em produção (se aplicável)

6. PROPOSTA DE SOLUÇÃO: descreva a abordagem ANTES do código.
   Se houver alternativas, liste-as com prós e contras.

7. NÃO gere o código ainda. Vou validar a análise e responder
   as dúvidas. Depois disso peço a implementação.
</pedido>
```

### Implementação do chamado

> Após validar a análise.

```
<contexto_chamado>
Continuando o chamado CH-XXXXX.
Análise validada. Solução acordada: [resumir abordagem escolhida]
</contexto_chamado>

<respostas_duvidas>
[respostas obtidas]
</respostas_duvidas>

<codigo_atual>
[colar o código que será alterado, se houver]
</codigo_atual>

<pedido>
1. Implemente a solução acordada seguindo padrões do CLAUDE.md
   e docs/ aplicáveis
2. Forneça o código sem commit
3. Destaque CLARAMENTE o que mudou em relação ao código atual
   (linhas alteradas, adicionadas, removidas)
4. Para correção de bug: explique POR QUE a alteração corrige
5. Indique nome correto do arquivo (extensão + prefixo) caso
   seja arquivo novo, ou em qual arquivo existente alterar
6. Liste premissas assumidas
7. Aponte se afeta replicação ou exige desabilitar triggers
8. Gere checklist de teste enxuto:
   - Como reproduzir o problema antes
   - Como validar que foi corrigido
   - O que verificar para garantir não-regressão
</pedido>
```

---

## Dicas de uso

### Quando usar cada template

| Situação | Template |
|----------|----------|
| Serviço novo com DF formal | Template A (Fases 1, 2, 3) |
| Migração Forms→APEX completa | Template A |
| Nova funcionalidade não trivial | Template A |
| Bug em produção | Template B |
| Ajuste pequeno solicitado pelo usuário | Template B |
| Adicionar campo/validação | Template B |
| Investigação ainda sem solução clara | Template B (foco na análise) |

### Por que separar em fases

A separação **análise → implementação → testes** existe por:

1. **Captura ambiguidades cedo.** DF em Word costuma ter trechos
   ambíguos — resolver antes de codar economiza retrabalho.
2. **Evita prompts gigantes.** Tentar fazer tudo de uma vez gera
   respostas superficiais. Quebrar em etapas gera respostas mais
   profundas e fáceis de validar.
3. **Permite revisão em pontos naturais.** Validar análise antes
   de partir para código é o ponto mais barato de corrigir rumo.

### Adaptações comuns

- **Chamado muito simples** (ex: "trocar label do campo X"): pule
  a fase de análise, vá direto à implementação.
- **DF muito longa**: faça a Fase 1 por partes da DF, não tente
  colar tudo de uma vez.
- **Bug crítico em PRD**: na Fase de análise do Template B, peça
  também "workaround temporário enquanto a correção é homologada".

### Coisas que vale a pena fazer SEMPRE

1. **Validar o "RESUMO" / "ENTENDIMENTO"** antes de prosseguir.
   Se o Claude entendeu errado, todo o resto vai sair errado.
2. **Levar ambiguidades de volta ao analista/usuário** antes de
   começar a codar.
3. **Conferir COMMIT escondido** no código gerado, especialmente
   em blocos `EXCEPTION`.
4. **Para chamados em PRD**: pedir explicitamente o impacto da
   mudança em outras partes do sistema.
5. **Verificar diretório de origem** (TESTE / APLIC / ULTIMA_VERSAO /
   CONCLUIDO) ANTES de alterar qualquer programa, conforme regra
   do CLAUDE.md.
