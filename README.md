{
  "name": "ChatBot IA",
  "nodes": [
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "webhook",
        "options": {}
      },
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2.1,
      "position": [
        -688,
        -176
      ],
      "id": "7e65bed2-7a16-46d0-8908-5261f58be0be",
      "name": "Webhook",
      "webhookId": "2a27d11e-2aeb-49d8-9f36-69ca4f28b347",
      "disabled": true
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "ddc53f2d-b794-41e1-8725-75eeaf09da76",
              "name": "session",
              "value": "={{ $json.body.session }}",
              "type": "string"
            },
            {
              "id": "b63ca9ec-c632-4a5b-9028-0ae3eecaf0b5",
              "name": "chatId",
              "value": "={{ $json.body.payload.from }}",
              "type": "string"
            },
            {
              "id": "4ebcc493-cb6f-4179-8454-c1d0d9738fd2",
              "name": "pushName",
              "value": "={{ $json.body.me.pushName }}",
              "type": "string"
            },
            {
              "id": "a6fdb0f8-e908-4b98-a87a-6ea163012f2f",
              "name": "paload_Id",
              "value": "={{ $json.body.payload.id }}",
              "type": "string"
            },
            {
              "id": "73ebc010-8925-41b4-927a-1dff347ef8c2",
              "name": "event",
              "value": "={{ $json.body.event }}",
              "type": "string"
            },
            {
              "id": "f2c6cc56-aa0e-4de3-86e6-53a30b0d9455",
              "name": "message",
              "value": "={{ $json.body.payload.body }}",
              "type": "string"
            },
            {
              "id": "781c02ce-6789-499c-b845-c27cf22739a1",
              "name": "fromMe",
              "value": "={{ $json.body.payload.fromMe }}",
              "type": "boolean"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        -464,
        -176
      ],
      "id": "35bdfb21-c526-434d-bdf8-9bbadc80a3bf",
      "name": "Dados"
    },
    {
      "parameters": {
        "rules": {
          "values": [
            {
              "conditions": {
                "options": {
                  "caseSensitive": true,
                  "leftValue": "",
                  "typeValidation": "strict",
                  "version": 3
                },
                "conditions": [
                  {
                    "leftValue": "={{ $json.event }}",
                    "rightValue": "=message",
                    "operator": {
                      "type": "string",
                      "operation": "equals"
                    },
                    "id": "a5d7af31-720b-47cb-b605-efe2b6bfdac4"
                  }
                ],
                "combinator": "and"
              }
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.switch",
      "typeVersion": 3.4,
      "position": [
        -240,
        -176
      ],
      "id": "4e552867-d9f8-4f37-a097-d426a25c6ed2",
      "name": "Switch"
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "={{ $json.message }}",
        "options": {
          "systemMessage": "Você é uma IA do Grupo Starce , e seu nome é Star , se apresente , antes de iniciar a conversa.  criada para tirar duvida sobre telemetria veicular , sobre Rastreadores FMC e FMB 130 e 150"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 3.1,
      "position": [
        -16,
        -176
      ],
      "id": "ab91881d-a73a-4201-abac-bf33cf470a8a",
      "name": "AI Agent"
    },
    {
      "parameters": {
        "options": {
          "temperature": 0.4
        }
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatGoogleGemini",
      "typeVersion": 1,
      "position": [
        -16,
        48
      ],
      "id": "75ac81e1-0a80-454e-984a-4277d99542b9",
      "name": "Google Gemini Chat Model",
      "credentials": {
        "googlePalmApi": {
          "id": "XmDXxeNgTmsboyek",
          "name": "Google Gemini(PaLM) Api account 2"
        }
      }
    },
    {
      "parameters": {
        "sessionIdType": "customKey",
        "sessionKey": "={{ $('Dados').item.json.chatId }}",
        "sessionTTL": 3600,
        "contextWindowLength": 10
      },
      "type": "@n8n/n8n-nodes-langchain.memoryRedisChat",
      "typeVersion": 1.5,
      "position": [
        128,
        48
      ],
      "id": "2bdc90d2-b3e0-41cd-a975-90f5647e506b",
      "name": "Redis Chat Memory",
      "credentials": {
        "redis": {
          "id": "3d0FrjsUZ3felZ7D",
          "name": "Redis account"
        }
      }
    },
    {
      "parameters": {
        "resource": "Chatting",
        "operation": "Send Seen",
        "session": "={{ $('Switch').item.json.session }}",
        "chatId": "={{ $('Switch').item.json.chatId }}",
        "messageId": "={{ $('Switch').item.json.paload_Id }}",
        "requestOptions": {}
      },
      "type": "n8n-nodes-waha.WAHA",
      "typeVersion": 202411,
      "position": [
        336,
        -176
      ],
      "id": "d4f76292-8a1a-499a-b6d7-1b2dc3c5d952",
      "name": "Send Seen",
      "credentials": {
        "wahaApi": {
          "id": "Lyi7jbLCaqZDj3KQ",
          "name": "WAHA account"
        }
      }
    },
    {
      "parameters": {
        "resource": "Chatting",
        "operation": "Send Text",
        "session": "={{ $('Dados').item.json.session }}",
        "chatId": "={{ $('Dados').item.json.chatId }}",
        "text": "={{ $('AI Agent').item.json.output }}",
        "requestOptions": {}
      },
      "type": "n8n-nodes-waha.WAHA",
      "typeVersion": 202411,
      "position": [
        560,
        -176
      ],
      "id": "2b14264f-0335-4a87-9644-80f951b8b731",
      "name": "Send a text message",
      "credentials": {
        "wahaApi": {
          "id": "Lyi7jbLCaqZDj3KQ",
          "name": "WAHA account"
        }
      }
    }
  ],
  "pinData": {
    "Webhook": [
      {
        "json": {
          "headers": {
            "accept": "application/json, text/plain, */*",
            "content-type": "application/json",
            "user-agent": "WAHA/2026.1.3",
            "x-webhook-request-id": "18a767mkh37y0h",
            "x-webhook-timestamp": "1768580591344",
            "content-length": "2157",
            "accept-encoding": "gzip, compress, deflate, br",
            "host": "host.docker.internal:5678",
            "connection": "keep-alive"
          },
          "params": {},
          "query": {},
          "body": {
            "id": "evt_01kf3swbqgyssk8qevwzhf3ajw",
            "timestamp": 1768580591344,
            "event": "message",
            "session": "default",
            "metadata": {},
            "me": {
              "id": "558589848381@c.us",
              "pushName": "Staff Monitoramento",
              "lid": "87678150291685@lid",
              "jid": "558589848381:12@s.whatsapp.net"
            },
            "payload": {
              "id": "false_38203818037365@lid_3AC22AE73E797638455D",
              "timestamp": 1768580591,
              "from": "38203818037365@lid",
              "fromMe": false,
              "source": "app",
              "body": "Ixi kkk",
              "to": null,
              "participant": null,
              "hasMedia": false,
              "media": null,
              "ack": 2,
              "location": null,
              "vCards": null,
              "ackName": "DEVICE",
              "replyTo": null,
              "_data": {
                "Info": {
                  "Chat": "38203818037365@lid",
                  "Sender": "38203818037365@lid",
                  "IsFromMe": false,
                  "IsGroup": false,
                  "AddressingMode": "",
                  "SenderAlt": "558589130738@s.whatsapp.net",
                  "RecipientAlt": "",
                  "BroadcastListOwner": "",
                  "BroadcastRecipients": null,
                  "ID": "3AC22AE73E797638455D",
                  "ServerID": 0,
                  "Type": "text",
                  "PushName": "Lu✨",
                  "Timestamp": "2026-01-16T16:23:11Z",
                  "Category": "",
                  "Multicast": false,
                  "MediaType": "",
                  "Edit": "",
                  "MsgBotInfo": {
                    "EditType": "",
                    "EditTargetID": "",
                    "EditSenderTimestampMS": "0001-01-01T00:00:00Z"
                  },
                  "MsgMetaInfo": {
                    "TargetID": "",
                    "TargetSender": "",
                    "TargetChat": "",
                    "DeprecatedLIDSession": null,
                    "ThreadMessageID": "",
                    "ThreadMessageSenderJID": ""
                  },
                  "VerifiedName": null,
                  "DeviceSentMeta": null
                },
                "Message": {
                  "conversation": "Ixi kkk",
                  "messageContextInfo": {
                    "deviceListMetadata": {
                      "senderTimestamp": 1768580572,
                      "recipientKeyHash": "GRsrxos3DWU4dA==",
                      "recipientTimestamp": 1768575618
                    },
                    "deviceListMetadataVersion": 2,
                    "messageSecret": "R2HpLJ3QhdsplZD35twh6WdIIbZijdrloFiB0xWKjKU="
                  }
                },
                "IsEphemeral": false,
                "IsViewOnce": false,
                "IsViewOnceV2": false,
                "IsViewOnceV2Extension": false,
                "IsDocumentWithCaption": false,
                "IsLottieSticker": false,
                "IsBotInvoke": false,
                "IsEdit": false,
                "SourceWebMsg": null,
                "UnavailableRequestID": "",
                "RetryCount": 0,
                "NewsletterMeta": null,
                "RawMessage": {
                  "conversation": "Ixi kkk",
                  "messageContextInfo": {
                    "deviceListMetadata": {
                      "senderTimestamp": 1768580572,
                      "recipientKeyHash": "GRsrxos3DWU4dA==",
                      "recipientTimestamp": 1768575618
                    },
                    "deviceListMetadataVersion": 2,
                    "messageSecret": "R2HpLJ3QhdsplZD35twh6WdIIbZijdrloFiB0xWKjKU="
                  }
                },
                "Status": 3
              }
            },
            "environment": {
              "version": "2026.1.3",
              "engine": "GOWS",
              "tier": "CORE",
              "browser": null,
              "platform": "linux/x64"
            }
          },
          "webhookUrl": "http://host.docker.internal:5678/webhook-test/webhook",
          "executionMode": "test"
        },
        "pairedItem": {
          "item": 0
        }
      }
    ]
  },
  "connections": {
    "Webhook": {
      "main": [
        [
          {
            "node": "Dados",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Dados": {
      "main": [
        [
          {
            "node": "Switch",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Switch": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Google Gemini Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Redis Chat Memory": {
      "ai_memory": [
        [
          {
            "node": "AI Agent",
            "type": "ai_memory",
            "index": 0
          }
        ]
      ]
    },
    "AI Agent": {
      "main": [
        [
          {
            "node": "Send Seen",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Send Seen": {
      "main": [
        [
          {
            "node": "Send a text message",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1",
    "availableInMCP": false
  },
  "versionId": "2ff44b49-29f6-42de-97a5-18d9381fdd74",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "146835f5277f78fa472aba05cec3ad06cec284e4eef70d88b5da961cec524a6a"
  },
  "id": "7e0XHJdVjfUG34M_J8_G7",
  "tags": []
}
