---
title: Distributie op Azure VMware-oplossing implementeren
description: Meer informatie over het implementeren van VMware horizon op de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 6d5d8e12e358e2289128af9840660be18f5f217a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537438"
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

In de tabellen ziet u de algemene werk belastingen voor aanmelding VSI werk belastingen van werk nemers en Power werk-workloads.

#### <a name="knowledge-worker-workloads"></a>Werk belastingen van kennis werk nemer

:::image type="content" source="media/horizon/common-vdi-profiles-vsi-workloads-knowledge.png" alt-text="Tabel met algemene VDI-profielen voor VMware-horizon voor aanmelding VSI kennis werk-workloads" lightbox="media/horizon/common-vdi-profiles-vsi-workloads-knowledge.png" border="false":::

#### <a name="power-worker-workloads"></a>Power work-workloads

:::image type="content" source="media/horizon/common-vdi-profiles-vsi-workloads-power.png" alt-text="Tabel met algemene VDI-profielen voor VMware-horizon voor aanmelding VSI Power work-workloads" lightbox="media/horizon/common-vdi-profiles-vsi-workloads-power.png" border="false":::

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

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Kosten van de VM-Vm's in azure Virtual Network

Op basis van de architectuur van de standaard implementatie, bestaan de Vm's van de horizon-infra structuur uit verbindings servers, UAGs, app-volume managers. Ze worden geïmplementeerd in de Azure-Virtual Network van de klant. Extra Azure native instanties zijn vereist ter ondersteuning van de services voor hoge Beschik baarheid (HA), micro soft SQL of micro soft Active Directory (AD) in Azure. De tabel geeft een overzicht van de Azure-exemplaren op basis van een voor beeld van een implementatie van 2.000-desktop. 

>[!NOTE]
>Als u storingen wilt kunnen afhandelen, implementeert u een andere server dan is vereist voor het aantal verbindingen (n + 1). Het mini maal aanbevolen aantal exemplaren van de verbindings server, het UAG-en app-volume beheer is 2, en het vereiste aantal zal toenemen op basis van de hoeveelheid gebruikers die de omgeving ondersteunt.  Eén verbindings server ondersteunt Maxi maal 4.000 sessies, hoewel 2.000 wordt aanbevolen als best practice. Maxi maal zeven verbindings servers worden ondersteund per pod met een aanbeveling van 12.000 actieve sessies in totaal per pod. Zie het Knowledge Base-artikel VMware horizon-KB- [formaat limieten en aanbevelingen](https://kb.vmware.com/s/article/2150348)voor de meest actuele aantallen.

| Onderdeel van de horizon-infra structuur | Azure-exemplaar | Aantal exemplaren vereist (voor 2.000-Desk tops)    | Opmerking  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Verbindings server                | D4sv3          | 2       | *Zie de opmerking hierboven*                         |    
| UAG                              | F2sv2          | 2       | *Zie de opmerking hierboven*                         |
| App-volume beheer              | D4sv3          | 2       | *Zie de opmerking hierboven*                         |
| Cloud connector                  | D4sv3          | 1       |                                          |
| AD-controller                    | D4sv3          | 2       | *Optie voor het gebruik van MSFT AD service op Azure* |
| MS-SQL Database                  | D4sv3          | 2       | *Optie voor het gebruik van SQL service op Azure*     |
| Windows-bestands share               | D4sv3          |         | *Optioneel*                               |

De kosten voor de VM van de infra structuur zijn \$ 0,36 per gebruiker per maand voor de 2.000-desktop implementatie in bovenstaand voor beeld. In dit voor beeld wordt de prijs van juni 2020 van het US Oost Azure-exemplaar gebruikt. Uw prijzen kunnen variëren, afhankelijk van de regio, opties geselecteerd en timing.

## <a name="next-steps"></a>Volgende stappen
Lees de [Veelgestelde vragen](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)over VMware-horizon voor meer informatie over VMware-horizon op de Azure VMware-oplossing.
