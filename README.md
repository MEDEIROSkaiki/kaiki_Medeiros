Sistema de Gestão de Estoque 

Este projeto consiste em um sistema web completo para o gerenciamento de estoque, desenvolvido utilizando o framework **Django**.

O foco principal é o controle de inventário (Cadastro de Produtos) e o rastreamento detalhado de todas as movimentações (Entrada e Saída), garantindo a rastreabilidade do usuário responsável por cada transação.

---

## 1. Tecnologias e Infraestrutura (Entrega 9)

| Componente | Detalhe |
| :--- | :--- |
| **Framework** | Django 5.2.7 |
| **Linguagem** | Python 3 (Requisito mínimo para Django 5.2.7) |
| **Banco de Dados (SGBD)** | SQLite 3 (Utilizando o `django.db.backends.sqlite3`) |
| **Autenticação** | Sistema nativo de `django.contrib.auth` |
| **Ambiente/SO** | Compatível com Linux/macOS. Desenvolvido em Windows. |

---

## 2. Diagrama Entidade-Relacionamento (DER) (Entrega 2)

O modelo de dados é centrado na tabela de movimentações, que se conecta tanto aos produtos quanto aos usuários, garantindo a rastreabilidade do histórico.

| Entidade | Chave Primária (PK) | Relacionamentos (Chaves Estrangeiras - FK) |
| :--- | :--- | :--- |
| **`produtos_produto`** | `id` | N/A |
| **`produtos_movimentacaoestoque`** | `id` | `produto_id` (N:1 com `produtos_produto`), `responsavel_id` (N:1 com `auth_user`) |
| **`auth_user`** | `id` | N/A |

---

## 3. Lista de Requisitos Funcionais (Entrega 1)

Abaixo, detalhamento de como os requisitos funcionais solicitados são atendidos pelo projeto:

### 3.1 Interface Principal do Sistema (5)

| Requisito | Status / Implementação |
| :--- | :--- |
| Exibir o nome do usuário logado | Implementado em `principal.html`: `Olá, {{ usuario.username }}!`. |
| Meio para fazer logout | Implementado com o link `Sair` (`{% url 'logout' %}`). |
| Meio de acesso a Interfaces | Implementado via links na tag `<nav>` em `principal.html`. |

### 3.2 Interface Cadastro de Produto (6)

| Requisito | Status / Implementação |
| :--- | :--- |
| Listar produtos e ordenação (6.1.1/6.1.2) | O `ProdutoAdmin` já exibe os campos principais. A view de listagem (`lista_produtos`) deve listar a tabela. |
| Campo de busca (6.1.2) | Implementado no painel Admin, que usa `search_fields = ('nome', 'descricao')`. |
| Inserir, Editar, Excluir Produto (6.1.3, 6.1.4, 6.1.5) | O `ProdutoForm` define os campos necessários. |
| Validações (6.1.6) | O modelo `Produto` define `quantidade_estoque` e `estoque_minimo` como `PositiveIntegerField`, garantindo validade de dados numéricos positivos. |

### 3.3 Interface Gestão de Estoque (7)

| Requisito | Status / Implementação |
| :--- | :--- |
| Histórico de Movimentação (7.1.5) | O modelo `MovimentacaoEstoque` armazena todas as transações (produto, quantidade, tipo). |
| Tipos de Movimentação (Entrada/Saída) (7.1.2) | O campo `tipo` utiliza `choices=(('E', 'Entrada'), ('S', 'Saída'))`. |
| Rastreio de Responsável e Data (7.1.4/7.1.5) | O campo `responsavel` (`ForeignKey` para `auth_user`) e o campo `data` (`auto_now_add=True`) garantem o registro completo da transação. |
| Alerta Automático (Estoque Mínimo) (7.1.4) | A propriedade `@property abaixo_do_minimo` em `Produto` realiza a checagem lógica de estoque. |

---

## 4. Implementação de Interfaces e Autenticação

### 4.1 Interface de Autenticação de Usuários (Login) (Entrega 4)

| Detalhe | Status / Implementação |
| :--- | :--- |
| **URL de Acesso** | `path('', auth_views.LoginView.as_view(), name='login')` (URL raiz do projeto). |
| **Redirecionamento Pós-Login** | Configurado em `settings.py`: `LOGIN_REDIRECT_URL = '/principal/'`. |
| **Redirecionamento Pós-Logout** | Configurado em `settings.py`: `LOGOUT_REDIRECT_URL = '/'`. |

### 4.2 Interface Principal do Sistema (Entrega 5)

A interface principal (`principal.html`) é a primeira tela que o usuário vê após o login e é protegida pelo decorador `@login_required`.

### 4.3 Gestão de Estoque (Entrega 7)

A interface de histórico (`/produtos/historico/`) lista as transações, e o `MovimentacaoEstoqueAdmin` no painel de administração permite listar e filtrar o histórico por `tipo` e `data`, além de exibir o `responsavel`.

---

## 5. Descrição de Casos de Teste de Software (Entrega 8)

