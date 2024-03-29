# Robert Gryska, nr. 176787
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
## Zadanie 1 - Cicha woda

```sql
select * from postac;
delete from postac where id_postaci != 1 and rodzaj = 'wiking' limit 2;
select * from postac;

# Usunięcie klucza głównego
alter table postac change id_postaci id_postaci int; # Krok 1 - pozbycie się auto increment
alter table walizka drop foreign key walizka_ibfk_1; # Krok 2 - usunięcie klucza obcego
alter table przetwory drop foreign key przetwory_ibfk_1;
alter table przetwory drop foreign key przetwory_ibfk_2; 
show create table przetwory;
alter table postac drop primary key;
show create table postac;

# varchar(n) -> max n znaków
# char(n) -> dokładnie n znaków

# alternatywne zastosowania select
select 5;
select now();
select 6 + 5;
```
## Zadanie 2 - Syrenka

```sql
alter table postac add column pesel char(11) first;
describe postac;
update postac set pesel='63817938475' + id_postaci;
select * from postac;
alter table postac add primary key(pesel);

alter table postac change rodzaj rodzaj enum('wiking','ptak','kobieta','syrena') default null;

insert into postac values(68291744831, 7, 'Gertruda Nieszczera', 'syrena', '1690-11-09', 48, default, default)
```
## Zadanie 3 - Przechyły
```sql
show create table statek;

update postac set statek='Niezatapialny II' where nazwa like '%a%';
# % - dowolny ciąg znaków
# _ - dokładnie jeden znak

# nauczyć się funkcji regexp

select * from statek;

update statek set max_ladownosc=max_ladownosc * 0.7 
where data_wodowania >= '1901-01-01' 
and data_wodowania <= '2000-12-31';

# alternatywnie: between '1901-01-01' and '2000-12-31'	/	where year(data_wodowania) between 1901 and 2000

alter table postac add check(wiek < 1000);
# check(warunek)
```
## Zadanie 4 - Wąż Loko
```sql
alter table postac change rodzaj rodzaj enum('wiking','ptak','kobieta','syrena','wąż') default null;
insert into postac values(68291744832, 8, 'Loko', 'wąż', '1680-11-09', 58, default, default);

create table marynarz like postac;
show create table postac;
show create table marynarz;
select * from marynarz;

insert into marynarz select * from postac where statek is not null;

alter table marynarz add foreign key (`statek`) references `statek` (`nazwa_statku`) on delete set null;
```
## zadanie 5 - Sztorm
```sql
update postac set statek = null where statek is not null;
select * from postac;

delete from postac where id_postaci != 1 and rodzaj = 'wiking' limit 1;

delete from statek;
alter table postac drop foreign key postac_ibfk_1;
alter table marynarz drop foreign key marynarz_ibfk_1;
drop table statek;

create table zwierz(
id int auto_increment primary key,
nazwa varchar(30),
wiek int);

insert into zwierz select id_postaci, nazwa, wiek from postac where rodzaj='ptak' or rodzaj='wąż';
select * from zwierz;
```

# Zadania lab_06
## Zadanie 1 - Rozbitek

```sql
create table kreatura as select * from wikingowie.kreatura;
create table zasob as select * from wikingowie.zasob;
create table ekwipunek as select * from wikingowie.ekwipunek;

select * from zasob;

select * from zasob where rodzaj = 'jedzenie';

select idZasobu, ilosc from ekwipunek where idKreatury in (1, 3, 5);
```

## Zadanie 2 - Kokos?
```sql
select * from kreatura where rodzaj != 'wiedzma' and udzwig >= 50;

select * from zasob where waga between 2 and 5;

select * from kreatura where nazwa like '%or%' and udzwig between 30 and 70;
```

## Zadanie 3 - Hamak
```sql
select * from zasob where month(dataPozyskania) in (07, 08);

select * from zasob where rodzaj is not null order by waga;

select * from kreatura where dataUr is not null order by dataUr limit 5;
```

## Zadanie 4 - Złota rybka
```sql
select distinct rodzaj from zasob;

select concat(nazwa, ' - ', rodzaj) as nazwaRodzaj from kreatura where rodzaj like 'wi%';

select waga * ilosc as calkowitaWaga from zasob where year(dataPozyskania) between 2000 and 2007;
```

