---
title: Veelgestelde vragen over Azure resource-overdrijfing
description: Antwoorden vinden op veelgestelde vragen over Azure resource verhuizer
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: ab4b8f5a691bc8e4091e9f3f01b709391deeddb0
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550705"
---
# <a name="common-questions"></a>Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over [Azure resource-Overdrijfing](overview.md).

## <a name="general"></a>Algemeen

### <a name="is-resource-mover-generally-available"></a>Is resource-overdrijfing algemeen beschikbaar?

Resource-overdrijfing is momenteel beschikbaar als open bare preview. Werk belastingen voor productie worden ondersteund.



## <a name="moving-across-regions"></a>Verplaatsen tussen regio's

### <a name="can-i-move-resources-across-any-regions"></a>Kan ik resources over alle regio's verplaatsen?

Op dit moment kunt u resources verplaatsen van elke open bare bron regio naar een open bare doel regio, afhankelijk van de [beschik bare resource typen in die regio](https://azure.microsoft.com/global-infrastructure/services/). Het verplaatsen van resources in Azure Government regio's wordt momenteel niet ondersteund.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Welke resources kan ik verplaatsen tussen regio's met resource-overdrijf?

Met Resource Mover kunt u momenteel de volgende resources tussen regio's verplaatsen:

- Azure-VM's en gekoppelde schijven
- NIC’s
- Beschikbaarheidssets 
- Virtuele netwerken van Azure. 
- Openbare IP-adressen
- Netwerkbeveiligingsgroepen (NSG's)
- Interne en openbare load balancers 
- Azure SQL-databases en elastische pools


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Kan ik resources verplaatsen tussen abonnementen wanneer ik deze Verplaats tussen regio's?

U kunt het abonnement wijzigen nadat u resources naar de doel regio hebt verplaatst. Meer [informatie](../azure-resource-manager/management/move-resource-group-and-subscription.md) over het verplaatsen van resources naar een ander abonnement. 

### <a name="does-azure-resource-move-service-store-customer-data"></a>Worden klant gegevens door Azure resource Move service opgeslagen? 
Nee. De resource Move-service slaat geen klant gegevens op; hierin worden alleen meta gegevens opgeslagen die de tracering en voortgang van de resources die zijn geselecteerd voor verplaatsing, vergemakkelijkt door de klant.


### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Waar worden de meta gegevens voor het verplaatsen van meerdere opgeslagen regio's?

Het wordt opgeslagen in een [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -data base en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md), in een micro soft-abonnement. Momenteel worden meta gegevens opgeslagen in VS-Oost 2 en Europa-noord. Deze dekking wordt uitgebreid naar andere regio's. Hierdoor hoeft u geen resources over alle open bare regio's te verplaatsen.

### <a name="is-the-collected-metadata-encrypted"></a>Zijn de verzamelde meta gegevens versleuteld?

Ja, zowel onderweg als in rust.
- Tijdens de overdracht worden de meta gegevens via internet veilig verzonden naar de resource-service voor het verplaatsen van HTTPS.
- In de opslag worden meta gegevens versleuteld.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Hoe wordt de beheerde identiteit gebruikt in resource-overschakeling?

[Beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen bekend als managed service Identity (MSI)) biedt Azure-Services met een automatisch beheerde identiteit in azure AD.
- Resource-overschakeling gebruikt beheerde identiteit zodat deze toegang kan krijgen tot Azure-abonnementen om resources te verplaatsen tussen regio's.
- Een verzameling voor verplaatsen heeft een door het systeem toegewezen identiteit nodig, met toegang tot het abonnement dat resources bevat die u wilt verplaatsen.

- Als u resources verplaatst tussen regio's in de portal, wordt dit proces automatisch uitgevoerd.
- Als u resources verplaatst met behulp van Power shell, voert u cmdlets uit om een door het systeem toegewezen identiteit toe te wijzen aan de verzameling en vervolgens een rol met de juiste abonnements machtigingen toe te wijzen aan de id-principal. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Welke beheerde identiteits machtigingen moeten resource-overschakeling hebben?

De beheerde identiteit van de Azure Resource Mover moet ten minste de volgende machtigingen hebben: 

- Machtiging voor het schrijven/maken van resources in het gebruikers abonnement, beschikbaar met de rol *Inzender* . 
- Machtiging om roltoewijzingen te maken. Normaal gesp roken beschikbaar bij de rol van *eigenaar* of *beheerder voor toegang tot de gebruiker* , of met een aangepaste rol waaraan de *machtiging micro soft. Authorization/Role Assignment/write Schrijf* is toegewezen. Deze machtiging is niet nodig als de beheerde identiteit van de gegevensshareresource al toegang tot de Azure-gegevensopslag heeft gekregen. 
 
Wanneer u resources toevoegt in de Resource Mover-hub in de portal, worden de machtigingen automatisch afgehandeld zolang de gebruiker beschikt over de machtigingen die hierboven worden beschreven. Als u resources toevoegt met Power shell, wijst u machtigingen hand matig toe.

> [!IMPORTANT]
> We raden u ten zeerste aan geen toewijzing van identiteits rollen te wijzigen of verwijderen. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Wat moet ik doen als ik geen machtiging heb om een rol-id toe te wijzen?

**Mogelijke oorzaak** | **Aanbeveling**
--- | ---
U bent geen *Inzender* en *beheerder van gebruikers toegang* (of *eigenaar*) wanneer u een resource voor de eerste keer toevoegt. | Gebruik een account met de machtigingen *Inzender* en *beheerder voor gebruikers toegang* (of *eigenaar*) voor het abonnement.
De beheerde identiteit van de resource-overschakeling heeft niet de vereiste rol. | Voeg de rollen Inzender en beheerder van gebruikers toegang toe.
De beheerde identiteit voor resource-overschakeling is opnieuw ingesteld op *geen*. | Schakel een door het systeem toegewezen identiteit opnieuw in voor de verzameling > **identiteit** voor het verplaatsen van verzamelingen. U kunt de resource ook opnieuw toevoegen in **resources toevoegen**, wat hetzelfde doet.  
Het abonnement is verplaatst naar een andere Tenant. | Schakel de beheerde identiteit voor de verzameling verplaatsen uit en vervolgens in.

### <a name="how-can-i-do-multiple-moves-together"></a>Hoe kan ik meerdere verplaatsen tegelijk uitvoeren?

Wijzig zo nodig de bron-en doel combinaties met de optie wijzigen in de portal.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](about-move-process.md) over Resource Mover-onderdelen en het verplaatsingsproces.
