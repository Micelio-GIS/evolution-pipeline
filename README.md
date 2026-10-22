# evolution-pipeline
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
