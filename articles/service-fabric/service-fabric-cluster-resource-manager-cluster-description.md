---
title: Een cluster beschrijven met cluster resource manager
description: Beschrijf een Service Fabric cluster door fout domeinen, upgrade domeinen, knooppunt eigenschappen en knooppunt capaciteit voor cluster resource manager op te geven.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92911763"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Een Service Fabric cluster beschrijven met cluster resource manager

De functie cluster resource manager van Azure Service Fabric biedt verschillende methoden voor het beschrijven van een cluster:

* Foutdomeinen
* Upgrade domeinen
* Knooppunteigenschappen
* Knooppunt capaciteit

Tijdens runtime gebruikt cluster resource manager deze informatie om te zorgen voor een hoge Beschik baarheid van de services die in het cluster worden uitgevoerd. Bij het afdwingen van deze belang rijke regels wordt er ook geprobeerd het Resource verbruik in het cluster te optimaliseren.

## <a name="fault-domains"></a>Foutdomeinen

Een fout domein is een deel van de gecoördineerde fout. Een enkele computer is een fout domein. De IT-omgeving kan om verschillende redenen mislukken, van fouten in de stroom voorziening om storingen te vertonen in beschadigde NIC-firmware.

Computers die zijn verbonden met dezelfde Ethernet-switch, bevinden zich in hetzelfde fout domein. Dit zijn computers die één bron van kracht of op één locatie delen.

Omdat het natuurlijk is voor hardwarefouten die elkaar overlappen, zijn fout domeinen inherent hiërarchisch. Ze worden weer gegeven als Uri's in Service Fabric.

Het is belang rijk dat fout domeinen correct zijn ingesteld, omdat Service Fabric deze informatie gebruikt om services veilig te plaatsen. Service Fabric geen services wilt plaatsen zodanig dat het verlies van een fout domein (veroorzaakt door uitval van bepaalde onderdelen) ertoe leidt dat een service uitvalt.

In de Azure-omgeving gebruikt Service Fabric de fout domein gegevens die door de omgeving worden verstrekt om namens u de knoop punten in het cluster correct te configureren. Voor zelfstandige exemplaren van Service Fabric worden fout domeinen gedefinieerd op het moment dat het cluster is ingesteld.

