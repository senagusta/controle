# PLANO.md — TechPath (MVP)

Plano de tarefas **pequenas e verificáveis**, na ordem recomendada do `PRODUCT.md`.
Regras:

- Faça **uma tarefa por vez**. Marque `[x]` só quando o "Pronto quando" for verdadeiro.
- Tarefa concluída = 1 commit pequeno com mensagem clara.
- Não pule para a próxima fase antes de fechar a anterior.
- Convenções, tecnologias e testes obrigatórios estão em `AGENTS.md`.
- Nenhuma tarefa abaixo está implementada ainda.

Legenda: `[ ]` a fazer · `[x]` concluída

---

## Fase 0 — Preparação do ambiente

- [ ] **0.1 Verificar PHP e MySQL**
  Rodar `php -v` (esperado 8.2+) e iniciar MySQL no painel do XAMPP.
  **Pronto quando:** os dois comandos respondem sem erro e o Apache serve
  `http://localhost/controle/`.

- [ ] **0.2 Criar o banco `techpath`**
  Criar no phpMyAdmin com `utf8mb4_unicode_ci`.
  **Pronto quando:** `SHOW DATABASES;` lista `techpath`.

- [ ] **0.3 Criar `.gitignore`**
  Ignorar `config/local.php`, logs, dumps (`*.sql.gz`, `database/dump-*`), pastas de editor.
  **Pronto quando:** `git status` não mostra `config/local.php` mesmo depois do passo 0.4.

- [ ] **0.4 Criar `config/config.php` e `config/local.php.example`**
  Configurações gerais (fuso, charset) em `config.php`; modelo de credenciais em
  `local.php.example`. Copiar para `config/local.php` com os dados locais.
  **Pronto quando:** `php -r "var_dump(require 'config/local.php');"` retorna um array
  e `git status` continua limpo.

- [ ] **0.5 Criar `app/db.php` com a função `db()`**
  Conexão PDO única, `ERRMODE_EXCEPTION`, charset `utf8mb4`, sem imprimir erro na tela.
  **Pronto quando:** um script temporário mostra a versão do MySQL e é apagado em seguida.

- [ ] **0.6 Criar o esqueleto visual**
  `index.php` como roteador (lista branca `?pagina=`), `views/partials/header.php`,
  `footer.php`, `views/home.php` e `assets/css/estilo.css` mínimo.
  **Pronto quando:** `http://localhost/controle/` abre sem erro, com cabeçalho e rodapé,
  e `php -l` passa em todos os arquivos PHP. Nenhuma funcionalidade de F1–F5 ainda.

---

## Fase 1 — Banco de dados

- [ ] **1.1 Tabela `usuarios`**
  Campos: `id`, `nome`, `email` (UNIQUE), `senha_hash`, `criado_em`.
  **Pronto quando:** `schema.sql` roda do zero e `DESCRIBE usuarios;` mostra a UNIQUE em `email`.

- [ ] **1.2 Tabela `areas`**
  Campos: `id`, `nome`, `descricao`, `criado_em`.
  **Pronto quando:** `schema.sql` roda do zero sem erro (com 1.1).

- [ ] **1.3 Tabelas `trilhas` e `atividades`**
  `trilhas`: `nome`, `descricao`, `nivel` (`iniciante|intermediario|avancado`).
  `atividades`: `trilha_id` (FK), `titulo`, `descricao`, `posicao`.
  **Pronto quando:** `SELECT * FROM atividades WHERE trilha_id = 1 ORDER BY posicao;`
  retorna na ordem certa e a FK impede `trilha_id` inexistente.

- [ ] **1.4 Tabelas de vínculo e conclusão**
  `usuario_trilhas` (`usuario_id`, `trilha_id`, `iniciado_em`, UNIQUE no par).
  `atividade_conclusoes` (`usuario_id`, `atividade_id`, `concluido_em`, UNIQUE no par).
  **Pronto quando:** inserir a mesma conclusão duas vezes falha na segunda (idempotência).

- [ ] **1.5 `seed.sql` com dados fictícios**
  3 áreas, 1 trilha "Desenvolvimento Web" com 6 atividades, 1 usuário de teste.
  **Pronto quando:** `SELECT COUNT(*)` mostra 3 áreas, 1 trilha, 6 atividades, e o
  `seed.sql` roda duas vezes sem duplicar (ou é seguro recriar o banco).

---

## Fase 2 — F1: Cadastro e autenticação

- [ ] **2.1 Helpers básicos em `app/helpers.php`**
  `e()`, `redirecionar()`, `csrf_token()`, `csrf_validar()`, `flash()`.
  **Pronto quando:** `php -l` passa e um teste manual comprova que `e('<b>')` não gera HTML.

- [ ] **2.2 View de cadastro**
  Formulário com nome, e-mail, senha e confirmação, com `label`, `required` e token CSRF.
  **Pronto quando:** `/index.php?pagina=cadastro` abre pelo roteador e mostra os campos.

- [ ] **2.3 Processar cadastro**
  Validação server-side (nome, e-mail válido, senha mínima, confirmação igual),
  bloqueio de e-mail repetido, `password_hash` e inserção via prepared statement.
  **Pronto quando:** cadastro válido cria a linha em `usuarios` com hash; dados inválidos
  mostram mensagem e nada é inserido (testar com o mesmo e-mail duas vezes).

- [ ] **2.4 View de login**
  Formulário e-mail + senha, com CSRF e link para cadastro.
  **Pronto quando:** `/index.php?pagina=login` abre e o formulário envia por POST.

