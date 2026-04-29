# Criação de Parâmetros

> Sempre incluir valor `default`. Validar por cliente com `sft_fnc_parametro('codigo_cliente_sistema_acessos')`.

---

## Parâmetro Geral (sft_parametro)

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
  if sft_fnc_parametro('codigo_cliente_sistema_acessos') in (1) then
    --
    insert into sft.sft_parametro
      (parametro, valor, tipo, permite_alteracao, observacao, valida_dominio)
    values
      ('NOME_PARAMETRO', 'S', 'C', 'S', 'DESCRICAO DO PARAMETRO ...', 'S');
    --
  else
    --
    insert into sft.sft_parametro
      (parametro, valor, tipo, permite_alteracao, observacao, valida_dominio)
    values
      ('NOME_PARAMETRO', 'N', 'C', 'S', 'DESCRICAO DO PARAMETRO ...', 'S');
    --
  end if;
  --
end;
/
```

**Chamada:**
```sql
select sft_fnc_parametro('NOME_PARAMETRO') from dual;
```

---

## Parâmetro por Empresa (sft_parametro_empresa)

```sql
-- OWNER: SFT
begin
  --
  for reg in (select e.codigo_empresa
                from sft_empresa e
               order by 1)
  loop
    --
    insert into sft_parametro_empresa
      (codigo_empresa, campo, permite_alteracao, tipo_dados,
       valor_caracter, valor_data, valor_numerico, observacao,
       valor_padrao_caracter, valor_padrao_data, valor_padrao_numerico,
       valida_dominio)
    values
      (reg.codigo_empresa, 'NOME_PARAMETRO', 'S', 'N',
       null, null, null, 'DESCRICAO DO PARAMETRO ...',
       null, null, null, 'N');
    --
    insert into sft.sft_dominio_parametro_empresa
      (codigo_empresa, campo, valor_caracter)
    values
      (reg.codigo_empresa, 'NOME_PARAMETRO', 'S');
    --
    insert into sft.sft_dominio_parametro_empresa
      (codigo_empresa, campo, valor_caracter)
    values
      (reg.codigo_empresa, 'NOME_PARAMETRO', 'N');
    --
  end loop;
  --
end;
/
```

**Chamadas:**
```sql
select sft_pck_parametro_empresa.fnc_parametro_caracter(
         pcampo => 'NOME_PARAMETRO', pcodigo_empresa => CODIGO_EMPRESA)
  from dual;

select sft_pck_parametro_empresa.fnc_parametro_numerico(
         pcampo => 'NOME_PARAMETRO', pcodigo_empresa => CODIGO_EMPRESA)
  from dual;

select sft_pck_parametro_empresa.fnc_parametro_data(
         pcampo => 'NOME_PARAMETRO', pcodigo_empresa => CODIGO_EMPRESA)
  from dual;
```

---

## Parâmetro por Programa (sft_parametro_programa)

```sql
-- OWNER: SFT
begin
  --
  insert into sft.sft_dominio_parametro_programa
    (nome_programa, parametro, valor_caracter)
  values
    ('NOME_DO_PROGRAMA', 'NOME_PARAMETRO', 'S');
  --
  insert into sft.sft_dominio_parametro_programa
    (nome_programa, parametro, valor_caracter)
  values
    ('NOME_DO_PROGRAMA', 'NOME_PARAMETRO', 'N');
  --
  if sft_fnc_parametro('codigo_cliente_sistema_acessos') in (1) then
    --
    insert into sft.sft_parametro_programa
      (nome_programa, parametro, tipo, permite_alteracao,
       valor_caracter, observacao, valida_dominio)
    values
      ('NOME_DO_PROGRAMA', 'NOME_PARAMETRO', 'C', 'S',
       'S', 'DESCRICAO DO PARAMETRO ...', 'S');
    --
  else
    --
    insert into sft.sft_parametro_programa
      (nome_programa, parametro, tipo, permite_alteracao,
       valor_caracter, observacao, valida_dominio)
    values
      ('NOME_DO_PROGRAMA', 'NOME_PARAMETRO', 'C', 'S',
       'N', 'DESCRICAO DO PARAMETRO ...', 'S');
    --
  end if;
  --
end;
/
```

**Chamadas:**
```sql
select sft_pck_parametro_programa.fnc_parametro_caracter(
         'NOME_PROGRAMA', 'NOME_PARAMETRO')
  from dual;

select sft_pck_parametro_programa.fnc_parametro_numerico(
         'NOME_PROGRAMA', 'NOME_PARAMETRO')
  from dual;

select sft_pck_parametro_programa.fnc_parametro_data(
         'NOME_PROGRAMA', 'NOME_PARAMETRO')
  from dual;
```
