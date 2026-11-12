```mermaid
erDiagram
    COLLECTIONS ||--o{ COLLECTION_ENTRIES : contains
    COLLECTION_ENTRIES ||--o{ COLLECTION_ENTRY_AUTHORS : has
    AUTHORS ||--o{ COLLECTION_ENTRY_AUTHORS : appears_in

    COLLECTION_ENTRIES ||--o{ RECOMMENDATIONS : contains
    WORKS |o--o{ RECOMMENDATIONS : referenced_by
    WORK_GROUPS |o--o{ RECOMMENDATIONS : referenced_by

    AUTHORS ||--o{ WORK_AUTHORS : has
    WORKS ||--o{ WORK_AUTHORS : has

    WORK_GROUPS ||--o{ GROUP_WORKS : contains
    WORKS ||--o{ GROUP_WORKS : belongs_to

```