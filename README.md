# git-init-git-add-.-git-commit--m-Primeiro-commit
O que o projeto faz: Permite adicionar, listar, atualizar e deletar tarefas.  Salva tudo em um banco de dados SQLite local.  Código organizado e fácil de entender.  Ideal para mostrar seus conhecimentos em banco de dados, Python e boas práticas.
todo_app/
│
├── app.py                # Script principal com a lógica da aplicação
├── models.py             # Definição do modelo de dados (tarefa)
├── database.py           # Configuração do banco de dados
├── requirements.txt      # Dependências do projeto
└── README.md             # Documentação do projeto
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

engine = create_engine('sqlite:///todo.db')
Session = sessionmaker(bind=engine)
Base = declarative_base()
from sqlalchemy import Column, Integer, String, Boolean
from database import Base

class Tarefa(Base):
    __tablename__ = 'tarefas'

    id = Column(Integer, primary_key=True, autoincrement=True)
    descricao = Column(String, nullable=False)
    concluida = Column(Boolean, default=False)

    def __repr__(self):
        status = "✔" if self.concluida else "✘"
        return f"<Tarefa(id={self.id}, descricao='{self.descricao}', concluida={status})>"
from database import engine, Session, Base
from models import Tarefa

def criar_banco():
    Base.metadata.create_all(engine)

def adicionar_tarefa(session, descricao):
    tarefa = Tarefa(descricao=descricao)
    session.add(tarefa)
    session.commit()
    print(f"Tarefa '{descricao}' adicionada!")

def listar_tarefas(session):
    tarefas = session.query(Tarefa).all()
    if not tarefas:
        print("Nenhuma tarefa cadastrada.")
        return
    for tarefa in tarefas:
        status = "✔" if tarefa.concluida else "✘"
        print(f"[{status}] {tarefa.id}: {tarefa.descricao}")

def marcar_concluida(session, tarefa_id):
    tarefa = session.query(Tarefa).filter_by(id=tarefa_id).first()
    if tarefa:
        tarefa.concluida = True
        session.commit()
        print(f"Tarefa {tarefa_id} marcada como concluída.")
    else:
        print(f"Tarefa {tarefa_id} não encontrada.")

def deletar_tarefa(session, tarefa_id):
    tarefa = session.query(Tarefa).filter_by(id=tarefa_id).first()
    if tarefa:
        session.delete(tarefa)
        session.commit()
        print(f"Tarefa {tarefa_id} deletada.")
    else:
        print(f"Tarefa {tarefa_id} não encontrada.")

def main():
    criar_banco()
    session = Session()

    while True:
        print("\nGerenciador de Tarefas")
        print("1 - Listar tarefas")
        print("2 - Adicionar tarefa")
        print("3 - Marcar tarefa como concluída")
        print("4 - Deletar tarefa")
        print("0 - Sair")

        escolha = input("Escolha uma opção: ")

        if escolha == '1':
            listar_tarefas(session)
        elif escolha == '2':
            desc = input("Descrição da tarefa: ")
            adicionar_tarefa(session, desc)
        elif escolha == '3':
            tarefa_id = int(input("ID da tarefa para concluir: "))
            marcar_concluida(session, tarefa_id)
        elif escolha == '4':
            tarefa_id = int(input("ID da tarefa para deletar: "))
            deletar_tarefa(session, tarefa_id)
        elif escolha == '0':
            print("Saindo...")
            break
        else:
            print("Opção inválida. Tente novamente.")

if __name__ == "__main__":
    main()
# Gerenciador de Tarefas com Banco de Dados (SQLite + SQLAlchemy)

Projeto simples em Python para gerenciar tarefas usando banco de dados SQLite e SQLAlchemy.

## Funcionalidades

- Adicionar tarefas
- Listar tarefas
- Marcar tarefas como concluídas
- Deletar tarefas

## Como rodar

1. Clone o repositório
2. Instale as dependências: `pip install -r requirements.txt`
3. Rode o script principal: `python app.py`

## Tecnologias usadas

- Python 3
- SQLite
- SQLAlchemy (ORM)