- [ ] **2.5 Processar login e logout**
  `password_verify`, `session_regenerate_id(true)`, mensagem genérica em caso de erro,
  logout com `session_destroy()`.
  **Pronto quando:** login válido entra no painel; senha errada volta com erro genérico;
  após logout, o painel volta a exigir login.

- [ ] **2.6 Proteger páginas internas**
  Função `exigir_login()` chamada nas páginas de trilhas, atividades e painel.
  **Pronto quando:** abrir `/index.php?pagina=painel` deslogado redireciona para o login,
  e após entrar volta a funcionar.

---

## Fase 3 — F2: Catálogo de áreas

- [ ] **3.1 Listar áreas**
  Consulta em `app/areas.php`, exibição em `views/areas.php` com nome e descrição curta.
  **Pronto quando:** a lista mostra as 3 áreas do seed, com texto escapado.

- [ ] **3.2 Detalhe da área**
  `index.php?pagina=area&id=N` com busca por id validado.
  **Pronto quando:** id válido mostra nome e descrição completa; id inexistente
  retorna uma página 404 amigável (não um erro do PHP).

---

## Fase 4 — F3: Catálogo e seleção de trilhas

- [ ] **4.1 Listar trilhas**
  Nome, descrição curta e nível de dificuldade.
  **Pronto quando:** a página lista as trilhas do seed e o nível aparece legível.

- [ ] **4.2 Detalhe da trilha**
  Mostra nome, descrição, nível e a sequência de atividades **na ordem da `posicao`**,
  com botão "Iniciar trilha".
  **Pronto quando:** a trilha de exemplo lista as 6 atividades na ordem correta.

- [ ] **4.3 Iniciar trilha**
  POST com CSRF que grava em `usuario_trilhas`; se já existir, não duplica.
  **Pronto quando:** iniciar duas vezes mantém apenas 1 linha e a página indica
  "trilha já iniciada". Deslogado, a ação é negada.

- [ ] **4.4 "Minhas trilhas"**
  Lista apenas as trilhas iniciadas pelo usuário logado.
  **Pronto quando:** dois usuários diferentes veem listas diferentes (RN11).

---

## Fase 5 — F4: Atividades e conclusão

- [ ] **5.1 Página de atividades da trilha**
  Acessível só se a trilha foi iniciada; separa **concluídas** e **pendentes**.
  **Pronto quando:** trilha não iniciada é bloqueada (RN12) e a ordem da sequência é respeitada.

- [ ] **5.2 Marcar atividade como concluída**
  POST + CSRF, valida que a atividade pertence a uma trilha iniciada pelo usuário
  e grava em `atividade_conclusoes` sem duplicar.
  **Pronto quando:** marcar atualiza a lista na hora; tentar marcar atividade de trilha
  não iniciada é negado; recarregar/sair e entrar mantém a conclusão (RN08).

- [ ] **5.3 Desmarcar atividade (opcional — confirmar com o professor)**
  Só implementar se aprovado, pois o MVP não exige.
  **Pronto quando:** desmarcar remove apenas a conclusão do próprio usuário.

---

## Fase 6 — F5: Painel de progresso

- [ ] **6.1 Listar trilhas iniciadas com contagens**
  Concluídas, pendentes e total por trilha.
  **Pronto quando:** os números batem com a tabela de conclusões do usuário logado.

- [ ] **6.2 Calcular e exibir a porcentagem**
  `concluidas / total * 100`, arredondada, com barra de progresso visual.
  **Pronto quando:** 0%, 50% e 100% aparecem corretamente e o cálculo não quebra
  com trilha sem atividades (evitar divisão por zero).

- [ ] **6.3 Mostrar a próxima atividade pendente**
  Primeira atividade não concluída, na ordem da `posicao`.
  **Pronto quando:** após concluir a primeira, o painel aponta a segunda;
  com tudo concluído, mostra mensagem de trilha finalizada.

- [ ] **6.4 Atualização após conclusão**
  Redirecionar após o POST (evita reenvio) e exibir mensagem de sucesso.
  **Pronto quando:** concluir uma atividade e voltar ao painel mostra o novo percentual
  sem precisar de F5 manual (RN10).

---

## Fase 7 — Qualidade e entrega

- [ ] **7.1 Revisão de segurança**
  Conferir: todo SQL com prepared statement, toda saída com `e()`, todo POST com CSRF,
  toda página interna com `exigir_login()`, nenhum segredo versionado.
  **Pronto quando:** a revisão é feita item por item e os problemas são corrigidos.

- [ ] **7.2 Responsividade e acessibilidade básica**
  Testar em 360px e desktop; labels associados; contraste legível; foco visível.
  **Pronto quando:** nenhuma rolagem horizontal em 360px e o formulário é navegável por Tab.

- [ ] **7.3 `README.md` de instalação**
  Como criar o banco, importar `schema.sql`/`seed.sql`, configurar `local.php` e abrir o app.
  **Pronto quando:** outra pessoa consegue seguir o passo a passo do zero sem ajuda.

- [ ] **7.4 Checklist final do MVP**
  Percorrer o critério de sucesso do `PRODUCT.md` (criar conta → explorar áreas →
  iniciar trilha → concluir atividades → ver progresso).
  **Pronto quando:** todos os 6 passos funcionam em sequência com um usuário novo.

---

## Decisões pendentes (perguntar antes de implementar)

1. Um estudante pode ter **várias trilhas** iniciadas ao mesmo tempo? (a F5 fala em plural)
2. O botão "Iniciar trilha" pode ser desfeito? Se sim, o progresso é apagado?
3. Quem cadastra áreas/trilhas/atividades? Nesta versão, apenas o `seed.sql`?
4. A trilha exige concluir uma atividade por vez (bloquear a próxima) ou permite qualquer ordem?
