# Laboratory Work 5

## Tasks
### 1. Fill in the following code to display the biggest number of the three numbers shown.
<img  align="center" width="450" height="350" src="screenshots/lab5_ex1_cond.PNG">

#### Task implementation: 
```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;
SET @N1 = 60 * RAND()
SET @N2 = 60 * RAND()
SET @N3 = 60 * RAND()

IF @N1 > @N2  and @N1 > @N3 
	BEGIN
		SELECT @MAI_MARE = @N1
	END
IF @N2 > @N1  and @N2 > @N3 
	BEGIN
		SELECT @MAI_MARE = @N2
	END
IF @N3 > @N1  and @N3 > @N2 
	BEGIN
		SELECT @MAI_MARE = @N3
	END
PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2));
```
#### Result : 
<img  align="center" width="250" height="150" src="screenshots/lab5_ex1_result.PNG">


### 2. Display the first 10 data (Name, Surname of student) in function of Grade value (except 6 and 8) of student on first evaluation at Database , using IF-ELSE statement. To use variables.
#### Task implementation: 
```sql
DECLARE @nota1 as int , @nota2 as int , @numberOfData as int;
SET @nota1 = 6;
SET @nota2 = 8;
SET @numberOfData = 10;
select top (@numberOfData) s.Nume_Student, s.Prenume_Student from discipline d 
inner join studenti_reusita sr on d.Id_Disciplina = sr.Id_Disciplina
inner join studenti s on s.Id_Student = sr.Id_Student
where Disciplina = 'Baze de date' and Tip_evaluare = 'Testul 1' and Nota in ( iif(Nota <> @nota1 and Nota <>@nota2 , Nota , null));
```
#### Result : 
<img  align="center" width="350" height="200" src="screenshots/lab5_ex2_result.PNG">


### 3. Solve the same task,1, using CASE statement.
#### Task implementation: : 
```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;
SET @N1 = 60 * RAND()
SET @N2 = 60 * RAND()
SET @N3 = 60 * RAND()
SELECT @MAI_MARE = 
	CASE 
		WHEN @N1 > @N2  and @N1 > @N3  THEN  @N1
		WHEN @N2 > @N1  and @N2 > @N3  THEN  @N2
		WHEN @N3 > @N1  and @N3 > @N2  THEN  @N3
	END 
PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2));
```
#### Result : 
<img  align="center" width="250" height="150" src="screenshots/lab5_ex3_result.PNG">


### 4. Modify the exercises from task 1 and 2 for including error processing with TRY-CATCH and RAISERROR.
#### Task implementation: : 
```sql
-- task1
BEGIN TRY
	DECLARE @N1 INT, @N2 INT, @N3 INT;
	DECLARE @MAI_MARE INT;
	SET @N1 = 60 * RAND()
	SET @N2 = 60 * RAND()
	SET @N3 = 60 * RAND()
	--SET @N1 = 1/0
	SELECT @MAI_MARE = 
		CASE 
			WHEN @N1 > @N2  and @N1 > @N3  THEN  @N1
			WHEN @N2 > @N1  and @N2 > @N3  THEN  @N2
			WHEN @N3 > @N1  and @N3 > @N2  THEN  @N3
		END 
	PRINT @N1;
	PRINT @N2;
	PRINT @N3;
	PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2));
END TRY
BEGIN CATCH
	DECLARE @EM as varchar(4000) , @ESEV as int , @EST as int;
	SELECT @EM = ERROR_MESSAGE() , @ESEV = ERROR_SEVERITY() , @EST = ERROR_STATE();
	raiserror(@EM,@ESEV,@EST);
END CATCH

-- task2
BEGIN TRY
DECLARE @nota1 as int , @nota2 as int , @numberOfData as int;
SET @nota1 = 6;
SET @nota2 = 8;
SET @numberOfData = 10;
select top (@numberOfData) s.Nume_Student, s.Prenume_Student from discipline d 
inner join studenti_reusita sr on d.Id_Disciplina = sr.Id_Disciplina
inner join studenti s on s.Id_Student = sr.Id_Student
where Disciplina = 'Baze de date' and Tip_evaluare = 'Testul 1' and Nota in ( iif(Nota <> @nota1 and Nota <>@nota2 , Nota , null));
END TRY
BEGIN CATCH
	DECLARE @EM as varchar(4000) , @ESEV as int , @EST as int;
	SELECT @EM = ERROR_MESSAGE() , @ESEV = ERROR_SEVERITY() , @EST = ERROR_STATE();
	raiserror(@EM,@ESEV,@EST);
END CATCH
```
#### Result : 
<img  align="center" width="350" height="150" src="screenshots/lab5_ex4_result.PNG">

