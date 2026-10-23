# NIWNET

## 1. Objetivo

A NIWNET é um gestor de crescimento digital para redes sociais. Ela leva uma pessoa ou marca do diagnóstico até a execução, análise e otimização de conteúdo, respondendo à pergunta: “O que eu deveria fazer nas minhas redes hoje, por quê, como fazer e o que fazer depois do resultado?”. O sistema substitui boa parte do trabalho operacional e estratégico de um social media, atuando como copiloto, gestor ou autopiloto conforme o nível de autonomia escolhido pelo usuário.

## 2. Usuários

- **Usuário comum (pessoa física / criador / profissional liberal / pequena empresa)**: realiza onboarding estratégico, visualiza e edita o Perfil Estratégico, recebe recomendações de conteúdo, gera calendário e roteiros, aprova ou rejeita conteúdos, consulta analytics interpretados e acompanha o funil de crescimento.
- **Administrador do sistema**: gerencia contas, monitora uso, configura integrações globais e visualiza métricas agregadas da plataforma (fora do escopo do MVP do aluno).

## 3. Casos de uso principais

- Usuário realiza cadastro e login.
- Usuário completa o onboarding estratégico (perguntas sobre objetivos, público, redes, tempo disponível etc.).
- Sistema gera e salva o Perfil Estratégico.
- Usuário visualiza e ajusta pilares de conteúdo e matriz de conteúdo.
- Sistema gera calendário semanal/mensal de conteúdo (ideias, ganchos, roteiros, legendas, CTAs, hashtags).
- Usuário solicita criação de conteúdo a partir de um tema e recebe estratégia + roteiro + derivações (legenda, Stories, carrossel, LinkedIn etc.).
- Usuário salva conteúdos na biblioteca com status (ideia, rascunho, em produção, aguardando aprovação, agendado, publicado, reaproveitável, arquivado).
- Usuário consulta analytics inteligentes (interpretação de métricas + recomendações de próxima ação).
- Usuário visualiza dashboard do dia/semana com plano de ação e recomendações da IA.
- Usuário altera o nível de autonomia (Copiloto / Gestor / Autopiloto).

## 4. Requisitos funcionais

- RF01: Cadastro de usuário com e-mail, senha e nome.
- RF02: Login e logout com sessão autenticada.
- RF03: Onboarding estratégico com formulário de perguntas (objetivo, público, redes, tempo, assuntos, concorrentes, personalidade, equipamento, orçamento, critério de sucesso).
- RF04: Geração e persistência do Perfil Estratégico a partir das respostas do onboarding.
- RF05: Edição do Perfil Estratégico pelo usuário.
- RF06: Geração de pilares de conteúdo e matriz (pilar × formato × objetivo × plataforma) com base no Perfil Estratégico.
- RF07: Geração de calendário de conteúdo (semanal e mensal) com ideias adaptadas por plataforma (TikTok ≠ Instagram ≠ LinkedIn ≠ YouTube).
- RF08: Copiloto de criação: a partir de um tema informado, devolver estratégia do conteúdo, roteiro e derivações (legenda, Stories, carrossel, versão LinkedIn, etc.).
- RF09: Biblioteca de conteúdo com CRUD e status (ideia, rascunho, em produção, aguardando aprovação, agendado, publicado, reaproveitável, arquivado).
- RF10: Registro de performance básica por conteúdo (visualizações, engajamento, etc. — pode ser manual no MVP).
- RF11: Analytics inteligente: interpretar dados e gerar recomendações textuais (ex.: “vídeos educativos têm X% mais retenção”).
- RF12: Dashboard do dia e da semana com plano de ação, metas e recomendações.
- RF13: Seleção e persistência do nível de autonomia (Copiloto, Gestor, Autopiloto).
- RF14: Histórico de decisões/recomendações aceitas ou rejeitadas pelo usuário (changelog simples).

## 5. Requisitos não funcionais

- **Segurança**:
  - Senhas nunca armazenadas em texto puro (usar hash).
  - Toda consulta ao banco usa parâmetros/prepared statements (nunca concatenação de string com dado do usuário).
  - Todo formulário validado no servidor (não só no navegador).
  - Nenhuma senha, chave de API ou string de conexão commitada no código — tudo em variável de ambiente.
  - Acesso restrito: um usuário só vê e edita os próprios dados (perfil, conteúdos, analytics).
  - Mensagens de erro não expõem detalhes internos do sistema.
  - HTTPS ativo (fornecido pelo Vercel).
- **Desempenho**:
  - Páginas principais do dashboard e da biblioteca devem carregar em tempo aceitável para uso educacional (sem requisitos de escala de produção).
- **Usabilidade**:
  - Interface clara, com feedback de carregamento nas gerações de conteúdo/estratégia.
  - Textos e recomendações em português.

## 6. Modelo de dados (DER simples)

Tabelas principais e relacionamentos:

- **users**
  - id (PK)
  - name
  - email (unique)
  - password_hash
  - autonomy_level (enum: copiloto | gestor | autopiloto)
  - created_at, updated_at

