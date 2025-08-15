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
## 3. CTAS
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
```sql
SELECT 
    b.category,
    SUM(b.rental_price),
    COUNT(*)
FROM 
issued_status as ist
JOIN
books as b
ON b.isbn = ist.issued_book_isbn
GROUP BY 1
```

Tarefa 9. **Listar membros que se registraram nos últimos 180 dias**:
```sql
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

Tarefa 10. **Listar funcionários com o nome do gerente da filial e os detalhes da filial**:

```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id
```

Tarefa 11 11. **Criar uma tabela de livros com preço de aluguel acima de um determinado valor**:
```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;
```

Tarefa 12: **Recuperar a lista de livros que ainda não foram devolvidos**
```sql
SELECT * FROM issued_status as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;
```

## Operações Avançadas em SQL

**Tarefa 13: Identificar membros com livros atrasados**  
Escreva uma consulta para identificar membros que têm livros em atraso (assumir um prazo de devolução de 30 dias). Exiba o ID do membro, o nome do membro, o título do livro, a data de empréstimo e os dias de atraso.

```sql
SELECT 
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    -- rs.return_date,
    CURRENT_DATE - ist.issued_date as over_dues_days
FROM issued_status as ist
JOIN 
members as m
    ON m.member_id = ist.issued_member_id
JOIN 
books as bk
ON bk.isbn = ist.issued_book_isbn
LEFT JOIN 
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE 
    rs.return_date IS NULL
    AND
    (CURRENT_DATE - ist.issued_date) > 30
ORDER BY 1
```


**Tarefa 14: Atualizar status do livro na devolução**  
Escreva uma consulta para atualizar o status dos livros na tabela books para "Yes" quando eles forem devolvidos (com base nos registros da tabela return_status).


```sql

CREATE OR REPLACE PROCEDURE add_return_records(p_return_id VARCHAR(10), p_issued_id VARCHAR(10), p_book_quality VARCHAR(10))
LANGUAGE plpgsql
AS $$

DECLARE
    v_isbn VARCHAR(50);
    v_book_name VARCHAR(80);
    
BEGIN

    INSERT INTO return_status(return_id, issued_id, return_date, book_quality)
    VALUES
    (p_return_id, p_issued_id, CURRENT_DATE, p_book_quality);

    SELECT 
        issued_book_isbn,
        issued_book_name
        INTO
        v_isbn,
        v_book_name
    FROM issued_status
    WHERE issued_id = p_issued_id;

    UPDATE books
    SET status = 'yes'
    WHERE isbn = v_isbn;

    RAISE NOTICE 'Thank you for returning the book: %', v_book_name;
    
END;
$$


-- Testando a função add_return_records

issued_id = IS135
ISBN = WHERE isbn = '978-0-307-58837-1'

SELECT * FROM books
WHERE isbn = '978-0-307-58837-1';

SELECT * FROM issued_status
WHERE issued_book_isbn = '978-0-307-58837-1';

SELECT * FROM return_status
WHERE issued_id = 'IS135';

-- calling function 
CALL add_return_records('RS138', 'IS135', 'Good');

-- calling function 
CALL add_return_records('RS148', 'IS140', 'Good');

```




**Tarefa 15: Relatório de desempenho da filial**  
Crie uma consulta que gere um relatório de desempenho para cada filial, mostrando o número de livros emprestados, o número de livros devolvidos e a receita total gerada com os aluguéis de livros.

```sql
CREATE TABLE branch_reports
AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id) as number_book_issued,
    COUNT(rs.return_id) as number_of_book_return,
    SUM(bk.rental_price) as total_revenue
FROM issued_status as ist
JOIN 
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
JOIN 
books as bk
ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

SELECT * FROM branch_reports;
```

**Tarefa 16: CTAS — Criar uma tabela de membros ativos**  
Use o comando CREATE TABLE AS (CTAS) para criar uma nova tabela chamada active_members contendo os membros que emitiram (emprestaram) pelo menos um livro nos últimos 2 meses.

```sql

CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN (SELECT 
                        DISTINCT issued_member_id   
                    FROM issued_status
                    WHERE 
                        issued_date >= CURRENT_DATE - INTERVAL '2 month'
                    )
;

SELECT * FROM active_members;

```


**Tarefa 17: Encontrar os funcionários que processaram mais empréstimos de livros**  
Escreva uma consulta para encontrar os 3 principais funcionários que processaram o maior número de empréstimos de livros. Exiba o nome do funcionário, o número de livros processados e a filial a que pertencem.

```sql
SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) as no_book_issued
FROM issued_status as ist
JOIN
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2
```

**Tarefa 18: Stored Procedure**
Objetivo:
Criar uma procedure para gerenciar o status dos livros em um sistema de biblioteca.
Descrição:
Escreva uma stored procedure que atualize o status de um livro na biblioteca com base em seu empréstimo. A procedure deve funcionar da seguinte forma:
Receber o book_id como parâmetro de entrada.
Verificar se o livro está disponível (status = 'yes').
Se estiver disponível, registrar o empréstimo e atualizar o status na tabela books para 'no'.
Se não estiver disponível (status = 'no'), retornar uma mensagem de erro indicando que o livro não está disponível no momento.

```sql

CREATE OR REPLACE PROCEDURE issue_book(p_issued_id VARCHAR(10), p_issued_member_id VARCHAR(30), p_issued_book_isbn VARCHAR(30), p_issued_emp_id VARCHAR(10))
LANGUAGE plpgsql
AS $$

DECLARE
-- all the variabable
    v_status VARCHAR(10);

BEGIN
-- all the code
    -- checking if book is available 'yes'
    SELECT 
        status 
        INTO
        v_status
    FROM books
    WHERE isbn = p_issued_book_isbn;

    IF v_status = 'yes' THEN

        INSERT INTO issued_status(issued_id, issued_member_id, issued_date, issued_book_isbn, issued_emp_id)
        VALUES
        (p_issued_id, p_issued_member_id, CURRENT_DATE, p_issued_book_isbn, p_issued_emp_id);

        UPDATE books
            SET status = 'no'
        WHERE isbn = p_issued_book_isbn;

        RAISE NOTICE 'Book records added successfully for book isbn : %', p_issued_book_isbn;


    ELSE
        RAISE NOTICE 'Sorry to inform you the book you have requested is unavailable book_isbn: %', p_issued_book_isbn;
    END IF;
END;
$$

-- Testing The function
SELECT * FROM books;
-- "978-0-553-29698-2" -- yes
-- "978-0-375-41398-8" -- no
SELECT * FROM issued_status;

CALL issue_book('IS155', 'C108', '978-0-553-29698-2', 'E104');
CALL issue_book('IS156', 'C108', '978-0-375-41398-8', 'E104');

SELECT * FROM books
WHERE isbn = '978-0-375-41398-8'

```


## Reports

- **Database Schema**: Estruturas detalhadas das tabelas e relacionamentos.
- **Data Analysis**: Insights sobre categorias de livros, salários de funcionários, tendências de registro de membros e livros emprestados.
- **Summary Reports**: Dados agregados sobre livros de alta demanda e desempenho de funcionários.

## Conclusão

Este projeto demonstra a aplicação de habilidades em SQL na criação e gestão de um sistema de gerenciamento de biblioteca. Inclui configuração do banco de dados, manipulação de dados e consultas avançadas, fornecendo uma base sólida para gestão e análise de dados.
