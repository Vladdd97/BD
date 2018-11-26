# Laboratory Work 6

## Tasks:
### 1. Write an T-SQL instruction, which will populate Adresa_Postala_Profesor column from profesori table with 'mun.Chisinau' value where it is NULL.
```
UPDATE profesori
SET Adresa_Postala_Profesor = 'mun.Chisinau'
WHERE Adresa_Postala_Profesor is NULL;
```

### 2.  Modify the  grupe table for respecting the following requirements:
-   a) 'Cod_Grupa' field should be unique  and NOT NULL.
-   b) To respect the fact that primary key is set on 'Id_Grupa'.

```
ALTER TABLE grupe
ALTER COLUMN Cod_Grupa char(6)  NOT NULL;

ALTER TABLE grupe
ADD UNIQUE (Cod_Grupa);
```

### 3.  Add 2 columns 'Sef_grupa' and 'Prof_Indrumator' to  grupe  table , both columns have to be of type INT. Populate the columns with candidates which follow the following requirements:
-   a) 'Sef_grupa' should has the best mark from group of all types of evaluation at all courses. One student couldn't be the monitor for two groups.
-   b) 'Prof_Indrumator' should teach the maximum courses. If he don't exist it is choosen the Professor with the lowest 'Id_Professor'. One professor couldn't be the 'Indrumator' for two groups.

```
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

```
UPDATE studenti_reusita
SET Nota = studenti_reusita.Nota + 1
where studenti_reusita.Id_Student IN (select Sef_grupa from grupe ) 
and studenti_reusita.Nota < 10 
```