---
title: DevTest Labs-concepten | Microsoft Docs
description: Meer informatie over de basis concepten van DevTest Labs en hoe u Azure virtual machines eenvoudig kunt maken, beheren en bewaken
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92327855"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-concepten
## <a name="overview"></a>Overzicht
De volgende lijst bevat de belangrijkste concepten en definities van Key DevTest Labs:

## <a name="labs"></a>Labs
Een Lab is de infra structuur die een groep resources omvat, zoals Virtual Machines (Vm's), waarmee u deze resources beter kunt beheren door limieten en quota's op te geven.

## <a name="virtual-machine"></a>Virtuele machine
Een Azure-VM is een van de verschillende soorten [schaal bare computer bronnen op aanvraag](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Met Azure Vm's beschikt u over de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en onderhouden, hoewel u de virtuele machine nog steeds moet onderhouden door bepaalde taken uit te voeren, zoals het configureren, patchen en installeren van de software die erop wordt uitgevoerd.

[Overzicht van virtuele Windows-machines in azure](../virtual-machines/windows/overview.md) geeft u informatie over wat u moet overwegen voordat u een virtuele machine maakt, hoe u deze maakt en hoe u deze beheert.

## <a name="claimable-vm"></a>Claim bare VM
Een Azure-claim bare VM is een virtuele machine die beschikbaar is voor gebruik door een test gebruiker met machtigingen. Een test beheerder kan Vm's voorbereiden met specifieke basis installatie kopieën en artefacten en deze opslaan in een gedeelde groep. Een test gebruiker kan vervolgens een werkende VM op basis van de pool claimen wanneer ze deze nodig hebben met die specifieke configuratie.

Een virtuele machine die claimbaar is, wordt in eerste instantie niet toegewezen aan een bepaalde gebruiker, maar wordt weer gegeven in de lijst van elke gebruiker onder ' claim bare virtuele machines '. Nadat een virtuele machine door een gebruiker is geclaimd, wordt deze naar het gebied mijn virtuele machines verplaatst en is deze niet langer claimbaar voor een andere gebruiker.

## <a name="environment"></a>Omgeving
In DevTest Labs verwijst een omgeving naar een verzameling Azure-resources in een lab. In [dit blog bericht](./devtest-lab-faq.md#blog-post) wordt beschreven hoe u multi-VM-omgevingen maakt op basis van uw Azure Resource Manager sjablonen.

## <a name="base-images"></a>Basisinstallatiekopieën
Basis kopieën zijn VM-installatie kopieën met alle hulpprogram ma's en instellingen die vooraf zijn geïnstalleerd en geconfigureerd om snel een virtuele machine te maken. U kunt een virtuele machine inrichten door een bestaande basis te kiezen en een artefact toe te voegen om uw test agent te installeren. U kunt de ingerichte virtuele machine vervolgens als basis opslaan, zodat de basis kan worden gebruikt zonder dat de test agent opnieuw moet worden geïnstalleerd voor elke inrichting van de virtuele machine.

## <a name="artifacts"></a>Artifacts
Artefacten worden gebruikt voor het implementeren en configureren van uw toepassing nadat een virtuele machine is ingericht. Artefacten kunnen zijn:

* Hulpprogram ma's die u wilt installeren op de VM, zoals agents, Fiddler en Visual Studio.
* Acties die u wilt uitvoeren op de VM, zoals het klonen van een opslag plaats.
* Toepassingen die u wilt testen.

Artefacten worden [Azure Resource Manager](../azure-resource-manager/management/overview.md) json-bestanden die instructies bevatten voor het uitvoeren van implementatie en het Toep assen van de configuratie.

## <a name="artifact-repositories"></a>Artefact opslagplaatsen
Artefact opslagplaatsen zijn Git-opslag plaatsen waar artefacten worden ingecheckt. Artefact opslagplaatsen kunnen worden toegevoegd aan meerdere Labs in uw organisatie, waardoor hergebruik en delen mogelijk zijn.

## <a name="formulas"></a>Formules
Formules, naast basis installatie kopieën, bieden een mechanisme voor snelle VM-inrichting. Een formule in DevTest Labs is een lijst met standaard eigenschaps waarden die worden gebruikt voor het maken van een Lab-VM.
Met formules kunnen Vm's met dezelfde set eigenschappen, zoals basis installatie kopie, VM-grootte, virtueel netwerk en artefacten, worden gemaakt zonder dat ze elke keer deze eigenschappen moeten opgeven. Wanneer u een virtuele machine maakt op basis van een formule, kunnen de standaard waarden worden gebruikt als-is of gewijzigd.

## <a name="policies"></a>Beleidsregels
Met beleids regels kunt u de kosten in uw Lab beheren. U kunt bijvoorbeeld een beleid maken om Vm's automatisch af te sluiten op basis van een gedefinieerd schema.

## <a name="caps"></a>Petjes
Caps is een mechanisme voor het minimaliseren van afval in uw Lab. U kunt bijvoorbeeld een cap instellen om het aantal Vm's te beperken dat per gebruiker of in een Lab kan worden gemaakt.

## <a name="security-levels"></a>Beveiligingsniveaus
Beveiligings toegang wordt bepaald door Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Om te begrijpen hoe Access werkt, is het handig om te begrijpen wat de verschillen zijn tussen een machtiging, een rol en een bereik zoals gedefinieerd door Azure RBAC.

* Machtiging: een machtiging is een gedefinieerde toegang tot een specifieke actie (bijvoorbeeld lees toegang tot alle virtuele machines).
* Rol: een rol is een set machtigingen die kan worden gegroepeerd en toegewezen aan een gebruiker. De rol van *abonnements eigenaar* heeft bijvoorbeeld toegang tot alle resources in een abonnement.
* Bereik: een bereik is een niveau in de hiërarchie van een Azure-resource, zoals een resource groep, één Lab of het hele abonnement.

Binnen het bereik van DevTest Labs zijn er twee soorten rollen om gebruikers machtigingen te definiëren: Lab-eigenaar en Lab-gebruiker.

* Eigenaar van het lab-een Lab-eigenaar heeft toegang tot alle resources in het lab. Daarom kan een eigenaar van het lab beleids regels wijzigen, Vm's lezen en schrijven, het virtuele netwerk wijzigen, enzovoort.
* Lab-gebruiker: een Lab-gebruiker kan alle Lab-bronnen weer geven, zoals Vm's, beleids regels en virtuele netwerken, maar kunnen geen beleids regels of Vm's wijzigen die door andere gebruikers zijn gemaakt.

Raadpleeg het artikel, [gebruikers machtigingen verlenen aan specifieke Lab-beleids regels](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)voor meer informatie over het maken van aangepaste rollen in DevTest Labs.

Aangezien de scopes hiërarchisch zijn, worden deze machtigingen automatisch verleend wanneer een gebruiker machtigingen heeft voor een bepaald bereik. dit bereik is ook toegestaan op elke lagere bereik. Als een gebruiker bijvoorbeeld is toegewezen aan de rol van eigenaar van het abonnement, hebben ze toegang tot alle resources in een abonnement, die alle virtuele machines, alle virtuele netwerken en alle Labs omvat. Daarom neemt de eigenaar van het abonnement automatisch de rol van de Lab-eigenaar over. Het tegenovergestelde is echter niet waar. Een Lab-eigenaar heeft toegang tot een lab. Dit is een lager bereik dan het abonnements niveau. Daarom kan een Lab-eigenaar geen virtuele machines of virtuele netwerken of bronnen die zich buiten het lab bevinden, zien.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen
Alle concepten die in dit artikel worden besproken, kunnen worden geconfigureerd met behulp van Azure Resource Manager sjablonen, waarmee u de infra structuur/configuratie van uw Azure-oplossing kunt definiëren en deze herhaaldelijk in een consistente status implementeert.

[Inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md#template-format) beschrijft de structuur van een Azure Resource Manager sjabloon en de eigenschappen die beschikbaar zijn in de verschillende secties van een sjabloon.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
[Een lab maken in DevTest Labs](devtest-lab-create-lab.md)
