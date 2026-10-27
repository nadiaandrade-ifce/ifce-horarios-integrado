# Sistema de Horários — IFCE Campus Canindé

Ferramenta de montagem, publicação e exportação de horários docentes. Integra-se ao sistema PIT/RIT existente via exportação de CSV e JSON.

---

## Arquivos

| Arquivo | Acesso | Descrição |
|---|---|---|
| `painel-horarios.html` | Restrito — coordenação e comissão | Montagem de grade, mapa de salas, importação de lotação e exportações |

---

## Perfis de acesso

| Perfil | Emails | O que pode fazer |
|---|---|---|
| **Admin** | `nadia.andrade@ifce.edu.br` · `dg.caninde@ifce.edu.br` · `diren.caninde@ifce.edu.br` | Todas as abas, incluindo **Importar lotação** |
| **Comissão** | `cdh@caninde.ifce.edu.br` | Por turma · Por professor · Mapa de salas |

O login é feito por **magic link** — o usuário digita o email e recebe um link de acesso na caixa de entrada. Não há senha.

---

## Abas do painel

### 📋 Por turma
Montagem visual da grade de horários. Selecione o curso e a turma, clique nos slots `+` para alocar disciplinas. O sistema detecta conflitos de professor automaticamente. É possível editar código, nome e professor de cada aula diretamente no modal.

**🚫 Bloquear horário** — reserva um horário em várias turmas de uma vez, sem disciplina (ex.: atividades extracurriculares da quarta, A e B Tarde, nas turmas do Ensino Médio). Nenhuma disciplina pode ser colocada ali. O bloqueio aparece na grade, no quadro geral e no PDF por turma; **não** entra no CSV do PIT/RIT, no PDF de professores nem no mapa de salas. Para desfazer, use "Remover de todas" na mesma janela, ou clique no horário bloqueado e em Remover.

**👥 Turma conjunta** — ao alocar uma disciplina, marque as outras turmas que assistem à mesma aula, com o mesmo professor, no mesmo horário (ex.: optativa de História do Ceará para turmas de Eletrônica e Eventos). Não conta como conflito de professor; no CSV sai numa célula só (`Disciplina - CÓDIGO ELE A2 / EVT A2`). Se o outro curso tiver a mesma disciplina na planilha, o painel usa o card dele, baixando a carga dos dois. A caixa "🔎 Mostrar também disciplinas de outras turmas e cursos" permite escolher uma disciplina que está em outra turma (ex.: turma EXTRA).

**➕ Incluir disciplina** — para disciplina que não entrou na planilha de lotação (ex.: Programação Orientada a Objetos, 10.302.19, Prof. Lucas Oliveira). Escolha curso, turma (ou "Outra turma / semestre…"), código, nome, professor e CH semanal. A disciplina passa a aparecer na lista da turma como qualquer outra (com o selo "➕ incluída no painel"), entra nos PDFs e no CSV do PIT/RIT e viaja no JSON. Também dá para abrir pelo link "A disciplina não está na lista?" dentro da janela de alocação. Na mesma janela é possível editar ou remover as disciplinas incluídas. Se uma planilha reimportada depois trouxer a mesma disciplina (mesmo curso, semestre e código), as aulas passam sozinhas para o card da planilha.

**✂️ Disciplina dividida entre professores** — quando o professor da disciplina tem mais de um nome (ex.: `George/Ronaldo`, `Fulano e Beltrano`), o painel entende que são professores **já cadastrados** que dividem a disciplina, não um professor novo. Ao alocar, aparece a caixa "quem dá cada horário?", já sugerindo a divisão da carga (2h → 1h para cada; 4h em dois dias → um dia para cada). Cada horário entra no horário do professor escolhido. "Todos juntos no mesmo horário" é para quando os dois estão na sala ao mesmo tempo. Se um nome curto for ambíguo (ex.: dois professores George), o painel sugere o que leciona no curso e deixa trocar. Para escrever uma disciplina dividida à mão, separe os nomes por `/`.

Na carga horária (aba Por professor e PDF de professores), cada aula de disciplina dividida que ainda aparece para os dois conta **pela metade** para cada um (Projeto Integrador I, 4h → 2h para George e 2h para Ronaldo). No CSV do PIT/RIT, porém, cada horário vale 1h para quem estiver na linha — por isso, ao exportar o CSV com divisões pendentes, o painel avisa e oferece definir antes quem dá cada horário.

O botão **✂️ Disciplinas divididas** lista as aulas já gravadas com mais de um professor no mesmo nome (como o `George/Ronaldo` de Projeto Integrador I, RED S4) e distribui de uma vez.

**📌 Pendentes da turma** (versão de 27/10/2026) — abaixo da grade de cada turma aparece a lista das disciplinas da turma que ainda têm carga horária a alocar ("faltam 2h de 4h"). É só informativa: conforme as aulas são alocadas, a disciplina sai da lista. Disciplina de outro turno do mesmo semestre mostra o turno no cartão.

