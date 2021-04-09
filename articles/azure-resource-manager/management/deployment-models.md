---
title: Resource Manager en klassieke implementatie
description: Lees hier wat de verschillen zijn tussen het implementatiemodel van Resource Manager en het klassieke implementatiemodel (of Service Management).
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: da260db84de0cc8bd092eb95491b7cc38df93a12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185841"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager vergeleken met klassieke implementatie: implementatiemodellen en de status van uw resources begrijpen

> [!NOTE]
> De informatie in dit artikel wordt alleen gebruikt wanneer u een migratie van de klassieke implementatie naar de Azure Resource Manager-implementatie uitvoert.

In dit artikel vindt u informatie over Azure Resource Manager en het klassieke implementatiemodel. Het implementatiemodel van Resource Manager en het klassieke implementatiemodel zijn twee verschillende manieren voor het implementeren en beheren van uw Azure-oplossingen. U gebruikt de modellen via twee verschillende API-sets, en de geïmplementeerde resources kunnen sterk afwijken. De twee modellen zijn niet met elkaar compatibel. In dit artikel worden deze verschillen beschreven.

Om de implementatie en het beheer van resources te vereenvoudigen, wordt aangeraden om Resource Manager te gebruiken voor alle nieuwe resources. Indien mogelijk is het ook raadzaam om bestaande resources opnieuw te implementeren via Resource Manager.

Als u geen ervaring hebt met Resource Manager, kunt u eerst de terminologie bekijken die is gedefinieerd in het [Azure Resource Manager overzicht](overview.md).

## <a name="history-of-the-deployment-models"></a>Geschiedenis van de implementatiemodellen

In Azure was in eerste instantie alleen het klassieke implementatiemodel beschikbaar. In dit model waren alle resources zelfstandig en er was geen enkele manier om gerelateerde resources te groeperen. In plaats daarvan moest u handmatig bijhouden welke resources in uw oplossing of toepassing werden gebruikt en niet vergeten om de resources op een gecoördineerde manier te beheren. Als u een oplossing wilde implementeren, moest u elke resource afzonderlijk maken via de portal of een script schrijven waarmee alle bronnen in de juiste volgorde werden geïmplementeerd. Als u een oplossing wilde verwijderen, moest u elke resource afzonderlijk verwijderen. U kunt geen toegangs beheer beleid voor gerelateerde resources Toep assen en bijwerken. Ten slotte kunt u geen tags Toep assen op resources om ze te labelen met voor waarden die u helpen bij het bewaken van uw resources en het beheren van facturering.

In 2014 werd Azure Resource Manager geïntroduceerd, en daarmee het concept van resourcegroepen. Een resourcegroep is een container voor resources die een gemeenschappelijke levenscyclus delen. Het implementatiemodel van Resource Manager biedt diverse voordelen:

* U kunt alle services voor uw oplossing als een groep implementeren, beheren en bewaken, in plaats van deze services afzonderlijk te verwerken.
* U kunt de oplossing herhaaldelijk implementeren gedurende de levenscyclus en erop vertrouwen dat uw resources op een consistente manier worden geïmplementeerd.
* U kunt toegangsbeheer toepassen op alle resources in de resourcegroep. Deze beleidsregels worden automatisch toegepast wanneer nieuwe resources worden toegevoegd aan de resourcegroep.
* U kunt tags toepassen op de resources om alle resources in uw abonnement op een logische manier te organiseren.
* U kunt JSON (JavaScript Object Notation) gebruiken voor het definiëren van de infrastructuur voor uw oplossing. Het JSON-bestand is in feite de Resource Manager-sjabloon.
* U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

Op het moment dat Resource Manager werd toegevoegd, werden alle resources met terugwerkende kracht toegevoegd aan standaardresourcegroepen. Als u nu een resource maakt via de klassieke implementatie, wordt de resource automatisch gemaakt in een standaard resource groep voor die service, ook al hebt u die resource groep niet opgegeven tijdens de implementatie. Maar alleen bestaande in een resource groep betekent echter niet dat de resource is geconverteerd naar het Resource Manager-model.

