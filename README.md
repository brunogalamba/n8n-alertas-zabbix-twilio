# 📡 n8n — Alertas Zabbix via Twilio (Voz + WhatsApp)

Workflow para **notificação automática de incidentes de infraestrutura**, disparado pelo Zabbix e entregue simultaneamente como **ligação telefônica** e **mensagem de WhatsApp** usando o Twilio.

---



## ⚙️ Como funciona

```
[Zabbix] → POST webhook → [n8n] → Twilio Voz (ligação)
                                 → Twilio WhatsApp (mensagem)
```

1. O **Zabbix** detecta uma falha e envia um POST para o webhook do n8n
2. O n8n extrai a mensagem do payload
3. Em paralelo:
   - Realiza uma **ligação telefônica** com voz sintetizada em português (AWS Polly — Vitoria)
   - Envia uma **mensagem de WhatsApp** via template aprovado no Twilio

---

## 🧰 Pré-requisitos

### Zabbix
- Criar uma **Media Type** do tipo webhook com POST para a URL do fluxo
- Payload obrigatório:
```json
{ "message": "texto do alerta" }
```
- Associar a action/usuário desejado

### Twilio
- Conta ativa em [console.twilio.com](https://console.twilio.com)
- **Account SID** e **Auth Token** disponíveis no dashboard
- Número de origem com **Voice habilitado**
- WhatsApp Sandbox ativado em `Messaging > Try it out > WhatsApp`
- Template criado em `Content > Content Template Builder` com 1 variável `{{1}}`

### n8n
- Versão 1.x ou superior
- Node **Twilio** disponível (nativo)

---

## 🚀 Como usar

### 1. Importe o workflow
No n8n, vá em **Workflows > Import** e selecione o arquivo `n8n-alertas-zabbix-twilio.json`.

### 2. Configure as credenciais

Crie duas credenciais no n8n:

| Nome sugerido | Tipo | Campos |
|---|---|---|
| `Twilio account` | Twilio API | Account SID + Auth Token |
| `Twilio` | HTTP Basic Auth | user = Account SID / senha = Auth Token |

### 3. Substitua os placeholders

Abra o workflow importado e substitua:

| Placeholder | Onde | Valor |
|---|---|---|
| `{TWILIO_ACCOUNT_SID}` | Node WHATSAPP → URL | Seu Account SID (`ACxxxx...`) |
| `{CONTENT_SID}` | Node WHATSAPP → ContentSid | SID do template (`HXxxxx...`) |
| `{nr_origem}` | Nodes Twilio e WHATSAPP | Número Twilio sem `+55` |
| `{nr_destino}` | Nodes Twilio e WHATSAPP | Número de destino sem `+55` |
| `{ERROR_WORKFLOW_ID}` | Settings | ID do workflow de tratamento de erros (opcional) |

### 4. Teste o webhook

```bash
curl -X POST https://<seu-host>/webhook/{WEBHOOK_PATH} \
  -H "Content-Type: application/json" \
  -d '{"message": "Servidor não responde — teste de alerta."}'
```

### 5. Ative o workflow
Após validar o teste, ative o workflow pelo toggle no canto superior direito do n8n.

---

## 📁 Estrutura do repositório

```
n8n-alertas-zabbix-twilio/
├── README.md
├── n8n-alertas-zabbix-twilio.json
└── assets/
    └── screenshot.png
```

---

## 🔐 Segurança

O arquivo `n8n-alertas-zabbix-twilio.json` foi sanitizado — nenhum dado sensível está exposto.  
Todos os valores confidenciais foram substituídos por placeholders no formato `{PLACEHOLDER}`.

---

## 📄 Licença

MIT — sinta-se livre para usar, modificar e distribuir.

---

## 🤝 Contribuindo

Pull requests são bem-vindos! Abra uma issue para sugestões ou problemas encontrados.

---

_Feito com [n8n](https://n8n.io) + [Twilio](https://twilio.com) + [Zabbix](https://zabbix.com)_
