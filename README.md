# Banco-de-dados-academia
CREATE DATABASE IF NOT EXISTS academia_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE academia_db;

CREATE TABLE IF NOT EXISTS academia (
  cnpj CHAR(14) NOT NULL,
  nome_fantasia VARCHAR(150) NOT NULL,
  endereco VARCHAR(255) NOT NULL,
  PRIMARY KEY (cnpj),
  CONSTRAINT chk_academia_cnpj CHECK (cnpj REGEXP '^[0-9]{14}$')
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS aluno (
  cpf CHAR(11) NOT NULL,
  nome VARCHAR(150) NOT NULL,
  data_nasc DATE,
  tel VARCHAR(20),
  PRIMARY KEY (cpf),
  CONSTRAINT chk_aluno_cpf CHECK (cpf REGEXP '^[0-9]{11}$')
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS aula (
  id_aula INT NOT NULL AUTO_INCREMENT,
  categoria VARCHAR(100) NOT NULL,
  modalidade VARCHAR(100) NOT NULL,
  PRIMARY KEY (id_aula)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS plano (
  id_plano INT NOT NULL AUTO_INCREMENT,
  nome_plano VARCHAR(100) NOT NULL,
  preco DECIMAL(10,2) NOT NULL,
  PRIMARY KEY (id_plano),
  CONSTRAINT chk_plano_preco CHECK (preco >= 0)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS manutencao (
  registro VARCHAR(30) NOT NULL,
  nome VARCHAR(150) NOT NULL,
  contato VARCHAR(100),
  dados VARCHAR(255),
  cnpj_academia CHAR(14) NOT NULL,
  PRIMARY KEY (registro),
  KEY idx_manutencao_academia (cnpj_academia),
  CONSTRAINT fk_manutencao_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS financeiro (
  registro VARCHAR(30) NOT NULL,
  nome VARCHAR(150) NOT NULL,
  contato VARCHAR(100),
  dados VARCHAR(255),
  cnpj_academia CHAR(14) NOT NULL,
  PRIMARY KEY (registro),
  KEY idx_financeiro_academia (cnpj_academia),
  CONSTRAINT fk_financeiro_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS recepcionista (
  registro VARCHAR(30) NOT NULL,
  nome VARCHAR(150) NOT NULL,
  contato VARCHAR(100),
  dados VARCHAR(255),
  cnpj_academia CHAR(14) NOT NULL,
  PRIMARY KEY (registro),
  KEY idx_recepcionista_academia (cnpj_academia),
  CONSTRAINT fk_recepcionista_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS instrutor (
  cref VARCHAR(30) NOT NULL,
  nome VARCHAR(150) NOT NULL,
  contato VARCHAR(100),
  dados VARCHAR(255),
  cnpj_academia CHAR(14) NOT NULL,
  PRIMARY KEY (cref),
  KEY idx_instrutor_academia (cnpj_academia),
  CONSTRAINT fk_instrutor_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS academia_aula (
  cnpj_academia CHAR(14) NOT NULL,
  id_aula INT NOT NULL,
  PRIMARY KEY (cnpj_academia, id_aula),
  KEY idx_academia_aula_aula (id_aula),
  CONSTRAINT fk_academia_aula_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT fk_academia_aula_aula FOREIGN KEY (id_aula)
    REFERENCES aula (id_aula)
    ON UPDATE CASCADE ON DELETE CASCADE
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS academia_aluno (
  cnpj_academia CHAR(14) NOT NULL,
  cpf_aluno CHAR(11) NOT NULL,
  PRIMARY KEY (cnpj_academia, cpf_aluno),
  KEY idx_academia_aluno_aluno (cpf_aluno),
  CONSTRAINT fk_academia_aluno_academia FOREIGN KEY (cnpj_academia)
    REFERENCES academia (cnpj)
    ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT fk_academia_aluno_aluno FOREIGN KEY (cpf_aluno)
    REFERENCES aluno (cpf)
    ON UPDATE CASCADE ON DELETE CASCADE
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS matricula (
  id_matricula INT NOT NULL AUTO_INCREMENT,
  data_inicio DATE NOT NULL,
  status VARCHAR(30) NOT NULL,
  cpf_aluno CHAR(11) NOT NULL,
  id_plano INT NOT NULL,
  PRIMARY KEY (id_matricula),
  KEY idx_matricula_aluno (cpf_aluno),
  KEY idx_matricula_plano (id_plano),
  CONSTRAINT fk_matricula_aluno FOREIGN KEY (cpf_aluno)
    REFERENCES aluno (cpf)
    ON UPDATE CASCADE ON DELETE RESTRICT,
  CONSTRAINT fk_matricula_plano FOREIGN KEY (id_plano)
    REFERENCES plano (id_plano)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS pagamento (
  id_pagamento INT NOT NULL AUTO_INCREMENT,
  valor DECIMAL(10,2) NOT NULL,
  data_vencimento DATE NOT NULL,
  status_pagamento VARCHAR(30) NOT NULL,
  tipo_pagamento VARCHAR(50) NOT NULL,
  id_matricula INT NOT NULL,
  PRIMARY KEY (id_pagamento),
  KEY idx_pagamento_matricula (id_matricula),
  CONSTRAINT fk_pagamento_matricula FOREIGN KEY (id_matricula)
    REFERENCES matricula (id_matricula)
    ON UPDATE CASCADE ON DELETE RESTRICT,
  CONSTRAINT chk_pagamento_valor CHECK (valor >= 0),
  CONSTRAINT chk_pagamento_status CHECK (status_pagamento IN ('pendente', 'pago', 'atrasado', 'cancelado'))
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS treino (
  id_treino INT NOT NULL AUTO_INCREMENT,
  nome_treino VARCHAR(120) NOT NULL,
  data_criacao DATE NOT NULL,
  cpf_aluno CHAR(11) NOT NULL,
  cref_instrutor VARCHAR(30) NOT NULL,
  PRIMARY KEY (id_treino),
  KEY idx_treino_aluno (cpf_aluno),
  KEY idx_treino_instrutor (cref_instrutor),
  CONSTRAINT fk_treino_aluno FOREIGN KEY (cpf_aluno)
    REFERENCES aluno (cpf)
    ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT fk_treino_instrutor FOREIGN KEY (cref_instrutor)
    REFERENCES instrutor (cref)
    ON UPDATE CASCADE ON DELETE RESTRICT
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS exercicio (
  id_exercicio INT NOT NULL AUTO_INCREMENT,
  nome_exercicios VARCHAR(150) NOT NULL,
  grupo_muscular VARCHAR(100) NOT NULL,
  PRIMARY KEY (id_exercicio)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS treino_exercicio (
  id_treino INT NOT NULL,
  id_exercicio INT NOT NULL,
  PRIMARY KEY (id_treino, id_exercicio),
  KEY idx_treino_exercicio_exercicio (id_exercicio),
  CONSTRAINT fk_treino_exercicio_treino FOREIGN KEY (id_treino)
    REFERENCES treino (id_treino)
    ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT fk_treino_exercicio_exercicio FOREIGN KEY (id_exercicio)
    REFERENCES exercicio (id_exercicio)
    ON UPDATE CASCADE ON DELETE CASCADE
) ENGINE=InnoDB;

