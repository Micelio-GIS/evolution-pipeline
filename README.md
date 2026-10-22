# Evolution Gestion Predial pipelines
Pipeline propuesto para la optimización de los procesos de gestión predial del proyecto de la vía férrea, con un enfoque reutilizable y adaptable a otros proyectos de gestión predial.

## 1. Microservicio de Extracción de viviendas con teledetección:

Planteamos un microservicio qure reciba como insumo impágenes de la zona de interes, y por medio de un modelo entrenado reconozca las viviendas y retorne un shape de las mismas, el cual ademas de vectorizar la informacion permitira su discriminación y cuantificación, este insumo permitira concoer un valor aproximado de las unidades sociales, y su presicion dependera del insumo:

```mermaid
flowchart LR

    B1["Microservicio de censo y caracterización de unidades sociales"]
    B1 --> B1c@{ shape: cloud, label: "Demo de 2 km" }
    B1 --> B2["Intersección con cartografía base"]
    B2 --> B2Explain{"Insumo"} -- Ortofotografía --> B3["Modelo 1 - Alta precisión"]
    B2 --> B2Explain{"Insumo"} -- Imagen satelital --> B3Alt["Modelo 2 - Precisión moderada"]
    B2 --> B2Explain{"Insumo"} -- Otro --> B3Alt2@{shape: cloud, label: "Definir cuál"}

    B3Alt2 --> B4["Smart Digital Factory: viviendas"]
    B3Alt --> B4["Smart Digital Factory: viviendas"]
    B3 --> B4["Smart Digital Factory: viviendas"]
    B4 --> B5@{ shape: display, label: "Censo preliminar: shapefile de unidades sociales"}
```

## 2. Aplicación de Análisis masivo de titulos

Planteemos una aplicación en la nube que integre un pipeline automatizado para la consulta, descarga y organización de información en carpetas, así como la generación y gestión de documentos, optimizando el flujo de trabajo y centralizando los procesos en una única plataforma.


```mermaid
flowchart
    C(["Aplicación de automatización"]) 
    C --> C1["Base inicial"] 
    C1 --> Cd{"Insumo"} -- Matrícula inmobiliaria --> cD0["Directo"] --> C2
    C1 --> Cd{"Insumo"} -- Cédula catastral --> cD1["Validar con gestor"] --> C2
    C1 --> Cd{"Insumo"} -- Sin insumo --> cD1["Validar con gestor"] --> C2

    C2["Scraping / bot / microservicio"] --> C21["Inicio de sesión en el VUR"] --> C22["Consulta"] --> C24["Descarga de documentos básicos y jurídicos"] --> C2
    C2 --> C3["Procesamiento estructurado de títulos"]
    C3 --> C4["Agente de Vertex: generación automatizada del informe jurídico"] --> C5@{shape: display, label: "Carpeta por predio en S3 con los documentos"}

```
## 3. Estudios de valoración masiva y Avalúos de referencia

