# Workflows n8n

## `job-hunter-pipeline.json`

Fluxo inicial do JobHunter AI para executar manualmente ou às 08:30 (horário de São Paulo) em dias úteis. Ele vem desativado e usa vagas fictícias por padrão, portanto a primeira execução não acessa portais nem envia candidaturas.

### Importar

1. No n8n, abra **Workflows** e escolha **Import from File**.
2. Selecione `workflows/job-hunter-pipeline.json`.
3. Abra o nó **Iniciar manualmente** e clique em **Execute workflow**.
4. Confira a saída de **Preparar fila de revisão**: devem aparecer três vagas fictícias, todas em revisão porque o envio automático começa desligado.

O workflow permanece inativo ao importar. Ative-o somente após concluir o teste manual e configurar o worker local.

### Teste de aceitação antes de ativar

- **Entrada válida:** execute em modo `test`. A vaga Backend fica em revisão porque o envio está desligado; a de Suporte fica abaixo da nota mínima; a terceira exige revisão por CAPTCHA.
- **Evento duplicado:** no nó **Gerar vagas de teste**, duplique a primeira vaga sem alterar `source` nem `externalId`. Depois da execução, somente uma delas pode passar por **Remover duplicadas do lote**. Essa proteção vale apenas nesta execução; a proteção persistente deve ficar no SQLite do worker usando `idempotencyKey` como `UNIQUE`.
- **Entrada inválida:** temporariamente remova `applyUrl` de uma vaga no nó **Gerar vagas de teste**. O nó **Normalizar vagas** deve interromper a execução com a mensagem de campos obrigatórios. Desfaça essa alteração após o teste.

Esses testes ainda não foram executados em uma instância n8n, pois esta máquina não está conectada ao seu Docker/n8n. O JSON foi validado estruturalmente e o workflow está inativo.

### Configuração do contêiner n8n

Defina estas variáveis no serviço `n8n` do Docker Compose. Elas não são credenciais e podem ficar em um arquivo `.env` local que não seja versionado.

```dotenv
JOBHUNTER_MODE=test
JOBHUNTER_AUTO_APPLY=false
JOBHUNTER_MIN_SCORE=70
JOBHUNTER_TARGET_ROLES=desenvolvedor,software engineer
JOBHUNTER_REQUIRED_KEYWORDS=javascript,typescript
JOBHUNTER_PREFERRED_LOCATIONS=remoto,remote
JOBHUNTER_RESUME_REF=PREENCHER_REFERENCIA_DO_CURRICULO
JOBHUNTER_WORKER_URL=http://jobhunter-worker:3000
```

Para permitir que o nó Code leia essas configurações no n8n self-hosted, mantenha `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` no contêiner n8n.

### Contrato do worker local

Quando `JOBHUNTER_MODE=production`, o fluxo chama `POST /v1/jobs/search` no endereço de `JOBHUNTER_WORKER_URL` e espera:

```json
{
  "jobs": [
    {
      "source": "empresa-exemplo",
      "externalId": "id-unico-da-vaga",
      "title": "Desenvolvedor TypeScript",
      "company": "Empresa Exemplo",
      "location": "Remoto - Brasil",
      "description": "...",
      "applyUrl": "https://carreiras.exemplo/vaga/123",
      "requiresCaptcha": false,
      "requiresMfa": false,
      "hasUnknownQuestions": false
    }
  ]
}
```

Com `JOBHUNTER_AUTO_APPLY=true`, somente vagas acima da nota mínima, sem CAPTCHA, MFA ou perguntas desconhecidas chegam ao `POST /v1/applications/submit`. O worker deve usar `idempotencyKey` como chave única em SQLite para não repetir candidaturas entre execuções e deve devolver um `status` claro, como `submitted`, `manual_review`, `captcha`, `mfa` ou `blocked`.

Não coloque senhas, tokens ou chaves de APIs no JSON do workflow. Caso uma fonte autorizada exija autenticação, crie a credencial correspondente na interface do n8n ou no worker local.
