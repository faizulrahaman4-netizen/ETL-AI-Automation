{
  "name": "ETL AI Automation",
  "nodes": [
    {
      "parameters": {
        "formTitle": "File Upload",
        "formDescription": "Upload the student data",
        "formFields": {
          "values": [
            {
              "fieldLabel": "File Upload",
              "fieldType": "file"
            }
          ]
        },
        "options": {
          "appendAttribution": false
        }
      },
      "type": "n8n-nodes-base.formTrigger",
      "typeVersion": 2.6,
      "position": [
        0,
        0
      ],
      "id": "dc25f206-7948-47eb-8893-c39fbdf80437",
      "name": "CSV Upload",
      "webhookId": "cb8bcdeb-c497-4c59-86a4-3a690d035f56"
    },
    {
      "parameters": {
        "binaryPropertyName": "File_Upload",
        "options": {}
      },
      "type": "n8n-nodes-base.extractFromFile",
      "typeVersion": 1.1,
      "position": [
        224,
        0
      ],
      "id": "3848fe9d-3d77-454d-9b5d-b8bafc4f1e87",
      "name": "Extract from File"
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "=Clean this student enrollment row and return ONLY a JSON object.\nRow data: {{ JSON.stringify($json) }}\nRules:\n- Name: Title Case\n- Email: if invalid (no @ or no .com/.in) set INVALID_EMAIL\n- Phone: if less than 10 digits or empty set MISSING\n- Course: only Python or Machine Learning or Data Science\n- Fee_Paid: yes/YES = true, no/NO = false\n- City: Title Case, empty = UNKNOWN\n- Enrolled_Date: YYYY-MM-DD format\nReturn ONLY raw JSON. No explanation. No markdown.",
        "hasOutputParser": true,
        "batching": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chainLlm",
      "typeVersion": 1.9,
      "position": [
        448,
        0
      ],
      "id": "5c3d185e-8f79-4079-af13-fb2f45c944ed",
      "name": "Basic LLM Chain"
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "value": "gpt-4.1-mini",
          "mode": "list",
          "cachedResultName": "gpt-4.1-mini"
        },
        "builtInTools": {},
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.3,
      "position": [
        528,
        240
      ],
      "id": "d3b0a252-abb2-460c-a1e0-40c8f40dfeb5",
      "name": "OpenAI Chat Model",
      "credentials": {
        "openAiApi": {
          "id": "xkW85WYT0rrkfON6",
          "name": "OpenAI account"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "const items = $input.all();\n\nreturn items.map(item => {\n  const raw = item.json;\n\n  // Get the AI output\n  let text = raw.output || raw.text || raw.content || raw;\n\n  // Parse JSON string\n  if (typeof text === 'string') {\n    text = JSON.parse(text);\n  }\n\n  // Clean email\n  if (\n    !text.Email ||\n    text.Email === 'INVALID_EMAIL' ||\n    !/^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$/.test(text.Email)\n  ) {\n    text.Email = null;\n  }\n\n  // Clean phone\n  if (text.Phone) {\n    text.Phone = String(text.Phone).replace(/\\D/g, '');\n  }\n\n  // Clean text fields\n  if (text.Student_ID) text.Student_ID = String(text.Student_ID).trim();\n  if (text.Name) text.Name = String(text.Name).trim();\n  if (text.Course) text.Course = String(text.Course).trim();\n  if (text.City) text.City = String(text.City).trim();\n  if (text.Enrolled_Date) text.Enrolled_Date = String(text.Enrolled_Date).trim();\n\n  return {\n    json: text\n  };\n});"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        800,
        0
      ],
      "id": "d5eb77e6-70a0-4c50-9792-9d45ee2f51f6",
      "name": "Code in JavaScript"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "92ff3a41-8ee5-4069-aeb7-db2261551819",
              "leftValue": "={{ $json.City }}",
              "rightValue": "Chennai",
              "operator": {
                "type": "string",
                "operation": "equals",
                "name": "filter.operator.equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        1008,
        0
      ],
      "id": "2dbbe2ec-ac7c-468a-b333-f73472e312e6",
      "name": "City"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "3f71c8d4-d2c5-4205-95d9-6d74449c61e8",
              "leftValue": "={{ $json.Email }}",
              "rightValue": "",
              "operator": {
                "type": "string",
                "operation": "equals",
                "name": "filter.operator.equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        1232,
        -320
      ],
      "id": "f5d46051-4c85-41a7-a7b0-e5514c4af3b6",
      "name": "Chennai"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "a9631cf2-d0a7-4b25-872d-7128cd1d4cfd",
              "leftValue": "={{ $json.Email }}",
              "rightValue": "",
              "operator": {
                "type": "string",
                "operation": "equals",
                "name": "filter.operator.equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        1232,
        336
      ],
      "id": "21d9594a-c170-467b-8b85-efd62de62ed8",
      "name": "Other Cities"
    },
    {
      "parameters": {
        "binaryPropertyName": "Chennai_Invalid_Email",
        "options": {
          "fileName": "Chennai_Invalid_Email"
        }
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        1456,
        -496
      ],
      "id": "4625fa90-77e3-4e23-b1d9-bb25843691a0",
      "name": "Chennai_Invalid_Email"
    },
    {
      "parameters": {
        "binaryPropertyName": "Chennai_Valid_Email",
        "options": {
          "fileName": "Chennai_Valid_Email"
        }
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        1456,
        -176
      ],
      "id": "a584d161-802c-41f1-812c-2b76ef16cd37",
      "name": "Chennai_Valid_Email"
    },
    {
      "parameters": {
        "binaryPropertyName": "Other__Invalid_Email",
        "options": {
          "fileName": "Other__Invalid_Email"
        }
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        1472,
        160
      ],
      "id": "edfb558b-03e0-4b64-97c7-0de649eef20e",
      "name": "Other__Invalid_Email"
    },
    {
      "parameters": {
        "binaryPropertyName": "Other__Valid_Email",
        "options": {
          "fileName": "Other__Valid_Email"
        }
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        1472,
        480
      ],
      "id": "229cf396-7965-47e7-9a5b-c8f1fdec271b",
      "name": "Other__Valid_Email"
    }
  ],
  "pinData": {},
  "connections": {
    "CSV Upload": {
      "main": [
        [
          {
            "node": "Extract from File",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract from File": {
      "main": [
        [
          {
            "node": "Basic LLM Chain",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "Basic LLM Chain",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Basic LLM Chain": {
      "main": [
        [
          {
            "node": "Code in JavaScript",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Code in JavaScript": {
      "main": [
        [
          {
            "node": "City",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "City": {
      "main": [
        [
          {
            "node": "Chennai",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Other Cities",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Chennai": {
      "main": [
        [
          {
            "node": "Chennai_Invalid_Email",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Chennai_Valid_Email",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Other Cities": {
      "main": [
        [
          {
            "node": "Other__Invalid_Email",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Other__Valid_Email",
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
    "binaryMode": "separate"
  },
  "versionId": "7360a3d9-0da6-496e-b442-c0af349c8d97",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "3b2d897e7154c9cf2444c7c855855234462e8c89ec41ef38156bfffea31a27c6"
  },
  "nodeGroups": [],
  "id": "f27YkvRbFOjZwftn",
  "tags": []
}
