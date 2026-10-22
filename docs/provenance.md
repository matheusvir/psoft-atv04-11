# Provenance — Projetos e Trechos Analisados

Registro obrigatório de cada trecho de código open source usado na atividade.
**Um bloco por projeto e smell** — se o mesmo projeto servir a mais de um smell, um bloco para cada. Preencha todos os campos, o que é o que o revisor e a apresentação vão citar.

As issues de extração (#1, #3, #5) preenchem o bloco inicial; **SHA refatorado** e **PR upstream** são preenchidos pelas issues de refactoring (#2, #4, #6).

## Formato do bloco

```markdown
## <nome do projeto>

- **Smell:** <comments | duplicated-code | divergent-change>
- **Issue:** #<número>
- **URL upstream:** https://github.com/<org>/<repo>
- **SHA original:** <permalink de blob nesse SHA, com #L<início>-L<fim>>
- **SHA refatorado:** <permalink do commit no fork — a preencher pela issue de refactoring>
- **PR upstream:** <url — a preencher pela issue de refactoring>
- **Licença (SPDX):** <MIT | Apache-2.0>
- **Path no upstream:** <path> — <arquivo(s) e linhas do trecho>, copiado para `smells/<smell>/`
- **Por que é code smell:** <3–5 linhas, específicas do trecho; para divergent change, nomear os eixos de mudança e os métodos de cada eixo>
```

## Exemplo — projeto-de-exemplo (fictício)

> **EXEMPLO — não é um projeto real da atividade; bloco só para fixar o formato.**

- **Smell:** duplicated-code
- **Issue:** #3
- **URL upstream:** https://github.com/exemplo-org/registrar-exemplo
- **SHA original:** https://github.com/exemplo-org/registrar-exemplo/blob/0123456789abcdef/src/main/java/org/exemplo/Service.java#L40-L78
- **SHA refatorado:** _(a preencher pela issue de refactoring)_
- **PR upstream:** _(a preencher pela issue de refactoring)_
- **Licença (SPDX):** Apache-2.0
- **Path no upstream:** `src/main/java/org/exemplo/Service.java` — duplicação entre `save()` (L40–58) e `update()` (L60–78), copiado para `smells/duplicated-code/`
- **Por que é code smell:** a lógica de validação e persistência está duplicada em dois métodos públicos; qualquer mudança de regra (ex.: nova validação) precisa ser aplicada duas vezes, e não há sinal no código de que os dois devem permanecer idênticos.
