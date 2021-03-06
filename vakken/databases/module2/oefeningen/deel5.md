# Deel 5
## Oefening 1
### 1.
We veranderen eerst de delimiter, anders zou de procedure niet kunnen worden afgemaakt.
```sql
delimiter //
```
Vervolgens creëren we de procedure.
```sql
CREATE PROCEDURE aantal_boetes
(IN p_spelersnr INTEGER)
BEGIN
SELECT COUNT(*) FROM boetes WHERE spelersnr = p_spelersnr;
END//
```
We veranderen opnieuw de delimiter naar de standaard ;.
```sql
delimiter ;
```
We roepen onze stored procedure op en geven als parameter-waarde '8' mee.
```sql
CALL aantal_boetes(8);
```
## Oefening 2
### 1.
```sql
delimiter //
create procedure grootste_tabel
(out grootste varchar(50))
begin
case when (select count(*) from spelers) > (select count(*) from boetes) then set grootste = 'Spelers'; else set grootste = 'Boetes';
end case;
end //
```
### 2.
```sql
call grootste_tabel(@grootste);
select @grootste;
```
## Oefening 3
### Versie 1
#### 1.
```sql
delimiter //
CREATE PROCEDURE hoogste_3_boetes
(OUT hoogste_1 INTEGER, OUT hoogste_2 INTEGER, OUT hoogste_3 INTEGER)
BEGIN
	-- begin handler
	DECLARE ERROR VARCHAR(5);
	DECLARE c_hoogste CURSOR FOR
		SELECT
			bedrag
		FROM
			boetes
		ORDER BY
			bedrag DESC;

	DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET ERROR = '23000';
	SET ERROR = '00000';
  -- einde handler

	OPEN c_hoogste;

	FETCH c_hoogste INTO hoogste_1;
	FETCH c_hoogste INTO hoogste_2;
	FETCH c_hoogste INTO hoogste_3;

	CLOSE c_hoogste;
END //

delimiter ;
```
#### 2.
```sql
CALL hoogste_3_boetes(@hoogste_1, @hoogste_2, @hoogste_3);
SELECT @hoogste_1, @hoogste_2, @hoogste_3;
```

### Versie 2
#### 1.
```sql
DELIMITER //

CREATE PROCEDURE HOOGSTE_3_BOETES
(
	out BOETE1 DECIMAL(7,2),
	out BOETE2 DECIMAL(7,2),
    	out BOETE3 DECIMAL(7,2)
)

BEGIN
	DECLARE FOUND BOOLEAN;
   	DECLARE C_BOETES CURSOR FOR
	SELECT	 bedrag
        FROM	 boetes
        ORDER BY bedrag DESC;
        
	DECLARE CONTINUE HANDLER FOR NOT FOUND
		SET FOUND = FALSE;
	SET FOUND = TRUE;
    
    SET BOETE1 = 0;
    SET BOETE2 = 0;
    SET BOETE3 = 0;
    
    OPEN C_BOETES;
    	FETCH C_BOETES INTO BOETE1;
    	IF FOUND THEN
    		FETCH C_BOETES INTO BOETE2;
    	END IF;
    	IF FOUND THEN
		FETCH C_BOETES INTO BOETE3;
	END IF;
    CLOSE C_BOETES;
END //

DELIMITER ;
```

#### 2.
```sql
CALL HOOGSTE_3_BOETES(@BOETE1, @BOETE2, @BOETE3);
SELECT @BOETE1, @BOETE2, @BOETE3;
```
