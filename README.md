# WhatsApp Bot

![Node.js](https://img.shields.io/badge/Node.js-18+-339933?logo=node.js&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue)

Bot de WhatsApp com arquitetura modular para atendimento automatizado, construído sobre `whatsapp-web.js`.

## Recursos

- **Arquitetura modular:** cliente, serviços, roteador e handlers separados.
- **Reconexão automática** com backoff exponencial.
- **Configuração por ambiente** via `.env`.
- **Roteamento de comandos** extensível, inclusive em tempo de execução.
- **Simulação de digitação** para um fluxo de conversa natural.
- **Agendamento de mensagens** com atraso configurável.
- **Encerramento gracioso** em sinais do processo.

## Arquitetura

```
Whatsapp-Bot/
├── config/
│   ├── constants.js        # Configuração e variáveis de ambiente
│   └── messages.js         # Templates de mensagem
├── modules/
│   ├── whatsapp-client.js  # Gerenciamento da conexão
│   ├── message-service.js  # Envio de mensagens
│   └── api-service.js      # Integração com API externa
├── handlers/
│   ├── message-router.js   # Roteamento de mensagens
│   └── command-handlers.js # Implementação dos comandos
├── utils/helpers.js
├── .env.example
└── index.js                # Ponto de entrada
```

## Instalação

Requisitos: Node.js 18+ ou Bun.

```bash
git clone https://github.com/emersonbernardini/Whatsapp-Bot.git
cd Whatsapp-Bot
bun install        # ou: npm install
cp .env.example .env
bun start          # ou: npm start
```

Escaneie o QR Code exibido no terminal com o WhatsApp para autenticar.

## Configuração

Copie `.env.example` para `.env` e ajuste:

```bash
# WhatsApp
CLIENT_NAME=your-bot-name
AUTH_DATA_PATH=./.wwebjs_auth

# API externa
API_BASE_URL=https://api.example.com
API_ENDPOINT_TRIAL=/path/to/trial
API_USER_AGENT=Mozilla/5.0

# Tempos (ms)
TYPING_START_DELAY=3000
TYPING_DURATION=2000
TRIAL_EXPIRY_TIME=14400000

# Puppeteer
PUPPETEER_HEADLESS=true
PUPPETEER_TIMEOUT=60000
PUPPETEER_PROTOCOL_TIMEOUT=180000
```

## Uso

**Palavras de ativação:** `oi`, `ola`, `bom dia`, `boa tarde`, `boa noite`

**Comandos:** `!menu` (menu principal), `1-6` (opções do menu), `!trial` (gera um período de teste via API externa), `!player` (ajuda de configuração do player) e `subscribe` (processo de assinatura).

### Adicionando comandos

Em `handlers/command-handlers.js`:

```js
export const commandHandlers = {
  'meu-comando': async ({ chat, msg, client, name }) => {
    await messageService.sendWithTyping(chat, msg, client, 'Resposta aqui');
  },
};
```

Ou em tempo de execução:

```js
const router = new MessageRouter(client);
router.addHandler('custom', async ({ chat, msg, client }) => {
  await messageService.sendWithTyping(chat, msg, client, 'Resposta');
});
router.listen();
```

## Produção

Use um gerenciador de processos, por exemplo PM2:

```bash
pm2 start index.js --name whatsapp-bot
pm2 save
pm2 startup
```

Mantenha `PUPPETEER_HEADLESS=true`, restrinja as permissões do `.env` e monitore os logs.

## Solução de problemas

- **QR Code não aparece:** revise a configuração do Puppeteer, confirme memória suficiente ou rode com `PUPPETEER_HEADLESS=false`.
- **Conexão cai:** verifique a rede e ajuste os timeouts no `.env`.
- **Comando não responde:** confira o registro do handler e os logs.

## Aviso

Este projeto usa a API não oficial do WhatsApp Web. Use com responsabilidade e respeite os Termos de Serviço do WhatsApp.

## Licença

MIT. Veja [LICENSE](LICENSE).

## Créditos

[whatsapp-web.js](https://github.com/pedroslopez/whatsapp-web.js), [qrcode-terminal](https://github.com/gtanner/qrcode-terminal) e [chalk](https://github.com/chalk/chalk).
