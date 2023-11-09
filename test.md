# Zadania lab_04
## Test

Tekst **pogrubiony** lub _kursywa_

```sql
SELECT * FROM osoba;
```

```python
def funkcja();
  print('Test')
```

Polecenie `SELECT` służy wybieraniu danych z bazy

Aby zmusić zmianę linijki wpisać dwie spacje

Lista ponumerowana
1. Punkt pierwszy
2. Punkt drugi  
  2.1. Punkt dwa jeden

Aby zrobić wcięcie, przed punktem wstawić dwie spacje

Lista wypunktowana
* Punkt pierwszy
* Punkt drugi
  * Punkt dwa jeden

## Zadanie 1

```sql
create table postac ( id_postaci int auto_increment primary key, nazwa varchar(40), rodzaj enum('wiking','ptak','kobieta'), data_ur date, wiek int unsigned);

create table walizka( id_walizki int primary key auto_increment, pojemnosc int unsigned, kolor enum('rozowy','czerwony','reczowy','zolty'), id_wlasciciela int, foreign key(id_wlasciciela) references postac(id_postaci) on delete cascade);
```
