``` js
Project AdlerCatalog {
  database_type: 'PostgreSQL'
}

// ==========================================
// COLLECTIONS
// ==========================================

Table collections {
  id integer [pk, increment]
  name varchar(255) [not null]
  year integer
  description text
  source text
}

Table collection_entries {
  id integer [pk, increment]
  collection_id integer [not null]
  number integer [not null]
  original_text text [not null]
  notes text

  indexes {
    (collection_id, number) [unique]
  }
}

Table collection_entry_authors {
  collection_entry_id integer [not null]
  author_id integer [not null]
  is_marked boolean [not null, default: false]

  indexes {
    (collection_entry_id, author_id) [pk]
  }
}

// ==========================================
// CATALOG
// ==========================================

Table authors {
  id integer [pk, increment]
  name varchar(255) [not null]
  birth_year integer
  death_year integer
  biography text
  nationality varchar(100)
}

Table works {
  id integer [pk, increment]
  title varchar(255) [not null]
  original_title varchar(255)
  description text
  content_type varchar(50)
  original_year integer
  original_language varchar(100)
}

Table work_authors {
  work_id integer [not null]
  author_id integer [not null]

  indexes {
    (work_id, author_id) [pk]
  }
}

// ==========================================
// WORK GROUPS
// ==========================================

Table work_groups {
  id integer [pk, increment]
  name varchar(255) [not null]
  description text
}

Table group_works {
  work_group_id integer [not null]
  work_id integer [not null]

  indexes {
    (work_group_id, work_id) [pk]
  }
}

// ==========================================
// RECOMMENDATIONS
// ==========================================

Table recommendations {
  id integer [pk, increment]
  collection_entry_id integer [not null]
  work_id integer
  work_group_id integer
  qualifier varchar(100)
  is_marked boolean [not null, default: false]

  Note: 'CHECK: exactly one of work_id or work_group_id must be non-null.'
}

// ==========================================
// RELATIONSHIPS
// ==========================================

// Collections
Ref: collection_entries.collection_id > collections.id

// Entry authors
Ref: collection_entry_authors.collection_entry_id > collection_entries.id
Ref: collection_entry_authors.author_id > authors.id

// Work authors
Ref: work_authors.work_id > works.id
Ref: work_authors.author_id > authors.id

// Work groups
Ref: group_works.work_group_id > work_groups.id
Ref: group_works.work_id > works.id

// Recommendations
Ref: recommendations.collection_entry_id > collection_entries.id
Ref: recommendations.work_id > works.id
Ref: recommendations.work_group_id > work_groups.id


```
