
# Redis Laboratory Runbook

Este repositório contém os comandos necessários para configurar e utilizar o Redis em um ambiente de laboratório. Siga as instruções abaixo para instalar o Docker, criar containers do Redis e executar comandos no Redis.

## Pré-requisitos

### Instalação do Docker no Windows

1. **Verifique os Requisitos:**
   - Windows 10 64 bits (versão 19041 ou superior).
   - Virtualização habilitada na BIOS (Intel VT-x ou AMD-V).

2. **Baixe o Docker Desktop:**
   - Acesse a página oficial de download do Docker Desktop.
   - Clique no botão de download para baixar o instalador.

3. **Execute o Instalador:**
   - Após o download, execute o arquivo `Docker Desktop Installer.exe`.
   - Siga as instruções do instalador para concluir a instalação.

4. **Ative o WSL 2 (Windows Subsystem for Linux):**
   - Abra o PowerShell como administrador e execute o seguinte comando:
     ```sh
     wsl --set-default-version 2
     ```

5. **Inicie o Docker Desktop:**
   - Após a instalação, o Docker Desktop será iniciado automaticamente.

6. **Teste o Docker:**
   - Abra o PowerShell ou o terminal e execute alguns comandos do Docker para verificar se tudo está funcionando corretamente:
     ```sh
     docker --version
     docker-compose --version
     ```

7. **Adicione seu Usuário ao Grupo Docker (opcional):**
   - Para evitar usar o Docker com privilégios de administrador, você pode adicionar seu usuário ao grupo Docker:
     ```sh
     Add-LocalGroupMember -Group "Docker Users" -Member "NomeDoSeuUsuário"
     ```

### Instalação do Docker no Ubuntu Server

### Conectando na VM na cloud de uma maquina windows
1.**Descubra user local com **
```cmd
whoami
```
2.**Alterar permissao de chave pem para ser executado no powershell e conectar via ssh**
```cmd
icacls "imdb-chaves.pem" /inheritance:r /grant:r SYSTEM:F /grant:r Administrators:F /grant:r <SEU_USER_WINDOWS>:F

ssh -i imdb-chaves.pem ubuntu@<IP_PUBLICO_VM>
```

1. **Atualize o Sistema:**
   - Abra um terminal e execute os seguintes comandos:
     ```sh
     sudo apt update
     sudo apt upgrade
     ```

2. **Instale o Docker e o Docker Compose:**
   - Execute o seguinte comando:
     ```sh
     sudo apt install docker.io docker-compose
     ```

3. **Inicie o Serviço do Docker:**
   - Após a instalação, inicie o serviço do Docker:
     ```sh
     sudo systemctl start docker
     ```

4. **Habilite o Docker na Inicialização:**
   - Para garantir que o Docker seja iniciado automaticamente sempre que o sistema for reiniciado, execute:
     ```sh
     sudo systemctl enable docker
     ```

5. **Adicione seu Usuário ao Grupo Docker (opcional):**
   - Para executar comandos Docker sem precisar usar `sudo`, adicione seu usuário ao grupo Docker:
     ```sh
     sudo usermod -aG docker $USER
     ```

6. **Verifique a Instalação:**
   - Para confirmar que o Docker está funcionando corretamente, execute:
     ```sh
     docker --version
     docker-compose --version
     ```

7. **Teste com um Contêiner Hello World:**
   - Execute o seguinte comando para testar o Docker:
     ```sh
     docker run hello-world
     ```

1. **Atualize o Sistema:**
   - Abra um terminal e execute os seguintes comandos:
     ```sh
     sudo apt update
     sudo apt upgrade
     ```

## Criando Containers do Redis

1. **Criar e Iniciar o Container Redis:**
   - O comando abaixo expõe o `redis-server` na porta 6379 e o `RedisInsight` na porta 8001:
     ```sh
     docker run -d --name redis-stack -p 6379:6379 -p 8001:8001 redis/redis-stack:latest
     ```

2. **Conectar ao Redis Server:**
   - Use o `redis-cli` para se conectar ao servidor em `localhost:6379`:
     ```sh
     docker exec -it redis-stack redis-cli
     ```

3. **Se o Container já foi Criado Previamente:**
   - Iniciar o container:
     ```sh
     docker start redis-stack
     ```
   - Remover o container:
     ```sh
     docker rm redis-stack
     ```

## Comandos Redis

### Comandos Básicos

- **SET e GET:**
  ```sh
  set user:1 daniel
  get user:1
  SETEX minha_chave 10 "Este valor expirará em 10 segs"
  ```

- **Parâmetros Adicionais:**
  ```sh
  SET CHAVE1 000 NX
  EXPIRE CHAVE1 5
  INCR CONTADOR
  INCRBY CONTADOR 5
  DECR CONTADOR
  DECRBY CONTADOR 2
  GET user:1
  ```

- **Múltiplos SET e GET:**
  ```sh
  MSET a 10 b 20 c 30
  MGET a b c
  ```

### Comandos de Lista

- **Adicionar e Listar Elementos:**
  ```sh
  RPUSH lista a
  RPUSH lista b
  LPUSH lista c
  RPUSH lista d
  LRANGE lista 0 -1
  ```

- **Mais Exemplos:**
  ```sh
  RPUSH PILHA A B C
  RPOP PILHA
  LPOP PILHA
  LRANGE PILHA 0 -1
  ```

### Comandos de Hash

- **Exemplos de Hash:**
  ```sh
  HMSET usuario:1000 nome Daniel estado SP idade 42
  HGETALL usuario:1000
  DEL usuario:1000
  ```

### Comandos de Conjunto (SET)

- **Comandos Básicos:**
  ```sh
  SADD meuset 1 2 3
  SMEMBERS meuset
  SISMEMBER meuset 3
  SISMEMBER meuset 30
  DEL meuset
  ```

### Comandos de Conjunto Ordenado (ZADD)

- **Adicionar e Atualizar Membros:**
  ```sh
  ZADD meuconjunto 1 "um" 2 "dois" 3 "três"
  ZADD meuconjunto 4 "um"
  ZADD meuconjunto 3 "quatro" 3 "cinco"
  ZREM meuconjunto "três"
  ZRANGEBYSCORE meuconjunto 0 5
  ZSCORE meuconjunto cinco
  ```

### Comandos Geo Espacial

- **Principais Comandos:**
  ```sh
  GEOADD cars -115.17087 36.12306 my-car
  GEOADD cars -115.171971 36.120609 robins-car
  GEODIST cars my-car robins-car
  GEODIST cars my-car robins-car ft
  GEORADIUS cars -115.17258 36.11996 100 m
  GEORADIUSBYMEMBER cars robins-car 100 m
  GEORADIUSBYMEMBER cars robins-car 100 m WITHDIST
  GEORADIUSBYMEMBER cars robins-car 100 m WITHDIST WITHCOORD
  ZREM cars my-car
  ```

### Controle de Transação no Redis

- **Exemplo de Transação:**
  ```sh
  SET WALLET:DANIEL 100
  SET WALLET:JOAO 100
  MULTI
  DECRBY WALLET:JOAO 10
  INCRBY WALLET:DANIEL 10
  EXEC
  GET WALLET:DANIEL
  GET WALLET:JOAO
  ```

## Contribuição

Sinta-se à vontade para contribuir com este repositório. Abra uma issue ou envie um pull request com melhorias e sugestões.



