---
title: "Validação De CNPJ Em SQL"
date: 2006-12-14T19:15:05+02:00
---

Algumas vezes precisamos mover algumas regras de negócio para dentro do banco de dados por n razões. Certa vez eu estava trabalhando em um mecanismo de importação de dados, que utilizava como fonte arquivos *plain text* e algumas validações de dados eram feitas diretamente na _stored procedure_ que realizava o _insert/update_.

Uma das validações era para verificar se o CNPJ estava correto. A forma mais simples foi criar uma _sql function_ para que pudesse verificar diretamente na tabela temporaria do bulk insert se o número estava correto ou não.

O script é o seguinte:

```sql
CREATE FUNCTION f_VerificaCNPJ (@CNPJ varchar(14))
RETURNS varchar(14)
AS
BEGIN
  DECLARE @CNPJt varchar (14)
  DECLARE @FatorMultiplicacao_fase1 varchar(12)
  DECLARE @FatorMultiplicacao_fase2 varchar(13)

  DECLARE @Resultado int
  DECLARE @cont int

  DECLARE @DigitoVerificador1 int
  DECLARE @DigitoVerificador2 int

  -- Inicializa variaveis
  SET @cont = 0
  SET @Resultado = 0
  SET @DigitoVerificador1 = 0
  SET @DigitoVerificador2 = 0
  SET @CNPJt = substring(@CNPJ, 1, 12)

  -- Fator multiplicativo que deve ser aplicado sobre o CNPJ
  -- para obtenção do código verificador
  -- valor constante!
  SET @FatorMultiplicacao_fase1 = '543298765432' -- #1
  SET @FatorMultiplicacao_fase2 = '6543298765432' -- #2

  -- Soma da multiplicação das colunas do CNPJ com o fator de multiplicacao #1
  WHILE @cont BEGIN
    SET @Resultado = @Resultado + convert(int, substring(@CNPJt, @cont, 1)) * convert(int, substring(@FatorMultiplicacao_fase1, @cont, 1))
    SET @cont = @cont + 1
  END

  -- Verifica o 'resto' da divisao por 11
  SET @Resultado = @Resultado%11

  -- Gera o digito verificador #1
  IF (@Resultado)
  BEGIN
    SET @DigitoVerificador1 = 0
  END
  ELSE
  BEGIN
    SET @DigitoVerificador1 = 11 - @Resultado
  END

  -- Fim da primeira fase:
  SET @CNPJt = @CNPJt + convert(varchar, @DigitoVerificador1)

  -- Reseta o contador
  SET @cont = 0
  SET @Resultado = 0

  -- Soma da multiplicação das colunas do CNPJ com o fator de multiplicacao #1
  WHILE @cont BEGIN
    SET @Resultado = @Resultado + convert(int, substring(@CNPJt, @cont, 1)) * convert(int, substring(@FatorMultiplicacao_fase2, @cont, 1))
    SET @cont = @cont + 1
  END

  -- Verifica o 'resto' da divisao por 11
  SET @Resultado = @Resultado%11

  -- Gera o digito verificador #2
  IF (@Resultado)
  BEGIN
    SET @DigitoVerificador2 = 0
  END
  ELSE
  BEGIN
    SET @DigitoVerificador2 = 11 - @Resultado
  END

  -- Fim da segunda fase:
  SET @CNPJt = @CNPJt + convert(varchar, @DigitoVerificador2)

  -- Verifica digitos verificadores gerados com os do enviados
  IF (substring(@CNPJ, 13, 1) @DigitoVerificador1) OR (substring(@CNPJ, 14, 1) @DigitoVerificador2)
  BEGIN
    RETURN '0'
  END

  RETURN @CNPJ
END
GO
```

Se preferir, [clique aqui](https://gist.github.com/dijeferson/b6eb7e2baaa0199ea6ea#file-valida_cnpj-sql) para clonar o gist.
