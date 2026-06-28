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

### 👤 Por professor
Visão consolidada automática — gerada a partir da grade montada em "Por turma". Não requer preenchimento manual.

### 🚪 Mapa de salas
Vinculação de aulas às salas. Selecione a sala à esquerda, depois escolha o curso e turma nos seletores. Os slots daquela turma sem sala aparecem como botões — clique para alocar. Conforme aloca, a lista diminui. Cada slot pode estar em uma sala diferente (flexibilidade total por aula).

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

O JSON acumula o trabalho de todos. Importar um JSON nunca apaga o que já foi feito — apenas soma.

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

---

## Salas cadastradas

25 espaços do campus Canindé: blocos 1, 2 e 3, laboratórios de desenvolvimento, turismo, matemática, brinquedoteca, redes, música, eletrônica, sala de videoconferência.

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