## Zadanie 5 - Twardy sen
```sql
select nazwa, waga * 0.7 as wagaWlasciwa, waga * 0.3 as wagaOdpady from zasob;

select * from zasob where rodzaj is null;

select distinct rodzaj from zasob where rodzaj like 'Ba%' or rodzaj like '%os' order by rodzaj asc;
```

# Zadania lab_07
## Zadanie 1 - Skwarek

```sql
# funkcje agragujące
# avg(), sum(), count(), min(), max()

select avg(waga) from kreatura where rodzaj = 'wiking';

# count(kolumna) zlicza pomijając null, count(*) zlicza ilość wierszy, w tym null
select avg(waga), rodzaj, count(waga), count(*) from kreatura group by rodzaj;

select avg(year(now()) - year(dataUr)) as wiek, rodzaj from kreatura group by rodzaj;
```

## Zadanie 2 - Dymek
```sql
select sum(waga* ilosc), rodzaj from zasob group by rodzaj;

select nazwa, avg(waga) from zasob 
where ilosc >= 4
group by nazwa having sum(waga) > 10 ;

select count(distinct(nazwa)) as liczba, rodzaj from zasob 
group by rodzaj having liczba > 1;
```

## Zadanie 3 - Pieprz
```sql
select k.nazwa, z.nazwa, e.ilosc from 
kreatura k inner join ekwipunek e on k.idKreatury = e.idKreatury
inner join zasob z on z.idZasobu = e.idZasobu
order by k.nazwa asc;

select k.nazwa from 
kreatura k left join ekwipunek e on k.idKreatury = e.idKreatury
where e.idKreatury is null;
```

## Zadanie 4 - Dziadek
```sql
select k.nazwa, z.nazwa from 
kreatura k inner join ekwipunek e on k.idKreatury = e.idKreatury
inner join zasob z on z.idZasobu = e.idZasobu
where year(k.dataUr) between 1670 and 1679
order by k.nazwa asc;

select k.nazwa from 
kreatura k inner join ekwipunek e on k.idKreatury = e.idKreatury
inner join zasob z on z.idZasobu = e.idZasobu
where z.rodzaj = 'jedzenie'
order by k.dataUr desc limit 5;

select concat(k1.nazwa,' - ', k2.nazwa) from
kreatura k1 inner join kreatura k2
where k1.idKreatury - k2.idKreatury = 5;
```

## Zadanie 5 - Zła nowina
```sql
select k.rodzaj, avg(z.waga * e.ilosc) from 
kreatura k inner join ekwipunek e on k.idKreatury = e.idKreatury
inner join zasob z on z.idZasobu = e.idZasobu
where k.rodzaj not in ('malpa', 'waz')
group by k.rodzaj having sum(e.ilosc) < 30;

select a.nazwa, a.rodzaj, a.dataUr from kreatura a,
(select min(dataUr) min, max(dataUr) max 
from kreatura group by rodzaj) b
where b.min = a.dataUr or b.max = a.dataUr;
```

