---
title: Azure Monitor metrische gegevens voor Application Gateway
description: Meer informatie over het gebruik van metrische gegevens voor het bewaken van de prestaties van application gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: azhussai
ms.openlocfilehash: 3baaf49cb3d1c8c5502d96974f9729d05996c75b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519879"
---
# <a name="metrics-for-application-gateway"></a>Metrische gegevens voor Application Gateway

Application Gateway publiceert gegevenspunten, ook wel metrische gegevens genoemd, om [Azure Monitor](../azure-monitor/overview.md) prestaties van uw Application Gateway en back-Application Gateway te verbeteren. Deze metrische gegevens zijn numerieke waarden in een geordende set tijdreeksgegevens die een bepaald aspect van uw toepassingsgateway op een bepaald moment beschrijven. Als er aanvragen door de Application Gateway, meet en verzendt het de metrische gegevens in intervallen van 60 seconden. Als er geen aanvragen door de gegevensstroom Application Gateway of geen gegevens voor een metrische gegevens, wordt de metrische gegevens niet gerapporteerd. Zie metrische gegevens voor [Azure Monitor meer informatie.](../azure-monitor/essentials/data-platform-metrics.md)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metrische gegevens die worden ondersteund Application Gateway V2 SKU

### <a name="timing-metrics"></a>Metrische timinggegevens

Application Gateway biedt verschillende ingebouwde metrische timinggegevens met betrekking tot de aanvraag en het antwoord, die allemaal worden gemeten in milliseconden. 

![Diagram van metrische timinggegevens voor de Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.jpg)

> [!NOTE]
>
> Als er meer dan één listener in de Application Gateway,  filtert u altijd op listenerdimensie terwijl u verschillende metrische latentiegegevens vergelijkt om zinvolle deferentie te krijgen.

- **Verbindingstijd van back-en-back-en**

  Tijd besteed aan het tot stand brengen van een verbinding met de back-endtoepassing. 

  Dit omvat de netwerklatentie en de tijd die de TCP-stack van de back-ende server nodig heeft om nieuwe verbindingen tot stand te brengen. In het geval van TLS omvat het ook de tijd die wordt besteed aan handshake. 