## <a name="understand-support-for-the-models"></a>Ondersteuning voor de modellen begrijpen

Er zijn drie mogelijke scenario's:

1. Cloud Services biedt geen ondersteuning voor het Resource Manager-implementatie model.
2. Virtuele machines, opslagaccounts en virtuele netwerken ondersteunen zowel het implementatiemodel van Resource Manager als het klassieke implementatiemodel.
3. Alle andere Azure-services ondersteunen Resource Manager.

Als de resource is gemaakt via de klassieke implementatie, geldt voor virtuele machines, opslagaccounts en virtuele netwerken dat u alleen klassieke bewerkingen kunt uitvoeren op de resource. Als de virtuele machine, het opslagaccount of het virtuele netwerk is gemaakt via Resource Manager, moet u alleen Resource Manager-bewerkingen gebruiken. Dit verschil kan problemen geven wanneer uw abonnement een combinatie van resources bevat die zijn gemaakt via Resource Manager en de klassieke implementatie. Deze combi natie van resources kan onverwachte resultaten opleveren omdat de resources niet dezelfde bewerkingen ondersteunen.

In sommige gevallen kunt u met een opdracht van Resource Manager gegevens ophalen van een resource die via de klassieke implementatie is gemaakt of een beheertaak uitvoeren zoals het verplaatsen van een klassieke resource naar een andere resourcegroep. Maar deze gevallen mogen niet de indruk geven dat het type Resource Manager-bewerkingen ondersteunt. Stel dat u een resourcegroep hebt met daarin een virtuele machine die is gemaakt met het klassieke implementatiemodel. Als u de volgende PowerShell-opdracht van Resource Manager uitvoert:

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

wordt deze virtuele machine geretourneerd:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Met de cmdlet **Get-AzVM** van Resource Manager worden echter alleen virtuele machines geretourneerd die zijn geïmplementeerd via Resource Manager. Met de volgende opdracht wordt niet de virtuele machine geretourneerd die is gemaakt met de klassieke implementatie.

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Tags worden alleen ondersteund door resources die via Resource Manager zijn gemaakt. U kunt geen tags Toep assen op klassieke resources.

## <a name="changes-for-compute-network-and-storage"></a>Wijzigingen voor reken-, netwerk- en opslagresources

In het volgende diagram ziet u de reken-, netwerk- en opslagresources die zijn geïmplementeerd via Resource Manager.

![Architectuur van Resource Manager](./media/deployment-models/arm_arch3.png)

SRP: opslag Resource provider, CRP: Compute resource provider, NRP: Network resource provider

Dit zijn de verschillende relaties tussen de resources:

* Alle resources bestaan binnen een resourcegroep.
* De virtuele machine is afhankelijk van een specifiek opslagaccount dat is gedefinieerd in de Storage-resourceprovider (SRP) voor het opslaan van schijven in blob-opslag (vereist).
* De virtuele machine verwijst naar een specifieke netwerk interface kaart die is gedefinieerd in de netwerk resource provider (vereist) en een beschikbaarheidsset die is gedefinieerd in de compute resource provider (optioneel).
* De netwerk interface kaart verwijst naar het toegewezen IP-adres van de virtuele machine (vereist), het subnet van het virtuele netwerk voor de virtuele machine (vereist) en aan een netwerk beveiligings groep (optioneel).
* Het subnet binnen een virtueel netwerk verwijst naar een netwerkbeveiligingsgroep (optioneel).
* Het load balancer exemplaar verwijst naar de back-end-groep met IP-adressen die de netwerk interface kaart van een virtuele machine bevatten (optioneel) en verwijst naar een load balancer openbaar of privé IP-adres (optioneel).

Dit zijn de onderdelen en hun relaties voor een klassieke implementatie:

![klassieke architectuur](./media/deployment-models/arm_arch1.png)

De klassieke oplossing voor het hosten van een virtuele machine omvat:

