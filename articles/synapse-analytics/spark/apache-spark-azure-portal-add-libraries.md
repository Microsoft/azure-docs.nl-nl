---
title: Pakketbeheer
description: Meer informatie over het toevoegen en beheren van bibliotheken die worden gebruikt door Apache Spark in azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598011"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Bibliotheken voor Apache Spark beheren in azure Synapse Analytics
Bibliotheken bieden herbruikbare code die u mogelijk wilt toevoegen aan uw Program ma's of projecten. 

Mogelijk moet u uw serverloze Apache Spark pool omgeving om verschillende redenen bijwerken. U kunt bijvoorbeeld het volgende doen:
- een van de kern afhankelijkheden heeft een nieuwe versie uitgegeven.
- u hebt een extra pakket nodig om uw machine learning model te trainen of uw gegevens voor te bereiden.
- u hebt een beter pakket gevonden en hebt het oudere pakket niet meer nodig.
- uw team heeft een aangepast pakket gebouwd dat u nodig hebt in uw Apache Spark groep.

Als u een derde of lokaal gemaakte code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van de serverloze Apache Spark Pools of de notitieblok sessie.
  
## <a name="default-installation"></a>Standaard installatie
Apache Spark in azure Synapse Analytics beschikt over een volledige Anacondas-installatie plus extra bibliotheken. De lijst met volledige bibliotheken vindt u op [Apache Spark-versie ondersteuning](apache-spark-version-support.md). 

Wanneer een Spark-exemplaar wordt gestart, worden deze bibliotheken automatisch opgenomen. Extra pakketten kunnen worden toegevoegd op het niveau van de Spark-groep of het sessie niveau.

## <a name="workspace-packages"></a>Werkruimte pakketten
Bij het ontwikkelen van aangepaste toepassingen of modellen kan uw team verschillende code artefacten, zoals Wheel-of jar-bestanden, ontwikkelen om uw code te pakken. 

In Synapse kunnen werkruimte pakketten aangepaste of privé-wielen of jar-bestanden zijn. U kunt deze pakketten uploaden naar uw werk ruimte en deze later toewijzen aan een specifieke Spark-groep. Na de toewijzing worden deze werkruimte pakketten automatisch geïnstalleerd op alle Spark-groeps sessies.

Voor meer informatie over het beheren van werkruimte bibliotheken raadpleegt u de volgende hand leidingen:

- [Python-werkruimte pakketten (preview-versie): ](./apache-spark-manage-python-packages.md#install-wheel-files) Upload python-wiel bestanden als een werkruimte pakket en voeg deze pakketten later toe aan specifieke serverloze Apache Spark groepen.
- [Scala/Java Workspace-pakketten (preview): ](./apache-spark-manage-scala-packages.md#workspace-packages) Upload scala-en java jar-bestanden als een werkruimte pakket en voeg deze pakketten later toe aan specifieke serverloze Apache Spark groepen.

## <a name="pool-packages"></a>Groeps pakketten
In sommige gevallen wilt u mogelijk de set met pakketten die worden gebruikt in een bepaalde Apache Spark groep standaardiseren. Deze standaardisatie kan nuttig zijn als dezelfde pakketten worden geïnstalleerd door meerdere personen in uw team. 

Met de Azure Synapse Analytics-groeps beheer mogelijkheden kunt u de standaardset bibliotheken configureren die u wilt installeren op een gegeven serverloze Apache Spark groep. Deze bibliotheken worden boven op de basis- [runtime](./apache-spark-version-support.md)geïnstalleerd. 

Groeps beheer wordt momenteel alleen ondersteund voor python. Voor python gebruiken Synapse Spark-Pools Conda om python-pakket afhankelijkheden te installeren en te beheren. Wanneer u uw bibliotheken op groeps niveau opgeeft, kunt u nu een requirements.txt of een omgeving. yml opgeven. Dit omgevings configuratie bestand wordt gebruikt wanneer een Spark-exemplaar wordt gemaakt vanuit die Spark-groep. 

Raadpleeg de documentatie over [python-groeps beheer](./apache-spark-manage-python-packages.md#pool-libraries)voor meer informatie over deze mogelijkheden.

> [!IMPORTANT]
> - Als het pakket dat u installeert groot is of veel tijd nodig heeft om te worden geïnstalleerd, is dit van invloed op de start tijd van de Spark-instantie.
> - Het wijzigen van de PySpark-, Python-, scala/Java-, .NET-of Spark-versie wordt niet ondersteund.
> - Het installeren van pakketten van PyPI wordt niet ondersteund in werk ruimten waarvoor DEP is ingeschakeld.

## <a name="session-scoped-packages"></a>Pakketten met sessie bereik
Bij het uitvoeren van interactieve gegevens analyse of machine learning, kunt u er vaak voor zorgen dat u nieuwere pakketten wilt proberen of dat u pakketten nodig hebt die nog niet beschikbaar zijn in uw Apache Spark groep. In plaats van de pool configuratie bij te werken, kunnen gebruikers nu pakketten met sessie bereik gebruiken om sessie afhankelijkheden toe te voegen, te beheren en bij te werken.

Met pakketten met sessie bereik kunnen gebruikers aan het begin van hun sessie pakket afhankelijkheden definiëren. Wanneer u een pakket met sessie bereik installeert, heeft alleen de huidige sessie toegang tot de opgegeven pakketten. Als gevolg hiervan zijn deze pakketten met sessie bereik niet van invloed op andere sessies of taken die gebruikmaken van dezelfde Apache Spark pool. Daarnaast worden deze bibliotheken boven op de basis-runtime-en pool level-pakketten geïnstalleerd. 

Voor meer informatie over het beheren van pakketten met sessie bereik raadpleegt u de volgende hand leidingen:

- [Python-sessie pakketten (preview-versie):](./apache-spark-manage-python-packages.md) Aan het begin van een sessie geeft u een Conda- *omgeving. yml* extra Python-pakketten installeren vanuit populaire opslag plaatsen. 
- [Scala/Java-sessie pakketten: ](./apache-spark-manage-scala-packages.md) Aan het begin van uw sessie geeft u een lijst van jar-bestanden op die moeten worden geïnstalleerd met `%%configure` .

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)
