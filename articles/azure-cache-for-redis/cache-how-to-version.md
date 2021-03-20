---
title: Redis-versie voor Azure cache instellen voor redis (preview)
description: Meer informatie over het configureren van redis-versie
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93349134"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Redis-versie voor Azure cache instellen voor redis (preview)
In dit artikel leert u hoe u de redis-software versie kunt configureren voor gebruik met uw cache-exemplaar. Azure cache voor redis biedt de meest recente primaire versie van redis en ten minste één vorige versie. Deze versies worden regel matig bijgewerkt als nieuwere redis-software wordt uitgebracht. U kunt kiezen uit de twee beschik bare versies. Houd er rekening mee dat uw cache automatisch wordt bijgewerkt naar de volgende versie als de versie die momenteel wordt gebruikt, niet meer wordt ondersteund.

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Een cache maken
Voer de volgende stappen uit om een cache te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.
  
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecteer Azure Cache voor Redis.":::
   
1. Configureer de instellingen voor de nieuwe cache op de pagina **basis beginselen** .
   
    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
    | **Resourcegroep** | Selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
    | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
    | **Locatie** | Selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
    | **Cachetype** | Selecteer een [cache-laag en-grootte](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |
   
1. Kies op de pagina **Geavanceerd** een redis-versie die u wilt gebruiken.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis-versie.":::

1. Klik op **Create**. 
   
    Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken.

    > [!NOTE]
    > Op dit moment kan de redis-versie niet meer worden gewijzigd nadat een cache is gemaakt.
    >

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

> [!div class="nextstepaction"]
> [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)
