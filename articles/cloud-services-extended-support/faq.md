---
title: Veelgestelde vragen over Azure Cloud Services (uitgebreide ondersteuning)
description: Veelgestelde vragen over Azure Cloud Services (uitgebreide ondersteuning)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9cac6cdd8e68af77b611c89e8b62e6f8d8845fd0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107513"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Veelgestelde vragen over Azure Cloud Services (uitgebreide ondersteuning)
In dit artikel vindt u informatie over veelgestelde vragen met betrekking tot Azure Cloud Services (uitgebreide ondersteuning).

## <a name="general"></a>Algemeen

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Wat is de resource naam voor Cloud Services (klassiek) & Cloud Services (uitgebreide ondersteuning)?
- Cloud Services (klassiek): `microsoft.classiccompute/domainnames`
- Cloud Services (uitgebreide ondersteuning): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Welke locaties zijn er beschikbaar voor de implementatie van Cloud Services (uitgebreide ondersteuning)?
Cloud Services (uitgebreide ondersteuning) is beschikbaar in alle open bare Cloud regio's.

### <a name="how-does-my-quota-change"></a>Hoe wordt mijn quotum gewijzigd? 
Klanten moeten quota aanvragen met behulp van dezelfde processen als andere Azure Resource Manager product. Het quotum in Azure Resource Manager is regionaal en er is een afzonderlijke quotum aanvraag vereist voor elke regio.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Waarom zie ik geen productie & staging-sleuf meer?
Cloud Services (uitgebreide ondersteuning) biedt geen ondersteuning voor het logische concept van een gehoste service, die twee sleuven (productie & fase) omvat. Elke implementatie is een onafhankelijke implementatie van de Cloud service (uitgebreide ondersteuning). Als u een nieuwe release van een Cloud service wilt testen en faseren, implementeert u een Cloud service (uitgebreide ondersteuning) en kunt u deze als VIP-verwisselbaar labelen met een andere Cloud service (uitgebreide ondersteuning)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Waarom kan ik geen lege Cloud service meer maken?
Het concept van gehoste service namen bestaat niet meer. u kunt geen lege Cloud service maken (uitgebreide ondersteuning).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Ondersteunt Cloud Services (uitgebreide ondersteuning) Resource Health controle (RHC)?
Nee, Cloud Services (uitgebreide ondersteuning) biedt geen ondersteuning voor Resource Health Check (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>Hoe worden metrische waarden van rolinstantie gewijzigd?
Er zijn geen wijzigingen in de metrische gegevens van het Role-exemplaar. 

### <a name="how-are-web--worker-roles-changing"></a>Hoe veranderen web & werk rollen?
Er zijn geen wijzigingen in het ontwerp, de architectuur of de onderdelen van de web-en werk rollen. 

### <a name="how-are-role-instances-changing"></a>Hoe worden rolinstanties gewijzigd?
Er zijn geen wijzigingen in het ontwerp, de architectuur of de onderdelen van de rolinstanties. 

### <a name="how-will-guest-os-updates-change"></a>Hoe worden updates voor het gast besturingssysteem gewijzigd?
 Er zijn geen wijzigingen in de implementatie methode. Cloud Services (klassiek) en Cloud Services (uitgebreide ondersteuning) krijgen dezelfde updates.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Ondersteunt Cloud Services (uitgebreide ondersteuning) niet-toegewezen en gestopte status toewijzingen?

De implementatie van Cloud Services (uitgebreide ondersteuning) ondersteunt alleen de status ' gestopt toegewezen ' die wordt weer gegeven als ' gestopt ' in de Azure Portal. De status stopped-deallocated wordt niet ondersteund. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>De implementaties van Cloud Services (uitgebreide ondersteuning) ondersteunen schalen in clusters, beschikbaarheids zones en regio's?
Implementaties van Cloud Services (uitgebreide ondersteuning) kunnen niet worden geschaald over meerdere clusters, beschikbaarheids zones en regio's. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Zijn er prijs verschillen tussen Cloud Services (klassiek) en Cloud Services (uitgebreide ondersteuning)?
Cloud Services (uitgebreide ondersteuning) maakt gebruik van de open bare IP-adressen Azure Key Vault en Basic (ARM).Klanten die certificaten vereisen, moeten Azure Key Vault gebruiken voor certificaat beheer ([meer informatie](https://azure.microsoft.com/pricing/details/key-vault/) over Azure Key Vault prijzen.)   Elk openbaar IP-adres voor Cloud Services (uitgebreide ondersteuning) wordt afzonderlijk in rekening gebracht (meer[informatie](https://azure.microsoft.com/pricing/details/ip-addresses/) over open bare IP-adres prijzen.) 
## <a name="resources"></a>Resources 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Welke resources die zijn gekoppeld aan een Cloud Services-implementatie (uitgebreide ondersteunings), moeten zich in dezelfde resource groep bevinden?
Load balancers, netwerk beveiligings groepen en route tabellen moeten in dezelfde regio en resource groep wonen. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Welke resources die zijn gekoppeld aan een Cloud Services-implementatie (uitgebreide ondersteuning), moeten zich in dezelfde regio bevinden?
Key Vault, virtueel netwerk, open bare IP-adressen, netwerk beveiligings groepen en route tabellen moeten zich in dezelfde regio bevinden.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Welke resources die zijn gekoppeld aan een Cloud Services-implementatie (uitgebreide ondersteuning), moeten in hetzelfde virtuele netwerk actief zijn?
Open bare IP-adressen, load balancers, netwerk beveiligings groepen en route tabellen moeten in hetzelfde virtuele netwerk actief zijn. 

## <a name="deployment-files"></a>Implementatiebestanden 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Hoe kan ik een sjabloon gebruiken voor het implementeren of beheren van mijn implementatie?
Sjabloon-en parameter bestanden kunnen worden door gegeven als een para meter met behulp van REST, Power shell en CLI. Ze kunnen ook worden geüpload met behulp van de Azure Portal.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Moet ik vier bestanden nu onderhouden? (sjabloon, para meter, csdef, cscfg)
Sjabloon-en parameter bestanden worden alleen gebruikt voor implementatie automatisering. Net als Cloud Services (klassiek) kunt u eerst hand matig afhankelijke resources maken en vervolgens een Cloud Services (uitgebreide ondersteuning) implementeren met behulp van Power shell, CLI-opdrachten of via de portal met bestaande csdef, cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Hoe wordt mijn toepassings code gewijzigd op Cloud Services (uitgebreide ondersteuning)
Er zijn geen wijzigingen vereist voor de toepassings code die in cspkg is verpakt. Uw bestaande toepassingen blijven werken zoals voorheen. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Staat Cloud Services (uitgebreide ondersteuning) CTP-pakket indeling toe?
De indeling van het CTP-pakket wordt niet ondersteund in Cloud Services (uitgebreide ondersteuning). Hiermee kan echter een maximale pakket grootte van 800 MB worden bereikt

## <a name="migration"></a>Migratie

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Wordt Cloud Services (uitgebreide ondersteuning) de storingen door toewijzings fouten beperken?
Nee, de implementaties van Cloud Services (uitgebreide ondersteuning) zijn gekoppeld aan een cluster zoals Cloud Services (klassiek). Daarom blijven toewijzings fouten bestaan als het cluster vol is. 

### <a name="when-do-i-need-to-migrate"></a>Wanneer moet ik migreren? 
Het schatten van de tijd die nodig is en de complexiteits migratie is afhankelijk van een bereik van variabelen. Planning is de meest efficiënte stap om inzicht te krijgen in de reik wijdte van het werk, de blok keringen en de complexiteit van de migratie.

## <a name="networking"></a>Netwerken 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Waarom kan ik geen implementatie maken zonder virtueel netwerk?
Virtuele netwerken zijn een vereiste bron voor elke implementatie op Azure Resource Manager. De implementatie van Cloud Services (uitgebreide ondersteuning) moet binnen een virtueel netwerk wonen. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Waarom krijg ik nu zoveel netwerk bronnen te zien? 
In Azure Resource Manager worden de onderdelen van de implementatie van uw Cloud Services (uitgebreide ondersteuning) weer gegeven als resource voor betere zicht baarheid en betere controle. Hetzelfde type resources is gebruikt in Cloud Services (klassiek), maar ze werden gewoon verborgen. Een voor beeld van een dergelijke resource is de open bare Load Balancer. Dit is nu een expliciete ' alleen-lezen ' resource die automatisch wordt gemaakt door het platform

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Welke beperkingen gelden voor een subnet met een eigen Cloud Services (uitgebreide ondersteuning)?
Een subnet met Cloud Services-implementaties (uitgebreide ondersteuning) kan niet worden gedeeld met implementaties van andere compute-producten, zoals Virtual Machines, Virtual Machines schaal sets, Service Fabric, enzovoort.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Welke IP-toewijzings methoden worden ondersteund in Cloud Services (uitgebreide ondersteuning)?
Cloud Services (uitgebreide ondersteuning) ondersteunt dynamische & statische IP-toewijzings methoden. In het cscfg-bestand wordt verwezen naar vaste IP-adressen als gereserveerde Ip's.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Waarom worden er kosten in rekening gebracht voor IP-adressen?
Klanten worden gefactureerd voor het gebruik van IP-adressen op Cloud Services (uitgebreide ondersteuning), net zoals gebruikers worden gefactureerd voor IP-adres dat is gekoppeld aan virtuele machines. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Kan ik een DNS-naam met Cloud Services (uitgebreide ondersteuning) gebruiken? 
Ja. Er kan ook een DNS-naam worden opgegeven voor Cloud Services (uitgebreide ondersteuning). Met Azure Resource Manager is het DNS-label een optionele eigenschap van het open bare IP-adres dat is toegewezen aan de Cloud service. De indeling van de DNS-naam voor implementaties op basis van Azure Resource Manager is `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>Kan ik de verwijzing naar het virtuele netwerk voor een bestaande Cloud service bijwerken of wijzigen (uitgebreide ondersteuning)? 
Nee. Verwijzing van virtueel netwerk is verplicht tijdens het maken van een Cloud service. Voor een bestaande Cloud service kan de verwijzing naar het virtuele netwerk niet worden gewijzigd. De adres ruimte van het virtuele netwerk zelf kan worden gewijzigd met behulp van VNet-Api's. 

## <a name="certificates--key-vault"></a>Certificaten & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Waarom moet ik mijn certificaten beheren op Cloud Services (uitgebreide ondersteuning)?
Cloud Services (uitgebreide ondersteuning) heeft hetzelfde proces als andere compute-aanbiedingen aangenomen waarbij certificaten zich bevinden in door de klant beheerde sleutel kluizen. Hierdoor kunnen klanten volledige controle hebben over hun geheimen & certificaten. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Kan ik een Key Vault voor al mijn implementaties in alle regio's gebruiken?
Nee. Key Vault is een regionale resource en klanten hebben één Key Vault in elke regio nodig. Er kan echter één Key Vault worden gebruikt voor alle implementaties binnen een bepaalde regio.

## <a name="next-steps"></a>Volgende stappen
Als u Cloud Services wilt gaan gebruiken (uitgebreide ondersteuning), raadpleegt u [een Cloud service (uitgebreide ondersteuning) implementeren met behulp van Power shell](deploy-powershell.md)
