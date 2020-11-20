---
title: Azure container instance-recept
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van Cognitive Services-containers in azure container instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ddaed181d017e3167694a9d7edf53c7c09fd5e9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968516"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Container implementeren en uitvoeren in Azure Container Instance

Met de volgende stappen kunt u Azure Cognitive Services-toepassingen in de Cloud eenvoudig schalen met Azure [container instances](../../container-instances/index.yml). Met container opslag kunt u zich richten op het bouwen van uw toepassingen in plaats van de infra structuur te beheren. Zie [functies en voor delen](../cognitive-services-container-support.md#features-and-benefits)voor meer informatie over het gebruik van containers.

## <a name="prerequisites"></a>Vereisten

Het recept werkt met een Cognitive Services-container. De cognitieve service resource moet worden gemaakt in de Azure Portal voordat u het recept gebruikt. Elke cognitieve service die containers ondersteunt, is bedoeld voor het installeren en configureren van de service voor een container. Voor sommige services is een bestand of een set bestanden vereist als invoer voor de container. het is belang rijk dat u de container hebt gebruikt voordat u deze oplossing gebruikt.

* Een cognitieve service resource, gemaakt in Azure Portal.
* URL van het cognitieve service- **eind punt** : Controleer de installatie van uw specifieke service voor de container, zodat u kunt vinden waar de eind punt-URL zich bevindt in het Azure Portal en wat een juist voor beeld van de URL is. De exacte indeling kan veranderen van service naar service.
* Cognitieve service **sleutel** : de sleutels bevinden zich op de pagina **sleutels** voor de Azure-resource. U hebt slechts een van de twee sleutels nodig. De sleutel is een teken reeks van 32 alfanumerieke tekens.
* Eén Cognitive Services-container op uw lokale host (uw computer). Zorg ervoor dat u het volgende kunt doen:
  * Haal de installatie kopie op met een `docker pull` opdracht.
  * Voer de lokale container uit met alle vereiste configuratie-instellingen met een `docker run` opdracht.
  * Roep het eind punt van de container aan, waarbij een reactie van HTTP-2xx en een JSON-antwoord wordt teruggestuurd.

Alle variabelen tussen punt haken, `<>` moeten worden vervangen door uw eigen waarden. Deze vervanging omvat de punt haken.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Het container exemplaar gebruiken

1. Selecteer het **overzicht** en kopieer het IP-adres. Dit is een numeriek IP-adres, zoals `55.55.55.55` .
1. Open een nieuw browser tabblad en gebruik het IP-adres, bijvoorbeeld `http://<IP-address>:5000 (http://55.55.55.55:5000` ). U ziet de start pagina van de container, zodat u weet dat de container wordt uitgevoerd.

1. Selecteer **Service-API-beschrijving** om de Swagger-pagina voor de container weer te geven.

1. Selecteer een van de **post** -api's en selecteer **try-out**.  De para meters worden weer gegeven met inbegrip van de invoer. Vul de para meters in.

1. Selecteer **uitvoeren** om de aanvraag naar uw container exemplaar te verzenden.

    U hebt Cognitive Services containers gemaakt en gebruikt in azure container instance.