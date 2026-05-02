<img width="704" height="480" alt="image" src="https://github.com/user-attachments/assets/0fadc4f7-bac0-4a85-abd9-e185bca81a62" />

========================================================================================================

ENTIDADES
Hospede: clientes do hotel.
Quarto: quartos disponíveis para hospedagem.
Reserva: solicitação de uso de um quarto em determinado período.
Hospedagem: estadia do hóspede.
Pagamento: pagamentos realizados durante a hospedagem.
Funcionario: colaboradores do hotel.
Avaliacao: opinião do cliente após a hospedagem.
Servico_Extra: serviços adicionais oferecidos pelo hotel.

==========================================================================================================

RELACIONAMENTO
Um hóspede realiza reservas, podendo fazer várias reservas ao longo do tempo,
Uma reserva aloca um quarto, sendo cada reserva associada a um único quarto,
Uma reserva pode gerar uma hospedagem, representando a estadia do cliente,
Um funcionário atende hospedagens, sendo responsável pelo suporte ao cliente,
Uma hospedagem possui pagamentos, podendo ter um ou mais registros financeiros,
Uma hospedagem recebe avaliações, feitas pelos clientes após a estadia.

===========================================================================================================

REGRA DE NEGOCIO
Um hóspede pode realizar várias reservas, mas cada reserva pertence a um único hóspede,
Um quarto pode ser reservado várias vezes, desde que em períodos diferentes,
Uma reserva está associada a apenas um quarto,
Nem toda reserva necessariamente resulta em uma hospedagem,
Toda hospedagem deve estar vinculada a uma reserva,
Um funcionário pode atender várias hospedagens, mas cada hospedagem possui um responsável,
Uma hospedagem pode ter um ou mais pagamentos registrados,
A avaliação é opcional e ocorre após a hospedagem,
Serviços extras só podem ser utilizados durante uma hospedagem.

============================================================================================================
DOCUMENTAÇÃO DO BANCO DE DADOS 

  O desenvolvimento do banco de dados do projeto Hotel Nebula foi realizado com o auxílio do XAMPP,
utilizado para gerenciar o servidor MySQL, e do MySQL Workbench, ferramenta utilizada para modelagem
e execução dos comandos SQL.
  Inicialmente, foi criada a base de dados do sistema, seguindo a proposta definida na etapa de mode-
lagem conceitual (Diagrama ER). Em seguida, foram implementadas as tabelas no banco de dados, respei-
tando a estrutura previamente planejada, com definição de chaves primárias, chaves estrangeiras e tipo
de dados adequados para cada atributo.
  Após a criação das tabelas, foram inseridos dados simulados em todas elas, com o objetivo de testar 
o funcionamento do sistema e validar os relacionamentos entre as entidades.
  Por fim, foram realizadas consultas SQL no banco de dados, permitindo extrair informações relevantes 
e responder às questões propostas, como disponibilidade de quartos, faturamento, análise de reservas e 
desempenho operacional do hotel.

==================================================================================================================

BANCO DE DADOS

create database hotel_Nebulosa;
use hotel_Nebulosa;


create table hospede (
    id_hospede int primary key auto_increment,
    nome varchar(100) not null,
    cpf varchar(14) unique,
    telefone varchar(20),
    email varchar(100)
);

create table quarto (
    id_quarto int primary key auto_increment,
    numero int not null,
    tipo varchar(50),
    preco decimal(10,2),
    status_quarto varchar(20)
);

create table reserva (
    id_reserva int primary key auto_increment,
    data_inicio date,
    data_fim date,
    data_reserva date,
    status_reserva varchar(20),
    id_hospede int,
    id_quarto int,

    foreign key (id_hospede) references hospede(id_hospede),
    foreign key (id_quarto) references quarto(id_quarto)
);

create table funcionario (
    id_funcionario int primary key auto_increment,
    nome varchar(100),
    cargo varchar(50)
);

