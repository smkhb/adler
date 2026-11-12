# Adler Catalog — Product Vision

> **Status:** visão consolidada, com decisões pendentes explicitadas  
> **Versão do documento:** 0.1.0  
> **Última revisão:** 2026-11-11  
> **Idioma da documentação:** português (Brasil)

## 1. Propósito

Construir um catálogo bibliográfico público que permita explorar as listas de leitura recomendadas nas edições de **1940 e 1972 de *How to Read a Book***, de Mortimer J. Adler (e, na edição revista de 1972, Charles Van Doren), respeitando a organização e o contexto das fontes históricas.

A plataforma deve tornar mais simples **descobrir, pesquisar, compreender e relacionar** autores, obras, conjuntos de obras e recomendações. Sua evolução poderá incluir edições, traduções, formas legais de acesso, bibliotecas pessoais e recursos comunitários.

O objetivo **não** é substituir a fonte histórica, reescrevê-la ou apresentar recomendações editoriais contemporâneas como se fossem de Adler.

## 2. Princípios do produto

1. **Fidelidade à fonte:** preservar texto original, numeração, marcadores e qualificadores; distinguir transcrição de dados enriquecidos.
2. **Descoberta sem apagar contexto:** pesquisa e ordenação não alteram a identidade ou a sequência histórica da coleção.
3. **Obra não é edição:** uma obra intelectual pode ter muitas traduções e publicações; recomendações históricas referem-se a obras ou grupos, não automaticamente a uma edição comercial.
4. **Acesso legal e transparente:** domínio público, direitos de tradução/edição e disponibilidade de um arquivo digital são questões distintas; informações futuras deverão explicitar contexto e fontes.
5. **Evolução incremental:** entregar um catálogo utilizável antes de autenticação, administração, comunidade e recomendações personalizadas.
6. **Simplicidade deliberada:** não antecipar funcionalidades, tabelas ou padrões que ainda não resolvem problemas concretos.

## 3. Público e experiência inicial

O público inicial são leitores interessados nas recomendações de Adler. O acesso à V1 será público e sem conta.

A interface principal será uma **página de lista numerada expansível** (*accordion/dropdown*): o visitante vê o número e o nome/identificação da entrada e pode expandi-la para consultar autores, informações biográficas disponíveis, obras, grupos e recomendações. Entradas podem conter **mais de um autor**; portanto, a unidade histórica da página é a **entrada da coleção**, não um registro individual de autor.

A ordem padrão é a sequência da fonte. A opção **“Sempre manter a lista original”** começa ativada. Enquanto ativa, ordenações alternativas não reorganizam as entradas; a pesquisa pode restringir os resultados, mas os resultados visíveis permanecem na sequência original. Ao desativá-la, o visitante pode ordenar por nome de autor (A–Z ou Z–A). A busca exige a escolha entre **Autor** e **Obra**. Não haverá filtros nem ordenação cronológica na v1.0.0.

## 4. Escopo e entregas planejadas

As versões abaixo são **marcos de produto planejados**, não promessas de datas nem uma interpretação rígida de compatibilidade SemVer.

| Release | Marco | Entrega planejada |
| --- | --- | --- |
| `v0.1.0` | Domain & Application Core | Domínio, regras, casos de uso, contratos de repositório e testes isolados; sem framework, HTTP ou banco |
| `v0.2.0` | Infrastructure | PostgreSQL, migrations, adapters, testes de integração e importação inicial da lista de 1972 |
| `v0.3.0` | Web Application | Interface pública integrada ao núcleo e infraestrutura |
| `v1.0.0` | Public Catalog | Catálogo público da lista de 1972, navegação expansível, busca por autor/obra e ordenação alfabética por autor |
| `v1.1.0` | 1940 Collection | Inclusão da lista de 1940, preservando sua identidade como coleção distinta |
| `v1.2.0` | Author Profiles | Páginas individuais de autores e suas obras |
| `v2.0.0` | Administration | Painel administrativo e gerenciamento do catálogo |
| `v2.1.0` | Personal Library | Cadastro, autenticação, dashboard pessoal e estados quero ler/lendo/lido |
| `v3.0.0` | Editions & Availability | Edições físicas/digitais, traduções, editoras, ISBN, links legais e contexto de direitos |
| `v4.0.0` | Reading Recommendations | Sugestões de leitura baseadas em dados de leitura, conforme permissões e disponibilidade |
| `v5.0.0` | Community | Comentários, perguntas, respostas encadeadas, curtidas, denúncias e moderação |

