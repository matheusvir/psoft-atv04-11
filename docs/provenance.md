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

## JavaParser

- **Smell:** duplicated-code
- **Issue:** #3
- **URL upstream:** https://github.com/javaparser/javaparser
- **SHA original:** [JavaParserClassDeclaration.java](https://github.com/javaparser/javaparser/blob/0a9424cefafae806e6e97c56765dbe5ed5e192ae/javaparser-symbol-solver-core/src/main/java/com/github/javaparser/symbolsolver/javaparsermodel/declarations/JavaParserClassDeclaration.java#L323-L419) e [JavaParserRecordDeclaration.java](https://github.com/javaparser/javaparser/blob/0a9424cefafae806e6e97c56765dbe5ed5e192ae/javaparser-symbol-solver-core/src/main/java/com/github/javaparser/symbolsolver/javaparsermodel/declarations/JavaParserRecordDeclaration.java#L386-L482)
- **SHA refatorado:** [`4c1386d2bea6b6f32a650a8a861e942e81c9d8a3`](https://github.com/RailtonDantas/javaparser/commit/4c1386d2bea6b6f32a650a8a861e942e81c9d8a3) — commit no fork `RailtonDantas/javaparser`, branch `refactor/eliminate-duplicated-code`.
- **PR upstream:** https://github.com/javaparser/javaparser/pull/5199
- **Licença (SPDX):** Apache-2.0
- **Submodule:** `javaparser/` → fork `https://github.com/RailtonDantas/javaparser`, pinado em `4c1386d2bea6b6f32a650a8a861e942e81c9d8a3` (branch `refactor/eliminate-duplicated-code` publicada no fork).
- **Path no upstream:** `javaparser-symbol-solver-core/src/main/java/com/github/javaparser/symbolsolver/javaparsermodel/declarations/JavaParserClassDeclaration.java` (L323–L419), `JavaParserRecordDeclaration.java` (L386–L482) e o novo `JavaParserTypeDeclarationResolution.java`; copiados para `smells/duplicated-code/`
- **Nota local:** a refatoração moveu `solveType` e `getAncestors`/`isAncestor` para `JavaParserTypeDeclarationResolution`; 4 testes de caracterização foram adicionados; o CPD não reporta mais o bloco de 97 linhas (restam blocos de 61, 67 e 47 linhas fora do escopo).
- **Por que é code smell:** `JavaParserRecordDeclaration` documenta que sua implementação é em grande parte uma cópia da declaração de classe, e o PMD CPD encontrou 97 linhas correspondentes no código de produção. A duplicação abrange `solveType`, `getAncestors` e `isAncestor`. Mudanças na lógica comum de resolução de símbolos e ancestralidade precisam ser mantidas em sincronia, criando risco de divergência. Extrair apenas a lógica comum reduz essa duplicação sem apagar as diferenças específicas entre classes e records.
## retrofit (lysine-dev) — divergent change

- **Smell:** divergent-change
- **Issue:** #5
- **URL upstream:** https://github.com/lysine-dev/retrofit (canônica atual; o projeto nasceu como `square/retrofit`)
- **SHA original:** https://github.com/lysine-dev/retrofit/blob/3ab0f1a2c18666adb79ebf4eacef6fbc40d3e801/retrofit/src/main/java/retrofit2/RequestFactory.java#L65-L872
- **SHA refatorado:** _(a preencher pela issue de refactoring #6)_
- **PR upstream:** _(a preencher pela issue de refactoring #6)_
- **Licença (SPDX):** Apache-2.0
- **Submodule:** `retrofit/` (raiz do repo) → fork `https://github.com/matheusvir/retrofit` no padrão definido no CONTRIBUTING, pinado exatamente em `3ab0f1a2c18666adb79ebf4eacef6fbc40d3e801` (tag `3.0.0` do upstream).
- **Path no upstream:** `retrofit/src/main/java/retrofit2/RequestFactory.java` — classe inteira (872 linhas / 799 LOC), copiada fielmente para `smells/divergent-change/` junto com a `LICENSE.txt`.
- **Por que é code smell:** a classe muda por **dois motivos independentes**, comprovados em commits reais do upstream. **Eixo A — interpretar e validar anotações da interface** (vocabulário Retrofit): `parseAnnotations` (L66), `Builder.build` (L186), `parseMethodAnnotation` (L232), `parseHttpMethodAndPath` (L270), `parseHeaders` (L304), `parseParameter` (L329), `parseParameterAnnotation` (L368), `validateResolvableType` (L825), `validatePathName` (L832), `parsePathParameters` (L851) — gatilho real: commit `72a1feb9` "Support QUERY method". **Eixo B — montar a requisição em runtime** (vocabulário OkHttp): construtor `RequestFactory(Builder)` (L83) e `create(Object, Object[])` (L98–138) — gatilho real: commit `50472ac1` "Add Invocation.annotationUrl". Mudar uma regra de anotação não deveria tocar o código de montagem de request, e vice-versa; hoje os dois vocabulários compartilham um único arquivo. O smell não é o tamanho nem o padrão Builder — é a mistura de eixos num só arquivo.

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

