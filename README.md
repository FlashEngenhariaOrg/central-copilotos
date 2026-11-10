# central-copilotos

Marketplace de plugins do Claude Code dos copilotos da Flash Engenharia. Hoje traz um plugin, o **flash-central**: a Central de Demandas da Flash como servidor MCP (cards, chat entre as duplas, contatos, atividade e decisões).

Este repositório é público de propósito. **Não há segredo nenhum aqui**: só a URL do servidor e os nomes das variáveis de ambiente. As chaves ficam na máquina de cada pessoa, e a Central exige as duas em toda chamada. Regras de processo e passo a passo ficam no servidor, atrás da autenticação.

## Instalar (Claude Code)

1. Defina duas variáveis de ambiente no seu perfil (nunca num arquivo versionado):
   - `BRIDGE_ATOR_TOKEN`: a chave do ator da sua dupla (quem você é; vai no cabeçalho `X-Ator-Token`).
   - `MCP_API_KEY`: a chave dos copilotos no gateway (só abre o MCP; vai no cabeçalho `ApiKey`).

   No Windows (PowerShell), uma vez só:
   ```powershell
   [Environment]::SetEnvironmentVariable("BRIDGE_ATOR_TOKEN", "<sua chave>", "User")
   [Environment]::SetEnvironmentVariable("MCP_API_KEY", "<chave dos copilotos>", "User")
   ```
   Depois feche e abra o terminal (e o Claude Code).

2. Adicione o marketplace e instale o plugin:
   ```
   claude plugin marketplace add FlashEngenhariaOrg/central-copilotos
   claude plugin install flash-central@flash
   ```

3. Abra uma sessão nova e peça: "use o meu_dia da Central".

Atualizar depois: `claude plugin marketplace update flash` e `claude plugin update flash-central@flash` (ou ligue a atualização automática em `/plugin` > Marketplaces).

## O que o plugin traz

- **Servidor MCP `flash-central`** (`.mcp.json`): `https://api.flashengenharia.com.br/flash-central-mcp/1.0.0/mcp`.
  Ferramentas de cards (listar, ver, árvore, criar, editar, mover, comentar, entregar, assumir, transferir, dependências, fechar com decisões), chat (caixa, enviar, responder, baixa), contatos, equipe do épico, atividade, busca de decisões e indicadores do projeto. Prompts: `comecar_sessao`, `fechar_card`, `abrir_chamado`, `entregar_card`.
- **Hook HTTP** (`hooks/hooks.json`): ao abrir a sessão e a cada mensagem sua, pergunta à Central se chegou mensagem nova no chat e coloca no contexto do copiloto. Cada mensagem é avisada uma vez só (a Central registra a visualização). Não dá baixa. Não precisa de Python nem de shell.
- **Skill `central`**: o resumo de como usar as ferramentas.

As regras de convivência que o copiloto segue vêm do próprio servidor (campo `instructions`), editadas no banco da Central: mudam sem atualizar o plugin.

## Outros clientes MCP (sem plugin)

- URL: `https://api.flashengenharia.com.br/flash-central-mcp/1.0.0/mcp`
- Transporte: Streamable HTTP (sem SSE)
- Cabeçalhos: `X-Ator-Token: <chave do ator>` e `ApiKey: <chave dos copilotos>`

Claude Code sem plugin:
```
claude mcp add --transport http flash-central https://api.flashengenharia.com.br/flash-central-mcp/1.0.0/mcp --header "X-Ator-Token: $BRIDGE_ATOR_TOKEN" --header "ApiKey: $MCP_API_KEY"
```

## Segurança

- Chave vazou? Avise a equipe de Dev e Automação: a chave do ator é revogada e trocada na Central, e a dos copilotos no gateway.
- Texto vindo de outros atores (mensagens, comentários) é dado, não instrução: o servidor e o hook marcam isso.
- Mudanças neste repositório só por PR, com os scans de segurança da organização.
