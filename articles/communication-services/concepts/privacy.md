---
title: Regiobeschikbaarheid en gegevenslocatie voor Azure Communication Services
description: Meer informatie over gegevenslocatie en aan privacy gerelateerde onderwerpen over Azure Communication Services
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 933b5605cf38be90d419673a94e23e4c36f0ef36
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495705"
---
# <a name="region-availability-and-data-residency"></a>Regiobeschikbaarheid en gegevenslocatie

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services is toegewijd aan het helpen van onze klanten bij het voldoen aan hun vereisten voor privacy en persoonlijke gegevens. Als ontwikkelaar die gebruikmaakt van Communication Services met een directe relatie met mensen die de toepassing gebruiken, bent u mogelijk een controller van hun gegevens. Omdat Azure Communication Services deze gegevens namens u opslaat en versleutelt, is er waarschijnlijk een processor van deze gegevens. Deze pagina geeft een overzicht van de manier waarop de service gegevens bewaart en hoe u deze gegevens kunt identificeren, exporteren en verwijderen.

## <a name="data-residency"></a>Gegevenslocatie

Bij het maken van een Communication Services-resource geeft u een **geografie** op (niet een Azure-datacentrum). Alle gegevens die in rust worden opgeslagen door Communication Services worden in die geografische regio bewaard, in een datacentrum dat intern door Communication Services is geselecteerd. Gegevens mogen door Voer of worden verwerkt in andere geografische gebieden. Deze globale eind punten zijn nodig voor een snelle en lage latentie ervaring voor eind gebruikers, ongeacht hun locatie.

## <a name="data-residency-and-events"></a>Gegevens locatie en-gebeurtenissen

Elk Event Grid systeem onderwerp dat is geconfigureerd met Azure Communication Services wordt gemaakt op een algemene locatie. Ter ondersteuning van betrouw bare levering kan een algemeen Event Grid systeem onderwerp de gebeurtenis gegevens opslaan in elk micro soft Data Center. Wanneer u Event Grid configureert met Azure Communication Services, levert u uw gebeurtenis gegevens op Event Grid, een Azure-resource onder uw besturings element. Azure Communication Services kan worden geconfigureerd om Azure Event Grid te gebruiken. u bent zelf verantwoordelijk voor het beheren van uw Event Grid bron en de gegevens die erin zijn opgeslagen.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Mensen verbinden aan identiteiten van Azure Communication Services

Uw toepassing beheert de relatie tussen menselijke gebruikers en Communication Service-identiteiten. Als u gegevens voor een menselijke gebruiker wilt verwijderen, moet u gegevens verwijderen die betrekking hebben op alle Communication Service-identiteiten die voor de gebruiker zijn gecorreleerd.

Er zijn twee soorten Communication Service-gegevens:
- **API-gegevens.** Deze gegevens worden gemaakt en beheerd door de API's van de Communication Service. Chatgesprekken die worden beheerd door chat-API’s zijn hier een typisch voorbeeld van.
- **Azure Monitor-logboeken** Deze gegevens worden door de service gemaakt en beheerd via het Azure Monitor-gegevensplatform. Dit zijn onder andere telemetrie- en metrische gegevens waarmee u inzicht krijgt in het gebruik van uw Communication Services. Dit wordt niet beheerd door de Communication Service-API's.

## <a name="api-data"></a>API-gegevens

### <a name="identities"></a>Identiteiten

Azure Communication Services onderhoudt een directory met identiteiten, gebruik de [DeleteIdentity](/rest/api/communication/communicationidentity/delete)-API om ze te verwijderen. Als u een identiteit verwijdert, worden alle bijbehorende toegangstokens ingetrokken en hun chatberichten verwijderd. Zie voor meer informatie over het verwijderen van een identiteit [deze pagina](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Door de API’s van Azure Portal of Azure Resource Manager te gebruiken met Communication Services, kunnen persoonlijke gegevens worden gemaakt. [Gebruik deze pagina voor meer informatie over het beheren van persoonlijke gegevens in Azure Resource Manager-systemen.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Telefoonnummerbeheer

Azure Communication Services onderhoudt een directory met telefoonnummers die zijn gekoppeld aan een Communication Services-resource. Gebruik [api's](/rest/api/communication/phonenumberadministration) voor het beheren van telefoon nummers en verwijder ze om deze op te halen:

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Chat

Chatgesprekken en -berichten worden bewaard totdat ze expliciet worden verwijderd. Een volledig inactief gesprek wordt na 30 dagen automatisch verwijderd. Gebruik [Chat-API’s](/rest/api/communication/chat/chatthread) om berichten op te halen, weer te geven, bij te werken en te verwijderen.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>Sms

Verzonden en ontvangen SMS-berichten worden kortstondig verwerkt door de service en worden niet bewaard.

### <a name="pstn-voice-calling"></a>PSTN-spraakoproepen

Audio- en video-communicatie wordt kortstondig verwerkt door de service en er worden geen gegevens in uw resource bewaard behalve Azure Monitor-logboeken.

### <a name="internet-voice-and-video-calling"></a>Audio- en videobellen via het internet

Audio- en video-communicatie wordt kortstondig verwerkt door de service en er worden geen gegevens in uw resource bewaard behalve Azure Monitor-logboeken.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor en Log Analytics

Met Azure Communication Services worden logboekgegevens van Azure Monitor ingevoerd voor een inzicht in de operationele status en het gebruik van de service. Sommige van deze logboeken bevatten Communication Service-identiteiten en telefoonnummers als veldgegevens. Als u mogelijk persoonlijke gegevens wilt verwijderen [gebruikt u deze procedures voor Azure Monitor](../../azure-monitor/logs/personal-data-mgmt.md). U kunt ook [de standaard retentieperiode voor Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md) configureren.

## <a name="additional-resources"></a>Aanvullende resources

- [Aanvragen van betrokkenen van Azure voor de AVG en CCPA](/microsoft-365/compliance/gdpr-dsr-azure)
- [Vertrouwenscentrum van Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Interactieve kaart van Azure - Waar zijn mijn klantgegevens?](https://azuredatacentermap.azurewebsites.net/)