> ⚠️ Bloqueios e turmas conjuntas viajam no JSON a partir da versão de 16/10/2026. Disciplinas incluídas no painel viajam a partir da versão de 20/10/2026 (campo `disciplinasExtras`) — um painel mais antigo mostra as aulas delas como "aulas órfãs". Um painel mais antigo mostra os bloqueios como "aulas órfãs" — toda a comissão deve usar o HTML novo.

### 👤 Por professor
Visão consolidada automática — gerada a partir da grade montada em "Por turma". Não requer preenchimento manual. Grafias diferentes da mesma pessoa (ex.: Elizangela/Elizângela) aparecem como um professor só, e as aulas de disciplina dividida aparecem no horário de cada professor (não numa entrada à parte). A CH prevista conta a parte de cada um. Ao alocar uma disciplina dividida por esta aba, o painel preenche só a parte do professor selecionado.

**📌 Disciplinas pendentes do professor** (versão de 27/10/2026) — abaixo da grade do professor aparece a lista das disciplinas dele na planilha de lotação que ainda têm carga a alocar, com curso, turma e horas que faltam. É só informativa (ajuda a ver o que falta em trocas entre professores). Disciplina dividida mostra só a parte dele. Quando parte dos horários de uma disciplina foi alocada com outro professor (troca feita editando o nome na aula), o cartão avisa "↔️ 1h já alocada com Fulano". A lista segue a planilha: uma disciplina que o professor assumiu por troca não aparece na lista dele, e sim na do professor original.

### 🚪 Mapa de salas
Vinculação de aulas às salas. Selecione a sala à esquerda, depois escolha o curso e turma nos seletores. Os slots daquela turma sem sala aparecem como botões — clique para alocar. Conforme aloca, a lista diminui. Cada slot pode estar em uma sala diferente (flexibilidade total por aula).

**A sala acompanha a aula (versão de 22/10/2026).** Remover uma aula libera a sala daquele horário. Se a mesma disciplina for recolocada na mesma turma (mudança de horário), a sala vai junto, desde que esteja livre no horário novo; se estiver ocupada, o painel avisa. Uma disciplina diferente colocada no horário nunca herda a sala de quem estava ali. "Limpar grade" limpa também as salas da turma. Ao importar um JSON, salas presas em horário vazio são liberadas automaticamente.

**🔎 Conferir salas** — lista (1) disciplinas da mesma turma com salas diferentes, ou parte com sala e parte sem, e (2) tudo o que está em cada laboratório, com ⚠️ quando a aula é de outro curso num laboratório de uso habitual de um curso. Cada linha tem "Tirar da sala".

### 📊 Importar lotação *(somente Admin)*
Importação da planilha Excel de oferta e lotação (`.xlsx`). O sistema lê todas as abas por curso, detecta semestres e turnos automaticamente e salva o catálogo no Firestore. Na próxima vez que qualquer membro fizer login, o catálogo atualizado é carregado automaticamente — sem necessidade de reimportar.

---

## Fluxo de trabalho semestral

### 1. Diren importa a lotação
Abre o painel → aba **Importar lotação** → digita o semestre (ex: `2026.2`) → seleciona a planilha `.xlsx`. O catálogo é salvo no Firestore e fica disponível para toda a comissão.

### 2. Comissão monta os horários (trabalho sequencial)

```
Membro A monta seus cursos → Exportar JSON → envia para Membro B
        ↓
Membro B importa o JSON do A → monta seus cursos → Exportar JSON → envia para Membro C
        ↓
... repete para cada membro ...
        ↓
Diren recebe o JSON final → Importar JSON → confere a grade completa
```

> ⚠️ **Regra importante:** nunca dois membros trabalham ao mesmo tempo no mesmo arquivo. O fluxo é sequencial — cada membro importa o JSON mais recente antes de começar.

O JSON acumula o trabalho de todos **porque cada membro importa o JSON anterior antes de começar**.

> ⚠️ **Importar um JSON substitui tudo o que está na tela** (não mescla). Antes de substituir, o painel baixa automaticamente um backup do estado atual. Por isso, sempre importe o JSON mais recente *antes* de começar a alocar.

### 3. Mapa de salas
Após a grade de turmas estar completa, cada membro vincula suas turmas às salas antes de exportar o JSON.

### 4. Diren gera as versões finais

| Exportação | Botão | Destino |
|---|---|---|
| Grade por turma | `PDF turma` | Afixar / distribuir |
| Grade por professor | `PDF professor` | Arquivo único com índice alfabético |
| Horários para PIT/RIT | `Exportar → PIT/RIT (CSV)` | Importar no `admin-teste.html` do sistema PIT/RIT |
| Backup da grade | `Exportar JSON` | Guardar como histórico |

---

> Os PDFs saem com o semestre do catálogo de lotação carregado (ex.: 2027.1), mesmo que o semestre vigente do sistema ainda seja outro (ex.: 2026.2).

## Formato do CSV exportado

O arquivo `Horarios_2026_2.csv` gerado pelo botão **Exportar → PIT/RIT (CSV)** segue o mesmo padrão histórico dos semestres anteriores:

