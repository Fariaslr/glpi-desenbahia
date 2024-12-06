Aqui está o **README.md** atualizado e incluindo os novos dados fornecidos:

---

# **Docker Compose para GLPI e MariaDB**

Este repositório contém um ambiente configurado para rodar o GLPI integrado ao MariaDB utilizando **Docker Compose**.

---

## **Estrutura do Projeto**

- **`docker-compose.yml`**: Arquivo principal para configurar os serviços Docker.
- **`mariadb.env`**: Arquivo com variáveis de ambiente para configuração do MariaDB.
- **Backup de Banco de Dados**: Processo para criar e restaurar backups do banco GLPI.

---

## **Serviços Configurados**

### **1. MariaDB**
Banco de dados relacional utilizado pelo GLPI.

- **Imagem:** `mariadb:10.7`
- **Hostname:** `mariadb`
- **Rede:** `glpi-network`
- **Porta:** `3306` (internamente, não exposta diretamente)
- **Credenciais:** Configuradas no arquivo `mariadb.env`.

### **2. GLPI**
Sistema de gerenciamento de serviços de TI.

- **Imagem:** `diouxx/glpi`
- **Hostname:** `glpi`
- **Rede:** `glpi-network`
- **Porta:** `8081` (para acesso via navegador)

---

## **Configuração do Projeto**

### **1. Arquivo `docker-compose.yml`**

Certifique-se de que o arquivo contém o seguinte conteúdo:

```yaml
version: '3.8'

services:

  # MariaDB Container
  mariadb:
    image: mariadb:10.7
    ports:
      - "3306:3306"
    container_name: mariadb
    hostname: mariadb
    env_file:
      - ./mariadb.env
    networks:
      - glpi-network
    restart: always

  # GLPI Container
  glpi:
    image: diouxx/glpi
    container_name: glpi
    hostname: glpi
    ports:
      - "8081:80"
    networks:
      - glpi-network
    restart: always

networks:
  glpi-network:
    driver: bridge
```

### **2. Arquivo `mariadb.env`**

Crie um arquivo `mariadb.env` com o seguinte conteúdo:

```env
MARIADB_ROOT_PASSWORD=diouxx
MARIADB_DATABASE=glpi
MARIADB_USER=glpi_user
MARIADB_PASSWORD=glpi
```

### **3. Rede**

Certifique-se de que a rede `glpi-network` será gerenciada pelo Docker (já especificada no `docker-compose.yml`).

---

## **Iniciar o Ambiente**

1. Execute o comando para subir os containers em modo **detached**:
   ```bash
   docker-compose up -d
   ```

2. Verifique se os serviços estão rodando:
   ```bash
   docker-compose ps
   ```

3. Acesse o GLPI em: [http://localhost:8081](http://localhost:8081)

---

## **Contas de Acesso Padrão**

| **Usuário**  | **Senha**     | **Descrição**         |
|--------------|---------------|-----------------------|
| `glpi`       | `glpi`        | Conta de administrador|
| `tech`       | `tech`        | Conta de técnico      |
| `normal`     | `normal`      | Conta de usuário normal|
| `post-only`  | `postonly`    | Conta de post-only    |

---

## **Backup e Restauração do Banco de Dados**

### **Backup**
Para criar um backup do banco de dados GLPI:
```bash
docker exec mariadb mysqldump -u root -p glpi > /backup/glpi_database.sql
```

### **Restauração**
Para restaurar o banco de dados:
```bash
docker exec -i mariadb mysql -u root -p glpi < /backup/glpi_database.sql
```

---

## **Comandos Úteis**

### **Verificar os Logs**
```bash
docker-compose logs
```

### **Reiniciar os Containers**
```bash
docker-compose restart
```

### **Remover o Ambiente**
```bash
docker-compose down
```

---

## **Problemas Comuns**

1. **Erro de conexão com o banco de dados:**
   - Verifique as credenciais no arquivo `mariadb.env`.
   - Certifique-se de que o MariaDB foi inicializado corretamente.

2. **Permissões em diretórios de backup:**
   - Ajuste permissões:
     ```bash
     sudo chmod -R 777 /backup
     ```
