# central-copilotos

Marketplace de plugins do Claude Code dos copilotos da Flash Engenharia. Hoje traz um plugin, o **flash-central**: a Central de Demandas da Flash como servidor MCP (cards, chat entre as duplas, contatos, atividade e decisões).

Este repositório é público de propósito. **Não há segredo nenhum aqui**: só a URL do servidor. As chaves são pedidas na instalação e ficam no cofre seguro do sistema de cada pessoa; a Central exige as duas em toda chamada. Regras de processo e passo a passo ficam no servidor, atrás da autenticação.

## Instalar (Claude Code)

Tenha em mãos as duas chaves que o Jonathan entrega:
- **Chave do ator da sua dupla** (começa com `fla_`): diz à Central quem você é.
- **Chave dos copilotos** (arquivo `mcp.env`, `MCP_API_KEY`): só abre o MCP; é a mesma para todas as duplas.

1. **No app desktop (aba Code):** Personalização → Plugins → **+ Adicionar** → **Adicionar marketplace** → URL `FlashEngenhariaOrg/central-copilotos`. No Diretório que abre: Plugins → **Code** → **flash** → **Flash central** → **+**.
2. **O plugin pede as duas chaves** num formulário (entrada mascarada). Cole e confirme.
3. Abra uma sessão nova e peça: "use o meu_dia da Central".

Trocar as chaves depois: `/plugin configure flash-central@flash` (ou `/plugin` > Installed > Configure).

No terminal, a instalação não pergunta; passe as chaves no comando:
```
claude plugin marketplace add FlashEngenhariaOrg/central-copilotos
claude plugin install flash-central@flash
claude plugin configure flash-central@flash
```

Atualizar depois: `claude plugin marketplace update flash` e `claude plugin update flash-central@flash` (ou ligue a atualização automática em `/plugin` > Marketplaces).

## O que o plugin traz

- **Servidor MCP `flash-central`** (`.mcp.json`): `https://api.flashengenharia.com.br/flash-central-mcp/1.0.0/mcp`.
  Ferramentas de cards (listar, ver, árvore, criar, editar, mover, comentar, entregar, assumir, transferir, dependências, fechar com decisões), chat (caixa, enviar, responder, baixa), contatos, equipe do épico, atividade, busca de decisões e indicadores do projeto. Prompts: `comecar_sessao`, `fechar_card`, `abrir_chamado`, `entregar_card`.
- **Hook** (`hooks/hooks.json`): ao abrir a sessão e a cada mensagem sua, pergunta à Central (com `curl`, que já vem no Windows, Mac e Linux) se chegou mensagem nova no chat e coloca no contexto do copiloto. Cada mensagem é avisada uma vez só (a Central registra a visualização). Não dá baixa. Não precisa de Python nem de shell.
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
