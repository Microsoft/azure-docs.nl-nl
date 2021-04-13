---
title: Azure Automanage veelgestelde vragen over virtuele machines
description: Antwoorden op veelgestelde vragen over Azure Automanage virtuele machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363356"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Veelgestelde vragen over Azure Automanage voor VM's

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over [Azure Automanage voor virtuele machines.](automanage-virtual-machines.md)

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteuning voor Azure verzenden. Als u een ondersteuningsaanvraag wilt indienen, selecteert [ondersteuning voor Azure](https://azure.microsoft.com/support/options/)ondersteuning **krijgen op de pagina**.


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage voor virtuele machines

**Wat zijn alle vereisten voor het inschakelen van Azure Automanage?**

Hier volgen de vereisten voor het inschakelen van Azure Automanage:
- Ondersteunde [Versies van Windows Server en](automanage-windows-server.md#supported-windows-server-versions) [Linux-distributies](automanage-linux.md#supported-linux-distributions-and-versions)
- VM's moeten zich in een ondersteunde regio
- De gebruiker moet de juiste machtigingen hebben
- Alleen niet-schaalset-VM's
- Automanage biedt op dit moment geen ondersteuning voor Sandbox-abonnementen

**Welke Azure RBAC-machtiging is nodig om automatisch te kunnen worden uitgevoerd?**

Als u Automatisch beheer inschakelen op een VM met een bestaand Automanage-account, hebt u de rol Inzender nodig voor de resourcegroep waarin de VM zich bevindt.

Als u een nieuw Automanage-account gebruikt bij het inschakelen, moet u de rol Eigenaar of Inzender + Gebruikerstoegangbeheerder hebben voor het abonnement.


**Welke regio's worden ondersteund?**

De volledige lijst met ondersteunde regio's is hier [beschikbaar.](./automanage-virtual-machines.md#supported-regions)


**Welke mogelijkheden worden Azure Automanage automatiseren?**

Automanage registreert, configureert en bewaakt gedurende de levenscyclus van de VM de services die hier worden [vermeld.](automanage-virtual-machines.md)

**Werkt Azure Automanage met Azure Arc-VM's?**

Automanage biedt momenteel geen ondersteuning voor voor Arc ingeschakelde VM's.

**Kan ik configuraties op Azure Automanage?**

Klanten kunnen instellingen aanpassen voor specifieke services, zoals Azure Backup, via configuratievoorkeuren. Zie onze documentatie hier voor een volledige lijst met instellingen die kunnen worden [gewijzigd.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Werkt Azure Automanage met linux- en Windows-VM's?**

Ja, zie de ondersteunde [Versies van Windows Server](automanage-windows-server.md#supported-windows-server-versions) en [Linux-distributies.](automanage-linux.md#supported-linux-distributions-and-versions)


**Kan ik Automanage selectief toepassen op alleen een set VM's?**

Automanage kan worden ingeschakeld met klik- en wijs eenvoud op geselecteerde nieuwe en bestaande VM's. Automanage kan ook op elk moment worden uitgeschakeld.


**Ondersteunt Azure Automanage VM's in een virtuele-machineschaalset?**

Nee, Azure Automanage biedt momenteel geen ondersteuning voor VM's in een virtuele-machineschaalset.


**Wat kost Azure Automanage?**

Azure Automanage is gratis beschikbaar in de openbare preview. Gekoppelde Azure-resources, zoals Azure Backup, brengen kosten met zich mee.


**Kan ik Automanage toepassen via Azure-beleid?**

Ja, we hebben een ingebouwd beleid dat automatisch wordt toegepast op alle VM's binnen uw gedefinieerde bereik. U geeft ook de omgevingsconfiguratie (DevTest of Production) op, samen met uw Automanage-account. Meer informatie over het inschakelen van Automanage via Azure-beleid kunt u [hier vinden.](virtual-machines-policy-enable.md)


**Wat is een Automanage-account?**

Het Automanage-account is een MSI (Managed Service Identity) die de beveiligingscontext of de identiteit biedt waaronder de geautomatiseerde bewerkingen plaatsvinden.


**Is het van invloed op eventuele extra VM's naast de VM('s) die ik heb geselecteerd bij het inschakelen van Automanage?**

Als uw VM is gekoppeld aan een bestaande Log Analytics-werkruimte, gebruiken we die werkruimte opnieuw om deze oplossingen toe te passen: Wijzigingen bijhouden, Inventaris en Updatebeheer. Voor alle VM's die met die werkruimte zijn verbonden, zijn deze oplossingen ingeschakeld.


**Kan ik de omgeving van mijn VM wijzigen?**

Op dit moment moet u Automanage uitschakelen voor die VM en vervolgens Automatischmanage opnieuw inschakelen met de gewenste omgeving en voorkeuren.


**Als mijn VM al is geconfigureerd voor een service, zoals Updatebeheer, configureert Automanage deze dan opnieuw?**
Nee, automanage wordt niet opnieuw geconfigureerd. We beginnen met het controleren van de resources die aan die service zijn gekoppeld op drift.


**Waarom heeft mijn VM de status Mislukt in de Portal voor automatisch beheren?**

Als u de status Mislukt *ziet,* kunt u op verschillende manieren problemen met de implementatie oplossen:
* Ga naar **Resourcegroepen,** selecteer uw resourcegroep, klik op **Implementaties** en bekijk de *status* Mislukt, samen met foutdetails.
* Ga naar **Abonnementen,** selecteer uw resourcegroep, klik op **Implementaties** en bekijk de *status* Mislukt, samen met foutdetails.
* U kunt ook het activiteitenlogboek van een VM bezoeken, dat een vermelding bevat voor 'Configuratieprofieltoewijzingen maken of bijwerken'. Dit kan ook meer informatie bevatten over uw implementatie.

**Hoe krijg ik ondersteuning voor probleemoplossing voor Automanage?**

U kunt een ticket voor [een technische ondersteuningscase indienen.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Voor de **optie Service** zoekt en selecteert u *Automanage* in de *sectie Bewaking en* beheer.


## <a name="next-steps"></a>Volgende stappen

Probeer Automanage in teschakelen voor virtuele machines in Azure Portal.

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)