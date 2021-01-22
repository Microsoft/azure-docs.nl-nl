---
title: Distributie op Azure VMware-oplossing implementeren
description: Meer informatie over het implementeren van VMware horizon op de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 2cf6fc5cb7662188650365cb019774d6c778d405
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684872"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Distributie op Azure VMware-oplossing implementeren 

>[!NOTE]
>Dit document is gericht op het VMware-horizon product, voorheen bekend als horizon-7. Horizon is een andere oplossing dan een horizon-Cloud op Azure, hoewel er een aantal gedeelde onderdelen zijn. De belangrijkste voor delen van de Azure VMware-oplossing zijn een meer eenvoudige grootte methode en de integratie van VMware Cloud Foundation Management in de Azure Portal.

[VMware-Horizon](https://www.vmware.com/products/horizon.html)®, een virtueel bureau blad-en toepassings platform, wordt uitgevoerd in het Data Center en biedt eenvoudig en gecentraliseerd beheer. Het levert virtuele Bureau bladen en toepassingen op elk apparaat, waar dan ook. Met horizon kunt u verbindingen maken en brokeren met virtuele Windows-en Linux-Bureau bladen, op Extern bureaublad server (RDS) gehoste toepassingen, Desk tops en fysieke machines.

Hier Best Eden we aandacht aan het implementeren van Horizon in azure VMware-oplossing. Voor algemene informatie over VMware horizon raadpleegt u de horizon productie documentatie:

* [Wat is VMware horizon?](https://www.vmware.com/products/horizon.html)

* [Meer informatie over VMware horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Referentie architectuur van Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Met de inleiding over de Azure VMware-oplossing zijn er nu twee VDI-oplossingen (Virtual Desktop Infrastructure) op het Azure-platform. In het volgende diagram vindt u een overzicht van de belangrijkste verschillen op hoog niveau.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizon in azure VMware-oplossing en horizon-Cloud op Azure" border="false":::

Horizon 2006 en latere versies op de regel van de horizon-versie 8 bieden ondersteuning voor zowel on-premises implementatie als implementatie van Azure VMware-oplossingen. Er zijn een paar van de functies die on-premises maar niet in de Azure VMware-oplossing worden ondersteund. Aanvullende producten in het Horizon-ecosysteem worden ook ondersteund. Zie [pariteit en interoperabiliteit van functies](https://kb.vmware.com/s/article/80850)voor voor meer informatie.

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Een periode implementeren in een hybride Cloud

U kunt horizon in een hybride cloud omgeving implementeren wanneer u pod-architectuur (CPA) van de Cloud gebruikt om on-premises en Azure-data centers te verbinden. CPA schaalt uw implementatie, bouwt een hybride Cloud en biedt redundantie voor bedrijfs continuïteit en herstel na nood gevallen.  Zie voor meer informatie [bestaande horizon-7-omgevingen uitbreiden](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA is geen uitgerekte implementatie. elke horizon Pod is DISTINCT en alle verbindings servers die deel uitmaken van elk van de afzonderlijke peulen, moeten zich op één locatie bevinden en op hetzelfde broadcast-domein worden uitgevoerd vanuit een netwerk perspectief.

Net zoals on-premises of persoonlijk Data Center, kan horizon worden geïmplementeerd in een privécloud van Azure VMware-oplossingen. We bespreken belang rijke verschillen in de implementatie van Horizon-premises en de Azure VMware-oplossing in de volgende secties.

De privécloud van Azure is conceptueel hetzelfde als de VMware-SDDC, een term die meestal in de horizon-documentatie wordt gebruikt. De rest van dit document maakt gebruik van de termen Azure Private Cloud en VMware SDDC-uitwisselbaar.

De horizon-Cloud connector is vereist voor Horizon-Azure VMware-oplossing voor het beheren van abonnements licenties. Cloud connector kan worden geïmplementeerd in azure Virtual Network naast de andere horizon-verbinding servers.

>[!IMPORTANT]
>De ondersteuning voor Horizon besturings vlak voor Horizon op Azure VMware-oplossing is nog niet beschikbaar. Zorg ervoor dat u de VHD-versie van de horizon-Cloud Connector downloadt.

## <a name="vcenter-cloud-admin-role"></a>rol van vCenter-Cloud beheerder

Omdat de Azure VMware-oplossing een SDDC-service is en Azure de levens cyclus van de SDDC op de Azure VMware-oplossing beheert, wordt het vCenter-machtigings model voor de Azure VMware-oplossing beperkt door het ontwerp.

Klanten moeten de rol van Cloud beheerder gebruiken, die een beperkt aantal vCenter-machtigingen heeft. Het product is gewijzigd in samen werking met de rol van Cloud beheerder in de Azure VMware-oplossing, met name:

* Het inrichten van Instant klonen is gewijzigd om te worden uitgevoerd op de Azure VMware-oplossing.

* Er is een specifiek vSAN-beleid (VMware_Horizon) gemaakt op de Azure VMware-oplossing om met de horizon te werken. deze moet beschikbaar zijn en worden gebruikt in de SDDCs die voor de horizon worden geïmplementeerd.

* vSphere Lees cache (CBRC), ook wel bekend als weer geven, is uitgeschakeld wanneer het wordt uitgevoerd op de Azure VMware-oplossing.

>[!IMPORTANT]
>CBRC mag niet opnieuw worden ingeschakeld.

>[!NOTE]
>Met Azure VMware-oplossing worden specifieke instellingen voor de horizon automatisch geconfigureerd zolang u horizon 2006 (ook wel horizon 8) en hoger op de vertakking van de horizon-8 implementeert en selecteert u de optie **Azure** in het installatie programma van de horizon-verbinding server.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Distributie architectuur van de Azure VMware-oplossing

Een typische horizon architectuur ontwerp maakt gebruik van een pod-en blokkerings strategie. Een blok is één vCenter, terwijl meerdere blokken gecombineerd een pod maken. Een horizon-Pod is een organisatie-eenheid, bepaald door de limieten voor Horizon taal schalen. Elke horizon-Pod heeft een afzonderlijke beheer Portal, waardoor een standaard ontwerp praktijk het aantal peulen zo klein mogelijk maakt.

Elke Cloud heeft een eigen schema voor netwerk connectiviteit. In combi natie met VMware SDDC Networking/NSX Edge biedt de Azure VMware-oplossing voor netwerk connectiviteit unieke vereisten voor het implementeren van Horizon land dat verschilt van on-premises.

Elke Azure Private Cloud en SDDC kunnen 4.000-bureaublad-of toepassings sessies afhandelen, aangenomen:

* Het werk belasting verkeer wordt uitgelijnd met het LoginVSI-taak werknemers profiel.

* Alleen protocol verkeer wordt beschouwd, geen gebruikers gegevens.

* NSX Edge is geconfigureerd om groot te zijn.

>[!NOTE]
>Uw werkbelasting profiel en de behoeften kunnen afwijken en daarom kunnen de resultaten variëren op basis van uw use-case. Volumes met gebruikers gegevens kunnen de schaal limieten in de context van uw werk belasting verlagen. Grootte en plan uw implementatie dienovereenkomstig. Zie de richt lijnen voor het aanpassen van de [grootte van Azure VMware Solution-hosts voor Horizon-implementaties](#size-azure-vmware-solution-hosts-for-horizon-deployments) voor meer informatie.

Op basis van de limiet van Azure Private Cloud en SDDC is het raadzaam een implementatie architectuur te gebruiken waarin de horizon-verbindings servers en VMware Unified Access gateways (UAGs) worden uitgevoerd binnen de Azure-Virtual Network. Hiermee worden elke Azure-privécloud en SDDC in een blok omgezet. Maximaliseer vervolgens de schaal baarheid van de periode die wordt uitgevoerd op de Azure VMware-oplossing.

De verbinding van Azure Virtual Network met Azure private clouds/SDDCs moet worden geconfigureerd met ExpressRoute FastPath. In het volgende diagram ziet u een eenvoudige implementatie van Horizon pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Typische implementatie van Horizon pod met ExpressPath snel pad" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Netwerk connectiviteit voor schaal horizon op Azure VMware-oplossing

In deze sectie wordt de netwerk architectuur op hoog niveau beschreven met enkele veelvoorkomende implementatie voorbeelden, waarmee u de schaal van de Azure VMware-oplossing horizon keurig kunt schalen. De focus is specifiek op essentiële netwerk elementen. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Enkelvoudige pod op de Azure VMware-oplossing

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Enkelvoudige pod op de Azure VMware-oplossing" border="false":::

Eén horizon Pod is het meest rechtse implementatie scenario omdat u slechts één Horizon pod in de regio VS Oost implementeert.  Omdat elke privécloud en SDDC wordt geschat voor het afhandelen van 4.000-bureaublad sessies, implementeert u de maximale grootte van pod.  U kunt de implementatie van Maxi maal drie privé Clouds/SDDCs plannen.

Met de horizon-VM-machines (Vm's) die zijn geïmplementeerd in azure Virtual Network, kunt u de 12.000-sessies per pod bereiken. De verbinding tussen elke privécloud en SDDC met Azure Virtual Network is ExpressRoute snel pad.  Er is geen Oost-West-verkeer tussen persoonlijke Clouds nodig. 

Voor beelden van belang rijke hypo Thesen voor dit basis implementatie zijn:

* U hebt geen on-premises horizon pod die u wilt verbinden met deze nieuwe pod met behulp van Cloud pod Architecture (CPA).

* Eind gebruikers maken verbinding met hun virtuele Bureau bladen via internet (versus verbinding maken via een on-premises Data Center).

U verbindt uw AD-domein controller in azure Virtual Network met uw on-premises AD via een VPN-of ExpressRoute-circuit.

Een variant op het eenvoudige voor beeld is mogelijk om connectiviteit te ondersteunen voor on-premises resources. Gebruikers hebben bijvoorbeeld toegang tot Bureau bladen en genereren het verkeer van een virtueel-bureaublad toepassing of maken verbinding met een on-premises horizon pod met behulp van CPA.

In het diagram ziet u hoe connectiviteit voor on-premises resources wordt ondersteund. Als u verbinding wilt maken met het bedrijfs netwerk met Azure Virtual Network, hebt u een ExpressRoute-circuit nodig.  U moet ook verbinding maken met uw bedrijfs netwerk met elk van de privécloud en SDDCs met behulp van ExpressRoute Global Reach.  Hiermee kan de verbinding van de SDDC met het ExpressRoute-circuit en on-premises resources. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Verbind uw bedrijfs netwerk met een Azure-Virtual Network" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Meerdere horizon-peulen voor Azure VMware-oplossing in meerdere regio's

Een ander scenario is horizon taal schalen over meerdere peulen.  In dit scenario implementeert u twee Horizons-peul in twee verschillende regio's en vergelijkt u ze met CPA. Dit is vergelijkbaar met de netwerk configuratie in het vorige voor beeld, maar met een aantal aanvullende cross-regionale koppelingen. 

U verbindt de Azure-Virtual Network in elke regio met de persoonlijke Clouds/SDDCs in de andere regio. Het maakt het mogelijk dat het onderdeel van de CPA-Federatie verbinding maakt met alle Bureau bladen onder beheer. Door extra persoonlijke Clouds/SDDCs toe te voegen aan deze configuratie kunt u in totaal 24.000-sessies schalen. 

Dezelfde principes zijn van toepassing als u twee Horizons-peulen in dezelfde regio implementeert.  Zorg ervoor dat u de tweede horizon pod implementeert in een *afzonderlijke Azure-Virtual Network*. Net als bij het single pod-voor beeld kunt u uw bedrijfs netwerk en on-premises pod verbinding laten maken met dit voor beeld van een multi-pod/regio met behulp van ExpressRoute en Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Meerdere horizon-peulen voor Azure VMware-oplossing in meerdere regio's" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Grootte van Azure VMware Solution-hosts voor Horizon-implementaties 

De schaal methodologie van de horizon op een host die wordt uitgevoerd in de Azure VMware-oplossing is eenvoudiger dan horizon-premises.  Dat komt doordat de host van de Azure VMware-oplossing is gestandaardiseerd.  Nauw keurige host-grootte helpt bij het bepalen van het aantal hosts dat nodig is voor de ondersteuning van uw VDI-vereisten.  Het is een centrale oplossing om de kosten per Desktop te bepalen.

### <a name="sizing-tables"></a>Grootte van tabellen

Specifieke vereisten voor vCPU/vRAM voor Horizon virtuele Bureau bladen zijn afhankelijk van het specifieke werkbelasting Profiel van de klant.   Werk samen met uw MSFT-en VMware-verkoop team om uw vCPU/vRAM-vereisten voor uw virtuele Bureau bladen te bepalen. 

| vCPU per VM | vRAM per VM (GB) | Exemplaar | 100 Vm's | 200 Vm's | 300 Vm's | 400 Vm's | 500 Vm's | 600 Vm's | 700 Vm's | 800 Vm's | 900 Vm's | 1000 Vm's | 2000 Vm's | 3000 Vm's | 4000 Vm's | 5000 Vm's | 6000 Vm's | 6400 Vm's |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Invoer van Horizon formaat

U moet voor uw geplande workload het volgende verzamelen:

* Aantal gelijktijdige Bureau bladen

* Vereist vCPU per bureau blad

* Vereist vRAM per Desktop

* Vereiste opslag per Desktop

Over het algemeen zijn VDI-implementaties ofwel CPU-of RAM-geheugen, waarmee de grootte van de host wordt bepaald. Laten we het volgende voor beeld volgen voor een LoginVSI van de werk belasting, gevalideerd met prestatie tests:

* 2.000 gelijktijdige Desktop implementatie

* 2vCPU per bureau blad.

* 4-GB vRAM per bureau blad.

* 50 GB aan opslag per Desktop

In dit voor beeld wordt het totale aantal hosts gemeten tot 18, wat resulteert in een VM-per-host-densiteit van 111.

>[!IMPORTANT]
>De werk belasting van klanten is afhankelijk van dit voor beeld van een LoginVSI-kennis werker. Als onderdeel van het plannen van uw implementatie, werkt u met uw VMware EUC-SEs voor uw specifieke grootte-en prestatie behoeften. Zorg ervoor dat u uw eigen prestatie tests uitvoert met behulp van de werkelijke geplande werk belasting voordat u de grootte van de host afrondt en dienovereenkomstig bijwerkt.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizonal op Azure VMware-oplossings licenties 

Er zijn vier onderdelen voor de totale kosten van het uitvoeren van een Azure VMware-oplossing. 

### <a name="azure-vmware-solution-capacity-cost"></a>Capaciteits kosten van Azure VMware-oplossing

Zie de pagina met [prijzen voor Azure VMware-oplossingen](https://azure.microsoft.com/pricing/details/azure-vmware/) voor meer informatie over de prijzen.

### <a name="horizon-licensing-cost"></a>Kosten van Horizon-licentie verlening

Er zijn twee beschik bare licenties voor gebruik met de Azure VMware-oplossing. Dit kan gelijktijdige gebruiker (CCU) of een benoemde gebruiker (NU) zijn:

* Licentie voor een horizon-abonnement

* De licentie voor een universele abonnement

Als er slechts een distributie op Azure VMware-oplossing voor de nabije toekomst wordt geïmplementeerd, gebruikt u de licentie voor het gebruik van een horizon-abonnement.

Als het is geïmplementeerd in de Azure VMware-oplossing en on-premises, net als bij een gebruiks aanvraag voor herstel na nood gevallen, kiest u de licentie voor een Horizonal universele abonnement. Het bevat een vSphere-licentie voor on-premises implementatie, zodat deze een hogere prijs heeft.

Werk samen met uw VMware EUC-verkoop team om de Cloud kosten te bepalen op basis van uw behoeften.

### <a name="azure-instance-types"></a>Typen Azure-instanties

Raadpleeg de richt lijnen voor VMware die u [hier](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)kunt vinden voor meer informatie over de grootte van Azure virtual machines die zijn vereist voor de horizon-infra structuur.

## <a name="next-steps"></a>Volgende stappen
Lees de [Veelgestelde vragen](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)over VMware-horizon voor meer informatie over VMware-horizon op de Azure VMware-oplossing.
