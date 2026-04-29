# oracle-dev-standards

Repositório privado com os padrões de desenvolvimento Oracle utilizados nos projetos SFT.

## Ambiente

| Item | Versão |
|---|---|
| Oracle Forms | 6.0.8.27.0 |
| Oracle APEX | 20.1 |
| Oracle Database | 11g |
| Sistema Operacional | Windows 11 |

## Estrutura

```
oracle-dev-standards/
├── CLAUDE.md                  ← Instruções globais lidas pelo Claude Code / Claude Desktop
├── README.md                  ← Este arquivo
├── docs/
│   ├── tabelas.md             ← Manutenção de tabelas, constraints e índices
│   ├── objetos.md             ← Packages, Functions, Procedures, Triggers, Views, Sequences
│   ├── scripts.md             ← Padrões de scripts .SQL e .TAB
│   └── parametros.md          ← Criação de parâmetros gerais, por empresa e por programa
└── templates/
    └── README.md              ← Modelos de referência prontos para copiar
```

## Como Usar

### Claude Code (terminal)

Copie o `CLAUDE.md` para a pasta global do Claude Code:

```powershell
# Windows — copiar para pasta global (vale para todos os projetos)
copy CLAUDE.md %USERPROFILE%\.claude\CLAUDE.md
```

Ou clone diretamente na pasta `~/.claude`:

```powershell
cd %USERPROFILE%
git clone https://github.com/SEU_USUARIO/oracle-dev-standards.git .claude-standards
copy .claude-standards\CLAUDE.md .claude\CLAUDE.md
```

### Claude Desktop (via MCP Filesystem)

Adicione a configuração abaixo no arquivo `claude_desktop_config.json`:

**Localização do arquivo no Windows:**
```
C:\Users\SEU_USUARIO\AppData\Roaming\Claude\claude_desktop_config.json
```

**Configuração:**
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "C:\\Users\\SEU_USUARIO\\Documents\\oracle-dev-standards"
      ]
    }
  }
}
```

> Substitua `SEU_USUARIO` pelo seu usuário real do Windows e ajuste o caminho onde o repositório foi clonado.

Após configurar, o Claude Desktop terá acesso aos arquivos do repositório e lerá as instruções automaticamente.

## Atualização

Para manter os padrões atualizados em todas as máquinas:

```powershell
cd C:\Users\SEU_USUARIO\Documents\oracle-dev-standards
git pull
```
