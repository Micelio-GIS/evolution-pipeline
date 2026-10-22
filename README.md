# evolution-pipeline
Pipeline propuesto para la optimización de los procesos de gestión predial del proyecto de la vía férrea, con un enfoque reutilizable y adaptable a otros proyectos de gestión predial.

## 1. Microservicio de Extracción de viviendas con teledetección:

Planteamos un microservicio qure reciba como insumo impágenes de la zona de interes, y por medio de un modelo entrenado reconozca las viviendas y retorne un shape de las mismas, el cual ademas de vectorizar la informacion permitira su discriminación y cuantificación, este insumo permitira concoer un valor aproximado de las unidades sociales, y su presicion dependera del insumo:

```mermaid
flowchart LR

    B1["Microservicio Censo y caracterización US"]
    B1--> B1c@{ shape: cloud, label: "Demo de 2Kms" }
    B1 --> B2["Intersección con cartografía base"]
    B2 --> B2Explain{"Insumo"} -- ortofotografía --> B3["Modelo 1 - high Accuracy"]
    B2 --> B2Explain{"Insumo"} -- Imagen Satelital --> B3Alt["Modelo 2 - Moderate Accuracy"] 
    B2 --> B2Explain{"Insumo"} -- Otro --> B3Alt2@{shape: cloud, label: Definir cual}
    
    B3Alt2 --> B4["smart digital factory: Vivivendas"]
    B3Alt --> B4["smart digital factory: Vivivendas"]
    B3 --> B4["smart digital factory: Vivivendas"]
    B4 --> B5@{ shape: display, label: "Censo preliminar: Shape de unidades sociales"}
```

## 2. Aplicación de Análisis masivo de titulos

Planteemos una aplicación en la nube que integre un pipeline automatizado para la consulta, descarga y organización de información en carpetas, así como la generación y gestión de documentos, optimizando el flujo de trabajo y centralizando los procesos en una única plataforma.


```mermaid
flowchart 
    C(["Aplicación de automatización"]) 
    C --> C1["Base inicial"] 
    C1 --> Cd{"Insumo"} -- Matricula Inmobiliaria --> cD0["Directo"] --> C2
    C1 --> Cd{"Insumo"} -- Cedula Catastral --> cD1["Validar con Gestor"] --> C2
    C1 --> Cd{"Insumo"} -- Sin Insumo --> cD1["Validar con Gestor"] --> C2

    C2["Scraping/Bot Microservice"] --> C21["Login al vur"] --> C22["Consulta"] --> C24["descarga Basico/Juridico"] --> C2
    C2 --> C3["Procesamiento estructurado de títulos"]
    C3 --> C4["Vertex Agent: generación automatizada de informe jurídico"] --> C5@{shape: display, label: "Carpetas en S3, nombradas y con los documentos"}

```
