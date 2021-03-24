# Aula Banco de Dados (22/03/2021)

## Rotinas Armazenadas - Funções (CREATE FUNCTION) - 33

### Sintaxe:  
```
CREATE FUNCTION nome_função(parametros)  
RETURNS tipo_dados   
código_da_funcao;  

CREATE FUNCTION fn_teste(a DECIMAL(10,2), b INT)  
RETURNS INT   
RETURN a*b;  
```  
### Invocando função:  
```
SELECT nome_função(parametros);  

SELECT fn_teste(2.5, 4) AS Resultado;  
```
### Deletando função:  
```
DROP FUNCTION nome_função;  

DROP FUNCTION fn_teste;  
```  

## Procedimentos Armazenados - Básico (STORED PROCEDURES) - 34

### Sintaxe:  
```
CREATE PROCEDURE nome_procedimento (parametros) declarações;  

CREATE PROCEDURE verPreço (varLivro smallint)  
SELECT CONCAT('O preço é ', Preco_Livro) AS Preço  
FROM tbl_Livro  
WHERE ID_Livro = varLivro;  
```  
### Invocando procedimento:  
```
CALL nome_procedimento(parametros);  

CALL verPreço(3);  
```
### Deletando procedimento:  
```
DROP PROCEDURE nome_procedimento;  

DROP PROCEDURE verPreço;  
```  

## Blocos BEGIN...END - 35

Utiliza um DELIMITER pra ele não executar uma query na criação da função/procedimento ao ver o delimitador padrão ;  

### Sintaxe Função e Procedimento:  
```
DELIMITER $$  
CREATE FUNCTION aumenta_preco(preco DECIMAL(10, 2), taxa DECIMAL(10, 2))  
BEGIN  
	RETURN preco + preco * taxa /100;  
END$$  
DELIMITER ;  


DELIMITER //  
CREATE PROCEDURE verPreço(varLivro smallint)  
BEGIN  
	SELECT CONCAT('O preço é ', Preco_Livro) AS Preço  
	FROM tbl_Livro  
	WHERE ID_Livro = varLivro;  
	SELECT 'Procedimento executado com sucesso!';
END//  
DELIMITER ;  
```  
### Invocando Função e Procedimento:  
```
SELECT aumenta_preco(50.00, 10.00) AS Resultado;  

CALL verPreço(3);  
```
### Deletando Função e Procedimento:  
```
DROP PROCEDURE nome_procedimento;  

DROP PROCEDURE verPreço;  
```  

## Parâmetros IN, OUT e INOUT - 36

IN serve pra passar valores pra serem utilizados no procedimento  
OUT serve pra receber um valor na consulta e retornar  
INOUT serve pra ambos  

Obs: se não especifica nada é IN por default  

### Sintaxe:  
```
MODO nome_param tipo_param(tamanho_param)  

-- Exemplo IN:  
DELIMITER //  
CREATE PROCEDURE aumenta_preco(IN codigo INT, taxa DECIMAL(10,2))  
BEGIN  
	UPDATE tbl_Livro  
	SET Preco_Livro = tbl_Livro.Preco_Livro + tbl_Livro.Preco_Livro * taxa / 100  
	WHERE ID_Livro = codigo;  
END//  
DELIMITER ;  

-- Exemplo OUT:  
DELIMITER //  
CREATE PROCEDURE teste_out(IN id INT, OUT livro VARCHAR(50))  
BEGIN  
	SELECT Nome_Livro  
	INTO livro  
	FROM tbl_Livro  
	WHERE ID_Livro = id;  
END//  
DELIMITER ;  
```  
### Invocando:  
```
SET @livro = 4;  
SET @aumento = 20;  
CALL aumenta_preco(@livro, @aumento);  

CALL teste_out(3, @livro);  
SELECT @livro;  
```  

## Variáveis locais e escopo - 37

