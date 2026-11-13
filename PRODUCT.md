# TechPath — Product Definition

**Versão:** 1.0  
**Status:** Definição do MVP  
**Tipo:** Aplicação Web  
**Stack:** PHP, MySQL, HTML, CSS e JavaScript.

---

## 1. Visão do produto

O TechPath é uma aplicação web destinada a crianças e adolescentes de 7 a 17 anos que desejam ingressar no mundo da tecnologia.

A plataforma tem como objetivo facilitar os primeiros passos desses estudantes, oferecendo um ambiente para explorar diferentes áreas da tecnologia, conhecer seus fundamentos, organizar os estudos e acompanhar o próprio progresso por meio de trilhas de aprendizagem.

## 2. Problema

Organização, direcionamento e jornada de aprendizagem podem ser desafios encontrados por crianças e adolescentes que desejam ingressar no mundo da tecnologia.

A diversidade de áreas, ferramentas e conteúdos disponíveis pode dificultar a identificação de um ponto de partida. Além disso, muitos recursos não são organizados de maneira adequada para acompanhar a evolução de estudantes dessa faixa etária.

Como consequência, estudantes iniciantes podem enfrentar dificuldades para organizar seus estudos, identificar os próximos passos e manter a motivação durante o aprendizado.

## 3. Público-alvo

Crianças e adolescentes de 7 a 17 anos que desejam conhecer, explorar e iniciar seus estudos no mundo da tecnologia.

### Características do público

- Estudantes iniciantes ou com pouco conhecimento tecnológico.
- Pessoas interessadas em conhecer diferentes áreas da tecnologia.
- Estudantes que precisam de direcionamento para organizar seus estudos.
- Pessoas que desejam visualizar e acompanhar sua evolução.

## 4. Objetivo do produto

Desenvolver uma aplicação web que ajude crianças e adolescentes a:

- Conhecer as diferentes áreas da tecnologia.
- Compreender fundamentos da Tecnologia da Informação.
- Encontrar caminhos organizados de aprendizagem.
- Realizar atividades relacionadas às trilhas escolhidas.
- Acompanhar o progresso individual nos estudos.

## 5. Escopo do MVP

O MVP (Minimum Viable Product) será composto por cinco funcionalidades principais.

### F1 — Cadastro e autenticação

Permitir que estudantes criem contas e acessem seus ambientes individuais.

**Critérios de aceitação:**

- O sistema deve permitir cadastro com nome, e-mail e senha.
- Os campos obrigatórios devem ser validados.
- O sistema deve impedir o cadastro de e-mails já utilizados.
- O sistema deve permitir login de usuários cadastrados.
- O acesso às funcionalidades individuais deve exigir autenticação.
- As senhas devem ser armazenadas de maneira segura, utilizando hash.

### F2 — Catálogo de exploração de áreas

Apresentar diferentes áreas da tecnologia para que os estudantes conheçam suas possibilidades.

**Critérios de aceitação:**

- O sistema deve apresentar um catálogo de áreas tecnológicas.
- Cada área deve possuir nome e descrição.
- O estudante deve conseguir acessar informações individuais de cada área.
- As descrições devem utilizar linguagem acessível ao público-alvo.
- O estudante deve conseguir navegar entre as áreas disponíveis.

**Exemplos de áreas:**

- Desenvolvimento de software.
- Engenharia de dados.
- Inteligência artificial.
- Cibersegurança.
- Desenvolvimento de jogos.
- Redes de computadores.

### F3 — Catálogo e seleção de trilhas

Disponibilizar trilhas organizadas para orientar os estudantes em seus primeiros passos na tecnologia.

**Critérios de aceitação:**

- O sistema deve apresentar as trilhas disponíveis.
- Cada trilha deve possuir nome, descrição e nível de dificuldade.
- Cada trilha deve apresentar uma sequência organizada de conteúdos.
- O estudante deve conseguir visualizar os detalhes antes de selecionar uma trilha.
- O estudante deve conseguir iniciar uma trilha.
- O sistema deve associar a trilha selecionada à conta do estudante.

**Exemplo de trilha: Desenvolvimento Web**

1. Fundamentos da tecnologia.
2. Introdução à programação.
3. HTML.
4. CSS.
5. JavaScript.
6. Primeiro projeto web.

### F4 — Atividades e controle de conclusão

Permitir que os estudantes acompanhem as etapas das trilhas e registrem as atividades concluídas.

**Critérios de aceitação:**

- O sistema deve apresentar as atividades pertencentes à trilha selecionada.
- Cada atividade deve possuir título e descrição.
- As atividades devem respeitar a ordem estabelecida na trilha.
- O estudante deve conseguir marcar uma atividade como concluída.
- O sistema deve registrar a conclusão da atividade.
- O estudante deve conseguir visualizar atividades concluídas e pendentes.
- O estado de conclusão deve permanecer registrado após sair e entrar novamente na plataforma.

### F5 — Painel de acompanhamento do progresso

Apresentar informações sobre a evolução individual do estudante nas trilhas selecionadas.

**Critérios de aceitação:**

- O painel deve apresentar as trilhas iniciadas pelo estudante.
- O sistema deve apresentar a quantidade de atividades concluídas e pendentes.
- O sistema deve calcular a porcentagem de conclusão de cada trilha.
- O progresso deve ser atualizado após a conclusão de uma atividade.
- O estudante deve conseguir identificar a próxima atividade pendente.

