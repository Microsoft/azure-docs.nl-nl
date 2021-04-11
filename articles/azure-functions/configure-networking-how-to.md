---
title: Azure Functions configureren met een virtueel netwerk
description: Artikel waarin wordt uitgelegd hoe u bepaalde taken voor virtuele netwerken voor Azure Functions uitvoert.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609223"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Azure Functions configureren met een virtueel netwerk

Dit artikel laat u zien hoe u taken kunt uitvoeren die betrekking hebben op het configureren van uw functie-app om verbinding te maken met en uit te voeren op een virtueel netwerk. Zie [Azure functions Network options](functions-networking-options.md)(Engelstalig) voor meer informatie over Azure functions en netwerken.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Uw opslag account beperken tot een virtueel netwerk 

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. U kunt dit opslag account vervangen door een abonnement dat is beveiligd met Service-eind punten of een persoonlijk eind punt. 

> [!NOTE]  
> Deze functie werkt momenteel voor alle virtuele Windows-Sku's die worden ondersteund in het speciale abonnement (App Service) en voor Premium-abonnementen. Het verbruiks abonnement wordt niet ondersteund. 

Een functie instellen met een opslag account die is beperkt tot een particulier netwerk:

1. Maak een functie met een opslag account waarvoor geen service-eind punten zijn ingeschakeld.

1. Configureer de functie om verbinding te maken met uw virtuele netwerk.

1. Een ander opslag account maken of configureren.  Dit is het opslag account dat wordt beveiligd met Service-eind punten en om onze functie te verbinden.

1. [Maak een bestands share](../storage/files/storage-how-to-create-file-share.md#create-file-share) in het account voor beveiligde opslag.

1. Schakel de service-eind punten of het persoonlijke eind punt in voor het opslag account.  
    * Als u verbindingen met een privé-eind punt gebruikt, hebt u voor het opslag account een persoonlijk eind punt nodig voor de `file` `blob` subbrons.  Als u bepaalde functies als Durable Functions gebruikt, hebt u ook `queue` `table` toegang nodig via een verbinding met een privé-eind punt.
    * Als u service-eind punten gebruikt, schakelt u het subnet in dat is toegewezen aan uw functie-apps voor opslag accounts.

1. Kopieer het bestand en de blob-inhoud van het functie-app-opslag account naar het beveiligde opslag account en de bestands share.

1. Kopieer de connection string voor dit opslag account.

1. Werk de **Toepassings instellingen** onder **configuratie** voor de functie-app als volgt bij:

    | Naam van de instelling | Waarde | Opmerking |
    |----|----|----|
    | `AzureWebJobsStorage`| Opslag connection string | Dit is de connection string voor een beveiligd opslag account. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Opslag connection string | Dit is de connection string voor een beveiligd opslag account. |
    | `WEBSITE_CONTENTSHARE` | Bestandsshare | De naam van de bestands share die is gemaakt in het beveiligde opslag account waarin de project implementatie bestanden zich bevinden. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nieuwe instelling |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Hiermee wordt al het uitgaande verkeer via het virtuele netwerk geforceerd. Vereist wanneer het opslag account gebruikmaakt van verbindingen met een privé-eind punt. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | De DNS-server die door de app wordt gebruikt. Vereist wanneer het opslag account gebruikmaakt van verbindingen met een privé-eind punt. |

1. Selecteer **Opslaan** om de toepassings instellingen op te slaan. Als u de app-instellingen wijzigt, wordt de app opnieuw opgestart.  

Nadat de functie-app opnieuw is opgestart, is deze nu verbonden met een beveiligd opslag account.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Netwerkopties van Azure Functions](functions-networking-options.md)

