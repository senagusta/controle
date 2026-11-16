# NAVEGACAO.md — TechPath (MVP)

Documento de navegação do MVP do TechPath, alinhado ao `PRODUCT.md` (F1–F5), `PLANO.md` e `AGENTS.md`.

## 1. Convenções gerais

- **Roteamento:** `index.php?pagina=<nome>&id=<n>` com lista branca no roteador. Valor desconhecido leva à página 404.
- **Páginas públicas:** Home, Catálogo de áreas, Detalhe da área, Cadastro, Login.
- **Páginas protegidas (exigem login):** Trilhas (catálogo), Detalhe da trilha, Atividades da trilha, Meu painel, Minhas trilhas (pode ser seção do painel ou página própria, conforme implementação; fluxo abaixo assume acesso pelo painel/menu).
- **Feedback ao usuário:** mensagens de sucesso/erro exibidas no topo da área principal (`main`) usando o mecanismo de `flash()`; erros de validação de formulário podem aparecer junto ao campo e/ou no topo.
- **Formulários:** todo `POST` envia token CSRF. Botões de ação destrutiva/importante usam confirmação simples quando aplicável (não há exclusões no MVP).
- **Acessibilidade básica:** `<label>` associado a cada campo, foco visível, ordem de tabulação lógica, textos claros para 7–17 anos.
- **Responsividade:** layout funcional em **360px** (celular) e desktop. No celular, menu empilhado/hambúrguer simples; no desktop, menu horizontal no cabeçalho.

## 2. Mapa de telas

| Tela | Rota (`?pagina=`) | Acesso | Função MVP |
|---|---|---|---|
| Home | `home` (padrão) | Público | Apresentação e entrada no app |
| Cadastro | `cadastro` | Público (deslogado) | F1 — criar conta |
| Login | `login` | Público (deslogado) | F1 — entrar |
| Catálogo de áreas | `areas` | Público | F2 — listar áreas |
| Detalhe da área | `area&id=` | Público | F2 — ver área |
| Catálogo de trilhas | `trilhas` | Protegido | F3 — listar trilhas |
| Detalhe da trilha | `trilha&id=` | Protegido | F3 — ver trilha e iniciar |
| Atividades da trilha | `atividades&trilha_id=` | Protegido (trilha iniciada) | F4 — ver e concluir |
| Meu painel | `painel` | Protegido | F5 — progresso e minhas trilhas |
| Erro 404 | (fallback) | Público | Página/ID não encontrado |
| Logout | `logout` (ação) | Protegido | F1 — sair |

> Observação: a estrutura de `views/` prevista no `AGENTS.md` inclui `trilhas.php` e `painel.php`; "Minhas trilhas" (F3.4) pode ser atendida como seção dentro do painel e/ou filtro no catálogo, desde que respeite RN11.

## 3. Fluxo principal de navegação

### 3.1 Fluxo feliz (critério de sucesso do MVP)

1. **Visitante acessa Home** → conhece o TechPath.
2. Clica em **Criar conta** → preenche cadastro → conta criada (sucesso) → é orientado a entrar (ou entra, conforme implementação).
3. Faz **Login** → sessão iniciada → vai para **Meu painel**.
4. Acessa **Áreas** → explora catálogo → abre **Detalhe da área** → volta ao catálogo.
5. Acessa **Trilhas** → abre **Detalhe da trilha** → vê sequência de atividades → clica **Iniciar trilha**.
6. É direcionado para **Atividades da trilha** (ou acessa pelo painel) → marca atividades como **Concluídas**.
7. Acessa **Meu painel** → vê trilhas iniciadas, contagens, porcentagem e **próxima atividade pendente** atualizadas.

### 3.2 Fluxos alternativos importantes

- **Visitante tenta acessar página protegida:** redireciona para `login` com mensagem informativa ("Entre para continuar").
- **Usuário logado tenta ver trilha não iniciada em Atividades:** acesso negado/bloqueado com orientação para iniciar a trilha (RN12).
- **Recurso inexistente (`id` inválido/ausente):** exibe 404 amigável com link para Home.
- **Acesso a dados de outro usuário:** negado; não expõe informação (RN11).
- **Usuário já logado acessa login/cadastro:** pode ser redirecionado ao painel (comportamento recomendado).

### 3.3 Diagrama de navegação (simplificado)

