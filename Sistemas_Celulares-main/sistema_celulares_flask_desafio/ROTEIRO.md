# Roteiro passo a passo - Sistema da Assistência Técnica de Celulares

## 1. Pré-requisitos

Python 3.10 ou superior, MySQL 8 ou superior e um terminal aberto na pasta do projeto.

## 2. Ambiente virtual e dependências

Linux/macOS:

```bash
cd sistema_celulares_flask_desafio
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Windows PowerShell:

```powershell
cd sistema_celulares_flask_desafio
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## 3. Criar e popular o banco

O arquivo `assistencia_db.sql` recria o banco `assistencia_db` e insere dados de teste.

Linux/macOS:

```bash
mysql -u root -p < assistencia_db.sql
mysql -u root -p -e "USE assistencia_db; SELECT COUNT(*) AS usuarios FROM usuarios; SELECT COUNT(*) AS clientes FROM clientes; SELECT COUNT(*) AS aparelhos FROM aparelhos; SELECT COUNT(*) AS servicos FROM servicos; SELECT COUNT(*) AS agendamentos FROM agendamentos;"
```

Windows PowerShell:

```powershell
Get-Content .\assistencia_db.sql | mysql -u root -p
mysql -u root -p -e "USE assistencia_db; SELECT COUNT(*) AS usuarios FROM usuarios; SELECT COUNT(*) AS clientes FROM clientes; SELECT COUNT(*) AS aparelhos FROM aparelhos; SELECT COUNT(*) AS servicos FROM servicos; SELECT COUNT(*) AS agendamentos FROM agendamentos;"
```

O resultado esperado é 3 registros em cada tabela inicial.

## 4. Configurar a conexão

Linux/macOS:

```bash
export DB_HOST=localhost
export DB_PORT=3306
export DB_USER=root
export DB_PASSWORD="sua_senha_do_mysql"
export DB_NAME=assistencia_db
export SECRET_KEY="uma-chave-longa-e-secreta"
```

Windows PowerShell:

```powershell
$env:DB_HOST="localhost"
$env:DB_PORT="3306"
$env:DB_USER="root"
$env:DB_PASSWORD="sua_senha_do_mysql"
$env:DB_NAME="assistencia_db"
$env:SECRET_KEY="uma-chave-longa-e-secreta"
```

## 5. Testar a conexão

```bash
python testar_conexao.py
```

Resultado esperado:

```text
Conexão OK: {'banco': 'assistencia_db', 'versao': '8.x'}
```

Se falhar, verifique se o MySQL está ativo, se a senha está correta e se o banco foi criado.

## 6. Iniciar o servidor

```bash
python app.py
```

Abra no navegador:

```text
http://localhost:5000
```

## 7. Usuários de teste

| Usuário | Senha |
|---|---|
| `admin` | `admin123` |
| `carlos` | `carlos123` |
| `ana` | `ana12345` |

## 8. Testes principais

- Login vazio: `Informe usuário e senha.`
- Login incorreto: `Usuário ou senha inválidos.`
- Login correto: abre a tela principal.
- Acesso sem login: redireciona para `/login`.
- Clientes: cadastrar, editar, buscar por nome/telefone/e-mail e pesquisar por CPF completo.
- Aparelhos: cadastrar com cliente, validar IMEI com 15 dígitos, editar e excluir.
- Agendamentos: cadastrar, listar por data, reagendar e excluir.
- Exclusão de cliente com aparelhos e aparelho com agendamentos é bloqueada para preservar os relacionamentos.

## 9. Testes por API

Com o servidor executando em `http://localhost:5000`:

```bash
curl -c c.txt -H "Content-Type: application/json" -d '{"login":"admin","senha":"admin123"}' http://localhost:5000/api/login
curl -b c.txt http://localhost:5000/api/teste-conexao
curl -b c.txt "http://localhost:5000/api/clientes?q=maria"
curl -b c.txt http://localhost:5000/api/aparelhos
curl -b c.txt http://localhost:5000/api/agendamentos
```

## 10. Expiração da sessão

A sessão expira após 30 minutos, definido por `EXPIRACAO_SEGUNDOS` em `autenticacao.py`. Para testar rapidamente, altere temporariamente o valor para `60`, faça login, aguarde um minuto e faça uma nova ação.
