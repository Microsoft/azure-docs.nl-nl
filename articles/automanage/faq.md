---
title: Veelgestelde vragen over Azure automanage voor virtuele machines
description: Antwoorden op veelgestelde vragen over Azure automanage voor virtuele machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 2b15389ae35a63c71ac4cb9e167e578e7e1e690e
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695772"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Veelgestelde vragen over Azure automanage voor Vm's

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over [Azure automanage voor virtuele machines](automanage-virtual-machines.md).

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteunings aanvraag voor Azure indienen. Als u een ondersteunings aanvraag wilt indienen, selecteert u op de [pagina ondersteuning voor Azure](https://azure.microsoft.com/support/options/)de optie **ondersteuning ophalen**.


## <a name="azure-automanage-for-virtual-machines"></a>Azure automanage voor virtuele machines

**Wat zijn de vereisten die nodig zijn om Azure automanage in te scha kelen?**

Hier volgen de vereisten voor het inschakelen van Azure automanage:
- Alleen Windows Server Vm's
- Vm's moeten worden uitgevoerd
- Vm's moeten zich in een ondersteunde regio bevinden
- De gebruiker moet over de juiste machtigingen beschikken
- Alleen virtuele machines die geen schaal zijn ingesteld
- Automanage biedt momenteel geen ondersteuning voor sandbox-abonnementen

**Wat is Azure RBAC-machtiging nodig om automanage in te scha kelen?**

Als u automanage op een virtuele machine met een bestaand automanage-account inschakelt, moet u de rol Inzender hebben voor de resource groep waar de virtuele machine zich bevindt.

Als u een nieuw account voor automanage gebruikt wanneer u dit inschakelt, moet u de rol eigenaar hebben of de rol Inzender + gebruikers toegang hebben voor het abonnement.


**Welke regio's worden ondersteund?**

Virtuele machines in de volgende regio's worden ondersteund: Europa-west, VS-Oost, VS-West 2, Canada-centraal, VS-West-Centraal, Japan-Oost.


**Welke mogelijkheden automatiseert Azure automatisch beheren?**

Automatisch beheer registreert, configureert en bewaakt gedurende de levens cyclus van de virtuele machine die [hier](virtual-machines-best-practices.md)wordt vermeld.

**Werkt Azure automanage met virtuele machines met de Arc-functionaliteit van Azure?**

Automanage biedt momenteel geen ondersteuning voor virtuele machines met Arc-functionaliteit.

**Kan ik configuraties aanpassen voor Azure automanage?**

Klanten kunnen instellingen voor specifieke services, zoals Azure Backup retentie, aanpassen via configuratie voorkeuren. Zie [onze documentatie voor](virtual-machines-best-practices.md)een volledige lijst met instellingen die kunnen worden gewijzigd.


**Werkt Azure automanage met zowel Linux-als Windows-Vm's?**

Op dit moment ondersteunt automanage Windows Server Azure Vm's.


**Kan ik automanage selectief Toep assen op een verzameling Vm's?**

Automanage kan worden ingeschakeld met behulp van klikken en de eenvoud van de geselecteerde nieuwe en bestaande Vm's. Automanage kan ook op elk gewenst moment worden uitgeschakeld.


**Ondersteunt Azure automanage Vm's in een Schaalset voor virtuele machines?**

Nee, Azure automanage biedt momenteel geen ondersteuning voor Vm's in een Schaalset met virtuele machines.


**Wat kost Azure automanage?**

Azure automanage is gratis beschikbaar als open bare preview. Als er Azure-resources zijn gekoppeld, zoals Azure Backup, worden kosten in rekening gebracht.


**Kan ik automanage Toep assen via Azure Policy?**

Ja, er is een ingebouwd beleid voor automatisch beheer van alle virtuele machines binnen uw gedefinieerde bereik. U geeft ook het configuratie Profiel (DevTest of productie) samen met uw account voor automatisch beheren op. Meer informatie over het inschakelen van [het beleid voor](virtual-machines-policy-enable.md)autobeheren via Azure.


**Wat is een automanage-account?**

Het account voor automatisch beheer is een MSI-bestand (Managed Service Identity) dat de beveiligings context of de identiteit waarmee de geautomatiseerde bewerkingen worden uitgevoerd.


**Als u automanage inschakelt, heeft dit invloed op eventuele extra Vm's naast de virtuele machine (s) die ik heb geselecteerd?**

Als uw virtuele machine is gekoppeld aan een bestaande Log Analytics-werk ruimte, wordt die werk ruimte opnieuw gebruikt voor het Toep assen van deze oplossingen: Wijzigingen bijhouden, inventarisatie en Updatebeheer. Voor alle Vm's die zijn verbonden met deze werk ruimte, zijn deze oplossingen ingeschakeld.


**Kan ik het configuratie Profiel van mijn VM wijzigen?**

Op dit moment moet u automatisch beheer voor die virtuele machine uitschakelen en vervolgens automanage opnieuw inschakelen met het gewenste configuratie profiel en voor keuren.


**Als mijn virtuele machine al is geconfigureerd voor een service, zoals Updatebeheer, wordt deze dan automatisch beheerd opnieuw geconfigureerd?**
Nee, automatisch beheer wordt niet opnieuw geconfigureerd. We beginnen met het bewaken van de resources die zijn gekoppeld aan deze service voor drift.


**Waarom heeft mijn VM de status mislukt in de portal voor zelf beheer?**

Als u de status als *mislukt* ziet, kunt u problemen met de implementatie oplossen via de resource groep waar uw VM zich bevindt. Ga naar **resource groepen**, selecteer uw resource groep, klik op **implementaties** en Bekijk de status *mislukt* samen met de fout Details.

**Hoe kan ik probleemoplossings ondersteuning voor automanage krijgen?**

U kunt een [technisch ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)indienen. Voor de optie **service** zoekt en selecteert u in de sectie *bewaking en beheer* op *automanage* .


## <a name="next-steps"></a>Volgende stappen

Probeer automanage in te scha kelen voor virtuele machines in de Azure Portal.

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)