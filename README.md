# Code smells em Projetos Java Open Source

Atividade 4 da disciplina de Projetos de Software (UFCG): diagnóstico e eliminação de code smells em três projetos Java, com as refatorações submetidas como pull requests aos repositórios upstream.

## Equipe

| Integrante | GitHub |
|---|---|
| Matheus Virgolino | [@matheusvir](https://github.com/matheusvir) |
| João Pereira | [@jpereira-Dev](https://github.com/jpereira-Dev) |
| Lucas Gabriel | [@Lucaslg7](https://github.com/Lucaslg7) |
| Manoel Netto | [@ManoelNetto26](https://github.com/ManoelNetto26) |
| Railton Dantas | [@RailtonDantas](https://github.com/RailtonDantas) |

## Code smells e projetos

Os três projetos estão na raiz como submódulos Git, pinados no commit refatorado. O SHA original e os permalinks de cada trecho estão em [`docs/provenance.md`](docs/provenance.md), e o fluxo de forks e PRs está descrito no [`CONTRIBUTING.md`](CONTRIBUTING.md).

| Projeto | Code smell | Por quê |
|---|---|---|
| [JavaParser](https://github.com/javaparser/javaparser) | Duplicated Code | `JavaParserClassDeclaration` e `JavaParserRecordDeclaration` repetem entre si `solveType`, `getAncestors` e `isAncestor`; o CPD (PMD, minimum-tokens 100) apontava um bloco de 97 linhas idênticas |
| [Retrofit (lysine-dev)](https://github.com/lysine-dev/retrofit) | Divergent Change | `RequestFactory.java` (872 linhas) reunia dois eixos de mudança: interpretar e validar anotações, e montar a requisição em runtime |
| [Apache Commons CLI](https://github.com/apache/commons-cli) | Comments | `Parser.processOption()` e o construtor de `Parser` somavam seis comentários redundantes: cinco narrando o que o código já dizia em `processOption()`, e um `// empty` no construtor |

## Refatorações

Cada refatoração foi enviada como pull request ao repositório upstream do projeto.

### Duplicated Code no JavaParser

Extract Class: a lógica compartilhada saiu das duas declarações e foi para a classe auxiliar package-private `JavaParserTypeDeclarationResolution`, que recebe de cada declaração apenas o que ela tem de próprio. A API pública não mudou. O CPD deixa de reportar o bloco de 97 linhas e a suíte completa passa sem falhas (2169 testes, 27 ignorados).

PR: [javaparser#5199](https://github.com/javaparser/javaparser/pull/5199).

### Divergent Change no Retrofit

Extract Class: a classe interna `RequestFactory.Builder` virou a classe `RequestFactoryBuilder`, deixando as regras de anotações em um arquivo e a montagem das requisições em outro. A fachada `parseAnnotations`, o suporte a `@QUERY` e os metadados de `Invocation` foram preservados, e um teste de caracterização cobre a combinação de path, query e headers.

PR: [lysine-dev/retrofit#4796](https://github.com/lysine-dev/retrofit/pull/4796).

### Comments no Apache Commons CLI

Rename Variable e remoção dos comentários: `hasOption` virou `optionIsRecognized`, `opt` virou `optionToProcess`, e os cinco comentários de `processOption()` mais o `// empty` do construtor foram removidos, sem tocar em Javadocs, assinaturas ou mensagens de exceção. O clone da opção ganhou o teste `testParsedArgumentIsStoredOnOptionClone`, e a suíte completa passa sem falhas (998 testes, 61 ignorados).

PR: [apache/commons-cli#445](https://github.com/apache/commons-cli/pull/445).

## Apresentação

Slides: [Google Slides](https://docs.google.com/presentation/d/14438cgcdd60tslNo_pcUN85JMqtrIbTABDVbRfcx00A/edit?usp=sharing)

Vídeo: [Google Drive](https://drive.google.com/file/d/1wxRzoikULQp2_UADEjg7rjzo-VzgBU6x/view?usp=sharing)

---

*Este repositório é parte da Atividade 4 da disciplina de Projetos de Software do curso de Ciência da Computação da Universidade Federal de Campina Grande (UFCG), ministrada pelos professores Fabio Morais e Giovanni Farias.*
