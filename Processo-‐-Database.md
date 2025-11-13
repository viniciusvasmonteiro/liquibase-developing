# 1. Introdução
Este documento tem como objetivo registrar todo o processo de desenvolvimento do banco de dados da API do 5º semestre de Banco de Dados. O conteúdo abrange tanto o banco relacional quanto o Data Warehouse, destacando suas estruturas, integrações e implementações. Além disso, busca promover a integração entre todos os participantes do projeto dentro da aplicação dos conceitos de DevOps, garantindo rastreabilidade, colaboração e consistência no desenvolvimento. 

# 2. Desenvolvido por


# 3. Modelos do Banco de Dados
### Versão 1
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V1.0/DER_database_V01..png">DER</a>
  <br>
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V1.0/DDL_database_V01.sql">DDL</a>

### Versão 2
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V2.0/DER_database_V02.png">DER</a>
  <br>
  <a href="https://github.com/FatecNeoHorizon/API_5S/blob/main/docs/Data%20Warehouse/V2.0/DDL_database_V02.sql">DDL</a>

# 4. Seção: **DevOps**

## 4.1 Integração do Banco de Dados à Pipeline de DevOps

Iniciamos o projeto utilizando o esquema de *migration* do **Hibernate ORM** como forma de gerenciar as alterações na base de dados.  
No entanto, visando uma melhor integração do banco de dados à pipeline de DevOps, optamos por substituí-lo pelo **Liquibase**.  

Essa mudança proporcionou um controle de versão mais eficiente das tabelas, além de otimizar a rastreabilidade das modificações e facilitar o processo de implantação contínua (*continuous deployment*).

---

## 4.2 Configuração do Liquibase

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
