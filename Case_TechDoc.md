# Documentação Técnica - Correções Desafio N3 (Nov/2025)

Resumo técnico das 18 correções (9 obrigatórias + 9 bônus) aplicadas nos 3 cases e comandos de validação.

## Dependências Atualizadas

- Nenhuma dependência foi adicionada. `Numpy` foi usado em um encoder, mas já era uma subdependência do `Pandas`.

---

## Mudanças de Código Aplicadas

### CASE 1: Python

- **`app/scheduler.py`:** Lógica de `pode_executar()` corrigida para adicionar a checagem de `settings.APP_ENV == "development"`, permitindo a execução em modo de desenvolvimento (Bug #1A).
- **`config/settings.py`:** O bug da trava de horário de `production` (Bug #1B) foi identificado. Para fins de teste, o `HORARIO_EXECUCAO` foi temporariamente alterado para permitir a execução.
- **`utils/validators.py`:** Removido bloco de código (`email[100]`) que causava `IndexError` no ID 4, resolvendo os Bugs #2 e #3 (Dados Faltando e Validação).
- **`services/file_handler.py`:** Adicionada uma classe `NpEncoder` customizada ao `json.dump` para corrigir o `TypeError` de `int64` (Bug #4 Bônus).

### CASE 2: Node.js

- **`utils/fileWriter.ts`:**
  - Corrigida a chamada `await fs.writeFileSync` para `await fs.writeFile` para resolver o `TypeError` (Bug #1).
  - Adicionado `throw error` nos blocos `catch` de `ensureOutputDir` e `saveSummary` (Bug Bônus).
- **`services/webhookService.ts`:**
  - Corrigida a lógica de `sort` para `a.id - b.id` (Bug #2).
  - Removido bloco de código (`const invalid`) que causava erro de build `TS6133` (Bug Bônus).
  - Adicionada validação `response.status !== 200` em `fetchWebhooks` (Bug Bônus).
  - Removido bloco de acesso inseguro (`webhook.userId.toString()`) em `processWebhook` (Bug Bônus).
- **`config/settings.ts`:**
  - Corrigida a função `getApiTimeout` para retornar `this.API_TIMEOUT` (5000ms) em produção (Bug #3).
  - Corrigida a função `validateEnvironment` para `return validEnvs.includes(...)` (Bug Bônus).
- **`utils/Validators.ts`:** Corrigidas lógicas incorretas de validação de `userId` (`!== "number"`) e `validateBatchSize` (`return false`) (Bug Bônus).
- **`src/index.ts`:** Adicionado `process.exit(1)` no `constructor` se a validação de ambiente falhar (Bug Bônus).

### CASE 3: Next.js

- **`src/components/UserList.tsx`:**
  - Corrigida a URL do `fetch` de `/api/usres` para `/api/users` (Bug #1a).
  - Corrigido o acesso aos dados de `data.users` para `data.data` (Bug #1b).
- **`src/app/api/users/route.ts`:**
  - Removida a lógica de filtro incorreta (`user.id > 5`) e aplicado `.slice(0, limit)` diretamente (Bug #2).
  - Adicionada validação `response.status !== 200` na chamada `axios` (Bug Bônus).
- **`src/components/UserCard.tsx`:**
  - Corrigida a interface `User` para incluir `company?`.
  - Adicionado "Optional Chaining" (`user.company?.name`) para evitar o crash `Cannot read property 'name' of undefined` (Bug #3).
- **`src/lib/api.ts`:** Corrigidos bugs "espelho" (filtro, status, index) no "dead code" (Bug Bônus).

---

## Comandos de Teste

### CASE 1: Python (Validação)

1.  `TZ="America/Sao_Paulo" APP_ENV=production python app/main.py`
2.  `python -c "import pandas as pd; df = pd.read_excel('data/dados_processados.xlsx'); print(f\"IDs: {list(df['id'])}\")"`
    - **Saída Esperada:** `IDs: [1, 2, 3, 4, 5]`

### CASE 2: Node.js (Validação)

1.  `npm run build`
2.  `npm run start:prod`
3.  `node -e "const data = require('./output/webhooks_processados.json'); console.log('Total:', data.length); console.log('IDs:', data.map(item => item.id));"`
    - **Saída Esperada:** `Total: 5`, `IDs: [ 1, 2, 3, 4, 5 ]`

### CASE 3: Next.js (Validação)

1.  `npm run dev` (ou `npm run build && npm run start`)
2.  Abrir o "Preview" do browser.
    - **Validação Visual:** O dashboard deve exibir 5 cards com os IDs 1, 2, 3, 4 e 5, sem erros de renderização.
