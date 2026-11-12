# Adler Catalog — Regras de Negócio

> **Status:** decisões confirmadas + propostas identificadas  
> **Versão:** 0.1.0 · **Data:** 2026-11-11

## 1. Vocabulário do domínio

- **Collection (coleção):** lista bibliográfica de uma fonte/edição, como 1972 ou 1940.
- **CollectionEntry (entrada):** item numerado da coleção; preserva o texto da fonte. **Não é sinônimo de autor.**
- **Author (autor):** pessoa associada a uma ou mais entradas e/ou obras.
- **Work (obra):** obra intelectual, independente de edição, tradução ou exemplar físico.
- **WorkGroup (grupo de obras):** agrupamento conceitual, por exemplo diálogos de um autor.
- **Recommendation (recomendação):** indicação de uma obra **ou** de um grupo, associada a uma entrada, com possível qualificador e marcador.
- **Edição:** publicação/tradução concreta de uma obra; fora do modelo inicial, prevista para evolução futura.

## 2. Integridade da fonte histórica

| ID     | Regra                                                                                                    | Estado                        |
| ------ | -------------------------------------------------------------------------------------------------------- | ----------------------------- |
| RN-001 | Cada entrada pertence a exatamente uma coleção.                                                          | Confirmado                    |
| RN-002 | O par `(collection_id, number)` identifica unicamente uma entrada dentro da coleção.                     | Confirmado                    |
| RN-003 | O texto original da entrada deve ser preservado separadamente dos dados normalizados/enriquecidos.       | Confirmado                    |
| RN-004 | O número original de uma entrada não muda em função de pesquisa, filtros ou ordenação.                   | Confirmado                    |
| RN-005 | Uma entrada pode estar associada a vários autores; não assumir relação 1:1.                              | Confirmado                    |
| RN-006 | As listas de 1940 e 1972 são coleções distintas; um mesmo autor ou obra pode ser reutilizado entre elas. | Confirmado (modelo e roadmap) |
| RN-007 | A associação entrada–autor não pode ser duplicada para o mesmo par de IDs.                               | Confirmado (PK composta)      |

## 3. Obras, grupos e recomendações

| ID     | Regra                                                                                                                                                                                                                   | Estado                                                                   |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| RN-008 | Uma obra pode ter zero, um ou vários autores identificados.                                                                                                                                                             | Confirmado                                                               |
| RN-009 | Uma obra e um autor não podem ter a mesma associação duplicada em `work_authors`.                                                                                                                                       | Confirmado (PK composta)                                                 |
| RN-010 | Uma obra pode integrar um ou mais grupos; a mesma associação grupo–obra não pode ser duplicada.                                                                                                                         | Confirmado (PK composta)                                                 |
| RN-011 | Cada recomendação referencia **exatamente um** destino: `work_id` **ou** `work_group_id`, nunca ambos e nunca nenhum.                                                                                                   | Confirmado                                                               |
| RN-012 | Uma recomendação pertence a uma entrada de coleção.                                                                                                                                                                     | Confirmado                                                               |
| RN-013 | Uma indicação geral de grupo e indicações específicas de obras desse grupo podem coexistir na mesma entrada.                                                                                                            | Confirmado                                                               |
| RN-014 | Qualificadores, como “especialmente”, pertencem à recomendação e não devem ser confundidos com o título da obra.                                                                                                        | Confirmado                                                               |
| RN-015 | `collection_entry_authors.is_marked` representa o marcador de autor (`**`) da fonte; `recommendations.is_marked` representa o marcador de recomendação (`*`).                                                           | Confirmado                                                               |
| RN-016 | A interpretação dos marcadores deve permanecer ligada à fonte: `**` indica *Gateway to the Great Books* para o autor e `*` indica *Great Books of the Western World* para a recomendação, no contexto da lista de 1972. | Confirmado para a fonte discutida; verificar convenções ao importar 1940 |
| RN-017 | O registro `works` representa a obra intelectual, não uma edição, tradução ou exemplar.                                                                                                                                 | Confirmado                                                               |

### Invariante de persistência RN-011

A restrição deverá existir na camada de domínio/aplicação apropriada **e** no banco de dados. Para PostgreSQL, uma expressão possível é:

```sql
CHECK (num_nonnulls(work_id, work_group_id) = 1)
```

A escolha de onde construir/validar a recomendação no domínio será definida na modelagem dos agregados; **tabela de junção não implica automaticamente entidade ou agregado de domínio**.

## 4. Pesquisa e ordenação

