# Laboratory Work 6

## Tasks:
### 1. Write an T-SQL instruction, which will populate Adresa_Postala_Profesor column from profesori table with 'mun.Chisinau' value where it is NULL.
```sql
UPDATE profesori
SET Adresa_Postala_Profesor = 'mun.Chisinau'
WHERE Adresa_Postala_Profesor is NULL;
```

### 2.  Modify the  grupe table for respecting the following requirements:
-   a) 'Cod_Grupa' field should be unique  and NOT NULL.
-   b) To respect the fact that primary key is set on 'Id_Grupa'.

```sql
ALTER TABLE grupe
ALTER COLUMN Cod_Grupa char(6)  NOT NULL;

ALTER TABLE grupe
ADD UNIQUE (Cod_Grupa);
```

### 3.  Add 2 columns 'Sef_grupa' and 'Prof_Indrumator' to  grupe  table , both columns have to be of type INT. Populate the columns with candidates which follow the following requirements:
-   a) 'Sef_grupa' should has the best mark from group of all types of evaluation at all courses. One student couldn't be the monitor for two groups.
-   b) 'Prof_Indrumator' should teach the maximum courses. If he don't exist it is choosen the Professor with the lowest 'Id_Professor'. One professor couldn't be the 'Indrumator' for two groups.

```sql
ALTER TABLE grupe
ADD Sef_grupa INT,
Prof_Idrumator INT;

a)
update
   grupe 
set
   Sef_grupa = 
   (
      SELECT
         TOP(1) id_student AS student 
      FROM
         studenti_reusita 
      where
         Id_Grupa = grupe.Id_Grupa 			--100 150 127
      GROUP BY
         id_student 
      order by
         Avg(Nota) desc 
   )
)

b)
update
   grupe 
set
   Prof_Idrumator = 
   (
      select
         TOP(1) Id_Profesor 
      from
         studenti_reusita 
      where
         studenti_reusita.Id_Grupa = grupe.Id_Grupa 			--101 104 100
      group by
         Id_Profesor,
         Id_Grupa 
      order by
         count(distinct Id_Disciplina) desc,
         Id_Profesor asc 
   )
```


### 4.  Write the T-SQL instruction which will increase the 'Nota' with 1 unit. The maximum 'Nota' (10) couldn't be increased.

```sql
UPDATE studenti_reusita
SET Nota = studenti_reusita.Nota + 1
where studenti_reusita.Id_Student IN (select Sef_grupa from grupe ) 
and studenti_reusita.Nota < 10 
```

### 5. Create the  profesori_new  table which will include the following columns: Id_Profesor, Nume_Profesor, Prenume_Profesor, Localitate, Adresa_1, Adresa_2.
    

-   a) Id_Profesor column should be defined as primari key and should include an Clustered Index.   
-   b) Localitate column should has the DEFAULT = 'mun Chisinau' property.   
-   c) To insert the data from  **profesori**  table to  **profesori_new**  table following the criteria: Id_Profesor = Id_Profesor
    
    Nume_Profesor = Nume_Profesor
    Prenume_Profesor = Prenume_Profesor
    Localitate = Name of the locality
    Adresa_1 = street name
    Adresa_2 = house number or apartment.

```sql
CREATE TABLE profesori_new (
    Id_Profesor int IDENTITY (1,1) NOT NULL, 
    Nume_Profesor varchar(255),
    Prenume_Profesor varchar(255),
    Localitate varchar(255) DEFAULT 'mun.Chisinau',
	Adresa_1 varchar(255),
	Adresa_2 varchar(255),
	CONSTRAINT PK_TransactionHistoryArchive_TransactionID PRIMARY KEY CLUSTERED (Id_Profesor) 
);
	
	
	CREATE TABLE profesori_new (
    Id_Profesor int IDENTITY (1,1) NOT NULL, 
    Nume_Profesor varchar(255),
    Prenume_Profesor varchar(255),
    Localitate varchar(255) DEFAULT 'mun.Chisinau',
	Adresa_1 varchar(255),
	Adresa_2 varchar(255),
	PRIMARY KEY CLUSTERED (Id_Profesor) 
);

INSERT INTO profesori_new 
            (
             nume_profesor, 
             prenume_profesor, 
             localitate, 
             adresa_1, 
             adresa_2) 
SELECT  
       nume_profesor, 
       prenume_profesor, 
       CASE 
         WHEN Charindex('str.', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 1, 
         Charindex('str.', adresa_postala_profesor) 
         - 3) 
         WHEN Charindex('bd.', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 1, 
         Charindex('bd.', adresa_postala_profesor 
         ) 
         - 3) 
         WHEN Charindex('mun.', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 1, Len(adresa_postala_profesor)) 
       END, 
       CASE 
         WHEN Charindex('str.', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 
         Charindex('str.', adresa_postala_profesor), 
         Patindex('%[0-9]%', adresa_postala_profesor) 
         - 
         Charindex 
         ('str.', 
         adresa_postala_profesor 
                    ) - 2) 
         WHEN Charindex('bd.', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 
         Charindex('bd.', adresa_postala_profesor), 
         Patindex('%[0-9]%', adresa_postala_profesor) 
         - 
         Charindex 
         ('bd.', 
         adresa_postala_profesor 
                     ) - 2) 
       END, 
       CASE 
         WHEN Patindex('%[0-9]%', adresa_postala_profesor) > 0 THEN 
         Substring(adresa_postala_profesor, 
         Patindex('%[0-9]%', adresa_postala_profesor 
         ), 
         Len( 
       adresa_postala_profesor) - Patindex('%[0-9]%', adresa_postala_profesor) + 
         1) 
       END 
FROM   profesori 

```
### 6. To insert in the 'orarul' table the data for the 'Grupa = CIB171'(Id_Grupa = 1) for Monday. All courses will be teaching in the B block, following the criteria :

