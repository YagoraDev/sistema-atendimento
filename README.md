# 🤖 Agente de Atendimento e Qualificação (Sujeito Programador)

Este projeto consiste em um ecossistema completo de **Multi-Agentes de IA** desenvolvido no **n8n** para automatizar o atendimento, qualificação de leads e follow-up para cursos de tecnologia (Fullstack PRO e Gestor IA).

O sistema atua diretamente no WhatsApp (via Evolution API), utilizando LLMs (OpenAI) para manter conversas humanizadas, consultar uma base de conhecimento (Google Docs) e persistir dados de clientes (Supabase).

## 🚀 Arquitetura e Funcionalidades

O fluxo foi desenhado para ser resiliente, lidando com diferentes tipos de entrada e mantendo o contexto do usuário.

*   **Multimodalidade:** Recebe e processa mensagens de texto, áudio (transcrição via Whisper) e imagem (análise via GPT-4o-mini).
*   **Buffer de Mensagens:** Utiliza **Redis** para agrupar mensagens enviadas em sequência rápida, evitando que o agente responda de forma fragmentada.
*   **Agente Principal (Lucas):** Consultor educacional com instruções estritas (Prompt Engineering) para manter respostas curtas, consultivas e focadas no escopo.
*   **Sub-Agentes (Tools):**
    *   *SubAgent de Interesse:* Identifica intenção de compra e atualiza o status no banco de dados.
    *   *SubAgent de Suporte Humano:* Transborda o atendimento para uma equipe humana (via Telegram) quando a IA não consegue resolver ou o lead fica "travado".
*   **Follow-up Automático:** Um segundo fluxo (agendado) analisa leads inativos há mais de 2 dias e gera mensagens personalizadas de reengajamento.

## 🛠️ Tecnologias Utilizadas

*   **Orquestração:** n8n (rodando em modo de fila/Queue Mode com Worker separado)
*   **Inteligência Artificial:** OpenAI (GPT-4o-mini / GPT-5-mini) e LangChain
*   **Banco de Dados (Relacional):** PostgreSQL 16 (para n8n, Evolution API e Memória do Agente).
*   **Banco de Dados (Cache):** Redis 7.2 (para buffer de mensagens e filas do n8n).
*   **Integração WhatsApp:** Evolution API v2.3.6.
*   **Notificações Internas:** Telegram Bot.
*   **Infraestrutura:** Docker e Docker Compose.

## 📂 Estrutura do Repositório

```text
├── workflow/
│   └── Atendimento_cliente.json    # Arquivo exportado do n8n (sem credenciais)
├── prompts/
│   ├── fullstack_pro.docx          # Base de conhecimento do curso Fullstack
│   ├── gestor_ia.docx              # Base de conhecimento do curso Gestor IA
│   ├── prompt_agente_lucas.txt     # Prompt do Agente Principal
│   ├── prompt_follow_up.txt        # Prompt do Agente de Follow-up
│   ├── prompt_inferencia_humana.txt# Regras de transferência para humano
│   ├── prompt_particionador.txt    # Prompt para dividir mensagens longas
│   ├── prompt_suporte_humano.txt   # Prompt do Subagente de Suporte
│   ├── prompt_agent.txt            # Prompt do Agente Principal
│   ├── prompt_agent_principal.txt  # Variação do Prompt Principal
│   └── prompt_subagent_interesse.txt # Prompt do Subagente de Interesse
├── .env.example                    # Modelo de variáveis de ambiente
├── .gitignore                      # Arquivos ignorados pelo Git
├── docker-compose.yml              # Configuração do ambiente (n8n, Postgres, Redis, Evolution)
└── README.md