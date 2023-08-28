# Desafio Modelagem de Banco de Dados
 Refinamento

 
-- Desafio do curso de Modelagem de banco de dados --
-- BANCO DE DADOS REFINADO --

-- criação do banco de dados para o cenário de E-commerce 
-- drop database Ecommerce;
create database if not exists Ecommerce;
use ecommerce;
-- drop table seller;
-- criar tabela cliente
create table clients(
		idClient int auto_increment primary key,
        Fname varchar(10) not null,
        Minit char(3),
        Lname varchar(20) not null,
        CPF char(11),
        CNPJ char(15),
        Address varchar(255) not null,
        ClientType enum('PF', 'PJ') not null,
        constraint unique_cpf_client unique (CPF),
        constraint unique_CNPJ_client unique (CNPJ)
);

-- criar tabela produto
create table product(
		idProduct int auto_increment primary key,
        Pname varchar(255) not null,
        classification_kids bool default false,
        category enum('Eletrônico','Vestimenta','Brinquedos','Alimentos','Móveis') not null,
        avaliação float default 0,
        size varchar(10)
);

-- criar tabela pagamento
create table payments(
	idclient int,
    idPayment int,
    typePayment enum('Boleto','Cartão','Dois cartões'),
    limitAvailable float,
    primary key(idClient, idPayment)
);

-- criar tabela pedido
create table request(
	idRequest int auto_increment primary key,
    idClient int,
    status enum('Processando', 'cancelado', 'confirmado') default 'Processando',
    description varchar(255),
    freightage float default 10,
    paymentCash bool default false,
    idPayment int,
    constraint fk_request_client foreign key(idClient) references clients(idClient)
);

-- drop table orders;
create table orders(
	idOrder int auto_increment primary key,
    idOrderClient int,
    orderStatus enum('Cancelado','Confirmado','Em processamento') default 'Em processamento',
    orderDescription varchar(255),
    sendValue float default 10,
    paymentCash boolean default false, 
    constraint fk_ordes_client foreign key (idOrderClient) references clients(idClient)
			on update cascade
);

-- criar tabela estoque
create table productStorage(
	idProdStorage int primary key,
    Location varchar(255),
    quantity int default 0
);

-- criar tabela fornecedor
create table supplier(
	idSupplier int  primary key,
    SocialName varchar(255) not null,
    CNPJ char(15) not null,
    contact char(11) not null,
    constraint unique_supplier unique (CNPJ)
);

-- criar tabela vendedor
create table seller(
	idSeller int  primary key,
    SocialName varchar(255) not null,
    AbstName varchar(255),
    CNPJ char(15),
    CPF char(9),
    location varchar(255),
    contact char(11) not null,
    Address varchar(255),
    constraint unique_cnpj_seller unique (CNPJ),
    constraint unique_cpf_seller unique (CPF)
);

-- criar tabelas de relacionamentos M:N pedido/pagamento
CREATE TABLE request_payments (
  idRequest INT NOT NULL,
  idPayment INT NOT NULL,
  idClient INT NOT NULL,
  PRIMARY KEY (idRequest, idPayment, idClient),
  CONSTRAINT fk_request_payments FOREIGN KEY(idRequest) REFERENCES request(idRequest),
  CONSTRAINT fk_request_payments_payment FOREIGN KEY(idPayment , idClient) REFERENCES payments (idPayment, idClient)
);

-- criar relacionamento produto_vendedor
create table productSeller(
	idPseller int,
    idPproduct int,
    prodQuantity int default 1,
    primary key (idPseller, idPproduct),
    constraint fk_product_seller foreign key (idPseller) references seller(idSeller),
    constraint fk_product_product foreign key (idPproduct) references product(idProduct)
);

-- criar relacionamento produto_pedido
create table productOrder(
	idPOproduct int,
    idPOorder int,
    poQuantity int default 1,
    poStatus enum('Disponível', 'Sem estoque') default 'Disponível',
    primary key (idPOproduct, idPOorder),
    constraint fk_productorder_product foreign key (idPOproduct) references product(idProduct),
    constraint fk_productorder_order foreign key (idPOorder) references orders(idOrder)
);

-- criar tabela estoque
create table storageLocation(
	idLproduct int,
    idLstorage int,
    location varchar(255) not null,
    primary key (idLproduct, idLstorage),
    constraint fk_storage_location_product foreign key (idLproduct) references product(idProduct),
    constraint fk_storage_location_storage foreign key (idLstorage) references productStorage(idProdStorage)
);

-- criar tabela de relacionamento produto/fornecedor
create table productSupplier(
	idPsSupplier int,
    idPsProduct int,
    quantity int not null,
    primary key (idPsSupplier, idPsProduct),
    constraint fk_product_supplier_supplier foreign key (idPsSupplier) references supplier(idSupplier),
    constraint fk_product_supplier_prodcut foreign key (idPsProduct) references product(idProduct)
);

