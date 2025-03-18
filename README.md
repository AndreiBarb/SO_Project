# Projeto de Sistemas Operativos 2024/2025

Este projeto foi desenvolvido no âmbito da disciplina de **Sistemas Operativos** (SO) do Instituto Superior Técnico (IST). O projeto tem como objetivo:

1. **Tornar o IST-KVS acessível a processos clientes** que monitorizam pares chave-valor através de _named pipes_.
2. **Gerir a terminação das ligações entre o IST-KVS e os clientes** utilizando sinais.

## Estrutura do Projeto

O código base fornecido contém:
- **Servidor IST-KVS**: Implementação que gerencia sessões de clientes e alterações na estrutura de dados.
- **API Cliente**: Interface para interação com o servidor via _named pipes_.
- **Comandos suportados**: Extensão dos comandos da primeira parte do projeto para permitir a subscrição e monitorização de chaves.

## Compilação e Execução

1. **Compilar o projeto**:
   ```sh
   make
   ```

2. **Executar o servidor**:
   ```sh
   ./kvs <dir_jobs> <max_threads> <backups_max> <nome_do_FIFO_de_registo>
   ```

3. **Executar o cliente**:
   ```sh
   ./client <id_do_cliente> <nome_do_FIFO_de_registo>
   ```

## Funcionalidades

### Interação Cliente-Servidor via Named Pipes
- O servidor cria um _named pipe_ para receber conexões de clientes.
- Cada cliente cria três _named pipes_ para comunicação bidirecional e notificações.
- O servidor aceita um número máximo de sessões concorrentes **S** (definido no código-fonte).
- Durante uma sessão, o cliente pode **adicionar** ou **remover subscrições** para chaves específicas.
- O servidor envia notificações para os clientes sempre que o valor de uma chave subscrita for alterado.

### API Cliente do IST-KVS

A API permite que os clientes realizem as seguintes operações:

```c
int kvs_connect(char const *req_pipe, char const *resp_pipe, char const *notif_pipe, char const *server_pipe);
int kvs_disconnect();
int kvs_subscribe(char const *key);
int kvs_unsubscribe(char const *key);
```

- **`kvs_connect`**: Estabelece a sessão com o servidor.
- **`kvs_disconnect`**: Finaliza a sessão e remove todas as subscrições.
- **`kvs_subscribe`**: Subscreve notificações para uma chave.
- **`kvs_unsubscribe`**: Cancela a subscrição de uma chave.

### Tratamento de Sinais

- O servidor escuta o **SIGUSR1** para finalizar todas as conexões ativas.
- Os clientes detectam o encerramento dos _named pipes_ e terminam automaticamente.
