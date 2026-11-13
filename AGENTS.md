# AGENTS.md — Regras do projeto TechPath

Este arquivo vale para **qualquer pessoa ou agente de IA** que altere este repositório.
Antes de codar, leia `PRODUCT.md` (o que construir) e `PLANO.md` (em que ordem).

## 1. Objetivo do projeto

Aplicação web educacional (TechPath) para estudantes de 7 a 17 anos explorarem áreas de
tecnologia, iniciarem trilhas e acompanharem o próprio progresso.
O escopo é **somente o MVP** descrito em `PRODUCT.md` (F1 a F5). Nada de funcionalidades futuras.

## 2. Tecnologias permitidas

| Camada | Permitido | Observação |
|---|---|---|
| Servidor | PHP 8.2 (XAMPP) | sem frameworks, sem Composer |
| Banco | MySQL / MariaDB (XAMPP) | acesso via PDO |
| Estrutura | HTML5 | páginas renderizadas no servidor |
| Estilo | CSS3 puro (um arquivo) | sem pré-processador, sem Tailwind/Bootstrap |
| Interação | JavaScript puro (ES2020) | sem jQuery, sem React/Vue, sem bundler |
| Testes | `php -l` + checklist manual | sem PHPUnit (não há Composer) |

**Proibido adicionar:** frameworks PHP (Laravel, Slim...), Composer, npm, ORM, bibliotecas
via CDN, geradores de código, Docker. Se achar que precisa de algo fora desta lista,
pare e pergunte antes.

## 3. Ambiente

- Windows + XAMPP; projeto em `C:\xampp\htdocs\controle`
- URL local: `http://localhost/controle/`
- PHP 8.2.12 verificado; MySQL disponível em `C:\xampp\mysql\bin\mysql.exe`
- Banco: `techpath`, charset `utf8mb4`, collation `utf8mb4_unicode_ci`
- Fuso: `America/Sao_Paulo`

## 4. Estrutura de arquivos

Estrutura **plana e simples**, pensada para quem está começando. Não crie pastas novas
sem necessidade; se precisar, atualize este arquivo e o `PLANO.md`.

```
controle/
├─ index.php                 # ÚNICA porta de entrada (roteador por ?pagina=)
├─ AGENTS.md                 # este arquivo
├─ PLANO.md                  # tarefas em ordem
├─ PRODUCT.md                # especificação do produto
├─ .gitignore
├─ config/
│  ├─ config.php             # configurações gerais (sem segredos)
│  └─ local.php.example      # modelo de configuração local (sem valores reais)
├─ app/
│  ├─ db.php                 # função db() -> conexão PDO
│  ├─ helpers.php            # e(), redirecionar(), csrf_*(), flash()
│  ├─ auth.php               # F1: cadastro, login, logout, exigir_login()
│  ├─ areas.php              # F2: consultas de áreas
│  ├─ trilhas.php            # F3: consultas e início de trilha
│  ├─ atividades.php         # F4: atividades e conclusão
│  └─ progresso.php          # F5: cálculos do painel
├─ views/
│  ├─ partials/
│  │  ├─ header.php
│  │  └─ footer.php
│  ├─ home.php
│  ├─ cadastro.php
│  ├─ login.php
│  ├─ areas.php
│  ├─ area.php
│  ├─ trilhas.php
│  ├─ trilha.php
│  ├─ atividades.php
│  └─ painel.php
├─ assets/
│  ├─ css/estilo.css
│  └─ js/app.js
└─ database/
   ├─ schema.sql             # criação das tabelas
   └─ seed.sql               # dados iniciais (áreas, trilhas, atividades)
```

Regras de camada:

- `views/` **só exibe** dados; não faz SQL.
- `app/*.php` **só processa** dados; não imprime HTML.
- `index.php` decide qual página carregar e chama as funções de `app/`.
- `config/local.php` nunca é versionado (está no `.gitignore`).

## 5. Convenções de código

**PHP**

- Arquivos que só têm PHP **não** fecham com `?>`.
- Comece arquivos de lógica com `declare(strict_types=1);` (exceto views).
- Estilo procedural com funções: `snake_case` para funções e variáveis.
- 4 espaços de indentação (sem tabs). UTF-8 sem BOM.
- Toda saída de dado vindo do banco/usuário passa por `e()` (que usa `htmlspecialchars`).
- Toda consulta usa **prepared statement** com `:param`. Nunca concatene SQL.
- Toda entrada de formulário é tratada como não confiável: valide tipo, tamanho e formato
  no servidor, mesmo que o HTML já valide.