* Een vereiste cloudservice die als een container fungeert voor het hosten van virtuele machines (rekentaken). Virtuele machines worden automatisch voorzien van een netwerk interface kaart en een IP-adres dat is toegewezen door Azure. Bovendien bevat de cloudservice een instantie van een externe load balancer, een openbaar IP-adres en standaardeindpunten om verkeer van Extern bureaublad en extern PowerShell-verkeer toe te staan voor virtuele Windows-machines en SSH-verkeer (Secure Shell) voor virtuele Linux-machines.
* Een vereist opslag account voor het opslaan van de virtuele harde schijven voor een virtuele machine, met inbegrip van het besturings systeem, tijdelijke en aanvullende gegevens schijven (opslag).
* Een optioneel virtueel netwerk dat fungeert als een extra container, waarin u een subnet-structuur kunt maken en het subnet moet kiezen waarop de virtuele machine zich bevindt (netwerk).

In de volgende tabel wordt beschreven wat er is veranderd aan de interactie tussen de resourceproviders voor Compute, Network en Storage:

| Item | Klassiek | Resource Manager |
| --- | --- | --- |
| Cloudservice voor virtuele machines |Cloudservice was een container voor de opslag van de virtuele machines waarvoor de beschikbaarheid van het platform en taakverdeling vereist was. |Cloudservice is niet langer een object dat is vereist voor het maken van een virtuele machine met het nieuwe model. |
| Virtuele netwerken |Een virtueel netwerk is optioneel voor de virtuele machine. Indien opgenomen, kan het virtuele netwerk niet worden geïmplementeerd met Resource Manager. |Een virtuele machine vereist een virtueel netwerk dat is geïmplementeerd met Resource Manager. |
| Storage Accounts |De virtuele machine vereist een opslag account voor het opslaan van de virtuele harde schijven voor het besturings systeem, tijdelijke en aanvullende gegevens schijven. |De virtuele machine vereist een opslagaccount voor het opslaan van de schijven in de blob-opslag. |
| Beschikbaarheidssets |De beschikbaarheid van het platform werd aangegeven door de configuratie van dezelfde "AvailabilitySetName" op de virtuele machines. Het maximumaantal foutdomeinen was 2. |Beschikbaarheidsset is een resource die beschikbaar wordt gesteld door Microsoft.Compute-provider. Virtuele machines die uiterst beschikbaar moeten zijn, worden opgenomen in de beschikbaarheidsset. Het maximumaantal foutdomeinen is nu 3. |
| Affiniteitsgroepen |Voor het maken van virtuele netwerken waren affiniteitsgroepen vereist. Met de introductie van regionale virtuele netwerken is dit echter niet meer vereist. |Het concept van affiniteitsgroepen bestaat niet meer in de API's die via Azure Resource Manager beschikbaar worden gesteld. |
| Taakverdeling |Bij het maken van een cloudservice wordt een impliciete load balancer voor de geïmplementeerde virtuele machines aangemaakt. |De load balancer is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De primaire netwerkinterface van de virtuele machines waarvoor de taken moeten worden verdeeld moet verwijzen naar de load balancer. Load balancers kunnen intern of extern zijn. Een instantie van de load balancer verwijst naar de back-endpool van IP-adressen met daarin de NIC van een virtuele machine (optioneel) en het openbare of particuliere IP-adres (optioneel) van een load balancer. |
| Virtueel IP-adres |Cloud Services krijgt een standaard-VIP (virtueel IP-adres) toegewezen wanneer een VM wordt toegevoegd aan een cloudservice. Het virtuele IP-adres is het adres dat is gekoppeld aan de impliciete load balancer. |Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. Dynamische openbare IP-adressen kunnen worden toegewezen aan een load balancer. Openbare IP-adressen kunnen worden beveiligd met beveiligingsgroepen. |
| Gereserveerd IP-adres |U kunt een IP-adres in Azure reserveren en dit koppelen aan een cloudservice om ervoor te zorgen dat het IP-adres is vergrendeld. |Een openbaar IP-adres kan in de statische modus worden gemaakt en biedt dezelfde mogelijkheden als een gereserveerd IP-adres. |
| Openbaar IP-adres (PIP) per VM |Openbare IP-adressen kunnen ook rechtstreeks aan een VM worden gekoppeld. |Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. |
| Eindpunten |Invoereindpunten moesten eerder op een virtuele machine worden geconfigureerd voor open connectiviteit voor bepaalde poorten. Een van de algemene modi voor het maken van verbinding met virtuele machines wordt gerealiseerd door het instellen van invoereindpunten. |Inkomende NAT-regels kunnen op load balancers worden geconfigureerd om eindpunten in te schakelen op bepaalde poorten voor verbinding met de VM's. |
| DNS-naam |Een cloudservice krijgt een impliciete, globaal unieke DNS-naam. Bijvoorbeeld: `mycoffeeshop.cloudapp.net`. |DNS-namen zijn optionele parameters die voor de resource van een openbaar IP-adres kunnen worden opgegeven. De FQDN heeft de volgende notatie: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Netwerkinterfaces |Primaire en secundaire netwerkinterface en de bijbehorende eigenschappen werden gedefinieerd als de netwerkconfiguratie van een virtuele machine. |De netwerkinterface is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De levens cyclus van de netwerk interface is niet gekoppeld aan een virtuele machine. Een NIC verwijst naar het toegewezen IP-adres van de virtuele machine (vereist), het subnet van het virtuele netwerk voor de virtuele machine (vereist) en een netwerkbeveiligingsgroep (optioneel). |

