# Adler Catalog — Requisitos Funcionais

> **Status:** rascunho consolidado para revisão  
> **Versão:** 0.1.0 · **Data:** 2026-11-11  
> **Escopo principal:** v1.0.0 (lista de 1972); itens futuros identificados por release.

## Convenções

- **Confirmado:** comportamento acordado nas conversas.
- **Proposto:** detalhamento útil, ainda não validado.
- **RF:** requisito funcional; **RN:** regra de negócio relacionada.
- A numeração é estável para referenciar Issues, casos de uso e testes; não corresponde necessariamente a uma classe ou endpoint.

## 1. Coleção e navegação — v1.0.0

| ID | Requisito | Estado |
| --- | --- | --- |
| RF-001 | Exibir publicamente a coleção de 1972, sem exigir autenticação. | Confirmado |
| RF-002 | Exibir as entradas na ordem da numeração original ao abrir o catálogo. | Confirmado |
| RF-003 | Mostrar número e identificação/nome de cada entrada em uma lista principal expansível. | Confirmado |
| RF-004 | Permitir expandir e recolher uma entrada para consultar seu conteúdo. | Confirmado |
| RF-005 | Exibir todos os autores associados a cada entrada, inclusive quando houver múltiplos autores. | Confirmado |
| RF-006 | Exibir dados biográficos disponíveis dos autores na expansão da entrada. | Confirmado |
| RF-007 | Exibir obras individuais, conjuntos de obras e recomendações associados à entrada. | Confirmado |
| RF-008 | Mostrar qualificadores de recomendação (ex.: “especialmente”) e marcadores preservados da fonte. | Confirmado |
| RF-009 | Permitir consultar o texto original da entrada, preservado no catálogo. | Confirmado |

## 2. Pesquisa e ordenação — v1.0.0

| ID     | Requisito                                                                                                                            | Estado                       |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------- |
| RF-010 | Oferecer uma barra de pesquisa com seleção obrigatória do escopo: **Autor** ou **Obra**.                                             | Confirmado                   |
| RF-011 | Pesquisar entradas pelo nome de autor quando **Autor** estiver selecionado; pelo título de obra quando **Obra** estiver selecionado. | Confirmado                   |
| RF-012 | Disponibilizar a opção “Sempre manter a lista original”, **ativada por padrão**.                                                     | Confirmado                   |
| RF-013 | Com essa opção ativa, manter a sequência histórica entre as entradas exibidas, inclusive após uma pesquisa.                          | Confirmado                   |
| RF-014 | Com a opção desativada, aplicar a ordenação alfabética por autor selecionada sem modificar o número histórico das entradas.          | Confirmado                   |
| RF-015 | Oferecer **somente** ordenação por autor, crescente (A–Z) e decrescente (Z–A).                                                       | Confirmado                   |
| RF-016 | Ordenação por título de obra.                                                                                                        | **Retirado do escopo da V1** |
| RF-017 | Ordenação cronológica por ano de nascimento do autor.                                                                                | **Retirado do escopo da V1** |
| RF-018 | Ordenação cronológica por ano da obra.                                                                                               | **Retirado do escopo da V1** |
| RF-019 | Ao pesquisar com preservação ativa, manter a ordem original **entre as entradas encontradas**.                                       | Confirmado                   |
| RF-020 | Filtros adicionais.                                                                                                                  | **Retirado do escopo da V1** |
| RF-021 | Desabilitar ou explicar os controles de ordenação enquanto a preservação estiver ativa.                                              | Proposto (UX)                |
| RF-022 | Restaurar a sequência original ao reativar a opção de preservação.                                                                   | Confirmado                   |

> **Nota de rastreabilidade:** RF-016, RF-017, RF-018 e RF-020 foram mantidos com seus IDs originais para registrar a redução de escopo. **Não devem gerar tarefas de implementação na V1.**

