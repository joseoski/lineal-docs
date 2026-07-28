---
layout: default
title: Modos de operación
---

# Modos de operación

El menú lateral (`ModeMenu`) organiza los modos en cinco categorías.

## Mapa de modos

```mermaid
mindmap
  root((Menú Lineal))
    Medición
      Normal A-B
      Dibujo
      Descuento
      Distancia
      Marcar punto
      Crear perímetro
    Navegación
      Rumbo
      Guianza en curvas
    Gestión
      Gestionar POI
      Registrar tarea
      Mapas históricos
    Configuración
      Section Control
      Configurar RTK
      Sync nube
      Equipos
      Unidades
    Base de datos
      Hacer backup
      Restaurar backup
```

## Relación modo → acción

```mermaid
flowchart TB
  subgraph Medición
    N[normal] --> AB[Guianza A-B / destino]
    D[dibujo] --> POL[Polígono + área]
    DE[descuento] --> DESC[Zonas de descuento]
    DI[distancia] --> MED[Segmentos medidos]
    M[marcar] --> PT[Punto simple]
    P[perimetro] --> REC[Grabación GPS al conducir]
  end

  subgraph Navegación
    R[rumbo] --> HDG[Heading en vivo]
    C[curvas] --> CUR[Trayectoria curva]
  end

  subgraph Gestión
    POI[poi] --> POIM[CRUD POI]
    T[tarea] --> LOG[Logging de trabajo]
    H[historico] --> MAP[Capas históricas]
  end
```

## Guianza A-B (modo normal)

```mermaid
stateDiagram-v2
  [*] --> Idle
  Idle --> PuntoA: tocar mapa (destino)
  PuntoA --> Tracking: iniciar tracking
  Tracking --> Alerta: desviación > umbral
  Alerta --> Tracking: corregir rumbo
  Tracking --> Idle: detener
```

## Creación de perímetro

```mermaid
sequenceDiagram
  participant U as Usuario
  participant App as Lineal
  participant GPS as GpsService
  participant DB as DatabaseService

  U->>App: Crear Perímetro
  U->>App: ▶️ iniciar
  loop cada ~5 m
    GPS-->>App: posición
    App->>App: agregar punto al path
  end
  U->>App: ⏹️ detener
  U->>App: 💾 guardar
  App->>App: calcular área (HA)
  App->>DB: insert/update Field
  DB-->>App: OK
```

## Registro de tarea

```mermaid
stateDiagram-v2
  [*] --> Configurar: tipo / implemento / producto
  Configurar --> Activa: ▶️
  Activa --> Activa: GPS path + métricas
  Activa --> Finalizada: ⏹️
  Finalizada --> [*]: guardar en tasks
```

## Códigos internos (`onModeSelected`)

| Código | Modo |
|--------|------|
| `normal` | Modo Normal |
| `dibujo` | Modo Dibujo |
| `descuento` | Modo Descuento |
| `distancia` | Modo Distancia |
| `marcar` | Marcar Punto |
| `perimetro` | Crear Perímetro |
| `rumbo` | Modo Rumbo |
| `curvas` | Guianza en Curvas |
| `poi` | Gestionar POI |
| `tarea` | Registrar Tarea |
| `historico` | Mapas Históricos |
| `section` | Section Control |
| `rtk` | Configurar RTK |
| `cloud` | Sincronización Nube |
| `equipos` | Equipos Conectados |
| `settings` | Configuración |
| `backup` | Hacer Backup |
| `restore` | Restaurar Backup |
