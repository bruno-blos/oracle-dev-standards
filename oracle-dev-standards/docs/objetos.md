# Manutenção de Objetos de Banco

## Regras Gerais

- Usar nome do objeto, **nunca** do sinônimo
- Sempre prefixar com owner em todos os scripts
- Criar sinônimo público para todos os objetos, **exceto triggers de banco**
- Liberar grants para `R_MENU` e owners que usam diretamente, **exceto triggers de banco**
- Sempre colocar `/` no final de Packages, Functions, Procedures e Triggers

---

## Packages (.pck)

Prefixo: `sft_pck_nome_package`

```sql
create or replace package sft.sft_pck_nome_package as
  --
  procedure prc_nome_procedure;
  --
end sft_pck_nome_package;
/

create or replace package body sft.sft_pck_nome_package as
  --
  procedure prc_nome_procedure is
  begin
    --
    null;
    --
  end prc_nome_procedure;
  --
end sft_pck_nome_package;
/

create or replace public synonym sft_pck_nome_package for sft.sft_pck_nome_package
/

grant execute on sft.sft_pck_nome_package to r_menu
/
```

---

## Functions (.fnc)

Prefixo: `sft_fnc_nome_function`

```sql
create or replace function sft.sft_fnc_nome_function(
  pparametro1 in varchar2)
return varchar2 is
  --
  vretorno varchar2(100);
  --
begin
  --
  vretorno := pparametro1;
  --
  return vretorno;
  --
end sft_fnc_nome_function;
/

create or replace public synonym sft_fnc_nome_function for sft.sft_fnc_nome_function
/

grant execute on sft.sft_fnc_nome_function to r_menu
/
```

---

## Procedures (.prc)

Prefixo: `sft_prc_nome_procedure`

```sql
create or replace procedure sft.sft_prc_nome_procedure(
  pparametro1 in  varchar2
 ,pparametro2 out varchar2) is
  --
begin
  --
  pparametro2 := pparametro1;
  --
end sft_prc_nome_procedure;
/

create or replace public synonym sft_prc_nome_procedure for sft.sft_prc_nome_procedure
/

grant execute on sft.sft_prc_nome_procedure to r_menu
/
```

---

## Sequences (.seq)

Prefixo: `sft_seq_nome_sequence`

```sql
-- OWNER: SFT
create sequence sft.sft_seq_nome_sequence
  minvalue 0
  maxvalue 9999999999999999999999
  start with 1
  increment by 1
  nocache
  nocycle
/

create or replace public synonym sft_seq_nome_sequence for sft.sft_seq_nome_sequence
/

grant select on sft.sft_seq_nome_sequence to r_menu
/
```

---

## Views (.vw)

Prefixo: `sft_vw_nome_view`

```sql
create or replace force view sft.sft_vw_nome_da_view as
  select gp.numero_pedido
        ,gp.sequencia_grade
    from sft_grade_pedido gp
/

create or replace public synonym sft_vw_nome_da_view for sft.sft_vw_nome_da_view
/

grant select, insert, update, delete on sft.sft_vw_nome_da_view to r_menu
/
```

---

## Triggers (.trg)

### Nomenclatura

| Prefixo | Tipo |
|---|---|
| `trgb_s_sft_nome` | Before — statement |
| `trga_s_sft_nome` | After — statement |
| `trgb_sft_nome` | Before — for each row |
| `trga_sft_nome` | After — for each row |
| `trgl_sft_nome_log` | Log — before for each row |

### Trigger Before (for each row)

```sql
create or replace trigger sft.trgb_sft_nome_da_trigger
  before insert or update on sft.nome_da_tabela
  for each row
begin
  --
  if pck_util.v_desativa_triggers then
    return;
  end if;
  --
  null;
  --
end trgb_sft_nome_da_trigger;
/
```

### Trigger After (for each row)

```sql
create or replace trigger sft.trga_sft_nome_da_trigger
  after insert or update on sft.nome_da_tabela
  for each row
begin
  --
  if pck_util.v_desativa_triggers then
    return;
  end if;
  --
  null;
  --
end trga_sft_nome_da_trigger;
/
```

### Trigger Statement (after)

```sql
create or replace trigger sft.trgs_nome_da_tabela
  after insert or update or delete on sft.nome_da_tabela
begin
  --
  if tc_pck_util.v_desativar_triggers = true then
    return;
  end if;
  --
  null;
  --
end trgs_nome_da_tabela;
/
```

### Trigger de Log (before for each row)

```sql
create or replace trigger sft.trgl_sft_nome_da_trigger
  before insert or update or delete
  on sft.sft_nome_da_tabela
  for each row
declare
  --
  v_tipo_movimento varchar2(1);
  vusuario_rede    lsft_nome_tabela.usuario_rede_log%type;
  vmaquina         lsft_nome_tabela.maquina_usuario_log%type;
  vprograma        lsft_nome_tabela.programa_log%type;
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
    insert into lsft_nome_tabela values
      (v_tipo_movimento
      ,nvl(sys_context('APEX$SESSION','APP_USER'),user)
      ,sysdate
      ,vusuario_rede
      ,vmaquina
      ,vprograma
      ,:new.nome_da_coluna);
    --
  elsif deleting then
    --
    insert into lsft_nome_tabela values
      ('E'
      ,nvl(sys_context('APEX$SESSION','APP_USER'),user)
      ,sysdate
      ,vusuario_rede
      ,vmaquina
      ,vprograma
      ,:old.nome_da_coluna);
    --
  end if;
  --
end trgl_sft_nome_da_trigger;
/
```

> Não criar sinônimo público nem liberar grant para triggers de banco.
