# ⚓ Cybernautica - Equipe Finalista no Porto Hack 2025!

> Automação inteligente para acompanhamento de operações portuárias 🚢🤖

O **Cybernautica** é um workflow para n8n que consulta, organiza e interpreta dados de operações portuárias. Ele reúne informações de praticagem, rebocadores, amarração, terminais portuários e armadores para criar uma visão consolidada da situação de cada navio e manobra.

## 🎯 Objetivo

Centralizar informações operacionais do ambiente portuário para acompanhar:

* 🚢 Chegadas e saídas previstas e realizadas;
* ⚓ Situação de atracação e operação dos navios;
* 🧭 Status de praticagem, rebocagem e amarração;
* ⚠️ Pendências, atrasos, cancelamentos e intercorrências;
* 💡 Possíveis soluções para problemas operacionais.

Desta forma, o workflow se propõe a diminuir ruídos de comunicação e deixar o Porto de Santos ainda mais eficiente!

## 🔄 Como funciona

O workflow executa as seguintes etapas:

1. ▶️ Início manual da execução;
2. 🔢 Geração de IDs de `1` a `10`;
3. 🔁 Processamento sequencial de cada ID;
4. 🌐 Consulta de informações de rebocadores, praticagem, amarração, terminais e armadores;
5. 🧠 Análise da manobra por um agente de IA;
6. 🔎 Checagem da operação do navio por outro agente de IA;
7. 📄 Conversão do resultado consolidado em arquivo de texto.

## 🗺️ Arquitetura do workflow

```text
▶️ Manual Trigger
      ↓
🔢 IDs a serem percorridos
      ↓
🔁 Loop Over Items
      ↓
🧠 Agente Analista e Estruturador
      ↓
🔎 Agente de Checagem
      ↓
🔁 Loop Over Items
      ↓
📄 Convert to File
```

## 🧠 Agente Analista e Estruturador

Este agente consulta e interpreta dados relacionados a:

* 🧭 Praticagem;
* 🚤 Rebocadores;
* 🪢 Amarração.

A resposta é estruturada em JSON e pode incluir:

```json
{
  "identificadorNavio": {
    "comum": "Identificador compartilhado entre praticagem e amarração",
    "rebocador": "Identificador utilizado no serviço de rebocagem"
  },
  "nomeRebocador": "Nome do rebocador",
  "tipoMovimentacao": "entrada ou saída",
  "janelaSolicitada": "Data ou horário solicitado à praticagem",
  "horaPrevistaRebocador": "Horário previsto para a operação",
  "horaRealRebocador": "Horário real da operação",
  "statusRebocador": "Status do serviço de rebocagem",
  "statusPraticagem": "Status da praticagem",
  "statusAmarracao": "Status da amarração",
  "statusFinal": "cancelado, realizada, pendente ou realizada com atraso",
  "localAmarracao": "Local da amarração",
  "equipeResponsavel": "Equipe responsável",
  "observacoesGerais": "Interpretação da situação atual",
  "solucao": "Sugestão de solução para problemas identificados"
}
```

## 🔎 Agente de Checagem

Este agente consulta dados de terminais e armadores para consolidar a situação operacional do navio.

A saída esperada contém:

```json
{
  "identificadorNavio": "Identificador do navio",
  "nomeTerminal": "Terminal portuário",
  "tipoOperacao": "carga, descarga ou embarque",
  "dataPrevistaAtracacao": "Data prevista",
  "dataRealAtracacao": "Data real",
  "dataPrevisaoChegada": "Previsão de chegada",
  "dataPrevisaoSaida": "Previsão de saída",
  "situacaoAtual": "entrando, saindo, aguardando, operando ou cancelado"
}
```

> ℹ️ Quando não houver informação disponível, o workflow utiliza `—` como valor padrão.

## 🚦 Regras para situação atual