> [!WARNING]
> Het is belang rijk dat de fout domein gegevens die aan Service Fabric worden verstrekt nauw keurig zijn. Stel bijvoorbeeld dat de knoop punten van uw Service Fabric-cluster worden uitgevoerd binnen 10 virtuele machines, die worden uitgevoerd op 5 fysieke hosts. In dit geval, zelfs als er tien virtuele machines zijn, zijn er slechts 5 verschillende fout domeinen (op het hoogste niveau). Het delen van dezelfde fysieke host zorgt ervoor dat Vm's hetzelfde hoofd fout domein delen, omdat de Vm's zich in een gecoördineerde storing voordoen als de fysieke host mislukt.  
>
> Service Fabric verwacht dat het fout domein van een knoop punt niet kan worden gewijzigd. Andere mechanismen voor het garanderen van een hoge Beschik baarheid van de virtuele machines, zoals [ha-vm's](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10)), kunnen leiden tot conflicten met Service Fabric. Deze mechanismen maken gebruik van transparante migratie van Vm's van de ene host naar een andere. Ze configureren of melden de actieve code niet opnieuw in de virtuele machine. Daarom worden ze *niet ondersteund* als omgevingen voor het uitvoeren van service Fabric clusters. 
>
> Service Fabric moet de enige technologie zijn die als hoge Beschik baarheid wordt gebruikt. Mechanismen als Livemigratie en San's van de VM zijn niet nodig. Als deze mechanismen worden gebruikt in combi natie met Service Fabric, _beperken_ ze de beschik baarheid en betrouw baarheid van de toepassing. De reden hiervoor is dat ze extra complexiteit introduceren, gecentraliseerde bronnen van fouten toevoegen en Betrouwbaarheids-en beschikbaarheids strategieën gebruiken die in Service Fabric conflicteren.
>
>

In de volgende afbeelding kleuren we alle entiteiten die bijdragen aan fout domeinen en een lijst van alle verschillende fout domeinen die het resultaat zijn. In dit voor beeld hebben we data centers (' DC '), racks (' R ') en Blades (' B '). Als elke blade meer dan één virtuele machine bevat, is er mogelijk nog een laag in de fout domein hiërarchie.

![Knoop punten ingedeeld via fout domeinen][Image1]

Tijdens runtime beschouwt Service Fabric cluster resource manager de fout domeinen in het cluster en de indelingen van plannen. De stateful replica's of stateless instanties voor een service worden gedistribueerd zodat ze zich in afzonderlijke fout domeinen bevinden. Wanneer de service wordt gedistribueerd over fout domeinen, zorgt u ervoor dat de beschik baarheid van de service niet wordt aangetast wanneer een fout domein op een niveau van de hiërarchie mislukt.

Cluster resource manager bezorgt niet hoeveel lagen er in de fout domein hiërarchie aanwezig zijn. Er wordt geprobeerd om ervoor te zorgen dat het verlies van een deel van de hiërarchie geen invloed heeft op de services die erop worden uitgevoerd.

Het is het beste als hetzelfde aantal knoop punten zich op elk niveau van diepte in de fout domein hiërarchie bevindt. Als de boom structuur van fout domeinen niet in balans is in uw cluster, is het moeilijker voor cluster bron beheer om de beste toewijzing van services te bepalen. Niet-sluitende fout domein indelingen betekenen dat het verlies van sommige domeinen van invloed is op de beschik baarheid van services die meer dan andere domeinen zijn. Als gevolg hiervan is cluster resource manager gescheurd tussen twee doel einden:

* Dit wil zeggen dat de computers in dat ' zware ' domein worden gebruikt door services op deze servers te plaatsen. 
* Er moeten services in andere domeinen worden geplaatst, zodat het verlies van een domein geen problemen veroorzaakt.

Hoe zien onevenwichtige domeinen eruit? In het volgende diagram ziet u twee verschillende cluster indelingen. In het eerste voor beeld worden de knoop punten gelijkmatig verdeeld over de fout domeinen. In het tweede voor beeld heeft één fout domein veel meer knoop punten dan de andere fout domeinen.

![Twee verschillende cluster indelingen][Image2]

In azure wordt de keuze van het fout domein dat een knoop punt bevat, voor u beheerd. Maar afhankelijk van het aantal knoop punten dat u inricht, kunt u nog steeds eindigen met fout domeinen die meer knoop punten bevatten dan in andere.

Stel dat u vijf fout domeinen in het cluster hebt, maar zeven knoop punten inricht voor een knooppunt type (**NodeType**). In dit geval eindigen de eerste twee fout domeinen uiteindelijk met meer knoop punten. Als u meer **NodeType** -exemplaren met slechts een paar exemplaren wilt implementeren, wordt het probleem erger. Daarom raden we aan dat het aantal knoop punten in elk knooppunt type een veelvoud is van het aantal fout domeinen.

## <a name="upgrade-domains"></a>Upgrade domeinen

Upgrade domeinen zijn een andere functie waarmee Service Fabric cluster resource manager de indeling van het cluster begrijpt. Upgrade domeinen definiëren sets van knoop punten die op hetzelfde moment worden bijgewerkt. Upgrade domeinen helpen cluster resource Manager beheer bewerkingen zoals upgrades te begrijpen en te organiseren.

Upgrade domeinen zijn veel zoals fout domeinen, maar met een aantal belang rijke verschillen. Ten eerste bepalen gebieden met gecoördineerde hardwarefouten fout domeinen. Upgrade domeinen worden daarentegen door beleid gedefinieerd. U kunt bepalen hoeveel u wilt, in plaats van dat de omgeving het nummer zelf bepaalt. U kunt net zoveel upgrade domeinen als knoop punten hebben. Een ander verschil tussen fout domeinen en upgrade domeinen is dat upgrade domeinen niet hiërarchisch zijn. In plaats daarvan zijn ze meer hetzelfde als een eenvoudige tag.

In het volgende diagram ziet u drie upgrade domeinen die zijn gesegmenteerd over drie fout domeinen. Er wordt ook een mogelijke plaatsing weer gegeven voor drie verschillende replica's van een stateful service, waarbij elke oploopt in verschillende fout-en upgrade domeinen. Deze plaatsing maakt het mogelijk dat er een fout domein verloren gaat in het midden van een service-upgrade en nog steeds één exemplaar van de code en gegevens.  

![Plaatsing met fout-en upgrade domeinen][Image3]

Er zijn voor-en nadelen voor een groot aantal upgrade domeinen. Meer upgrade domeinen betekenen dat elke stap van de upgrade nauw keuriger is en invloed heeft op een kleiner aantal knoop punten of services. Er moeten minder services tegelijk worden verplaatst, zodat er minder verloop in het systeem wordt geïntroduceerd. Dit is doorgaans de betrouw baarheid verbeterd, omdat het minder van de service wordt beïnvloed door een probleem dat tijdens de upgrade wordt geïntroduceerd. Meer upgrade domeinen betekenen ook dat u minder beschik bare buffer op andere knoop punten nodig hebt om de impact van de upgrade te kunnen afhandelen.

Als u bijvoorbeeld vijf upgrade domeinen hebt, worden de knoop punten in elk ongeveer 20 procent van uw verkeer verwerkt. Als u het upgrade domein wilt uitvoeren voor een upgrade, moet die belasting meestal ergens anders gaan. Omdat u vier resterende upgrade domeinen hebt, moeten er voor elk 25 procent van het totale verkeer voldoende ruimte zijn. Meer upgrade domeinen betekenen dat u minder buffers nodig hebt voor de knoop punten in het cluster.

Houd rekening met het gebruik van 10 upgrade domeinen. In dat geval verwerkt elk upgrade domein slechts 10 procent van het totale verkeer. Wanneer een upgrade stappen door het cluster worden uitgevoerd, moet elk domein voldoende ruimte hebben voor slechts ongeveer 11 procent van het totale verkeer. Meer upgrade domeinen maken het meestal mogelijk om uw knoop punten uit te voeren op een hoger gebruik, omdat u minder gereserveerde capaciteit nodig hebt. Dit geldt ook voor fout domeinen.  

Het nadeel van veel upgrade domeinen is dat upgrades meer tijd in beslag nemen. Service Fabric wacht een korte periode nadat een upgrade domein is voltooid en voert controles uit voordat u begint met het upgraden van de volgende. Deze vertragingen zorgen ervoor dat detectie problemen die door de upgrade worden geïntroduceerd, worden gedetecteerd voordat de upgrade wordt uitgevoerd. De balans is acceptabel omdat het voor komt dat onjuiste wijzigingen van invloed zijn op een groot deel van de service.

De aanwezigheid van te weinig upgrade domeinen heeft veel negatieve neven effecten. Hoewel elk upgrade domein niet actief is en wordt bijgewerkt, is een groot deel van de totale capaciteit niet beschikbaar. Als u bijvoorbeeld slechts drie upgrade domeinen hebt, neemt u ongeveer één derde van uw totale service-of cluster capaciteit tegelijk in beslag. Een groot deel van uw service is in één keer niet gewenst omdat u voldoende capaciteit in de rest van uw cluster nodig hebt om de werk belasting te kunnen afhandelen. Als u die buffer beheert, zijn deze knoop punten niet meer geladen dan ze anders zouden zijn. Dit verhoogt de kosten voor het uitvoeren van uw service.

Er is geen echte limiet voor het totale aantal fout-of upgrade domeinen in een omgeving, of beperkingen voor de overlap ping. Maar er zijn algemene patronen:

* Fout domeinen en upgrade domeinen toegewezen 1:1
* Eén upgrade domein per knoop punt (fysiek of virtueel OS-exemplaar)
* Een ' striped ' of ' matrix ' model waarbij de fout domeinen en upgrade domeinen een matrix vormen met computers die meestal diagonaal omlaag worden uitgevoerd

![Indelingen van fout-en upgrade domeinen][Image4]

Er is geen beste antwoord voor de indeling die u moet kiezen. Elk heeft voor-en nadelen. Het model 1FD: 1UD is bijvoorbeeld eenvoudig in te stellen. Het model van één upgrade domein per knooppunt model is het meest geschikt voor de doel nemers. Tijdens upgrades wordt elk knoop punt onafhankelijk bijgewerkt. Dit is vergelijkbaar met de manier waarop kleine sets van machines hand matig in het verleden zijn bijgewerkt.

Het meest voorkomende model is de FD/UD matrix, waarbij de fout domeinen en upgrade domeinen vormen een tabel en knoop punten die op de diagonaal worden geplaatst. Dit is het model dat standaard wordt gebruikt in Service Fabric clusters in Azure. Voor clusters met veel knoop punten is alles wat lijkt op een dikke matrix patroon.

> [!NOTE]
> Service Fabric clusters die worden gehost in azure, bieden geen ondersteuning voor het wijzigen van de standaard strategie. Alleen zelfstandige clusters bieden de aanpassing.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fout-en upgrade van domein beperkingen en resulterende gedrag
### <a name="default-approach"></a>Standaard benadering

Cluster resource manager houdt standaard services in balans tussen fout-en upgrade domeinen. Dit wordt als een [beperking](service-fabric-cluster-resource-manager-management-integration.md)gemodelleerd. De beperking voor de status van fout-en upgrade domeinen: voor een bepaalde service partitie moet er nooit een verschil zijn dat groter is dan één in het aantal service objecten (stateless service exemplaren of stateful service replica's) tussen twee domeinen op hetzelfde niveau van de hiërarchie.

Stel dat deze beperking een ' Maxi maal verschil ' garantie biedt. De beperking voor fout-en upgrade domeinen voor komt bepaalde verplaatsingen of voorzieningen die de regel schenden.

Stel bijvoorbeeld dat we een cluster hebben met zes knoop punten, geconfigureerd met vijf fout domeinen en vijf upgrade domeinen.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |Categorieën | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Stel nu dat we een service maken met een **TargetReplicaSetSize** (of, voor een stateless service, **InstanceCount**)-waarde van vijf. Het land van de replica's op N1-N5. In feite wordt N6 nooit gebruikt, ongeacht het aantal services dat u hier maakt. Maar waarom? Laten we eens kijken naar het verschil tussen de huidige indeling en wat er zou gebeuren als N6 is gekozen.

Dit is de indeling die we hebben ontvangen en het totaal aantal replica's per fout-en upgrade domein:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Deze indeling is evenwichtig in het kader van knoop punten per fout domein en upgrade domein. Het is ook afgewogen in termen van het aantal replica's per fout-en upgrade domein. Elk domein heeft hetzelfde aantal knoop punten en hetzelfde aantal replica's.

Nu gaan we kijken wat er gebeurt als we N6 gebruiken in plaats van N2. Hoe worden de replica's gedistribueerd?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Deze indeling schendt de definitie van de garantie ' Maxi maal verschil ' voor de beperking van het fout domein. FD0 heeft twee replica's, terwijl FD1 nul heeft. Het verschil tussen FD0 en FD1 is een totaal van twee, wat groter is dan het maximum verschil van één. Omdat de beperking wordt geschonden, staat cluster resource manager deze rang schikking niet toe.

En als we N2 en N6 (in plaats van N1 en N2) hebben gepickt, krijgen we het volgende:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Deze indeling is evenwichtig in termen van fout domeinen. Maar nu wordt de beperking van het upgrade domein geschonden, omdat UD0 null-replica's heeft en UD1 twee heeft. Deze indeling is ook ongeldig en wordt niet gekozen door cluster resource manager.

Deze methode voor het distribueren van stateful replica's of stateless instanties biedt de best mogelijke fout tolerantie. Als één domein uitvalt, is het minimale aantal replica's/instanties verloren gegaan.

Aan de andere kant kan deze aanpak te strikt zijn en mag het cluster niet alle resources gebruiken. Voor bepaalde cluster configuraties kunnen bepaalde knoop punten niet worden gebruikt. Dit kan ertoe leiden dat Service Fabric uw services niet plaatst, wat resulteert in waarschuwings berichten. In het vorige voor beeld kunnen sommige cluster knooppunten niet worden gebruikt (N6 in het voor beeld). Zelfs als u knoop punten aan dat cluster hebt toegevoegd (N7-N10), worden replica's/instanties alleen op N1-N5 geplaatst vanwege beperkingen op fout-en upgrade domeinen.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |Categorieën | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Alternatieve aanpak

Cluster resource manager ondersteunt een andere versie van de beperking voor fout-en upgrade domeinen. Het maakt plaatsing mogelijk en garandeert nog steeds een minimum niveau van veiligheid. De alternatieve beperking kan als volgt worden aangegeven: "voor een bepaalde service partitie, replica distributie over domeinen moet ervoor zorgen dat de partitie geen quorum verlies ondervindt." Stel dat deze beperking een ' quorum veilige ' garantie biedt.

> [!NOTE]
> Voor een stateful service definiëren we *quorum verlies* in een situatie waarin een meerderheid van de partitie replica's tegelijk actief is. Als **TargetReplicaSetSize** bijvoorbeeld vijf is, vertegenwoordigt een set van elke drie replica's het quorum. En als **TargetReplicaSetSize** zes zijn, zijn er vier replica's nodig voor quorum. In beide gevallen kunnen niet meer dan twee replica's tegelijkertijd worden uitgevoerd als de partitie normaal gesp roken gewoon werkt.
>
> Voor een stateless service is er geen *quorum verlies*. Stateless services blijven normaal functioneren, zelfs als een meerderheid van de exemplaren tegelijkertijd actief is. Daarom zullen we zich richten op stateful Services in de rest van dit artikel.
>

We gaan terug naar het vorige voor beeld. Met de ' quorum veilige ' versie van de beperking zijn alle drie de indelingen geldig. Zelfs als FD0 is mislukt in de tweede indeling of als UD1 in de derde indeling mislukt, zou de partitie nog steeds quorum hebben. (Een meerderheid van de replica's zou nog steeds actief zijn.) Met deze versie van de beperking kan N6 bijna altijd worden gebruikt.

De benadering ' quorum veilig ' biedt meer flexibiliteit dan de benadering ' Maxi maal verschil '. De reden hiervoor is dat het gemakkelijker is om replica distributies te vinden die geldig zijn in vrijwel elke cluster topologie. Deze aanpak kan echter niet voldoen aan de kenmerken van de beste fout tolerantie omdat sommige fouten erger zijn dan andere.

In het ergste geval kan een meerderheid van de replica's verloren gaan met het mislukken van één domein en één extra replica. In plaats van drie storingen die zijn vereist om het quorum te verliezen met vijf replica's of exemplaren, kunt u nu een meerderheid met twee storingen verliezen.

### <a name="adaptive-approach"></a>Adaptieve benadering

Omdat beide benaderingen sterke en zwakke plekken hebben, hebben we een adaptieve benadering geïntroduceerd waarin deze twee strategieën worden gecombineerd.

> [!NOTE]
> Dit is het standaard gedrag dat begint bij Service Fabric versie 6,2.
>
> De adaptieve benadering maakt standaard gebruik van de logica ' Maximum verschil ' en schakelt alleen de logica ' quorum veilig ' in als dat nodig is. Cluster resource manager heeft automatisch de benodigde strategie door te kijken naar de configuratie van het cluster en de services.
>
> Cluster resource manager moet de logica ' op basis van quorum ' voor een service gebruiken beide voor waarden waar zijn:
>
> * **TargetReplicaSetSize** voor de service is gelijkmatig deelbaar door het aantal fout domeinen en het aantal upgrade domeinen.
> * Het aantal knoop punten is kleiner dan of gelijk aan het aantal fout domeinen vermenigvuldigd met het aantal upgrade domeinen.
>
> Houd er rekening mee dat cluster resource manager deze methode gebruikt voor stateless en stateful Services, zelfs als quorum verlies niet relevant is voor stateless Services.

We gaan terug naar het vorige voor beeld en gaan ervan uit dat een cluster nu acht knoop punten heeft. Het cluster is nog steeds geconfigureerd met vijf fout domeinen en vijf upgrade domeinen en de **TargetReplicaSetSize** -waarde van een service die op dat cluster wordt gehost, blijft vijf.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |Categorieën | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Omdat aan alle benodigde voor waarden wordt voldaan, gebruikt cluster resource manager de logica ' quorum gebaseerd ' bij het distribueren van de service. Hiermee wordt het gebruik van N6-N8 ingeschakeld. Een mogelijke service distributie in dit geval kan er als volgt uitzien:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Als de **TargetReplicaSetSize** -waarde van uw service wordt beperkt tot vier (bijvoorbeeld), ziet u in cluster resource manager dat er wijzigingen zijn. Het wordt hervat met behulp van de logica ' Maximum verschil ', omdat **TargetReplicaSetSize** niet meer kan worden gedeeld door het aantal fout domeinen en upgrade domeinen. Als gevolg hiervan worden bepaalde replica verplaatsingen uitgevoerd om de resterende vier replica's te verdelen op knoop punten N1-N5. Op die manier wordt de versie ' Maximum verschil ' van het fout domein en de upgrade van domein logica niet geschonden.

Als in de vorige indeling de **TargetReplicaSetSize** -waarde vijf en N1 uit het cluster is verwijderd, wordt het aantal upgrade domeinen gelijk aan vier. Daarnaast begint cluster resource manager met behulp van de logica ' Maximum verschil ', omdat het aantal upgrade domeinen de **TargetReplicaSetSize** -waarde van de service evenredig niet meer opsplitst. Als gevolg hiervan heeft replica R1, wanneer deze opnieuw wordt gebouwd, op N4, zodat de beperking voor het probleem en het upgrade domein niet wordt geschonden.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N.v.t. |N.v.t. |N.v.t. |N.v.t. |N.v.t. |N.v.t. |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Fout-en upgrade domeinen configureren

In door Azure gehoste Service Fabric-implementaties worden fout domeinen en upgrade domeinen automatisch gedefinieerd. Service Fabric de omgevings gegevens van Azure ophaalt en gebruikt.

Als u uw eigen cluster maakt (of als u een bepaalde topologie in ontwikkeling wilt uitvoeren), kunt u het fout domein zelf opgeven en zelf de domein gegevens upgraden. In dit voor beeld definiëren we een lokaal ontwikkelings cluster met negen knoop punten dat drie data centers omvat (elk met drie racks). Dit cluster heeft ook drie upgrade domeinen die zijn gesegmenteerd over deze drie data centers. Hier volgt een voor beeld van de configuratie in ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

In dit voor beeld wordt ClusterConfig.jsgebruikt voor zelfstandige implementaties:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Wanneer u clusters definieert via Azure Resource Manager, worden fout domeinen en upgrade domeinen door Azure toegewezen. De definitie van uw knooppunt typen en schaal sets voor virtuele machines in uw Azure Resource Manager sjabloon bevat dus geen informatie over het fout domein of het upgrade domein.
>

## <a name="node-properties-and-placement-constraints"></a>Knooppunt eigenschappen en plaatsings beperkingen

Soms wilt u er ook voor zorgen dat bepaalde werk belastingen alleen worden uitgevoerd op bepaalde typen knoop punten in het cluster. Sommige werk belastingen vereisen bijvoorbeeld Gpu's of Ssd's, en andere kunnen niet.

Een goed voor beeld van het doel van hardware voor bepaalde werk belastingen is bijna elke n-tier-architectuur. Bepaalde machines fungeren als front-end-of API-client voor de toepassing en worden blootgesteld aan de clients of het internet. Verschillende machines, vaak met verschillende hardwarebronnen, verwerken het werk van de reken-of opslag lagen. Deze worden meestal _niet_ rechtstreeks blootgesteld aan clients of Internet.

Service Fabric verwacht dat in sommige gevallen bepaalde werk belastingen mogelijk moeten worden uitgevoerd op bepaalde hardwareconfiguraties. Bijvoorbeeld:

* Een bestaande toepassing met n-Tiers is in een Service Fabric omgeving getil en geschoven.
* Een werk belasting moet op specifieke hardware worden uitgevoerd om redenen voor prestaties, schalen of beveiligings isolatie.
* Een werk belasting moet worden geïsoleerd van andere werk belastingen om redenen van beleid of bronnen verbruik.

Service Fabric bevat Tags die u kunt Toep assen op knoop punten voor ondersteuning bij het gebruik van deze configuraties. Deze tags worden *knooppunt eigenschappen* genoemd. *Plaatsings beperkingen* zijn de instructies die zijn gekoppeld aan afzonderlijke services die u voor een of meer knooppunt eigenschappen selecteert. Plaatsings beperkingen bepalen waar services moeten worden uitgevoerd. De set met beperkingen is uitbreidbaar. Een combi natie van sleutel/waarde kan werken.

![Verschillende werk belastingen voor een cluster indeling][Image5]

### <a name="built-in-node-properties"></a>Ingebouwde knooppunt eigenschappen

Service Fabric definieert een aantal standaard knooppunt eigenschappen die automatisch kunnen worden gebruikt, zodat u ze niet hoeft te definiëren. De standaard eigenschappen die op elk knoop punt worden gedefinieerd, zijn **NodeType** en **knooppuntnaam**.

U kunt bijvoorbeeld een plaatsings beperking schrijven als `"(NodeType == NodeType03)"` . **NodeType** is een veelgebruikte eigenschap. Het is nuttig omdat deze 1:1 overeenkomt met een type machine. Elk type computer komt overeen met een type werk belasting in een traditionele toepassing met n-tier.

![Plaatsings beperkingen en knooppunt eigenschappen][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Plaatsings beperkingen en syntaxis van knooppunt eigenschappen

De waarde die in de knooppunt eigenschap is opgegeven, kan een teken reeks, een Boolean of een lange integer zijn. De instructie bij de service wordt een plaatsings *beperking* genoemd, omdat deze beperkt waar de service kan worden uitgevoerd in het cluster. De beperking kan elke Booleaanse instructie zijn die wordt toegepast op de knooppunt eigenschappen in het cluster. De geldige selecters in deze Boole-instructies zijn:

* Voorwaardelijke controles voor het maken van specifieke instructies:

  | Instructie | Syntax |
  | --- |:---:|
  | "gelijk aan" | "==" |
  | is niet gelijk aan | "!=" |
  | ' groter dan ' | ">" |
  | ' groter dan of gelijk aan ' | ">=" |
  | kleiner dan | "<" |
  | kleiner dan of gelijk aan | "<=" |

* Booleaanse instructies voor groepering en logische bewerkingen:

  | Instructie | Syntax |
  | --- |:---:|
  | maar | "&&" |
  | of | "&#124;&#124;" |
  | ten | "!" |
  | "groeperen als één instructie" | "()" |

Hier volgen enkele voor beelden van elementaire beperkings instructies:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Alleen knoop punten waarbij de instructie van de algemene plaatsings beperking resulteert in ' True ', kan de service op de node hebben geplaatst. Knoop punten waarvoor geen eigenschap is gedefinieerd, komen niet overeen met een plaatsings beperking die de eigenschap bevat.

Stel dat de volgende knooppunt eigenschappen zijn gedefinieerd voor een knooppunt type in ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

In het volgende voor beeld ziet u de knooppunt eigenschappen die zijn gedefinieerd via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters.

> [!NOTE]
> In uw Azure Resource Manager-sjabloon is het knooppunt type meestal para meters. Dit ziet eruit als `"[parameters('vmNodeType1Name')]"` in plaats van NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

U kunt als volgt service plaatsings *beperkingen* voor een service maken:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Als alle knoop punten van NodeType01 geldig zijn, kunt u het knooppunt type ook selecteren met de beperking `"(NodeType == NodeType01)"` .

De plaatsings beperkingen van een service kunnen dynamisch worden bijgewerkt tijdens runtime. Indien nodig kunt u een service in het cluster verplaatsen, vereisten toevoegen en verwijderen, enzovoort. Service Fabric zorgt ervoor dat de service actief blijft, zelfs wanneer deze typen wijzigingen worden aangebracht.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Plaatsings beperkingen zijn opgegeven voor elk benoemd service-exemplaar. Updates nemen altijd de plaats van (overschrijven) wat eerder is opgegeven.

De cluster definitie definieert de eigenschappen van een knoop punt. Als u de eigenschappen van een knoop punt wilt wijzigen, moet u een upgrade uitvoeren naar de cluster configuratie. Als u de eigenschappen van een knoop punt wilt bijwerken, moet elk betrokken knoop punt opnieuw worden gestart om de nieuwe eigenschappen te melden. Service Fabric beheert deze roulerende upgrades.

## <a name="describing-and-managing-cluster-resources"></a>Cluster bronnen beschrijven en beheren

Een van de belangrijkste taken van een Orchestrator is om het resource gebruik in het cluster te beheren. Het beheren van cluster bronnen kan een aantal verschillende dingen betekenen.

De eerste keer dat de computers niet overbelast zijn. Dit betekent dat machines niet meer services uitvoeren dan ze kunnen verwerken.

Ten tweede is er sprake van Balancing en optimalisatie, wat van essentieel belang is voor het efficiënt uitvoeren van services. Met rendabele of prestatie gevoelige service aanbiedingen kunnen bepaalde knoop punten niet worden warme wanneer anderen koud zijn. Hot knoop punten leiden tot bron conflicten en slechte prestaties. Koude knoop punten vertegenwoordigen verspilde resources en verhoogde kosten.

Service Fabric geeft resources als *metrische gegevens*. Metrische gegevens zijn een logische of fysieke resource die u wilt beschrijven Service Fabric. Voor beelden van metrische gegevens zijn ' WorkQueueDepth ' of ' MemoryInMb '. Zie [resource governance](service-fabric-resource-governance.md)voor informatie over de fysieke resources die service Fabric kunnen bepalen op knoop punten. Zie [dit artikel](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over de standaard waarden die worden gebruikt door cluster resource manager en het configureren van aangepaste metrische gegevens.

Metrische gegevens verschillen van plaatsings beperkingen en eigenschappen van knoop punten. Knooppunt eigenschappen zijn statische descriptoren van de knoop punten zelf. Metrische gegevens beschrijven resources die knoop punten hebben en die services verbruiken wanneer ze op een knoop punt worden uitgevoerd. Een eigenschap van een knoop punt kan **HasSSD** zijn en kan worden ingesteld op True of false. De hoeveelheid beschik bare ruimte op die SSD en hoeveel er door Services wordt verbruikt, is een metrische waarde zoals ' DriveSpaceInMb '.

Net als bij plaatsings beperkingen en knooppunt eigenschappen Service Fabric cluster resource manager niet begrijpen wat de namen van de metrische gegevens betekenen. Metrische namen zijn alleen teken reeksen. Het is een goed idee om eenheden te declareren als onderdeel van de metrische namen die u maakt wanneer ze mogelijk dubbel zinnig zijn.

## <a name="capacity"></a>Capaciteit

Als u alle resources hebt *uitgebalanceerd*, wordt service Fabric cluster resource manager er nog steeds voor zorgen dat er geen knoop punten meer zijn dan de capaciteit. Het beheer van capaciteits overschrijdingen is mogelijk tenzij het cluster te vol is of omdat de werk belasting groter is dan een wille keurig knoop punt. De capaciteit is een andere *beperking* die door cluster resource manager wordt gebruikt om te begrijpen welk deel van een resource een knoop punt heeft. De resterende capaciteit wordt ook bijgehouden voor het cluster als geheel.

Zowel de capaciteit als het verbruik op service niveau worden uitgedrukt in metrieke waarden. De metriek kan bijvoorbeeld ' ClientConnections ' zijn en een knoop punt heeft mogelijk een capaciteit voor ' ClientConnections ' van 32.768. Andere knoop punten kunnen andere limieten hebben. Een service die op dat knoop punt wordt uitgevoerd, kan zeggen dat er op dit moment 32.256 van de metrische waarde ' ClientConnections ' wordt gebruikt.

Tijdens runtime houdt cluster resource manager de resterende capaciteit in het cluster en op knoop punten bij. Voor het bijhouden van de capaciteit trekt cluster resource manager elk gebruik van de service af van de capaciteit van een knoop punt waar de service wordt uitgevoerd. Met deze informatie kan cluster bron beheer bepalen waar replica's moeten worden geplaatst of verplaatst, zodat de knoop punten de capaciteit niet overschrijden.

![Cluster knooppunten en-capaciteit][Image7]

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

U kunt de capaciteit zien die in het cluster manifest is gedefinieerd. Hier volgt een voor beeld van ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Hier volgt een voor beeld van capaciteit gedefinieerd via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Het laden van een service wordt vaak dynamisch gewijzigd. Stel dat de belasting van een replica van ClientConnections is gewijzigd van 1.024 naar 2.048. Op het knoop punt waarop het werd uitgevoerd, was er slechts een capaciteit van 512 voor die metriek. Nu de plaatsing van de replica of het exemplaar ongeldig is, omdat er onvoldoende ruimte is op het knoop punt. Cluster resource manager moet het knoop punt onder de capaciteit ophalen. Het verkleint de belasting van het knoop punt dat de capaciteit overschrijdt door een of meer van de replica's of exemplaren van dat knoop punt te verplaatsen naar andere knoop punten.

Cluster resource manager probeert de kosten voor het verplaatsen van replica's te minimaliseren. U vindt meer informatie over de [verplaatsings kosten](service-fabric-cluster-resource-manager-movement-cost.md) en over het [herverdelen van strategieën en regels](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Cluster capaciteit

Hoe houdt het Service Fabric cluster resource manager het hele cluster niet meer te vol? Met dynamische belasting is er geen partij die het kan doen. Services kunnen de belasting piek onafhankelijk van de acties die cluster resource manager onderneemt. Als gevolg hiervan kan uw cluster met veel ruimte vandaag worden onderstroomd als er morgen een Prikker is.

Besturings elementen in cluster resource manager helpen bij het voor komen van problemen. Het eerste wat u kunt doen, voor komt dat er nieuwe werk belastingen worden gemaakt waardoor het cluster vol raakt.

Stel dat u een stateless service maakt en dat er een andere belasting is gekoppeld. De service zorgt voor de metrische gegevens van de DiskSpaceInMb. De service gebruikt vijf eenheden van ' DiskSpaceInMb ' voor elk exemplaar van de service. U wilt drie instanties van de service maken. Dit betekent dat u 15 eenheden van ' DiskSpaceInMb ' nodig hebt om deze service-exemplaren zelfs te maken in het cluster.

Cluster resource manager berekent voortdurend de capaciteit en het verbruik van elke metriek zodat de resterende capaciteit in het cluster kan worden bepaald. Als er onvoldoende ruimte is, weigert cluster resource manager de aanroep om een service te maken.

Omdat de vereiste is dat er 15 eenheden beschikbaar zijn, kunt u deze ruimte op tal van verschillende manieren toewijzen. Er kan bijvoorbeeld één resterende capaciteits eenheid zijn op 15 verschillende knoop punten, of drie resterende capaciteits eenheden op vijf verschillende knoop punten. Als cluster resource manager items kan herschikken zodat er vijf eenheden beschikbaar zijn op drie knoop punten, wordt de service geplaatst. Het cluster kan meestal niet opnieuw worden ingedeeld, tenzij het cluster bijna vol is of als de bestaande services om een of andere reden niet kunnen worden geconsolideerd.

## <a name="node-buffer-and-overbooking-capacity"></a>Knooppunt buffer en overboekings capaciteit

Als de capaciteit van een knoop punt voor een metriek is opgegeven, plaatst cluster resource manager nooit replica's naar een knoop punt als de totale belasting de opgegeven knooppunt capaciteit zou overschrijden. Dit kan soms voor komen dat nieuwe replica's worden geplaatst of dat er mislukte replica's worden vervangen als het cluster bijna vol is en een replica met een grote belasting moet worden aangebracht, vervangen of verplaatst.

Als u meer flexibiliteit wilt bieden, kunt u de capaciteit van het knoop punt of de overboeking opgeven. Als er een waarde voor de knooppunt buffer of de overboeking van de capaciteit voor een metriek wordt opgegeven, probeert cluster resource manager replica's op een zodanige manier te plaatsen of verplaatsen dat de buffer-of overboekings capaciteit ongebruikt blijft, maar de buffer-of overboekings capaciteit zo nodig kan worden gebruikt voor acties die de beschik baarheid van de service verhogen, zoals:

* Nieuwe replica-plaatsing of vervangen van mislukte replica's
* Plaatsing tijdens upgrades
* Vaststelling van schendingen van zachte en harde beperkingen
* Defragmentatie

De capaciteit van de knooppunt buffer vertegenwoordigt een gereserveerd gedeelte van de capaciteit onder opgegeven knooppunt capaciteit en overboekings capaciteit vertegenwoordigt een deel van extra capaciteit boven de opgegeven knooppunt capaciteit. In beide gevallen probeert cluster resource manager deze capaciteit vrij te laten.

Als een knoop punt bijvoorbeeld een opgegeven capaciteit heeft voor het metrieke *CpuUtilization* van 100 en het percentage van het knoop punt buffer voor die metriek is ingesteld op 20%, zijn de totale en niet-gebufferde capaciteit respectievelijk 100 en 80. de cluster resource manager plaatst niet meer dan 80 eenheden van belasting op het knoop punt tijdens normale omstandigheden.

![Totale capaciteit is gelijk aan capaciteit van knoop punt (knooppunt buffer + niet in buffer)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

Knooppunt buffer moet worden gebruikt als u een deel van de knooppunt capaciteit wilt reserveren dat alleen wordt gebruikt voor acties die de hierboven vermelde Beschik baarheid van de service verg Roten.

Daarentegen geldt dat als het knoop punt overboekings percentage wordt gebruikt en is ingesteld op 20%, het totale aantal en de niet-gebufferde capaciteit respectievelijk 120 en 100.

![Totale capaciteit is gelijk aan overboekings capaciteit plus knooppunt capaciteit (overboeken en niet-gebufferd)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

De capaciteit voor overboeking moet worden gebruikt wanneer u wilt toestaan dat cluster resource manager replica's op een knoop punt plaatst, zelfs als het totale resource gebruik de capaciteit zou overschrijden. Dit kan worden gebruikt om extra Beschik baarheid voor services te bieden tegen de kosten van prestaties. Als overboeking wordt gebruikt, moet de toepassings logica van de gebruiker kunnen functioneren met minder fysieke resources dan nodig is.

Als er een knooppunt buffer of overboekings capaciteit is opgegeven, zal cluster resource manager geen replica's verplaatsen of plaatsen als de totale belasting van het doel knooppunt over de totale capaciteit gaat (knooppunt capaciteit in het geval van knooppunt buffer en knooppunt capaciteit + overboeking van capaciteit in het geval van overboeking).

De capaciteit voor overboeking kan ook worden opgegeven als oneindig. In dit geval probeert cluster resource manager de totale belasting op het knoop punt onder de opgegeven capaciteit van het knoop punt te blijven gebruiken, maar is het mogelijk dat er een veel grotere belasting op het knoop punt plaatsvindt, wat kan leiden tot ernstige prestatie vermindering.

Er kunnen op hetzelfde moment geen knooppunt buffer en overboekings capaciteit worden opgegeven voor een metriek.

Hier volgt een voor beeld van het opgeven van de knooppunt buffer of het overboeken van de capaciteit in *ClusterManifest.xml*:

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Hier volgt een voor beeld van het opgeven van de knooppunt buffer of het overboeken van capaciteit via *ClusterConfig.jsop* zelfstandige implementaties of *Template.jsop voor door* Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van cluster resource manager-architectuur](service-fabric-cluster-resource-manager-architecture.md)voor meer informatie over de architectuur en informatie stroom in cluster resource manager.
* Het definiëren van metrische gegevens over defragmentatie is een manier om de belasting op knoop punten te consolideren in plaats van deze te spreiden. Zie [defragmentatie van metrische gegevens en laden in service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md)voor meer informatie over het configureren van defragmentatie.
* Begin vanaf het begin en [krijg een inleiding tot service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md).
* Zie [Balancing your service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md)(Engelstalig) voor meer informatie over het beheren en verdelen van de belasting van cluster resource manager in het cluster.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