-- criar tabela entrega
create table delivery(
	idDelivery int,
    trackingCode varchar(20),
    idRequest int,
    status enum('processando', 'Em trânsito', 'Entregue', 'devolvido'),
    constraint fk_delivery_request foreign key(idRequest) references request(idRequest)
);






-- Banco de Dados(Refinado) --
use ecommerce;

show tables; 

--  inserindo registros na tabela Clientes --
INSERT INTO Clients (Fname, Minit, Lname, CPF, CNPJ, Address, clientType) VALUES
             ('Maria','M','Silva', 12346789, NULL, 'rua silva de prata 29, Carangola - Cidade das flores', 'PF'),
             ('Empresa AAA', NULL, 'Indústria Ltda', NULL, '12345678901236', 'Rua do Céu, 544', 'PJ'),
		     ('Matheus','O','Pimentel', 987654321, NULL,'rua alemeda 289, Centro - Cidade das flores', 'PF'),
             ('Empresa BBB', NULL, 'Brinquedos Ltda.', NULL, '98765432123456', 'Av. das Estrelas, 1012', 'PJ'),
			 ('Ricardo','F','Silva', 45678913, NULL, 'avenida alemeda vinha 1009, Centro - Cidade das flores','PF'),
             ('Empresa CCC', NULL, 'Comércio S.A.', NULL, '98765432109866', 'Av. Universitária, 1011', 'PJ'),
			 ('Julia','S','França', 789123456, NULL, 'rua lareijras 861, Centro - Cidade das flores', 'PF'),
			 ('Empresa DDD', NULL, 'Distribuidora Ltda.', NULL, '54321678909878', 'Av. das Palmeiras, 1021', 'PJ'),
			 ('Roberta','G','Assis', 98745631, NULL, 'avenidade koller 19, Centro - Cidade das flores', 'PF'),
			 ('Empresa EEE', NULL, 'Tecnologia S.A.', NULL, '34567891234564', 'Rua Invertida, 123', 'PJ'),
			 ('Isabela','M','Cruz', 654789123, NULL, 'rua alemeda das flores 28, Centro - Cidade das flores','PF');
             
--  Inserindo registros na tabela Documentos --
INSERT INTO `Documentos` (`DocType`, `DocNum`, `fk_idClient`) VALUES ('CNPJ', '123456789456123', '1');
INSERT INTO `Documentos` (`DocNum`, `fk_idClient`) VALUES ('36985214785', '2');
INSERT INTO `Documentos` (`DocNum`, `fk_idClient`) VALUES ('35795174123', '3');

--  Inserindo registros na tabela cartão de crédito --
INSERT INTO `creditcards` (`Name`, `Number`, `Flag`, `ExpDate`,`fk_idClient`) VALUES 
 ('Julio C. Okuda', '123456789321456', 'Visa', '25-10-01', '1'),
 ('Solange M Okita', '3579514852', 'Master', '24-01-02', '2'),
 ('Solange M Okita', '3579518888', 'Visa', '25-01-05', '2');

--  Inserindo registros na tabela formas de pagamentos --
INSERT INTO `formas_pagamentos` (`PayType`) VALUES 
   ('Credito'),
   ('Boleto'),
   ('Pix'),
   ('Debito');

--  Inserindo registros na tabela Fornecedores --
INSERT INTO `fornecedores` (`SocialName`, `CNPJ`, `Contact`) VALUES 
   ('Nihontech', '123654985632569', '119849849'),
   ('Techbyte', '123321123321122', '21988787878');                     
                
-- Inserindo registro na tabela Produtos
INSERT INTO produtos (Pname, classification_kids, category, avaliação, size) VALUES
							  ('Fone de ouvido',FALSE,'Eletrônico','4',NULL),
                              ('Barbie Elsa',TRUE,'Brinquedos','3',NULL),
                              ('Body Carters',TRUE,'Vestimenta','5',NULL),
                              ('Microfone Vedo - Youtuber',FALSE,'Eletrônico','4',NULL),
                              ('Sofá retrátil',FALSE,'Móveis','3','3x57x80'),
                              ('Farinha de arroz',FALSE,'Alimentos','2',NULL),
                              ('Fire Stick Amazon',FALSE,'Eletrônico','3',NULL),
							  ('Biscoitos de Chocolate', FALSE, 'Alimentos', 4.0, NULL),
                              ('Vestido Floral', TRUE, 'vestuário', 4.1, 'P'),
                              ('Carrinho de Controle Remoto', TRUE, 'brinquedos', 4.5, NULL),
                              ('Barras de Cereal', FALSE, 'Alimentos', 3.5, NULL),
                              ('Blusa de Moletom', TRUE, 'vestuário', 3.9, 'G'),
                              ('Notebook ABC', FALSE, 'eletrônico', 4.1, NULL),
                              ('Calça Jeans', TRUE, 'vestuário', 4.4, 'M'),
							  ('Boneca de Pelúcia', TRUE, 'brinquedos', 3.8, NULL);