- **strategic_profiles**
  - id (PK)
  - user_id (FK → users)
  - objective
  - audience
  - positioning
  - tone
  - priority_platforms (JSON ou texto)
  - short_term_goals
  - medium_term_goals
  - raw_answers (JSON — respostas completas do onboarding)
  - created_at, updated_at

- **content_pillars**
  - id (PK)
  - strategic_profile_id (FK → strategic_profiles)
  - name (ex.: Educação, Autoridade, Bastidores)
  - description
  - weight_percent (opcional)

- **contents**
  - id (PK)
  - user_id (FK → users)
  - strategic_profile_id (FK → strategic_profiles)
  - theme
  - pillar_id (FK → content_pillars, nullable)
  - format (ex.: Reels, Carrossel, Stories, Vídeo, Post)
  - platform (ex.: Instagram, TikTok, LinkedIn, YouTube)
  - status (enum: ideia | rascunho | em_producao | aguardando_aprovacao | agendado | publicado | reaproveitavel | arquivado)
  - objective
  - hook
  - script
  - caption
  - cta
  - hashtags
  - scheduled_at (nullable)
  - published_at (nullable)
  - performance_views (nullable)
  - performance_engagement (nullable)
  - created_at, updated_at

- **recommendations**
  - id (PK)
  - user_id (FK → users)
  - type (ex.: estrategia, calendario, analytics, proxima_acao)
  - content (texto da recomendação)
  - accepted (boolean, nullable)
  - created_at

Relacionamentos:
- Um usuário tem um (ou mais versões de) Perfil Estratégico.
- Um Perfil Estratégico tem vários pilares.
- Um usuário tem vários conteúdos.
- Um conteúdo pertence a um usuário e opcionalmente a um pilar.
- Um usuário tem várias recomendações geradas pelo sistema.

## 7. Critérios de aceite

- Dado que o usuário não está autenticado, quando ele acessa qualquer página protegida, então é redirecionado para a tela de login.
- Dado que o usuário preenche e-mail e senha válidos, quando ele submete o formulário de login, então é autenticado e redirecionado para o dashboard.
- Dado que o usuário acabou de se cadastrar, quando ele completa o onboarding estratégico, então um Perfil Estratégico é salvo no banco e exibido na tela.
- Dado que existe um Perfil Estratégico, quando o usuário solicita a geração de pilares, então o sistema grava e exibe pelo menos 4 pilares coerentes com o perfil.
- Dado que o usuário informa um tema, quando ele solicita o copiloto de criação, então recebe estratégia, roteiro e pelo menos duas derivações (ex.: legenda + Stories) e pode salvá-las na biblioteca.
- Dado que o usuário cria um conteúdo, quando ele altera o status para “publicado” e registra visualizações, então o analytics consegue gerar pelo menos uma recomendação textual baseada nesses dados.
- Dado que o usuário está no dashboard, quando a página carrega, então ele vê o plano de ação do dia e a meta da semana (mesmo que ainda sem dados reais de performance).
- Dado que o usuário escolhe o nível de autonomia “Copiloto”, quando o sistema gera recomendações, então nenhuma ação é executada automaticamente — tudo fica pendente de aprovação.
- Dado que uma senha é cadastrada, quando ela é consultada no banco, então nunca aparece em texto puro (apenas o hash).
- Dado que um formulário é enviado com campos obrigatórios vazios, quando a validação do servidor roda, então a operação é rejeitada e uma mensagem de erro clara é retornada.

## 8. Decisão de stack e deploy

- **Stack recomendada**: Next.js (App Router) + TypeScript + Prisma (ou Drizzle) + banco PostgreSQL.
- **Banco na nuvem**: Neon Postgres (plano gratuito, integração nativa com Vercel).
- **Autenticação**: NextAuth.js ou solução equivalente com hash de senha (bcrypt/argon2).
- **Deploy**: Vercel (deploy automático a cada push na branch main do GitHub).
- **Justificativa**: 
  - A apostila recomenda decidir a stack já pensando no destino do deploy.
  - Vercel não roda PHP nativamente; Node.js/Next.js + Neon é o caminho mais simples e gratuito para o contexto educacional.
  - Permite variáveis de ambiente seguras, HTTPS automático e deploys de preview.
  - Atende aos requisitos mínimos da disciplina (cadastro/login + fluxo de negócio completo que grava e consulta dados em banco).

### Observações para o MVP (alinhado à apostila)

- Foco no **MVP da Fase 1 do roadmap original**: onboarding, Perfil Estratégico, estratégia/pilares, planejador básico, copiloto de conteúdo, biblioteca de conteúdos e analytics/recomendações simples.
- Integrações reais com Instagram, TikTok, YouTube e LinkedIn (publicação automática) ficam fora do escopo deste projeto acadêmico.
- Radar de tendências, gestão de comunidade, CRM e autopiloto completo ficam para fases posteriores.
- Toda geração de texto (estratégia, roteiros, legendas, recomendações) pode ser simulada no MVP com templates + lógica determinística ou com chamada a uma API de IA, desde que as chaves fiquem apenas em variáveis de ambiente.
