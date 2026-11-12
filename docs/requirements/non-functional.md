# Adler Catalog — Requisitos Não Funcionais

> **Status:** princípios arquiteturais confirmados + metas operacionais propostas  
> **Versão:** 0.1.0 · **Data:** 2026-11-11

## Convenções

- **Confirmado:** decisão de engenharia expressamente assumida no planejamento.
- **Proposto:** meta ou prática sugerida, ainda sujeita a aprovação e medição.
- Não confundir uma **preferência arquitetural** com um **SLA de produção**.

## 1. Arquitetura e manutenibilidade

| ID      | Requisito                                                                                                | Estado                              | Evidência esperada                                   |
| ------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------- | ---------------------------------------------------- |
| RNF-001 | Domínio e aplicação não devem depender de frameworks web, HTTP, ORM ou banco de dados.                   | Confirmado                          | Grafo de imports e testes do core sem infraestrutura |
| RNF-002 | Dependências devem apontar para dentro: adapters dependem dos contratos internos, não o inverso.         | Confirmado                          | Estrutura de módulos e revisão de imports            |
| RNF-003 | Casos de uso devem depender de portas/interfaces quando precisarem de persistência ou serviços externos. | Confirmado                          | Injeção de dependência e adapters substituíveis      |
| RNF-004 | Regras e invariantes de negócio não devem depender exclusivamente de validações da interface.            | Confirmado                          | Testes unitários de regras e constraints de banco    |
| RNF-005 | A modelagem deve priorizar clareza e simplicidade, evitando padrões sem justificativa concreta.          | Confirmado                          | ADRs e revisões de arquitetura                       |
| RNF-006 | Decisões relevantes devem documentar contexto, alternativas e consequências.                             | Confirmado                          | ADRs em `docs/architecture/decisions/`               |
| RNF-007 | O código deve manter responsabilidades claras, nomes coerentes com o domínio e módulos coesos.           | Confirmado (diretriz de Clean Code) | Revisão de código e testes                           |

## 2. Testabilidade e qualidade

| ID      | Requisito                                                                                               | Estado                                           | Evidência esperada                 |
| ------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------ | ---------------------------------- |
| RNF-008 | Casos de uso e regras de domínio devem poder ser testados sem PostgreSQL, rede ou servidor HTTP.        | Confirmado                                       | Testes com repositórios em memória |
| RNF-009 | O núcleo da v0.1.0 deve ter testes unitários para caminhos de sucesso, falhas e invariantes relevantes. | Confirmado                                       | Suíte executável em CI/local       |
| RNF-010 | Adapters de persistência devem ter testes de integração quando forem introduzidos na v0.2.0.            | Confirmado (estratégia acordada)                 | Testes contra banco de teste       |
| RNF-011 | Executar testes e verificação de tipos automaticamente em Pull Requests.                                | Proposto                                         | GitHub Actions ou CI equivalente   |
| RNF-012 | Utilizar TypeScript em modo estrito no núcleo.                                                          | Proposto, consistente com o projeto CRM anterior | `tsconfig` e `tsc --noEmit`        |
| RNF-013 | Usar Vitest para os testes do núcleo.                                                                   | Proposto, consistente com o projeto CRM anterior | Configuração e testes executáveis  |

> **Nota:** não foi definida meta numérica de cobertura de testes. Cobertura pode ser medida, mas não deve substituir análise dos cenários importantes.

## 3. Fidelidade, integridade e confiabilidade

| ID      | Requisito                                                                                             | Estado                 | Evidência esperada                              |
| ------- | ----------------------------------------------------------------------------------------------------- | ---------------------- | ----------------------------------------------- |
| RNF-014 | Preservar a integridade da numeração, texto original, associações e marcadores bibliográficos.        | Confirmado             | Constraints e testes de importação              |
| RNF-015 | Aplicar unicidade de entrada por coleção e exclusividade de destino de recomendação no banco.         | Confirmado             | Migrations e testes de constraint               |
| RNF-016 | O processo de importação deve relatar falhas de validação de forma compreensível.                     | Proposto               | Logs/relatórios de importação                   |
| RNF-017 | Evitar importações parcialmente aplicadas quando a operação exigir consistência transacional.         | Proposto               | Transações e testes de falha                    |
| RNF-018 | Manter procedência identificável dos dados bibliográficos e distinguir transcrição de enriquecimento. | Confirmado (princípio) | Referências de coleção e documentação de origem |

## 4. Usabilidade e acessibilidade

| ID | Requisito | Estado | Evidência esperada |
| --- | --- | --- | --- |
| RNF-019 | A navegação pública deve priorizar legibilidade da lista histórica e clareza de seus agrupamentos. | Confirmado | Revisão de interface |
| RNF-020 | O estado de preservação da ordem e o efeito dos controles de ordenação devem ser perceptíveis. | Confirmado | Testes de interação |
| RNF-021 | Os accordions e controles devem ser utilizáveis por teclado e expor estados semânticos a tecnologias assistivas. | Proposto | Testes de acessibilidade |
| RNF-022 | Adotar WCAG 2.2 nível AA como referência de acessibilidade para a interface pública. | Proposto, meta a aprovar | Auditoria automatizada e manual |
| RNF-023 | A interface deve funcionar em telas móveis e desktop. | Proposto | Testes responsivos |

## 5. Desempenho e escalabilidade

| ID | Requisito | Estado | Evidência esperada |
| --- | --- | --- | --- |
| RNF-024 | Busca por autor/obra e ordenação alfabética por autor devem permanecer utilizáveis com o conjunto completo das listas importadas. | Confirmado (objetivo), sem métrica aprovada | Testes com dados representativos |
| RNF-025 | Evitar consultas N+1 ao carregar autores e recomendações das entradas. | Proposto | Inspeção de queries/testes de integração |
| RNF-026 | Definir orçamento de desempenho após medir protótipo e volume real de dados. | Pendente | Benchmark documentado |

Não foram acordados valores como “responder em menos de 200 ms”, número de usuários simultâneos, disponibilidade de 99,9% ou estratégia obrigatória de cache. **Não inventar SLAs** nesta fase.

## 6. Segurança e privacidade

| ID | Requisito | Estado | Evidência esperada |
| --- | --- | --- | --- |
| RNF-027 | A V1 não requer autenticação e não deve expor operações públicas de escrita do catálogo. | Confirmado | Revisão de rotas e permissões |
| RNF-028 | Dados externos/importados devem ser tratados como não confiáveis e validados antes do uso. | Proposto | Testes de validação |
| RNF-029 | Texto bibliográfico apresentado na interface não deve permitir execução de scripts injetados (XSS). | Proposto | Testes e renderização segura |
| RNF-030 | Credenciais e segredos de infraestrutura não devem ser versionados no repositório. | Proposto | Configuração de ambiente e revisão |
| RNF-031 | Autenticação, autorização e privacidade de dados de leitura devem ser especificadas antes das versões v2+. | Confirmado como necessidade futura | Requisitos específicos das releases |

## 7. Entrega, observabilidade e documentação

| ID | Requisito | Estado | Evidência esperada |
| --- | --- | --- | --- |
| RNF-032 | O projeto deve permitir executar testes e desenvolvimento local com instruções reproduzíveis. | Proposto | README e scripts de projeto |
| RNF-033 | O desenvolvimento deve ser dividido em releases incrementais documentadas. | Confirmado | Tags, releases, milestones e changelog |
| RNF-034 | Requisitos devem ser rastreáveis até Issues e testes relevantes. | Confirmado (objetivo de processo) | IDs em Issues, PRs e descrições de teste |
| RNF-035 | Importações e erros relevantes devem produzir informações suficientes para diagnóstico. | Proposto | Logs e mensagens de erro |

## 8. Critérios de saída sugeridos por marco

### v0.1.0 — Core

- Código de domínio e aplicação executa testes sem servidor e sem banco.
- Casos de uso necessários à primeira versão pública possuem testes de sucesso e erro.
- Contratos de repositório e invariantes estão documentados.
- Ausência de imports de infraestrutura no núcleo foi verificada.

### v0.2.0 — Infraestrutura

- Migrations aplicáveis em ambiente de teste.
- Adapters implementam contratos do núcleo.
- Importação inicial de 1972 validada contra constraints e casos de integração.

### v1.0.0 — Catálogo público

- Lista histórica expansível, pesquisa e ordenação funcionam conforme RF/RN.
- Números e marcadores originais são preservados.
- Interface acessível e responsiva conforme metas aprovadas.
- Documentação e instruções de execução atualizadas.

## 9. Qualidade e honestidade dos metadados históricos

| ID | Requisito | Estado | Evidência esperada |
| --- | --- | --- | --- |
| RNF-036 | Não exibir datas desconhecidas como se fossem conhecidas; ausência de ano deve ser representada como dado ausente. | Confirmado | Testes de apresentação com `NULL` |
| RNF-037 | Não apresentar anos aproximados como exatos quando a fonte ou a curadoria indicar incerteza. | Confirmado | Revisão de conteúdo e testes com exemplos históricos |
| RNF-038 | Documentar a limitação dos anos inteiros opcionais: não codificam precisão, intervalo nem proveniência. | Confirmado (trade-off consciente) | Documentação e futura decisão arquitetural se o modelo evoluir |

**Trade-off:** a V1 privilegia um esquema bibliográfico enxuto, aceitando que certas datas serão omitidas ou precisarão de contexto editorial adicional. Essa decisão evita introduzir prematuramente uma estrutura complexa de datas históricas, mas impede alegações de precisão automática a partir de `birth_year`, `death_year` ou `original_year`. A futura representação de estimativas e intervalos será avaliada quando houver casos reais e fontes verificadas.

## 10. Decisões ainda necessárias

- Framework web, ORM e estratégia de deploy.
- Métricas reais de performance e volume de dados.
- Ferramentas de CI, lint e formatação.
- Nível formal de acessibilidade exigido.
- Estratégia de importação, transações, logs e recuperação de falhas.
