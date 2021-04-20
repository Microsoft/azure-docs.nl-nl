---
title: Verplaats Azure-resources tussen resourcegroepen, abonnementen of regio's.
description: Overzicht van Azure-resourcetypen die kunnen worden verplaatst tussen resourcegroepen, abonnementen of regio's.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730457"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Azure-resources verplaatsen tussen resourcegroepen, abonnementen of regio's

Azure-resources kunnen worden verplaatst naar een nieuwe resourcegroep, een nieuw abonnement of tussen regio's.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Resources verplaatsen tussen resourcegroepen of abonnementen

U kunt Azure-resources verplaatsen naar een ander Azure-abonnement of een andere resourcegroep onder hetzelfde abonnement. U kunt resources verplaatsen via de Azure-portal, Azure PowerShell, Azure CLI of de REST API. Zie Resources verplaatsen naar een nieuwe resourcegroep of een [nieuw abonnement voor meer informatie.](move-resource-group-and-subscription.md)

### <a name="upgrade-a-subscription"></a>Een abonnement upgraden

Als u uw Azure-abonnement daadwerkelijk wilt upgraden (bijvoorbeeld van gratis naar betalen per gebruik), moet u uw abonnement converteren.

- Zie Upgrade your Free Trial or [Microsoft Imagine Azure subscription to Pay-As-You-Go](../../cost-management-billing/manage/upgrade-azure-subscription.md)(Uw gratis proefversie upgraden of een Azure-abonnement upgraden naar betalen per gebruik) als u een gratis proefversie wilt upgraden.
- Zie Uw Azure-abonnement voor betalen per gebruik wijzigen in een andere aanbieding als u een betalen per [gebruik-account wilt wijzigen.](../../cost-management-billing/manage/switch-azure-offer.md)

Als u het abonnement niet kunt converteren, maakt [u een ondersteuning voor Azure aanvraag](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **Abonnementsbeheer** als het probleemtype.

## <a name="move-resources-across-regions"></a>Resources verplaatsen tussen regio's

Geografische gebieden, regio's en beschikbaarheidszones in Azure vormen de basis van de wereldwijde Azure-infrastructuur. [Azure-geografieën](https://azure.microsoft.com/global-infrastructure/geographies/) bevatten doorgaans twee of meer [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) Een regio is een gebied binnen een geografie, met Beschikbaarheidszones en meerdere datacenters.

Nadat u resources in een specifieke Azure-regio hebt geïmplementeerd, zijn er veel redenen waarom u resources naar een andere regio wilt verplaatsen.

- **Afstemmen op het starten van een regio:** verplaats uw resources naar een nieuw geïntroduceerde Azure-regio die nog niet beschikbaar was.
- **Afstemming op services/functies**: Verplaats resources om te profiteren van services of functies die in een bepaalde regio beschikbaar zijn.
- **Reageren op bedrijfsontwikkelingen**: Verplaats resources naar een regio als reactie op bedrijfswijzigingen, zoals fusies of overnames.
- **Afstemming op nabijheid**: Verplaats resources naar een regio die lokaal is voor uw bedrijf.
- **Voldoen aan de gegevensvereisten**: Verplaats resources om af te stemmen met de vereisten voor gegevenslocatie of de behoeften aan gegevensclassificatie. [Meer informatie](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reageren op implementatievereisten**: Verplaats resources die foutief zijn geïmplementeerd of verplaats als reactie op capaciteitsbehoeften.
- **Reageren op uit bedrijf nemen:** resources verplaatsen vanwege uit bedrijf genomen regio's.

### <a name="move-resources-with-resource-mover"></a>Resources verplaatsen met Resource Mover

U kunt resources verplaatsen naar een andere regio met [Azure Resource Mover.](../../resource-mover/overview.md) Resource Mover biedt:

- Eén hub voor het verplaatsen van resources binnen regio's.
- Beperkte verplaatsingstijd en complexiteit. Alles wat u nodig hebt, bevindt zich op één locatie.
- Een eenvoudige en consistente ervaring voor het verplaatsen van verschillende typen Azure-resources.
- Een eenvoudige manier om afhankelijkheden te identificeren binnen resources die u wilt verplaatsen. Deze identificatie helpt u bij het samen verplaatsen van gerelateerde resources, zodat alles werkt zoals verwacht in de doelregio, na de overstap.
- Automatische opschoning van resources in de bronregio, als u deze na de verplaatsing wilt verwijderen.
- Testen. U kunt een verplaatsing uitproberen en deze vervolgens verwijderen als u geen volledige verplaatsing wilt uitvoeren.

U kunt resources op verschillende manieren verplaatsen naar een andere regio:

- **Beginnen met het verplaatsen van resources vanuit een resourcegroep:** met deze methode start u de regio verplaatsen vanuit een resourcegroep. Nadat u de resources hebt geselecteerd die u wilt verplaatsen, wordt het proces voortgezet in de Resource Mover-hub om resourceafhankelijkheden te controleren en het verplaatsen te beheren. [Meer informatie](../../resource-mover/move-region-within-resource-group.md).
- Begin met het verplaatsen van resources rechtstreeks vanuit de **Resource Mover-hub:** met deze methode start u het proces voor het verplaatsen van regio's rechtstreeks in de hub. [Meer informatie](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Volgende stappen

- Zie Bewerkingsondersteuning verplaatsen voor resources om te controleren of een resourcetype ondersteuning biedt voor [verplaatsen.](move-support-resources.md)
- Zie Over het verplaatsproces voor meer informatie over het proces voor het [verplaatsen van regio's.](../../resource-mover/about-move-process.md)
