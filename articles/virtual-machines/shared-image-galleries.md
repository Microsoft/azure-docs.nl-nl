---
title: VM-installatiekopieën delen met gedeelde galerieën
description: Meer informatie over het gebruik van gemeen schappelijke afbeeldings galerieën voor het delen van Linux VM-installatie kopieën in uw organisatie.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 4156308fa58ea1f02e6e3f2c5174839dc1cd381c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841445"
---
# <a name="shared-image-galleries-overview"></a>Galerieën met gedeelde installatiekopieën (overzicht)

Shared Image Gallery is een service waarmee u structuur en organisatie kunt bouwen rond uw installatiekopieën. Galerieën met gedeelde installatiekopieën bieden:

- Wereldwijde replicatie van installatiekopieën.
- Versiebeheer en groepering van installatiekopieën voor eenvoudiger beheer.
- Maximaal beschikbare installatiekopieën met zone-redundante opslag (ZRS)-accounts in regio’s die beschikbaarheidszones ondersteunen. ZRS biedt betere flexibiliteit tegen zonegebonden fouten.
- Ondersteuning voor Premium-opslag (Premium_LRS).
- U kunt delen tussen meerdere abonnementen en zelfs tussen Active Directory (AD)-tenants met behulp van RBAC.
- Het schalen van uw implementaties met replica's van installatiekopieën in elke regio.

Met behulp van Shared Image Gallery kunt u uw installatiekopieën delen met verschillende gebruikers, service-principals of AD-groepen binnen uw organisatie. Gedeelde installatiekopieën kunnen worden gerepliceerd naar meerdere regio's, zodat u uw implementaties sneller kunt schalen.

