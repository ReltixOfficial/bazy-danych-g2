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

## Lab_04 Zadania 3, 4 i 5

```sql
use infs_gryskar;
create table izba(
adres_budynku varchar(50) not null,
nazwa_izba varchar(50) not null,
metraz int unsigned,
wlasciciel int,
foreign key (wlasciciel) references postac(id_postaci) on delete set null
);
# on delete / on update --> restrict | set null | cascade

alter table izba add column kolor varchar(30) default 'czarny' after metraz;
describe izba;

insert into izba values('Kolejowa 23','Spiżarnia',10,default,1);
select * from izba;

alter table izba add primary key(adres_budynku, nazwa_izba);

create table przetwory(
id_przetworu int not null auto_increment,
rok_produkcji smallint default 1654,
id_wykonawcy int,
zawartosc varchar(255),
dodatek varchar(255) default 'papryczka chilli',
id_konsumenta int,
primary key(id_przetworu),
foreign key (id_wykonawcy) references postac(id_postaci) on delete set null,
foreign key (id_konsumenta) references postac(id_postaci) on delete set null
);

describe przetwory;

insert into przetwory values(default, default, 1, 'czosnek', default, 2);
select * from przetwory;

insert into postac values(default, 'Stefan', 'wiking', '1702-11-09', 36);
insert into postac values(default, 'Bartek', 'wiking', '1702-11-09', 36);
insert into postac values(default, 'Bożydar', 'wiking', '1702-11-09', 36);
insert into postac values(default, 'Albert', 'wiking', '1702-11-09', 36);
insert into postac values(default, 'Adam', 'wiking', '1702-11-09', 36);
select * from postac;

create table statek(
nazwa_statku varchar(50) not null,
rodzaj_statku enum('żeglówka','prom','kontenerowiec'),
data_wodowania date,
max_ladownosc int unsigned,
primary key(nazwa_statku)
);
describe statek;
select * from statek;

insert into statek values('Niezatapialny II', 'żeglówka', '1731-11-09', 10);
insert into statek values('Prom 1', 'prom', '1727-11-09', 500);

alter table postac add column funkcja varchar(30);
update postac set funkcja = 'kapitan' where id_postaci = 1;

alter table postac add column statek varchar(50);
alter table postac add foreign key (statek) references statek(nazwa_statku) on delete set null;
select * from postac;

update postac set statek = 'Niezatapialny II' where id_postaci != 3;
select * from postac;

select * from izba;
delete from izba where nazwa_izba = 'Spiżarnia';
select * from izba;

drop table izba;
show tables;
```

# Zadania lab_05
## Zadanie 1

```sql
select * from postac;
delete from postac where id_postaci != 1 and rodzaj = 'wiking' limit 2;
select * from postac;

# Usunięcie klucza głównego
alter table postac change id_postaci id_postaci int; # Krok 1 - pozbycie się auto increment
alter table walizka drop foreign key walizka_ibfk_1; # Krok 2 - usunięcie klucza obcego
alter table przetwory drop foreign key przetwory_ibfk_1; 
alter table postac drop primary key;
```
