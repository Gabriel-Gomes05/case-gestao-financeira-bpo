# 💼 Case: Sistema de Gestão Financeira para BPO de Clínicas

> **Código privado.** O sistema foi desenvolvido no meu trabalho atual e pertence à empresa. Aqui apresento o problema, a arquitetura e as decisões técnicas, sem expor código nem dados de clientes.

## O problema

Uma operação de **BPO financeiro** atende várias clínicas e consultórios ao mesmo tempo. Antes do sistema, o controle de contas a pagar, os lançamentos e a conciliação bancária ficavam espalhados em planilhas. Isso gerava retrabalho, risco de erro e pouca visibilidade para o fechamento mensal de cada cliente.

## A solução

Uma aplicação web interna que centraliza a operação financeira de todos os clientes em um só lugar, com isolamento total dos dados entre clínicas.

**Principais funcionalidades:**

- **Lançamentos e contas a pagar**, com recorrência, pagamentos parciais e importação em lote por planilha
- **Plano de contas, centros de custo e rateios** por cliente
- **Conciliação bancária e de cartões**, com sugestões automáticas que o operador revisa e confirma
- **Orçamento, DRE, rotinas e fechamento mensal**
- **Exportação de relatórios** e alertas de vencimento
- **Perfis de acesso:** coordenador, editor, funcionário, secretária e médico, cada um vendo só o que precisa

## Arquitetura

```text
Navegador (Jinja2 + JavaScript)
        │
        ▼
FastAPI + Gunicorn/Uvicorn ──► Redis (rate limiting)
   ├─ 21 módulos de rotas por funcionalidade
   ├─ camada de serviços (conciliação, fechamento, exportação, alertas…)
   └─ SQLAlchemy ORM
        │
        ▼
PostgreSQL 15 (dados separados por cliente, campos sensíveis cifrados)

Tudo empacotado com Docker Compose
```

| Camada | Tecnologias |
|---|---|
| Backend | Python 3.11, FastAPI, SQLAlchemy, Uvicorn/Gunicorn |
| Banco | PostgreSQL 15, Alembic |
| Frontend | Jinja2, HTML, CSS, JavaScript |
| Dados | pandas e openpyxl para importações |
| Infra | Docker, Docker Compose, Redis |
| Qualidade | Pytest, Ruff, auditorias automatizadas |

## Decisões técnicas que eu destacaria

**🔐 Segurança e LGPD**
- Campos pessoais e bancários **cifrados na própria aplicação**, com chave separada da chave de autenticação
- Autenticação por **JWT em cookie `HttpOnly`** e **rate limiting** com Redis contra força bruta
- **Autorização validada no servidor** e filtro obrigatório por cliente em todas as consultas, para impedir acesso cruzado entre clínicas
- Logs sem senhas, tokens nem dados bancários; backups cifrados

**🧪 Confiabilidade**
- Suíte de testes automatizados (Pytest) e lint (Ruff) antes de cada publicação
- **Auditores próprios** que verificam tabelas, chaves estrangeiras, relacionamentos do ORM, rotas autenticadas e se algum campo sensível ficou sem criptografia

**📚 Manutenibilidade**
- Monólito modular, pensado para ser fácil de manter por devs em início de carreira
- Documentação de arquitetura, guia de manutenção, referência de funções gerada automaticamente e manual do usuário

## Números do projeto

| | |
|---|---|
| Linhas de Python | ~28 mil |
| Tabelas no modelo de dados | 46 |
| Módulos de rotas | 21 |
| Arquivos de teste | 23 |

## Meu papel

Desenvolvimento do backend e das regras de negócio financeiras, modelagem do banco de dados, implementação da camada de segurança, containerização com Docker e documentação técnica.

## Telas

> Prints com dados fictícios.

<!-- Adicione aqui 3 ou 4 prints com dados de demonstração, por exemplo:
![Dashboard](img/dashboard.png)
![Conciliação bancária](img/conciliacao.png)
![Contas a pagar](img/contas-pagar.png)
-->

---

📫 Quer saber mais sobre o projeto? Fale comigo pelo [meu perfil](https://github.com/Gabriel-Gomes05).
