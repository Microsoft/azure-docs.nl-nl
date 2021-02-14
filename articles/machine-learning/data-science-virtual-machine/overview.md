---
title: Wat is de Azure Data Science Virtual Machine?
titleSuffix: Azure Data Science Virtual Machine
description: "Overzicht van Azure Data Science Virtual Machine: een gebruiksvriendelijke virtuele machine op het Azure-cloudplatform met vooraf geïnstalleerde en geconfigureerde hulpprogramma's en bibliotheken voor het uitvoeren van datatechnologie."
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: bd2333d89e4d1789b3464606b49f624609ef67d5
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518756"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wat is de Azure Data Science Virtual Machine voor Linux en Windows?

Data Science Virtual Machine (DSVM) is een aangepaste VM-installatiekopie op het Azure-cloudplatform, dat speciaal is gebouwd voor datatechnologie. DSVM bevat veel populaire datatechnologie- en andere hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd opdat u snel intelligente toepassingen voor geavanceerde analyses kunt gaan bouwen.

De DSVM is beschikbaar op:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Vergelijking met Azure Machine Learning

De DSVM is een aangepaste VM-installatiekopie voor Data Science, maar [Azure Machine Learning](../overview-what-is-azure-ml.md) (AzureML) is een end-to-end platform dat het volgende omvat:

+ Volledig beheerd rekenproces
  + Rekeninstanties
  + Rekenclusters voor gedistribueerde ML-taken
  + Deductieclusters voor realtime scoren
+ Gegevensarchieven (bijvoorbeeld blob, ADLS Gen2, SQL DB)
+ Experimenten volgen
+ Modelbeheer
+ Notebooks
+ Omgevingen (Conda- en R-afhankelijkheden beheren)
+ Labels
+ Pijplijnen (end-to-end datatechnologiewerkstromen automatiseren)

### <a name="comparison-with-azureml-compute-instances"></a>Vergelijking met AzureML Compute-instanties

[Azure Machine Learning Compute-instanties](../concept-compute-instance.md) zijn een volledig geconfigureerde en __beheerde__ VM-installatiekopie, terwijl de DSVM een __niet-beheerde__ VM is.

De belangrijkste verschillen tussen deze twee productaanbiedingen worden hieronder beschreven:


|Functie |Datatechnologie<br>VM |AzureML<br>Rekenproces  | 
|---------|---------|---------|
| Volledig beheerd | Nee        | Ja        |
|Taalondersteuning     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F#       | Python en R        |
|Besturingssysteem     | Ubuntu<br>Windows         |    Ubuntu     |
|Vooraf geconfigureerde GPU-optie     |  Ja       |    Ja     |
|Optie voor omhoog schalen | Ja | Ja |
|SSH-toegang    | Ja        |    Ja     |
|RDP-toegang    | Ja        |     Nee    |
|Ingebouwd<br>Gehoste notebooks     |   Nee<br>(hiervoor is aanvullende configuratie vereist)      |      Ja   |
|Ingebouwde SSO     | Nee <br>(hiervoor is aanvullende configuratie vereist)         |    Ja     |
|Ingebouwde samenwerking     | Nee         | Ja        |
|Vooraf geïnstalleerde hulpprogramma's     |  Jupyter(lab), RStudio Server, VSCode,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SQL Server Management Studio, <br>Microsoft Office 365, Apache Drill       |     Jupyter(lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Voorbeelden van gebruikstoepassingen

Hieronder ziet u enkele veelvoorkomende gebruikstoepassingen voor DSVM-klanten.

### <a name="short-term-experimentation-and-evaluation"></a>Experimenten en evaluatie op korte termijn

U kunt de DSVM gebruiken om nieuwe [-hulpprogramma's voor datatechnologie te evalueren of te leren](./tools-included.md), met name door enkele van onze gepubliceerde [-voorbeelden en -instructies te door lopen](./dsvm-samples-and-walkthroughs.md).

### <a name="deep-learning-with-gpus"></a>Deep learning met GPU's

In de DSVM kunnen uw trainingsmodellen deep learning-algoritmen op hardware gebruiken die is gebaseerd op GPU's (grafische verwerkingseenheden). Door gebruik te maken van de VM-schaalmogelijkheden van het Azure-platform, helpt de DSVM u om op GPU gebaseerde hardware in de cloud te gebruiken op basis van uw behoeften. U kunt overschakelen naar een GPU-VM wanneer u grote modellen traint of snelle berekeningen nodig hebt en dezelfde besturingssysteemschijf moet behouden. U kunt kiezen uit een van de VM-SKU's met GPU-functionaliteit uit de N-serie met DSVM. Let wel: VM-SKU's met GPU-functionaliteit worden niet ondersteund in gratis Azure-accounts.

De Windows-edities van DSVM worden geleverd met vooraf geïnstalleerde GPU-stuurprogramma's, frameworks en GPU-versies van frameworks voor deep learning. In de Linux-edities is deep learning voor GPU's ingeschakeld op de Ubuntu-DSVM's. 

U kunt ook de Ubuntu- of Windows-edities van de DSVM implementeren op een virtuele machine van Azure die niet is gebaseerd op GPU's. In dit geval zullen alle deep learning frameworks terugvallen op de CPU-modus.

[Meer informatie over beschikbare deep learning en AI-frameworks](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Training en onderwijs voor datatechnologie

Bedrijfstrainers en docenten die datatechnologiecursussen geven verstrekken doorgaans een VM-installatiekopie. De installatiekopie zorgt ervoor dat studenten een consistente installatie hebben en dat de voorbeelden werken zoals verwacht.

De DSVM maakt een omgeving op aanvraag met een consistente configuratie, waardoor zich minder problemen met ondersteuning en incompatibiliteit voordoen. In gevallen waarin deze omgevingen vaak moeten worden gemaakt, met name voor kortere trainingen, levert dit veel voordelen op.


## <a name="whats-included-on-the-dsvm"></a>Wat is inbegrepen in de DSVM?

Bekijk [hier](tools-included.md) een volledige lijst met hulpprogramma's voor zowel de Windows- als de Linux-DSVM.

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in deze artikelen:

+ Windows:
  + [Een Windows-DSVM instellen](provision-vm.md)
  + [Datatechnologie op een Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Een Linux-DSVM instellen (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Datatechnologie op een Linux-DSVM](linux-dsvm-walkthrough.md)