| Situação     | Critério                                                        |
| ------------ | --------------------------------------------------------------- |
| 🟢 Entrando  | O navio está previsto para chegar, mas ainda não atracou.       |
| ⚙️ Operando  | O navio está atracado e realizando operação.                    |
| 🚢 Saindo    | A operação foi finalizada e o navio está em manobra de saída.   |
| ⏳ Aguardando | Existe alguma pendência, como barra, documentação ou rebocador. |
| ❌ Cancelado  | A operação foi cancelada.                                       |

## 📊 Regras para status final da manobra

| Condição                                 | Status final           |
| ---------------------------------------- | ---------------------- |
| ❌ Qualquer serviço cancelado             | `cancelado`            |
| ✅ Todos os serviços concluídos           | `realizada`            |
| ⏳ Algum serviço pendente                 | `pendente`             |
| ⚠️ Atraso ou intercorrência identificada | `realizada com atraso` |

## 🌐 Serviços consultados

| Serviço                | Endpoint                                                   |
| ---------------------- | ---------------------------------------------------------- |
| 🚤 Rebocadores         | `https://api.hackathon.souamigu.org.br/rebocadores/{id}`   |
| 🧭 Praticagem          | `https://api.hackathon.souamigu.org.br/praticagem/{id}`    |
| 🪢 Amarração           | `https://api.hackathon.souamigu.org.br/amarracao/{id}`     |
| 🏗️ Terminal portuário | `https://api.hackathon.souamigu.org.br/terminal-portuario` |
| 🚢 Armador             | `https://api.hackathon.souamigu.org.br/armador`            |

## 🧰 Pré-requisitos

* 🖥️ Instância do n8n;
* 🔑 Credencial da OpenAI configurada no n8n;
* 🌐 Acesso aos endpoints da API;
* 🤖 Modelo de linguagem configurado no nó **Chamar OpenAI Chat Model**.

## 🚀 Instalação

1. 🖥️ Abra sua instância do n8n;
2. ➕ Crie um novo workflow;
3. 📥 Importe o arquivo `Cybernautica.json`;
4. 🔑 Configure a credencial da OpenAI no nó **Chamar OpenAI Chat Model**;
5. 🌐 Verifique se os endpoints da API estão acessíveis;
6. ▶️ Execute o workflow manualmente.

## ⚙️ Personalização

Você pode adaptar o projeto para sua operação:

* 🔢 Alterar a faixa de IDs processados;
* ✍️ Ajustar os prompts dos agentes;
* ➕ Incluir novos serviços ou APIs;
* 📄 Alterar o formato do arquivo final;
* 🗄️ Conectar os dados a bancos, planilhas ou dashboards;
* 🔔 Criar alertas para atrasos e pendências.

## 🧩 Estrutura dos nós

| Nó                                  | Responsabilidade                             |
| ----------------------------------- | -------------------------------------------- |
| ▶️ When clicking ‘Execute workflow’ | Inicia a execução manualmente.               |
| 🔢 IDs a serem percorridos          | Cria a lista de IDs de operação.             |
| 🔁 Loop Over Items                  | Processa cada ID individualmente.            |
| 🚤 Rebocador                        | Consulta dados de rebocadores.               |
| 🧭 Praticagem                       | Consulta dados de praticagem.                |
| 🪢 Amarração                        | Consulta dados de amarração.                 |
| 🏗️ Terminal                        | Consulta dados de terminais portuários.      |
| 🚢 Armador                          | Consulta dados de armadores e navios.        |
| 🧠 Agente Analista e Estruturador   | Analisa a manobra e gera dados estruturados. |
| 🔎 Agente de Checagem               | Consolida a situação operacional do navio.   |
| 📄 Convert to File                  | Converte a saída em arquivo de texto.        |

## 💡 Próximos passos

* 🗄️ Persistir resultados em banco de dados;
* 📈 Criar dashboards em tempo real;
* 🔔 Enviar alertas por e-mail, Slack ou WhatsApp;
* 🧾 Gerar relatórios automáticos;
* 🔍 Criar filtros por navio, terminal ou período;
* 📚 Manter histórico de operações e indicadores;
* 🖱️ Criar uma interface para consulta manual de operações.

Feito por alunos da UNIP + FATEC de Santos @ 2025

