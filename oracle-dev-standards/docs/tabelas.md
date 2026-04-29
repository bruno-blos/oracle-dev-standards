# Manutenção de Tabelas

## Regras Gerais (.tab)

- Extensão obrigatória: `.tab`
- Usar nome da **tabela**, nunca do sinônimo
- Sempre prefixar com owner: `sft.nome_tabela`
- Criar sinônimo público em toda tabela nova
- Liberar `grant select, insert, update, delete` para a role `R_MENU` e owners que usarão diretamente
- Tablespace de acordo com o owner (ver tabela em `CLAUDE.md`)

---

## Criação de Tabela (owner != LOG)

```sql
-- OWNER: SFT
create table sft.sft_nome_tabela
(nome_coluna1 number(05) not null
,nome_coluna2 varchar2(10) not null
,nome_coluna3 number(05) not null
,nome_coluna4 varchar2(100) not null
,nome_coluna5 number(05)
,nome_coluna6 varchar2(230)
,nome_coluna7 varchar2(01) default 'N' not null
,nome_coluna8 varchar2(01) default 'E' not null
,nome_coluna9 number(15,2) default 0 not null)
tablespace sft_dados
/

alter table sft.sft_nome_tabela
add constraint sft_nome_tabela_pk
primary key (nome_coluna1)
using index tablespace sft_indices
/

alter table sft.sft_nome_tabela
add constraint sft_nome_tabela_col1_col2_uk
unique (nome_coluna1, nome_coluna2)
using index tablespace sft_indices
/

alter table sft.sft_nome_tabela
add constraint sft_nome_tabela_col1_ck
check (nome_coluna1 in ('S','N'))
/

create or replace public synonym sft_nome_tabela for sft.sft_nome_tabela
/

grant select, insert, update, delete on sft_nome_tabela to r_menu
/
```

---

## Criação de Tabela Temporária

```sql
-- OWNER: SFT
create table sft.sft_parametro_relatorio_temp (
sessao number(10) not null,
codigo1 number(20),
valor1 number(18,6),
data1 date,
descricao1 varchar2(500))
tablespace sft_dados
/

create or replace public synonym sft_parametro_relatorio_temp for
sft.sft_parametro_relatorio_temp
/

grant select, insert, update, delete on sft.sft_parametro_relatorio_temp to r_menu
/

begin
  sft_pck_limpa_tabela_sistema.prc_insere_tabela(
     powner               => 'sft'
    ,ptabela              => 'sft_parametro_relatorio_temp'
    ,pnome_coluna_sesao   => 'sessao'
    ,pdata_ultima_limpeza => trunc(sysdate)
    ,pdias_permanencia    => 1
    ,ptipo_limpeza        => 'T'
    ,pcondicao_limpeza    => null);
end;
/
```

---

## Criação de Tabela de Log (owner LOG)

Tablespace obrigatória: `LOGS`. Prefixo: `lsft_`. Contém 6 campos extras no início.

```sql
-- OWNER: LOG
create table log.lsft_nome_tabela
( tipo_movimento_log    varchar2(1)
, usuario_movimento_log varchar2(30)
, data_movimento_log    date
, usuario_rede_log      varchar2(30)
, maquina_usuario_log   varchar2(70)
, programa_log          varchar2(30)
, nome_coluna1          tipo_de_dado
, nome_coluna2          tipo_de_dado
, nome_coluna3          tipo_de_dado
) tablespace LOGS
/

create or replace public synonym lsft_nome_tabela for log.lsft_nome_tabela
/

begin
  sft_grant_log('select,insert','log.lsft_nome_tabela','R_MENU','G');
end;
/

begin
  sft_grant_log('select,insert','log.lsft_nome_tabela','sft','G');
end;
/
```

---

## Adicionar Coluna COM Default

Sempre desabilitar triggers antes e reabilitar depois.

### Opção 1 — via package

```sql
-- OWNER: SFT
begin
  pck_util.v_desativar_triggers := true;
end;
/

alter table sft.sft_nome_tabela add nome_coluna varchar2(1)
/

alter table sft.sft_nome_tabela modify nome_coluna default 'N'
/

begin
  pck_util.v_desativar_triggers := false;
end;
/
```

### Opção 2 — desabilitar individualmente

```sql
-- OWNER: SFT
alter trigger sft.trgl_sft_nome_tabela disable
/
alter trigger sft.trgb_sft_nome_tabela disable
/

alter table sft.sft_nome_tabela add nome_coluna varchar2(1)
/

alter table sft.sft_nome_tabela modify nome_coluna default 'N'
/

alter trigger sft.trgl_sft_nome_tabela enable
/
alter trigger sft.trgb_sft_nome_tabela enable
/
```

---

## Adicionar Coluna COM Default e NOT NULL

Requer dois scripts separados.

**Script 1: NOME_TABELA.tab**

```sql
-- OWNER: SFT
alter table sft.sft_nome_tabela add (nome_coluna varchar2(1))
/

alter trigger sft.trgl_sft_nome_tabela disable
/
alter trigger sft.trgb_sft_nome_tabela disable
/

alter table sft.sft_nome_tabela modify nome_coluna default 'N'
/

alter table sft.sft_nome_tabela modify nome_coluna not null
/

alter trigger sft.trgl_sft_nome_tabela enable
/
alter trigger sft.trgb_sft_nome_tabela enable
/
```

**Script 2: LSFT_NOME_TABELA.tab**

```sql
-- OWNER: LOG
alter table log.lsft_nome_tabela add (nome_coluna tipo_de_dado)
/
```

---

## Constraints

### Primary Key

```sql
-- OWNER: SFT
alter table sft.sft_nome_tabela
add constraint sft_nome_tabela_pk
primary key (col1, col2)
using index tablespace sft_indices
/
```

### Foreign Key (sempre criar índice)

```sql
-- OWNER: PCP
alter table pcp.pedido
add constraint pedido_rep_vinc_marca_fk
foreign key (codigo_pessoa_repres_vinculado, codigo_marca)
references generico.representante_marca(codigo_pessoa, codigo_marca)
/

create index pcp.rep_vinc_idx
on pcp.pedido (codigo_pessoa_repres_vinculado)
tablespace sft_indices
/
```

> FK: criar índice exceto se já existir índice de PK ou UQ para a mesma coluna.

### Unique

```sql
-- OWNER: SFT
alter table sft.sft_fase_linha
add constraint sft_fase_linha_uk
unique (codigo_linha, codigo_fase, codigo_grade)
using index tablespace sft_indices
/
```

### Índice Avulso

```sql
-- OWNER: PCP
create index pcp.rep_vinc_idx
on pcp.pedido (codigo_pessoa_repres_vinculado)
tablespace sft_indices
/
```

---

## Procedures de Grant por Owner

| Owner | Procedure |
|---|---|
| `LOG` | `sft_grant_log` |
| `ESTOQUE` | `sft_grant_estoque` |
| `GENERICO` | `sft_grant_generico` |
| `PATRIMONIO` | `sft_grant_patrimonio` |
| `PCP` | `sft_grant_pcp` |
| `NFE` | `sft_grant_nfe` |
| `CORPORATIVO` | `tc_prc_grant_corporativo` |
| `LOG_VAREJO` | `tc_prc_grant_log_varejo` |
| `PDV` | `tc_prc_grant_pdv` |
| `SFTC_CREDIARIO` | `tc_prc_grant_sftc_crediario` |
