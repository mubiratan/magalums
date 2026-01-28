# Magalu MS - Notification Scheduler

## 📝 Descrição
Microsserviço desenvolvido com **Spring Boot** responsável por agendar e enviar notificações via múltiplos canais (Email, SMS, Push, WhatsApp).

O sistema funciona de maneira assíncrona:
1. A API recebe uma solicitação de agendamento.
2. A notificação é salva no banco de dados com status `PENDING`.
3. Um **Scheduler** (tarefa agendada) roda a cada 1 minuto, busca notificações pendentes cuja data e hora já chegaram e realiza o envio.

## 🚀 Tecnologias Utilizadas
- **Java 17**
- **Spring Boot 3**
- **Spring Data JPA** (Hibernate)
- **MySQL**
- **Docker** (para containerização do banco de dados)
- **Maven**

## ⚙️ Como Executar o Projeto

### Pré-requisitos
- Java 17+ instalado
- Docker e Docker Compose instalados

### Passo 1: Subir o Banco de Dados
O projeto possui um arquivo `docker-compose.yml` na pasta `docker/`. Execute o comando abaixo para subir o MySQL:

```bash
docker-compose -f docker/docker-compose.yml up -d
```

### Passo 2: Executar a Aplicação
Na raiz do projeto, execute:

```bash
./mvnw spring-boot:run
```

A aplicação iniciará na porta `8080`.

---

## 🔌 Documentação da API

### 1. Agendar uma Notificação
Cria um novo agendamento de notificação.

- **URL:** `/notifications`
- **Método:** `POST`
- **Corpo da Requisição (JSON):**

```json
{
  "dateTime": "2024-06-20T10:00:00",
  "destination": "cliente@magalu.com",
  "message": "Sua promoção chegou!",
  "channel": "email"
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `dateTime` | String (ISO-8601) | Data e hora para o envio da notificação. |
| `destination` | String | Destino (Email, Telefone, Token, etc). |
| `message` | String | Conteúdo da mensagem. |
| `channel` | Enum | Canal de envio: `email`, `sms`, `push`, `whatsapp`. |

### 2. Consultar Notificação
Verifica o status de uma notificação específica.

- **URL:** `/notifications/{notificationId}`
- **Método:** `GET`

### 3. Cancelar Notificação
Cancela uma notificação que ainda não foi enviada.

- **URL:** `/notifications/{notificationId}`
- **Método:** `DELETE`

---

## 🕒 Funcionamento do Scheduler
O projeto possui uma classe `MagaluTaskScheduler` configurada para rodar a cada **1 minuto**.

A lógica de envio é:
1. Busca no banco notificações com status `PENDING` ou `ERROR`.
2. Filtra apenas aquelas cuja `dateTime` é anterior ao momento atual (`LocalDateTime.now()`).
3. Simula o envio e atualiza o status para `SUCCESS`.

## 🗄️ Estrutura do Banco de Dados
O sistema popula automaticamente tabelas de domínio ao iniciar (`DataLoader`):
- **tb_channel**: Tipos de canais (1-Email, 2-SMS, 3-Push, 4-WhatsApp).
- **tb_status**: Status possíveis (1-Pending, 2-Success, 3-Error, 4-Canceled).
