---
layout: default
title: Flujos de uso
---

# Flujos de uso

## Flujo típico de jornada agrícola

```mermaid
flowchart TD
  A[Configuración inicial] --> B[Crear perímetro conduciendo]
  B --> C[Marcar POIs / obstáculos]
  C --> D{¿RTK disponible?}
  D -->|Sí| E[Configurar NTRIP]
  D -->|No| F[Usar GPS estándar]
  E --> G[Preparar trabajo]
  F --> G
  G --> H[Elegir guianza A-B o curvas]
  H --> I[Configurar Section Control]
  I --> J[Iniciar registro de tarea]
  J --> K[Trabajar con alertas de desviación]
  K --> L[Revisar métricas]
  L --> M[Finalizar y guardar]
  M --> N[Opcional: backup / sync]
```

## Flujo de guianza con desviación

```mermaid
sequenceDiagram
  participant Op as Operador
  participant UI as MapScreen
  participant GPS as GpsService

  Op->>UI: Marcar punto A (destino)
  UI->>GPS: calcular bearing deseado
  Op->>UI: Iniciar tracking
  loop cada actualización GPS
    GPS-->>UI: posición + heading
    UI->>UI: distancia a línea A-B
    alt desviación > umbral (10 m)
      UI-->>Op: alerta visual / snackbar
    else dentro del umbral
      UI-->>Op: rumbo OK
    end
  end
```

## Flujo Section Control

```mermaid
flowchart LR
  CFG[Configurar N secciones<br/>y ancho implemento] --> IND[Indicadores en barra]
  IND --> AUTO{¿Tarea activa?}
  AUTO -->|Sí| ADJ[Ajuste automático<br/>por solapamiento]
  AUTO -->|No| MAN[Toggle manual]
  ADJ --> MET[Métricas de ahorro %]
  MAN --> MET
```

## Flujo backup / restauración

```mermaid
flowchart TB
  subgraph Backup
    B1[Hacer Backup] --> B2[Copia local de fields.db]
    B3[Exportar] --> B4[JSON estructurado]
  end

  subgraph Restaurar
    R1[Restaurar Backup] --> R2[Elegir archivo .db]
    R2 --> R3[Reemplazar DB]
    R4[Importar] --> R5[Merge/carga JSON]
  end
```

## Gestión avanzada

```mermaid
flowchart TB
  subgraph Análisis
    H[Mapas históricos] --> C[Comparar por campo/año]
    M[Métricas de tarea] --> A[HA/h, km/h, giros]
    S[Section Control] --> I[Ahorro de insumos]
  end

  subgraph Flota
    E[Equipos conectados] --> V[Estados: activo / trabajando]
  end
```

## Plataformas y mapas

```mermaid
flowchart LR
  App[Lineal] --> Plat{Plataforma}
  Plat -->|Android| GM[Google Maps]
  Plat -->|Desktop / cross| FM[Flutter Map + OSM]
  Plat -->|Android| GPSR[GPS real]
  Plat -->|Desktop| GPSS[GPS simulado / disponible]
```