(Id_Disciplina =107, Id_Profesor = 101, Ora = '08:00', Auditoriu = 202)
(Id_Disciplina =108, Id_Profesor = 101, Ora = '11:30', Auditoriu = 501)
(Id_Disciplina =109, Id_Profesor = 117, Ora = '13:00', Auditoriu = 501)


```sql

CREATE TABLE orarul 
  ( 
     id_disciplina INT, 
     id_profesor   INT, 
     id_grupa      INT, 
     ora           TIME, 
     auditoriu     INT, 
     bloc          CHAR(1) DEFAULT('B'), 
     zi            CHAR(10) 
     PRIMARY KEY(id_disciplina, id_profesor, id_grupa) 
  ); 

INSERT INTO
   orarul (id_disciplina, id_profesor, id_grupa, ora, auditoriu, bloc, zi) 
VALUES
   (
      107, 101, 1, '08:00', 202, 'B', 'Luni'
   )
, 
   (
      108, 101, 1, '11:30', 501, 'B', 'Luni'
   )
, 
   (
      119, 117, 1, '13:00', 501, 'B', 'Luni'
   )
```
### 7.  Write the T-SQL instructions for populating the 'orarul' table for 'Group = INF171' ,Monday. Should be uused the SELECT instruction for populating with the following data :

(Ora = '08:00' , Disciplina = 'Structuri de date si algoritmi', Profesor = 'Bivol Ion')
(Ora = '11:30' , Disciplina = 'Programe aplicative', Profesor = 'Mircea Sorin')
(Ora = '13:00' , Disciplina = 'Baze de date', Profesor = 'Micu Elena')

```sql
INSERT INTO
   orarul (Id_Disciplina, Id_Profesor, Id_Grupa, Ora, Auditoriu, Bloc, Zi) 
VALUES
   (
(
      SELECT
         Id_Disciplina 
      FROM
         discipline 
      WHERE
         Disciplina = 'Structuri de date si algoritmi'),
         (
            SELECT
               Id_Profesor 
            FROM
               profesori 
            WHERE
               Nume_Profesor = 'Bivol' 
               AND Prenume_Profesor = 'Ion'
         )
,
         (
            SELECT
               Id_Grupa 
            FROM
               grupe 
            WHERE
               Cod_Grupa = 'INF171'
         )
,
         '08:00',
         502,
         'B',
         'Luni'
   )
,
   (
(
      SELECT
         Id_Disciplina 
      FROM
         discipline 
      WHERE
         Disciplina = 'Programe aplicative'),
         (
            SELECT
               Id_Profesor 
            FROM
               profesori 
            WHERE
               Nume_Profesor = 'Mircea' 
               AND Prenume_Profesor = 'Sorin'
         )
,
         (
            SELECT
               Id_Grupa 
            FROM
               grupe 
            WHERE
               Cod_Grupa = 'INF171'
         )
,
         '11:30',
         502,
         'B',
         'Luni'
   )
,
   (
(
      SELECT
         Id_Disciplina 
      FROM
         discipline 
      WHERE
         Disciplina = 'Baze de date'),
         (
            SELECT
               Id_Profesor 
            FROM
               profesori 
            WHERE
               Nume_Profesor = 'Micu' 
               AND Prenume_Profesor = 'Elena'
         )
,
         (
            SELECT
               Id_Grupa 
            FROM
               grupe 
            WHERE
               Cod_Grupa = 'INF171'
         )
,
         '13:00',
         502,
         'B',
         'Luni'
   ) 
```

### 8....


```sql

CREATE NONCLUSTERED INDEX lab6_ex8 ON studenti.studenti(Nume_Student)
on userdatagroup1


CREATE NONCLUSTERED INDEX lab6_ex8_2 ON studenti.studenti_reusita(Nota)
on userdatagroup1
```
