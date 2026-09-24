# Projeto Lógico de Banco de Dados e-commerce

## 📌 Sobre o projeto

Este projeto consiste na criação de um **banco de dados lógico para um sistema de e-commerce**, desenvolvido utilizando **MySQL**.

O objetivo foi estruturar as principais entidades envolvidas em uma plataforma de comércio eletrônico, estabelecendo relacionamentos entre **clientes, produtos, pedidos, pagamentos, entregas, fornecedores, vendedores e estoque**.

O projeto foi desenvolvido com foco na aplicação prática dos conceitos de **modelagem de banco de dados, chaves primárias, chaves estrangeiras, relacionamentos e consultas SQL**.

---

## 🗄️ Estrutura do banco de dados

O banco de dados é composto pelas seguintes tabelas:

- `clients` — armazena os dados dos clientes.
- `clientsPJ` — armazena os dados de clientes pessoa jurídica.
- `clientsPF` — armazena os dados de clientes pessoa física.
- `product` — armazena os produtos disponíveis.
- `payments` — registra as formas e informações de pagamento.
- `orders` — registra os pedidos realizados pelos clientes.
- `delivery` — controla as informações de entrega.
- `productStorage` — registra os estoques e suas quantidades.
- `supplier` — armazena os fornecedores.
- `seller` — armazena os vendedores.
- `productSeller` — relaciona produtos e vendedores.
- `productOrder` — relaciona produtos e pedidos.
- `storageLocation` — relaciona produtos aos locais de armazenamento.
- `productSupplier` — relaciona produtos e fornecedores.

```sql
create table clients(
	idClient  int auto_increment primary key, 
    Fname varchar(10),
    Minit char(3),
    Lname varchar(20),
    Address varchar(30)
);

create table clientsPJ(
	idClientPJ  int auto_increment primary key, 
    idNumberClient int,
    CNPJ char(15) not null,
    constraint unique_cpf_cliente unique (CNPJ),
    constraint fk_number_client foreign key (idNumberClient) references clients(idClient)
);

create table clientsPF(
	idClientPF  int auto_increment primary key, 
    idNumberClient2 int,
    CPF char(11) not null,
    constraint unique_cpf_cliente unique (CPF),
    constraint fk_number_client_2 foreign key (idNumberClient2) references clients(idClient)
);

create table product(
	idProduct  int auto_increment primary key, 
    Pname varchar(10),
    Classification bool,
    category enum('Elétronico','Vestimenta','Brinquedos','Alimentos','Móveis') not null,
    avaliação float default 0,
    size varchar(10)
);

create table payments(
	idPayment int auto_increment primary key,
	idClientPayment int,
    typePayment enum('Boleto','Cartão','Dois cartões'),
    limitAvaiable float,
    constraint fk_payment_client foreign key (idClientPayment) references clients(idClient)
);

create table orders(
	idOrder int auto_increment primary key,
    idOrderClient int, 
    idDeliveryPayment int,
    orderStatus enum('Cancelado','Confiramdo','Processando') default 'Processando',
    orderDescription varchar(255),
    sendValue float default 10,
    paymentCash bool default false,
    constraint fk_orders_client foreign key (idOrderClient) references clients(idClient),
    constraint fk_orders_payment foreign key (idDeliveryPayment) references payments(idPayment)
);

create table delivery(
	idDelivery int auto_increment primary key,
    idDeliveryClient int, 
    idDeliveryPayment int,
    idDeliveryOrders int,
    deliveryStatus enum('Cancelado','Confiramdo','Processando') default 'Processando',
    deliveryDescription varchar(255),
    constraint fk_delivery_client foreign key (idDeliveryClient) references clients(idClient),
    constraint fk_delivery_payment foreign key (idDeliveryPayment) references payments(idPayment),
    constraint fk_delivery_orders foreign key (idDeliveryOrders) references orders(idOrder)
);

create table productStorage(
	idProdStorage int auto_increment primary key,
    storageLocation varchar(255),
    quantity int default 0
);

create table supplier(
	idSupplier int auto_increment primary key,
    SocialName varchar(255) not null,
    CNPJ char(15) not null ,
    contact char(11) not null,
    constraint unique_supplier unique (CNPJ)
);

create table seller(
	idSeller int auto_increment primary key,
    SocialName varchar(255) not null,
    AbstName varchar(255),
    CNPJ char(15) not null,
    CPF char(9),
    location varchar(255),
    contact char(11) not null,
    constraint unique_cnpj_seller unique (CNPJ),
    constraint unique_cpf_seller unique (CPF)
);

create table productSeller(
	idPseller int,
    idPproduct int,
    prodQuantityin int default 1,
    primary key (idPseller, idPproduct),
    constraint fk_product_seller foreign key (idPseller) references seller(idSeller),
    constraint fk_product_product foreign key (idPproduct) references product(idProduct)
);

create table productOrder(
	idPOproduct int,
    idPOorder int,
    prodQuantityin int default 1,
    poStatus enum('Disponivel','Sem estoque')default 'Disponivel',
    primary key (idPOproduct, idPOorder),
    constraint fk_productorder_seller foreign key (idPOproduct) references product(idProduct),
    constraint fk_productorder_product foreign key (idPOorder) references orders(idOrder)
);

create table storageLocation(
	idLproduct int,
    idLstorage int,
    location varchar(255) not null,
    primary key (idLproduct, idLstorage),
    constraint fk_storage_location_product foreign key (idLproduct) references product(idProduct),
    constraint fk_storage_location_storage foreign key (idLstorage) references productStorage(idProdStorage)
);

create table productSupplier(
	idPsSupplier int,
    idPsProduct int,
    quantity int not null,
    primary key (idPsSupplier, idPsProduct),
    constraint fk_product_supplier_supplier foreign key (idPsSupplier) references supplier(idSupplier),
    constraint fk_storage_supplier_product foreign key (idPsProduct) references product(idProduct)
);
```

