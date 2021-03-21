---
title: Overzicht van voor Azure toegewezen hosts voor virtuele machines
description: Meer informatie over hoe met Azure toegewezen hosts kunnen worden gebruikt voor het implementeren van virtuele machines.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667350"
---
# <a name="azure-dedicated-hosts"></a>Met Azure toegewezen hosts

De toegewezen Azure-host is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in onze data centers als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.


## <a name="benefits"></a>Voordelen 

Het reserveren van de hele host biedt de volgende voor delen:

-   Hardwarematige isolatie op het niveau van de fysieke server. Er worden geen andere Vm's op uw hosts geplaatst. Toegewezen hosts worden in dezelfde data centers geïmplementeerd en delen hetzelfde netwerk en onderliggende opslag infrastructuur als andere, niet-geïsoleerde hosts.
-   Controle over onderhouds gebeurtenissen geïnitieerd door het Azure-platform. Hoewel het meren deel van de onderhouds gebeurtenissen weinig gevolgen heeft voor uw virtuele machines, zijn er enkele gevoelige werk belastingen waarbij elke seconde van de onderbreking een impact kan hebben. Met toegewezen hosts kunt u zich aanmelden bij een onderhouds venster om de gevolgen voor uw service te verminderen.
-   Met Azure Hybrid voordelen kunt u uw eigen licenties voor Windows en SQL naar Azure brengen. Het gebruik van de hybride voor delen biedt extra voor delen. Zie [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)voor meer informatie.


## <a name="groups-hosts-and-vms"></a>Groepen, hosts en Vm's  

