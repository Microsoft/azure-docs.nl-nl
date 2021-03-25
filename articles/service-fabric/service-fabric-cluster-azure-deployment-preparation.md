---
title: Een Azure Service Fabric-cluster implementatie plannen
description: Meer informatie over het plannen en voorbereiden van een productie-Service Fabric cluster implementatie naar Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046314"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Plannen en voorbereiden voor een cluster implementatie

Het plannen en voorbereiden van een productie cluster implementatie is zeer belang rijk.  Er zijn veel factoren waarmee u rekening moet houden.  Dit artikel begeleidt u stapsgewijs door de stappen voor het voorbereiden van de implementatie van uw cluster.

## <a name="read-the-best-practices-information"></a>Lees de informatie over best practices
Voor het beheren van Azure Service Fabric-toepassingen en-clusters zijn er bewerkingen die u het beste kunt uitvoeren om de betrouw baarheid van uw productie omgeving te optimaliseren.  Lees [service Fabric aanbevolen procedures voor toepassingen en clusters](./service-fabric-best-practices-security.md)voor meer informatie.

## <a name="select-the-os-for-the-cluster"></a>Het besturings systeem voor het cluster selecteren
Met Service Fabric kunt u Service Fabric-clusters maken op alle Vm's of computers met Windows Server of Linux.  Voordat u het cluster implementeert, moet u het besturings systeem: Windows of Linux kiezen.  Elk knoop punt (virtuele machine) in het cluster voert hetzelfde besturings systeem uit. u kunt geen Windows-en Linux-Vm's in hetzelfde cluster combi neren.

## <a name="capacity-planning"></a>Capaciteitsplanning
Bij elke implementatie voor productie is capaciteitsplanning van groot belang. Hier volgen enkele aandachtspunten voor dat proces.

