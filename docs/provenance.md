## Apache Commons CLI

- **Smell:** Comments
- **Issue:** #1
- **URL upstream:** [https://github.com/apache/commons-cli](https://github.com/apache/commons-cli)
- **SHA original (trechos analisados):**
  - [Construtor `Parser()`, linhas 35–51](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L35-L51)
  - [Getters `getOptions()` e `getRequiredOptions()`, linhas 76–92](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L76-L92)
  - [Método `processOption()`, linhas 247–263](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L247-L263)
  - [Setter `setOptions()`, linhas 291–299](https://github.com/apache/commons-cli/blob/03ff1171f35a45721131c4c7ef65973a796f8d6a/src/main/java/org/apache/commons/cli/Parser.java#L291-L299)
- **SHA refatorado:** A preencher após a refatoração, na issue correspondente.
- **PR upstream:** A preencher após a abertura do PR de refatoração.
- **Licença (SPDX):** Apache-2.0
- **Path no upstream:** `src/main/java/org/apache/commons/cli/Parser.java` — construtor `Parser()` (linhas 42–47), getters `getOptions()` e `getRequiredOptions()` (linhas 69–85), método `processOption()` (linhas 234–250) e setter `setOptions()` (linhas 291–299); arquivo copiado para `smells/comments/Parser.java`.

- **Por que é code smell:** O arquivo contém comentários redundantes em diferentes métodos. No construtor, o Javadoc genérico e o comentário `// empty` descrevem uma operação sem comportamento adicional; nos getters e no setter, os Javadocs repetem os nomes dos métodos e os significados dos parâmetros ou retornos, sem fornecer informações relevantes adicionais. Em `processOption()`, cinco comentários apenas narram as condições e instruções que aparecem logo abaixo deles. Esses comentários não esclarecem decisões não óbvias nem regras de negócio, tornando-se candidatos à remoção para reduzir ruído e evitar divergências entre comentários e implementação.
