---
title: Een virtuele Azure Functions configureren
description: In dit artikel wordt beschreven hoe u bepaalde taken voor virtuele netwerken uitvoert voor Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718427"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Een virtuele Azure Functions configureren

In dit artikel wordt beschreven hoe u taken uitvoert die betrekking hebben op het configureren van uw functie-app om verbinding te maken met en uit te voeren in een virtueel netwerk. Zie netwerkopties voor Azure Functions meer informatie [over Azure Functions netwerken.](functions-networking-options.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Uw opslagaccount beperken tot een virtueel netwerk 

Wanneer u een functie-app maakt, moet u een account voor algemeen gebruik Azure Storage maken of koppelen dat ondersteuning biedt voor Blob-, Queue- en Table-opslag. U kunt dit opslagaccount vervangen door een opslagaccount dat is beveiligd met service-eindpunten of een privé-eindpunt. 

> [!NOTE]  
> Deze functie werkt momenteel voor alle door virtuele Windows-netwerken ondersteunde SKU's in het Dedicated-abonnement (App Service) en voor Premium-abonnementen. Verbruiksplan wordt niet ondersteund. 

Een functie instellen met een opslagaccount dat is beperkt tot een particulier netwerk:

1. Maak een functie met een opslagaccount waar geen service-eindpunten zijn ingeschakeld.

1. Configureer de functie om verbinding te maken met uw virtuele netwerk.

1. Maak of configureer een ander opslagaccount.  Dit is het opslagaccount dat we beveiligen met service-eindpunten en onze functie verbinden.

1. [Maak een bestands share](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) in het beveiligde opslagaccount.

1. Schakel service-eindpunten of privé-eindpunten in voor het opslagaccount.  
    * Als u privé-eindpuntverbindingen gebruikt, heeft het opslagaccount een privé-eindpunt nodig voor de `file` subbronnen en `blob` .  Als u bepaalde mogelijkheden, zoals Durable Functions, hebt u ook en toegankelijk `queue` `table` nodig via een privé-eindpuntverbinding.
    * Als u service-eindpunten gebruikt, moet u het subnet inschakelen dat is toegewezen aan uw functie-apps voor opslagaccounts.

1. Kopieer het bestand en de blob-inhoud van het opslagaccount van de functie-app naar het beveiligde opslagaccount en de bestands share.

1. Kopieer de connection string voor dit opslagaccount.

1. Werk de **toepassingsinstellingen** onder **Configuratie voor** de functie-app als volgt bij:

    | Naam van de instelling | Waarde | Opmerking |
    |----|----|----|
    | `AzureWebJobsStorage`| Opslag connection string | Dit is de connection string voor een beveiligd opslagaccount. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Opslag connection string | Dit is de connection string voor een beveiligd opslagaccount. |
    | `WEBSITE_CONTENTSHARE` | Bestandsshare | De naam van de bestands share die is gemaakt in het beveiligde opslagaccount waarin de projectimplementatiebestanden zich bevinden. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nieuwe instelling |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Dwingt al het uitgaande verkeer via het virtuele netwerk. Vereist wanneer het opslagaccount privé-eindpuntverbindingen gebruikt. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | De DNS-server die wordt gebruikt door de app. Vereist wanneer het opslagaccount privé-eindpuntverbindingen gebruikt. |

1. Selecteer **Opslaan om** de toepassingsinstellingen op te slaan. Het wijzigen van app-instellingen zorgt ervoor dat de app opnieuw wordt gestart.  

Nadat de functie-app opnieuw is opgestart, is deze verbonden met een beveiligd opslagaccount.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Netwerkopties van Azure Functions](functions-networking-options.md)

