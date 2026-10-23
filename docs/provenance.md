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
