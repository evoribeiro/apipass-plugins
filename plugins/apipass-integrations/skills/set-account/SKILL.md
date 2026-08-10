---
name: set-account
description: Como informar a conta APIPASS (accountName) que resolve o realm do Keycloak no login. No servidor hospedado, a conta vai como argumento do apipass_login (por usuario/sessao).
disable-model-invocation: true
argument-hint: "[account-name]"
---

# Conta APIPASS (realm) no login

A APIPASS e multi-realm: **um realm do Keycloak por cliente, resolvido pelo `accountName`**. No servidor MCP hospedado (multi-usuario), cada pessoa informa a propria conta **no login** — nao ha config global.

## Como usar
1. Rode `apipass_login` com seu `account_name`:
   ```
   apipass_login account_name="sua-conta"
   ```
   O servidor resolve o realm, retorna a URL de autorizacao.
2. Abra a URL no navegador, autorize no Keycloak da sua conta.
3. Nao espere o usuario avisar que autorizou: faca poll de `apipass_auth_status` voce mesmo a cada poucos segundos ate `authenticated: true` (ou timeout de alguns minutos), depois prossiga com a acao original automaticamente. O token fica vinculado a sua sessao e renova sozinho; `apipass_logout` descarta.

## Descobrindo o `account_name` a partir de um link

Quando o usuario colar um link do ambiente da APIPASS, o `account_name` e sempre o subdominio entre `https://` e `.app.apipass.com.br` — use-o direto no `apipass_login`, sem perguntar ao usuario:

```
https://royalcanin.app.apipass.com.br/dashboard                                    -> account_name: royalcanin
https://royalcanin.app.apipass.com.br/executions?startDate=...&endDate=...          -> account_name: royalcanin
https://royalcanin.app.apipass.com.br/flow/setup/e9ea1a46-5b3a-49ed-b3e8-14ede551f11f -> account_name: royalcanin
https://carrefour.app.apipass.com.br/dashboard                                      -> account_name: carrefour
https://mondelez.app.apipass.com.br/dashboard                                       -> account_name: mondelez
```

Isso vale so para links de **dashboard/app** (`*.app.apipass.com.br/...`). E diferente do link de SSO/Keycloak (`https://sso.apipass.com.br/keycloak/realms/{realm}/...`), onde o realm ja vem explicito na propria URL.

## Observacoes
- O `account_name` so e necessario para escolher o realm no login; depois disso, o API Gateway deriva a identidade do proprio token.
- Em desenvolvimento local (servidor na sua maquina), da para definir um realm default via `APIPASS_KEYCLOAK_REALM` no `.env` do servidor — assim `apipass_login` funciona sem argumento. Em producao hospedada, sempre informe o `account_name`.
