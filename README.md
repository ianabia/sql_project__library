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


**Tarefa 1. Criar um Novo Registro de Livro -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co'**
```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```
**Tarefa 2: Atualizar o Endereço de um Membro Existente**
```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```
**Tarefa 3: Excluir um registro da tabela de status de emissão — Objetivo: Excluir o registro com issued_id = 'IS121' da tabela issued_status**
```sql**
DELETE FROM issued_status
WHERE   issued_id =   'IS121';
```
**Tarefa 4: Excluir um Registro da Tabela de Status de Empréstimo — Objetivo: Excluir o registro com issued_id = 'IS121' da tabela issued_status**
```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101'
```
**Tarefa 5: Listar Membros que Emitiram Mais de um Livro — Objetivo: Usar GROUP BY para encontrar membros que emitiram mais de um livro**
```sql
SELECT 
	issued_emp_id
	-- COUNT(issued_id) as total_book_issued
FROM issued_status
GROUP BY issued_emp_id
HAVING COUNT(issued_id) > 1;
```
**CTAS
**Tarefa 6: Criar Tabelas Resumo: Utilizar CTAS para gerar novas tabelas com base nos resultados de consultas — cada livro e o total de vezes que foi emitido (book_issued_cnt)**
```sql
CREATE TABLE book_cnts
AS
SELECT
	b.isbn,
	b.book_title,
	COUNT(ist.issued_id) as no_issued
FROM books as b
JOIN
issued_status as ist
ON ist.issued_book_isbn = b.isbn
GROUP BY 1, 2;

SELECT * FROM book_cnts;
```
**Tarefa 7. Recuperar Todos os Livros de uma Categoria Específica**
```sql
SELECT * FROM books
WHERE category = 'Classic';
```
**Tarefa 8: Encontrar a Receita Total de Aluguéis por Categoria**
