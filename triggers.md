# Aula Banco de Dados (29/03/2021)

## Triggers - 44

Usa NEW pra indicar que o processo ocorrerá no novo registro  
Existe também o OLD, pra indicar o registro antigo, normalmente utilizado para fins de comparação na operação de UPDATE  

timing = AFTER, BEFORE  
operação = INSERT, UPDATE, DELETE  

### Sintaxe:  
```sql
CREATE TRIGGER nome_trigger timing operação  
ON nome_tabela  
FOR EACH ROW
declarações;  

CREATE TRIGGER tr_desconto BEFORE INSERT  
ON produto  
FOR EACH ROW
SET NEW.Preco_Desconto = (NEW.Preco_Normal * 0.90);  
```  
### Invocando trigger:  
```sql
INSERT INTO produto (Nome_produto, Preco_Normal)  
VALUES ('Monitor', 1.00);  
```
## Referências:  
- https://www.youtube.com/watch?v=JOnkvqUaNOU (MySQL - Triggers - Definição, Sintaxe e Criação - 44)  
- https://www.youtube.com/watch?v=YMPGnavrfjs (Criando Trigger no MySQL em poucos minutos)  
- https://ayltoninacio.com.br/blog/criando-trigger-no-mysql-em-poucos-minutos  