# Zadania lab_08
## Zadanie 1 - Spisek
```sql
insert into kreatura select * from wikingowie.kreatura;
create table uczestnicy as select * from wikingowie.uczestnicy;
create table etapy_wyprawy as select * from wikingowie.etapy_wyprawy;
create table sektor as select * from wikingowie.sektor;
create table wyprawa as select * from wikingowie.wyprawa;

select k.nazwa from 
kreatura k left join uczestnicy u on k.idKreatury = u.id_uczestnika
where u.id_uczestnika is null;

select w.nazwa, sum(e.ilosc) from ekwipunek e
inner join uczestnicy u on e.idKreatury = u.id_uczestnika
inner join wyprawa w on w.id_wyprawy = u.id_wyprawy
group by w.nazwa;
```
## Zadanie 2 - Trudna decyzja
```sql
select w.nazwa, count(distinct u.id_uczestnika), group_concat(distinct k.nazwa separator ', ')
from kreatura k
inner join uczestnicy u on k.idKreatury = u.id_uczestnika
inner join wyprawa w on w.id_wyprawy = u.id_wyprawy
group by w.nazwa;

select e.idEtapu, w.id_wyprawy, s.nazwa,  k.nazwa as kierownik, w.data_rozpoczecia from
etapy_wyprawy e 
inner join wyprawa w on e.idWyprawy = w.id_wyprawy
inner join sektor s on s.id_sektora = e.sektor
inner join kreatura k on k.idKreatury = w.kierownik
order by w.data_rozpoczecia, e.kolejnosc;
```
## Zadanie 3 - Misterny plan
```sql
select s.nazwa, count(e.sektor) from
sektor s left join etapy_wyprawy e on s.id_sektora = e.sektor 
group by nazwa;

select k.nazwa, 
if(count(u.id_uczestnika) > 0, 'Był na wyprawie',
'Nie był na wyprawie') czy_byl
from kreatura k
left join uczestnicy u on u.id_uczestnika = k.idKreatury
group by k.nazwa;
```
## Zadanie 4 - Pułapka
```sql
select w.nazwa, sum(length(e.dziennik))
from wyprawa w 
inner join etapy_wyprawy e on w.id_wyprawy = e.idWyprawy
group by w.nazwa
having sum(length(dziennik)) < 400;

select w.nazwa, sum(z.waga * e.ilosc)/count(distinct u.id_uczestnika) as średni_ekwipunek
from wyprawa w
inner join uczestnicy u on u.id_wyprawy = w.id_wyprawy
inner join ekwipunek e on e.idKreatury = u.id_uczestnika
inner join zasob z on z.idZasobu = e.idZasobu
group by w.nazwa;
```
## Zadanie 5 - Tchórz
```sql
use wikingowie;
select w.nazwa, k.nazwa, datediff(w.data_rozpoczecia, k.dataUr) as wiek
from kreatura k
inner join uczestnicy u on k.idKreatury = u.id_uczestnika
inner join wyprawa w on w.id_wyprawy = u.id_wyprawy
inner join etapy_wyprawy e on e.idWyprawy = w.id_wyprawy
where e.sektor = 7
group by w.id_wyprawy, k.idKreatury;
```
# Lab_09
## Zadanie 1 - Dziura
```sql
alter table kreatura modify waga int default 0;

delimiter //
create trigger kreatura_before_insert
before insert on kreatura
for each row
begin
	if new.waga <= 0
    then
		set new.waga = 1;
	end if;
end
//
delimiter ;

delimiter //
create trigger kreatura_before_update
before update on kreatura
for each row
begin
	if new.waga <= 0
    then
		set new.waga = 1;
	end if;
end
//
delimiter ;
```
## Zadanie 2 - Futro
```sql
create table archiwum_wypraw(
id_wyprawy int,
nazwa varchar(200) not null,
data_rozpoczecia date not null,
data_zakonczenia date not null,
kierownik varchar(30));
drop trigger wyprawa_before_delete;

delimiter //
create trigger wyprawa_before_delete
before delete on wyprawa
for each row
begin
	insert into archiwum_wypraw
	select w.id_wyprawy, w.nazwa, w.data_rozpoczecia, w.data_zakonczenia, k.nazwa 
	from wyprawa w inner join kreatura k 
	on w.kierownik = k.idKreatury
	where id_wyprawy = old.id_wyprawy;
end
//
delimiter ;

insert into wyprawa values (default, 'Test01', '1700-08-02', '1700-08-03', '1');

delete from wyprawa where id_wyprawy = 0;
```
## Zadanie 3 - Spalony
```sql
delimiter //
create procedure eliksir_sily(in id int)
begin
	update kreatura set udzwig = udzwig * 1.2 where id = idKreatury;
end
//
delimiter ;
```
# Powtórzenie - lab_3_zadania_1
## Zadanie 1
```sql
select imie, nazwisko, year(data_urodzenia) from pracownik;
```
## Zadanie 2
```sql
select imie, nazwisko, year(now()) - year(data_urodzenia) as wiek from pracownik;
```
## Zadanie 3
```sql
select d.nazwa, count(distinct p.id_pracownika) from
dzial d inner join pracownik p on d.id_dzialu = p.dzial
group by d.id_dzialu;
```
## Zadanie 4
```sql
select k.nazwa_kategori, count(t.id_towaru) from
kategoria k inner join towar t on k.id_kategori = t.kategoria
group by k.id_kategori;
```
## Zadanie 5
```sql
select k.nazwa_kategori, group_concat(distinct t.nazwa_towaru separator ', ') from
kategoria k inner join towar t on k.id_kategori = t.kategoria
group by k.id_kategori;
```
## Zadanie 6
```sql
select round(avg(pensja), 2) from pracownik;
```
## Zadanie 7
```sql
select round(avg(pensja), 2) from pracownik where data_zatrudnienia <= '2019-01-11';
```
## Zadanie 8
```sql
select t.nazwa_towaru, count(distinct p.id_pozycji) from 
towar t inner join pozycja_zamowienia p on t.id_towaru = p.towar
group by t.id_towaru
order by count(distinct p.id_pozycji) desc
limit 10;
```
## Zadanie 9
```sql
select z.numer_zamowienia, z.id_zamowienia, sum(pz.ilosc * pz.cena) as wartość from zamowienie z
inner join pozycja_zamowienia pz on z.id_zamowienia = pz.zamowienie
where z.data_zamowienia between '2017-01-01' and '2017-03-31'
group by z.id_zamowienia;
```
## Zadanie 10
```sql
select p.imie, p.nazwisko, sum(pz.ilosc * pz.cena) as wartosc from 
pracownik p inner join zamowienie z on p.id_pracownika = z.pracownik_id_pracownika
inner join pozycja_zamowienia pz on z.id_zamowienia = pz.zamowienie
group by p.id_pracownika
order by sum(pz.ilosc * pz.cena) desc;
```
# Powtórzenie - lab_3_zadania_2
# Zadanie 1
```sql
select d.nazwa, min(p.pensja), max(p.pensja), avg(p.pensja) from 
pracownik p inner join dzial d on p.dzial = d.id_dzialu
group by d.id_dzialu;
```
# Zadanie 2
```sql
select k.pelna_nazwa, sum(pz.ilosc * pz.cena) as wartosc from 
klient k inner join zamowienie z on z.klient = k.id_klienta
inner join pozycja_zamowienia pz on pz.zamowienie = z.id_zamowienia
group by z.id_zamowienia
order by sum(pz.ilosc * pz.cena) desc
limit 10;
```
# Zadanie 3
```sql
select year(z.data_zamowienia), sum(pz.ilosc * pz.cena) as przychód from 
zamowienie z inner join pozycja_zamowienia pz on pz.zamowienie = z.id_zamowienia
group by year(z.data_zamowienia)
order by sum(pz.ilosc * pz.cena) desc;
```
# Zadanie 4
```sql
select sum(pz.ilosc * pz.cena) as wartość from 
pozycja_zamowienia pz inner join zamowienie z on pz.zamowienie = z.id_zamowienia
where z.status_zamowienia = 6;
```
# Zadanie 5
```sql
select ak.miejscowosc, sum(pz.ilosc * pz.cena), count(ak.miejscowosc) from 
adres_klienta ak inner join klient k on k.id_klienta = ak.klient
inner join zamowienie z on z.klient = k.id_klienta
inner join pozycja_zamowienia pz on z.id_zamowienia = pz.zamowienie
group by ak.miejscowosc;
```
# Zadanie 6
```sql
select sum(pz.ilosc * pz.cena) as łączny_przychód from 
pozycja_zamowienia pz inner join zamowienie z on pz.zamowienie = z.id_zamowienia
where z.status_zamowienia = 5;
```
# Zadanie 7
```sql
select year(z.data_zamowienia) as Rok, sum(pz.ilosc * pz.cena - t.cena_zakupu) as Przychód from pozycja_zamowienia pz 
inner join towar t on t.id_towaru = pz.towar
inner join zamowienie z on z.id_zamowienia = pz.zamowienie
group by year(z.data_zamowienia)
order by year(z.data_zamowienia);
```
# Zadanie 8
```sql
select k.nazwa_kategori, sum(sm.ilosc) from
kategoria k inner join towar t on k.id_kategori = t.kategoria
inner join stan_magazynowy sm on t.id_towaru = sm.towar
group by k.nazwa_kategori;
```
# Zadanie 9
```sql
select monthname(data_urodzenia) as 'Miesiąc', count(distinct id_pracownika) as 'Liczba pracowników' from pracownik
group by monthname(data_urodzenia)
order by FIELD(Miesiąc,'January','February','March','April','May','June','July','August','September','October','November','December');
```
# Zadanie 10
```sql

```
