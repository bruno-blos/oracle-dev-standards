# Templates de Referência

Modelos prontos para copiar e adaptar. Substituir sempre os placeholders em MAIÚSCULO pelo valor real.

---

## template.tab — Criação de Tabela

```sql
-- OWNER: SFT
create table sft.sft_NOME_TABELA
(COLUNA1 number(05) not null
,COLUNA2 varchar2(10) not null
,COLUNA3 varchar2(01) default 'N' not null)
tablespace sft_dados
/

alter table sft.sft_NOME_TABELA
add constraint sft_NOME_TABELA_pk
primary key (COLUNA1)
using index tablespace sft_indices
/

create or replace public synonym sft_NOME_TABELA for sft.sft_NOME_TABELA
/

grant select, insert, update, delete on sft_NOME_TABELA to r_menu
/
```

---

## template.tab — Adicionar Coluna com Default

```sql
-- OWNER: SFT
begin
  pck_util.v_desativar_triggers := true;
end;
/

alter table sft.sft_NOME_TABELA add NOME_COLUNA varchar2(1)
/

alter table sft.sft_NOME_TABELA modify NOME_COLUNA default 'N'
/

begin
  pck_util.v_desativar_triggers := false;
end;
/

-- Script de log separado: lsft_NOME_TABELA.tab
-- OWNER: LOG
alter table log.lsft_NOME_TABELA add (NOME_COLUNA varchar2(1))
/
```

---

## template.fk — Foreign Key com Índice

```sql
-- OWNER: SFT
alter table sft.sft_NOME_TABELA
add constraint sft_NOME_TABELA_COLUNA_fk
foreign key (NOME_COLUNA)
references sft.sft_TABELA_REFERENCIA (COLUNA_REFERENCIA)
/

create index sft.sft_NOME_TABELA_COLUNA_idx
on sft.sft_NOME_TABELA (NOME_COLUNA)
tablespace sft_indices
/
```

---

## template.seq — Sequence

```sql
-- OWNER: SFT
create sequence sft.sft_seq_NOME_SEQUENCE
  minvalue 0
  maxvalue 9999999999999999999999
  start with 1
  increment by 1
  nocache
  nocycle
/

create or replace public synonym sft_seq_NOME_SEQUENCE for sft.sft_seq_NOME_SEQUENCE
/

grant select on sft.sft_seq_NOME_SEQUENCE to r_menu
/
```

---

## template.trg — Trigger de Log

```sql
create or replace trigger sft.trgl_sft_NOME_TRIGGER
  before insert or update or delete
  on sft.sft_NOME_TABELA
  for each row
declare
  --
  v_tipo_movimento varchar2(1);
  vusuario_rede    lsft_NOME_TABELA.usuario_rede_log%type;
  vmaquina         lsft_NOME_TABELA.maquina_usuario_log%type;
  vprograma        lsft_NOME_TABELA.programa_log%type;
  --
begin
  --
  if pck_util.v_desativa_triggers_log then
    return;
  end if;
  --
  sft_prc_inform_usuario_log(userenv('sessionid')
                             ,vusuario_rede
                             ,vmaquina
                             ,vprograma);
  --
  if inserting or updating then
    --
    if inserting then
      v_tipo_movimento := 'I';
    else
      v_tipo_movimento := 'A';
    end if;
    --
    insert into lsft_NOME_TABELA values
      (v_tipo_movimento
      ,nvl(sys_context('APEX$SESSION','APP_USER'),user)
      ,sysdate
      ,vusuario_rede
      ,vmaquina
      ,vprograma
      ,:new.NOME_COLUNA);
    --
  elsif deleting then
    --
    insert into lsft_NOME_TABELA values
      ('E'
      ,nvl(sys_context('APEX$SESSION','APP_USER'),user)
      ,sysdate
      ,vusuario_rede
      ,vmaquina
      ,vprograma
      ,:old.NOME_COLUNA);
    --
  end if;
  --
end trgl_sft_NOME_TRIGGER;
/
```

---

## template.sql — Parâmetro Geral

```sql
-- OWNER: SFT
begin
  --
  insert into sft.sft_dominio_parametro(parametro, valor_padrao)
  values ('NOME_PARAMETRO', 'S');
  --
  insert into sft.sft_dominio_parametro(parametro, valor_padrao)
  values ('NOME_PARAMETRO', 'N');
  --
  if sft_fnc_parametro('codigo_cliente_sistema_acessos') in (CODIGO_CLIENTE) then
    insert into sft.sft_parametro
      (parametro, valor, tipo, permite_alteracao, observacao, valida_dominio)
    values
      ('NOME_PARAMETRO', 'S', 'C', 'S', 'DESCRICAO DO PARAMETRO', 'S');
  else
    insert into sft.sft_parametro
      (parametro, valor, tipo, permite_alteracao, observacao, valida_dominio)
    values
      ('NOME_PARAMETRO', 'N', 'C', 'S', 'DESCRICAO DO PARAMETRO', 'S');
  end if;
  --
end;
/
```
