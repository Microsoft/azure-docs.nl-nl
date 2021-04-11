---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning-R-script uitvoeren'
description: Opnieuw opbouwen Studio (klassiek) R-script modules uitvoeren om uit te voeren op Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564897"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>De script modules Execute R in Studio (klassiek) migreren

In dit artikel leert u hoe u een **R-script** module voor het uitvoeren van een studio (klassiek) opnieuw bouwt in azure machine learning.

Zie het [artikel migratie overzicht](migrate-overview.md)voor meer informatie over het migreren van Studio (klassiek).

## <a name="execute-r-script"></a>R-Script uitvoeren

Azure Machine Learning Designer wordt nu uitgevoerd op Linux. Studio (klassiek) wordt uitgevoerd op Windows. Als gevolg van het platform is gewijzigd, moet u het **uitvoeren van R-script** aanpassen tijdens de migratie, anders mislukt de pijp lijn.

Als u een **Execute R-script** module van Studio (klassiek) wilt migreren, moet u de interfaces en vervangen door de `maml.mapInputPort` `maml.mapOutputPort` standaard functies.

De volgende tabel bevat een overzicht van de wijzigingen in de R-script module:

|Functie|Studio (klassiek)|Azure Machine Learning-ontwerpprogramma|
|---|---|---|
|Script interface|`maml.mapInputPort` en `maml.mapOutputPort`|Functie interface|
|Platform|Windows|Linux|
|Toegankelijk via Internet |Nee|Ja|
|Geheugen|14 GB|Afhankelijk van Compute-SKU|

### <a name="how-to-update-the-r-script-interface"></a>De R-script Interface bijwerken

Hier vindt u de inhoud van een voor beeld van een **R-script module uitvoeren** in Studio (klassiek):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Hier vindt u de bijgewerkte inhoud in de ontwerp functie. U ziet dat de `maml.mapInputPort` en `maml.mapOutputPort` zijn vervangen door de standaard functie interface `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Zie de naslag informatie over het uitvoeren van de [R-script module](../algorithm-module-reference/execute-r-script.md).

### <a name="install-r-packages-from-the-internet"></a>R-pakketten installeren vanaf internet

Met Azure Machine Learning Designer kunt u pakketten rechtstreeks vanuit KRANs installeren.

Dit is een verbetering ten opzichte van Studio (klassiek). Omdat Studio (klassiek) wordt uitgevoerd in een sandbox-omgeving zonder Internet toegang, moest u scripts in een zip-bundel uploaden om meer pakketten te installeren. 

Gebruik de volgende code om KRANs pakketten te installeren in de module **Execute R script** van de ontwerp functie:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Execute R-script modules kunt migreren naar Azure Machine Learning.

Zie de andere artikelen in de migratie reeks Studio (klassiek):

1. [Overzicht migratie](migrate-overview.md).
1. [Gegevensset migreren](migrate-register-dataset.md).
1. [Bouw een studio-trainings pijplijn (klassiek) opnieuw](migrate-rebuild-experiment.md)op.
1. [Een studio-webservice (klassiek) opnieuw bouwen](migrate-rebuild-web-service.md).
1. [Een Azure machine learning-webservice integreren met client-apps](migrate-rebuild-integrate-with-client-app.md).
1. **Migreer het uitvoeren van R-script modules**.