As tabelas possuem **chaves primárias (`PRIMARY KEY`)** e **chaves estrangeiras (`FOREIGN KEY`)**, garantindo os relacionamentos e a integridade dos dados.

---

## 📥 Inserção dos dados

Após a criação da estrutura do banco de dados, foram inseridos **dados fictícios para testes**, permitindo validar o funcionamento das tabelas e seus relacionamentos.

Foram inseridos pelo menos **5 registros em cada tabela**, utilizando informações fictícias de clientes, produtos, pagamentos, pedidos, fornecedores, vendedores e estoques.

A inserção foi realizada respeitando a ordem dos relacionamentos entre as tabelas. Dessa forma, os registros das tabelas que possuem chaves estrangeiras fazem referência a registros previamente cadastrados nas tabelas relacionadas.

Exemplo:

```sql
insert into clients (
	idClient, Fname, Minit, Lname, Address
) values
	(1, 'Joao', 'A', 'Silva', 'Rua A, 100'),
	(2, 'Maria', 'B', 'Santos', 'Rua B, 200'),
	(3, 'Carlos', 'C', 'Oliveira', 'Rua C, 300'),
	(4, 'Ana', 'D', 'Souza', 'Rua D, 400'),
	(5, 'Pedro', 'E', 'Costa', 'Rua E, 500');

insert into clientsPJ (
	idClientPJ, idNumberClient, CNPJ
) values
	(1, 1, '123456789012345'),
	(2, 2, '234567890123456'),
	(3, 3, '345678901234567'),
	(4, 4, '456789012345678'),
	(5, 5, '567890123456789');

insert into clientsPF (
	idClientPF, idNumberClient2, CPF
) values
(1, 1, '12345678901'),
(2, 2, '23456789012'),
(3, 3, '34567890123'),
(4, 4, '45678901234'),
(5, 5, '56789012345');

insert into product (
	idProduct, Pname, Classification_kids, category, avaliação, size
) values
	(1, 'Notebook', false, 'Elétronico', 4.5, 'M'),
	(2, 'Camisa', false, 'Vestimenta', 4.2, 'G'),
	(3, 'Carrinho', true, 'Brinquedos', 4.8, 'P'),
	(4, 'Arroz', false, 'Alimentos', 4.0, '5kg'),
	(5, 'Sofa', false, 'Móveis', 4.7, 'Grande');

insert into payments(
	idPayment, idClientPayment, typePayment, limitAvaiable
) values
	(1, 1, 'Cartão', 5000.00),
	(2, 2, 'Boleto', 2500.00),
	(3, 3, 'Dois cartões', 8000.00),
	(4, 4, 'Cartão', 3500.00),
	(5, 5, 'Boleto', 1500.00);

insert into orders (
	idOrder, idOrderClient, idDeliveryPayment, orderStatus, orderDescription, sendValue, paymentCash
) values
	(1, 1, 1, 'Processando', 'Pedido de notebook', 25.00, false),
	(2, 2, 2, 'Confiramdo', 'Pedido de camisa', 15.00, true),
	(3, 3, 3, 'Processando', 'Pedido de brinquedo', 20.00, false),
	(4, 4, 4, 'Confiramdo', 'Pedido de alimentos', 12.00, true),
	(5, 5, 5, 'Cancelado', 'Pedido de sofa', 50.00, false);

insert into delivery (
	idDelivery, idDeliveryClient, idDeliveryPayment, idDeliveryOrders, deliveryStatus, deliveryDescription
) values
	(1, 1, 1, 1, 'Processando', 'Entrega aguardando despacho'),
	(2, 2, 2, 2, 'Confiramdo', 'Entrega confirmada'),
	(3, 3, 3, 3, 'Processando', 'Produto em transporte'),
	(4, 4, 4, 4, 'Confiramdo', 'Entrega pronta para envio'),
	(5, 5, 5, 5, 'Cancelado', 'Entrega cancelada');

insert into productStorage (
	idProdStorage, storageLocation, quantity
) values
	(1, 'Estoque Salvador', 50),
	(2, 'Estoque Recife', 100),
	(3, 'Estoque Sao Paulo', 75),
	(4, 'Estoque Rio de Janeiro', 120),
	(5, 'Estoque Brasilia', 60);

insert into supplier (
	idSupplier, SocialName, CNPJ, contact
) values
	(1, 'Tech Brasil LTDA', '678901234567890', '71999990001'),
	(2, 'Moda Nacional LTDA', '789012345678901', '71999990002'),
	(3, 'Brinca Mais LTDA', '890123456789012', '71999990003'),
	(4, 'Alimentos Brasil LTDA', '901234567890123', '71999990004'),
	(5, 'Moveis Conforto LTDA', '012345678901234', '71999990005');

insert into seller (
	idSeller, SocialName, AbstName, CNPJ, CPF, location, contact
) values
	(1, 'Tech Store LTDA', 'Tech Store', '112233445566778', '111111111', 'Salvador', '71988880001'),
	(2, 'Moda Fashion LTDA', 'Moda Fashion', '223344556677889', '222222222', 'Recife', '71988880002'),
	(3, 'Brinquedos Kids LTDA', 'Kids Toys', '334455667788990', '333333333', 'Sao Paulo', '71988880003'),
	(4, 'Mercado Bom Preco LTDA', 'Bom Preco', '445566778899001', '444444444', 'Brasilia', '71988880004'),
	(5, 'Casa Conforto LTDA', 'Casa Conforto', '556677889900112', '555555555', 'Rio de Janeiro', '71988880005');

insert into productSeller (
	idPseller, idPproduct, prodQuantityin
) values
	(1, 1, 10),
	(2, 2, 20),
	(3, 3, 15),
	(4, 4, 30),
	(5, 5, 8);

insert into productOrder (
	idPOproduct, idPOorder, prodQuantityin, poStatus
) values
	(1, 1, 1, 'Disponivel'),
	(2, 2, 2, 'Disponivel'),
	(3, 3, 1, 'Disponivel'),
	(4, 4, 5, 'Disponivel'),
	(5, 5, 1, 'Sem estoque');

insert into storageLocation (
	idLproduct, idLstorage, location
) values
	(1, 1, 'Prateleira A1'),
	(2, 2, 'Prateleira B2'),
	(3, 3, 'Prateleira C3'),
	(4, 4, 'Prateleira D4'),
	(5, 5, 'Prateleira E5');

insert into productSupplier (
	idPsSupplier, idPsProduct, quantity
) values
	(1, 1, 50),
	(2, 2, 100),
	(3, 3, 75),
	(4, 4, 120),
	(5, 5, 60);
```

