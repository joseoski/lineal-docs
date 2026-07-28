# Diagramas

Índice de diagramas Mermaid de la documentación Lineal. Se renderizan automáticamente en GitHub.

| Diagrama | Ubicación |
|----------|-----------|
| Vista rápida de arquitectura | [index.md](../index.md) |
| Capas y componentes | [arquitectura.md](../arquitectura.md) |
| Stack (mindmap) | [arquitectura.md](../arquitectura.md) |
| Estado del refactor | [arquitectura.md](../arquitectura.md) |
| ER / esquema SQLite | [base-de-datos.md](../base-de-datos.md) |
| CASCADE y ciclo de vida DB | [base-de-datos.md](../base-de-datos.md) |
| Backup / sync | [base-de-datos.md](../base-de-datos.md) |
| Mapa de modos | [modos.md](../modos.md) |
| Estados guianza A-B | [modos.md](../modos.md) |
| Secuencia perímetro | [modos.md](../modos.md) |
| Estados de tarea | [modos.md](../modos.md) |
| Jornada agrícola | [flujos.md](../flujos.md) |
| Guianza + desviación | [flujos.md](../flujos.md) |
| Section Control | [flujos.md](../flujos.md) |
| Plataformas y mapas | [flujos.md](../flujos.md) |

## Diagrama consolidado del sistema

```mermaid
flowchart TB
  subgraph Usuario
    Op[Operador agrícola]
  end

  subgraph App["Lineal App"]
    UI[MapScreen + ModeMenu]
    GPS[GpsService]
    ST[AppState]
    DB[DatabaseService]
  end

  subgraph Storage
    SQL[(SQLite)]
    SP[(SharedPreferences)]
  end

  subgraph Hardware
    GNSS[GPS / RTK NTRIP]
    MAP[Motor de mapas]
  end

  Op --> UI
  UI --> GPS
  UI --> ST
  UI --> DB
  UI --> MAP
  GPS --> GNSS
  ST --> SP
  DB --> SQL
```

## Diagrama de despliegue

```mermaid
flowchart TB
  subgraph Dispositivo
    APK[APK Android / Desktop]
    LocalDB[(fields.db local)]
    Backup[Backups locales]
  end

  subgraph Opcional
    NTRIP[Servidor NTRIP]
    Cloud[Sync nube - preparado]
  end

  APK --> LocalDB
  APK --> Backup
  APK -.->|RTK| NTRIP
  APK -.->|export/import| Cloud
```
