# 1. Introdução

Este documento apresenta a função de Database no ambiente DevOps da equipe Neo Horizon, assegurando consistência, rastreabilidade e manutenção das estruturas do Data Warehouse (DW) e do banco relacional.
O objetivo é detalhar as decisões técnicas, ferramentas e processos de automação utilizados no controle de versões do banco.

# 2. Seção: DevOps
## 2.1 Visão Geral

O DevOps foi implementado para garantir controle de mudanças, padronização e integração contínua entre o banco de dados e as demais camadas do sistema (backend, frontend e ETL).

### Conceito aplicado

- Controle de versões do BD: As alterações estruturais são registradas e aplicadas automaticamente por meio do Liquibase.

- Integração à pipeline CI/CD: O Liquibase foi incorporado ao fluxo de build do Maven, garantindo consistência entre os ambientes.

### Objetivo

Permitir rastreabilidade, auditoria e automação das migrações do banco de dados sem comprometer a integridade dos demais componentes.

### Implementação

- Sprint 1: Controle de versão inicial via Hibernate ORM.

- Sprint 2: Transição para Liquibase para gerenciamento de migrações automatizadas.

- Sprint 3: Integração do Liquibase à pipeline CI/CD (Maven).

### Desafios e soluções

- **Desafio:** Falta de rastreabilidade nas alterações de schema.
<br>
**Solução:** Adoção do Liquibase para versionamento centralizado.

- **Desafio**: Integração com o pipeline automatizado.
<br>**Solução:** Configuração de execução via Maven (liquibase:update).

### Justificativas

O Liquibase foi escolhido por oferecer controle granular de mudanças, suporte nativo a PostgreSQL e integração direta com Maven e ferramentas DevOps.

### Decisões de arquitetura

- Banco de dados PostgreSQL.

- Versionamento de schema via Liquibase YAML.

- Execução automatizada em pipeline CI/CD.

# 3. Seção: Produto
### Regras de Desenvolvimento **(ajustar com equipe)**

- Todas as alterações de banco de dados devem ser versionadas.

- Utilizar convenção vX.Y.sql para versionamento manual e changelogs YAML para o Liquibase.

### Solução do Produto

O Data Warehouse consolida informações de múltiplas origens, promovendo performance, consistência e rastreabilidade.

### Diagrama de Arquitetura e Compatibilidades

| Versão V2.0 | Data do deploy: 05/11/2025

DER V1.0






# 2. Modelos do Banco de Dados
### Versão 1
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V1.0/DER_database_V01..png">DER</a>
  <br>
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V1.0/DDL_database_V01.sql">DDL</a>

### Versão 2
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V2.0/DER_database_V02.png">DER</a>
  <br>
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V2.0/DDL_database_V02.sql">DDL</a>

# 3. Seção: **DevOps**

## 3.1 Integração do Banco de Dados à Pipeline de DevOps

Iniciamos o projeto utilizando o esquema de *migration* do **Hibernate ORM** como forma de gerenciar as alterações na base de dados.  
No entanto, visando uma melhor integração do banco de dados à pipeline de DevOps, optamos por substituí-lo pelo **Liquibase**.  

Essa mudança proporcionou um controle de versão mais eficiente das tabelas, além de otimizar a rastreabilidade das modificações e facilitar o processo de implantação contínua (*continuous deployment*).

---

## 3.2 Configuração do Liquibase

Em um primeiro momento, é necessário adicionar a dependência do Liquibase no arquivo `pom.xml` do projeto:

```xml
<dependency>
  <groupId>org.liquibase</groupId>
  <artifactId>liquibase-core</artifactId>
</dependency>
```
Em seguida, adicione o plugin logo abaixo:
```xml
<plugin>
  <groupId>org.liquibase</groupId>
  <artifactId>liquibase-maven-plugin</artifactId>
  <version>4.31.1</version>
  <configuration>
    <propertyFile>liquibase.properties</propertyFile>
  </configuration>
</plugin>
```