```text
[Home] --Criar conta--> [Cadastro] --sucesso--> [Login]
  |                        ^
  +--Entrar--------------->+
  |                        |
  +--Explorar áreas--> [Áreas] --> [Detalhe da área]
  |
  (após login)
  v
[Meu painel] <-------------------------------+
  |                                          |
  +--> [Trilhas] --> [Detalhe da trilha] --Iniciar--> [Atividades]
  |                         ^                              |
  +-------------------------+--------Concluir------------+
```

## 4. Componentes globais (presentes na maioria das telas)

### 4.1 Cabeçalho (`header`)
- **Elementos:** nome/logo "TechPath", navegação principal.
- **Links (deslogado):** Home, Áreas, Entrar, Criar conta.
- **Links (logado):** Home, Áreas, Trilhas, Meu painel, Sair (ação POST ou link com proteção, conforme implementação).
- **Mensagens:** área de flash (sucesso/erro/info).
- **Estados:** menu recolhido no celular (ex.: botão "Menu"); expandido no desktop.

### 4.2 Rodapé (`footer`)
- Texto curto do projeto/MVP e ano.

### 4.3 Padrões de estado (aplicáveis às telas)

| Estado | Quando ocorre | Comportamento esperado |
|---|---|---|
| **Carregamento** | Envio de formulário / navegação entre páginas | Navegação normal do navegador (página recarrega). Botão de submit desabilitado após clique (JS opcional em `app.js`) para evitar duplo envio; texto pode mudar para "Aguarde...". |
| **Lista vazia** | Consulta sem resultados | Mensagem amigável + ação sugerida (ex.: "Nenhuma trilha iniciada ainda. Explorar trilhas"). |
| **Sucesso** | Ação concluída (cadastro, login, iniciar trilha, concluir atividade) | Mensagem flash de confirmação e atualização do conteúdo/indicadores visíveis. |
| **Erro** | Validação, falha de permissão, recurso ausente, erro de processamento | Mensagem clara (sem expor detalhes técnicos/sensíveis), manter dados já preenchidos quando seguro (nunca repopular senha), oferecer caminho de correção. |

## 5. Detalhamento das telas

### 5.1 Home (`home`)
- **Objetivo:** apresentar o propósito do TechPath e direcionar para exploração ou acesso.
- **Campos:** nenhum.
- **Botões/links:** "Criar conta", "Entrar", "Explorar áreas" (e "Ver trilhas"/"Ir para meu painel" se logado).
- **Conteúdo:** título, texto curto sobre explorar áreas, iniciar trilhas e acompanhar progresso.
- **Mensagens:** flash (ex.: "Você saiu da sua conta").
- **Carregamento:** padrão de navegação.
- **Lista vazia:** não se aplica.
- **Sucesso:** não se aplica (exceto mensagens vindas de redirecionamento).
- **Erro:** não se aplica.

**Desenho simples (celular):**
```text
[Logo TechPath] [Menu]
----------------------
 Bem-vindo ao TechPath
 Descubra áreas de tecnologia
 e acompanhe seu progresso.

 [Criar conta]
 [Entrar]
 [Explorar áreas]
----------------------
 Rodapé
```

**Desenho simples (desktop):** cabeçalho horizontal; conteúdo centralizado com blocos/cards lado a lado para os atalhos.

---

### 5.2 Cadastro (`cadastro`)
- **Objetivo:** criar conta individual (F1, RN01).
- **Campos (formulário):**
  - Nome (texto, obrigatório)
  - E-mail (email, obrigatório, único)
  - Senha (password, obrigatório, mínimo definido na validação)
  - Confirmar senha (password, obrigatório, igual à senha)
  - Token CSRF (oculto)
- **Botões/links:** "Criar conta" (submit), link "Já tenho conta / Entrar".
- **Mensagens:**
  - Sucesso: "Conta criada com sucesso. Faça login para continuar." (ou equivalente)
  - Erro de validação: "Informe seu nome.", "Digite um e-mail válido.", "As senhas não coincidem.", "A senha deve ter pelo menos X caracteres."
  - E-mail já usado: mensagem clara sem expor dados sensíveis além do necessário ao fluxo (ex.: "Não foi possível concluir o cadastro. Verifique os dados e tente novamente." ou mensagem específica definida na implementação, mantendo cuidado com privacidade; seguir AGENTS sobre não revelar indevidamente).
- **Carregamento:** botão desabilitado durante envio (opcional JS).
- **Lista vazia:** não se aplica.
- **Sucesso:** redireciona para login com flash de sucesso; registro criado com `senha_hash`.
- **Erro:** permanece na página, exibe mensagens, preserva nome/e-mail preenchidos; limpa campos de senha.

