---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: b602cbfde22cc87b42a32b007c19b626814d1660
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554573"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Als u een Azure SignalR Service-resource wilt maken, meldt u zich eerst aan bij de [Azure-portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de startpagina de optie **+ Een resource maken**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Schermopname van de knop een resource maken in de Azure-portal.":::

Voer **Communicatie** in **Zoek in de Marketplace** invoer of in de zoekbalk bovenaan de portal.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Schermopname van een zoekopdracht naar Communication Services in de zoekbalk.":::

Selecteer **communicatie Services** in de resultaten en selecteer vervolgens **maken**.

:::image type="content" source="../media/create-a-communication-resource/create-communication-portal.png" alt-text="Scherm opname van het deel venster communicatie Services, waarbij u de knop maken markeert.":::

U kunt nu uw Communication Services-resource configureren. Op de eerste pagina van het proces maken, wordt u gevraagd het volgende op te geven:

* Het abonnement
* De resourcegroep (u kunt een nieuwe maken of een bestaande resourcegroep kiezen)
* De naam van de Communication Services-resource
* De geografie waaraan de resource wordt gekoppeld

In de volgende stap kunt u tags toewijzen aan de resource. Tags kunnen worden gebruikt voor het organiseren van uw Azure-resources. Zie de [documentatie van resource-tagging ](../../../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags.

Ten slotte kunt u uw configuratie controleren en de resource **Maken**. Houd er rekening mee dat het enkele minuten duurt voordat de implementatie is voltooid.

## <a name="manage-your-communication-services-resource"></a>Een Communication Services-resource maken.

Als u uw Communication Services-resource wilt beheren, gaat u naar de [Azure Portal](https://portal.azure.com)en zoekt en selecteert u **Azure Communication Services**.

Selecteer op de pagina **Communication Services** de naam van uw resource.

De pagina **Overzicht** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Meer configuratie opties vindt u in het menu links van de resource pagina.