- Todo `POST` valida o token CSRF.
- Toda página que exige conta chama `exigir_login()` no início.
- Nada de `var_dump`/`print_r` esquecido no código final.
- Comentários e textos de interface em **português**.

**HTML/CSS**

- `<html lang="pt-BR">`, tags semânticas (`header`, `main`, `nav`, `section`, `footer`).
- Todo campo de formulário tem `<label for="...">`.
- Um único arquivo CSS (`assets/css/estilo.css`); nomes de classe em `kebab-case`.
- Layout precisa funcionar em telas de 360px e no desktop.

**Banco**

- Tabelas no plural e `snake_case`: `usuarios`, `areas`, `trilhas`, `atividades`.
- Toda tabela tem `id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY`.
- Chaves estrangeiras com `FOREIGN KEY` e `ON DELETE CASCADE` quando fizer sentido.
- Datas em `TIMESTAMP` com `DEFAULT CURRENT_TIMESTAMP`.
- `schema.sql` e `seed.sql` são a fonte da verdade: toda mudança de tabela atualiza os dois.

**Rotas**

- Sem `.htaccess`/rewrite. Padrão: `index.php?pagina=trilha&id=3`.
- O roteador usa uma **lista branca** de páginas; qualquer valor desconhecido cai em 404.
- `id` sempre validado com `filter_var(..., FILTER_VALIDATE_INT)`.

## 6. Segredos e dados sensíveis

- Credenciais ficam **somente** em `config/local.php`, criado a partir de
  `config/local.php.example`. Ele está no `.gitignore`.
- Nunca escreva senha, usuário do banco, token ou chave em: código, `README`, commit,
  mensagem de erro, print de tela ou issue.
- Nunca use dados reais de crianças/adolescentes em testes. Use dados fictícios.
- Senhas de estudantes: sempre `password_hash($senha, PASSWORD_DEFAULT)` e
  `password_verify()`. Nunca MD5/SHA1, nunca senha em texto puro.
- Nunca registre senha, token CSRF ou conteúdo de sessão em log.
- Mensagens de erro para o usuário são genéricas ("E-mail ou senha inválidos"),
  sem revelar se o e-mail existe.
- Sessão: `session_regenerate_id(true)` após login; cookie com `httponly` e `samesite=Lax`.
- Se um segredo vazar: 1) troque a senha do banco, 2) remova do código,
  3) avise no grupo, 4) limpe o histórico do Git se necessário.

## 7. Procedimento de testes

Não há framework de teste. Cada tarefa do `PLANO.md` tem um critério
**"Pronto quando"**; siga este roteiro:

1. **Sintaxe** (sempre, em todo arquivo alterado):
   ```powershell
   php -l index.php
   php -l app/auth.php
   ```
2. **Banco** (quando o SQL mudar):
   ```powershell
   mysql -u root controle < database/schema.sql
   mysql -u root controle -e "SELECT COUNT(*) FROM atividades;"
   ```
   `schema.sql` e `seed.sql` precisam rodar do zero sem erro.
3. **Manual no navegador**, em `http://localhost/controle/`:
   - caminho feliz (dados válidos);
   - caminho de erro (campo vazio, e-mail repetido, senha errada, id inexistente);
   - acesso sem estar logado (deve redirecionar para o login);
   - acesso a dado de outro usuário (deve ser negado — RN11);
   - recarregar a página e conferir que o estado persiste (RN08).
4. **Console/erros:** nenhum `Warning`, `Notice` ou `Deprecated` do PHP na tela.
   Erros aparecem no log do Apache, não na interface.
5. **Git:** `git status` não pode listar `config/local.php` nem dumps de banco.

**Definição de pronto (DoD) de qualquer tarefa:**

- [ ] O código roda sem erro de sintaxe e sem warnings
- [ ] O critério "Pronto quando" da tarefa foi verificado no navegador
- [ ] Formulário testado com dados válidos **e** inválidos
- [ ] Nenhum segredo no `git diff`
- [ ] Um commit pequeno, com mensagem clara (ex.: `F1: valida e-mail duplicado no cadastro`)

## 8. Regras para agentes de IA

- Implemente **apenas** a tarefa atual do `PLANO.md`. Não adiante tarefas futuras.
- Não implemente funcionalidades fora do MVP.
- Não adicione dependências nem pastas fora da estrutura da seção 4.
- Se a tarefa exigir mudança de banco, atualize `schema.sql` e `seed.sql` juntos.
- Se a especificação estiver ambígua, **pergunte** em vez de inventar regra de negócio.
- Ao terminar, relate: arquivos alterados, como testou e o que ficou pendente.
