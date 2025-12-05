<div align="center">
  
<a href="https://www.canva.com/design/DAG6as54OsI/2qaZDzIoYMY8KwqDUjZKKg/view?utm_content=DAG6as54OsI&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=haa642ada9e#1" target="_blank">
<img src="https://github.com/ColectivoHagamos/EcoBalance360-Mapa-Nacional-de-Captura-y-Emisiones-de-Carbono/blob/main/Doc/EcoBalance360.png?r=duu" width="90%" align="center" alt="EcoBalance360"/>
</a>

</br>
</br>

**Mapa Nacional de Captura y Emisiones de Carbono**

Herramienta de analítica territorial para visualizar y simular el balance de carbono en los municipios de Colombia, identificando zonas emisoras y sumideros de CO₂.

[![Licencia MIT](https://img.shields.io/badge/Licencia-MIT-green.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![Next.js](https://img.shields.io/badge/Next.js-15-black.svg)](https://nextjs.org/)
[![Estado](https://img.shields.io/badge/Estado-Producción-brightgreen.svg)]()
[![Google Colab](https://img.shields.io/badge/Colab-Ejecutar%20Notebook-orange.svg)](https://colab.research.google.com/drive/1aRjH__szKk7sYtouJpD1VOlVqytemwbI?usp=sharing)
[![Stack Científico](https://img.shields.io/badge/Librerías-Pandas%20%7C%20NumPy%20%7C%20Matplotlib%20%7C%20Seaborn%20%7C%20Sklearn-purple.svg)]()



**[https://colectivohagamos.com](https://colectivohagamos.com)**

</div>

---

## Tabla de Contenidos

1. [Descripción del Proyecto](#descripción-del-proyecto)
2. [Contexto y Problema](#contexto-y-problema)
3. [Metodología](#metodología)
4. [Estructura del Repositorio](#estructura-del-repositorio)
5. [Datos y Fuentes](#datos-y-fuentes)
6. [Resultados Principales](#resultados-principales)
7. [Instalación y Uso](#instalación-y-uso)
8. [Arquitectura Técnica](#arquitectura-técnica)
9. [Limitaciones y Trabajo Futuro](#limitaciones-y-trabajo-futuro)
10. [Equipo](#equipo)
11. [Licencia](#licencia)
12. [Referencias](#referencias)

---

## Descripción del Proyecto

EcoBalance360 es una solución integral que aborda la brecha de información climática a nivel municipal en Colombia. El proyecto desarrolla un modelo de desagregación territorial que estima emisiones y captura de gases de efecto invernadero (GEI) para los 87 municipios del departamento de Santander, utilizando exclusivamente datos abiertos y la metodología del Panel Intergubernamental de Expertos sobre Cambio Climático (IPCC).

### Componentes Principales

| Componente | Descripción |
|------------|-------------|
| **Modelo de Estimación** | Cálculo de emisiones por 5 módulos IPCC usando factores Tier 1 |
| **Índice de Equilibrio Climático (IEC)** | Métrica normalizada 0-100 para clasificar municipios |
| **Clustering Municipal** | Agrupación de municipios por perfil de emisiones (K-Means) |
| **Aplicación Web Interactiva** | Mapa, simulador de escenarios y minijuegos educativos |
| **Integración con IA** | Análisis contextualizado por municipio mediante Google Gemini |

---

## Contexto y Problema

### La Brecha de Información

Colombia cuenta con un Inventario Nacional de Emisiones de GEI elaborado por el IDEAM, sin embargo, este inventario presenta datos agregados a nivel nacional y departamental. Los municipios, que son las unidades administrativas donde se implementan las políticas públicas, carecen de información específica sobre sus emisiones y captura de carbono.

Esta situación genera:

- Alcaldes sin datos para priorizar acciones climáticas
- Imposibilidad de establecer líneas base municipales
- Dificultad para medir el impacto de políticas locales
- Desconexión entre compromisos nacionales (NDC) y acción territorial

### Nuestra Propuesta

Desarrollamos un modelo que utiliza datos de actividad disponibles a nivel municipal (inventarios pecuarios, consumo energético, cobertura forestal, entre otros) y los transforma en estimaciones de emisiones mediante factores de emisión internacionalmente validados.

---

## Metodología

### Enfoque General

El modelo sigue la ecuación fundamental del IPCC:

```
Emisiones = Dato de Actividad × Factor de Emisión
```

Donde:
- **Dato de Actividad (DA):** Variable medible a nivel municipal (cabezas de ganado, hectáreas de bosque, kWh consumidos)
- **Factor de Emisión (FE):** Cantidad de GEI emitida por unidad de actividad (kg CO₂eq/unidad)

### Potencial de Calentamiento Global (GWP-100)

Para expresar diferentes gases en unidades comparables, se utilizan los valores GWP del IPCC AR5:

| Gas | Fórmula | GWP-100 |
|-----|---------|--------:|
| Dióxido de carbono | CO₂ | 1 |
| Metano | CH₄ | 28 |
| Óxido nitroso | N₂O | 265 |

### Módulos de Cálculo

#### Módulo 1: Energía

**Electricidad:**
```
E_elec = Consumo_kWh × 0.126 kg CO₂/kWh
```
Factor de emisión de la red eléctrica colombiana (UPME/FECOC 2019).

**Transporte:**
```
E_trans = Vehículos × 15,000 km/año × (1/40 gal/km) × 3.785 L/gal × 2.31 kg CO₂/L
```

#### Módulo 2: Procesos Industriales (IPPU)

```
E_IPPU = VAB × 0.35 ton CO₂/millón COP + Vol_minería × 0.005 ton CO₂/ton
```

Estimación basada en Valor Agregado Bruto como proxy de actividad industrial.

#### Módulo 3: Agricultura

**Fermentación Entérica:**

| Especie | Factor (kg CH₄/cabeza/año) |
|---------|---------------------------:|
| Bovinos | 56 |
| Búfalos | 55 |
| Equinos | 18 |
| Ovinos | 5 |
| Caprinos | 5 |
| Porcinos | 1 |

Fuente: IPCC 2006, Volumen 4, Capítulo 10, Tabla 10.10

**Gestión de Estiércol:**

| Especie | Factor (kg CH₄/cabeza/año) |
|---------|---------------------------:|
| Porcinos | 3.00 |
| Equinos | 1.64 |
| Bovinos | 1.00 |
| Caprinos | 0.17 |
| Ovinos | 0.15 |
| Aves | 0.02 |

Fuente: IPCC 2006, Volumen 4, Capítulo 10, Tabla 10.14

**Suelos Agrícolas:**
```
E_suelos = Área_cosechada × 1 kg N₂O-N/ha/año × (44/28) × 265
```

#### Módulo 4: Uso del Suelo (LULUCF)

**Captura por Bosques:**
```
Captura = Área_bosque × 4.5 ton CO₂/ha/año
```
Tasa de absorción para bosque tropical secundario.

**Emisiones por Deforestación:**
```
E_deforest = Área_perdida × 120 ton C/ha × (44/12)
```
Stock de carbono promedio en biomasa de bosque tropical.

#### Módulo 5: Residuos

**Residuos Sólidos:**
```
E_RSU = Toneladas_RSU × 0.5 ton CH₄/ton × 28
```

**Aguas Residuales:**
```
E_aguas = Consumo_agua × 0.8 × 0.025 kg CH₄/m³ × 28
```

### Balance de Carbono

```
Balance = Emisiones_Totales - Captura_Bosques
```

Donde:
```
Emisiones_Totales = E_Energía + E_IPPU + E_Agricultura + E_Residuos + E_Deforestación
```

- **Balance < 0:** Municipio sumidero (captura más de lo que emite)
- **Balance > 0:** Municipio emisor (emite más de lo que captura)

### Índice de Equilibrio Climático (IEC)

Para facilitar la comparación entre municipios, se calcula un índice normalizado:

1. **Normalización robusta:**
```
Balance_norm = (Balance - Mediana) / IQR
```

2. **Transformación sigmoide:**
```
IEC = sigmoid(Balance_norm, k=0.5) × 100
```

| Rango IEC | Clasificación |
|-----------|---------------|
| 0 - 40 | Sumidero |
| 40 - 60 | Equilibrio |
| 60 - 100 | Emisor |

### Clustering de Municipios

Se aplicó K-Means (k=4) sobre las siguientes variables estandarizadas:

- Proporción de emisiones por energía
- Proporción de emisiones por agricultura
- Proporción de emisiones por LULUCF
- Balance per cápita

**Perfiles resultantes:**

| Cluster | Perfil | Características |
|---------|--------|-----------------|
| 0 | Sumideros Forestales | Alta cobertura boscosa, balance negativo |
| 1 | Agrícola-Ganadero | Predominancia de emisiones pecuarias |
| 2 | Urbano-Industrial | Alta proporción de emisiones energéticas |
| 3 | Mixto-Transición | Características intermedias |

---

## Estructura del Repositorio

```
EcoBalance360-Mapa-Nacional-de-Captura-y-Emisiones-de-Carbono/
│
├── dataset/                          # Datos del proyecto
│   ├── consolidado_final_definitivo_santander_2019.xlsx
│   ├── ecobalance360_santander_resultados.csv
│   ├── ecobalance360_santander_resultados.xlsx
│   ├── ecobalance360_santander_resultados.json
│   └── santander_balance.geojson
│
├── doc/                              # Documentación técnica
│   ├── METODOLOGIA_IPCC.md
│   ├── DICCIONARIO_DATOS.md
│   └── REQUERIMIENTO_FRONTEND.md
│
├── resources/                        # Recursos visuales y de apoyo
│   ├── mapa_balance_carbono_santander.html
│   ├── distribucion_emisiones.png
│   └── balance_municipios.png
│
├── frontend/                         # Aplicación web (Next.js)
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── README.md
│
├── ecobalance360_modelo_ipcc.ipynb   # Notebook principal del modelo
├── ecobalance360_analisis_exploratorio.ipynb  # Análisis exploratorio
├── LICENSE                           # Licencia MIT
└── README.md                         # Este archivo
```

### Descripción de Carpetas

| Carpeta | Contenido |
|---------|-----------|
| `dataset/` | Datos de entrada consolidados y resultados del modelo en múltiples formatos |
| `doc/` | Documentación técnica, metodológica y especificaciones |
| `resources/` | Visualizaciones, mapas y recursos gráficos generados |
| `frontend/` | Código fuente de la aplicación web interactiva |

---

## Datos y Fuentes

### Fuentes de Datos Abiertos

| Fuente | Datos Utilizados | Año |
|--------|------------------|-----|
| DANE | Población municipal, Valor Agregado Bruto | 2019 |
| ICA | Inventario pecuario (bovinos, porcinos, aves, etc.) | 2019 |
| SUI | Consumo de agua y electricidad por sector | 2019 |
| Global Forest Watch | Cobertura de bosques, pérdida de cobertura arbórea | 2019 |
| RUNT | Vehículos registrados por municipio | 2019 |
| EVA (MADR) | Área sembrada y cosechada | 2019 |
| ANM | Volúmenes de explotación minera | 2019 |
| SSPD | Disposición final de residuos sólidos | 2019 |

### Fuentes de Factores de Emisión

| Fuente | Factores |
|--------|----------|
| IPCC 2006 Guidelines | Fermentación entérica, gestión estiércol, LULUCF, residuos |
| IPCC 2019 Refinement | Actualizaciones metodológicas |
| UPME/FECOC 2019 | Factor de emisión red eléctrica Colombia |
| IDEAM (2018) | Referencias del inventario nacional |

### Variables del Dataset

El dataset consolidado contiene 37 variables para 87 municipios:

**Identificación (4):** codDepartamento, departamento, codMunicipio, municipio

**Demográficas (3):** totalPoblacion, cabeceraMunicipal, centrosPobladosRuralDisperso

**Pecuarias (7):** totalBovinos, totalPorcinos, totalAvesOcupadasTraspatio, totalBufalos, totalEquinos, totalOvinos, totalCaprinos

**Energía (6):** consumoElectricoResidencial, consumoElectricoIndustrial, consumoElectricoComercial, consumoElectricoOficial, consumoElectricoOtros, cantidadVehiculosRegistrados

**Agua (5):** consumoAguaResidencial, consumoAguaIndustrial, consumoAguaComercial, consumoAguaOficial, consumoAguaOtros

**Industria (3):** valorAgregadoBruto, volumenesExplotacion, totalSubscriptoresGas

**Agricultura (3):** areaSembradaHectareas, areaCosechadaHectareas, produccion

**Bosques (3):** bosquesNaturales, perdidaCoberturaArborea, coberturaArboreaNoNatural

**Residuos (1):** disposicionFinalResiduosToneladas

**Hidrocarburos (2):** produccionKpc, produccionBls

---

## Resultados Principales

### Inventario Departamental de Santander (2019)

| Componente | Toneladas CO₂eq | Participación |
|------------|----------------:|---------------|
| Emisiones Energía | 335,048 | 4.2% |
| Emisiones IPPU | 1,902,355 | 23.9% |
| Emisiones Agricultura | 2,826,745 | 35.5% |
| Emisiones Residuos | 69,641 | 0.9% |
| Emisiones Deforestación | 2,824,645 | 35.5% |
| **Total Emisiones** | **7,958,433** | **100%** |
| Captura por Bosques | 7,728,228 | - |
| **Balance Neto** | **+230,205** | - |

**Conclusión:** Santander es un emisor neto marginal, con un balance cercano al equilibrio.

### Distribución Municipal

| Clasificación | Municipios | Porcentaje |
|---------------|------------|------------|
| Sumidero | 16 | 18.4% |
| Equilibrio | 53 | 60.9% |
| Emisor | 18 | 20.7% |

### Municipios Destacados

**Principales Emisores:**

| Posición | Municipio | Balance (ton CO₂eq) |
|----------|-----------|--------------------:|
| 1 | Barrancabermeja | +551,918 |
| 2 | Curití | +382,462 |
| 3 | Cimitarra | +370,443 |
| 4 | Puerto Wilches | +252,009 |
| 5 | Bolívar | +238,768 |

**Principales Sumideros:**

| Posición | Municipio | Balance (ton CO₂eq) |
|----------|-----------|--------------------:|
| 1 | Coromoro | -179,278 |
| 2 | Gámbita | -159,993 |
| 3 | Sucre | -157,239 |
| 4 | Onzaga | -156,737 |
| 5 | Mogotes | -133,274 |

---

## Instalación y Uso

### Requisitos Previos

- Python 3.10 o superior
- Node.js 18 o superior (para el frontend)
- Git

### Clonar el Repositorio

```bash
git clone https://github.com/ColectivoHagamos/EcoBalance360-Mapa-Nacional-de-Captura-y-Emisiones-de-Carbono.git
cd EcoBalance360-Mapa-Nacional-de-Captura-y-Emisiones-de-Carbono
```

### Ejecutar los Notebooks

1. Crear entorno virtual:
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows
```

2. Instalar dependencias:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl folium
```

3. Ejecutar Jupyter:
```bash
jupyter notebook
```

4. Abrir `ecobalance360_modelo_ipcc.ipynb`

### Ejecutar el Frontend

1. Navegar a la carpeta frontend:
```bash
cd frontend
```

2. Instalar dependencias:
```bash
npm install
```

3. Configurar variables de entorno:
```bash
cp .env.example .env.local
# Editar .env.local y agregar GEMINI_API_KEY
```

4. Ejecutar en desarrollo:
```bash
npm run dev
```

5. Abrir http://localhost:3000

### Despliegue

La aplicación está desplegada en Vercel:

<a href="https://colectivohagamos.com" target="_blank">https://colectivohagamos.com</a>

---

## Arquitectura Técnica

### Backend (Modelo de Datos)

```
Python 3.10+
├── pandas          # Manipulación de datos
├── numpy           # Cálculos numéricos
├── scikit-learn    # Clustering K-Means
├── matplotlib      # Visualizaciones
├── seaborn         # Visualizaciones estadísticas
├── folium          # Mapas interactivos
└── openpyxl        # Lectura/escritura Excel
```

### Frontend (Aplicación Web)

```
Next.js 15
├── React 18        # Interfaz de usuario
├── TypeScript      # Tipado estático
├── Tailwind CSS    # Estilos
├── Leaflet         # Mapas interactivos
├── Recharts        # Gráficos
├── shadcn/ui       # Componentes UI
└── Google Gemini   # Análisis con IA
```

### Flujo de Datos

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Datos Abiertos │────▶│  Modelo IPCC    │────▶│  JSON/GeoJSON   │
│  (Excel)        │     │  (Python)       │     │  (Resultados)   │
└─────────────────┘     └─────────────────┘     └────────┬────────┘
                                                         │
                                                         ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Usuario        │◀────│  App Web        │◀────│  API Gemini     │
│                 │     │  (Next.js)      │     │  (Análisis IA)  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

---

## Limitaciones y Trabajo Futuro

### Limitaciones Actuales

| Limitación | Descripción | Impacto |
|------------|-------------|---------|
| Datos de un año | Solo disponibles datos 2019 | No permite análisis temporal |
| Factores Tier 1 | Factores IPCC genéricos, no calibrados localmente | Mayor incertidumbre |
| Proxies para IPPU | VAB como proxy de actividad industrial | Estimación indirecta |
| Cobertura geográfica | Solo Santander en esta versión | Escalabilidad pendiente |

### Trabajo Futuro

1. **Expansión Nacional:** Replicar la metodología para los 1,122 municipios de Colombia
2. **Series Temporales:** Incorporar datos de múltiples años para análisis de tendencias
3. **Factores Tier 2:** Calibrar factores de emisión con datos locales del IDEAM
4. **Validación:** Contrastar resultados con mediciones directas donde estén disponibles
5. **Escenarios Climáticos:** Integrar proyecciones de cambio climático (RCP/SSP)

---

## Equipo

### Colectivo HAGAMOS 🤗💪🏾🌱🤓

Colectivo que promueve la actualización de la identidad, la educación para la paz y el respeto por la diversidad y el ambiente.

Este proyecto fue desarrollado en el marco del reto **EcoBalance360** del concurso **Datos al Ecosistema 2025** organizado por el Ministerio de Tecnologías de la Información y las Comunicaciones de Colombia (MinTIC).

---

## Licencia

Este proyecto está licenciado bajo la Licencia MIT. Consulte el archivo [LICENSE](LICENSE) para más detalles.

```
MIT License

Copyright (c) 2025 Colectivo HAGAMOS

Se concede permiso, de forma gratuita, a cualquier persona que obtenga una copia
de este software y los archivos de documentación asociados (el "Software"), para
utilizar el Software sin restricción, incluyendo sin limitación los derechos de
usar, copiar, modificar, fusionar, publicar, distribuir, sublicenciar y/o vender
copias del Software, y permitir a las personas a quienes se les proporcione el
Software hacer lo mismo, sujeto a las siguientes condiciones:

El aviso de copyright anterior y este aviso de permiso se incluirán en todas las
copias o partes sustanciales del Software.
```

---

## Referencias

### Documentos Técnicos

- IPCC (2006). *2006 IPCC Guidelines for National Greenhouse Gas Inventories*. Hayama, Japan: Institute for Global Environmental Strategies.

- IPCC (2019). *2019 Refinement to the 2006 IPCC Guidelines for National Greenhouse Gas Inventories*. Geneva, Switzerland: IPCC.

- IDEAM, PNUD, MADS, DNP, CANCILLERÍA (2018). *Segundo Informe Bienal de Actualización de Colombia ante la CMNUCC*. Bogotá, Colombia.

- UPME (2019). *Factor de Emisión de CO2 del Sistema Interconectado Nacional*. Bogotá, Colombia: Unidad de Planeación Minero Energética.

### Marco Normativo

- Ley 2169 de 2021 - Ley de Acción Climática de Colombia
- Decreto 926 de 2017 - Sistema Nacional de Cambio Climático (SISCLIMA)
- Resolución 1447 de 2018 - Sistema de Monitoreo, Reporte y Verificación (MRV)

### Datos Abiertos

- <a href="https://www.datos.gov.co" target="_blank">Datos Abiertos Colombia</a>
- <a href="https://www.globalforestwatch.org" target="_blank">Global Forest Watch</a>
- <a href="https://sui.superservicios.gov.co" target="_blank">Sistema Único de Información de Servicios Públicos - SUI</a>

---

<p align="center">
  Desarrollado con 🌱 para el futuro sostenible de Colombia
</p>

<p align="center">
  <a href="https://colectivohagamos.com" target="_blank">Ver Aplicación</a>
  <a href="https://www.instagram.com/hagamoses" target="_blank">Colectivo HAGAMOS</a>
</p>
