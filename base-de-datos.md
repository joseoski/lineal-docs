# Base de datos

SQLite local (`fields.db`, versión de esquema **2**), con soporte desktop vía `sqflite_common_ffi`.

## Modelo entidad-relación

```mermaid
erDiagram
  fields ||--o{ pois : "tiene"
  fields ||--o{ tasks : "tiene"
  fields ||--o{ historical_maps : "tiene"

  fields {
    INTEGER id PK
    TEXT name
    TEXT date
    TEXT type
    REAL hectares
    TEXT perimeter
    REAL total_area
    REAL worked_area
  }

  pois {
    INTEGER id PK
    INTEGER field_id FK
    TEXT name
    TEXT type
    REAL latitude
    REAL longitude
    TEXT description
    TEXT created_at
  }

  tasks {
    INTEGER id PK
    INTEGER field_id FK
    TEXT type
    TEXT start_time
    TEXT end_time
    TEXT implement
    TEXT product
    REAL quantity
    REAL area_worked
    REAL speed
    TEXT path
    TEXT status
  }

  historical_maps {
    INTEGER id PK
    INTEGER field_id FK
    TEXT type
    TEXT year
    TEXT data
    TEXT created_at
  }
```

## Tablas

### `fields`
Campos agrícolas con perímetro (JSON de coordenadas), área total y área trabajada.

### `pois`
Puntos de interés por campo. Tipos típicos: obstáculo, muestreo, referencia.

### `tasks`
Registro de trabajos (arado, siembra, pulverización, cosecha) con path GPS y métricas.

### `historical_maps`
Capas históricas (rendimiento, prescripción, humedad) por año.

## Índices

| Índice | Tabla | Columna |
|--------|-------|---------|
| `idx_fields_name` | fields | name |
| `idx_pois_field_id` | pois | field_id |
| `idx_tasks_field_id` | tasks | field_id |
| `idx_historical_maps_field_id` | historical_maps | field_id |

## Integridad referencial

```mermaid
flowchart LR
  F[fields] -->|ON DELETE CASCADE| P[pois]
  F -->|ON DELETE CASCADE| T[tasks]
  F -->|ON DELETE CASCADE| H[historical_maps]
```

Al eliminar un campo se eliminan POIs, tareas y mapas históricos asociados.

## Ciclo de vida

```mermaid
sequenceDiagram
  participant App
  participant DB as DatabaseService
  participant SQLite

  App->>DB: database (getter)
  alt primera vez
    DB->>SQLite: openDatabase v2
    SQLite->>SQLite: onCreate / onUpgrade
  else ya abierta
    DB-->>App: instancia cacheada
  end
  App->>DB: CRUD Field / POI / Task / Map
  DB->>SQLite: SQL
  SQLite-->>DB: filas
  DB-->>App: modelos Dart
```

## Backup y sync

```mermaid
flowchart TB
  DB[(fields.db)] -->|backup local| BAK[Archivo .db]
  DB -->|export JSON| JSON[Campos + POI + Tareas + Mapas]
  JSON -->|import| DB
  BAK -->|restaurar| DB
```