| ID | Regra | Estado |
| --- | --- | --- |
| RN-018 | A ordem padrão é a sequência histórica da coleção, por `number`. | Confirmado |
| RN-019 | “Sempre manter a lista original” começa ativado; enquanto ativo, a sequência das entradas não muda. | Confirmado |
| RN-020 | A pesquisa restringe entradas visíveis, mas, com preservação ativa, as entradas encontradas continuam em ordem histórica. | Confirmado |
| RN-021 | Com preservação desativada, ordenar **entradas** por autor A–Z ou Z–A, sem alterar seus números originais. | Confirmado |
| RN-022 | Ordenações cronológicas de autores e obras. | **Retirado da V1** |
| RN-023 | Menor ano de nascimento em entradas com múltiplos autores como chave cronológica. | **Retirado da V1** |
| RN-024 | Menor ano de obra como chave cronológica. | **Retirado da V1** |
| RN-025 | Datas desconhecidas devem permanecer ausentes, não ser estimadas; caso ordenações cronológicas sejam criadas no futuro, registros sem data devem ir ao fim. | Confirmado para ausência; ordenação futura |
| RN-026 | Em empate de ordenação alfabética por autor, usar o número histórico crescente para obter ordem estável. | Proposto |
| RN-027 | Para entradas com múltiplos autores, usar um autor de referência sem dividir a entrada. **Proposta:** primeiro autor da fonte; é necessário definir como preservar essa ordem. | Pendente |
| RN-032 | A pesquisa da V1 exige seleção entre **Autor** e **Obra**; não há filtros adicionais nem ordenação por título. | Confirmado |

### Trade-off: incerteza histórica dos anos

Os campos `authors.birth_year`, `authors.death_year` e `works.original_year` são **inteiros opcionais**. Esse modelo é simples, mas **não codifica se o ano é exato, aproximado ou parte de um intervalo**, nem a fonte da estimativa. Um número como `-427` sozinho não autoriza apresentar “427 a.C.” como data certa.

**Decisões confirmadas:**

- **Data desconhecida:** deixar o campo sem valor (`NULL`) e não mostrar uma data na interface; não preencher com `0`, data atual ou estimativa inventada.
- **Data aproximada:** sinalizar claramente a aproximação (por exemplo, `c. 427 a.C.`) **quando houver evidência de que é aproximada**.
- **Datas expressas como intervalos:** são períodos possíveis, como “entre 380 e 370 a.C.”; a V1 não exige armazená-los como datas estruturadas.
- **Não haverá ordenação cronológica na V1**, portanto não é necessário resolver comparações entre datas incertas.

**Limitação assumida:** com o esquema atual, um `integer` não guarda a marca de aproximação. Antes de exibir um ano aproximado, devemos ter uma representação confiável dessa incerteza (por exemplo, metadados futuros ou texto contextual revisado). **Não inferir certeza a partir de um inteiro.** Preferimos omitir uma data a afirmar precisão que a fonte não sustenta.

## 5. Regras de importação

| ID | Regra | Estado |
| --- | --- | --- |
| RN-028 | A importação não pode violar chaves, associações únicas ou RN-011. | Confirmado |
| RN-029 | O processo de importação não deve alterar silenciosamente o texto histórico de uma entrada já revisada. | Proposto |
| RN-030 | Reexecuções da importação devem ter política explícita de duplicatas e atualização. | Pendente |
| RN-031 | Informações editoriais adicionadas posteriormente devem ser distinguíveis da transcrição da fonte. | Confirmado (princípio do produto) |

## 6. Domínio público e edições — visão futura, não regras implementadas na V1

- A situação de domínio público de uma **obra original** não determina automaticamente a situação jurídica de uma **tradução ou edição**.
- A situação jurídica depende de contexto, como jurisdição e direitos aplicáveis; não representar isso apenas como um booleano global sem justificativa.
- Uma obra em domínio público pode **não ter** um arquivo digital legalmente disponível no catálogo.
- Um link de acesso deve corresponder a uma fonte legalmente disponibilizada; não hospedar ou vincular material sem verificar permissões.

## 7. Limites entre modelo de dados e modelo de domínio

O modelo relacional inicial tem **nove tabelas**: `collections`, `collection_entries`, `collection_entry_authors`, `authors`, `works`, `work_authors`, `work_groups`, `group_works` e `recommendations`.

Essas tabelas são um ponto de partida para a persistência, **não** uma lista obrigatória de nove classes de domínio. Agregados, entidades, objetos de valor e serviços serão escolhidos a partir de comportamentos e invariantes reais. Regras críticas devem ser testadas independentemente de banco e reforçadas por constraints quando pertinente.

## 8. Questões pendentes

1. Como identificar o autor de referência para ordenar entradas com coautoria, e como preservar a ordem original dos autores?
2. Como representar a incerteza e a proveniência das datas sem perder a simplicidade do esquema inicial?
3. Como tratar obras presentes apenas dentro de um grupo na pesquisa por título?
4. Como identificar e corrigir erros de transcrição mantendo histórico e proveniência?
5. Qual a política de reexecução da importação manual?