### Sintaxe:  
```
DECLARE nome_var1 tipo, nome_var2 tipo [DEFAULT valor_padrão]  

DELIMITER //  
CREATE FUNCTION calcula_desconto(livro INT, desconto DECIMAL(10,2))  
RETURNS DECIMAL(10,2)  
BEGIN  
	DECLARE preco DECIMAL(10,2);  
	SELECT Preco_Livro FROM tbl_Livro  
	WHERE ID_Livro = livro INTO preco;  
	RETURN preco - desconto;  
END//  
DELIMITER ;  

```  
### Invocando:  
```
SELECT calcula_desconto(4, 10.00);  
```  

## Blocos Condicionais IF - THEN - ELSE e CASE - 38

### Sintaxe:  
```
IF condição THEN lista_declarações  
	[ELSEIF condição THEN lista_declarações]  
	...  
	[ELSE lista_declarações]  
END IF;  

CASE valor_referência  
	WHEN valor_comparado THEN  
lista_declarações  
	WHEN valor_comparado THEN  
lista_declarações  
	...  
ELSE lista_declarações  
END CASE;  
```  
### Invocando:  
```
SELECT função(4, 10.00);  
```  

## Estruturas de Repetição - LOOP - 40

### Sintaxe:  
```
[<rótulo>:] LOOP  
	declarações  
END LOOP [<rótulo>];  

DELIMITER //  
CREATE PROCEDURE acumula(limite INT)  
main: BEGIN  
	DECLARE contador INT DEFAULT 0;  
	DECLARE soma INT DEFAULT 0;  
	IF limite < 1 THEN  
		SELECT "O valor deve ser maior que zero." AS Erro;  
		LEAVE main;  
	loop_teste: LOOP  
		SET contador = contador + 1;  
		SET soma = soma + contador;  
		IF contador >= limite THEN  
			LEAVE loop_teste;  
		END IF;  
	END LOOP loop_teste;  
	SELECT soma;  
END//  
DELIMITER ;  
```  
### Invocando:  
```
CALL acumula(10);  
```  

## Estruturas de Repetição - REPEAT - 41

Diferença pro LOOP é que ele faz uma verificação utilizando o UNTIL pra continuar se UNTIL for verdadeiro ele encerra.  

### Sintaxe:  
```
[<rótulo>:] REPEAT  
	declarações  
UNTIL condição  
END REPEAT [<rótulo>];  

DELIMITER //  
CREATE PROCEDURE acumula_repita(limite TINYINT UNSIGNED)  
main: BEGIN  
	DECLARE contador INT DEFAULT 0;  
	DECLARE soma INT DEFAULT 0;  
	IF limite < 1 THEN  
		SELECT "O valor deve ser maior que zero." AS Erro;  
		LEAVE main;  
	END IF;  
	REPEAT
		SET contador = contador + 1;  
		SET soma = soma + contador;  
	UNTIL contador >= limite  
	END REPEAT;  
	SELECT soma;  
END//  
DELIMITER ;  
```  
### Invocando:  
```
CALL acumula_repita(10);  
```  

## Estruturas de Repetição - WHILE - 42

### Sintaxe:  
```
[<rótulo>:] WHILE condição DO  
	declarações  
END WHILE [<rótulo>];  

DELIMITER //  
CREATE PROCEDURE acumula_while(limite TINYINT UNSIGNED)  
main: BEGIN  
	DECLARE contador INT DEFAULT 0;  
	DECLARE soma INT DEFAULT 0;  
	WHILE contador < limite DO  
		SET contador = contador + 1;  
		SET soma = soma + contador;  
	END WHILE;  
	SELECT soma;  
END//  
DELIMITER ;  
```  
### Invocando:  
```
CALL acumula_while(10);  
```  

## Estruturas de Repetição - ITERATE - 43

Declaração que vai dentro dos loops pra retornar ao início do loop novamente  

### Sintaxe:  
```
ITERATE nome_rótulo_while_repeat_loop;  
```  