Zie [Verschillende implementatiemodellen gebruiken om vanuit de portal verbinding te maken met virtuele netwerken](../../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) om te lezen hoe u met behulp van verschillende implementatiemodellen verbinding kunt maken met virtuele netwerken.

## <a name="migrate-from-classic-to-resource-manager"></a>Migreren van klassiek naar Resource Manager

Als u klaar bent om uw resources te migreren van de klassieke implementatie naar de Resource Manager-implementatie, raadpleegt u:

1. [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-deep-dive.md)
2. [Platformondersteunde migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md)
3. [IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure PowerShell](../../virtual-machines/migration-classic-resource-manager-ps.md)
4. [IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure CLI](../../virtual-machines/migration-classic-resource-manager-cli.md)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Kan ik een virtuele machine maken met Resource Manager voor implementatie in een virtueel netwerk dat is gemaakt met behulp van het klassieke implementatiemodel?**

Deze configuratie wordt niet ondersteund. U kunt Resource Manager niet gebruiken om een virtuele machine te implementeren in een virtueel netwerk dat is gemaakt met de klassieke implementatie.

**Kan ik met Resource Manager een virtuele machine maken van een gebruikersinstallatiekopie die is gemaakt met het klassieke implementatiemodel?**

Deze configuratie wordt niet ondersteund. U kunt de virtuele harde-schijf bestanden echter kopiëren van een opslag account dat is gemaakt met behulp van het klassieke implementatie model en ze toevoegen aan een nieuw account dat is gemaakt via Resource Manager.

**Wat zijn de gevolgen voor het quotum voor mijn abonnement?**

De quota voor de virtuele machines, virtuele netwerken en opslagaccounts die zijn gemaakt met behulp van Azure Resource Manager zijn gescheiden van andere quota. Elk abonnement krijgt quota voor het maken van de resources met behulp van de nieuwe API's. U vindt [hier](../../azure-resource-manager/management/azure-subscription-service-limits.md) meer informatie over de extra quota.

**Kan ik mijn geautomatiseerde scripts voor het inrichten van virtuele machines, virtuele netwerken en opslagaccounts via de API's van Azure Resource Manager blijven gebruiken?**

Alle automatisering en scripts die u hebt gemaakt, blijven werken voor de bestaande virtuele machines en virtuele netwerken die zijn gemaakt in de modus Azure Service Management. De scripts moeten echter worden bijgewerkt voor het gebruik van het nieuwe schema voor het maken van dezelfde resources via de modus Resource Manager.

**Waar vind ik voorbeelden van Azure Resource Manager-sjablonen?**

In [Azure Resource Manager Quick](https://azure.microsoft.com/documentation/templates/)start-sjablonen vindt u een uitgebreide set met starter sjablonen.

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../templates/deploy-powershell.md) voor informatie over de opdrachten voor het implementeren van een sjabloon.