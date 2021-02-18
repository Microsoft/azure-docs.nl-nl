---
title: Azure Monitor metrische gegevens voor Application Gateway
description: Meer informatie over het gebruik van metrische gegevens voor het bewaken van de prestaties van de toepassings gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: 9faa3a284aa7151880526c1ee70cfadc3dbf3089
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576116"
---
# <a name="metrics-for-application-gateway"></a>Metrische gegevens voor Application Gateway

Application Gateway publiceert gegevens punten, met de naam metrieken, naar [Azure monitor](../azure-monitor/overview.md) voor de prestaties van uw Application Gateway-en back-end-exemplaren. Deze metrische gegevens zijn numerieke waarden in een geordende set gegevensverzamelinggegevens die op een bepaald moment een aspect van uw toepassings gateway beschrijven. Als er aanvragen worden doorgelopen door de Application Gateway, worden de metrische gegevens gemeten en verzonden in intervallen van 60 seconden. Als er geen aanvragen worden verzonden door de Application Gateway of geen gegevens voor een metriek, wordt de metriek niet gerapporteerd. Zie [Azure monitor metrische](../azure-monitor/essentials/data-platform-metrics.md)gegevens voor meer informatie.

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway v2 SKU

### <a name="timing-metrics"></a>Metrische gegevens over timing

Application Gateway biedt verschillende ingebouwde metrische gegevens voor de timing met betrekking tot de aanvraag en het antwoord, die allemaal worden gemeten in milliseconden. 

