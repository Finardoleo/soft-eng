# Feature Specification: Pesquisa, filtragem e candidatura de projetos de extensão - Protótipo de alta fidelidade

## User Scenarios & Testing 

### User Story 1 – Descobrir projetos por busca e filtros (Priority: P1)

Como estudante ou visitante, quero buscar e filtrar projetos por área, curso, carga horária e modalidade, para encontrar um projeto compatível com meus interesses e com as horas que preciso cumprir para formar-me.

**Why this priority**: resolve a falta de visibilidade e entrega valor mesmo sem candidatura.

**Independent Test**: sem login, buscar e filtrar a lista de projetos de exemplo e conferir cada cenário abaixo.

**Acceptance Scenarios**:

1. **Dado** projetos publicados, **quando** o usuário busca "meio ambiente", **então** são listados os projetos com o termo no título, na descrição ou no nome do coordenador.
2. **Dado** os filtros "Remoto" e "30–45 h", **então** todos os resultados atendem aos dois.
3. **Dado** uma lista de resultados, **então** cada card mostra título, coordenador, carga horária, modalidade e vagas disponíveis.
4. **Dado** que nada atende aos critérios, **então** aparece "Nenhum projeto encontrado" com a opção "Limpar filtros".
5. **Dado** carga horária mínima maior que a máxima, **então** o sistema mostra validação e não executa a busca.
6. **Dado** projetos em rascunho, **então** eles nunca aparecem.
7. **Dado** "Incluir encerrados" marcado, **então** projetos encerrados aparecem com a etiqueta "Encerrado" e sem opção de candidatura.

### User Story 2 – Analisar, aprovar ou recusar candidaturas (Priority: P1)

Como coordenador, quero ver os candidatos do meu projeto e aprovar ou recusar cada um, para formar a equipe sem ultrapassar o número de vagas.

**Why this priority**: fecha o ciclo; sem ela a candidatura não gera resultado.

**Independent Test**: autenticado como coordenador de um projeto com candidaturas "Em análise", percorrer os cenários abaixo.

**Acceptance Scenarios**:

1. **Dado** que o coordenador abre seu projeto, **então** vê nome, curso, semestre, data e status de cada candidatura e o contador "X de N vagas preenchidas".
2. **Dado** vaga livre e candidatura "Em análise", **quando** aprova, **então** o status vira "Aprovada", o contador sobe 1 e o estudante é notificado.
3. **Dado** que o coordenador recusa, **então** o sistema exige justificativa; ao confirmar, o status vira "Recusada" e o estudante é notificado com a justificativa.
4. **Dado** vagas esgotadas, **então** "Aprovar" fica desabilitado com o aviso "Vagas esgotadas", e um pedido direto ao servidor também é rejeitado.
5. **Dado** um usuário que não é o coordenador do projeto, **então** recebe "Acesso negado", sem dados.
6. **Dado** que a candidatura deixou de estar "Em análise" (cancelada ou já decidida), **então** o sistema avisa e recarrega a lista.

### User Story 3 – Lista de espera e reversão de decisões (Priority: P2)

Como coordenador, quero manter candidatos em espera quando as vagas acabam e reverter decisões até o início do projeto.

**Why this priority**: completa o ciclo de vagas.

**Independent Test**: em um projeto com todas as vagas preenchidas e candidaturas pendentes, percorrer os cenários abaixo.

**Acceptance Scenarios**:

1. **Dado** vagas preenchidas, **então** as candidaturas "Em análise" aparecem em "Lista de espera", ordenadas pela data (mais antiga primeiro), e só podem ser recusadas.
2. **Dado** uma vaga liberada (reabertura ou cancelamento), **então** "Aprovar" volta a ficar disponível, sem promoção automática.
3. **Dado** a data de início do projeto, **quando** ela chega, **então** as candidaturas "Em análise" viram "Recusada" com a justificativa "Vagas preenchidas" e os estudantes são notificados.
4. **Dado** uma candidatura "Aprovada" ou "Recusada" até a véspera do início, **quando** o coordenador a reabre com motivo, **então** ela volta a "Em análise", o estudante é notificado com o motivo e, se estava aprovada, a vaga é liberada.
5. **Dado** a data de início ou posterior, **então** a reabertura é impossível.