![Weer gave van de nieuwe resources voor toegewezen hosts.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Een **hostgroep** is een resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheids zone en voegt hierop hosts toe.

Een **host** is een resource die is toegewezen aan een fysieke server in een Azure-Data Center. De fysieke server wordt toegewezen wanneer de host wordt gemaakt. Er wordt een host gemaakt binnen een hostgroep. Een host heeft een SKU waarin wordt beschreven welke VM-grootten er kunnen worden gemaakt. Elke host kan meerdere Vm's hosten, van verschillende grootten, zolang ze afkomstig zijn uit dezelfde grootte reeks.


## <a name="high-availability-considerations"></a>Overwegingen voor hoge Beschik baarheid 

Voor maximale Beschik baarheid implementeert u meerdere Vm's, verspreid over meerdere hosts (mini maal 2). Met Azure dedicated hosts hebt u verschillende opties om uw infra structuur in te richten op de grenzen van de isolatie van fouten.

### <a name="use-availability-zones-for-fault-isolation"></a>Beschikbaarheidszones gebruiken voor fout isolatie

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Er wordt een hostgroep gemaakt in één beschikbaarheids zone. Nadat deze is gemaakt, worden alle hosts in die zone geplaatst. Als u een hoge Beschik baarheid in meerdere zones wilt bezorgen, moet u meerdere hostgroepen maken (één per zone) en de hosts vervolgens dienovereenkomstig verspreiden.

Als u een hostgroep toewijst aan een beschikbaarheids zone, moeten alle Vm's die op die host worden gemaakt, in dezelfde zone worden gemaakt.

### <a name="use-fault-domains-for-fault-isolation"></a>Fout domeinen gebruiken voor fout isolatie

Een host kan worden gemaakt in een specifiek fout domein. Net als bij een VM in een schaalset of beschikbaarheidsset worden hosts in verschillende fout domeinen op verschillende fysieke racks geplaatst in het Data Center. Wanneer u een hostgroep maakt, moet u het aantal fout domeinen opgeven. Wanneer u hosts in de hostgroep maakt, wijst u fout domein toe voor elke host. Voor de virtuele machines is geen fout domein toewijzing vereist.

Fout domeinen zijn niet hetzelfde als een co-locatie. Als u hetzelfde fout domein voor twee hosts gebruikt, betekent dit niet dat ze zich in de buurt van elkaar bevinden.

Fout domeinen zijn binnen het bereik van de hostgroep. U moet geen veronderstelling doen over de anti-affiniteit tussen twee hostgroepen (tenzij ze zich in verschillende beschikbaarheids zones bevinden).

Vm's die zijn geïmplementeerd op hosts met verschillende fout domeinen, hebben hun onderliggende services voor beheerde schijven op meerdere opslag tempels om de fout isolatie beveiliging te verg Roten.

### <a name="using-availability-zones-and-fault-domains"></a>Beschikbaarheidszones en fout domeinen gebruiken

U kunt beide mogelijkheden samen gebruiken om nog meer fouten te isoleren. In dit geval geeft u de beschikbaarheids zone en het aantal fout domeinen op voor elke hostgroep, wijst u een fout domein toe aan elk van uw hosts in de groep en wijst u een beschikbaarheids zone toe aan elke virtuele machine.

De Resource Manager-voorbeeld sjabloon die [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) wordt aangetroffen, maakt gebruik van zones en fout domeinen om hosts te verspreiden voor maximale tolerantie in een regio.


## <a name="manual-vs-automatic-placement"></a>Hand matig versus automatische plaatsing 

Wanneer u een virtuele machine in azure maakt, kunt u selecteren welke specifieke host u wilt gebruiken. U kunt ook de optie gebruiken om uw virtuele machines automatisch te plaatsen op bestaande hosts binnen een hostgroep. 

Wanneer u een nieuwe hostgroep maakt, moet u ervoor zorgen dat de instelling voor automatische VM-plaatsing is geselecteerd. Wanneer u een virtuele machine maakt, selecteert u de hostgroep en laat u Azure de beste host voor uw virtuele machine kiezen. 

Voor hostgroepen waarvoor automatische plaatsing is ingeschakeld, hoeven niet alle Vm's automatisch te worden geplaatst. U kunt nog steeds expliciet een host kiezen, zelfs als automatische plaatsing is geselecteerd voor de hostgroep. 

### <a name="limitations"></a>Beperkingen

Bekende problemen en beperkingen bij het gebruik van automatische plaatsing van VM'S:

- U kunt geen hybride voor delen van Azure Toep assen op uw toegewezen hosts.
- U kunt uw virtuele machine niet opnieuw implementeren. 
- U kunt geen Lsv2-, NVasv4-, NVsv3-, Msv2-of M-serie Vm's met toegewezen hosts gebruiken 


## <a name="virtual-machine-scale-set-support"></a>Ondersteuning voor schaal sets voor virtuele machines

Met schaal sets voor virtuele machines kunt u een groep virtuele machines behandelen als één resource en beschik baarheid, beheer, schalen en indelings beleid Toep assen als groep. Uw bestaande gereserveerde hosts kunnen ook worden gebruikt voor virtuele-machine schaal sets. 

Bij het maken van een schaalset voor virtuele machines kunt u een bestaande hostgroep opgeven zodat alle VM-exemplaren op specifieke hosts worden gemaakt.

De volgende vereisten zijn van toepassing bij het maken van een schaalset voor virtuele machines in een specifieke hostgroep:

- Automatische plaatsing van VM'S moet worden ingeschakeld.
- De beschikbaarheids instelling van uw hostgroep moet overeenkomen met uw schaalset. 
    - Een regionale hostgroep (gemaakt zonder een beschikbaarheids zone op te geven) moet worden gebruikt voor regionale schaal sets.
    - De hostgroep en de schaalset moeten dezelfde beschikbaarheids zone gebruiken. 
    - Het aantal fout domeinen voor het niveau van de hostgroep moet overeenkomen met het aantal fout domeinen voor uw schaalset. Met de Azure Portal kunt u de *maximale sprei ding* voor uw schaalset opgeven, waarmee het aantal fout domeinen van 1 wordt ingesteld.
- U moet eerst toegewezen hosts maken, met voldoende capaciteit en dezelfde instellingen voor schaal sets en fout domeinen.
- De ondersteunde VM-grootten voor uw toegewezen hosts moeten overeenkomen met de grootte die voor uw schaalset wordt gebruikt.

Niet alle instellingen voor indelings-en optimalisatie van schaal sets worden ondersteund door toegewezen hosts. Pas de volgende instellingen toe op uw schaalset: 
- Overinrichting wordt niet aanbevolen en is standaard uitgeschakeld. U kunt overinrichting inschakelen, maar de toewijzing van de schaalset mislukt als de hostgroep geen capaciteit heeft voor alle virtuele machines, met inbegrip van de overingerichte instanties. 
- De Orchestration-modus ScaleSetVM gebruiken 
- Gebruik geen proximity-plaatsings groepen voor co-locatie



## <a name="maintenance-control"></a>Onderhoudsbeheer

De infra structuur die uw virtuele machines ondersteunt, kan af en toe worden bijgewerkt om de betrouw baarheid, prestaties en beveiliging te verbeteren en om nieuwe functies te kunnen starten. Het Azure-platform probeert de impact van platform onderhoud zoveel mogelijk te minimaliseren, maar klanten met *onderhoud gevoelige* workloads kunnen zelfs enkele seconden niet verdragen dat de virtuele machine moet worden bevroren of ontkoppeld voor onderhoud.

**Onderhouds beheer** biedt klanten een optie voor het overs laan van regel matige platform updates die zijn gepland op hun toegewezen hosts en pas deze toe op het tijdstip van hun keuze in een voortschrijdende periode van 35 dagen. Binnen het onderhouds venster kunt u het beheer direct op het niveau van de host Toep assen, in een wille keurige volg orde. Zodra het onderhouds venster is afgelopen, wordt door micro soft het wachtend onderhoud op de hosts toegepast in een bestelling die mogelijk niet voldoet aan de door de gebruiker gedefinieerde fout domeinen.

Zie [platform updates beheren met onderhouds beheer](./maintenance-control.md)voor meer informatie.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

Zodra een toegewezen host is ingericht, wijst Azure deze toe aan de fysieke server. Hiermee wordt de beschik baarheid van de capaciteit gegarandeerd wanneer u uw virtuele machine moet inrichten. Azure gebruikt de volledige capaciteit in de regio (of zone) om een fysieke server voor uw host te kiezen. Het betekent ook dat klanten kunnen verwachten dat ze hun toegewezen host footprint kunnen verg Roten zonder dat er bijna geen ruimte meer is in het cluster.

## <a name="quotas"></a>Quota

Er zijn twee typen quota die worden gebruikt wanneer u een toegewezen host implementeert.

1. Quotum voor toegewezen host-vCPU. De standaard quotum limiet is 3000 Vcpu's, per regio.
1. Quotum voor VM-grootte familie. Een abonnement op basis van **betalen per gebruik** heeft bijvoorbeeld alleen een quotum van 10 vcpu's beschikbaar voor de Dsv3 grootte serie, in de regio VS-Oost. Als u een Dsv3-specifieke host wilt implementeren, moet u een quotum verhoging tot ten minste 64 Vcpu's aanvragen voordat u de toegewezen host kunt implementeren. 

Als u een quotum toename wilt aanvragen, maakt u een ondersteunings aanvraag in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Bij het inrichten van een toegewezen host worden zowel een toegewezen host-vCPU als het vCPU quotum van de VM-serie gebruikt, maar wordt de regionale vCPU niet verbruikt.


![Scherm afbeelding van de pagina gebruik en quota's in de portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Zie [vCPU quota's voor virtuele machines](./windows/quotas.md)voor meer informatie.

De gratis proef versie en MSDN-abonnementen hebben geen quota voor voor Azure toegewezen hosts.

## <a name="pricing"></a>Prijzen

Gebruikers worden in rekening gebracht per toegewezen host, ongeacht het aantal Vm's dat is geïmplementeerd. In uw maandelijkse afschriften ziet u een nieuw Factureerbaar resource type van hosts. De Vm's op een toegewezen host worden nog steeds weer gegeven in uw instructie, maar de prijs is 0.

De prijs van de host wordt ingesteld op basis van de VM-familie, type (hardware-grootte) en regio. De prijs van een host is relatief ten opzichte van de grootste VM-grootte die op de host wordt ondersteund.

Software licentie verlening, opslag en netwerk gebruik worden afzonderlijk van de host en Vm's gefactureerd. Deze factureer bare items worden niet gewijzigd.

Zie [prijzen voor toegewezen hosts voor Azure](https://aka.ms/ADHPricing)voor meer informatie.

U kunt ook besparen op kosten met een [gereserveerd exemplaar van voor Azure toegewezen hosts](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Grootten en hardware gegenereerd

Er wordt een SKU gedefinieerd voor een host en deze vertegenwoordigt de VM-grootte reeks en-type. U kunt meerdere virtuele machines van verschillende grootten op één host combi neren, zolang ze dezelfde grootte reeks hebben. 

Het *type* is het genereren van hardware. Verschillende typen hardware voor dezelfde VM-serie zijn van verschillende CPU-leveranciers en hebben verschillende CPU-generaties en aantal kernen. 

De grootten en typen hardware variëren per regio. Raadpleeg de pagina met [prijzen](https://aka.ms/ADHPricing) voor de host voor meer informatie.

> [!NOTE]
> Zodra een toegewezen host is richten, kunt u de grootte of het type niet wijzigen. Als u een ander type grootte nodig hebt, moet u een nieuwe host maken.  

## <a name="host-life-cycle"></a>Levens cyclus van host


Azure bewaakt en beheert de integriteits status van uw hosts. De volgende statussen worden geretourneerd wanneer u een query op uw host uitvoert:

| Status   | Beschrijving       |
|----------|----------------|
| Host beschikbaar     | Er zijn geen bekende problemen met de host.   |
| Host wordt onderzocht  | Er zijn enkele problemen met de host die we op zoek zijn. Dit is een overgangs status die vereist is voor Azure om het bereik en de hoofd oorzaak voor het geïdentificeerde probleem te identificeren. Voor virtuele machines die op de host worden uitgevoerd, kan dit gevolgen hebben. |
| Toewijzing in afwachting van host   | Azure kan de host niet herstellen naar een goede status en u vraagt u uw virtuele machines opnieuw te implementeren vanaf deze host. Als `autoReplaceOnFailure` is ingeschakeld, worden uw virtuele machines *retoucheerd* naar goede hardware. Anders kan de virtuele machine worden uitgevoerd op een host die op het punt staat om te mislukken.|
| Toewijzing van host is ongedaan gemaakt  | Alle virtuele machines zijn van de host verwijderd. Er worden geen kosten meer in rekening gebracht voor deze host omdat de hardware niet is gedraaid.   |


## <a name="next-steps"></a>Volgende stappen

- U kunt een toegewezen host implementeren met behulp van [Azure PowerShell](./windows/dedicated-hosts-powershell.md), de [Portal](./dedicated-hosts-portal.md)en [Azure cli](./linux/dedicated-hosts-cli.md).

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook besparen op kosten met een [gereserveerd exemplaar van voor Azure toegewezen hosts](prepay-dedicated-hosts-reserved-instances.md).
