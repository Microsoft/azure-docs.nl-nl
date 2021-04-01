---
title: Toepassings migratie en integratie in Azure DevTest Labs
description: Dit artikel bevat richt lijnen voor de governance van Azure DevTest Labs-infra structuur in de context van toepassings migratie en-integratie.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87289354"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governance van Azure DevTest Labs-infra structuur-toepassings migratie en-integratie
Als uw ontwikkel-en test omgeving eenmaal tot stand is gebracht, moet u rekening houden met de volgende vragen:

- Hoe gebruikt u de omgeving in uw project team?
- Hoe zorgt u ervoor dat u de vereiste organisatie beleidsregels volgt en de flexibiliteit houdt om waarde toe te voegen aan uw toepassing?

## <a name="azure-marketplace-images-vs-custom-images"></a>Installatie kopieën van Azure Marketplace versus aangepaste installatie kopieën

### <a name="question"></a>Vraag
Wanneer moet ik een Azure Marketplace-installatie kopie gebruiken versus mijn eigen aangepaste organisatie-installatie kopie?

### <a name="answer"></a>Antwoord
Azure Marketplace moet standaard worden gebruikt, tenzij u specifieke problemen of organisatorische vereisten hebt. Enkele algemene voor beelden zijn:

- Installatie van complexe software waarvoor een toepassing moet worden opgenomen als onderdeel van de basis installatie kopie.
- Installatie en installatie van een toepassing kan veel uur duren, wat geen efficiënt gebruik van reken tijd kan worden toegevoegd aan een Azure Marketplace-installatie kopie.
- Ontwikkel aars en testers hebben snel toegang tot een virtuele machine nodig en willen de instel tijd van een nieuwe virtuele machine minimaliseren.
- Nalevings-of regelgeving (bijvoorbeeld beveiligings beleid) die voor alle machines aanwezig moet zijn.

Het gebruik van aangepaste installatie kopieën mag niet lichter worden beschouwd. Ze introduceren extra complexiteit, omdat u nu VHD-bestanden voor die onderliggende basis installatie kopieën moet beheren. U moet deze basis installatie kopieën ook regel matig patchen met software-updates. Deze updates omvatten nieuwe besturings systemen (OS)-updates en eventuele updates of configuratie wijzigingen die nodig zijn voor het software pakket zelf.

## <a name="formula-vs-custom-image"></a>Formule versus aangepaste afbeelding

### <a name="question"></a>Vraag
Wanneer moet ik een formule versus aangepaste afbeelding gebruiken?

### <a name="answer"></a>Antwoord
De beslissings factor in dit scenario is doorgaans kosten en hergebruik.

Als u een scenario hebt waarbij veel gebruikers/Labs een installatie kopie met veel software boven op de basis installatie kopie vereisen, kunt u de kosten verlagen door een aangepaste installatie kopie te maken. Dit betekent dat de installatie kopie één keer wordt gemaakt. Het vermindert de instel tijd van de virtuele machine en de kosten die zijn gemaakt door de virtuele machine die wordt uitgevoerd wanneer de installatie plaatsvindt.

Een extra te noteren factor is echter de frequentie van wijzigingen in uw software pakket. Als u dagelijks bouwt en vereist dat de software op de virtuele machines van uw gebruikers wordt uitgevoerd, kunt u overwegen een formule te gebruiken in plaats van een aangepaste installatie kopie.

## <a name="use-custom-organizational-images"></a>Aangepaste organisatie-installatie kopieën gebruiken

### <a name="question"></a>Vraag
Hoe kan ik een eenvoudig herhaalbaar proces instellen om mijn aangepaste organisatie-installatie kopieën naar een DevTest Labs-omgeving te brengen?