Een installatiekopie is een kopie van een volledige virtuele machine (VM) (inclusief eventuele gekoppelde gegevensschijven) of alleen van de schijf voor het besturingssysteem, afhankelijk van hoe deze is gemaakt. Wanneer u een virtuele machine maakt op basis van de installatiekopie, wordt een kopie van de virtuele harde schijven (VHD's) in de installatiekopie gebruikt om de schijven voor de nieuwe virtuele machine te maken. De installatiekopie blijft in de opslag aanwezig en kan telkens opnieuw worden gebruikt voor het maken van nieuwe VM's.

Als u een groot aantal installatiekopieën wilt onderhouden en beschikbaar wilt maken voor het hele bedrijf, kunt u een Shared Image Gallery als opslagplaats gebruiken. 

De functie Shared Image Gallery heeft meerdere resourcetypen:

| Resource | Beschrijving|
|----------|------------|
| **Bron van installatiekopie** | Dit is een resource die kan worden gebruikt om een **versie van een installatiekopie** in een galerie met installatiekopieën te maken. Een bron van een installatiekopie kan een bestaande VM van Azure zijn die [gegeneraliseerd of gespecialiseerd](#generalized-and-specialized-images) is, een beheerde installatiekopie, een momentopname, een VHD of een versie van een installatiekopie in een andere galerie met installatiekopieën. |
| **Galerie met installatiekopieën** | Net als Azure Marketplace is een **galerie met installatiekopieën** een opslagplaats voor het beheren en delen van installatiekopieën, maar u bepaalt zelf wie er toegang heeft. |
| **Definitie van installatiekopie** | Definities van installatiekopieën worden in een galerie gemaakt en bevatten informatie over de installatiekopie en de vereisten voor intern gebruik. Dit houdt ook in of het om een Windows- of Linux-installatiekopie gaat. Daarnaast bevat de definitie releaseopmerkingen en de minimale en maximale geheugenvereisten. Het is een definitie van een type installatiekopie. |
| **Versie van installatiekopie** | U gebruikt een **versie van een installatiekopie** om een VM te maken wanneer u een galerie gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Net als bij een beheerde installatiekopie, wanneer u een **versie van een installatiekopie** gebruikt om een VM te maken, wordt de versie van de installatiekopie gebruikt voor het maken van nieuwe schijven voor de VM. Versies van installatiekopieën kunnen meerdere keren worden gebruikt. |

<br>

![Afbeelding waarop u kunt zien hoe u meerdere versies van een installatiekopie in uw galerie kunt hebben](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definities van installatiekopieën

Definities van installatiekopieën vormen een logische groepering voor versies van een installatiekopie. De definitie van de installatiekopie bevat informatie over waarom de installatiekopie is gemaakt, voor welk besturingssysteem de kopie wordt gebruikt en andere informatie over het gebruik ervan. Een definitie van een installatiekopie lijkt op een plan met alle details voor het maken van een specifieke installatiekopie. U implementeert geen VM op basis van een definitie van een installatiekopie, maar van versies van installatiekopieën die zijn gemaakt op basis van de definitie.

Er zijn drie parameters voor elke definitie van de installatiekopie. Deze worden gecombineerd gebruikt: **Uitgever**, **Aanbieding** en **SKU**. Deze worden gebruikt om een specifieke definitie van een installatiekopie te vinden. Er zijn versies van installatiekopieën die een of twee parameters gemeenschappelijk hebben, maar niet alle drie.  Hier ziet u drie voorbeelden van definities van installatiekopieën en de bijbehorende waarden:

|Definitie van installatiekopie|Uitgever|Aanbieding|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testen|Finance|Front-end|

Deze hebben alle drie een unieke reeks waarden. De indeling is vergelijkbaar met de manier waarop u momenteel uitgever, aanbieding en SKU kunt opgeven voor [Azure Marketplace-installatiekopieën](./windows/cli-ps-findimage.md) in Azure PowerShell om de meest recente versie van een Marketplace-installatiekopie te verkrijgen. Elke definitie van een installatiekopie moet een unieke reeks van deze waarden bevatten.

De volgende parameters bepalen welke soorten installatiekopieversies ze kunnen bevatten:

- Status van het besturingssysteem: u kunt de status van het besturingssysteem instellen op [gegeneraliseerd of gespecialiseerd](#generalized-and-specialized-images). Dit veld is vereist.
- Besturingssysteem : dit kan Windows of Linux zijn. Dit veld is vereist.
-   Hyper-V-generatie: geef op of de installatiekopie is gemaakt op basis van een Hyper-V-VHD van generatie 1 of [generatie 2](generation-2.md). De standaardinstelling is generatie 1.


Hieronder ziet u andere parameters die voor de definitie van uw installatiekopie kunnen worden ingesteld, zodat u uw resources gemakkelijker kunt bijhouden:

- Beschrijving: gebruik een beschrijving om meer gedetailleerde informatie te geven over waarom er een definitie van de installatiekopie is. U kunt bijvoorbeeld een definitie van een installatiekopie hebben voor de front-endserver waarop de toepassing vooraf is geïnstalleerd.
- EULA: deze kan worden gebruikt om te verwijzen naar een gebruiksrechtovereenkomst die specifiek is voor de definitie van de installatiekopie.
- Privacyverklaring en opmerkingen bij de release: sla release opmerkingen bij de release en privacyverklaringen op in Azure Storage en geef bij de definitie een URI op om ze te kunnen openen.
- Datum einde levensduur: koppel een datum voor het einde van de levensduur aan de definitie van uw installatiekopie, zodat oude definities automatisch kunnen worden verwijderd.
- Tag: u kunt tags toevoegen wanneer u de definitie van de installatiekopie maakt. Zie [Tags gebruiken om uw esources te organiseren](../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags
- Aanbevelingen voor minimum- en maximumwaarden voor vCPU en geheugen: als uw installatiekopie aanbevelingen voor vCPU en geheugen bevat, kunt u die gegevens koppelen aan de definitie van uw installatiekopie.
- Niet-toegestane schijftypen: u kunt informatie opgeven over de opslagbehoeften voor uw VM. Als de installatiekopie bijvoorbeeld niet geschikt is voor standaard-HDD-schijven, voegt u deze toe aan de lijst met niet-toegestane schijftypen.
- Aankoopplaninformatie voor Marketplace-installatiekopieën: `-PurchasePlanPublisher`, `-PurchasePlanName` en `-PurchasePlanProduct`. Zie [Installatiekopieën zoeken in Azure Marketplace](./windows/cli-ps-findimage.md) en [Aankoopplaninformatie van Marketplace leveren bij het maken van installatiekopieën](marketplace-images.md) voor meer informatie over het aankoopplan.


## <a name="image-versions"></a>Installatiekopieversies

U gebruikt een **installatiekopieversie** om een VM te maken. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een **installatiekopieversie** gebruikt om een VM te maken, wordt de installatiekopieversie gebruikt voor het maken van nieuwe schijven voor de VM. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.

De eigenschappen van een installatiekopieversie zijn:

- Versienummer. Dit wordt gebruikt als de naam van de installatiekopieversie. Het heeft altijd deze indeling: MajorVersion.MinorVersion.Patch. Als u **nieuwste** opgeeft wanneer u een VM maakt, wordt de nieuwste installatiekopie gekozen op basis van de hoogste MajorVersion, gevolgd door MinorVersion, gevolgd door Patch. 
- Bron. De bron kan een VM, beheerde schijf, momentopname, beheerde installatiekopie of andere installatiekopieversie zijn. 
- Uitsluiten vanaf de nieuwste. U kunt voorkomen dat een versie als de nieuwste installatiekopieversie wordt gebruikt. 
- Datum einde levensduur. De datum waarna er geen VM's meer kunnen worden gemaakt van deze installatiekopie.


## <a name="generalized-and-specialized-images"></a>Gegeneraliseerde en gespecialiseerde installatiekopieën

Er zijn twee statussen van het besturingssysteem die door Shared Image Gallery worden ondersteund. Voor installatiekopieën geldt normaal gesproken dat de VM die voor het maken van de installatiekopie wordt gebruikt, is gegeneraliseerd voordat de installatiekopie wordt gebruikt. Generalisatie is een proces waarbij computer- en gebruikersspecifieke gegevens van de VM worden verwijderd. Voor Windows wordt het hulpprogramma Sysprep gebruikt. Voor Linux kunt u gebruikmaken van [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` of `-deprovision+user`-parameters.

Voor gespecialiseerde VM's is geen proces gevolgd om computerspecifieke gegevens en accounts te verwijderen. Daarnaast is aan VM's die op basis van gespecialiseerde installatiekopieën zijn gemaakt, geen `osProfile` gekoppeld. Dit betekent dat gespecialiseerde installatiekopieën naast enkele voordelen ook enkele beperkingen zullen hebben.

- VM's en schaalsets die op basis van gespecialiseerde installatiekopieën zijn gemaakt, kunnen sneller worden uitgevoerd. Aangezien ze zijn gemaakt op basis van een bron die al een keer is opgestart, worden VM's die van deze installatiekopieën zijn gemaakt, sneller opgestart.
- Accounts die kunnen worden gebruikt voor het aanmelden bij de VM, kunnen ook worden gebruikt voor een VM die is gemaakt met behulp van de gespecialiseerde installatiekopie die op basis van die VM is gemaakt.
- VM's hebben de **computernaam** van de VM waarmee de installatiekopie is gemaakt. Wijzig de computernaam om conflicten te voorkomen.
- Met `osProfile` wordt aangegeven hoe gevoelige informatie aan de VM wordt doorgegeven met behulp van `secrets`. Dit kan problemen veroorzaken bij het gebruik van Key Vault, WinRM en andere functionaliteiten die gebruikmaken van `secrets` in `osProfile`. In sommige gevallen kunt u identiteiten van beheerde services (MSI) gebruiken om deze beperkingen te omzeilen.

## <a name="regional-support"></a>Regionale ondersteuning

Alle openbare regio's kunnen doelregio's zijn, maar als u naar Australië - centraal en Australië - centraal 2 wilt repliceren, moet uw abonnement aan de lijst met toegestane regio's worden toegevoegd. Als u wilt aanvragen dat een abonnement wordt toegevoegd aan de lijst met toegestane regio's, gaat u naar: https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>Limieten 

Elk abonnement kent limieten voor het implementeren van resources met behulp van galerieën met gedeelde installatiekopieën:
- 100 galerieën met gedeelde installatiekopieën per abonnement per regio
- 1000 definities van installatiekopieën per abonnement per regio
- 10.000 versies van installatiekopieën per abonnement per regio
- 10 replica's van versies van installatiekopieën per abonnement per regio
- Schijven die aan de installatiekopie zijn gekoppeld, mogen niet groter zijn dan 1 TB

Zie [Resourcegebruik controleren op basis van limieten](../networking/check-usage-against-limits.md) voor voorbeelden voor het controleren van uw huidige gebruik.
 
## <a name="scaling"></a>Schalen
Met Shared Image Gallery kunt u het aantal replica's van de installatiekopieën opgeven dat in Azure moet worden bewaard. Dit komt van pas bij implementatiescenario's met meerdere VM's omdat de VM-implementaties over verschillende replica's kunnen worden gespreid. Hierdoor wordt de kans kleiner dat het maken van exemplaren wordt beperkt als gevolg van het overbelasten van één replica.

Met Shared Image Gallery kunt u nu maximaal 1000 VM-exemplaren implementeren in een virtuele-machineschaalset (vanaf 600 met beheerde installatiekopieën). Replica's van installatiekopieën verbeteren de prestaties van de implementatie, de betrouwbaarheid en de consistentie.   U kunt in elke doelregio een verschillend aantal replica's instellen, op basis van de schaalbehoeften voor de regio. Aangezien elke replica een kopie met gegevens van uw installatiekopie is, kunt u hiermee uw implementaties lineair schalen met elke extra replica. Twee installatiekopieën of regio's zijn uiteraard niet aan ekaar gelijk. Hieronder ziet u de algemene richtlijnen voor het gebruik van replica's in een regio:

- Voor implementaties van niet-virtuele-machine schaal sets: voor elke 20 Vm's die u gelijktijdig maakt, raden we u aan één replica te blijven gebruiken. Als u in een regio bijvoorbeeld gelijktijdig 120 VM's maakt met behulp van dezelfde installatiekopie, wordt u aangeraden minstens zes replica's van uw installatiekopie te bewaren. 
- Voor implementaties van virtuele-machine schaal sets: voor elke implementatie van een schaalset met Maxi maal 600 exemplaren, raden we u aan ten minste één replica te gebruiken. Als u in één regio bijvoorbeeld gelijktijdig vijf VM's - elk met 600 VM-exemplaren - maakt met behulp van dezelfde installatiekopie, wordt u aangeraden minstens vijf replica's van uw installatiekopie te bewaren. 

U wordt altijd aangeraden het voor meer replica's te zorgen vanwege factoren als afbeeldingsgrootte, inhoud en type besturingssysteem.

![Afbeelding waarin wordt getoond hoe u installatiekopieën kunt schalen](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Uw installatiekopieën maximaal beschikbaar maken

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) (Zone-redundante opslag (ZRS) van Azure) biedt tolerantie voor een fout in de beschikbaarheidszone in de regio. Dankzij de algemene beschikbaarheid van Shared Image Gallery kunt u ervoor kiezen uw installatiekopieën op te slaan in ZRS-accounts in regio's met beschikbaarheidszones. 

U kunt voor elk van de doelregio's ook het accounttype kiezen. Het standaardtype opslagaccount is Standard_LRS, maar u kunt Standard_ZRS kiezen voor regio's met beschikbaarheidszones. Controleer [hier](../storage/common/storage-redundancy.md) de regionale beschikbaarheid van ZRS.

![Afbeelding met ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicatie
Met Shared Image Gallery kunt u ook automatisch uw installatiekopieën naar andere Azure-regio's repliceren. Elke versie van een gedeelde installatiekopie kan worden gerepliceerd naar verschillende regio's, afhankelijk van wat voor uw organisatie zinvol is. U kunt bijvoorbeeld altijd de meest recente installatiekopie in meerdere regio's repliceren terwijl alle oudere versies slechts in één regio beschikbaar is. Hiermee kunt u besparen op opslagkosten voor versies van gedeelde installatiekopieën. 

De regio's waarheen een versie van de gedeelde installatiekopie wordt gerepliceerd, kunnen na het maakproces worden bijgewerkt. De tijd die nodig is voor replicatie naar verschillende regio's, is afhankelijk van de hoeveelheid gegevens die wordt gekopieerd en het aantal regio's waarnaar de versie wordt gerepliceerd. Dit kan in sommige gevallen enkele uren duren. Terwijl de replicatie plaatsvindt, kunt u de status van de replicatie per regio weergeven. Zodra de replicatie van de installatiekopie in een regio is voltooid, kunt u een virtuele machine of schaalset implementeren met behulp van de versie van de installatiekopie in de regio.

![Afbeelding waarin wordt getoond hoe u installatiekopieën kunt repliceren](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Aangezien Shared Image Gallery, de definitie en de versie van de installatiekopie allemaal resources zijn, kunnen ze worden gedeeld met behulp van de ingebouwde, systeemeigen Azure RBAC-besturingselementen. Met RBAC kunt u deze resources delen met andere gebruikers, service-principals en groepen. U kunt zelfs toegang delen met personen buiten de tenant waarin ze zijn gemaakt. Zodra een gebruiker toegang heeft tot de versie van de gedeelde installatiekopie, kan hij een virtuele machine of een virtuele-machineschaalset implementeren.  Dit is de matrix voor delen waarin u kunt zien waartoe de gebruiker toegang krijgt:

| Gedeeld met gebruiker     | Galerie met gedeelde installatiekopieën | Definitie van installatiekopie | Versie van installatiekopie |
|----------------------|----------------------|--------------|----------------------|
| Galerie met gedeelde installatiekopieën | Ja                  | Ja          | Ja                  |
| Definitie van installatiekopie     | Nee                   | Ja          | Ja                  |

Voor de beste ervaring wordt u aangeraden op galerieniveau te delen. Het is niet raadzaam om afzonderlijke versies van een installatiekopie te delen. Zie [Toegang tot Azure-resources beheren met behulp van RBAC](../role-based-access-control/role-assignments-portal.md) voor meer informatie over RBAC.

Installatiekopieën kunnen ook op schaal worden gedeeld, ook via tenants met behulp van een app-registratie voor meerdere tenants. Voor meer informatie over het delen van installatie kopieën tussen tenants, zie ' share galerie-VM-installatie kopieën delen in azure-tenants ' met behulp van [Azure cli](./linux/share-images-across-tenants.md) of [Power shell](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Billing
Er worden geen extra kosten in rekening gebracht voor het gebruik van de service Shared Image Gallery. Er worden kosten in rekening gebracht voor de volgende resources:
-   Opslagkosten voor het opslaan van elke replica. De opslagkosten worden in rekening gebracht volgens een momentopname en worden gebaseerd op de bezette grootte van de versie van de installatiekopie, het aantal replica's van de versie van de installatiekopie en het aantal regio's waarnaar de versie wordt gerepliceerd. 
-   Kosten van uitgaand netwerkverkeer voor replicatie van de eerste versie van de installatiekopie van de bronregio naar de gerepliceerde regio's. Navolgende replica's worden binnen de regio verwerkt, zodat er geen extra kosten in rekening worden gebracht. 

Stel dat u een installatiekopie hebt van een 127GB-besturingssysteemschijf, die maar 10 GB aan opslag beslaat, en één lege gegevensschijf van 32 GB. De bezette grootte van elke installatiekopie zou 10 GB zijn. De installatiekopie wordt gerepliceerd naar drie regio's en elke regio heeft twee replica's. Er zijn zes volledige momentopnamen die elk 10 GB gebruiken. De opslagkosten voor elke momentopname worden in rekening gebracht op basis van de bezette grootte van 10 GB. U betaalt netwerkkosten voor uitgaand verkeer voor de eerste replica die naar de twee extra regio's wordt gekopieerd. Raadpleeg de [prijsinformatie voor Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/) voor meer informatie over de prijzen van momentopnamen in elke regio. Raadpleeg de [prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/) voor meer informatie over uitgaand netwerkverkeer.


## <a name="updating-resources"></a>Bijwerken van resources

Nadat u de resources van de galerie met installatiekopieën hebt gemaakt, kunt u er enkele wijzigingen aan aanbrengen. Deze zijn beperkt tot:
 
Galerie met gedeelde installatiekopieën:
- Beschrijving

Definitie van installatiekopie:
- Aanbevolen vCPU's
- Aanbevolen geheugen
- Beschrijving
- Datum einde levensduur

Versie van installatiekopie:
- Aantal regionale replica's
- Doelregio's
- Uitsluiten vanaf de nieuwste
- Datum einde levensduur

## <a name="sdk-support"></a>SDK-ondersteuning

De volgende SDK's bieden ondersteuning voor het maken van galerieën met gedeelde installatiekopieën:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](/java/azure/?view=azure-java-stable)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](/azure/go/)

## <a name="templates"></a>Sjablonen

U kunt een Shared Image Gallery-resource maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

* [Hoe kan ik alle resources van Shared Image Gallery voor verschillende abonnementen weergeven?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Kan ik mijn bestaande installatiekopie verplaatsen naar de galerie met gedeelde installatiekopieën?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Kan ik een versie van een installatiekopie maken op basis van een gespecialiseerde schijf?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Kan ik de resource van Shared Image Gallery naar een ander abonnement verplaatsen nadat deze is gemaakt?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Kan ik mijn versies van de installatiekopieën in verschillende clouds repliceren, zoals Azure China 21Vianet of Azure Duitsland of Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Kan ik mijn versies van de installatiekopieën in verschillende abonnementen repliceren?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Kan ik versies van installatiekopieën in verschillende Azure AD-tenants delen?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Hoe lang duurt het om installatiekopieën in de verschillende doelregio's te repliceren?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Wat is het verschil tussen bronregio en doelregio?](#what-is-the-difference-between-source-region-and-target-region)
* [Hoe kan ik de bronregio opgeven tijdens het maken van de versie van de installatiekopie?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hoe kan ik het aantal replica's van de versie van de installatiekopie opgeven dat in elke regio moet worden gemaakt?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Kan ik de galerie met gedeelde installatiekopieën in een andere locatie maken dan in die voor de definitie van de installatiekopie en de versie van de installatiekopie?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Wat zijn de kosten voor het gebruik van Shared Image Gallery?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Welke API-versie moet ik gebruiken voor het maken van Shared Image Gallery en een definitie van een installatiekopie en een versie van een installatiekopie?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Welke API-versie moet ik gebruiken voor het maken van een gedeelde virtuele machine of een virtuele-machineschaalset op basis van de versie van de installatiekopie?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Kan ik mijn virtuele-machineschaalset bijwerken die met een beheerde installatiekopie is gemaakt, voor het gebruik van installatiekopieën uit Shared Image Gallery?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hoe kan ik alle resources van Shared Image Gallery voor verschillende abonnementen weergeven?

Volg de onderstaande stappen als u alle resources van Shared Image Gallery wilt vermelden in abonnementen waartoe u toegang hebt in Azure Portal:

1. Open [Azure Portal](https://portal.azure.com).
1. Schuif naar beneden op de pagina en selecteer **Alle resources**.
1. Selecteer alle abonnementen waaronder u alle resources wilt vermelden.
1. Zoek naar resources van het type **Galerie met gedeelde installatiekopieën**.
  
Als u alle resources van Shared Image Gallery wilt vermelden voor alle abonnementen waarvoor u machtigingen hebt, gebruikt u de volgende opdracht in de Azure CLI:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Zie **Galerieresources beheren** met behulp van de [Azure CLI](update-image-resources-cli.md) of [PowerShell](update-image-resources-powershell.md) voor meer informatie.

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Kan ik mijn bestaande installatiekopie verplaatsen naar de galerie met gedeelde installatiekopieën?
 
Ja. Er zijn drie scenario's die zijn gebaseerd op de typen installatiekopieën die u mogelijk hebt.

 Scenario 1: Als u een beheerde installatiekopie hebt, kunt u er een definitie van de installatiekopie en versie van de installatiekopie van maken. Zie **Een migratie uitvoeren van een beheerde installatiekopie naar een versie van een installatiekopie** met behulp van de [Azure CLI](image-version-managed-image-cli.md) of [PowerShell](image-version-managed-image-powershell.md) voor meer informatie.

 Scenario 2: Als u een niet-beheerde installatiekopie hebt, kunt u er een beheerde installatiekopie van maken en vervolgens definitie en een versie van de installatiekopie. 

 Scenario 3: Als u een VHD hebt in uw lokale bestandssysteem, moet u de VHD uploaden naar een beheerde installatiekopie. Vervolgens kunt u er een definitie van de installatiekopie en een versie van de installatiekopie van maken.

- Als de VHD van een Windows-VM is, raadpleegt u [Een VHD uploaden](./windows/upload-generalized-managed.md).
- Als de VHD van een Linux-VM is, raadpleegt u [Een VHD uploaden](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Kan ik een versie van een installatiekopie maken op basis van een gespecialiseerde schijf?

Ja, u kunt een virtuele machine maken op basis van een gespecialiseerde installatiekopie met behulp van de [CLI](vm-specialized-image-version-cli.md), [PowerShell](vm-specialized-image-version-powershell.md) of de API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Kan ik de resource van Shared Image Gallery naar een ander abonnement verplaatsen nadat deze is gemaakt?

Nee, u kunt de resource van Shared Image Gallery niet naar een ander abonnement verplaatsen. U kunt de versies van de installatiekopieën in de galerie repliceren naar andere regio's of een installatiekopie vanuit een andere galerie kopiëren met behulp van de [Azure CLI](image-version-another-gallery-cli.md) of [PowerShell](image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Kan ik mijn versies van de installatiekopieën in verschillende clouds repliceren, zoals Azure China 21Vianet of Azure Duitsland of Azure Government Cloud?

Nee, u kunt geen versies van de installatiekopie in verschillende clouds repliceren.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Kan ik mijn versies van de installatiekopieën in verschillende abonnementen repliceren?

Nee, u kunt de versies van de installatiekopie in de verschillende regio's van een abonnement repliceren en deze via RBAC in andere abonnementen gebruiken.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Kan ik versies van installatiekopieën in verschillende Azure AD-tenants delen? 

Ja, u kunt RBAC gebruiken om te delen met personen in verschillende tenants. Als u echter op schaal wilt delen, raadpleegt u Installatiekopieën van de galerie delen in verschillende Azure-tenants door middel van [PowerShell](./windows/share-images-across-tenants.md) of de [CLI](./linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Hoe lang duurt het om installatiekopieën in de verschillende doelregio's te repliceren?

De replicatietijd van versie van de installatiekopie is geheel afhankelijk van de grootte van de installatiekopie en het aantal regio's waarnaar deze wordt gerepliceerd. Als best practice kunt u echter het beste de installatiekopie klein houden en de bron- en doelregio's sluiten voor optimale resultaten. U kunt de status van de replicatie controleren met de vlag ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Wat is het verschil tussen bronregio en doelregio?

De bronregio is de regio waarin de versie van de installatiekopie wordt gemaakt; de doelregio's zijn de regio's waarin een kopie van uw versie van de installatiekopie wordt opgeslagen. Voor elke versie van de installatiekopie kunt u slechts één bronregio hebben. Zorg er ook voor dat u de locatie van de bronregio als een van de doelregio's doorgeeft wanneer u een versie van de installatiekopie maakt.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hoe kan ik de bronregio opgeven tijdens het maken van de versie van de installatiekopie?

Tijdens het maken van een versie van de installatiekopie, kunt u gebruikmaken van de tag **--location** in de CLI en van de tag **-Location** in PowerShell om de bronregio op te geven. Zorg ervoor dat de beheerde installatiekopie die u als basisinstallatiekopie gebruikt voor het maken van de versie van de installatiekopie, zich op dezelfde locatie bevindt als de locatie waar u de versie van de installatiekopie wilt maken. Zorg er ook voor dat u de locatie van de bronregio als een van de doelregio's doorgeeft wanneer u een versie van de installatiekopie maakt.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hoe kan ik het aantal replica's van de versie van de installatiekopie opgeven dat in elke regio moet worden gemaakt?

Er zijn twee manieren waarop u het aantal replica's van de versie van de installatiekopie kunt opgeven dat in elke regio moet worden gemaakt:
 
1. Het aantal regionale replica's, wat het aantal replica's aangeeft dat u per regio wilt maken. 
2. Het aantal normale replica's, wat het standaardaantal per regio is, in het geval dat het aantal regionale replica's niet wordt opgegeven. 

Als u het aantal regionale replica's wilt opgeven, geeft u de locatie door, samen met het aantal replica's dat u in die regio wilt maken: VS - zuid-centraal. 

Als het aantal replica's niet voor elke locatie is opgegeven, is het standaardaantal replica's het aantal normale replica's dat u hebt opgegeven. 

Als u het aantal algemene replica's in de CLI wilt opgeven, gebruikt u het argument **--replica-count** in de opdracht `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Kan ik de galerie met gedeelde installatiekopieën in een andere locatie maken dan in die voor de definitie van de installatiekopie en de versie van de installatiekopie?

Ja, dat is mogelijk. Maar als best practice raden we u aan de resourcegroep, de galerie met gedeelde installatiekopieën, de definitie van de installatiekopie en de versie van de installatiekopie op dezelfde locatie te laten staan.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Wat zijn de kosten voor het gebruik van Shared Image Gallery?

Er worden geen kosten in rekening gebracht voor het gebruik van Shared Image Gallery, met uitzondering van de opslagkosten voor het opslaan van de versies van de installatiekopie en de kosten van het uitgaand netwerkverkeer voor het repliceren van de versies van de installatiekopie van de bronregio naar de doelregio's.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Welke API-versie moet ik gebruiken voor het maken van Shared Image Gallery en een definitie van een installatiekopie en een versie van een installatiekopie?

Als u wilt werken met de galerieën met gedeelde installatiekopieën, definities van installatiekopieën en versies van installatiekopieën, wordt u aangeraden API-versie 2018-06-01 te gebruiken. Voor zone-redundante opslag (ZRS) is versie 2019-03-01 of hoger vereist.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Welke API-versie moet ik gebruiken voor het maken van een gedeelde virtuele machine of een virtuele-machineschaalset op basis van de versie van de installatiekopie?

Voor implementaties van virtuele machines en virtuele-machineschaalsets die gebruikmaken van een versie van een installatiekopie wordt u aangeraden API-versie 2018-04-01 of hoger te gebruiken.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Kan ik mijn virtuele-machineschaalset bijwerken die met een beheerde installatiekopie is gemaakt, voor het gebruik van installatiekopieën uit Shared Image Gallery?

Ja, u kunt de verwijzing naar de installatiekopie van de schaalset bijwerken van een beheerde installatiekopie naar een installatiekopie van een galerie met gedeelde installatiekopieën, op voorwaarde dat het type besturingssysteem, de Hyper-V-generatie en de indeling van de gegevensschijf van de installatiekopieën overeenkomen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van gedeelde installatie kopieën met behulp van [Azure cli](shared-images-cli.md) of [Power shell](shared-images-powershell.md).

