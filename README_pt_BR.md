
# Syrin Speak Audio Agent

## Descrição

O componente `syrin-speak` processa arquivos de áudio para reprodução a partir de mensagens no RabbitMQ e interage com o MinIO para baixar e enviar arquivos. O agente realiza as seguintes tarefas:

1. Baixa arquivos de áudio do MinIO.
2. Reproduz áudio nos dispositivos de saída disponíveis.
3. Faz upload do áudio reproduzido para uma subpasta no MinIO.
4. Exclui os arquivos originais e locais após o processamento.
5. Publica mensagens confirmando o processamento bem-sucedido.

## Demo

![Application Demo](./diagram/Syrin-Speak.gif)

## Funcionalidades

- **RabbitMQ**: Conecta-se a filas do RabbitMQ para receber e processar mensagens de áudio.
- **MinIO**: Baixa e envia arquivos de áudio para o armazenamento MinIO.
- **Reprodução de Áudio**: Tenta reproduzir o áudio em todos os dispositivos disponíveis até ser bem-sucedido.
- **Fila de Reprocessamento**: Envia mensagens para a fila de reprocessamento em caso de falha na reprodução.

## Requisitos

- **Python 3.x**
- **Bibliotecas Python**:
  - `pika` (interação com RabbitMQ)
  - `minio` (integração com MinIO)
  - `sounddevice` (reprodução de áudio)
  - `numpy` (processamento de dados de áudio)
  - `wave` (manipulação de arquivos WAV)

Instale as dependências com:
```bash
pip install pika minio sounddevice numpy wave
```

## Configuração

As configurações são carregadas das variáveis de ambiente:

### Configurações do RabbitMQ

- `RABBITMQ_HOST`: Host do RabbitMQ (padrão: `127.0.0.1`)
- `RABBITMQ_PORT`: Porta do RabbitMQ (padrão: `5672`)
- `RABBITMQ_VHOST`: Virtual host no RabbitMQ
- `RABBITMQ_USER`: Nome de usuário do RabbitMQ
- `RABBITMQ_PASS`: Senha do RabbitMQ
- `RABBITMQ_TTL_DLX`: TTL da mensagem em ms (padrão: `60000`)

### Configurações do MinIO

- `MINIO_URL`: Host do MinIO (padrão: `127.0.0.1`)
- `MINIO_PORT`: Porta do MinIO (padrão: `9000`)
- `MINIO_ROOT_USER`: Nome de usuário do MinIO
- `MINIO_ROOT_PASSWORD`: Senha do MinIO
- `MINIO_BUCKET_WORK`: Bucket no MinIO (padrão: `syrin`)

## Como Executar

1. Configure as variáveis de ambiente.
2. Inicie o agente:

```bash
python syrin-speak.py
```

O agente consome mensagens da fila `03_syrin_notification_audio_process_play`, processa e publica em outras filas se bem-sucedido.

## Instalação como Serviço

1. Copie o arquivo `syrin-speak.py` para `/usr/local/bin` e torne-o executável.
2. Copie o arquivo de serviço systemd `syrin-speak.service` para `/etc/systemd/system`.
3. Recarregue o systemd, habilite e inicie o serviço:

```bash
sudo systemctl daemon-reload
sudo systemctl enable syrin-speak.service
sudo systemctl start syrin-speak.service
```

4. Verifique o status e os logs do serviço:

```bash
sudo systemctl status syrin-speak.service
journalctl -u syrin-speak.service -f
```

## Licença

Este projeto está licenciado sob a Licença MIT.
