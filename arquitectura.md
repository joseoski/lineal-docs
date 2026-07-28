# Arquitectura

## Capas de la aplicación

```mermaid
flowchart LR
  subgraph presentation["Presentación"]
    screens["screens/"]
    widgets["widgets/"]
  end

  subgraph domain["Dominio"]
    models["models/"]
    utils["utils/"]
  end

  subgraph infrastructure["Infraestructura"]
    services["services/"]
    platforms["android / ios / desktop"]
  end

  screens --> models
  screens --> services
  widgets --> services
  services --> models
  services --> platforms
  utils --> models
```

## Stack tecnológico

```mermaid
mindmap
  root((Lineal))
    Flutter
      Material Design
      Provider
    Mapas
      google_maps_flutter
      flutter_map
      latlong2
    GPS
      geolocator
      permission_handler
    Persistencia
      sqflite
      sqflite_common_ffi
      shared_preferences
      path_provider
```

## Estructura de `lib/`

```
lib/
├── models/
│   ├── field.dart
│   ├── poi.dart
│   ├── task.dart
│   └── historical_map.dart
├── services/
│   ├── database_service.dart
│   ├── gps_service.dart
│   └── app_state.dart
├── screens/
│   └── field_screen.dart
├── widgets/
│   ├── mode_menu.dart
│   └── dialogs/
│       ├── rtk_dialog.dart
│       ├── settings_dialog.dart
│       ├── cloud_sync_dialog.dart
│       ├── equipos_dialog.dart
│       └── backup_dialog.dart
├── utils/
│   └── validators.dart
├── main.dart          # Entrada actual (MapScreen monolítico)
├── main_new.dart      # Entrada refactorizada (en progreso)
└── field_screen.dart  # Re-exports de compatibilidad
```

## Diagrama de componentes

```mermaid
graph TB
  main["main.dart<br/>MapScreen"]
  mainNew["main_new.dart"]
  fieldScreen["FieldScreen"]
  modeMenu["ModeMenu"]

  appState["AppState"]
  gps["GpsService"]
  db["DatabaseService"]

  field["Field"]
  poi["POI"]
  task["Task"]
  hist["HistoricalMap"]

  dialogs["Dialogs<br/>RTK / Settings / Sync / Backup / Equipos"]

  main --> modeMenu
  main --> fieldScreen
  mainNew --> appState
  mainNew --> db
  fieldScreen --> field
  fieldScreen --> poi
  fieldScreen --> task
  fieldScreen --> hist
  modeMenu --> dialogs
  appState --> field
  db --> field
  db --> poi
  db --> task
  db --> hist
  gps --> field
```

## Responsabilidades de servicios

| Servicio | Responsabilidad |
|----------|-----------------|
| `DatabaseService` | CRUD, migraciones, índices, FFI desktop |
| `GpsService` | Stream de posición, distancias, áreas, bearing, desviación |
| `AppState` | Campo actual, RTK, unidades, sync (ChangeNotifier) |

## Estado de la refactorización

```mermaid
flowchart LR
  A["main.dart monolítico"] -->|en progreso| B["Servicios + modelos extraídos"]
  B -->|pendiente| C["MapScreen migrado a main_new.dart"]
  C -->|futuro| D["Modos en archivos separados"]

  style A fill:#f9d,stroke:#333
  style B fill:#ff9,stroke:#333
  style C fill:#9cf,stroke:#333
  style D fill:#9f9,stroke:#333
```

- **Hecho:** modelos, servicios, widgets, validadores, `main_new.dart` (esqueleto)
- **Pendiente:** migrar lógica de `MapScreen` desde `main.dart` (~3350 líneas)