create table hospedagens (
    id_hospedagens int primary key auto_increment,
    checkin date,
    checkout date,
    id_reserva int,
    id_funcionario int,

    foreign key (id_reserva) references reserva(id_reserva),
    foreign key (id_funcionario) references funcionario(id_funcionario)
);

create table pagamento (
    id_pagamento int primary key auto_increment,
    valor decimal(10,2),
    data_pagamento date,
    metodo varchar(50),
    status_pagamento varchar(20),
    id_hospedagens int,

    foreign key (id_hospedagens) references hospedagens(id_hospedagens)
);

create table avaliacao (
    id_avaliacao int primary key auto_increment,
    nota int,
    comentario text,
    id_hospedagens int,

    foreign key (id_hospedagens) references hospedagens(id_hospedagens)
);

create table servico_extra (
    id_servico int primary key auto_increment,
    nome varchar(100),
    preco decimal(10,2)
);

insert into hospede (nome, cpf, telefone, email) values
('joao pereira', '111.222.333-44', '11988887777', 'joao@email.com'),
('maria oliveira', '222.333.444-55', '11977776666', 'maria@email.com'),
('lucas santos', '333.444.555-66', '11966665555', 'lucas@email.com');


insert into quarto (numero, tipo, preco, status_quarto) values
(102, 'standard', 200.00, 'disponivel'),
(103, 'luxo', 400.00, 'ocupado'),
(104, 'suite', 600.00, 'disponivel');

insert into funcionario (nome, cargo) values
('fernanda lima', 'recepcionista'),
('roberto alves', 'gerente'),
('juliana costa', 'camareira');

insert into reserva (data_inicio, data_fim, data_reserva, status_reserva, id_hospede, id_quarto) values
('2026-06-01', '2026-06-05', '2026-05-20', 'confirmada', 2, 2),
('2026-06-10', '2026-06-12', '2026-05-25', 'cancelada', 3, 3),
('2026-06-15', '2026-06-20', '2026-05-28', 'confirmada', 1, 2);

insert into hospedagens (checkin, checkout, id_reserva, id_funcionario) values
('2026-06-01', '2026-06-05', 4, 2),
('2026-06-15', '2026-06-20', 6, 1);

insert into pagamento (valor, data_pagamento, metodo, status_pagamento, id_hospedagens) values
(800.00, '2026-06-01', 'pix', 'pago', 7),
(3000.00, '2026-06-15', 'cartao', 'pendente', 8);


insert into avaliacao (nota, comentario, id_hospedagens) values
(4, 'boa estadia', 8),
(5, 'perfeito, recomendo!', 7);

select * from quarto;
select * from reserva;
select * from hospedagens;

-- para saber quais quartos estaos vazios
select * from quarto
where id_quarto not in (
    select id_quarto from reserva
    where '2026-06-03' between data_inicio and data_fim
);


-- hospedes que mais reservam (quebrei a cabeça pra fazer esse)
select h.nome, count(r.id_reserva) as total_reservas
from hospede h
join reserva r on h.id_hospede = r.id_hospede
group by h.nome
order by total_reservas desc;


-- faturamento por mes
select month(data_pagamento) as mes, sum(valor) as faturamento
from pagamento
group by mes;

-- servicos que foram usados
select nome, preco
from servico_extra
order by preco desc;

-- quarto com melhor availiação 
select q.numero, avg(a.nota) as media
from quarto q
join reserva r on q.id_quarto = r.id_quarto
join hospedagens h on r.id_reserva = h.id_reserva
join avaliacao a on h.id_hospedagens = a.id_hospedagens
group by q.numero
order by media desc;

-- reservas canceladas 
select * from reserva
where status_reserva = 'cancelada';


-- reservas atendidas pelos funcionarios
select f.nome, count(h.id_hospedagens) as total
from funcionario f
join hospedagens h on f.id_funcionario = h.id_funcionario
group by f.nome;