### <a name="answer"></a>Antwoord
Bekijk [deze video over het patroon van de afbeeldings fabriek](./devtest-lab-faq.md#blog-post). Dit scenario is een geavanceerd scenario en de meegeleverde scripts zijn alleen voorbeeld scripts. Als er wijzigingen zijn vereist, moet u de scripts die in uw omgeving worden gebruikt, beheren en onderhouden.

DevTest Labs gebruiken om een aangepaste afbeeldings pijplijn in azure-pijp lijnen te maken:

- [Inleiding: Vm's in een paar minuten voorbereiden door een installatie kopie-Factory in te stellen in Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Image Factory: deel 2! Azure-pijp lijnen en het fabrieks omgeving instellen om Vm's te maken](./devtest-lab-faq.md#blog-post)
- [Image Factory – deel 3: aangepaste installatie kopieën opslaan en naar meerdere lessen distribueren](./devtest-lab-faq.md#blog-post)
- [Video: aangepaste installatie kopie fabriek met Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Patronen voor het instellen van de netwerk configuratie

### <a name="question"></a>Vraag
Hoe kan ik ervoor te zorgen dat de ontwikkelings-en test-virtuele machines het open bare Internet niet kunnen bereiken? Zijn er aanbevolen patronen voor het instellen van de netwerk configuratie?

### <a name="answer"></a>Antwoord
Ja. Er zijn twee aspecten die u kunt overwegen: inkomend en uitgaand verkeer.

**Inkomend verkeer** : als de virtuele machine geen openbaar IP-adres heeft, kan het niet worden bereikt door Internet. Een veelvoorkomende aanpak is om ervoor te zorgen dat er een beleid op abonnements niveau is ingesteld, zodat geen enkele gebruiker een openbaar IP-adres kan maken.

**Uitgaand verkeer** : als u wilt voor komen dat virtuele machines rechtstreeks naar het open bare Internet gaan en verkeer via een bedrijfs firewall forceren, kunt u verkeer on-premises via Express route of VPN routeren met behulp van geforceerde route ring.

> [!NOTE]
> Als u een proxy server hebt die verkeer blokkeert zonder proxy instellingen, vergeet dan niet om uitzonde ringen toe te voegen aan het account voor artefact opslag van het lab.

U kunt ook netwerk beveiligings groepen gebruiken voor virtuele machines of subnetten. Deze stap voegt een extra beveiligingslaag toe om verkeer toe te staan of te blok keren.

## <a name="new-vs-existing-virtual-network"></a>Nieuw versus bestaand virtueel netwerk

### <a name="question"></a>Vraag
Wanneer moet ik een nieuw virtueel netwerk maken voor mijn DevTest Labs-omgeving versus een bestaand virtueel netwerk gebruiken?

### <a name="answer"></a>Antwoord
Als uw Vm's moeten communiceren met de bestaande infra structuur, kunt u overwegen om een bestaand virtueel netwerk in uw DevTest Labs-omgeving te gebruiken. Als u ExpressRoute gebruikt, kunt u bovendien de hoeveelheid VNets/subnetten minimaliseren, zodat u de IP-adres ruimte die wordt toegewezen voor gebruik in de abonnementen niet hoeft te fragmenteren. U moet ook rekening houden met het VNet-peering-patroon (hub-spoke model). Deze aanpak maakt vnet/subnet-communicatie mogelijk tussen abonnementen binnen een bepaalde regio hoewel peering tussen regio's een up-to-me-functie in azure-netwerken is.

Anders kan elke DevTest Labs-omgeving een eigen virtueel netwerk hebben. Houd er echter rekening mee dat er [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md) gelden voor het aantal virtuele netwerken per abonnement. De standaard waarde is 50, maar deze limiet kan worden verhoogd naar 100.

## <a name="shared-public-or-private-ip"></a>Gedeeld, openbaar of privé IP-adres

### <a name="question"></a>Vraag
Wanneer moet ik een gedeeld IP-adres versus een openbaar IP-adres versus particulier IP-adres gebruiken?

### <a name="answer"></a>Antwoord
Als u een site-naar-site-VPN of Express route gebruikt, kunt u het gebruik van privé IP-adressen overwegen, zodat uw computers toegankelijk zijn via uw interne netwerk en niet toegankelijk zijn via het open bare Internet.

> [!NOTE]
> Lab-eigen aars kunnen dit subnetbeleid wijzigen om ervoor te zorgen dat niemand per ongeluk open bare IP-adressen voor hun virtuele machines maakt. De eigenaar van het abonnement moet een abonnements beleid maken voor komen dat open bare IP-adressen worden gemaakt.

Wanneer u gedeelde open bare Ip's gebruikt, delen de virtuele machines in een lab een openbaar IP-adres. Deze aanpak kan handig zijn wanneer u wilt voor komen dat de limieten voor open bare IP-adressen voor een bepaald abonnement worden geschonden.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limieten van aantal virtuele machines per gebruiker of Lab

### <a name="question"></a>Vraag
Is er een regel voor het aantal virtuele machines dat ik per gebruiker of per Lab moet instellen?

### <a name="answer"></a>Antwoord
Bij het overwegen van het aantal virtuele machines per gebruiker of per Lab zijn er drie belang rijke problemen:

- De **totale kosten** die het team kan best Eden aan resources in het lab. Het is eenvoudig om meerdere machines op te zetten. Om de kosten te beheren, is het één mechanisme om het aantal Vm's per gebruiker en/of per Lab te beperken
- Het totale aantal virtuele machines in een lab wordt beïnvloed door de beschik bare [abonnements niveau quota's](../azure-resource-manager/management/azure-subscription-service-limits.md) . Een van de hoogste limieten is 800 resource groepen per abonnement. DevTest Labs maakt momenteel een nieuwe resource groep voor elke VM (tenzij gedeelde open bare Ip's worden gebruikt). Als er 10 Labs in een abonnement is, zouden Labs ongeveer 79 virtuele machines in elk lab passen (800 hoogste limiet – 10 resource groepen voor de 10 Labs zelf) = 79 virtuele machines per Lab.
- Als het lab is verbonden met on-premises via een Express route (bijvoorbeeld), zijn er **gedefinieerde IP-adres ruimten beschikbaar** voor het VNet/subnet. Om ervoor te zorgen dat de virtuele machines in het lab niet worden gemaakt (fout: kan IP-adres niet ophalen), kunnen Lab-eigen aars de maximale Vm's per Lab opgeven, uitgelijnd met de beschik bare IP-adres ruimte.

## <a name="use-resource-manager-templates"></a>Resource Manager-sjablonen gebruiken

### <a name="question"></a>Vraag
Hoe kan Ik Resource Manager-sjablonen in mijn DevTest Labs-omgeving gebruiken?

### <a name="answer"></a>Antwoord
U implementeert uw Resource Manager-sjablonen in een DevTest Labs-omgeving met behulp van de stappen die worden beschreven in de [functie omgevingen in DevTest Labs](devtest-lab-test-env.md) -artikel. In principe controleert u uw Resource Manager-sjablonen in een Git-opslag plaats (Azure opslag plaatsen of GitHub) en voegt u een [privé-opslag plaats voor uw sjablonen](devtest-lab-test-env.md) toe aan het lab.

Dit scenario is mogelijk niet nuttig als u DevTest Labs gebruikt voor het hosten van ontwikkel machines, maar kan handig zijn als u een faserings omgeving bouwt, die representatief is voor productie.

Het is ook een goed idee dat het aantal virtuele machines per Lab of per gebruiker alleen het aantal machines dat systeem eigen is gemaakt in het lab zelf, en niet door een wille keurige omgeving (Resource Manager-sjablonen) beperkt.

## <a name="next-steps"></a>Volgende stappen
Zie [omgevingen gebruiken in DevTest Labs](devtest-lab-test-env.md).
