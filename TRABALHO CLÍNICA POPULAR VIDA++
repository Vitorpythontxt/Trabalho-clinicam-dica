import sqlite3
from datetime import datetime

# Conexão com o banco de dados
conn = sqlite3.connect("clinica.db")
cursor = conn.cursor()

# Criação das tabelas
cursor.execute('''
CREATE TABLE IF NOT EXISTS medicos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT NOT NULL,
    crm TEXT NOT NULL,
    especialidade TEXT NOT NULL
)
''')

cursor.execute('''
CREATE TABLE IF NOT EXISTS pacientes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT NOT NULL,
    cpf TEXT NOT NULL,
    data_nascimento TEXT NOT NULL,
    telefone TEXT NOT NULL
)
''')

cursor.execute('''
CREATE TABLE IF NOT EXISTS consultas (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    paciente_id INTEGER NOT NULL,
    medico_id INTEGER NOT NULL,
    data TEXT NOT NULL,
    observacoes TEXT,
    FOREIGN KEY (paciente_id) REFERENCES pacientes(id),
    FOREIGN KEY (medico_id) REFERENCES medicos(id)
)
''')
conn.commit()

# Limpar tabelas e resetar AUTO_INCREMENT para garantir IDs sequenciais
cursor.execute("DELETE FROM consultas")
cursor.execute("DELETE FROM medicos")
cursor.execute("DELETE FROM pacientes")
cursor.execute("DELETE FROM sqlite_sequence WHERE name IN ('medicos','pacientes','consultas')")
conn.commit()

# Inserir dados de exemplo para testes

def popular_dados_exemplo():
    # Médicos (4 exemplos)
    medicos_exemplo = [
        ("Dra. Ana Beatriz", "CRM1234", "Cardiologia"),
        ("Dr. João Carlos", "CRM5678", "Pediatria"),
        ("Dra. Camila Ribeiro", "CRM9101", "Dermatologia"),
        ("Dr. Lucas Mendes", "CRM3344", "Neurologia")
    ]

    # Pacientes (3 exemplos): adicionada Maria Oliveira conforme especificado
    pacientes_exemplo = [
        ("Maria Oliveira", "56899456094", "1998-08-09", "11985677895"),
        ("Carlos Silva", "12345678900", "1985-06-15", "11986778890"),
        ("Ana Souza", "98768895577", "1990-12-01", "11988756772")
    ]

    cursor.executemany(
        "INSERT INTO medicos (nome, crm, especialidade) VALUES (?, ?, ?)",
        medicos_exemplo
    )
    cursor.executemany(
        "INSERT INTO pacientes (nome, cpf, data_nascimento, telefone) VALUES (?, ?, ?, ?)",
        pacientes_exemplo
    )
    conn.commit()

popular_dados_exemplo()

# ------------------ FUNÇÕES ------------------

def cadastrar_medico():
    nome = input("Nome do médico: ")
    crm = input("CRM: ")
    especialidade = input("Especialidade: ")
    cursor.execute(
        "INSERT INTO medicos (nome, crm, especialidade) VALUES (?, ?, ?)",
        (nome, crm, especialidade)
    )
    conn.commit()
    print("Médico cadastrado com sucesso!")

def cadastrar_paciente():
    nome = input("Nome do paciente: ")
    cpf = input("CPF: ")
    data_nascimento = input("Data de nascimento (YYYY-MM-DD): ")
    telefone = input("Telefone: ")
    cursor.execute(
        "INSERT INTO pacientes (nome, cpf, data_nascimento, telefone) VALUES (?, ?, ?, ?)",
        (nome, cpf, data_nascimento, telefone)
    )
    conn.commit()
    print("Paciente cadastrado com sucesso!")

def agendar_consulta():
    listar_pacientes()
    paciente_id = input("ID do paciente: ")

    listar_medicos()
    medico_id = input("ID do médico: ")

    data_consulta = input("Data da consulta (YYYY-MM-DD): ")
    observacoes = input("Observações (opcional): ")

    cursor.execute(
        "INSERT INTO consultas (paciente_id, medico_id, data, observacoes) VALUES (?, ?, ?, ?)",
        (paciente_id, medico_id, data_consulta, observacoes)
    )
    conn.commit()
    print("Consulta agendada com sucesso!")

def listar_consultas_paciente():
    listar_pacientes()
    paciente_id = input("ID do paciente: ")

    cursor.execute(
        '''
        SELECT c.data, m.nome, c.observacoes 
        FROM consultas c 
        JOIN medicos m ON c.medico_id = m.id 
        WHERE c.paciente_id = ?
        ''', (paciente_id,)
    )
    consultas = cursor.fetchall()

    if consultas:
        for data, medico, obs in consultas:
            print(f"Data: {data} | Médico: {medico} | Obs: {obs}")
    else:
        print("Nenhuma consulta encontrada para este paciente.")

def relatorio_consultas_por_medico():
    cursor.execute(
        '''
        SELECT m.nome, COUNT(*) AS total_consultas 
        FROM consultas c 
        JOIN medicos m ON c.medico_id = m.id 
        GROUP BY m.id
        '''
    )
    resultados = cursor.fetchall()
    for nome, total in resultados:
        print(f"Médico: {nome} | Consultas: {total}")

def listar_medicos():
    print("\n--- Médicos ---")
    cursor.execute("SELECT id, nome, especialidade FROM medicos")
    for id_, nome, esp in cursor.fetchall():
        print(f"[{id_}] {nome} - {esp}")
    print("----------------")

def listar_pacientes():
    print("\n--- Pacientes ---")
    cursor.execute("SELECT id, nome FROM pacientes")
    for id_, nome in cursor.fetchall():
        print(f"[{id_}] {nome}")
    print("-----------------")

# ------------------ MENU PRINCIPAL ------------------

def exibir_menu():
    while True:
        print("\n--- MENU ---")
        print("1. Cadastrar médico")
        print("2. Cadastrar paciente")
        print("3. Agendar consulta")
        print("4. Consultas de um paciente")
        print("5. Relatório de consultas por médico")
        print("6. Listar médicos")
        print("7. Listar pacientes")
        print("0. Sair")

        opcao = input("Escolha: ")

        match opcao:
            case '1': cadastrar_medico()
            case '2': cadastrar_paciente()
            case '3': agendar_consulta()
            case '4': listar_consultas_paciente()
            case '5': relatorio_consultas_por_medico()
            case '6': listar_medicos()
            case '7': listar_pacientes()
            case '0':
                print("Encerrando sistema.")
                break
            case _:
                print("Opção inválida. Tente novamente.")      

# Iniciar o sistema
exibir_menu()
conn.close()