* Het eerste aantal knooppunt typen voor uw cluster 
* De eigenschappen van elk knooppunt type (grootte, aantal instanties, primair, Internet gericht, aantal Vm's, enzovoort)
* De betrouwbaarheid en duurzaamheid van de clusterkenmerken

### <a name="select-the-initial-number-of-node-types"></a>Het eerste aantal knooppunt typen selecteren
Eerst moet u nagaan wat het cluster is dat u maakt, wordt gebruikt voor. Welke soorten toepassingen wilt u in dit cluster implementeren? Biedt uw toepassing meerdere services en moeten hiervan een of meer service openbaar zijn of op internet gericht? Hebben uw services (waaruit uw toepassing bestaan) verschillende vereisten voor de infra structuur, zoals meer RAM-geheugen of hogere CPU-cycli? Een Service Fabric cluster kan bestaan uit meer dan één knooppunt type: een primair knooppunt type en een of meer niet-primaire knooppunt typen. Elk knooppunt type wordt toegewezen aan een virtuele-machine schaalset. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. [Knooppunt eigenschappen en plaatsings beperkingen][placementconstraints] kunnen worden ingesteld om specifieke services te beperken tot specifieke knooppunt typen.  Zie [service Fabric cluster capaciteit plannen](service-fabric-cluster-capacity.md)voor meer informatie.

### <a name="select-node-properties-for-each-node-type"></a>Knooppunt eigenschappen selecteren voor elk knooppunt type
In knooppunt typen worden de SKU, het nummer en de eigenschappen van de virtuele machine van de virtuele machines in de bijbehorende schaalset gedefinieerd.

De minimale grootte van Vm's voor elk knooppunt type wordt bepaald door de [duurzaamheids categorie][durability] die u hebt gekozen voor het knooppunt type.

Het minimale aantal Vm's voor het primaire knooppunt type wordt bepaald door de gekozen [betrouwbaarheids categorie][reliability] .

Zie de minimale aanbevelingen voor [primaire knooppunt typen](service-fabric-cluster-capacity.md#primary-node-type), [stateful werk belastingen op niet-primaire knooppunt](service-fabric-cluster-capacity.md#stateful-workloads)typen en [stateless workloads op niet-primaire knooppunt typen](service-fabric-cluster-capacity.md#stateless-workloads).

Meer dan het minimum aantal knoop punten moet zijn gebaseerd op het aantal replica's van de toepassing/services dat u wilt uitvoeren in dit knooppunt type.  [Capaciteits planning voor service Fabric-toepassingen](service-fabric-capacity-planning.md) helpt u bij het schatten van de resources die u nodig hebt om uw toepassingen uit te voeren. U kunt het cluster altijd hoger of lager schalen om de werk belasting van de toepassing te wijzigen. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Tijdelijke besturingssysteem schijven gebruiken voor schaal sets voor virtuele machines

*Tijdelijke besturingssysteem schijven* zijn opslag gemaakt op de lokale virtuele machine (VM) en worden niet opgeslagen op externe Azure Storage. Deze worden aanbevolen voor alle Service Fabric knooppunt typen (primair en secundair), omdat deze worden vergeleken met traditionele permanente besturingssysteem schijven, tijdelijke besturingssysteem schijven:

* Lees-en schrijf latentie beperken tot de besturingssysteem schijf
* Beheer bewerkingen voor het opnieuw instellen of terugzetten van knoop punten sneller uitvoeren
* De totale kosten verlagen (de schijven zijn gratis en er zijn geen extra opslag kosten in rekening gebracht)

Tijdelijke besturingssysteem schijven zijn geen specifieke Service Fabric-functie, maar in plaats daarvan een functie van de *virtuele-machine schaal sets* van Azure die zijn toegewezen aan service Fabric knooppunt typen. Voor het gebruik van deze met Service Fabric hebt u het volgende nodig in uw cluster Azure Resource Manager sjabloon:

1. Zorg ervoor dat de typen knoop punten [ondersteunde Azure VM-grootten](../virtual-machines/ephemeral-os-disks.md) voor tijdelijke besturingssysteem schijven opgeven en dat de grootte van de virtuele machine voldoende is voor de cache, zodat de grootte van de besturingssysteem schijf wordt ondersteund (Zie *Opmerking* hieronder). Bijvoorbeeld:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Zorg ervoor dat u een VM-grootte selecteert met een cache grootte die gelijk is aan of groter is dan de schijf grootte van het besturings systeem van de VM zelf, anders kan uw Azure-implementatie een fout veroorzaken (zelfs als deze in eerste instantie wordt geaccepteerd).

2. Een versie van een virtuele-machine schaalset ( `vmssApiVersion` ) van `2018-06-01` of hoger opgeven:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Geef in de sectie schaalset voor virtuele machines van uw implementatie sjabloon `Local` optie op voor `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Gebruikers toepassingen mogen geen afhankelijkheid/bestand/artefact hebben op de besturingssysteem schijf, omdat de besturingssysteem schijf verloren gaat in het geval van een upgrade van het besturings systeem.

> [!NOTE]
> Bestaande niet-tijdelijke VMSS kunnen niet in-place worden bijgewerkt om tijdelijke schijven te gebruiken.
> Als gebruikers wilt migreren, moeten ze een nieuw nodeType [toevoegen](./virtual-machine-scale-set-scale-node-type-scale-out.md) met tijdelijke schijven, de werk belastingen verplaatsen naar de nieuwe NodeType & het bestaande NodeType [verwijderen](./service-fabric-how-to-remove-node-type.md) .
>

Zie voor meer informatie en meer configuratie opties [kortstondige besturingssysteem schijven voor virtuele Azure-machines](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>De duurzaamheid en betrouwbaarheids niveaus voor het cluster selecteren
De laag duurzaamheid wordt gebruikt om aan het systeem de bevoegdheden aan te geven die uw virtuele machines hebben met de onderliggende Azure-infra structuur. In het primaire knooppunt type kan met deze bevoegdheid Service Fabric elke infrastructuur aanvraag op VM-niveau onderbreken (zoals het opnieuw opstarten van een VM, een VM-installatie kopie of een VM-migratie) die van invloed is op de quorum vereisten voor de systeem services en uw stateful Services. In de niet-primaire knooppunt typen kan met deze bevoegdheid Service Fabric alle infrastructuur aanvragen op VM-niveau (zoals opnieuw opstarten van de VM, VM-installatie kopie en VM-migratie) onderbreken die van invloed zijn op de quorum vereisten voor uw stateful Services.  Zie [de duurzaamheids kenmerken van het cluster][durability]voor meer voor delen van de verschillende niveaus en aanbevelingen voor het niveau dat moet worden gebruikt en wanneer.

De betrouwbaarheids categorie wordt gebruikt om het aantal replica's in te stellen van de systeem services die u wilt uitvoeren in dit cluster op het primaire knooppunt type. Hoe langer het aantal replica's, hoe betrouwbaarder de systeem services zijn in uw cluster.  Zie [de betrouw baarheids kenmerken van het cluster][reliability]voor meer voor delen van de verschillende niveaus en aanbevelingen voor het niveau dat moet worden gebruikt en wanneer. 

## <a name="enable-reverse-proxy-andor-dns"></a>Omgekeerde proxy en/of DNS inschakelen
Services die met elkaar in een cluster verbinding maken, hebben doorgaans rechtstreeks toegang tot de eind punten van andere services, omdat de knoop punten in een cluster zich op hetzelfde lokale netwerk bevinden. Service Fabric biedt extra services: een [DNS-service](service-fabric-dnsservice.md) en een [reverse proxy service](service-fabric-reverseproxy.md)om het maken van verbinding tussen services gemakkelijker te maken.  Beide services kunnen worden ingeschakeld wanneer u een cluster implementeert.

Omdat veel services, met name in container Services, een bestaande URL-naam kunnen hebben, kunt u deze omzetten met het standaard-DNS-protocol (in plaats van het Naming Service-Protocol), met name in de scenario's ' lift and Shift '. Dit is precies wat de DNS-service doet. Hiermee kunt u DNS-namen toewijzen aan een service naam en daarom IP-adres van het eind punt omzetten.

De services voor omgekeerde proxy adressen in het cluster die HTTP-eind punten (inclusief HTTPS) beschikbaar stellen. De omgekeerde proxy vereenvoudigt het aanroepen van andere services door een specifieke URI-indeling op te geven.  De omgekeerde proxy zorgt er ook voor dat de stappen voor probleem oplossing, verbinding maken en opnieuw proberen voor één service worden uitgevoerd om met elkaar te communiceren.

## <a name="prepare-for-disaster-recovery"></a>Voorbereiden op herstel na noodgeval
Een essentieel onderdeel van het leveren van hoge Beschik baarheid zorgt ervoor dat Services alle verschillende soorten storingen kunnen overlaten. Dit is vooral belang rijk voor storingen die ongepland zijn en buiten uw besturings element vallen. [Voor bereiding op herstel na nood gevallen](service-fabric-disaster-recovery.md) worden enkele veelvoorkomende fout modi beschreven die nood gevallen kunnen zijn als ze niet correct worden gemodelleerd en beheerd. Er worden ook oplossingen en acties beschreven die moeten worden uitgevoerd als er toch een nood geval is opgetreden.

## <a name="production-readiness-checklist"></a>Controlelijst voor productiegereedheid
Is uw toepassing en cluster klaar om productie verkeer te nemen? Voordat u uw cluster implementeert voor productie, voert u de [controle lijst voor productie gereedheids](service-fabric-production-readiness-checklist.md)uit. Zorg ervoor dat uw toepassing en cluster probleemloos werken door de items in deze controle lijst te door lopen. We raden u ten zeerste aan om al deze items uit te checken voordat u naar productie gaat.

## <a name="next-steps"></a>Volgende stappen
* [Een Service Fabric cluster maken waarop Windows wordt uitgevoerd](./service-fabric-best-practices-security.md)
* [Een Service Fabric cluster maken waarop Linux wordt uitgevoerd](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster