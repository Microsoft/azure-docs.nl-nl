---
title: Een Cloud service bijwerken (klassiek) | Microsoft Docs
description: Meer informatie over het bijwerken van Cloud Services in Azure. Meer informatie over hoe een update in een Cloud service wordt uitgevoerd om de beschik baarheid te garanderen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741108"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Een Azure-Cloud service bijwerken (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Het bijwerken van een Cloud service, met inbegrip van de rollen en het gast besturingssysteem, is een drie stappen procedure. Ten eerste moet u de binaire bestanden en configuratie voor de nieuwe Cloud service of versie van het besturings systeem uploaden. Vervolgens reserveert Azure computer-en netwerk bronnen voor de Cloud service op basis van de vereisten van de nieuwe versie van de Cloud service. Ten slotte voert Azure een rolling upgrade uit om de Tenant incrementeel bij te werken naar de nieuwe versie of het gast besturingssysteem, terwijl uw Beschik baarheid behouden blijft. In dit artikel worden de details van deze laatste stap beschreven: de rolling upgrade.

## <a name="update-an-azure-service"></a>Een Azure-service bijwerken
Met Azure worden uw rolinstanties ingedeeld in logische groepen met de naam upgrade domeinen (UD). Upgrade domeinen (UD) zijn logische sets van rolinstanties die als groep worden bijgewerkt.  Azure werkt een Cloud service één UD tegelijk bij, waardoor instanties in andere UDs geen verkeer meer kunnen verwerken.

Het standaard aantal upgrade domeinen is 5. U kunt een ander aantal upgrade domeinen opgeven door het kenmerk upgradeDomainCount in het definitie bestand (. csdef) van de service op te nemen. Zie [Azure Cloud Services definition schema (csdef-bestand)](./schema-csdef-file.md)voor meer informatie over het kenmerk upgradeDomainCount.

Wanneer u een in-place update van een of meer functies in uw service uitvoert, worden door Azure de sets rolinstanties bijgewerkt op basis van het upgrade domein waartoe ze behoren. Alle exemplaren in een opgegeven upgrade domein worden door Azure bijgewerkt. deze worden vervolgens gestopt, bijgewerkt, teruggezet, waarna ze weer online worden gezet. vervolgens gaat u naar het volgende domein. Als u alleen de exemplaren stopt die worden uitgevoerd in het huidige upgrade domein, zorgt Azure ervoor dat er een update wordt uitgevoerd met de minste mogelijke gevolgen voor de actieve service. Zie [hoe de update verloopt](#howanupgradeproceeds) verderop in dit artikel voor meer informatie.

> [!NOTE]
> Hoewel de termen **Update** en **upgrade** iets anders betekenis hebben in de context Azure, kunnen ze door elkaar worden gebruikt voor de processen en beschrijvingen van de functies in dit document.
>
>

In uw service moeten ten minste twee exemplaren van een rol worden gedefinieerd, zodat deze rol zonder downtime kan worden bijgewerkt. Als de service slechts één exemplaar van één rol omvat, is uw service niet beschikbaar totdat de in-place update is voltooid.

In dit onderwerp worden de volgende informatie over Azure-updates beschreven:

* [Toegestane service wijzigingen tijdens een update](#AllowedChanges)
* [Hoe een upgrade wordt uitgevoerd](#howanupgradeproceeds)
* [Het terugdraaien van een update](#RollbackofanUpdate)
* [Meerdere muteren-bewerkingen initiëren voor een doorlopende implementatie](#multiplemutatingoperations)
* [Distributie van rollen tussen upgrade domeinen](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Toegestane service wijzigingen tijdens een update
In de volgende tabel ziet u de wijzigingen die zijn toegestaan voor een service tijdens een update:

| Wijzigingen die zijn toegestaan voor hosting, services en rollen | In-place update | Gefaseerd (VIP swap) | Verwijderen en opnieuw implementeren |
| --- | --- | --- | --- |
| Versie van besturingssysteem |Ja |Ja |Ja |
| .NET-vertrouwens niveau |Ja |Ja |Ja |
| Grootte van virtuele machine<sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Instellingen voor lokale opslag |Meer dan<sup>2</sup> |Ja |Ja |
| Rollen toevoegen aan of verwijderen uit een service |Ja |Ja |Ja |
| Aantal exemplaren van een bepaalde rol |Ja |Ja |Ja |
| Aantal of type eind punten voor een service |Ja<sup>2</sup> |Nee |Ja |
| Namen en waarden van configuratie-instellingen |Ja |Ja |Ja |
| Waarden (maar geen namen) van configuratie-instellingen |Ja |Ja |Ja |
| Nieuwe certificaten toevoegen |Ja |Ja |Ja |
| Bestaande certificaten wijzigen |Ja |Ja |Ja |
| Nieuwe code implementeren |Ja |Ja |Ja |

<sup>1</sup> grootte wijziging is beperkt tot de subset van de beschik bare grootten voor de Cloud service.

<sup>2</sup> Azure SDK 1,5 of hoger is vereist.

> [!WARNING]
> Als u de grootte van de virtuele machine wijzigt, worden lokale gegevens vernietigd.
>
>

De volgende items worden niet ondersteund tijdens een update:

* De naam van een rol wijzigen. Verwijder de rol en voeg deze vervolgens toe met de nieuwe naam.
* Wijzigen van het aantal upgrade domeinen.
* De grootte van de lokale resources verlagen.

Als u andere updates wilt maken voor de definitie van uw service, zoals het verminderen van de grootte van de lokale resource, moet u in plaats daarvan een update voor VIP swap uitvoeren. Zie voor meer informatie [implementatie van swap](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hoe een upgrade wordt uitgevoerd
U kunt beslissen of u alle functies in uw service of één rol in de service wilt bijwerken. In beide gevallen worden alle exemplaren van elke rol die wordt bijgewerkt en behoren tot het eerste upgrade domein, gestopt, bijgewerkt en weer online gezet. Zodra deze weer online zijn, worden de exemplaren in het tweede upgrade domein gestopt, bijgewerkt en weer online gezet. Een Cloud service kan Maxi maal één upgrade tegelijk actief zijn. De upgrade wordt altijd uitgevoerd op basis van de nieuwste versie van de Cloud service.

In het volgende diagram ziet u hoe de upgrade wordt uitgevoerd als u alle functies in de service bijwerkt:

![Service upgraden](media/cloud-services-update-azure-service/IC345879.png "Service upgraden")

In dit volgende diagram ziet u hoe de update wordt voortgezet als u slechts één rol bijwerkt:

![Upgrade-rol](media/cloud-services-update-azure-service/IC345880.png "Upgrade-rol")  

Tijdens een automatische update evalueert de Azure Fabric-controller periodiek de status van de Cloud service om te bepalen wanneer het veilig is om de volgende UD te laten lopen. Deze status evaluatie wordt uitgevoerd per rol en beschouwt alleen instanties in de nieuwste versie (d.w.z. instanties van UDs die al werd uitgelegd hoe zijn). Er wordt gecontroleerd of een minimum aantal rolinstanties voor elke rol een bevredigende Terminal status heeft bereikt.

### <a name="role-instance-start-timeout"></a>Time-out voor starten van rolinstantie
De infrastructuur controller wacht 30 minuten voor elke rolinstantie om de status gestart te bereiken. Als de time-outperiode is verstreken, wordt de infrastructuur controller doorgegaan naar de volgende rolinstantie.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Gevolgen voor de stationsgegevens tijdens de Cloud service-upgrades

Wanneer u een service van één exemplaar bijwerkt naar meerdere instanties, wordt uw service uitgeschakeld terwijl de upgrade wordt uitgevoerd vanwege de manier waarop Azure Services upgradet. De service level agreement gegarandeerde service beschikbaarheid geldt alleen voor services die zijn geïmplementeerd met meer dan één exemplaar. In de volgende lijst wordt beschreven hoe de gegevens op elke schijf worden beïnvloed door elk Azure service-upgrade scenario:

|Scenario|Station C|D-station|E-station|
|--------|-------|-------|-------|
|VM opnieuw opstarten|Behouden|Behouden|Behouden|
|Portal opnieuw opstarten|Behouden|Behouden|Verbroken|
|Herinstallatie kopie van portal|Behouden|Verbroken|Verbroken|
|In-Place upgrade|Behouden|Behouden|Verbroken|
|Knooppunt migratie|Verbroken|Verbroken|Verbroken|

Houd er rekening mee dat in de bovenstaande lijst het station E: staat voor het hoofd station van de rol en niet moet worden vastgelegd. In plaats daarvan gebruikt u de omgevings variabele **% RoleRoot%** om het station aan te duiden.

Als u de uitval tijd wilt minimaliseren bij het upgraden van een service met één exemplaar, implementeert u een nieuwe service met meerdere exemplaren naar de staging-server en voert u een VIP-swap uit.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Het terugdraaien van een update
Azure biedt flexibiliteit bij het beheer van services tijdens een update door u toe te staan extra bewerkingen op een service te initiëren, nadat de eerste update aanvraag is geaccepteerd door de Azure Fabric-controller. Een terugdraai actie kan alleen worden uitgevoerd wanneer de status van een update (configuratie wijziging) of een upgrade wordt **uitgevoerd** voor de implementatie. Een update of upgrade wordt beschouwd als een onderhouds bewerking zolang er ten minste één exemplaar van de service is die nog niet is bijgewerkt naar de nieuwe versie. Als u wilt testen of een terugdraai bewerking is toegestaan, controleert u de waarde van de vlag RollbackAllowed, die wordt geretourneerd door [implementatie ophalen](/previous-versions/azure/reference/ee460804(v=azure.100)) en [Eigenschappen van Cloud service ophalen](/previous-versions/azure/reference/ee460806(v=azure.100)) , ingesteld op True.

> [!NOTE]
> Het is alleen zinvol om terugdraai actie aan te roepen voor een **in-place** update of upgrade, omdat bij het wisselen van de VIP een volledig actief exemplaar van uw service moet worden vervangen door een andere.
>
>

Het terugdraaien van een update die in voortgang is, heeft de volgende gevolgen voor de implementatie:

* Alle rolinstanties die nog niet zijn bijgewerkt of bijgewerkt naar de nieuwe versie, worden niet bijgewerkt of bijgewerkt, omdat deze instanties al de doel versie van de service uitvoeren.
* Alle rolinstanties die al zijn bijgewerkt of bijgewerkt naar de nieuwe versie van het bestand met het service pakket ( \* . cspkg) of het service configuratie \* bestand (. cscfg) (of beide bestanden), worden teruggezet naar de pre-upgrade versie van deze bestanden.

Deze functie wordt uitgevoerd met de volgende functies:

* De [terugdraai bewerking update of upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) , die kan worden aangeroepen in een configuratie-update (geactiveerd door het aanroepen van de [implementatie configuratie](/previous-versions/azure/reference/ee460809(v=azure.100))van de wijziging) of een upgrade (geactiveerd door het aanroepen van de [upgrade-implementatie](/previous-versions/azure/reference/ee460793(v=azure.100))), zolang er ten minste één instantie in de service is die nog niet is bijgewerkt naar de nieuwe versie.
* Het vergrendelde element en het RollbackAllowed-element, dat wordt geretourneerd als onderdeel van de antwoord tekst van de bewerking [implementatie ophalen](/previous-versions/azure/reference/ee460804(v=azure.100)) en [Eigenschappen van Cloud service ophalen](/previous-versions/azure/reference/ee460806(v=azure.100)) :

  1. Met het vergrendelde element kunt u detecteren wanneer een muteren-bewerking kan worden aangeroepen voor een bepaalde implementatie.
  2. Met het RollbackAllowed-element kunt u detecteren wanneer de [terugdraai update-of upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) bewerking kan worden aangeroepen voor een bepaalde implementatie.

  Als u een terugdraai actie wilt uitvoeren, hoeft u niet zowel de vergrendelde als de RollbackAllowed-elementen te controleren. Het is voldoende om te bevestigen dat RollbackAllowed is ingesteld op True. Deze elementen worden alleen geretourneerd als deze methoden worden aangeroepen met behulp van de aanvraag header ingesteld op x-MS-version: 2011-10-01 of een latere versie. Zie [Service Management versie beheer](/previous-versions/azure/gg592580(v=azure.100))voor meer informatie over het versie gebruik van headers.

Er zijn situaties waarin het terugdraaien van een update of upgrade niet wordt ondersteund. Dit zijn de volgende:

* Reductie van lokale resources: als de update de lokale resources voor een rol verhoogt, staat het Azure-platform niet toe dat wordt teruggedraaid.
* Quotum beperkingen: als de update een bewerking voor het omlaag schalen was, hebt u mogelijk niet langer voldoende reken quotum om de terugdraai bewerking te volt ooien. Aan elk Azure-abonnement is een quotum gekoppeld waarmee het maximum aantal kernen wordt opgegeven dat kan worden gebruikt door alle gehoste services die deel uitmaken van het abonnement. Als bij het terugdraaien van een bepaalde update het quotum wordt overschreden, wordt het terugdraaien niet ingeschakeld.
* Race condition: als de eerste update is voltooid, is een terugdraai bewerking niet mogelijk.

Een voor beeld van wanneer het terugdraaien van een update handig is, is als u de [implementatie](/previous-versions/azure/reference/ee460793(v=azure.100)) bewerking voor bijwerken in de hand matige modus gebruikt om de snelheid te bepalen waarmee een primaire in-place upgrade aan uw door Azure gehoste service wordt uitgerold.

Tijdens de implementatie van de upgrade roept u de [upgrade-implementatie](/previous-versions/azure/reference/ee460793(v=azure.100)) aan in de hand matige modus en begint u met het uitvoeren van upgrade domeinen. Als u op een bepaald moment de upgrade bewaakt, noteert u sommige rolinstanties in de eerste upgrade-domeinen die u onderzoekt niet meer reageert, kunt u de [terugdraai update-of upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) bewerking aanroepen voor de implementatie, waardoor de instanties die nog niet zijn bijgewerkt en terugdraaien van instanties die zijn bijgewerkt naar het vorige service pakket en de configuratie, ongewijzigd blijven.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Meerdere muteren-bewerkingen initiëren voor een doorlopende implementatie
In sommige gevallen wilt u mogelijk meerdere gelijktijdige muteren-bewerkingen initiëren op een doorlopende implementatie. U kunt bijvoorbeeld een service-update uitvoeren en, terwijl deze update wordt doorgevoerd in uw service, u een bepaalde wijziging wilt aanbrengen, bijvoorbeeld om de update terug te draaien, een andere update toe te passen of zelfs de implementatie te verwijderen. Een geval waarin dit nodig kan zijn, is als een service-upgrade een code voor de fout opsporing bevat die ervoor zorgt dat een bijgewerkte rolinstantie herhaaldelijk vastloopt. In dit geval kan de Azure Fabric-controller geen voortgang maken bij het Toep assen van deze upgrade omdat een ontoereikend aantal exemplaren in het bijgewerkte domein in orde is. Deze status wordt een *vastgelopen implementatie* genoemd. U kunt de implementatie ontsteken door de update terug te draaien of door een nieuwe update toe te passen boven op een failover.

Zodra de eerste aanvraag voor het bijwerken of upgraden van de service is ontvangen door de Azure Fabric-controller, kunt u de volgende muteren-bewerkingen starten. Dat wil zeggen dat u niet hoeft te wachten tot de eerste bewerking is voltooid voordat u een andere muteren-bewerking kunt starten.

Het starten van een tweede update bewerking tijdens het uitvoeren van de eerste update zal hetzelfde doen als de terugdraai bewerking. Als de tweede update zich in de automatische modus bevindt, wordt het eerste upgrade domein onmiddellijk bijgewerkt, waardoor het mogelijk is dat meerdere upgrade domeinen offline zijn op hetzelfde moment.

De muteren bewerkingen zijn als volgt: [implementatie configuratie wijzigen](/previous-versions/azure/reference/ee460809(v=azure.100)), [implementatie bijwerken](/previous-versions/azure/reference/ee460793(v=azure.100)), implementatie [status bijwerken](/previous-versions/azure/reference/ee460808(v=azure.100)), [implementatie verwijderen](/previous-versions/azure/reference/ee460815(v=azure.100))en [Update of upgrade terugdraaien](/previous-versions/azure/reference/hh403977(v=azure.100)).

Twee bewerkingen, de [implementatie ophalen](/previous-versions/azure/reference/ee460804(v=azure.100)) en eigenschappen van de [Cloud service ophalen](/previous-versions/azure/reference/ee460806(v=azure.100)), retour neren de vergrendelde vlag die kan worden onderzocht om te bepalen of een muteren-bewerking kan worden aangeroepen voor een bepaalde implementatie.

Als u de versie van deze methoden die de vergrendelde vlag retourneert, wilt aanroepen, moet u de aanvraag header instellen op x-MS-version: 2011-10-01 of hoger. Zie [Service Management versie beheer](/previous-versions/azure/gg592580(v=azure.100))voor meer informatie over het versie gebruik van headers.

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distributie van rollen tussen upgrade domeinen
Azure distribueert exemplaren van een rol gelijkmatig over een set aantal upgrade domeinen, die kunnen worden geconfigureerd als onderdeel van het bestand met de service definitie (. csdef). Het maximum aantal upgrade domeinen is 20 en de standaard waarde is 5. Zie voor meer informatie over het wijzigen van het service definitie bestand [Azure service definition schema (csdef-bestand)](cloud-services-model-and-package.md#csdef).

Als uw rol bijvoorbeeld tien instanties heeft, bevat elk upgrade domein standaard twee exemplaren. Als uw rol 14 exemplaren heeft, bevatten vier van de upgrade domeinen drie instanties en bevat een vijfde domein twee.

Upgrade domeinen worden aangeduid met een op nul gebaseerde index: het eerste upgrade domein heeft een ID van 0 en het tweede upgrade domein heeft een ID van 1, enzovoort.

In het volgende diagram ziet u hoe een service dan twee rollen bevat, wanneer de service twee upgrade domeinen definieert. Voor de service worden acht exemplaren van de webrole en negen exemplaren van de rol Worker uitgevoerd.

![Distributie van upgrade domeinen](media/cloud-services-update-azure-service/IC345533.png "Distributie van upgrade domeinen")

> [!NOTE]
> Houd er rekening mee dat Azure bepaalt hoe instanties worden toegewezen in upgrade domeinen. Het is niet mogelijk om op te geven welke exemplaren aan welk domein worden toegewezen.
>
>

## <a name="next-steps"></a>Volgende stappen
[Cloud Services beheren](cloud-services-how-to-manage-portal.md)  
[Cloud Services bewaken](cloud-services-how-to-monitor.md)  
[Cloud Services configureren](cloud-services-how-to-configure-portal.md)