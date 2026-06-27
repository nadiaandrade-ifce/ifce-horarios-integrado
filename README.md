# Sistema Integrado de Horários — IFCE Campus Canindé

Sistema de montagem, publicação e consulta de horários docentes. Integra a grade de turmas e o mapa de salas com o sistema PIT/RIT, eliminando a necessidade de alimentar o quadro de horários manualmente.

---

## Arquivos

| Arquivo | Acesso | Descrição |
|---|---|---|
| `painel-horarios.html` | Restrito — coordenação | Montagem de grade, mapa de salas, publicação no Firebase |
| `consulta.html` | Público — alunos | Consulta de horários por professor e por turma |

---

## Como funciona

```
Coordenação monta a grade visualmente
        ↓
Clica em "Publicar semestre"
        ↓
Firebase armazena automaticamente
        ↙               ↘
Professores             Alunos
PIT/RIT lê              consulta.html
horário do              exibe aulas e
docente                 atendimento
```

O semestre vigente é a chave de tudo. Ao definir um semestre como vigente na aba Administração e publicar a grade, todos os sistemas leem automaticamente os dados daquele semestre.

---

## Passo a passo de uso

### 1. Configuração inicial (primeira vez)

1. Abrir `painel-horarios.html` no navegador
2. Fazer login com o email de administrador autorizado (recebe link por email)
3. Ir para a aba **Administração**
4. Cadastrar o semestre no formato `AAAA.N` — exemplo: `2026.2`
5. Clicar em **Definir vigente**

### 2. Montar a grade (aba "Por turma")

1. Selecionar o curso e a turma no topo da página
2. Clicar no `+` de qualquer slot para abrir o modal de disciplinas
3. Escolher a disciplina — o professor padrão é preenchido automaticamente
4. Editar o professor no campo caso necessário (substituição, compartilhamento)
5. Confirmar — o sistema detecta conflitos de professor automaticamente
6. Repetir para todas as turmas e cursos

> A grade é salva automaticamente no navegador a cada alteração. Para transferir entre computadores, use **Exportar JSON** / **Importar JSON**.

### 3. Alocar salas (aba "Mapa de salas")

1. Selecionar a sala na lista à esquerda
2. Clicar no `+` de um slot para ver as aulas que acontecem naquele horário
3. Selecionar a aula para vinculá-la à sala
4. Clicar na aula já alocada para removê-la da sala

### 4. Conferir por professor (aba "Por professor")

Selecionar o nome do docente para visualizar a grade individual consolidada — todas as turmas, disciplinas e salas em uma única visão.

### 5. Publicar (aba "Publicar semestre")

1. Verificar o resumo: cursos, turmas, aulas alocadas, professores
2. Clicar em **Publicar grade vigente**
3. Aguardar a confirmação no log — leva alguns segundos

Após publicar, a grade fica disponível imediatamente para alunos (`consulta.html`) e para o sistema PIT/RIT dos professores.

### 6. Gerar PDFs

Na aba **Por turma**, três opções de exportação:

- **PDF turma** — uma página por turma com grade completa
- **PDF professor** — uma página por docente com todos os horários
- **PDF salas** — uma página por sala com a ocupação semanal

### 7. Semestre seguinte

1. Ir para **Administração** → cadastrar o novo semestre → definir como vigente
2. Montar a grade do zero (ou importar JSON do semestre anterior como base)
3. Publicar

O histórico de todos os semestres anteriores fica preservado no Firebase.

---

## Consulta pública (`consulta.html`)

Página independente para alunos — sem login, sem acesso ao painel.

**Por professor:** busca pelo nome, exibe grade com aulas e horários de atendimento.

**Por turma:** lista todas as turmas publicadas com filtro por curso. Cada card expande e mostra as aulas com dia, horário, professor e sala.

O aluno vê apenas aulas e atendimento a alunos. Atividades internas do docente (preparação, pesquisa, extensão, gestão) não são exibidas.

---

## Firebase

O sistema usa dois projetos Firebase totalmente independentes:

| Projeto | Uso |
|---|---|
| `proposta-completa-dev` | Sistema PIT/RIT atual (em produção — **não mexer**) |
| `sistema-integrado-2bf85` | Este sistema (desenvolvimento e testes) |

### Coleções gravadas no Firestore

| Coleção | Conteúdo |
|---|---|
| `configuracoes/status` | Semestre vigente atual |
| `semestres/{id}` | Semestres cadastrados |
| `horarios/{sem}_{prof}` | Grade semanal de cada docente — lida pelo PIT/RIT |
| `grade_turmas/{sem}_{turma}` | Grade por turma — lida pela consulta pública |

---

## Administradores autorizados

Editar o array `ADMINS` no início do bloco Firebase do `painel-horarios.html`:

```js
const ADMINS = ['email1@ifce.edu.br', 'email2@ifce.edu.br'];
```

Apenas emails listados aqui conseguem fazer login no painel.

---

## Alternativa: importar planilha de lotação

Na aba **Administração** há uma zona de upload que aceita `.xlsx`, `.xls` ou `.csv` com o quadro de horários dos professores no formato do aSc TimeTables. Essa importação grava os horários por docente diretamente na coleção `horarios` do Firestore, sem passar pela montagem visual da grade de turmas.

Use essa opção apenas como alternativa quando a montagem visual não for viável. O fluxo principal é sempre: montar → publicar.

---

## Tecnologias

- HTML + CSS + JavaScript puro (sem framework)
- Firebase Firestore (banco de dados em tempo real)
- Firebase Authentication (magic link por email)
- SheetJS (leitura de arquivos Excel, carregado sob demanda)
- Impressão via `window.print()` com CSS `@media print`

---

## Relação com o sistema PIT/RIT

Este sistema **alimenta** o PIT/RIT. Quando a coordenação publica a grade, os horários dos docentes ficam disponíveis na coleção `horarios` do Firestore — a mesma coleção que o PIT/RIT usa para pré-preencher o plano de trabalho de cada professor.

O fluxo completo do semestre é:

```
1. Coordenação publica a grade aqui
2. Professor abre o PIT/RIT → horário já aparece preenchido
3. Professor complementa com atendimento, pesquisa, extensão etc.
4. Aluno consulta horários em consulta.html
```

---

*IFCE Campus Canindé — Sistema desenvolvido para uso interno*