-- Inserindo registro na tabela request_payments (relacionamento N:M pedido/pagamento)
INSERT INTO request_payments (idRequest, idPayment, idClient) VALUES
    (5, 6, 5),
    (6, 7, 5),
    (7, 8, 6),
    (8, 9, 7),
    (9, 10, 8),
    (10, 11, 9),
    (11, 12, 9),
    (12, 13, 10),
    (13, 14, 11),
    (14, 15, 12);
    
--  Inserindo registros na tabela de estoques --
-- select * from estoques;
INSERT INTO `estoques` (`Local`, `Name`, `Contact`) VALUES 
      ('Rio de Janeiro - RJ','João','21965324123'),
	  ('São Paulo - SP','Laura Pausini','11965324512'),
      ('Salvador - BA','Rafael Silva','77956238574'),
      ('Curitiba - PR','Takao K.','41985749632'),
      ('Uberlândia - MG','Patrick O.','31956234578'),
      ('Campinas - SP','Cristiano O.','19978453265');
      
      
-- Inserindo registros na tabela productStorage --
INSERT INTO productStorage (idProductStarage, location, quantity) VALUES 
   	(5, 'caixa 1',100),
	(2, 'Estoque X',50),
	(1, 'Prateleira A',10),
	(3, 'Estoque C',100),
	(7, 'Caixa y',10),
	(9, 'Estoque D',60),
    (6, 'Estoque Y', 75),
    (7, 'Caixa 2', 60),
    (10, 'Estoque D', 120),
    (13, 'Prateleira C', 45),
    (14, 'Estoque F', 80),
    (15, 'Caixa 4', 30);

-- Inserindo registros na tabela product_request (relacionamento produto/pedido) --
INSERT INTO product_request (idProduct, idRequest, quantity, status) VALUES
    (2, 1, 1, 'cancelado'),
	(1, 4, 1, 'confirmado'),
    (2, 4, 2, 'confirmado'),
    (4, 4, 3, 'confirmado'),
    (5, 5, 2, 'Processando'),
    (6, 5, 1, 'confirmado'),
    (7, 6, 3, 'Processando'),
    (8, 6, 1, 'confirmado'),
    (9, 7, 2, 'Processando'),
	(12, 10, 2, 'Processando');
    
--  Inserindo registros na tabela de produtos_estoques -- 
INSERT INTO `produtos_estoques` (`fk_idProduct`, `fk_idStorage`, `Qtd`) VALUES 
     ('1','1', '100'),
     ('2','2', '50'),
     ('3','3', '200'),
     ('4','4', '2000');

--  Inserindo registros na tabela de Terceiros/Vendedores --
INSERT INTO `vendedores` (`SocialName`, `AbstName`, `CNPJ`, `CPF`, `Local`, `Contact`) VALUES 
      ('Tech eletronics',NULL,'963258741369852',NULL,'Rio de Janeiro','21963258763'),
      ('Boticário',NULL,'753159852951753',NULL,'São Paulo','11985265453'),
      ('JCNOK',NULL,NULL,'88899966621','Mato Grosso','67952654232'),
      ('Bar do zé',NULL,NULL,'96325875362','São Paulo','11965412548'),
      (`SocialName`, `AbstName`, `CNPJ`, `CPF`, `Local`, `Contact`),
      ('Boutique',NULL,'358952789654111',NULL,'Minas Gerais','31963524122');

--  Inserindo registros na tabela Entregas -- 
INSERT INTO `entregas` (`Cod`, `Status`, `ShipValue`) VALUES 
('1122334455', 'Aguardando', '20'),
('78965412', 'Enviado', '35'),
('123987456', 'Em trânsito', '30'),
('852369741', 'Saiu para entrega', '28'),
('123654852', 'Entregue', '25');

-- Inserindo registros na tabela storage_location --
INSERT INTO storage_location (idProduct, idStorage, location) VALUES
    (1, 1, 'Prateleira A'),
    (2, 2, 'Estoque B'),
    (3, 3, 'Caixa 1'),
    (6, 6, 'Estoque Y'),
    (7, 7, 'Caixa 2'),
    (8, 8, 'Estoque Z'),
    (11, 11, 'Caixa 3'),
    (12, 12, 'Estoque E'),
    (15, 15, 'Caixa 4');

