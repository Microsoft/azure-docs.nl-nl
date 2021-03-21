---
title: Back-end-en back-end-Pools in azure front-deur | Microsoft Docs
description: In dit artikel wordt beschreven welke back-end-en back-endservers zich in de voor deur bevinden.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449290"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Back-end-en back-end-Pools in azure front-deur
In dit artikel wordt beschreven hoe u de implementatie van uw webtoepassing met behulp van de voor deur van Azure toewijst. Ook worden de verschillende terminologie beschreven die wordt gebruikt in de voor deur configuratie rond de back-end van de toepassing.

## <a name="backends"></a>Back-ends
Een back-end verwijst naar een implementatie van een webtoepassing in een regio. De voor deur ondersteunt zowel Azure-als niet-Azure-resources in de back-end-pool. De toepassing kan zich in uw on-premises Data Center bevinden of in een andere Cloud provider.

Back-ends voor de voor deur verwijzen naar de hostnaam of het open bare IP-adres van uw toepassing die uw client aanvragen verzendt. Back-ends mogen niet worden verward met uw data base-laag, opslaglaag, enzovoort. Back-ends moeten worden weer gegeven als het open bare eind punt voor de back-end van uw toepassing. Wanneer u een back-end aan een back-end van een front-deur groep toevoegt, moet u ook het volgende toevoegen:

- **Type back-end**. Het type resource dat u wilt toevoegen. Front deur ondersteunt automatische detectie van uw app-back-ends vanuit app service, Cloud service of Storage. Als u een andere resource wilt in azure of zelfs een back-end die niet van Azure is, selecteert u **aangepaste host**.

    >[!IMPORTANT]
    >Tijdens de configuratie valideert Api's niet als de back-end niet toegankelijk is vanaf de front-deur omgevingen. Zorg ervoor dat de front-deur uw back-end kan bereiken.

- De hostnaam van het **abonnement en de back-end**. Als u geen **aangepaste host** voor het type back-end hebt geselecteerd, selecteert u uw back-end door het juiste abonnement en de bijbehorende back-end-hostnaam in de gebruikers interface te kiezen.

