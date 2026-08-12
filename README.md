# ChefePDV

![CI](https://github.com/ItamarJuniorDEV/chefepdv-system/actions/workflows/ci.yml/badge.svg)
![License](https://img.shields.io/badge/License-MIT-green)

Sistema de ponto de venda em PHP puro, com controle de caixa, estoque, vendas, relatórios e acesso por perfil.

## Sobre

O ChefePDV cobre o fluxo principal de uma operação de caixa: abertura e fechamento, lançamento de itens, finalização e cancelamento de vendas, atualização de estoque, relatórios e gestão de usuários.

O projeto foi desenvolvido sem framework no backend para trabalhar diretamente com roteamento, sessões, autorização, PDO, transações e organização em camadas.

## Screenshots

| | |
|---|---|
| ![Login](assets/img/1.login.png) | ![Frente de caixa](assets/img/3.FrenteCaixa.png) |
| ![Dashboard](assets/img/2.dashboard.png) | ![Relatórios](assets/img/5.relatorios.png) |

## Funcionalidades

### Caixa e vendas
- Abertura e fechamento de caixa
- Frente de caixa com busca por nome ou código de barras
- Pagamentos em dinheiro, crédito, débito e PIX
- Desconto e cálculo de troco
- Baixa de estoque durante a venda
- Cancelamento com estorno de estoque

### Cadastros
- Produtos, categorias e estoque mínimo
- Clientes com CPF/CNPJ, endereço e telefone
- Usuários com perfis `admin`, `gerente` e `operador`

### Relatórios
- Faturamento e ticket médio
- Histórico de vendas
- Situação do estoque
- Produtos mais vendidos
- Distribuição por forma de pagamento
- Exportação para impressão/PDF

### Integrações
- ViaCEP para consulta de endereço
- ReceitaWS para dados de CNPJ
- BrasilAPI para feriados nacionais

## Stack

| Camada | Tecnologia |
|---|---|
| Backend | PHP 7.4+, PDO |
| Banco | MySQL 8.0 |
| Frontend | Bootstrap 5, jQuery, AJAX |
| Infra | Docker, Apache, GitHub Actions |
| Testes | PHPUnit 9 |

## Como rodar

Pré-requisitos: Docker e Docker Compose.

```bash
git clone https://github.com/ItamarJuniorDEV/chefepdv-system.git
cd chefepdv-system
cp .env.example .env
docker compose up -d
```

A aplicação fica disponível em `http://localhost:8081`.

O banco é inicializado na primeira execução com dados de demonstração e um usuário administrador:

| Perfil | E-mail | Senha |
|---|---|---|
| Administrador | admin@pdv.com | admin123 |

Essas credenciais são apenas para ambiente local/demonstração.

## Modelo de dados

- `usuarios`: perfil e estado de acesso
- `categorias`: classificação dos produtos
- `produtos`: preço, estoque atual e estoque mínimo
- `clientes`: dados cadastrais vinculáveis às vendas
- `caixas`: turnos, saldo inicial, esperado e real
- `vendas`: caixa, cliente opcional, valores e status
- `venda_itens`: itens e preço praticado no momento da venda
- `login_attempts`: tentativas usadas no rate limiting do login

## Segurança e integridade

- PDO com prepared statements reais
- CSRF em operações POST
- Rate limiting no login
- Senhas com `password_hash` / `password_verify`
- Regeneração de sessão após autenticação
- Controle de acesso por perfil nas rotas
- Transações para operações críticas de venda e cancelamento
- `SELECT ... FOR UPDATE` na baixa de estoque para evitar concorrência sobre a mesma quantidade disponível

## Testes

```bash
composer install
vendor/bin/phpunit
```

A suíte cobre autenticação, perfis de acesso, controllers, vendas, itens, clientes, produtos e respostas da API. O mesmo conjunto é executado pelo GitHub Actions.

## Decisões técnicas

- **PHP sem framework:** o projeto mantém explícitos roteamento, sessão, autorização e acesso a dados.
- **Front controller:** as requisições passam por um ponto central de resolução de rota e acesso.
- **PDO sem ORM:** as consultas ficam explícitas e parametrizadas.
- **Lock pessimista no estoque:** reduz o risco de duas vendas consumirem simultaneamente a última unidade disponível.
- **Preço histórico:** `preco_unitario` é persistido em `venda_itens`, evitando que alterações futuras no produto modifiquem vendas antigas.
- **Cancelamento auditável:** a venda é mantida com status de cancelada e o estoque é estornado.

## Licença

MIT
