---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure container instance-resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 24f6052c436b73d0075371fa74160d21826e2209
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97866048"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Een Azure container Instance-bron maken met behulp van de Azure Portal

1. Ga naar de pagina [maken](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) voor container instances.

2. Voer op het tabblad **basis beginselen** de volgende gegevens in:

    |Instelling|Waarde|
    |--|--|
    |Abonnement|Selecteer uw abonnement.|
    |Resourcegroep|Selecteer de beschik bare resource groep of maak een nieuwe, zoals `cognitive-services` .|
    |Containernaam|Voer een naam in zoals `cognitive-container-instance` . De naam moet in kleine letters worden gereduceerd.|
    |Locatie|Selecteer een regio voor de implementatie.|
    |Type installatiekopie|Als uw container installatie kopie is opgeslagen in een container register waarvoor geen referenties zijn vereist, kiest u `Public` . Als u toegang hebt tot de container installatie kopie, moet u de optie referenties kiezen `Private` . Raadpleeg [container opslagplaatsen en installatie kopieën](../container-image-tags.md) voor meer informatie over of de container installatie kopie al dan niet is `Public` `Private` (' open bare preview '). |
    |Naam van installatiekopie|Voer de locatie van de Cognitive Services-container in. De locatie is wat wordt gebruikt als argument voor de `docker pull` opdracht. Raadpleeg de [container opslagplaatsen en installatie kopieën](../container-image-tags.md) voor de beschik bare afbeeldings namen en de bijbehorende opslag plaats.<br><br>De naam van de installatie kopie moet volledig gekwalificeerd zijn om drie delen op te geven. Ten eerste, het container register en vervolgens de naam van de installatie kopie: `<container-registry>/<repository>/<image-name>` .<br><br>Hier ziet u een voor beeld `mcr.microsoft.com/azure-cognitive-services/keyphrase` van de Sleuteltermextractie installatie kopie in de micro soft-container Registry onder de Azure Cognitive Services-opslag plaats. Een ander voor beeld is `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` dat de spraak-naar-tekst afbeelding wordt weer gegeven in de micro soft-opslag plaats van het container voorbeeld container register. |
    |Type besturings systeem|`Linux`|
    |Grootte|Wijzig grootte in de voorgestelde aanbevelingen voor uw specifieke cognitieve service-container:<br>twee CPU-kernen<br>4 GB

3. Voer op het tabblad **netwerken** de volgende gegevens in:

    |Instelling|Waarde|
    |--|--|
    |Poorten|Stel de TCP-poort in op `5000` . Beschrijft de container op poort 5000.|

4. Voer op het tabblad **Geavanceerd** de vereiste **omgevings variabelen** in voor de instellingen van de container facturering van de Azure container Instance-bron:

    | Sleutel | Waarde |
    |--|--|
    |`ApiKey`|Wordt gekopieerd van de pagina **sleutels en het eind punt** van de resource. Het is een teken reeks van 32 alfanumerieke tekens zonder spaties of streepjes `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`Billing`| De eind punt-URL is gekopieerd van de pagina **sleutels en het eind punt** van de resource.|
    |`Eula`|`accept`|

5. Klik op **controleren en maken**
6. Nadat de validatie is geslaagd, klikt u op **maken** om het aanmaak proces te volt ooien
7. Wanneer de resource is geïmplementeerd, is deze gereed
