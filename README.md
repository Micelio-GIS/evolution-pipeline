# evolution-pipeline
Pipeline propuesto para la optimización de los procesos de gestión predial del proyecto de la vía férrea, con un enfoque reutilizable y adaptable a otros proyectos de gestión predial.


```mermaid
flowchart LR



    B1["Microservicio Censo y caracterización US"]
    B1--> B1c@{ shape: cloud, label: "Demo de 2Kms" }
    B1 --> B2["Intersección con cartografía base"]
    B2 --> B2Explain{"Insumo"} -- ortofotografía --> B3["Modelo 1 - high Acurasity"]
    B2 --> B2Explain{"Insumo"} -- Imagen Satelital --> B3Alt["Modelo 2 - Moderate acuracity"] 
    B2 --> B2Explain{"Insumo"} -- Otro --> B3Alt2@{shape: cloud, label: Definir cual}
    
    B3Alt2 --> B4["smart digital factory: Vivivendas"]
    B3Alt --> B4["smart digital factory: Vivivendas"]
    B3 --> B4["smart digital factory: Vivivendas"]
    B4 --> B5@{ shape: display, label: "Censo preliminar: estimación de áreas y unidades sociales"}

   

```
