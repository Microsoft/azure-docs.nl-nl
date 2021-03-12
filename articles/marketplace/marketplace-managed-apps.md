---
title: Publicatie handleiding voor Azure Applications Managed Application aanbieding-Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een beheerde toepassing in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 09badebab86da8e4fd8d35307aa6648a26e91821
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232253"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Publicatie handleiding voor door Azure beheerde toepassingen

Een Azure *Managed Application* -aanbieding is een manier om een Azure-toepassing te publiceren in azure Marketplace. Beheerde toepassingen zijn Transact-aanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De optie voor de vermelding die een gebruiker ziet, is *nu downloaden*.

In dit artikel worden de vereisten voor het type beheerde toepassings aanbieding beschreven.

Gebruik het aanbiedings type beheerde toepassing onder de volgende voor waarden:

- U implementeert een oplossing op basis van een abonnement voor uw klant door gebruik te maken van een virtuele machine (VM) of een volledige IaaS-oplossing (Infrastructure as a Service).
- U of uw klant vereist dat de oplossing wordt beheerd door een partner.

>[!NOTE]
>Een partner kan bijvoorbeeld een systeem integrator of een managed service provider (MSP) zijn.  

## <a name="managed-application-offer-requirements"></a>Vereisten voor de aanbieding van beheerde toepassingen

|Vereisten |Details  |
|---------|---------|
|Een Azure-abonnement | Beheerde toepassingen moeten worden geïmplementeerd op het abonnement van een klant, maar kunnen worden beheerd door derden. |
|Facturering en meting    |  De resources zijn opgenomen in het Azure-abonnement van een klant. Azure-resources die gebruikmaken van het betalen per gebruik-betalings model, worden door micro soft gefactureerd met de klant via het Azure-abonnement van de klant. <br><br> Voor de Azure-resources die uw eigen licentie bieden, factureert micro soft eventuele infrastructuur kosten die zijn gemaakt in het abonnement van de klant, maar u kunt ook de kosten voor software licenties ook rechtstreeks aan de klant door geven.        |
|Een door Azure beheerd toepassings pakket    |   De geconfigureerde Azure Resource Manager sjabloon en de definitie van de gebruikers interface maken die wordt gebruikt om uw toepassing te implementeren in het abonnement van de klant.<br><br>Zie [overzicht van beheerde toepassingen](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)voor meer informatie over het maken van een beheerde toepassing.|

---

> [!NOTE]
> Beheerde toepassingen moeten worden geïmplementeerd via Azure Marketplace. Als de communicatie van de klant een probleem is, kunt u contact met geïnteresseerde klanten bereiken nadat u het delen van leads hebt ingeschakeld.  

> [!Note]
> Een opt-in voor de Cloud Solution Provider (CSP)-partner kanaal is nu beschikbaar. Zie [Cloud Solution Providers](./cloud-solution-providers.md)(Engelstalig) voor meer informatie over het marketing uw aanbod via de micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, leert u hoe u [uw Cloud activiteiten kunt verg Roten met Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registreren voor en werken in het partner centrum:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- Zie [een Azure-toepassings aanbieding maken](./create-new-azure-apps-offer.md) voor meer informatie.
