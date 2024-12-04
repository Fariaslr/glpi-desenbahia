Segue um exemplo de **README** para documentar o seu ambiente **Docker Compose** com MariaDB e GLPI. Você pode ajustar conforme as necessidades do seu projeto.

---

# **Docker Compose para GLPI e MariaDB**

Este repositório contém um ambiente configurado para rodar o GLPI integrado ao MariaDB utilizando **Docker Compose**.

## **Estrutura do Projeto**

- **`docker-compose.yml`**: Arquivo principal para configurar os serviços Docker.
- **`mariadb.env`**: Arquivo com variáveis de ambiente para configuração do MariaDB.

---

## **Serviços Configurados**

### **1. MariaDB**
Banco de dados relacional utilizado pelo GLPI.

- **Imagem:** `mariadb:latest`
- **Porta:** `3306` (exposta para conexão externa)
- **Credenciais:** Configuradas no arquivo `mariadb.env`.

### **2. GLPI**
Sistema de gerenciamento de serviços de TI.

- **Imagem:** `glpi/glpi:latest`
- **Porta:** `80` (exposta para acesso via navegador)

---

## **Pré-requisitos**

Certifique-se de que o Docker e o Docker Compose estão instalados no sistema:

### **Instalar Docker**
```bash
sudo apt update
sudo apt install docker.io
```

### **Instalar Docker Compose**
```bash
sudo apt install docker-compose
```

---

## **Configuração do Projeto**

### **1. Clonar o Repositório**
```bash
git clone <URL_DO_REPOSITORIO>
cd docker-compose
```

### **2. Configurar Variáveis de Ambiente**

Crie ou edite o arquivo `mariadb.env` com as seguintes configurações:

```env
MARIADB_ROOT_PASSWORD=root_password
MARIADB_DATABASE=glpi
MARIADB_USER=root
MARIADB_PASSWORD=root_password
```

### **3. Arquivo `docker-compose.yml`**
Certifique-se de que o `docker-compose.yml` está configurado corretamente. Um exemplo básico:

```yaml
services:
#MariaDB Container
  mariadb:
    image: mariadb:10.7
    container_name: mariadb
    hostname: mariadb
    env_file:
      - ./mariadb.env
    networks:
      - glpi-network
    restart: always
 
#glpi/glpi conta de administrador,
#tech/tech conta de técnico,
#normal/normal conta «normal»,
#post-only/postonly conta somente pós-publicação.
 
#GLPI Container
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

---

## **Iniciar os Serviços**

Para iniciar o ambiente, execute:

```bash
docker-compose up -d
```

---

## **Acessar o GLPI**

Após iniciar os containers:

1. Abra o navegador e acesse: [http://localhost](http://localhost)
2. Siga as instruções de instalação do GLPI.
3. Utilize as credenciais configuradas no arquivo `mariadb.env` para conectar ao banco.

---

## **Comandos Úteis**

### **Verificar o Status dos Containers**
```bash
docker-compose ps
```

### **Parar os Containers**
```bash
docker-compose down
```

### **Acessar o Container MariaDB**
```bash
docker exec -it mariadb bash
```

### **Acessar o Banco de Dados MariaDB**
```bash
docker exec -it mariadb mysql -u root -p
```

---

## **Manutenção**

### **Backup do Banco de Dados**
Para realizar o backup do banco de dados:
```bash
docker exec mariadb mysqldump -u root -p glpi > backup.sql
```

### **Restauração do Banco de Dados**
Para restaurar o backup:
```bash
docker exec -i mariadb mysql -u root -p glpi < backup.sql
```

---

## **Problemas Comuns**

1. **Erro de conexão com o banco de dados:**
   - Certifique-se de que o arquivo `mariadb.env` está configurado corretamente.
   - Verifique o mapeamento de portas no `docker-compose.yml`.

2. **Containers não iniciam:**
   - Verifique os logs com:
     ```bash
     docker-compose logs
     ```

3. **Permissões em volumes:**
   - Ajuste permissões dos volumes para evitar erros:
     ```bash
     sudo chmod -R 777 mariadb_data glpi_data
     ```