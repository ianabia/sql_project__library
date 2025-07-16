# Sistema de Gerenciamento de Biblioteca usando SQL

## Project Overview

**Título do Projeto**: Sistema de Gerenciamento de Biblioteca
**Database**: `library_project`

Este projeto demonstra a implementação de um Sistema de Gerenciamento de Biblioteca utilizando SQL. Ele inclui a criação e o gerenciamento de tabelas, a realização de operações CRUD e a execução de consultas SQL avançadas. O objetivo é demonstrar habilidades em design, manipulação e consulta de banco de dados.


## Objetivos

1. **Configurar o Banco de Dados do Sistema de Gerenciamento de Biblioteca**: Criar e popular o banco de dados com tabelas para filiais, funcionários, membros, livros, status de empréstimo e status de devolução.
2. **Operações CRUD**: Realizar operações de Criação, Leitura, Atualização e Exclusão nos dados.
3. **CTAS (Create Table As Select)**: Utilizar CTAS para criar novas tabelas com base em resultados de consultas.
4. **Consultas SQL Avançadas:**: Desenvolver consultas complexas para analisar e recuperar dados específicos.


## Estrutura do Projeto

- **Criação da Database**: Criando um banco de dados chamado `library_project`.
- **Criação de Tabelas**: Criação das tabelas branches, employees, members, books, issued status, e return status. Cada tabela inclui colunas relevantes e relacionamentos.

```sql
CREATE DATABASE library_project;

DROP TABLE IF EXISTS branch;
CREATE TABLE branch
(
            branch_id VARCHAR(10) PRIMARY KEY,
            manager_id VARCHAR(10),
            branch_address VARCHAR(30),
            contact_no VARCHAR(15)
);


-- Create table "Employee"

DROP TABLE IF EXISTS employees;
CREATE TABLE employees
(
            emp_id VARCHAR(10) PRIMARY KEY,
            emp_name VARCHAR(30),
            position VARCHAR(30),
            salary DECIMAL(10,2),
            branch_id VARCHAR(10),
            FOREIGN KEY (branch_id) REFERENCES  branch(branch_id)
);


-- Create table "Members"

DROP TABLE IF EXISTS members;
CREATE TABLE members
(
            member_id VARCHAR(10) PRIMARY KEY,
            member_name VARCHAR(30),
            member_address VARCHAR(30),
            reg_date DATE
);



-- Create table "Books"

DROP TABLE IF EXISTS books;
CREATE TABLE books
(
            isbn VARCHAR(50) PRIMARY KEY,
            book_title VARCHAR(80),
            category VARCHAR(30),
            rental_price DECIMAL(10,2),
            status VARCHAR(10),
            author VARCHAR(30),
            publisher VARCHAR(30)
);



-- Create table "IssueStatus"

DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status
(
            issued_id VARCHAR(10) PRIMARY KEY,
            issued_member_id VARCHAR(30),
            issued_book_name VARCHAR(80),
            issued_date DATE,
            issued_book_isbn VARCHAR(50),
            issued_emp_id VARCHAR(10),
            FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
            FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
            FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn) 
);



-- Create table "ReturnStatus"

DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status
(
            return_id VARCHAR(10) PRIMARY KEY,
            issued_id VARCHAR(30),
            return_book_name VARCHAR(80),
            return_date DATE,
            return_book_isbn VARCHAR(50),
            FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
);

```

### 2. Operações CRUD

- **Criar**: Inserindo registros de exemplo na tabela `books`.
- **Ler**: Recuperados e exibidos dados de várias tabelas.
- **Atualizar**: Atualizados registros na tabela `employees`.
- **Excluir**: Removidos registros da tabela `members` conforme necessário.