**Desenho simples (celular/desktop):**
```text
Criar conta
[Nome________________]
[E-mail______________]
[Senha_______________]
[Confirmar senha_____]
(Criar conta)

Já tem conta? Entrar
```

---

### 5.3 Login (`login`)
- **Objetivo:** autenticar usuário (F1, RN02).
- **Campos:** E-mail, Senha, Token CSRF.
- **Botões/links:** "Entrar" (submit), "Criar conta".
- **Mensagens:**
  - Sucesso: redireciona ao painel (ou página solicitada).
  - Erro: **genérico** "E-mail ou senha inválidos." (conforme AGENTS; não informar se e-mail existe).
  - Info: "Entre para continuar." quando vindo de redirecionamento de página protegida.
- **Carregamento:** botão desabilitado durante envio (opcional).
- **Lista vazia:** não se aplica.
- **Sucesso:** sessão iniciada, `session_regenerate_id(true)`.
- **Erro:** mantém e-mail (opcional), limpa senha.

**Desenho simples:**
```text
Entrar
[E-mail______________]
[Senha_______________]
(Entrar)

Não tem conta? Criar conta
```

---

### 5.4 Catálogo de áreas (`areas`)
- **Objetivo:** listar áreas tecnológicas para exploração (F2, RN03).
- **Campos:** nenhum (pode haver busca/filtro futuro, fora do MVP).
- **Botões/links:** cada card/item "Ver detalhes" → `area&id=`.
- **Conteúdo por item:** nome e descrição curta.
- **Mensagens:** flash geral se houver.
- **Carregamento:** carregamento da página; se houver busca dinâmica no futuro, indicador local (fora do MVP).
- **Lista vazia:** "Nenhuma área disponível no momento." (situação anômala se seed não rodou; orientar administrador implicitamente não é necessário ao estudante).
- **Sucesso:** exibição da lista.
- **Erro:** falha ao carregar dados → mensagem "Não foi possível carregar as áreas. Tente novamente mais tarde." sem detalhes técnicos.

**Desenho simples (celular):** lista vertical de cards. **Desktop:** grade de cards (2–3 colunas).

---

### 5.5 Detalhe da área (`area&id=`)
- **Objetivo:** mostrar informações completas de uma área (F2).
- **Campos:** nenhum.
- **Botões/links:** "Voltar para áreas", links de navegação para outras áreas (opcional, se implementado) e "Ver trilhas" (se logado) / "Entrar para ver trilhas".
- **Conteúdo:** nome, descrição completa (linguagem acessível).
- **Mensagens:** flash se aplicável.
- **Carregamento:** padrão.
- **Lista vazia:** não se aplica.
- **Sucesso:** conteúdo exibido.
- **Erro:** `id` inválido/inexistente → página 404 amigável; falha de consulta → mensagem de erro genérica com link de retorno.

---

### 5.6 Catálogo de trilhas (`trilhas`) — protegido
- **Objetivo:** listar trilhas disponíveis com visão geral (F3, RN04).
- **Campos:** nenhum.
- **Botões/links:** "Ver detalhes" por trilha; indicação visual se já iniciada (ex.: selo "Iniciada").
- **Conteúdo por item:** nome, descrição curta, nível de dificuldade (iniciante/intermediário/avançado).
- **Mensagens:** flash (ex.: retorno de ações).
- **Carregamento:** padrão.
- **Lista vazia:** "Nenhuma trilha disponível no momento."
- **Sucesso:** lista exibida.
- **Erro:** não autenticado → redireciona ao login; falha de dados → mensagem genérica.

**Desenho simples:** cards com badge de nível e status.

---

### 5.7 Detalhe da trilha (`trilha&id=`) — protegido
- **Objetivo:** mostrar detalhes e sequência antes de iniciar (F3, RN05, RN06).
- **Campos (formulário de início):** `trilha_id` (oculto) ou uso do `id` da rota validado no servidor, Token CSRF.
- **Botões/links:** "Iniciar trilha" (submit, se não iniciada), "Continuar atividades" (se já iniciada), "Voltar para trilhas".
- **Conteúdo:** nome, descrição, nível, lista ordenada de atividades (título e breve descrição) por `posicao`.
- **Mensagens:**
  - Sucesso ao iniciar: "Trilha iniciada com sucesso."
  - Já iniciada: "Você já iniciou esta trilha." (estado informativo, sem duplicar registro).