## 6. Regras de negócio

As seguintes regras orientam o funcionamento do MVP.

**RN01 — Identificação de usuários:** cada estudante deve possuir uma conta individual, identificada por um e-mail único.

**RN02 — Autenticação:** somente usuários autenticados podem acessar funcionalidades individuais.

**RN03 — Exploração:** o catálogo de áreas deve permitir a consulta de informações independentemente da seleção de uma trilha.

**RN04 — Trilhas:** as trilhas devem ser previamente cadastradas e disponibilizadas pelo sistema.

**RN05 — Seleção:** o estudante pode iniciar uma trilha disponível.

**RN06 — Atividades:** cada atividade deve pertencer a uma trilha e possuir uma posição definida na sequência de aprendizagem.

**RN07 — Conclusão:** uma atividade pode ser marcada como concluída pelo estudante.

**RN08 — Persistência:** as informações de conclusão devem permanecer armazenadas entre diferentes acessos.

**RN09 — Progresso:** a porcentagem de conclusão deve ser calculada com base na quantidade de atividades concluídas em relação ao total de atividades da trilha.

**RN10 — Atualização:** o progresso deve ser atualizado automaticamente após o registro de conclusão de uma atividade.

**RN11 — Individualidade:** cada estudante deve visualizar apenas suas próprias informações de progresso.

**RN12 — Integridade:** um estudante não pode registrar a conclusão de atividades pertencentes a uma trilha que não tenha iniciado.

## 7. Funcionalidades futuras (fora do MVP)

As funcionalidades abaixo não fazem parte da primeira versão, mas poderão ser consideradas em versões posteriores.

| Funcionalidade | Descrição |
|---|---|
| Recomendação personalizada | Recomendar trilhas conforme interesses e conhecimentos do estudante. |
| Sistema de conquistas | Medalhas e recompensas por objetivos alcançados. |
| Gamificação | Pontuação, níveis e desafios. |
| Testes de conhecimento | Questionários para avaliar o aprendizado. |
| Conteúdos externos | Recomendar cursos, vídeos e materiais complementares. |
| Personalização de perfil | Personalização de avatar e preferências. |
| Relatórios de desempenho | Relatórios detalhados da evolução individual. |

## 8. Tecnologias

| Tecnologia | Responsabilidade |
|---|---|
| HTML | Estrutura das páginas. |
| CSS | Aparência e responsividade. |
| JavaScript | Interatividade e atualização de elementos da interface. |
| PHP | Processamento das informações e regras do sistema. |
| MySQL | Armazenamento dos usuários, trilhas, atividades e progresso. |

### Arquitetura inicial

- **Frontend:** HTML, CSS e JavaScript.
- **Backend:** PHP.
- **Banco de dados:** MySQL.
- **Frameworks:** não serão utilizados inicialmente.

## 9. Dependências entre funcionalidades

| Funcionalidade | Dependências |
|---|---|
| F1 — Cadastro e autenticação | Nenhuma. |
| F2 — Catálogo de exploração | Cadastro prévio das áreas tecnológicas. |
| F3 — Catálogo e seleção de trilhas | F1 e cadastro prévio das trilhas. |
| F4 — Atividades e conclusão | F1, F3 e cadastro prévio das atividades. |
| F5 — Painel de progresso | F1, F3 e F4. |

### Ordem recomendada de desenvolvimento

1. Cadastro e autenticação.
2. Catálogo de exploração de áreas.
3. Catálogo e seleção de trilhas.
4. Atividades e controle de conclusão.
5. Painel de acompanhamento.

## 10. Critério de sucesso do MVP

O MVP será considerado funcional quando um estudante conseguir:

1. Criar uma conta e realizar login.
2. Explorar diferentes áreas da tecnologia.
3. Escolher e iniciar uma trilha de aprendizagem.
4. Acessar as atividades disponíveis.
5. Registrar atividades concluídas.
6. Visualizar seu progresso atualizado.

O principal critério de sucesso é permitir que um estudante iniciante descubra uma área da tecnologia, inicie uma jornada de aprendizagem organizada e acompanhe sua evolução sem precisar de orientação externa para utilizar a plataforma.

## 11. Premissas e hipóteses

As seguintes definições são premissas iniciais do projeto e poderão ser revisadas durante o desenvolvimento:

- **H1:** a plataforma terá uma interface visualmente amigável e intuitiva.
- **H2:** os dados serão persistidos em um banco de dados MySQL.
- **H3:** as trilhas terão conteúdos organizados por dificuldade.
- **H4:** o progresso será apresentado por porcentagens e indicadores visuais.
- **H5:** haverá autenticação individual dos estudantes.
- **H6:** a aplicação será desenvolvida sem frameworks inicialmente.

## 12. Fora do escopo

Para manter o desenvolvimento compatível com o tamanho do MVP, não serão implementados inicialmente:

- Sistema de recomendação automática.
- Inteligência artificial integrada.
- Comunicação entre estudantes.
- Professores ou responsáveis com contas próprias.
- Ranking público de estudantes.
- Sistema avançado de gamificação.
- Produção e hospedagem de cursos completos.

---

**Documento de referência:** PRODUCT.md  
**Projeto:** TechPath  
**Versão:** 1.0