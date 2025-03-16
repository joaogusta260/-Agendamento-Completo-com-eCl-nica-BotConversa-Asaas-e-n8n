![Fluxos Principais](./FOTO%20PRINCIPAL%20DOS%20FLUXOS.png)

# Agendamento Completo com eClínica, BotConversa, Asaas e n8n

Este projeto integra uma série de fluxos criados no **n8n** com as APIs do **eClínica** e **Asaas** e o sistema de mensagens do **BotConversa (BrisaBot)** para automatizar completamente o processo de agendamento – seja para avaliação presencial, online ou reavaliação – em uma clínica de cirurgia plástica renomada. Essa automação não só agiliza o atendimento como também aumentou em **60%** o número de agendamentos concluídos, diminuindo a evasão e otimizando a experiência do paciente e da equipe.

---

## Tecnologias Integradas

- **eClínica (API):** Gerencia o cadastro de pacientes, agendamentos, horários e status das consultas.  
- **BotConversa (BrisaBot):** Plataforma de mensagens via WhatsApp, responsável pela interação automática com o paciente (agendamento, lembretes, etc.).  
- **Asaas:** Responsável pela geração de cobranças (PIX ou cartão) e confirmação de pagamentos.  
- **n8n:** Plataforma de automação que orquestra todos os fluxos, integrando os sistemas e realizando validações e transformações de dados.

---

## Principais Resultados

- **Automação Total do Agendamento:** Criação, confirmação, cancelamento e reagendamento sem intervenção manual.  
- **Aumento de 60% nos Agendamentos:** Processo automatizado que diminui o tempo de atendimento e reduz a evasão.  
- **Integração Financeira Eficiente:** Cobranças geradas automaticamente e links de pagamento enviados via BotConversa.  
- **Centralização dos Dados:** Informações sincronizadas entre eClínica, BotConversa e Asaas.

---

## Visão Geral dos Fluxos

### 1. Fluxo de Confirmação de Agendamento (Avaliação Presencial, Online ou Reavaliação)
Este fluxo, executado diariamente às 8h, tem como objetivo confirmar automaticamente as consultas agendadas no sistema eClínica. Ele envia mensagens de confirmação via BotConversa, ajusta datas conforme o dia da semana (evitando fins de semana), valida telefones e nomes dos pacientes, e dispara um flow final para completar a interação.

![Confirmação Serve para Todos](./CONFIRMA%C3%87AO%20SERVE%20PARA%20TODOS.png)

**Como Funciona:**
- **Schedule Trigger (8h):**  
  Inicia o fluxo e verifica se o dia atual é sábado ou domingo para ajustar a data (soma 1 ou 2 dias, conforme necessário).
- **Busca de Agendamentos:**  
  Consulta os agendamentos no eClínica usando a data calculada e filtra aqueles com status “CONFIRMADO” ou ainda não definidos.
- **Loop & Extração:**  
  Para cada agendamento, obtém detalhes do paciente (ID, hora, profissional, etc.) e valida os dados via HTTP Requests.
- **Validação e Formatação:**  
  Converte telefones para o formato internacional (+55), separa e formata o nome do paciente e mapeia os nomes dos profissionais para uma apresentação padronizada.
- **Integração com BotConversa:**  
  Cria ou atualiza o “subscriber” no BotConversa e armazena campos personalizados (nome, data, hora, ID do agendamento e nome do profissional).
- **Envio do Flow Final:**  
  Após processar e validar os dados, dispara um flow (ex. ID 5487721) que envia a mensagem de confirmação ao paciente.

---

### 2. Reagendamento, Confirmação e Cancelamento
Este fluxo recebe webhooks indicando ações dos pacientes (confirmar ou cancelar agendamento). Dependendo do motivo recebido, o sistema atualiza o status do agendamento no eClínica (por exemplo, definindo “C” para confirmar ou “L” para cancelar) e envia notificações correspondentes via BotConversa.

![Confirma e Cancela](./CONFIRMA%20E%20CANCELA.png)

---

### 3. Lembretes Automáticos
Um Schedule Trigger dispara lembretes diários (como 1 dia antes da consulta) para os pacientes. O fluxo consulta os agendamentos do dia, valida e formata os dados dos pacientes e envia lembretes personalizados via BotConversa. Se houver algum dado inválido (por exemplo, telefone), uma notificação de erro é enviada para a equipe.

![Envio de Lembrete 1 Dia](./ENVIO%20DE%20LEMBRETE%201%20DIA.png)

---

### 4. Cobrança da Consulta
Um fluxo específico gera cobranças utilizando a API do Asaas:
- Valida o CPF do paciente e cria/encontra o cliente no Asaas.  
- Gera um “Payment” com vencimento pré-configurado e envia automaticamente o link de pagamento ao paciente via BotConversa.  
- Um fluxo adicional confirma o pagamento quando o Asaas dispara o webhook.

![Gerar Cobrança](./GERAR%20COBRANCA.png)

---

### 5. Fluxo de "Não Compareceu"
Após a data da consulta, o sistema verifica se o agendamento foi marcado como “FALTA”. Em caso afirmativo, o fluxo atualiza os dados do paciente e envia uma mensagem de follow-up via BotConversa para reagendamento.

![Não Compareceu](./NAO%20COMPARECEU.png)

---

## Benefícios e Conclusão

- **Agilidade e Conveniência:**  
  Os pacientes agendam, confirmam e recebem lembretes 24/7, sem a necessidade de intervenção manual.
  
- **Redução de Falhas:**  
  Dados validados (telefones e nomes) e ajuste de datas garantem que as confirmações sejam enviadas corretamente.
  
- **Centralização e Sincronia:**  
  Todos os dados são integrados entre o eClínica, BotConversa e Asaas, proporcionando um fluxo unificado e eficiente.
  
- **Escalabilidade:**  
  A mesma estrutura pode ser adaptada facilmente para novos profissionais, diferentes tipos de consultas ou regras de cobrança.

Este projeto exemplifica como a integração inteligente de **APIs** e **serviços em nuvem** – orquestrada pelo **n8n** – pode transformar a gestão de agendamentos e cobranças, proporcionando maior eficiência, redução de custos e uma experiência aprimorada para pacientes e equipes.
