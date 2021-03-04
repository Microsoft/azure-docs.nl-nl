---
title: 'Quick Start: een redis Enter prise-cache maken'
description: In deze Quick Start leert u hoe u een exemplaar van Azure cache maakt voor redis in bedrijfs lagen
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8dbde1f74c700e772823d61b3e35a464f9bbc17e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120960"
---
# <a name="quickstart-create-a-redis-enterprise-cache-preview"></a>Quick Start: een redis Enter prise-cache maken (preview)

De Enterprise-lagen voor Azure Cache voor Redis bieden het volledig geïntegreerde en beheerde [Redis Enterprise](https://redislabs.com/redis-enterprise/) in Azure. Ze zijn momenteel beschikbaar als preview. Er zijn twee nieuwe lagen beschikbaar in deze preview:
* Enterprise, waarbij gebruik wordt gemaakt van vluchtig geheugen (DRAM) op een virtuele machine om gegevens op te slaan
* Enterprise Flash, waarbij wordt gebruikgemaakt van vluchtig en niet-vluchtig geheugen (NVMe of SSD) om gegevens op te slaan.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig voordat u kunt beginnen. Als u nog geen account hebt, kunt u [een account maken](https://azure.microsoft.com/). Zie [speciale overwegingen voor bedrijfs lagen](cache-overview.md#special-considerations-for-enterprise-tiers)voor meer informatie.

## <a name="create-a-cache"></a>Een cache maken
1. Meld u aan bij de Azure-portal via de link in uw preview-uitnodiging en selecteer **Een resource maken** om een cache te maken.

1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Azure Cache voor Redis selecteren":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van het cache-exemplaar is *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net*. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Tijdens de preview-fase zijn Enterprise-lagen in een beperkt aantal Azure-regio's beschikbaar. |
   | **Cachetype** | Open de vervolgkeuzelijst en selecteer een *Enterprise*- of *Enterprise Flash*-laag, en een grootte. |  De grootte, prestaties en functies die beschikbaar zijn voor de cache, zijn afhankelijk van de gekozen laag. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Tabblad basis principes van de Enter prise-laag":::

   > [!NOTE] 
   > Zorg ervoor dat u het selectievakje onder 'Voorwaarden' inschakelt voordat u doorgaat.
   >

1. Selecteer **Volgende: Netwerken** en ga door.

1. Selecteer **Volgende: Geavanceerd** en stel **Clusterbeleid** in **Enterprise** in. Schakel **niet-TLS-toegang alleen** in als u van plan bent om verbinding te maken met de nieuwe cache zonder TLS. Dit wordt echter niet aanbevolen.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Tabblad Geavanceerd van de Enter prise-laag":::

1. Selecteer **Volgende: Tags** en ga door.

1. Selecteer **Volgende: Beoordelen en maken**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Het tabblad controleren op bedrijfs niveau + maken":::

1. Controleer de instellingen en klik op **Maken**.
   
   Het duurt enige tijd voordat de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een instantie van een Enterprise-laag van Azure Cache voor Redis maakt.

> [!div class="nextstepaction"]
> [Maak een ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