## 5. Fora do escopo da v1.0.0

- Autenticação, contas e biblioteca pessoal.
- Painel administrativo e edição de dados por visitantes.
- Perfis individuais de autores (previstos para v1.2; informações resumidas podem aparecer no accordion da v1.0).
- Edições, traduções específicas, ISBN, links de leitura e verificação de domínio público.
- Comentários, moderação e recomendações personalizadas.
- Hospedagem de livros digitais.

## 6. Filosofia de engenharia e portfólio

O desenvolvimento seguirá a estratégia **core-first**: requisitos e regras documentados → modelagem de domínio e aplicação → testes unitários isolados → infraestrutura → interface. O núcleo deve ser independente de tecnologias externas serão escolhidas e integradas como adapters em fases posteriores.

Aplicar **DDD** e **Clean Architecture** onde produzam limites, invariantes e testes mais claros. Decisões relevantes e seus *trade-offs* devem ser registrados em ADRs; comportamento importante deve ter testes rastreáveis a requisitos.

## 7. Fonte de verdade e documentação

- `docs/product-vision.md`: propósito, princípios e roadmap.
- `docs/requirements/functional.md`: comportamentos esperados.
- `docs/requirements/non-functional.md`: atributos de qualidade e restrições de engenharia.
- `docs/requirements/business-rules.md`: invariantes e semântica do catálogo.
- `docs/architecture/`: decisões arquiteturais, modelo de domínio e persistência, quando forem produzidos.
- GitHub Issues/Projects/Milestones: execução e progresso; não substituem documentação versionada.

## 8. Trade-off: precisão de datas históricas

O modelo inicial usa anos inteiros opcionais (`birth_year`, `death_year`, `original_year`). Isso simplifica o cadastro, mas **não representa a certeza histórica, um intervalo, a fonte da estimativa ou o calendário utilizado**. Por exemplo, armazenar `-427` não distingue um nascimento comprovado de um nascimento estimado em torno de 427 a.C.

**Decisão para V1:** não inventar datas; quando desconhecidas, deixá-las vazias e omiti-las na apresentação. Quando aproximadas, sinalizar explicitamente a aproximação **somente quando essa informação for conhecida e puder ser representada fielmente**. Se o esquema atual não conseguir guardar essa distinção, não apresentar o ano como exato: usar uma apresentação contextualizada baseada em dados verificados ou adiar o campo até ajustar o modelo. Não criar ordenação cronológica na V1. Intervalos históricos e proveniência detalhada de datas ficam para revisão posterior.

**Consequência aceita:** algumas biografias e obras terão menos metadados visíveis, em troca de não transmitir falsa precisão. Esta é uma limitação consciente do modelo, não uma licença para preencher datas por suposição.

## 9. Decisões pendentes

- Para entradas com vários autores, como determinar o **autor principal para ordenação**? Proposta: primeiro autor da fonte; a ordem dos autores ainda precisa ser preservada explicitamente no modelo ou na importação.
- Comportamento detalhado da pesquisa (acentos, correspondência parcial, obras dentro de grupos).
- Estratégia de carregamento da lista extensa e acessibilidade do accordion.
- Estratégia de curadoria/importação e validação mínima das transcrições originais.
- Como armazenar e apresentar o grau de certeza de datas aproximadas, sem falsa precisão.
- Tecnologias concretas de infraestrutura e frontend, a decidir nas respectivas fases.
