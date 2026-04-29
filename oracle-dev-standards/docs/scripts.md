# Padrões de Scripts SQL

## Regras Absolutas

| Regra | Correto | Errado |
|---|---|---|
| Separador de comandos | `/` | `;` |
| Alteração de tabela | Script `.tab` separado | `EXECUTE IMMEDIATE` |
| Comentários em `.tab/.fk/.seq` | `-- comentário` | `/* comentário */` |
| Grant entre owners | `sft_grant_corporativo(...)` | `execute immediate 'grant...'` |
| Rename de tabela | `owner.prc_executa_comando(...)` | `conn owner` + `rename` |
| Alteração em tabela replicada | Dentro de `if tc_pck_util.fnc_matriz` | `insert/update` direto |

---

## Script .SQL — Regras

- Não deve conter `ALTER TABLE`, `CREATE TABLE`, FKs ou índices — esses ficam em `.tab` / `.fk`.
- Se não houver indicação de cliente específico no início, será rodado em todos os clientes.
- Parâmetros devem ter valor `default`. Validar por cliente com `sft_fnc_parametro('codigo_cliente_sistema_acessos')`.

---

## Script com Replicação (correto)

```sql
-- OWNER: SFT
begin
  --
  if tc_pck_util.fnc_matriz then
    --
    insert into sft_tabela_coluna_layout_banco
      (descricao_tabela_coluna, nome_tabela, nome_coluna,
       nome_funcao, tipo, alias, remessa_retorno, tipo_conta, gera_movto_financeiro)
    values
      ('VALOR_CREDITADO_CONTA_VENDEDOR', '', '', '', 'NUMBER', '',
       'RETORNO', 'CR', 'S');
    --
    commit;
    --
  end if;
  --
end;
/
```

---

## Grant Entre Owners (correto)

```sql
-- Grant de execute com grant option
begin
  sft_grant_corporativo(
    'EXECUTE',
    'CORPORATIVO.TC_PCK_INVENTARIO',
    'SFT WITH GRANT OPTION',
    'G');
end;
/

-- Grant de insert/update
begin
  sft_grant_corporativo('INSERT,UPDATE','CORPORATIVO.TABELA','SFT','G');
end;
/
```

---

## Rename de Tabela (correto)

```sql
-- OWNER: LOG
declare
  verro varchar2(500);
begin
  log.prc_executa_comando(
    'rename lsft_tabela_antiga to lsft_tabela_nova', verro);
end;
/
```

---

## Separação TAB + SQL (exemplo real)

### Errado — tudo em um bloco com EXECUTE IMMEDIATE

```sql
-- NUNCA FAZER ASSIM
begin
  execute immediate 'begin
    update corporativo.tc_clientes a
    set orgao_expedidor_rg = decode(...)
    where length(orgao_expedidor_rg) > 4;
  end;';
  execute immediate 'alter table corporativo.tc_clientes
    modify orgao_expedidor_rg varchar2(4)';
end;
/
```

### Correto — dois scripts separados

**tc_clientes.tab**

```sql
-- OWNER: CORPORATIVO
alter table corporativo.tc_clientes modify orgao_expedidor_rg varchar2(4)
/

-- OWNER: REPLICACAO
begin
  replicacao.prc_recriar_trigger(pnome_tabela => 'TC_CLIENTES');
end;
/
```

**tc_clientes.sql**

```sql
-- OWNER: SFT
begin
  --
  if sft_fnc_parametro('codigo_cliente_sistema_acessos') <> 1492 then
    --
    tc_pck_util.v_desativar_triggers := true;
    pck_var_repl.v_aplicando_replicacao := true;
    --
    update corporativo.tc_clientes a
       set orgao_expedidor_rg =
           decode(instr(a.orgao_expedidor_rg,'SSP'),0,
                  rtrim(ltrim(substr(a.orgao_expedidor_rg,1,4))),'SSP')
     where length(orgao_expedidor_rg) > 4;
    --
    tc_pck_util.v_desativar_triggers := false;
    pck_var_repl.v_aplicando_replicacao := false;
    --
  end if;
  --
end;
/
```
