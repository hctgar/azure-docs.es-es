---
title: 'Entrenamiento del recomendador SVD: referencia para los módulos'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el módulo de entrenamiento del recomendador SVD en Azure Machine Learning para entrenar a un recomendador bayesiano con el algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dc78322b09548e4e9c2f666e0c8315ce688d6541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428414"
---
# <a name="train-svd-recommender"></a>Entrenamiento del recomendador SVD

En este artículo se describe cómo usar el módulo Entrenar recomendador de SVD del diseñador de Azure Machine Learning (versión preliminar). Use este módulo para entrenar un modelo de recomendación basado en el algoritmo de descomposición en valores singulares (SVD).  

El módulo Entrenar recomendador de SVD lee un conjunto de datos de tripletas usuario-elemento-clasificación. Devuelve un recomendador SVD entrenado. Después, puede usar el modelo entrenado para predecir las clasificaciones o generar recomendaciones con el uso del módulo [ de puntuación del recomendador SVD](score-svd-recommender.md).  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Más información sobre los modelos de recomendación y el recomendador SVD  

El objetivo principal de un sistema de recomendación es recomendar uno o más *elementos* a *usuarios* del sistema. Algunos ejemplos de elementos pueden ser una película, un restaurante, un libro o una canción. Un usuario podría ser una persona, un grupo de personas u otra entidad con preferencias de elementos.  

Existen dos enfoques principales para los sistemas de recomendación: 

+ Un enfoque **basado en el contenido** hace uso de las características tanto para usuarios como para elementos. Los usuarios se pueden describir mediante propiedades como la edad y el género. Los usuarios se pueden describir mediante propiedades como el creador y el fabricante. En los sitios de encuentros sociales se pueden encontrar ejemplos típicos de sistemas de recomendación basados en contenido. 
+ Los **filtros de colaboración** usan solo identificadores de los usuarios y los elementos. Obtienen información implícita sobre estas entidades de una matriz (dispersa) de clasificaciones dadas por los usuarios a los elementos. Podemos obtener información sobre un usuario tanto a partir de los elementos que ha calificado como de otros usuarios que han calificado los mismos elementos.  

El recomendador SVD usa identificadores de los usuarios y los elementos, así como una matriz de las clasificaciones que los usuarios le proporcionan a los elementos. Es un *recomendador colaborativo*. 

Para más información sobre el recomendador SVD, consulte el documento de investigación pertinente: [Técnicas de factorización de matriz para los sistemas de recomendación](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Cómo configurar el entrenamiento del recomendador SVD  

### <a name="prepare-data"></a>Preparación de los datos

Antes de usar el módulo, los datos de entrada deben estar en el formato que el modelo de recomendación espera. Se requiere un conjunto de datos de aprendizaje de tripletas usuario-elemento-clasificación.

+ La primera columna contiene los identificadores de usuario.
+ La segunda columna contiene los identificadores de elemento.
+ La tercera columna contiene la clasificación del par usuario-elemento. Los valores de clasificación deben ser numéricos.  

El conjunto de datos **Calificaciones de restaurantes** del diseñador de Azure Machine Learning (seleccione **Saved Datasets** [Conjuntos de datos guardados] y, después, **Samples** [Ejemplos]) muestra el formato esperado:

|Identificador de usuario|Identificador de lugar|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

En este ejemplo, se puede ver que solo un usuario ha clasificado dos restaurantes distintos. 

### <a name="train-the-model"></a>Entrenamiento del modelo

1.  Agregue el módulo Entrenar recomendador de SVD a la canalización del diseñador y conéctelo a los datos de aprendizaje.  
   
2.  En **Number of factors** (Número de factores), escriba el número de factores que se van a usar con el recomendador.  
    
    Cada uno de ellos mide la relación del usuario con el elemento. El número de datos también representa la dimensionalidad del espacio del dato latente. Con el aumento del número de usuarios y de elementos, se recomienda establecer un mayor número de factores. Pero tenga en cuenta que si el número es demasiado grande, es posible que el rendimiento baje.
    
3.  **Número de iteraciones recomendadas del algoritmo** indica el número de veces que el algoritmo debe procesar los datos de entrada. Cuanto mayor sea este número, más precisas serán las predicciones. Sin embargo, un número mayor también significa que el aprendizaje será más lento. El valor predeterminado es 30.

4.  En **Learning rate** (Velocidad de aprendizaje), escriba un número entre 0,0 y 2,0 que defina el tamaño del paso durante el aprendizaje.

    La velocidad de aprendizaje determina el tamaño del paso en cada iteración. Si el tamaño del paso es demasiado grande, puede pasar por alto la solución óptima. Si el tamaño del paso es demasiado pequeño, el aprendizaje tarda más tiempo en encontrar la mejor solución. 
  
5.  Ejecución de la canalización  


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning. 