- **Reactietijd van eerste back-byte**

  Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met de back-mailserver en het ontvangen van de eerste byte van de antwoordheader. 

  Hiermee wordt de som van de verbindingstijd van de back-end, de tijd die nodig is voor de aanvraag om de back-Application Gateway te bereiken, de tijd die de back-endtoepassing nodig heeft om te reageren (de tijd die de server nodig heeft om inhoud te genereren, mogelijk databasequery's op te halen) en de tijd die nodig is door de eerste byte van het antwoord om de Application Gateway te bereiken vanuit de back-end.

- **Reactietijd van laatste back-eind byte**

  Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord-body. 

  Bij benadering vormt dit de som van *Reactietijd van de eerste back-endbyte* en de gegevensoverdrachttijd (deze tijdsduur kan verschillen afhankelijk van de grootte van de aangevraagde objecten en de latentie van het servernetwerk).

- **Totale tijd van Application Gateway**

  De gemiddelde tijd die het kost om een aanvraag te ontvangen, te verwerken en te antwoorden. 

  Dit is het interval vanaf het moment waarop Application Gateway de eerste byte van de HTTP-aanvraag ontvangt tot het tijdstip waarop de laatste antwoord-byte naar de client is verzonden. Dit omvat de verwerkingstijd van Application Gateway, de reactietijd van de laatste *back-Application Gateway,* de tijd die door de Application Gateway wordt genomen om het antwoord en de *Client RTT te verzenden.*

- **Client RTT**

  Gemiddelde retourtijd tussen clients en Application Gateway.



Deze metrische gegevens kunnen worden gebruikt om te bepalen of de waargenomen vertraging wordt veroorzaakt door het clientnetwerk, de Application Gateway-prestaties, de TCP-stackverzadiging van de back-endserver, de prestaties van de back-endtoepassing of de grote bestandsgrootte.

Als er bijvoorbeeld een piek is in de reactietijd van de eerste *back-end-byte,* maar de trend voor de verbinding met de *back-end* stabiel is, kan worden afgeleid dat de latentie van application gateway naar back-end en de tijd die nodig is om de verbinding tot stand te brengen stabiel is, en dat de piek wordt veroorzaakt door een toename in de reactietijd van de back-endtoepassing. Als de piek in reactietijd van de eerste back-ende *byte* daarentegen is gekoppeld aan een overeenkomstige piek in *back-endverbindingstijd,* kan worden afgeleid dat het netwerk tussen de Application Gateway- en back-en-back-en-back-endserver of de TCP-stack van de back-endserver is verzadigd. 

Als u een piek in reactietijd van de laatste back-ende *byte* ziet, maar de reactietijd van de eerste back-ende *byte* stabiel is, kan dit worden afgeleid dat de piek wordt veroorzaakt doordat een groter bestand wordt aangevraagd.

En als de totale tijd van *de toepassingsgateway* een piek heeft, maar de reactietijd van de laatste *back-byte* stabiel is, kan dit een teken zijn van een prestatieknelpunt in de Application Gateway of een knelpunt in het netwerk tussen client en Application Gateway. Als de *client-RTT* ook een overeenkomstige piek heeft, geeft dit aan dat de degradatie komt door het netwerk tussen de client en Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

Voor Application Gateway zijn de volgende metrische gegevens beschikbaar:

- **Ontvangen bytes**

   Aantal bytes dat door de Application Gateway ontvangen van de clients

- **Verzonden bytes**

   Aantal bytes dat door de Application Gateway naar de clients wordt verzonden

- **Client-TLS-protocol**

   Aantal TLS- en niet-TLS-aanvragen dat is geïnitieerd door de client die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocoldistributie wilt weergeven, filtert u op het dimensie-TLS-protocol.

- **Huidige capaciteitseenheden**

   Aantal capaciteitseenheden dat is verbruikt om de verkeerstaken te verdelen. Er zijn drie determinanten voor capaciteitseenheid: rekeneenheid, permanente verbindingen en doorvoer. Elke capaciteitseenheid bestaat uit ten zeerste: 1 rekeneenheid of 2500 permanente verbindingen of 2,22 Mbps doorvoer.

- **Huidige rekeneenheden**

   Aantal verbruikte processorcapaciteit. Factoren die de rekeneenheid beïnvloeden, zijn: TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL's en de verwerking van WAF-regels. 

- **Huidige verbindingen**

   Het totale aantal gelijktijdige verbindingen dat actief is van clients naar de Application Gateway
   
- **Geschatte gefactureerde capaciteitseenheden**

  Bij de tweede versie van de SKU is het prijsmodel gebaseerd op het verbruik. Capaciteitseenheden meten verbruikskosten die naast de vaste kosten in rekening worden gebracht. *Geschatte gefactureerde capaciteitseenheden* geeft het aantal capaciteitseenheden aan op basis waarvan de facturering wordt geschat. Dit wordt berekend als het verschil tussen *Huidige capaciteitseenheden* (capaciteitseenheden die nodig zijn om de verkeerstaken te verdelen) en *Vaste factureerbare capaciteitseenheden* (minimaal aantal capaciteitseenheden dat wordt ingericht).

- **Mislukte aanvragen**

  Aantal aanvragen dat Application Gateway heeft uitgevoerd met 5xx serverfoutcodes. Dit omvat de 5xx-codes die worden gegenereerd op basis van de Application Gateway en de 5xx-codes die worden gegenereerd op basis van de back-end. Het aantal aanvragen kan verder worden gefilterd om het aantal per combinatie van back-endpool-http-instelling weer te geven.
   
- **Vaste factureerbare capaciteitseenheden**

  Het minimale aantal capaciteitseenheden dat in de Application Gateway-configuratie wordt ingericht volgens de instelling *Minimale schaaleenheden* (1 exemplaar staat gelijk aan 10 capaciteitseenheden).
   
 - **Nieuwe verbindingen per seconde**

   Het gemiddelde aantal nieuwe TCP-verbindingen per seconde dat is ingesteld van clients naar de Application Gateway en van de Application Gateway naar de back-endleden.


- **Antwoordstatus**

   HTTP-antwoordstatus geretourneerd door Application Gateway. De distributie van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in de categorieën 2xx, 3xx, 4xx en 5xx.

- **Doorvoer**

   Aantal bytes per seconde dat door Application Gateway wordt bediend

- **Totaal aantal aanvragen**

   Aantal geslaagde aanvragen dat Application Gateway heeft bediend. Het aantal aanvragen kan verder worden gefilterd om het aantal per/specifieke combinatie van back-endpool-http-instellingen weer te geven.

### <a name="backend-metrics"></a>Metrische gegevens van back-en-over

Voor Application Gateway zijn de volgende metrische gegevens beschikbaar:

- **Antwoordstatus van back-end**

  Aantal HTTP-antwoordstatuscodes dat door de back-einden wordt geretourneerd. Dit omvat geen responscodes die door de Application Gateway. De distributie van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in de categorieën 2xx, 3xx, 4xx en 5xx.

- **Hostaantal in orde**

  Het aantal back-einden dat in orde wordt bepaald door de statustest. U kunt filteren op basis van een back-endpool om het aantal gezonde hosts in een specifieke back-endpool weer te geven.

- **Hostaantal beschadigd**

  Het aantal back-einden dat door de statustest niet in orde is. U kunt filteren op basis van een back-endpool om het aantal hosts met een slechte status in een specifieke back-endpool weer te geven.
  
- **Aanvragen per minuut per host met een goede gezondheid**

  Het gemiddelde aantal aanvragen dat in één minuut door elk gezond lid in een back-endpool is ontvangen. U moet de back-endpool opgeven met behulp van de *dimensie HttpSettings van BackendPool.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

Voor Application Gateway zijn de volgende metrische gegevens beschikbaar:

- **CPU-gebruik**

  Toont het gebruik van de CPU's die zijn toegewezen aan de Application Gateway.  Onder normale omstandigheden mag het CPU-gebruik niet al te vaak 90% overschrijden, omdat dit latentie kan veroorzaken in de websites die achter de Application Gateway worden gehost en omdat dit de clientervaring kan verstoren. U kunt het CPU-gebruik indirect beheren of verbeteren door de configuratie van de Application Gateway te wijzigen. Dit doet u door het aantal exemplaren te verhogen of door op een grotere SKU-grootte over te stappen, of allebei.

- **Huidige verbindingen**

  Aantal huidige verbindingen tot stand gebracht met Application Gateway

- **Mislukte aanvragen**

  Aantal aanvragen dat is mislukt vanwege verbindingsproblemen. Dit aantal omvat aanvragen die zijn mislukt vanwege het overschrijden van de HTTP-instelling 'Time-out van aanvraag' en aanvragen die zijn mislukt vanwege verbindingsproblemen tussen Application Gateway en back-end. Dit aantal omvat geen fouten omdat er geen goede back-end beschikbaar is. 4xx- en 5xx-antwoorden van de back-end worden ook niet beschouwd als onderdeel van deze metrische gegevens.

- **Antwoordstatus**

  HTTP-antwoordstatus geretourneerd door Application Gateway. De distributie van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in de categorieën 2xx, 3xx, 4xx en 5xx.

- **Doorvoer**

  Aantal bytes per seconde dat de Application Gateway heeft gebruikt

- **Totaal aantal aanvragen**

  Aantal geslaagde aanvragen dat Application Gateway heeft ingediend. Het aantal aanvragen kan verder worden gefilterd om het aantal per combinatie van back-endpool-http-instelling weer te geven.

- **Web Application Firewall aantal geblokkeerde aanvragen**
- **Web Application Firewall distributie van geblokkeerde aanvragen**
- **Web Application Firewall totale regeldistributie**

### <a name="backend-metrics"></a>Metrische gegevens van back-en-over

Voor Application Gateway zijn de volgende metrische gegevens beschikbaar:

- **Hostaantal in orde**

  Het aantal back-einden dat in orde wordt bepaald door de statustest. U kunt filteren op basis van een back-endpool om het aantal gezonde hosts in een specifieke back-endpool weer te geven.

- **Hostaantal beschadigd**

  Het aantal back-einden dat door de statustest niet in orde is. U kunt filteren op basis van een back-endpool om het aantal hosts met een slechte status in een specifieke back-endpool weer te geven.

## <a name="metrics-visualization"></a>Visualisatie van metrische gegevens

Blader naar een toepassingsgateway en selecteer **onder Bewaking de** optie **Metrische gegevens.** Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

In de volgende afbeelding ziet u een voorbeeld met drie metrische gegevens die in de afgelopen 30 minuten zijn weergegeven:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Metrische weergave." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Zie Ondersteunde metrische gegevens met de Azure Monitor voor [een Azure Monitor.](../azure-monitor/essentials/metrics-supported.md)

### <a name="alert-rules-on-metrics"></a>Waarschuwingsregels voor metrische gegevens

U kunt waarschuwingsregels starten op basis van metrische gegevens voor een resource. Een waarschuwing kan bijvoorbeeld een webhook aanroepen of een beheerder een e-mail sturen als de doorvoer van de toepassingsgateway boven, onder of op een drempelwaarde voor een bepaalde periode ligt.

In het volgende voorbeeld ziet u hoe u een waarschuwingsregel maakt die een e-mailbericht naar een beheerder verzendt nadat de doorvoer een drempelwaarde overschrijdt:

1. Selecteer **Waarschuwing voor metrische gegevens toevoegen** om de pagina Regel toevoegen **te** openen. U kunt deze pagina ook bereiken via de pagina met metrische gegevens.

   ![Knop Waarschuwing voor metrische gegevens toevoegen][6]

2. Vul op **de pagina** Regel toevoegen de secties naam, voorwaarde en melding in en selecteer **OK.**

   * Selecteer in **de** selector Voorwaarde een van de vier **waarden:** Groter dan , Groter dan of **gelijk** aan , **Kleiner dan** of Kleiner dan of **gelijk aan**.

   * Selecteer in **de** selector Periode een periode van vijf minuten tot zes uur.

   * Als u **E-maileigenaren, bijdragers** en lezers selecteert, kan het e-mailbericht dynamisch zijn op basis van de gebruikers die toegang hebben tot die resource. Anders kunt u een door komma's gescheiden lijst met gebruikers in het vak **Extra e-mail van beheerders** verstrekken.

   ![Regelpagina toevoegen][7]

Als de drempelwaarde wordt overschreden, ontvangt u een e-mailbericht dat vergelijkbaar is met het e-mailbericht in de volgende afbeelding:

![E-mail voor overschreden drempelwaarde][8]

Er wordt een lijst met waarschuwingen weergegeven nadat u een waarschuwing voor metrische gegevens hebt maken. Het biedt een overzicht van alle waarschuwingsregels.

![Lijst met waarschuwingen en regels][9]

Zie Waarschuwingsmeldingen ontvangen voor [meer informatie over waarschuwingsmeldingen.](../azure-monitor/alerts/alerts-overview.md)

Als u meer wilt weten over webhooks en hoe u deze kunt gebruiken met waarschuwingen, gaat u naar Een webhook configureren [voor een Azure-waarschuwing voor metrische gegevens.](../azure-monitor/alerts/alerts-webhooks.md)

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller- en gebeurtenislogboeken met [behulp Azure Monitor logboeken.](../azure-monitor/insights/azure-networking-analytics.md)
* [Visualiseer uw Azure-activiteitenlogboek met Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blogpost.
* [Bekijk en analyseer Azure-activiteitenlogboeken in Power BI blogpost.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
