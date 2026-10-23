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



## Apache Commons CLI

- **Smell:** Comments
- **Issue:** #1
- **URL upstream:** [https://github.com/apache/commons-cli](https://github.com/apache/commons-cli)
- **SHA original (trechos analisados):**
  - [Construtor `Parser()`, linhas 35–52](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L35-L52)
  - [Getters `getOptions()` e `getRequiredOptions()`, linhas 75–93](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L75-L93)
  - [Método `processOption()`, linhas 247–264](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L247-L264)
  - [Setter `setOptions()`, linhas 305–315](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L305-L315)
- **SHA refatorado:** A preencher após a refatoração, na issue correspondente.
- **PR upstream:** A preencher após a abertura do PR de refatoração.
- **Licença (SPDX):** Apache-2.0
- **Path no upstream:** `src/main/java/org/apache/commons/cli/Parser.java` — construtor `Parser()` (linhas 35–52), getters `getOptions()` e `getRequiredOptions()` (linhas 75–93), método `processOption()` (linhas 247–264) e setter `setOptions()` (linhas 305–315); arquivo copiado para `smells/comments/Parser.java`.

- **Por que é code smell:** O arquivo contém comentários redundantes em diferentes métodos. No construtor, o Javadoc genérico e o comentário `// empty` descrevem uma operação sem comportamento adicional; nos getters e no setter, os Javadocs repetem os nomes dos métodos e os significados dos parâmetros ou retornos, sem fornecer informações relevantes adicionais. Em `processOption()`, cinco comentários apenas narram as condições e instruções que aparecem logo abaixo deles. Esses comentários não esclarecem decisões não óbvias nem regras de negócio, tornando-se candidatos à remoção para reduzir ruído e evitar divergências entre comentários e implementação.