![Diagram van metrische gegevens over timing, voor de Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Als er meer dan één listener is in de Application Gateway, filtert u altijd op de dimensie *listener* terwijl u verschillende latentie gegevens vergelijkt om zinvolle interferentie te verkrijgen.

- **Moment back-end verbinding**

  Tijd die is besteed aan het tot stand brengen van een verbinding met de back-end-toepassing. 

  Dit geldt ook voor de netwerk latentie en de tijd die nodig is voor de TCP-stack van de back-endserver om nieuwe verbindingen tot stand te brengen. In het geval van TLS omvat het ook de tijd die wordt besteed aan handshake. 

- **Reactie tijd eerste byte van back-end**

  Het tijds interval tussen het begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de eerste byte van de reactie header. 

  Dit is een benadering van de som van de *back-uptijd*, het tijdstip van de aanvraag om de back-end te bereiken van Application Gateway, de tijd die door de back-end-toepassing wordt gebruikt om te reageren (het tijdstip waarop de server inhoud heeft gegenereerd, mogelijk database query's op te halen), en de tijd die wordt ingenel door de eerste byte Application Gateway van de back-end.

- **Reactie tijd laatste byte van back-end**

  Het tijds interval tussen het begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst. 

  Bij benadering vormt dit de som van *Reactietijd van de eerste back-endbyte* en de gegevensoverdrachttijd (deze tijdsduur kan verschillen afhankelijk van de grootte van de aangevraagde objecten en de latentie van het servernetwerk).

- **Totale tijd toepassings gateway**

  De gemiddelde tijd die nodig is om een aanvraag te ontvangen, verwerkt en het antwoord ervan te verzenden. 

  Dit is het interval vanaf het moment dat Application Gateway de eerste byte van de HTTP-aanvraag ontvangt naar het tijdstip waarop de laatste reactie byte is verzonden naar de client. Dit omvat de verwerkings tijd van Application Gateway, de *laatste reactie tijd* van de back-end-byte, de tijd die door Application Gateway is genomen om alle antwoorden en de *client RTT* te verzenden.

- **Client RTT**

  Gemiddelde Round-retour tijd tussen clients en Application Gateway.



Deze metrische gegevens kunnen worden gebruikt om te bepalen of de waargenomen vertraging wordt veroorzaakt door het client netwerk, de Application Gateway prestaties, het back-end netwerk en de back-endserver TCP-stack verzadiging, back-end-toepassings prestaties of grote bestands grootte.

Als er bijvoorbeeld sprake is van een piek in de back-end voor de *reactie tijd* van een time-out voor de back-end, maar de trend van de *moment verbinding* voor het verbinden stabiel is, kan deze worden uitgesteld dat de toepassings gateway naar de back-end-latentie en de tijd die nodig is om de verbinding tot stand te brengen stabiel is. Daarentegen, als de piek in de *back-end-byte-reactie tijd* is gekoppeld aan een corresponderende Prikker in back- *End Connect time*, kan deze worden afgeleid dat het netwerk tussen Application Gateway-en back-endserver of de TCP-stack van de back-endserver verzadigd is. 

Als u een piek in de *reactie tijd van de laatste byte* van de back-end hebt gezien, maar de *back-end-reactie tijd* van de backend stabiel is, kan deze worden afgeleid door een groter bestand dat wordt aangevraagd.

En als de *totale tijd van de toepassings gateway* een piek heeft, maar de *reactie tijd van de laatste byte* van de back-end is stabiel, dan kan het een teken van het knel punt van de prestaties zijn op het Application Gateway of een knel punt in het netwerk tussen client en Application Gateway. Bovendien, als de *client RTT* ook een overeenkomende Prikker heeft, geeft deze aan dat de degradatie wordt veroorzaakt door het netwerk tussen client en Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Ontvangen bytes**

   Aantal bytes dat is ontvangen door de Application Gateway van de clients

- **Verzonden bytes**

   Aantal bytes dat door de Application Gateway is verzonden naar de clients

- **TLS-protocol van client**

   Aantal TLS-en niet-TLS-aanvragen dat door de client is gestart en die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocol distributie wilt weer geven, filtert u op het TLS-protocol van de dimensie.

- **Huidige capaciteitseenheden**

   Aantal capaciteitseenheden dat is verbruikt om de verkeerstaken te verdelen. Er zijn drie determinanten voor de capaciteits eenheid: reken eenheid, permanente verbindingen en door voer. Elke capaciteits eenheid bestaat uit Maxi maal 1 Compute-eenheid of 2500 permanente verbindingen, of door Voer van 2,22-Mbps.

- **Huidige reken eenheden**

   Aantal verbruikte processor capaciteit. Factoren die de rekeneenheid beïnvloeden, zijn: TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL's en de verwerking van WAF-regels. 

- **Huidige verbindingen**

   Het totale aantal gelijktijdige verbindingen dat actief is van clients naar de Application Gateway
   
- **Geschatte gefactureerde capaciteitseenheden**

  Bij de tweede versie van de SKU is het prijsmodel gebaseerd op het verbruik. Capaciteitseenheden meten verbruikskosten die naast de vaste kosten in rekening worden gebracht. *Geschatte gefactureerde capaciteits eenheden* duiden het aantal capaciteits eenheden aan waarmee de facturering wordt geschat. Dit wordt berekend als het verschil tussen *Huidige capaciteitseenheden* (capaciteitseenheden die nodig zijn om de verkeerstaken te verdelen) en *Vaste factureerbare capaciteitseenheden* (minimaal aantal capaciteitseenheden dat wordt ingericht).

- **Mislukte aanvragen**

  Het aantal aanvragen dat Application Gateway heeft geleverd met 5xx-server fout codes. Dit omvat de 5xx-codes die worden gegenereerd op basis van de Application Gateway en de 5xx-codes die worden gegenereerd op basis van de back-end. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.
   
- **Vaste factureerbare capaciteitseenheden**

  Het minimale aantal capaciteitseenheden dat in de Application Gateway-configuratie wordt ingericht volgens de instelling *Minimale schaaleenheden* (1 exemplaar staat gelijk aan 10 capaciteitseenheden).
   
 - **Nieuwe verbindingen per seconde**

   Het gemiddelde aantal nieuwe TCP-verbindingen per seconde dat is gemaakt van clients met de Application Gateway en van de Application Gateway aan de back-end-leden.


- **Reactie status**

   De HTTP-antwoord status die is geretourneerd door Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Doorvoer**

   Aantal bytes per seconde dat de Application Gateway heeft bediend

- **Totaal aantal aanvragen**

   Aantal geslaagde aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.

### <a name="backend-metrics"></a>Metrische gegevens van back-end

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Reactie status van back-end**

  Aantal HTTP-antwoord status codes dat door de back-end wordt geretourneerd. Dit omvat geen antwoord codes die zijn gegenereerd door de Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Hostaantal in orde**

  Het aantal back-ends dat in orde wordt gesteld door de status test. U kunt filteren op basis van een per back-end-groep om het aantal gezonde hosts in een specifieke back-end-groep weer te geven.

- **Hostaantal beschadigd**

  Het aantal back-ends dat niet in orde is voor de status test. U kunt filteren op basis van een per back-end-groep om het aantal beschadigde hosts in een specifieke back-end-groep weer te geven.
  
- **Aanvragen per minuut per gegezonde host**

  Het gemiddelde aantal aanvragen dat is ontvangen door elk in orded lid in een back-end-groep in een minuut. U moet de back-end-pool opgeven met de dimensie *hosts HttpSettings* .  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **CPU-gebruik**

  Toont het gebruik van de CPU's die zijn toegewezen aan de Application Gateway.  Onder normale omstandigheden mag het CPU-gebruik niet al te vaak 90% overschrijden, omdat dit latentie kan veroorzaken in de websites die achter de Application Gateway worden gehost en omdat dit de clientervaring kan verstoren. U kunt het CPU-gebruik indirect beheren of verbeteren door de configuratie van de Application Gateway te wijzigen. Dit doet u door het aantal exemplaren te verhogen of door op een grotere SKU-grootte over te stappen, of allebei.

- **Huidige verbindingen**

  Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway

- **Mislukte aanvragen**

  Het aantal aanvragen dat is mislukt vanwege verbindings problemen. Dit aantal bevat aanvragen die zijn mislukt omdat de HTTP-instelling voor het aanvragen van een time-out is overschreden en aanvragen die zijn mislukt vanwege verbindings problemen tussen de toepassings gateway en de back-end. Dit aantal bevat geen fouten omdat er geen gezonde back-end beschikbaar is. 4xx-en 5xx-antwoorden van de back-end worden ook niet beschouwd als onderdeel van deze metriek.

- **Reactie status**

  De HTTP-antwoord status die is geretourneerd door Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Doorvoer**

  Aantal bytes per seconde dat de Application Gateway heeft bediend

- **Totaal aantal aanvragen**

  Aantal geslaagde aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.

- **Aantal geblokkeerde aanvragen voor Web Application firewall**
- **Distributie van door Web Application firewall geblokkeerde aanvragen**
- **Totale regel distributie Web Application firewall**

### <a name="backend-metrics"></a>Metrische gegevens van back-end

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Hostaantal in orde**

  Het aantal back-ends dat in orde wordt gesteld door de status test. U kunt filteren op basis van een per back-end-groep om het aantal gezonde hosts in een specifieke back-end-groep weer te geven.

- **Hostaantal beschadigd**

  Het aantal back-ends dat niet in orde is voor de status test. U kunt filteren op basis van een per back-end-groep om het aantal beschadigde hosts in een specifieke back-end-groep weer te geven.

## <a name="metrics-visualization"></a>Visualisatie van metrische gegevens

Blader naar een toepassings gateway onder **bewaking** **metrische gegevens** selecteren. Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

In de volgende afbeelding ziet u een voor beeld met drie metrische gegevens die gedurende de laatste 30 minuten worden weer gegeven:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Metrische weer gave." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/essentials/metrics-supported.md)voor een actuele lijst met metrische gegevens.

### <a name="alert-rules-on-metrics"></a>Waarschuwings regels voor metrische gegevens

U kunt waarschuwings regels starten op basis van metrische gegevens voor een resource. Een waarschuwing kan bijvoorbeeld een webhook aanroepen of een beheerder e-mailen als de door Voer van de toepassings gateway boven, onder of met een drempel waarde voor een opgegeven periode.

In het volgende voor beeld wordt stapsgewijs uitgelegd hoe u een waarschuwings regel maakt waarmee een e-mail wordt verzonden naar een beheerder na een drempel waarde voor de door Voer, bijvoorbeeld:

1. Selecteer **waarschuwing voor metrische gegevens toevoegen** om de pagina **regel toevoegen** te openen. U kunt deze pagina ook bereiken via de pagina metrische gegevens.

   ![Knop voor waarschuwing voor metrische gegevens toevoegen][6]

2. Vul op de pagina **regel toevoegen** de naam, de voor waarde en de meldings secties in en selecteer **OK**.

   * Selecteer in de **voor waarden** kiezer een van de vier waarden: **groter dan**, **groter dan of gelijk** aan, **kleiner dan** of **kleiner dan of gelijk aan**.

   * Selecteer in de **periode** kiezer een periode van vijf minuten tot zes uur.

   * Als u **e-mail eigenaren, inzenders en lezers** selecteert, kan het e-mail bericht dynamisch zijn op basis van de gebruikers die toegang hebben tot de bron. Als dat niet het geval is, kunt u een door komma's gescheiden lijst met gebruikers opgeven in het vak **extra e-mail adres** van de beheerder.

   ![Regel pagina toevoegen][7]

Als de drempel waarde wordt geschonden, ontvangt u een e-mail bericht dat lijkt op dat van de volgende afbeelding:

![E-mail voor drempel waarde voor schending][8]

Er wordt een lijst met waarschuwingen weer gegeven nadat u een metrische waarschuwing hebt gemaakt. Het bevat een overzicht van alle waarschuwings regels.

![Lijst met waarschuwingen en regels][9]

Zie [waarschuwings meldingen ontvangen](../azure-monitor/alerts/alerts-overview.md)voor meer informatie over waarschuwings meldingen.

Ga voor meer informatie over webhooks en hoe u deze kunt gebruiken met waarschuwingen, [een webhook configureren voor een Azure metric-waarschuwing](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller-en gebeurtenis logboeken met behulp van [Azure monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteiten logboek met Power bi](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blog bericht.
* [Bekijk en analyseer activiteiten logboeken van Azure in Power bi en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog berichten.

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