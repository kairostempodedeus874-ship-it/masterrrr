# Servidor de WhatsApp — Kairós (v4)

Este pacote é o "motor" que mantém o WhatsApp da empresa conectado e conversando
com o painel. Ele já vem configurado: **você não precisa criar nenhuma chave**.

---

## Passo a passo (Railway)

### 1. Enviar o código para o GitHub
1. Crie um repositório novo no GitHub (pode ser privado), ex.: `kairos-whatsapp`.
2. Descompacte este `.zip` e envie **todos os arquivos** para o repositório
   (pelo site do GitHub: botão *Add file → Upload files*).
3. Se existir um arquivo `nixpacks.toml`, apague — usamos Dockerfile.

### 2. Criar o serviço na Railway
1. Entre em <https://railway.app> e faça login com o GitHub.
2. Clique em **New Project → Deploy from GitHub repo**.
3. Escolha o repositório que você acabou de criar.
4. A Railway vai detectar o `Dockerfile` e começar o build (leva ~3 minutos).

### 3. Criar o VOLUME (passo mais importante!)
A sessão do WhatsApp é um arquivo. Sem um volume, a Railway apaga esse arquivo a
cada reinício/deploy e a conexão cai sempre.

1. Abra o serviço → aba **Volumes** → **New Volume**.
2. Em *Mount path*, digite exatamente: `/app/wpp-auth`
3. Confirme e faça um novo **Deploy**.

### 4. Pegar a URL pública
1. No serviço, vá em **Settings → Networking → Generate Domain**.
2. Copie o endereço gerado (algo como `https://kairos-whatsapp-production.up.railway.app`).
3. Cole esse endereço no painel Kairós, na aba **Conexão → URL do servidor**.
4. Clique em **Conectar WhatsApp** e escaneie o QR Code com o celular.

### 5. Conferir se está tudo certo
No navegador, abra: `SUA_URL/api/health`

Deve aparecer algo como:
```json
{ "ok": true, "version": "4.0.0", "status": "disconnected" }
```

Se aparecer **"Application not found"**, a URL está errada ou o serviço não está
publicado — gere o domínio novamente (passo 4).

---

## Como usar o QR Code

1. No celular da empresa, abra o **WhatsApp**.
2. Toque em **Dispositivos conectados → Conectar dispositivo**.
3. Aponte a câmera para o QR Code que aparece no painel.
4. O painel muda para **Conectado** em poucos segundos.

O QR expira em cerca de 40 segundos. Se expirar, clique em **Conectar WhatsApp**
novamente para gerar outro.

---

## Variáveis de ambiente (opcionais)

Já existem valores padrão no código. Só defina se quiser trocar algo:

| Variável | Para que serve |
|---|---|
| `WPP_AUTH_DIR` | Pasta da sessão (padrão `./wpp-auth`) |
| `WPP_API_KEY` | Chave usada pelo painel para falar com o servidor |
| `WPP_AGENT_SECRET` | Chave usada para chamar o agente de IA |
| `WPP_PUBLIC_URL` | URL pública, usada para manter o serviço acordado |
| `SUPABASE_URL` / `SUPABASE_ANON_KEY` | Conexão com o Backend |

---

## Problemas comuns

**"Servidor offline" no painel**
A URL está errada, o serviço está fora do ar ou ainda está fazendo deploy.
Confira em `SUA_URL/api/health`.

**Conecta e cai depois de alguns minutos**
Falta o volume do passo 3, ou a Railway hibernou o serviço. Confira o volume e
mantenha o painel aberto (o painel envia um sinal de vida a cada 2 minutos).

**QR Code não aparece**
O serviço está reiniciando. Espere o deploy terminar e clique em Conectar novamente.

**Celular ainda mostra o aparelho antigo**
No painel, use **Reconectar do zero** para apagar a sessão antiga e gerar um novo QR.