### Edge Cases

- Carga horária negativa ou não numérica: validação, sem busca.
- Duas sessões decidindo a mesma candidatura: a segunda é rejeitada com aviso.
- Reabrir sem motivo ou recusar sem justificativa: confirmação bloqueada.
- Projeto sem candidaturas: estado vazio.

## Requirements 

### Functional Requirements

- **FR-001**: O sistema DEVE permitir buscar por palavra-chave no título, na descrição ou no nome do coordenador.
- **FR-002**: O sistema DEVE permitir filtrar por área, curso, carga horária (faixa inclusiva), modalidade e situação das inscrições, combinando todos os critérios.
- **FR-003**: O sistema DEVE validar a faixa de carga horária e NÃO DEVE executar a busca se ela for inválida.
- **FR-004**: O sistema DEVE listar apenas projetos publicados.
- **FR-005**: O sistema DEVE listar por padrão projetos com inscrições abertas e incluir os encerrados só mediante "Incluir encerrados", sem opção de candidatura.
- **FR-006**: O sistema DEVE mostrar, em cada resultado, título, coordenador, carga horária, modalidade e vagas disponíveis, e permitir abrir os detalhes.
- **FR-007**: O sistema DEVE permitir busca e filtros sem login.
- **FR-008**: O sistema DEVE listar ao coordenador as candidaturas de seus projetos com nome, curso, semestre, data e status, com paginação.
- **FR-009**: O sistema DEVE mostrar o detalhe da candidatura (nome, curso, semestre, e-mail e motivação).
- **FR-010**: O sistema DEVE permitir aprovar uma candidatura "Em análise" somente se houver vaga livre, e NÃO DEVE permitir aprovações além das vagas, mesmo por pedido direto.
- **FR-011**: O sistema DEVE permitir recusar uma candidatura "Em análise" e DEVE exigir justificativa.
- **FR-012**: O sistema DEVE restringir a lista de candidaturas ao coordenador responsável.
- **FR-013**: O sistema DEVE atualizar o status e notificar o estudante, na plataforma, a cada decisão ou reabertura.
- **FR-014**: O sistema DEVE manter candidaturas "Em análise" em lista de espera quando as vagas estiverem preenchidas, sem promoção automática.
- **FR-015**: O sistema DEVE recusar automaticamente, na data de início, as candidaturas ainda "Em análise", com a justificativa "Vagas preenchidas".
- **FR-016**: O sistema DEVE permitir reabrir uma candidatura decidida até a véspera do início, com motivo obrigatório, liberando a vaga se ela estava aprovada.

### Key Entities

- **Projeto**: título, descrição, área, cursos-alvo, carga horária, modalidade, vagas, período de inscrição, data de início, situação.
- **Candidatura**: motivação, data, status (Em análise, Aprovada, Recusada, Cancelada), justificativa ou motivo.
- **Estudante**: nome, e-mail, curso, semestre.
- **Coordenador**: nome, e-mail, departamento.
- **Notificação**: mensagem, data, lida.

## Success Criteria 

### Measurable Outcomes

- **SC-001**: Resultados de busca em até 2 s com até 2.000 usuários simultâneos.
- **SC-002**: A lista de candidaturas com centenas de registros permanece fluida.
- **SC-003**: Nenhum dado de candidato é exibido a quem não é o coordenador responsável.
- **SC-004**: Funciona em Chrome, Firefox, Edge e Safari, em desktop e celular, sem plugins.
- **SC-005**: Uma pessoa externa completa o fluxo buscar → ver detalhes em menos de 2 minutos, sem ajuda.

## Assumptions

- Login é pré-condição, simulada por seleção de perfil; fora de escopo: aprovação de projetos pela coordenação do curso, cadastro de projetos e envio real de e-mail.
- Cancelamento pelo estudante existe e libera a vaga.
- [NEEDS CLARIFICATION: o semestre aparece também na lista de candidaturas ou só no detalhe?]
- [NEEDS CLARIFICATION: o estudante pode se recandidatar após recusa ou cancelamento?]