Após adicionar o plugin, crie a pasta resources/db/changelog, onde serão criados dois arquivos principais:

1º – db.changelog-001-create-tables.yaml
Define a estrutura inicial da base de dados.

Exemplo:
```yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: vinicius
      changes:
        - createTable:
            tableName: usuario
            columns:
              - column:
                  name: id
                  type: BIGSERIAL
                  constraints:
                    primaryKey: true
              - column:
                  name: nome
                  type: VARCHAR(100)
                  constraints:
                    nullable: false
              - column:
                  name: email
                  type: VARCHAR(100)
                  constraints:
                    unique: true
```

2º – db.changelog-master.yaml
Arquivo principal que referencia o changelog criado anteriormente.

Exemplo:
```yaml
databaseChangeLog:
  - include:
      file: db/changelog/db.changelog-001-create-tables.yaml
```

Em seguida, crie o arquivo liquibase.properties, onde serão definidas as credenciais de conexão com o banco de dados:

```properties
url=jdbc:postgresql://localhost:5432/minha_base
username=postgres
password=admin
driver=org.postgresql.Driver
changeLogFile=db/changelog/db.changelog-master.yaml
```

Por fim, adicione à pipeline de CI do projeto o comando responsável por executar o Liquibase:

```yaml
- name: Build and Run Liquibase
  run: mvn -B clean compile liquibase:update
```

Esse comando executará os changeSets definidos no arquivo db.changelog-001-create-tables.yaml, aplicando as alterações configuradas no banco de dados de forma automatizada.


##  **Conceito aplicado**  
  - **Controle de versões do DW**: As modificações realizadas nas estruturas do DW são registradas manualmente, permitindo rastreabilidade e acompanhamento da evolução ao longo do tempo.

  - **Integração e compatibilidade**: A documentação das versões assegura que o DW permaneça alinhado com os demais sistemas — front-end, back-end e fluxos de ETL — evitando conflitos e garantindo consistência no ambiente.

### **Objetivo**
Controlar as mudanças no DW com precisão, permitindo reversão, rastreabilidade e auditoria de versões, sem comprometer outros componentes da aplicação.

### **Implementação**

**Sprint 1:** Scripts aplicados manualmente, com gerenciamento via branches.  
**Sprint 2:** -  
**Sprint 3:** -

### **Desafios e soluções:**

- **Desafio:** - 
  - **Solução:** -

- **Desafio:** - 
  - **Solução:** -

### Justificativas

...

### Decisões de arquitetura
...

---

<a id="secao3"></a>

# 3. Seção: **Produto**

### Regras de Desenvolvimento

- Todas as alterações devem ser versionadas obrigatoriamente.
- Utilizando uma convenção de nomes padronizada, como `v1.0.sql`.

### Solução do Produto

O Data Warehouse foi estruturado para consolidar informações de diversas origens do sistema, convertendo-as em modelos de análise mais eficientes, com foco no desempenho e na rastreabilidade.

### Diagrama de arquitetura e compatibilidades

> Versão V1.0 | Data do deploy: 28/09/2025

* <a href="https://github.com/FatecNeoHorizon/API_5S/blob/sprint-1/docs/Data%20Warehouse/V1.0/BancoModelagem.png">Diagrama Estrela</a>
* <a href="https://github.com/FatecNeoHorizon/API_5S/blob/sprint-1/docs/script_sql.txt">Script SQL</a>
* <a href="https://github.com/FatecNeoHorizon/API_5S/blob/sprint-1/docs/Data%20Warehouse/V1.0/script_tabelas.txt">Insert SQL</a>
* <a href="https://github.com/FatecNeoHorizon/API_5S/tree/sprint-1/docs/wireframe">Wireframe</a>
* <a href="https://github.com/FatecNeoHorizon/API_5SEM_FRONTEND/tree/sprint-1">Front-end</a>
* <a href="https://github.com/FatecNeoHorizon/API_5SEM_BACKEND/tree/sprint-1">Back-end</a>

---

<a id="secao3"></a>

# 4. Seção: **Resultados**

### Resultados