Esses dados foram utilizados posteriormente para testar as consultas SQL.

---

# 🔎 Consultas e filtros dos dados

Após a criação e inserção dos dados, foram desenvolvidas consultas utilizando diferentes recursos da linguagem SQL.

Foram utilizadas **6 abordagens principais**, cada uma respondendo a uma pergunta relacionada aos dados armazenados no banco.

---

## 1. Recuperação simples com `SELECT`

### Pergunta:

> **Quais são todos os produtos cadastrados no banco de dados?**

Para responder essa pergunta, foi utilizado o `SELECT` para recuperar as informações diretamente da tabela `product`.

```sql
SELECT * FROM product;
```

Essa consulta permite visualizar os produtos cadastrados juntamente com suas principais informações.

---

## 2. Filtro de dados com `WHERE`

### Pergunta:

> **Quais produtos pertencem à categoria de Vestimenta?**

O `WHERE` foi utilizado para filtrar somente os registros que atendem à condição especificada.

```sql
SELECT * FROM product WHERE category = 'Vestimenta';
```

Nesse caso, apenas os produtos cuja categoria seja `Vestimenta` serão retornados.

---

## 3. Criação de atributos derivados

### Pergunta:

> **Qual é a quantidade de produtos em cada estoque e qual seria o valor estimado desse estoque considerando R$ 100,00 por unidade?**