-- Inserindo registros na tabela productSupplier (relacionamento produto/fornecedor) --    
INSERT INTO productSupplier (idSupplier, idProduct, quantity) VALUES
    (1, 2, 50),
    (2, 3, 200),
    (3, 6, 75),
    (4, 7, 60),
    (4, 8, 300),
    (5, 9, 80),
    (5, 10, 120),
    (1, 11, 40),
    (5, 15, 30);
    
--  Inserindo registros na tabela Produtos_Terceiros;
INSERT INTO `produtos_terceiros` (`fk_idSeller`, `fk_idProduct`, `Qtd`) VALUES
    ('1', '2', '10'),
	('2', '3', '5'),
    ('3', '4', '15'),
    ('4', '1', '50');


-- Inserindo registros na tabela delivery --
INSERT INTO delivery (idDelivery, trackingCode, idRequest, status) VALUES
    (2, 'XYZ456', 2, 'Em trânsito'),
    (5, 'JKL345', 6, 'Em trânsito'),
    (6, 'MNO678', 7, 'Entregue'),
    (7, 'PQR901', 8, 'processando'),
    (8, 'STU234', 9, 'Em trânsito'),
    (9, 'VWX567', 10, 'Entregue'),
    (10, 'YZA890', 11, 'processando'),
    (13, 'HIJ789', 14, 'processando'),
    (14, 'KLM012', 15, 'Em trânsito');




-- QUERIES ECOMMERCE REFINADOS --

--  Quantos pedidos foram feitos por cada cliente?
Select Concat(Fname,' ',Lname) as Client, idOrder as Request, orderStatus as Status from clients c, orders o where c.idClient = idOrderClient;
Select Count(*) from clients c, orders o where c.idClient = idOrderClient;

--  Algum vendedor também é fornecedor?
Select * from fornecedores f, vendedores v where f.CNPJ = v.CNPJ;

-- recuperação de pedido com produto associado
select * from clients c 
				inner join orders o ON c.idClient = o.idOrderClient
                inner join productOrder p on p.idPOorder = o.idOrder
		group by idClient; 
        
-- Recuperar quantos pedidos foram realizados pelos clientes?
select c.idClient, Fname, count(*) as Number_of_orders from clients c 
				inner join orders o ON c.idClient = o.idOrderClient
		group by idClient; 

-- Relação de nomes dos fornecedores e nomes dos produtos;
SELECT 
    f.SocialName,
    f.CNPJ,
    f.Contact,
    fp.Qtd,
    p.Name,
    p.ClassKids,
    p.Category,
    p.Size,
    p.Rating,
    p.Price
FROM
    fornecedores f
        JOIN
    fornecedores_produtos fp ON f.id = fp.fk_idSupplier
        JOIN
    produtos p ON fp.fk_idProduct = p.id;
    
  -- Primeiro nome, sobrenome e CPF de todos os clientes pessoa física.
SELECT Fnome, LName, CPF
FROM clients
WHERE clientType = 'PF';

-- Nome do produto, a categoria e a classificação (rating) de todos os produtos cadastrados.
SELECT Pname, category, rating
FROM product;

-- lista dos pedidos com cliente e pagamentos
select  concat(Fnome,' ',Minit,' ',LName) as Client, r.description, r.status, p.typePayment, p.valor 
		from clients c, request r, payments p 	
        where c.idClient = r.idClient and p.idPayment = r.idPayment;

-- Retornar os nomes e CPFs dos clientes que fizeram pedidos confirmados
SELECT Fnome, LName, CPF
FROM clients
WHERE idClient IN (
    SELECT idClient
    FROM request
    WHERE status = 'confirmado'
);

--  Quais produtos o cliente de ordem =1 comprou?
SELECT 
    c.Pname AS Nome,
    p.Date,
    p.NumberPayment,
    prod.Name,
    prod.Price AS preço,
    p.Status AS pagamento,
    pr.Qtd AS qtd,
    prod.Price * pr.Qtd AS total,
    pr.Status
FROM
    pedidos AS p
        JOIN
    pedidos_produtos AS pr ON p.id = pr.fk_idOrder
        JOIN
    clientes c ON c.idClient = p.fk_idClient
        JOIN
    produtos prod ON prod.id = pr.fk_idProduct
WHERE
    p.id = 1;
    
 -- Excluir todos os produtos que não possuem fornecedores na tabela de relacionamento "productSupplier":
DELETE product
FROM product
LEFT JOIN productSupplier ps ON product.idProduct = ps.idProduct
WHERE ps.idProduct IS NULL;

-- Categoria do produto e a soma da quantidade de produtos em estoque para cada categoria
SELECT category, SUM(quantity) AS total_quantity
FROM productSupplier
INNER JOIN product ON productSupplier.idProduct = product.idProduct
GROUP BY category;
  
  
  
  