| Caso de Teste | Requisito | Ação | Resultado Esperado |
| :--- | :--- | :--- | :--- |
| **CT-LOGIN-01** | Autenticação (4) | Tentar acessar a página `/principal/` sem estar logado. | Redirecionamento automático para a URL de login (`/`). |
| **CT-HIST-02** | Rastreio de Responsável (7) | Usuário faz uma "Entrada" de 50 unidades. Consultar a tabela `MovimentacaoEstoque`. | Deve existir um registro com `tipo='E'`, a quantidade 50 e o `responsavel` correto. |
| **CT-ALERTA-03** | Estoque Mínimo (7) | Definir `estoque_minimo=5` para Produto 'X'. Fazer uma 'Saída' que deixe o estoque em 4. | A propriedade `abaixo_do_minimo` para o Produto 'X' deve retornar `True`. |
| **CT-LOGOUT-04** | Logout (5) | Clicar no link "Sair" na página principal. | Redirecionamento para a tela de login (`/`). |

---

## 6. Como Executar o Projeto Localmente

1.  **Clone o repositório:**
    ```bash
    git clone [https://github.com/MEDEIROSkaiki/kaiki_Medeiros.git](https://github.com/MEDEIROSkaiki/kaiki_Medeiros.git)
    cd kaiki_medeiros
    ```

2.  **Ative o Ambiente Virtual (`venv`).**

3.  **Instale o Django** (versão 5.2.7 ou superior) e dependências necessárias.

4.  **Execute as Migrações** (Criação das tabelas no `saep_db.sqlite3`):
    ```bash
    python manage.py migrate
    ```

5.  **Crie um Superusuário** (Para acessar o /admin/):
    ```bash
    python manage.py createsuperuser
    
    ```

6.  **Inicie o Servidor:**
    ```bash
    python manage.py runserver
    ```

O sistema estará acessível em: `http://127.0.0.1:8000/`.

1. Estrutura de Dados e Rastreabilidade (DER)
O sistema foi desenhado para garantir a rastreabilidade total de todas as transações, associando movimentações a produtos e usuários.

Diagrama Entidade-Relacionamento (DER - Entrega 2)
O modelo é centrado na tabela de movimentações, que se conecta tanto aos produtos quanto aos usuários (auth_user), utilizando chaves estrangeiras.
Entidade,Chave Primária (PK),Relacionamentos (Chaves Estrangeiras - FK)
produtos_produto,id,N/A
produtos_movimentacaoestoque,id,"produto_id (N:1 com produtos_produto), responsavel_id (N:1 com auth_user)"
auth_user,id,N/A

Rastreio de Responsável
O requisito de rastrear o usuário responsável é atendido pelo modelo MovimentacaoEstoque:

Rastreio: O campo responsavel é uma ForeignKey para auth_user.

Data: O campo data (auto_now_add=True) registra o momento exato da transação.

2. Interface de Autenticação e Mapeamento de Rotas
Autenticação (Login/Logout - Entrega 4)
A autenticação utiliza o sistema nativo do Django (django.contrib.auth), garantindo segurança e aderência.

Detalhe	Implementação
URL de Acesso	path('', auth_views.LoginView.as_view(), name='login') (URL raiz do projeto).
Redirecionamento Pós-Login	Configurado em settings.py: LOGIN_REDIRECT_URL = '/principal/'.
Redirecionamento Pós-Logout	Configurado em settings.py: LOGOUT_REDIRECT_URL = '/'.

Exportar para as Planilhas

Mapeamento de Rotas Essenciais (URLs)
URL	Funcionalidade Principal	Proteção
/	Tela de Login	Pública
/principal/	Página Principal (Boas-vindas ao usuário)	Requer Login
/logout/	Encerra a sessão	Requer Login
/produtos/historico/	Gestão de Estoque (Histórico de Movimentações)	Requer Login
/produtos/lista/	Cadastro de Produto (Listagem)	Requer Login

Exportar para as Planilhas

3. Interface Cadastro de Produto (Entrega 6)
A interface de Cadastro de Produto é baseada no modelo Produto e no ProdutoForm.

Requisito	Implementação
CRUD	O ProdutoForm define os campos (nome, descricao, quantidade_estoque, estoque_minimo) necessários para Inserir e Editar.
Listagem e Busca	A view de listagem deve exibir a tabela. No painel Admin, o campo de busca está implementado com search_fields = ('nome', 'descricao').
Validações	Os campos quantidade_estoque e estoque_minimo são definidos como PositiveIntegerField, garantindo validade de dados numéricos positivos.

Exportar para as Planilhas

4. Interface Gestão de Estoque (Entrega 7)
Esta interface é centrada no controle de entradas e saídas e nos alertas de estoque.

Requisito	Implementação
Histórico de Movimentação (7.1.5)	O modelo MovimentacaoEstoque armazena todas as transações (produto, quantidade, tipo).
Tipos de Movimentação (7.1.2)	O campo tipo usa choices=(('E', 'Entrada'), ('S', 'Saída')).
Alerta Automático (7.1.4)	A propriedade @property abaixo_do_minimo em Produto checa se quantidade_estoque < estoque_minimo.
Admin View	O MovimentacaoEstoqueAdmin permite filtrar o histórico por tipo e data, além de exibir o responsavel.