- **Back-end-host-header**. De waarde van de host-header die wordt verzonden naar de back-end voor elke aanvraag. Zie voor meer informatie [back-end-host-header](#hostheader).

- **Prioriteit**. Wijs prioriteiten toe aan uw verschillende back-ends wanneer u een primaire service back-end voor al het verkeer wilt gebruiken. Geef ook back-ups op als de primaire of de back-end niet beschikbaar zijn. Zie [Priority](front-door-routing-methods.md#priority)voor meer informatie.

- **Gewicht**. Wijs gewichten toe aan uw verschillende back-ends voor het distribueren van verkeer over een reeks back-ends, hetzij gelijkmatig, hetzij op basis van gewichts coëfficiënten. Zie [gewichten](front-door-routing-methods.md#weighted)voor meer informatie.

### <a name="backend-host-header"></a><a name = "hostheader"></a>Host-header van back-end

Aanvragen die door de voor deur naar een back-end worden doorgestuurd, bevatten een host-header veld dat door de back-end wordt gebruikt om de doel resource op te halen. De waarde voor dit veld is doorgaans afkomstig van de back-end-URI die de host-header en-poort heeft.

Een aanvraag voor heeft bijvoorbeeld `www.contoso.com` de hostheader www.contoso.com. Als u Azure Portal gebruikt voor het configureren van uw back-end, is de standaard waarde voor dit veld de hostnaam van de back-end. Als uw back-end contoso-westus.azurewebsites.net is, wordt in de Azure Portal de automatisch gevulde waarde voor de host-header van de back-end contoso-westus.azurewebsites.net. Als u echter Azure Resource Manager sjablonen of een andere methode gebruikt zonder dit veld expliciet in te stellen, stuurt front deur de binnenkomende hostnaam als de waarde voor de host-header. Als de aanvraag voor www \. -contoso.com is gemaakt en uw back-end contoso-westus.azurewebsites.net met een leeg header-veld heeft, wordt de hostheader door de voor deur ingesteld als www- \. contoso.com.

Voor de meeste app-back-ends (Azure Web Apps, Blob Storage en Cloud Services) moet de host-header overeenkomen met het domein van de back-end. De frontend-host die naar uw back-end stuurt, gebruikt echter een andere hostnaam, zoals www.contoso.net.

Als uw back-end de host-header moet overeenkomen met de naam van de back-end, controleert u of de host-header van de back-end de hostnaam van de back-end bevat.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>De host-header van de back-end voor de back-end configureren

Het veld voor de **back-end-host-header** configureren voor een back-end in de back-endadresgroep-groep:

1. Open de resource van de voor deur en selecteer de back-end-pool met de te configureren back-end.

2. Voeg een back-end toe als u dit nog niet hebt gedaan, of bewerk een bestaande.

3. Stel het veld voor de back-end-host-header in op een aangepaste waarde of laat dit vak leeg. De hostnaam voor de binnenkomende aanvraag wordt gebruikt als de waarde voor de host-header.

## <a name="backend-pools"></a>Back-end-Pools
Een back-end-groep in de deur verwijst naar de set back-ends die soortgelijk verkeer voor hun app ontvangen. Met andere woorden, het is een logische groepering van uw app-instanties over de hele wereld die hetzelfde verkeer ontvangen en op de verwachte manier reageert. Deze back-ends worden geïmplementeerd in verschillende regio's of binnen dezelfde regio. Alle back-ends kunnen zich in de Active/Active-implementatie modus bevindt of wat is gedefinieerd als actieve/passieve configuratie.

Een back-end-pool definieert hoe de verschillende back-ends moeten worden geëvalueerd via status controles. Ook wordt gedefinieerd hoe taak verdeling tussen deze items wordt uitgevoerd.

### <a name="health-probes"></a>Statuscontroles
Met de voor deur worden periodiek HTTP/HTTPS-test aanvragen verzonden naar elk van de geconfigureerde back-ends. Test aanvragen bepalen de nabijheid en status van elke back-end om uw aanvragen van uw eind gebruikers te verdelen. De status test instellingen voor een back-end-pool bepalen hoe we de status van app-back-ends controleren. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

- **Pad**: de URL die wordt gebruikt voor het testen van aanvragen voor alle back-ends in de back-end-pool. Als een van uw back-ends bijvoorbeeld contoso-westus.azurewebsites.net is en het pad is ingesteld op/probe/test.aspx, worden de front-deur omgevingen, ervan uitgaande dat het protocol is ingesteld op HTTP, status test aanvragen verzonden naar http \: //Contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocol**: definieert of de Health probe-aanvragen van de voor deur naar uw back-end moeten worden verzonden met het HTTP-of HTTPS-protocol.

- **Methode**: de HTTP-methode die moet worden gebruikt voor het verzenden van status controles. De opties zijn GET of HEAD (standaard).
    > [!NOTE]
    > Voor een lagere belasting en kosten op uw back-end wordt aanbevolen om hoofd aanvragen voor status tests te gebruiken.

- **Interval (seconden)**: definieert de frequentie van de status tests voor uw back-ends of de intervallen waarin elk van de voor deur omgevingen een sonde verzendt.

    >[!NOTE]
    >Voor snellere failovers stelt u het interval in op een lagere waarde. Hoe lager de waarde, hoe hoger het volume van de status test uw back-ends worden ontvangen. Als het interval bijvoorbeeld is ingesteld op 30 seconden met zeggen, 100 front deur Pop's wereld wijd, ontvangt elke back-end ongeveer 200 test aanvragen per minuut.

Zie [status tests](front-door-health-probes.md)voor meer informatie.

### <a name="load-balancing-settings"></a>Instellingen voor taak verdeling
Instellingen voor taak verdeling voor de back-end-pool bepalen hoe de status tests worden geëvalueerd. Deze instellingen bepalen of de back-end in orde of beschadigd is. Ze controleren ook hoe de taak verdeling van verkeer tussen verschillende back-ends in de back-end-pool wordt gebalanceerd. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

- **Voorbeeld grootte**. Hiermee wordt aangegeven hoeveel voor beelden van status tests er nodig zijn om de status van de back-end te evalueren.

- De **voorbeeld grootte is voltooid**. Hiermee definieert u de voorbeeld grootte zoals eerder vermeld, het aantal geslaagde steek proeven dat nodig is voor het aanroepen van de back-end in orde. Stel dat het interval voor de status test voor de voor deur 30 seconden is, de voorbeeld grootte 5 is en een geslaagde voorbeeld grootte is ingesteld op 3. Telkens wanneer we de status controles voor uw back-end evalueren, bekijken we de laatste vijf steek proeven van meer dan 150 seconden (5 x 30). Ten minste drie geslaagde tests zijn vereist voor het declareren van de backend als in orde.

- **Latentie gevoeligheid (extra latentie)**. Hiermee definieert u of u de aanvraag wilt verzenden naar back-ends binnen het gevoeligheids bereik voor latentie meting of door sturen van de aanvraag naar de dichtstbijzijnde back-end.

Zie voor meer informatie [op minste latentie gebaseerde routerings methode](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Volgende stappen

- [Een Front Door-profiel maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)
