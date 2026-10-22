# Contribuindo — psoft-atv04-11

Este guia define **como** trabalhamos: fork, submodule, snapshot, branches, commits, PRs e review.
O **quê** de cada tarefa mora na issue correspondente — se um texto repetir uma regra daqui, o CONTRIBUTING é a fonte da verdade.

Board: [GitHub Project](https://github.com/users/matheusvir/projects/7) — To Do → In Progress → Review → Done.

## Glossário

| Termo | Significado |
|---|---|
| **upstream** | repositório oficial do projeto open source |
| **fork** | cópia do upstream no perfil do responsável; origem do submodule |
| **SHA original** | commit do upstream correspondente ao trecho escolhido ("antes") |
| **SHA refatorado** | commit no fork, na branch do PR upstream, com o refactoring aplicado ("depois") |
| **snapshot** | cópia fiel dos arquivos analisados em `smells/<smell>/`, versionada no repo do grupo |
| **tag `v0-<smell>`** | marca o estado original no repo do grupo (setup) |
| **tag `v1-<smell>`** | marca o estado refatorado (merge do PR interno) |
| **PR upstream** | PR do fork para o upstream, em inglês; é a contribuição real ao projeto |
| **PR interno** | PR no repo do grupo (`entrega/<smell>` → `main`); é o "PR entre as versões" e onde a revisão acontece |

## Estrutura

```
<projeto>/       # submodule → fork do projeto, no perfil do responsável
smells/<smell>/  # fiél snapshot do trecho analisado (antes: tag v0; depois: merge do PR interno)
docs/            # provenance.md, slides.pdf
```

O snapshot existe porque o GitHub **não renderiza diff de submodule** (mostra só `Subproject commit abc..def`), e o professor/revisor vão ler na web. O diff navegável das versões mora no PR interno; a contribuição real, no PR upstream.

## Fluxo

### Setup (issues de extração)
1. Faça fork do projeto no seu perfil, sincronizado com o upstream. Guarde o SHA original.
2. Antes de escolher, confirme que o projeto compila e roda os testes dele localmente (ver "Escolha do projeto").
3. Adicione o submodule e pine no SHA original:
   ```bash
   git submodule add <url-do-fork> <nome-do-projeto>
   git -C <nome-do-projeto> checkout <sha-original>
   git add .gitmodules <nome-do-projeto>
   ```
4. Copie, **sem editar**, os arquivos do trecho analisado para `smells/<smell>/` (1–3 arquivos) + o `LICENSE` do projeto.
5. Preencha o bloco do projeto em `docs/provenance.md`.
6. Commit na `main` referenciando a issue, e **tag `v0-<smell>`**:
   ```bash
   git add smells/<smell>/ docs/provenance.md
   git commit -m "chore: registrar <projeto> para <smell> (#n)"
   git push origin main
   git tag v0-<smell> && git push origin v0-<smell>
   ```

### Refactoring (issues de refactor)
1. Branch `refactor/<objetivo-em-inglês>` no fork, a partir do SHA original.
2. Refactoring + testes de caracterização. Commits e comentários no fork em inglês.
3. Abra o **PR upstream** (fork → repo oficial), em inglês. Registre a URL na issue.
4. No repo do grupo, na branch `entrega/<smell>`:
   ```bash
   git checkout -b entrega/<smell>
   # atualize smells/<smell>/ com o mesmo diff do refactoring
   git -C <nome-do-projeto> fetch origin && git -C <nome-do-projeto> checkout <sha-refatorado>
   # atualize docs/provenance.md (SHA refatorado + URL do PR upstream)
   git add smells/<smell>/ <nome-do-projeto> docs/provenance.md
   git commit -m "refactor: eliminar <smell> em <projeto> (#n)"
   git push -u origin entrega/<smell>
   ```
5. Abra o **PR interno** `entrega/<smell>` → `main` com `Closes #n` no corpo.
6. Após o merge (revisão no PR interno, ver abaixo): tag `v1-<smell>` + push da tag.

## Comandos úteis

```bash
git submodule status                              # ver SHAs pinados
git -C <projeto> log --oneline -1                 # commit apontado
git diff v0-<smell>..main -- smells/<smell>/      # antes → depois legível
```

## Commits

**No fork/upstream:** Conventional Commits, em inglês, sem referência às issues do grupo (o upstream não as conhece):

```
refactor: replace obvious comments with self-documenting names
test: add characterization tests for the refactored path
```

**No repo do grupo:** Conventional Commits em português, referenciando a issue:

```
chore: registrar comments em <projeto> (#1)
refactor: eliminar comentários redundantes em <projeto> (#2)
```

## Branches

- **No fork:** `refactor/<objetivo-em-inglês>` — o nome deve ler-se bem na tela do PR (ex.: `refactor/eliminate-duplicated-code`).
- **No repo do grupo:** mudanças de `docs:` e `chore:` vão **direto na `main`**, commit referenciando a issue. O PR interno (`entrega/<smell>` → `main`) existe **apenas** para o refactoring de código — é o "PR entre as versões" que o professor avalia; cerimônia de PR para documentos é ruído.
- Um smell por branch, um smell por PR — nunca misture dois no mesmo diff.
- **Sem force-push** depois de PR aberto (qualquer dos dois): o SHA pinado pelo submodule precisa continuar existindo.

## Pull Requests

### PR upstream (contribuição real)
1. Target: default branch do upstream. Um PR por smell.
2. **Título, corpo e comentários em inglês.** Título = objetivo do refactoring: `Refactor: extract class to resolve divergent change`.
3. Template:

```markdown
## What changed
<summary of the diff>

## Why the smell is gone
<explain how the code smell was eliminated>

## Evidence
- [ ] Characterization tests passing
- [ ] Behavior unchanged
```

4. Não solicite review ao mantenedor e não comente em português. O aceite (ou silêncio) do mantenedor **não bloqueia** a conclusão da issue.

### PR interno (PR entre as versões)
- Base/compare: `entrega/<smell>` → `main`, com `Closes #n` no corpo.
- O corpo resume: o que mudou, por que o smell sumiu, URL do PR upstream, evidência dos testes.
- A revisão acontece **aqui**, no diff `v0-<smell>` → HEAD de `smells/<smell>/` + ponteiro do submodule.
- Nota: issues de docs/organização **não geram PR** — seus entregáveis entram por commit direto na `main`.

## Review (PR interno)

- Autor abre o PR interno → move o card para **Review** e menciona o revisor na issue.
- Revisor lê o diff do PR interno e o PR upstream; aprova **justificando por que o smell sumiu** — um simples "LGTM" não constitui aprovação.
- Mudanças solicitadas → card volta para **In Progress**.
- Autor faz o squash merge; o merge fecha a issue; tag `v1-<smell>`; card → **Done**.
- O revisor de cada issue está definido no corpo dela. Ninguém revisa o próprio trabalho; issues **sem revisor** (marcada assim no corpo) não entram no fluxo de revisão.

## Regras do código

- Java na versão e build do próprio projeto upstream (`mvnw`/`gradlew` quando existirem) — não adapte o projeto para atender a versão "do grupo".
- Refactoring muda estrutura, não comportamento: teste que quebra = refactoring errado.
- Escopo do diff: 1–3 arquivos do trecho analisado. Acima disso, escolha outro trecho.

## Escolha do projeto

- Licença **MIT ou Apache-2.0**, obrigatória — sem licença explícita, descarte.
- Compila e roda os testes localmente **antes** de ser escolhido.
- **Um submodule por projeto.** Se dois smells usarem o mesmo projeto, o segundo reusa o submodule e cria branch própria no fork.
- Leia o `CONTRIBUTING.md` e o `CODE_OF_CONDUCT.md` **do projeto** (não este arquivo) antes de abrir o PR.

## O que é avaliado

- Tags `v0-<smell>` (antes) e `v1-<smell>` (depois) no repo do grupo.
- PRs internos `entrega/<smell>` → `main`: o "pull request entre as versões" do enunciado.
- PRs upstream: a contribuição real ao projeto open source (URL registrada em `docs/provenance.md`).
- `docs/slides.pdf` e o vídeo de apresentação (≤ 10 min), com link no `README.md`.
