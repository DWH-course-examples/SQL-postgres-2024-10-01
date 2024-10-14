```sql
CREATE TABLE Meas_Units (
  id SERIAL primary key, 
  name text
);

CREATE TABLE Products (
  id SERIAL primary key, 
  name text,
  is_product boolean,
  amount int,
  price decimal,
  photo_url varchar(255),
  meas_unit_id int,
  parent_id int,
  sum_cost decimal,
  constraint FK_measid foreign key (meas_unit_id) references Meas_Units(id),
  constraint FK_parent_product foreign key (parent_id) references Products(id)
);

CREATE TABLE Price_history (
  id SERIAL primary key, 
  product_id int,
  price decimal,
  date_start timestamp,
  date_end timestamp,
  constraint FK_Products  foreign key (product_id) references Products(id)
);

CREATE TABLE Organizations (
  id SERIAL primary key, 
  name text,
  address text,
  phone varchar(255)
);

CREATE TABLE Invoices (
  id SERIAL primary key, 
  numb int,
  date_in timestamp,
  org_id int,
  in_out char,
  sum_invoice decimal,
  Constraint FK_orgid foreign key (org_id) references Organizations(id)
);

CREATE TABLE Products_to_Invoices (
  id SERIAL primary key, 
  product_id int,
  invoice_id int,
  amount int,
  price decimal,
  Constraint FK_productid foreign key (product_id) references Products(id),
  Constraint FK_invoiceid foreign key (invoice_id) references Invoices(id)
);
```

Add data:

```sql
insert into meas_units(name) values ('шт'), ('набор'), ('кг'), ('литр');

insert into Organizations(name, address, phone) 
values ('ООО Рога и Копыта', 'Москва, улица Пушкина', '12345678');

INSERT INTO Organizations(Name, Address, Phone) 
VALUES ('Мультисистемы','Москва,Ходынский б-р, 9','8 (495) 555-43-21'), 
	   ('Инфосистемы','Воронеж, Бульвар Победы, д. 48а, оф. 10','8 (473) 555-43-21');
       
insert into Organizations(name, address, phone) 
values ('ООО Рога и Копыта_2', 'Ставрополь, улица Мира_2', '00012345678-2'); 

insert into Organizations(name, address, phone) 
values ('ООО Рога и Копыта_3', 'Ставрополь, улица Мира_2', '00012345678-3');  

INSERT INTO Invoices(Date_IN, Numb, Org_ID, In_Out, Sum_invoice) 
VALUES ('2024-10-11 11:20:00',1,2,'i',100000);  
INSERT INTO Invoices(Date_IN, Numb, Org_ID, In_Out, Sum_invoice) 
VALUES ('2024-10-14 11:21:33',9,1,'i',33000);

INSERT INTO Products(Name, Is_product, Amount, Price, Photo_url ,Meas_Unit_ID,  Sum_Cost) 
VALUES ('Inspark ITSM',TRUE,1,100000,'https://274418.selcdn.ru/cv08300-33250f0d-0664-43fc-9dbf-9d89738d114e/uploads/255954/6c70c5c8-a086-42e5-9368-b98c0ccc0846.png', 1, 1*100000);

INSERT INTO Products_To_Invoices(Invoice_ID, product_id, Amount, Price)
VALUES (1,1,1,100000);  
```

Select data:

```sql
 
--select * from organizations;

--select * from invoices i, organizations o
-- where i.org_id = o.id;

select * from products p, meas_units mu
where p.meas_unit_id = mu.id;

-- подсказка
--CONSTRAINT <имя ограничения> FOREIGN KEY [(<имя поля>)…)]
--REFERENCES <имя мастер таблицы>
--	

-- Получить список накладных на поступление товаров от организаций, расположенных -- на -- улице 'Свободы' c 1 января 2009г. по 30 апреля 2014г.

select * from invoices i
join Organizations o ON i.org_id = o.id
where o.address like '%Москва%'
and in_out = 'i'
and date_in between '2024-10-01' and '2024-10-30'
ORDER BY o.name, i.date_in;

-- Получить список пар организаций, имеющих одинаковый адрес

select * from Organizations o1
cross join Organizations o2
WHERE o1.id<o2.id and o1.address = o2.address;

-- Получить данные о последней накладной для каждой организации.

```
