# Skills de desenvolvimento

Skills instaladas no escopo do projeto para Codex, verificadas em 2026-11-06. Os arquivos em `.agents/skills/` acompanham o repositório; a instalação dos executáveis e serviços no servidor Linux é uma etapa separada.

## Seleção

- `using-n8n-skills-official`: orientação de uso das skills n8n.
- `n8n-workflow-lifecycle-official`: construção, validação e testes.
- `n8n-node-configuration-official`: parâmetros e configuração dos nodes.
- `n8n-error-handling-official`: falhas em fluxos executados sem supervisão.
- `n8n-credentials-and-security-official`: uso de credenciais.
- `playwright-cli`: inspeção de formulários, automação e testes de navegador.

As cinco primeiras são do repositório oficial `n8n-io/skills`; a última é de `microsoft/playwright-cli`. A seleção seguiu a `find-skills`: consulta ao catálogo, pesquisa pelo CLI, verificação de origem e popularidade e inspeção do conteúdo. Na consulta, as skills n8n selecionadas tinham cerca de 2,1–2,6 mil instalações; a Microsoft, 208,8 mil. Os repositórios tinham 577 e 15.006 estrelas, respectivamente. Esses números mudam e não garantem qualidade.

Fontes:

- https://skills.sh/n8n-io/skills
- https://skills.sh/microsoft/playwright-cli/playwright-cli

As skills são redistribuídas sem alterações. As licenças Apache-2.0 dos projetos de origem estão em `third_party/licenses/`.

## Repetir no servidor Linux

Com Node.js/npm e Git instalados, execute na pasta do projeto:

```bash
npx --yes skills add n8n-io/skills --skill using-n8n-skills-official n8n-workflow-lifecycle-official n8n-node-configuration-official n8n-error-handling-official n8n-credentials-and-security-official --agent codex --yes
npx --yes skills add microsoft/playwright-cli --skill playwright-cli --agent codex --yes
npx --yes skills list --agent codex
```

Os comandos baixam as versões disponíveis no momento. `skills-lock.json` registra origens e hashes locais. Abra uma nova sessão do Codex nessa pasta para descobrir as skills.

## Limpeza e dependências

A `find-skills` foi instalada, lida e utilizada; depois foi removida do projeto e do lock por não ser necessária após esta seleção:

```bash
npx --yes skills remove find-skills --agent codex --yes
```

É reversível por reinstalação:

```bash
npx --yes skills add vercel-labs/skills --skill find-skills --agent codex --yes
```

As demais skills permanecem úteis para as próximas etapas. Remover por nome quando deixarem de servir ao projeto, sem remover skills globais ou de outros projetos.

Skills são instruções. Esta instalação não instala n8n, não configura MCP, nem instala o executável Playwright e navegadores. As skills n8n selecionadas usam ferramentas do MCP oficial em vários procedimentos; conferir a disponibilidade quando houver uma instância n8n. O objetivo do projeto permanece o envio automático de candidaturas elegíveis sem aprovação individual.