> A ordenação reorganiza **entradas**, não desmembra uma entrada com vários autores. O critério de escolha do autor de referência em entradas com coautoria ainda requer uma decisão de modelagem.

## 3. Importação e manutenção técnica — v0.2.0

| ID | Requisito | Estado |
| --- | --- | --- |
| RF-023 | Importar dados estruturados da lista de 1972 sem depender de painel administrativo. | Confirmado |
| RF-024 | Importar coleções, entradas, autores, obras, grupos e associações de acordo com o modelo bibliográfico. | Confirmado |
| RF-025 | Validar integridade das associações e das recomendações antes ou durante a persistência. | Confirmado (resultado), mecanismo a definir |
| RF-026 | Permitir execução controlada do processo de importação por desenvolvedor/operador. | Confirmado |
| RF-027 | Evitar duplicação acidental de entradas com o mesmo número dentro da mesma coleção. | Confirmado |
| RF-028 | Definir política de reexecução da importação (idempotência, atualização ou falha explícita). | Pendente |

## 4. Entregas futuras (não fazem parte da v1.0.0)

| ID | Release | Requisito planejado |
| --- | --- | --- |
| RF-029 | v1.1.0 | Disponibilizar a lista de 1940 como coleção separada. |
| RF-030 | v1.2.0 | Oferecer página individual para cada autor, com seus dados e obras. |
| RF-031 | v2.0.0 | Disponibilizar painel administrativo protegido para gerenciamento bibliográfico. |
| RF-032 | v2.1.0 | Permitir cadastro, autenticação e dashboard de biblioteca pessoal. |
| RF-033 | v2.1.0 | Permitir marcar obras como “quero ler”, “lendo” e “lido”. |
| RF-034 | v3.0.0 | Exibir edições, traduções, editoras, ISBN quando disponível e formato físico/digital. |
| RF-035 | v3.0.0 | Exibir links de acesso legal e informação contextualizada sobre domínio público. |
| RF-036 | v4.0.0 | Oferecer recomendações baseadas em dados de leitura, conforme desenho futuro. |
| RF-037 | v5.0.0 | Oferecer comentários, perguntas, respostas encadeadas, curtidas, denúncias e moderação. |

## 5. Exemplos de critérios de aceitação

**RF-002 / RF-012 / RF-013 — Preservação da lista**

- **Dado** que o catálogo foi aberto pela primeira vez, **quando** a lista for exibida, **então** as entradas seguem a numeração histórica.
- **Dado** que “Sempre manter a lista original” está ativo, **quando** o visitante pesquisar, **então** os resultados encontrados continuam na sequência histórica.
- **Dado** que o visitante reativa a preservação, **então** a ordem original é restaurada.

**RF-010 / RF-011 — Pesquisa por escopo**

- **Dado** que o visitante escolheu **Autor**, **quando** pesquisar, **então** a busca considera nomes de autores.
- **Dado** que escolheu **Obra**, **quando** pesquisar, **então** a busca considera títulos de obras.

**RF-014 / RF-015 — Ordenação alternativa**

- **Dado** que a preservação foi desativada, **quando** o visitante selecionar A–Z ou Z–A, **então** as entradas são ordenadas por autor.
- **Dado** que a entrada `#80` passa a aparecer antes da `#10`, **então** os números históricos continuam `#80` e `#10`.

**RF-005 — Múltiplos autores**

- **Dado** que uma entrada associa vários autores, **quando** for expandida, **então** todos os autores associados são apresentados.

## 6. Questões em aberto para próxima revisão

1. Como escolher e preservar o autor de referência quando uma entrada tem vários autores?
2. A pesquisa por obra encontra títulos contidos em grupos? Como exibir correspondências?
3. Como apresentar biografias e datas aproximadas sem sugerir precisão inexistente?
4. A lista será carregada integralmente ou progressivamente, sem mudar a experiência de página única?
5. Quais validações mínimas serão feitas durante a importação manual da lista?