Para esta parte Plantamos varios flujos, sin embargo consideramos que debemos aclarar algunas presiciones del negocio, dado que 
```mermaid
flowchart

    D1["1. ZHF - Zonas Homogéneas Físicas"]

    D1 --> P0["Alistamiento: POT vigente y cartografía oficial, deslindes, ortofoto reciente, AHT, determinantes ambientales, OIC, estudio de zonas anterior si existe"]
    P0 --> P1["Mesa de trabajo con la autoridad municipal: interpretación de la norma de uso. Si el texto y el mapa del POT difieren, prevalece el texto"]
    P1 --> P2["Unificar CRS, validar topología y límites oficiales (municipal y perímetro urbano)"]

    P2 --> U["Variables urbanas"]
    U --> U1["Topografía: plano hasta 7%, inclinado 7 a 14%, empinado más de 14%. Incluir zonas inundables, taludes y caños. Fuente: curvas de nivel, DEM o LiDAR"]
    U --> U2["Influencia vial: pavimentada, sin pavimentar, peatonal, sin vías. Fuente: Planeación, Obras Públicas, POT, código de uso de vías férreas en catastro, ortofoto"]
    U --> U3["Servicios públicos: sin servicios, básicos incompletos, completos, completos más complementarios. Fuente: empresas de servicios, POT, validación de continuidad en terreno"]
    U --> U4["Norma de uso: áreas de actividad y tratamientos del POT más normas de superior jerarquía (CAR, POMCA, SINAP, BIC)"]
    U --> U5["Uso actual tipificado: residencial 1 a 6, comercial, industrial, institucional, lotes. Se define por frente de manzana con ortofoto y campo"]

    P2 --> R["Variables rurales"]
    R --> R1["AHT: aptitud agrológica. Clase y limitantes no subdividen. No aplica en parcelaciones, loteos ni edificaciones. Fuente: IGAC"]
    R --> R2["Norma de uso: POT rural y determinantes ambientales"]
    R --> R3["Uso actual: agrícola, pecuario, forestal, tierras improductivas, cuerpos de agua, condiciones especiales, edificaciones. Ortofoto y campo"]
    R --> R4["Influencia vial: buena, regular, mala, sin vías, fluvial. Vía buena por límite de predio, corredor del POT o buffer estadístico, documentado en memoria técnica"]
    R --> R5["Disponibilidad de aguas: abundantes, suficientes, escasas, sin agua. Drenajes y pendiente asociados a cada AHT, verificar permanencia en terreno"]

    U1 --> I0["Inventario de insumos: ¿existe en digital?"]
    U2 --> I0
    U3 --> I0
    U4 --> I0
    U5 --> I0
    R1 --> I0
    R2 --> I0
    R3 --> I0
    R4 --> I0
    R5 --> I0

    I0 --> I1{"¿Digital?"}
    I1 -- "Sí" --> I2["Homologar esquema y atributos (dominios LADM-COL)"]
    I1 -- "No" --> I3["Digitalizar: georreferenciar y vectorizar con control de calidad"]
    I3 --> I2

    I2 --> S0["Intersección espacial en la GDB"]
    S0 --> S1["Urbano: divisiones de manzana por lindero posterior del predio y por frente hasta el eje de la vía"]
    S0 --> S2["Rural: respetar límites de polígonos AHT"]
    S1 --> S3["Overlay de las variables y análisis de las combinaciones resultantes"]
    S2 --> S3
    S3 --> S4["Codificación: urbano 3 dígitos por bloques de uso (001 a 999), rural consecutivo desde 001, mejores condiciones agronómicas primero y edificaciones al final"]
    S4 --> S5["ZHF preliminares"]

    S5 --> V1["Validación en oficina: zonas como polígonos, coincidencia con cada variable, área de zonas vs área de terreno catastral dentro de tolerancia, tratamiento de predios partidos"]
    V1 --> V2["Recorrido y verificación en campo, ajuste de zonas y recolección de información económica"]
    V2 --> V3["Informe firmado de ejecutor y revisor, y mesa técnica de avalúos"]
    V3 --> V4["ZHF definitivas con leyenda y planos"]

    %% ===== 3. Zonas homogéneas - ZHG =====
    V4 --> D2["2. ZHG - Zonas Homogéneas Geoeconómicas"]
    D2 --> G1["Estudio del mercado inmobiliario directo e indirecto: OIC, avalúos administrativos, ofertas y transacciones, scraping inmobiliario de la zona con depuración por un avaluador. Guardar la ZHF de cada oferta"]
    D2 --> G2["Definición de muestra representativa por ZHF y predios especiales (atípicos, sin mercado)"]
    G2 --> G3["Condición: mínimo 10% de predios de cada zona física con reconocimiento en campo, o tipología completa de los puntos de investigación"]
    G1 --> G4["Valor unitario de terreno: valor total de la oferta menos valor de la construcción"]
    G3 --> G4
    G2 --> G5["Valor de construcción y anexos: tipologías con costos y depreciación (Resolución 620 de 2008)"]
    G2 --> G6["Valor integral PH por m2 de área privada, sin parqueaderos ni depósitos. Preguntar a Cami"]
    G4 --> G7["Modelo de valoración: regresión, tipologías, econométricos o geoestadística, elegir por mejor R2"]
    G5 --> G7
    G6 --> G7
    G7 --> G8["Valoración puntual de predios especiales"]
    G7 --> G9["Aprobación en mesa técnica de avalúos"]
    G8 --> G9

    %% ===== Cierre =====
    G9 --> E1["Valor de referencia"]
    E1 --> E2["Cruce de predios con las zonas homogéneas resultantes (incluir predios afectados por más de una zona)"]
    E2 --> E3["Productos finales: Valores de referencia"]
```
