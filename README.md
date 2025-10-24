# 🧭 Desafio Técnico — Sistema de Gestão de Almoxarifado

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Framework-Django-brightgreen?logo=django&logoColor=white)
![Postgres](https://img.shields.io/badge/Database-PostgreSQL-blue?logo=postgresql&logoColor=white)
![Tests](https://img.shields.io/badge/Tests-Required-success)
![Difficulty](https://img.shields.io/badge/Level-Junior-yellow)

---

## 📘 Contexto
Você foi convidado a desenvolver um **sistema de gestão de almoxarifado** (controle de estoque) para uma empresa que precisa acompanhar **entradas e saídas de materiais**, controlar **níveis mínimos** e **emitir alertas automáticos** quando o estoque estiver baixo.

O desafio tem como foco principal avaliar:
- Sua **lógica de negócio** e **organização de código**;  
- Clareza e legibilidade;  
- Capacidade de **modelar dados e escrever testes**;  
- Documentação e experiência de uso (DX).

---

## 🎯 Objetivo
Criar uma aplicação (API ou app web) capaz de:

- 📦 Cadastrar e gerenciar **itens** do almoxarifado  
- ➕ Registrar **entradas** e **saídas** de estoque  
- 🧮 Calcular o **saldo atual** de cada item  
- 🚨 Gerar **alertas automáticos** quando o saldo ≤ estoque mínimo  
- 📊 Expor um **relatório** com movimentações recentes  

---

## 🧩 Funcionalidades Obrigatórias

### 1️⃣ Cadastro de Itens
- Campos obrigatórios:
  - `id` (chave primária)
  - `uuid` (gerado automaticamente via `uuid4`)
  - `nome`
  - `unidade` (ex: un, cx, kg)
  - `estoque_minimo` (int)
  - `ativo` (bool)
- CRUD completo (criar, listar, atualizar e desativar itens).

---

### 2️⃣ Movimentação de Estoque
- Tipos possíveis: `ENTRADA`, `SAIDA`, `AJUSTE`.
- Campos obrigatórios:
  - `id`
  - `item_uuid`
  - `tipo`
  - `quantidade` (> 0)
  - `observacao`
  - `criado_em`
- **Regras de negócio:**
  - ❌ Uma **SAÍDA** não pode deixar o saldo negativo.
  - ⚙️ Um **AJUSTE** pode adicionar ou remover estoque (documente sua lógica).
  - Cada movimentação deve alterar o saldo do item.

---

### 3️⃣ Consulta de Saldo
- Endpoint ou página para consultar o **saldo atual** de todos os itens.
- Deve permitir filtros:
  - `ativos = true/false`
  - `abaixo_do_minimo = true`
  - busca por `nome`

---

### 4️⃣ Alertas de Baixo Estoque
- Quando um item atingir ou ficar abaixo do estoque mínimo:
  - Criar um registro em `low_stock_alerts`;
  - Permitir listar alertas pendentes.

---

### 5️⃣ Relatório de Estoque
- Endpoint que retorne:
  - O **saldo atual** de cada item;
  - A **quantidade movimentada nos últimos 7 dias** (agrupada por tipo).

---

## 🧱 Modelo de Dados (sugestão)

| Tabela | Campos principais |
|--------|-------------------|
| **items** | `id`, `uuid`, `nome`, `unidade`, `estoque_minimo`, `ativo`, `criado_em` |
| **stock_moves** | `id`, `item_uuid`, `tipo`, `quantidade`, `observacao`, `criado_em` |
| **low_stock_alerts** | `id`, `item_uuid`, `saldo_no_momento`, `estoque_minimo`, `criado_em`, `resolvido` |

> 🔹 O saldo pode ser calculado dinamicamente via somatório das movimentações  
> ou armazenado em um campo `saldo_atual` para otimização.  
> 📄 **Documente a abordagem escolhida.**

---

## ⚙️ Requisitos Técnicos

- API REST (Django REST Framework, FastAPI, Flask, etc.) **ou** app web simples.  
- Banco de dados: **SQLite** ou **PostgreSQL**.  
- Testes automatizados (mínimo das regras principais).  
- Scripts de seed com dados de exemplo.  
- Tudo deve ser executável localmente via `docker compose up`.

---

## 🧪 Testes obrigatórios

| Cenário | O que validar |
|----------|----------------|
| ✅ Cadastro de itens | Criar, listar e atualizar itens |
| ✅ Movimentação | Regras de saldo e bloqueio de saídas inválidas |
| ✅ Alertas | Geração automática ao atingir mínimo |
| ✅ Relatório | Cálculo correto de movimentações nos últimos 7 dias |

---

## 💾 Dados de Exemplo (para seed)

**items.csv**
```
uuid,nome,unidade,estoque_minimo,ativo
2d1d6d4a-2ad0-4f38-bf12-5c2e3e6b81b9,Caneta Azul BIC,un,50,true
f8d2392c-cc15-45a2-8c1d-8a962ba713b7,Papel A4 500fls,un,20,true
ac5f12df-8f9a-4e21-923a-1bb03bb6ef54,Clipes 100un,un,30,true
```

**stock_moves.csv**
```
item_uuid,tipo,quantidade,observacao
2d1d6d4a-2ad0-4f38-bf12-5c2e3e6b81b9,ENTRADA,200,Compra inicial
f8d2392c-cc15-45a2-8c1d-8a962ba713b7,ENTRADA,50,Compra inicial
2d1d6d4a-2ad0-4f38-bf12-5c2e3e6b81b9,SAIDA,30,Uso interno
ac5f12df-8f9a-4e21-923a-1bb03bb6ef54,AJUSTE,10,Inventário
```

---

## 🚀 Como Rodar

```bash
# Clone o repositório
git clone https://github.com/seuusuario/desafio-almoxarifado.git
cd desafio-almoxarifado

# Crie o ambiente e suba o container
docker compose up --build

# (opcional) Rode os seeds
python manage.py loaddata seeds/items.json

# Execute os testes
pytest -v
```

---

## 📡 Exemplos de Endpoints (API)

```bash
# Criar item
POST /api/items/
{
  "nome": "Papel A4",
  "unidade": "un",
  "estoque_minimo": 20
}

# Registrar movimentação
POST /api/moves/
{
  "item_uuid": "f8d2392c-cc15-45a2-8c1d-8a962ba713b7",
  "tipo": "SAIDA",
  "quantidade": 10,
  "observacao": "Uso interno"
}

# Consultar saldos
GET /api/items?situacao=abaixo_do_minimo

# Listar alertas
GET /api/alerts/
```

---

## 🧮 Critérios de Avaliação

| Critério | Peso | Descrição |
|-----------|------|-----------|
| **Regras de negócio** | 40% | Implementação correta das regras de estoque e alertas |
| **Organização e clareza do código** | 25% | Estrutura, nomes, comentários e docstrings |
| **Testes automatizados** | 20% | Cobertura das regras principais |
| **Experiência de uso (README + Setup)** | 15% | Documentação clara, facilidade de execução |

---

## 🧠 Dicas
- Foque em **clareza** e **boas práticas**.
- Prefira **nomes claros** a comentários longos.
- Testes simples e funcionais valem mais que features inacabadas.
- Documente **decisões e trade-offs** no README.

---

## 🏁 Bônus (opcional)
| Extra | Descrição |
|--------|------------|
| 🔹 Upload de CSV de itens | Endpoint para importação em massa |
| 🔹 Filtros e paginação | Melhorar experiência de consulta |
| 🔹 Autenticação simples | Usuário e senha para CRUD |
| 🔹 Interface Web leve | Django Admin ou template Jinja |
| 🔹 Diagrama ER | Inserir no README (PNG/SVG) |

---

## 📅 Prazo Sugerido
🕐 **3 dias úteis** após o recebimento do desafio.

---

💡 *Boa sorte! Mostre como você organiza seu raciocínio e estrutura seu código. Um bom desenvolvedor júnior é aquele que entende as regras do negócio e entrega com clareza e consistência.* 🚀