Foi utilizada uma expressão matemática para criar um atributo derivado chamado `valorEstoque`.

```sql
SELECT idProdStorage, storageLocation, quantity, quantity * 100.00 AS valorEstoque FROM productStorage;
```

O campo `valorEstoque` não existe fisicamente na tabela. Seu valor é calculado no momento da execução da consulta.

---

## 4. Ordenação com `ORDER BY`

### Pergunta:

> **Quais são os produtos cadastrados, ordenados da maior para a menor avaliação?**

O `ORDER BY` foi utilizado para organizar os resultados de acordo com o valor da avaliação.

```sql
SELECT * FROM product ORDER BY avaliação DESC;
```

A utilização de `DESC` faz com que os resultados sejam apresentados em ordem decrescente.

---

## 5. Filtro de grupos com `HAVING`

### Pergunta:

> **Quais categorias possuem mais de um produto cadastrado?**

Nesse caso, foi utilizado o `GROUP BY` para agrupar os produtos por categoria e o `HAVING` para filtrar os grupos.

```sql
select Classification as classificacao, COUNT(*) as quantidadeProdutos from product group by Classification_kids HAVING COUNT(*) >= 2;
```

O `HAVING` é utilizado para aplicar condições sobre os grupos gerados pelo `GROUP BY`.

---

## 6. Junção entre tabelas com `JOIN`

### Pergunta:

> **Quais clientes realizaram pedidos e quais produtos estão relacionados a cada pedido?**

Para responder uma pergunta mais complexa, foi necessário relacionar várias tabelas utilizando `INNER JOIN`.

```sql
SELECT
    c.Fname AS cliente,
    c.Lname AS sobrenome,
    o.idOrder AS pedido,
    p.Pname AS produto,
    po.prodQuantityin AS quantidade,
    o.orderStatus AS statusPedido
FROM clients AS c
INNER JOIN orders AS o
    ON c.idClient = o.idOrderClient
INNER JOIN productOrder AS po
    ON o.idOrder = po.idPOorder
INNER JOIN product AS p
    ON po.idPOproduct = p.idProduct;
```

Essa consulta combina informações das tabelas `clients`, `orders`, `productOrder` e `product`, permitindo visualizar os dados de forma integrada.

---

# 📊 Resumo das consultas

| Nº | Recurso SQL | Pergunta |
|---|---|---|
| 1 | `SELECT` | Quais são todos os produtos cadastrados? |
| 2 | `WHERE` | Quais produtos pertencem à categoria Vestimenta? |
| 3 | Expressão derivada | Qual é o valor estimado do estoque por produto? |
| 4 | `ORDER BY` | Quais produtos possuem as maiores avaliações? |
| 5 | `HAVING` | Quais categorias possuem mais de um produto? |
| 6 | `JOIN` | Quais clientes realizaram pedidos e quais produtos estão relacionados a cada pedido? |

---

## 🎯 Objetivos alcançados

Com o desenvolvimento deste projeto foi possível aplicar na prática conceitos fundamentais de bancos de dados relacionais, incluindo:

- Criação de tabelas;
- Definição de chaves primárias;
- Definição de chaves estrangeiras;
- Relacionamento entre entidades;
- Inserção de dados;
- Recuperação de informações com `SELECT`;
- Filtragem de registros com `WHERE`;
- Criação de atributos derivados;
- Ordenação com `ORDER BY`;
- Agrupamento com `GROUP BY`;
- Filtragem de grupos com `HAVING`;
- Junção de diferentes tabelas utilizando `JOIN`.

O projeto demonstra, portanto, a aplicação prática de conceitos de **SQL e modelagem de banco de dados** em um cenário de comércio eletrônico.