```
Professor;;Seg;Ter;Qua;Qui;Sex
Professor;A Manha 7:45 - 8:45;;;;;
Professor;C Manhã 10:00 - 11:00;;;Disciplina - 10.301.38 Redes S2;;
```

É lido diretamente pelo `admin-teste.html` do sistema PIT/RIT sem nenhuma modificação.

---

## Firebase

| Projeto | Uso |
|---|---|
| `sistema-integrado-2bf85` | **Este sistema** (desenvolvimento/testes) |
| `proposta-completa-dev` | Sistema PIT/RIT em produção — **não mexer** |

### Coleções usadas no Firestore

| Coleção | Conteúdo |
|---|---|
| `catalogo_disciplinas/{semestre}` | Catálogo importado da planilha de lotação |
| `configuracoes/status` | Semestre vigente (lido pelo PIT/RIT) |

---

## Formatos de semestre reconhecidos na planilha

O importador detecta automaticamente qualquer um destes formatos:

| Na planilha | No sistema |
|---|---|
| PRIMEIRO ANO — TURNO INTEGRAL | A1 — Integral |
| SEGUNDO ANO — TURNO INTEGRAL | A2 — Integral |
| ANO I — TURNO INTEGRAL | A1 — Integral |
| SEMESTRE IV — TURNO INTEGRAL | S4 — Integral |
| SEMESTRE 6 — TURNO INTEGRAL | S6 — Integral |
| SEMESTRE 2 | S2 |
| S4 — Tarde | S4 — Tarde |
| ANO 1 — TURNO: INTEGRAL · 1° ano - Turno Integral | A1 — Integral |
| SEMESTRE 6 — TURNO VARIADO | S6 — Integral |
| SEMESTRE EXTRA · EXTRA · Disciplinas Extra Curriculares · Disciplinas extras ou de outro currículo | EXTRA (turma própria) |
| DEMANDAS DE OUTROS CURSOS | ignorado (informativo — as disciplinas já estão nas abas dos cursos) |

> Um cabeçalho de bloco precisa ocupar **uma única célula** na linha. Textos na coluna "Proposições" (ex.: "5° semestre de Eventos") não são lidos como cabeçalho.
>
> Disciplinas **sem código** são identificadas pelo nome completo. Se o nome mudar na planilha, a aula já alocada fica órfã.

---

## Cursos reconhecidos na planilha

Cada aba da planilha vira um curso separado. O nome da aba é comparado sem acentos:

| Aba contém | Curso |
|---|---|
| ED. FÍSICA / EDUCAÇÃO FÍSICA / CLEF | CLEF — Licenciatura em Educação Física |
| CEVEN / EVENTOS | EVT — Eventos (Integrado ao Ensino Médio) |
| CELETRO / ELETRÔNICA | ELE — Eletrônica (Integrado ao Ensino Médio) |
| PEDAGOGIA · MATEMÁTICA · MÚSICA · REDES · TURISMO · INFORMÁTICA · ADS | PED · MAT · MUS · RED · TUR · INF · ADS |
| CENTRO DE IDIOMAS | EXT |

Uma aba que não esteja nesta lista **não é mais agrupada em "GEN"**: vira um curso próprio, com sigla gerada a partir do nome da aba, e o log da importação mostra um aviso ⚠️.

---

## Salas cadastradas

29 espaços do campus Canindé: blocos 1, 2 e 3, laboratórios de desenvolvimento, turismo, matemática, brinquedoteca, redes, música, eletrônica, informática I (bloco 1 — coord. Danilo Avilar, 17 PCs) e informática II (bloco 3), sala de videoconferência e sala extra do bloco administrativo. Dados conforme o "Estudo e Mapeamento de Espaços Físicos – 2026.1".

---

## Histórico de semestres

Cada semestre importado fica guardado separadamente no Firestore (`catalogo_disciplinas/2026.2`, `catalogo_disciplinas/2027.1`, etc.). O sistema carrega sempre o mais recente. O histórico nunca é apagado automaticamente.

---

## Tecnologias

- HTML + CSS + JavaScript puro (sem framework, funciona offline após carregado)
- Firebase Authentication (magic link por email)
- Firebase Firestore (catálogo de disciplinas)
- SheetJS (leitura de Excel, carregado sob demanda)
- Impressão via `window.print()` com CSS `@media print`

---

## Relação com o sistema PIT/RIT

Este sistema **alimenta** o PIT/RIT. O fluxo completo do semestre:

```
1. Diren importa planilha de lotação aqui
2. Comissão monta a grade e mapa de salas
3. Diren exporta o CSV → importa no admin-teste.html do PIT/RIT
4. Professores abrem o PIT/RIT → horário pré-preenchido → complementam o plano
5. Alunos consultam horários via consulta-teste.html
```

Os dois sistemas são completamente independentes e usam projetos Firebase diferentes — nenhuma alteração aqui afeta o PIT/RIT em produção.

---

*IFCE Campus Canindé · Sistema de Horários · 2026*