- **Carregamento:** ao iniciar, desabilitar botão e indicar "Aguarde...".
- **Lista vazia (atividades):** "Esta trilha ainda não possui atividades." (anômalo, mas tratado).
- **Sucesso:** trilha associada em `usuario_trilhas`; redireciona para atividades ou atualiza estado.
- **Erro:** id inválido → 404; falha ao iniciar → mensagem "Não foi possível iniciar a trilha. Tente novamente."

---

### 5.8 Atividades da trilha (`atividades&trilha_id=`) — protegido
- **Objetivo:** listar atividades, separar concluídas/pendentes e registrar conclusão (F4, RN06–RN08, RN12).
- **Campos (por atividade pendente):** formulário POST com identificador da atividade e Token CSRF (ou ação equivalente).
- **Botões/links:** "Marcar como concluída" (por atividade permitida), "Voltar ao painel", "Ver trilha".
- **Conteúdo:** título da trilha, progresso resumido (ex.: X de Y), seções/listas "Pendentes" e "Concluídas" respeitando ordem.
- **Mensagens:**
  - Sucesso: "Atividade concluída!" e atualização visual.
  - Erro: "Não foi possível registrar a conclusão." / acesso negado se trilha não iniciada.
- **Carregamento:** ao marcar, botão da atividade desabilitado/"Salvando..."; após POST+redirect, página recarrega com estado atualizado.
- **Lista vazia:** sem pendentes → "Você concluiu todas as atividades desta trilha!"; sem atividades cadastradas → mensagem informativa.
- **Sucesso:** conclusão persistida em `atividade_conclusoes` (idempotente).
- **Erro:** tentativa de concluir atividade de trilha não iniciada ou de outro contexto inválido → bloqueio com mensagem e sem gravação (RN12).

**Desenho simples (celular):**
```text
Atividades - Desenvolvimento Web
Progresso: 2/6
[Barra =======---]

Pendentes
1. ( ) Fundamentos... [Concluir]
2. ( ) Introdução...  [Concluir]

Concluídas
✓ HTML
✓ CSS
```

---

### 5.9 Meu painel (`painel`) — protegido
- **Objetivo:** mostrar evolução individual (F5, RN09–RN11).
- **Campos:** nenhum.
- **Botões/links:** "Continuar" / "Ver atividades" por trilha iniciada, "Explorar trilhas" (se vazio), "Ver áreas".
- **Conteúdo por trilha iniciada:** nome, nível, concluídas, pendentes, total, porcentagem, barra de progresso, **próxima atividade pendente** (ou mensagem "Trilha concluída").
- **Mensagens:** flash de sucesso/erro vindos de outras ações.
- **Carregamento:** padrão; cálculos no servidor na renderização.
- **Lista vazia:** "Você ainda não iniciou nenhuma trilha." com botão "Explorar trilhas".
- **Sucesso:** dados consistentes com conclusões do usuário logado.
- **Erro:** não autenticado → login; falha de consulta → mensagem genérica.

**Desenho simples (desktop):** cards de trilhas em grade com barra e destaque da próxima atividade.

---

### 5.10 Erro 404 (`404`)
- **Objetivo:** informar que página/recurso não existe de forma amigável.
- **Campos:** nenhum.
- **Botões/links:** "Ir para a página inicial", "Explorar áreas".
- **Mensagens:** "Página não encontrada." / "O conteúdo que você procura não existe ou foi removido."
- **Carregamento:** não se aplica.
- **Lista vazia:** não se aplica.
- **Sucesso:** não se aplica.
- **Erro:** estado final de navegação inválida (sem stack trace).

## 6. Regras de navegação e segurança refletidas na UI

- Links/ações para trilhas, atividades e painel só são úteis quando logado; o servidor reforça com `exigir_login()`.
- O usuário nunca vê progresso ou trilhas de terceiros (RN11).
- IDs em URL são validados; falha resulta em 404 ou erro controlado.
- Após ações `POST` bem-sucedidas, usar redirecionamento para evitar reenvio (PRG), atualizando o painel/progresso (RN10).
- Mensagens de autenticação são genéricas (não revelar existência de e-mail). 

## 7. Decisões pendentes (impactam navegação)

1. **Múltiplas trilhas simultâneas:** a navegação assume que sim (painel lista várias), conforme indicação da F5; confirmar.
2. **Desfazer início de trilha:** não previsto no MVP; sem botão/fluxo.
3. **Cadastro de áreas/trilhas/atividades:** apenas via `seed.sql` no MVP; sem telas administrativas.
4. **Ordem de conclusão:** a navegação assume marcação da atividade conforme disponibilidade na UI; confirmar se haverá bloqueio estrito da próxima atividade. O documento deve ser ajustado se a regra mudar.
