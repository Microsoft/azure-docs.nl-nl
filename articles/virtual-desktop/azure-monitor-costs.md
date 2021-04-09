---
title: Schattingen van de kosten van Windows Virtual Desktop bewaken-Azure
description: Kosten en prijzen voor het gebruik van Azure Monitor voor virtueel bureau blad van Windows schatten.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 099def5a29c8a9b46733ba435befed7210756012
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709978"
---
# <a name="estimate-azure-monitor-costs"></a>Azure Monitor kosten schatten

Azure Monitor-Logboeken is een service waarmee gegevens die door uw omgeving worden gegenereerd, worden verzameld, geïndexeerd en opgeslagen. Daarom is het Azure Monitor-prijs model gebaseerd op de hoeveelheid gegevens die wordt overgebracht naar en verwerkt (of ' opgenomen ') door uw Log Analytics-werk ruimte in gigabytes per dag. De kosten van een Log Analytics-werk ruimte zijn niet alleen gebaseerd op het volume van de verzamelde gegevens, maar ook welk Azure-betalings schema u hebt geselecteerd en hoe lang u ervoor kiest om de gegevens op te slaan die uw omgeving genereert.

In dit artikel worden de volgende zaken uitgelegd om te begrijpen hoe prijzen in Azure Monitor werken:

- Gegevens opname en opslag kosten vooraf schatten voordat u deze functie inschakelt
- Uw opname en opslag meten en beheren om de kosten te verlagen bij gebruik van deze functie

>[!NOTE]
> Alle grootten en prijzen die in dit artikel worden vermeld, zijn slechts voor beelden om te laten zien hoe de schatting werkt. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor een nauw keurige evaluatie op basis van uw Azure monitor log Analytics prijs model en Azure-regio.

## <a name="estimate-data-ingestion-and-storage-costs"></a>Schatting van gegevens opname en opslag kosten

U wordt aangeraden een vooraf gedefinieerde set gegevens te gebruiken die zijn geschreven als Logboeken in uw Log Analytics-werk ruimte. In het volgende voor beeld wordt gefactureerd gegevens weer gegeven in de standaard configuratie

De vooraf gedefinieerde gegevens sets voor Azure Monitor voor virtuele Windows-Bureau bladen zijn onder andere:

- Prestatie meter items van de sessie-hosts
- Windows-gebeurtenis logboeken van de sessie-hosts
- Diagnostische gegevens van Windows virtueel bureau blad van de service-infra structuur

Uw gegevens opname-en opslag kosten zijn afhankelijk van de grootte, status en het gebruik van uw omgeving. De voor beelden van de schattingen die we in dit artikel gebruiken om de gewenste datumbereiken te berekenen, zijn gebaseerd op in orde zijnde virtuele machines met licht over het energie verbruik, op basis van onze richt lijnen voor de grootte van de [virtuele machine](/remote/remote-desktop-services/virtual-machine-recs), om een reeks gegevens opname en opslag kosten te berekenen die u kunt verwachten.

De VM voor het gebruik van de Light-het gebruik in ons voor beeld bevat de volgende onderdelen:

- 4 Vcpu's, 1 schijf
- 16 sessies per dag
- Een gemiddelde sessie duur van 2 uur (120 minuten)
- 100 processen per sessie

De VM voor energie gebruik die in ons voor beeld wordt gebruikt, bevat de volgende onderdelen:

- 6 Vcpu's, 1 schijf
- 6 sessies per dag
- Gemiddelde sessie duur van 4 uur (240 minuten)
- 200 processen per sessie

## <a name="estimating-performance-counter-ingestion"></a>Opname van prestatie meter item schatten

Prestatie meter items laten zien hoe de systeem bronnen worden uitgevoerd. Gegevens opname van prestatie meter items is afhankelijk van de grootte en het gebruik van uw omgeving. In de meeste gevallen moeten de prestatie meter items 80 tot 99% van uw gegevens opname voor Azure Monitor voor virtueel bureau blad van Windows maken.

Voordat u begint met het schatten van de schatting, is het belang rijk dat u begrijpt dat elk prestatie meter item gegevens verzendt met een bepaalde frequentie. We stellen een standaard sample frequentie per minuut in (u kunt dit aantal ook in uw instellingen bewerken), maar dat aantal wordt toegepast op verschillende vermenigvuldigings factoren, afhankelijk van het prestatie meter item. De volgende factoren zijn van invloed op het aantal:

- Voor de factor per virtuele machine (VM) verzendt elke teller gegevens per VM in uw omgeving tegen de standaard sample snelheid per minuut terwijl de VM wordt uitgevoerd. U kunt een schatting maken van het aantal records dat per dag wordt verzonden door de standaard sampling frequentie per minuut te vermenigvuldigen met het aantal Vm's in uw omgeving. Vervolgens vermenigvuldigt u dat getal met de gemiddelde tijd voor het uitvoeren van de VM per dag.

   Samenvatting:

   Standaard sample frequentie per minuut × aantal CPU-kernen in de VM-SKU × aantal Vm's × gemiddelde VM-uitvoerings tijd per dag = aantal verzonden records per dag

- Voor de factor per CPU stuurt elke teller per minuut per vCPU in uw omgeving naar de standaard sampling frequentie, terwijl de virtuele machine wordt uitgevoerd. U kunt een schatting maken van het aantal records dat door de tellers per dag wordt verzonden door de standaard sampling frequentie per minuut te vermenigvuldigen met het aantal CPU-kernen in de VM-SKU en vervolgens dat getal te vermenigvuldigen met het aantal minuten dat de VM wordt uitgevoerd en het aantal Vm's in uw omgeving.

   Samenvatting:
   
   Standaard sample frequentie per minuut × het aantal CPU-kernen in de VM-SKU × het aantal minuten dat de VM wordt uitgevoerd × het aantal Vm's = aantallen verzonden records per dag

- Voor de factor per schijf verzendt elk item gegevens met de standaard sampling frequentie voor elke schijf in elke VM in uw omgeving. Het aantal records dat door deze tellers per dag wordt verzonden, is gelijk aan de standaard sampling frequentie per minuut, vermenigvuldigd met het aantal schijven in de VM-SKU, vermenigvuldigd met 60 minuten per uur en ten slotte vermenigvuldigd met het gemiddelde aantal actieve uren voor een virtuele machine.

   Samenvatting:

   Standaard sample frequentie per minuut × aantal schijven in VM-SKU × 60 minuten per uur × het aantal Vm's × de gemiddelde VM-uitvoerings tijd per dag = aantal verzonden records per dag

- Voor de per sessie factor verzendt elk item gegevens met de standaard sampling frequentie voor elke sessie in uw omgeving terwijl de sessie is verbonden. U kunt het aantal records dat door deze items per dag wordt verzonden, schatten door de standaard sampling frequentie per minuut te vermenigvuldigen met het gemiddelde aantal sessies per dag en de gemiddelde sessie duur.

   Samenvatting:

   Standaard sample frequentie per minuut × sessies per dag × gemiddelde sessie duur = aantal verzonden records per dag

- Voor de factor per proces verzendt elke teller gegevens op basis van het standaard aantal voor elk proces in elke sessie in uw omgeving. U kunt een schatting maken van het aantal records dat door deze items per dag wordt verzonden door de standaard sampling frequentie per minuut te vermenigvuldigen met het gemiddeld aantal sessies per dag en vervolgens te vermenigvuldigen met de gemiddelde sessie duur en het gemiddelde aantal processen per sessie.
  
   Samenvatting:

   Standaard sample frequentie per minuut × sessies per dag × gemiddelde sessie duur × gemiddeld aantal processen per sessie = aantal verzonden records per dag

De volgende tabel bevat de 20 prestatie meter items Azure Monitor voor virtuele Windows-Bureau bladen worden verzameld en de standaard tarieven hiervan:

| Naam van het meteritem | Standaard sampling frequentie | Frequentie factor |
|--------------|---------------------|------------------|
| Logische schijf (C:) \\ % beschik bare ruimte | 60 seconden  | Per schijf             |
| Logische schijf (C:) \\ Gem. wachtrij lengte voor schijf   | 30 seconden    | Per schijf             |
| Logische schijf (C:) \\ gemiddelde tijd schijf overdracht  | 60 seconden       | Per schijf             |
| Logische schijf (C:) \\ huidige wachtrij lengte voor schijf  | 30 seconden      | Per schijf             |
| Beschik \* \\ bare MB geheugen () | 30 seconden    | Per VM  |
| Geheugen ( \* ) \\ -pagina fouten per seconde | 30 seconden   | Per VM  |
| Geheugen \* \\ pagina's/SEC | 30 seconden   | Per VM  |
| Geheugen ( \* ) \\ % toegewezen bytes in gebruik | 30 seconden    | Per VM  |
| Fysieke \* schijf () \\ Gem. wachtrij lengte voor schijven | 30 seconden      | Per schijf             |
| Fysieke \* schijf () \\ gemiddeld aantal seconden per Lees bewerking | 30 seconden  | Per schijf             |
| Fysieke \* schijf () \\ gemiddeld aantal seconden per overdracht | 30 seconden  | Per schijf             |
| Fysieke \* schijf () \\ gemiddeld aantal seconden per schrijf bewerking | 30 seconden | Per schijf             |
| Processor informatie (_Total) \\ % processor tijd | 30 seconden | Per kern/CPU         |
| Terminal Services ( \* ) \\ actieve sessies          | 60 seconden | Per VM  |
| Inactieve sessies van Terminal Services ( \* ) \\        | 60 seconden | Per VM  |
| Aantal sessies van Terminal Services ( \* ) \\ | 60 seconden | Per VM  |
| Gebruikers invoer vertraging per proces ( \* ) \\ maximale invoer vertraging         | 30 seconden | Per proces          |
| Gebruikers invoer vertraging per sessie ( \* ) \\ maximale invoer vertraging        | 30 seconden | Per sessie          |
| RemoteFX-netwerk ( \* ) \\ huidige TCP RTT | 30 seconden | Per VM  |
| RemoteFX-netwerk ( \* ) \\ huidige UDP-band breedte     | 30 seconden | Per VM  |

Als we een schatting van elke record grootte hebben van 200 bytes, zou een voor beeld van een VM met een lichte werk belasting op de standaard sampling frequentie ongeveer 90 MB aan prestatie meter gegevens per VM verzenden. Ondertussen verzendt een voor beeld-VM met een energie belasting ongeveer 130 MB aan prestatie meter gegevens per dag per VM. De record grootte en het gebruik van de omgeving kunnen echter variëren, dus de mega bytes per dag die uw implementatie gebruikt, kunnen verschillen.

Voor meer informatie over prestatie meter items voor de invoer vertraging raadpleegt u [prestatie meter items voor gebruikers invoer vertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>De opname van Windows-gebeurtenis logboeken ramen

Windows-gebeurtenis logboeken zijn gegevens bronnen die worden verzameld door Log Analytics agents op virtuele Windows-machines. U kunt gebeurtenissen verzamelen uit standaard logboeken zoals systeem en toepassing, evenals aangepaste logboeken die zijn gemaakt door toepassingen die u wilt bewaken.

Dit zijn de standaard Windows-gebeurtenissen voor Azure Monitor voor virtueel bureau blad van Windows:

- Toepassing
- Micro soft-Windows-TerminalServices-RemoteConnectionManager/admin
- Micro soft-Windows-TerminalServices-LocalSessionManager/operationeel
- Systeem
- Micro soft-FSLogix-apps/operationeel
- Micro soft-FSLogix-Apps/beheerder

Windows-gebeurtenissen verzenden wanneer aan de voor waarden van de gebeurtenis wordt voldaan in de omgeving. Computers in gezonde statussen verzenden minder gebeurtenissen dan computers in slechte staat. Omdat het aantal gebeurtenissen onvoorspelbaar is, gebruiken we een bereik van 1.000 tot 10.000 gebeurtenissen per VM per dag, op basis van voor beelden van goede omgevingen voor deze schatting. Als we bijvoorbeeld elke gebeurtenis record grootte in dit voor beeld schatten om 1.500 bytes te zijn, is dit ongeveer 2 tot 15 MB aan gebeurtenis gegevens per dag voor de opgegeven omgeving.

Zie [Eigenschappen van Windows-gebeurtenis records](../azure-monitor/agents/data-sources-windows-events.md)voor meer informatie over Windows-gebeurtenissen.

## <a name="estimating-diagnostics-ingestion"></a>Opname van diagnostische gegevens schatten

De diagnostische service maakt activiteiten logboeken voor zowel gebruikers-als beheer acties.

Dit zijn de namen van de activiteit logboeken met diagnostische gegevens vastleggen:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

De service verzendt diagnostische gegevens wanneer de omgeving voldoet aan de voor waarden die nodig zijn om een record te maken. Omdat het aantal diagnostische records onvoorspelbaar is, gebruiken we een bereik van 500 tot 1000 gebeurtenissen per VM per dag, op basis van voor beelden van goede omgevingen voor deze schatting.

Als we bijvoorbeeld een schatting maken van elke diagnostische record grootte in dit voor beeld om 200 bytes te zijn, zou de totale hoeveelheid opgenomen gegevens per VM per dag kleiner zijn dan 1 MB.

Zie [Windows diagnostische gegevens over virtuele Bureau bladen](diagnostics-log-analytics.md)voor meer informatie over activiteiten logboek categorieën.

## <a name="estimating-total-costs"></a>De totale kosten schatten

Ten slotte schatten we de totale kosten. In dit voor beeld laten we zeggen dat we de volgende resultaten volgen, op basis van de voorbeeld waarden in de vorige secties:

| Gegevensbron        | Omvang van de schatting per dag (in mega bytes)   |
|-------------------------------------|------------------------------------------|
| Prestatiemeteritems   | 90-130 |
| Gebeurtenissen    | 2-15 |
| Diagnostische gegevens van Windows virtueel bureau blad | \< i |

In dit voor beeld bevindt het totale aantal opgenomen gegevens voor Azure Monitor voor virtuele Windows-bureau blad tussen 92 en 145 MB per VM per dag. Met andere woorden: elke 31 dagen neemt elke VM ongeveer 3 tot 5 gigabyte aan gegevens op.

Met het standaard model voor betalen naar gebruik voor [log Analytics prijzen](https://azure.microsoft.com/pricing/details/monitor/)kunt u een schatting maken van de Azure monitor gegevens verzameling en opslag kosten per maand. Afhankelijk van uw gegevens opname kunt u ook het capaciteits reserverings model voor Log Analytics prijzen overwegen.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Uw gegevens opname beheren om de kosten te verlagen

In deze sectie wordt uitgelegd hoe u gegevens opname kunt meten en beheren om de kosten te verlagen.

Zie [toegangs beheer](../azure-monitor/visualize/workbooks-access-control.md)voor meer informatie over het beheren van rechten en machtigingen voor de werkmap.

>[!NOTE]
>Het verwijderen van gegevens punten is van invloed op de bijbehorende visuals in Azure Monitor voor virtueel bureau blad van Windows.

### <a name="log-analytics-settings"></a>Log Analytics instellingen

Hier volgen enkele suggesties voor het optimaliseren van uw Log Analytics instellingen voor het beheren van gegevens opname:

- Gebruik een aangewezen Log Analytics-werk ruimte voor uw virtuele bureau blad-resources van Windows om ervoor te zorgen dat Log Analytics alleen prestatie meter items en gebeurtenissen verzamelt voor de virtuele machines in uw Windows-implementatie voor virtueel bureau blad.
- Stel uw Log Analytics opslag instellingen in om de kosten te beheren. U kunt de Bewaar periode verlagen, evalueren of een prijs categorie voor vaste opslag rendabeler is, of grenzen instellen voor hoeveel gegevens u kunt opnemen om de impact van een slechte implementatie te beperken. Zie het [gebruik en de kosten voor Azure monitor logboeken beheren voor](../azure-monitor/platform/manage-cost-storage.md)meer informatie.

### <a name="remove-excess-data"></a>Overtollige gegevens verwijderen

Onze standaard configuratie is de enige set gegevens die wordt aanbevolen voor Azure Monitor voor virtueel Windows-bureau blad. U kunt altijd extra gegevens punten toevoegen en deze weer geven in de diagnostische test van de host: de host-browser of aangepaste grafieken bouwen, maar toegevoegde gegevens zullen uw Log Analytics kosten verg Roten. Deze kunnen worden verwijderd voor kosten besparingen.

### <a name="measure-and-manage-your-performance-counter-data"></a>De gegevens van uw prestatie meter items meten en beheren

De werkelijke bewakings kosten zijn afhankelijk van de grootte, het gebruik en de status van uw omgeving. Zie informatie over opgenomen [logboek gegevens volume](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)als u wilt weten hoe u de opname van gegevens in uw log Analytics-werk ruimte kunt meten.

De prestatie meter items die door de sessie worden gebruikt, zijn waarschijnlijk uw grootste bron van opgenomen gegevens voor Azure Monitor voor virtueel bureau blad van Windows. De volgende aangepaste query sjabloon voor een Log Analytics-werk ruimte kan frequentie en mega bytes volgen per prestatie meter item gedurende de laatste dag:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Zorg ervoor dat u de waarden van de tijdelijke aanduidingen vervangt door de waarden die uw omgeving gebruikt, anders werkt de query niet.

Met deze query worden alle prestatie meter items weer gegeven die u hebt ingeschakeld in de omgeving, niet alleen de standaard instellingen voor Azure Monitor voor virtueel bureau blad van Windows. Aan de hand van deze informatie kunt u inzicht krijgen in de gebieden die gericht zijn op het verlagen van de kosten, zoals het verminderen van de frequentie van een teller of het verwijderen ervan.

U kunt ook kosten verlagen door prestatie meter items te verwijderen. Zie [prestatie meter items configureren](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters)voor meer informatie over het verwijderen van prestatie meter items of het bewerken van bestaande items om de frequentie te verlagen.

### <a name="manage-windows-event-logs"></a>Windows-gebeurtenis logboeken beheren

Windows-gebeurtenissen zijn waarschijnlijk niet in staat om een piek in gegevens opname te veroorzaken wanneer alle hosts in orde zijn. Een beschadigde host kan het aantal gebeurtenissen dat naar het logboek wordt verzonden, verg Roten, maar de informatie kan essentieel zijn om de problemen van de host te verhelpen. We raden u aan deze te bewaren. Zie [Windows-gebeurtenis logboeken configureren](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)voor meer informatie over het beheren van Windows-gebeurtenis Logboeken.

### <a name="manage-diagnostics"></a>Diagnostische gegevens beheren

De diagnostische gegevens van het virtuele bureau blad van Windows moeten minder dan 1% van de kosten van uw opslag ruimte opleveren. Daarom raden we u aan om deze niet te verwijderen. Als u Diagnostische gegevens van Windows virtueel bureau blad wilt beheren, [gebruikt u log Analytics voor de functie diagnostische gegevens](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Monitor voor virtueel bureau blad van Windows in de volgende artikelen:

- [Gebruik Azure monitor voor virtuele Windows-Bureau bladen om uw implementatie te bewaken](azure-monitor.md).
- Gebruik de [woorden lijst](azure-monitor-glossary.md) voor meer informatie over de voor waarden en concepten.
- Als u een probleem ondervindt, raadpleegt u de [hand leiding](troubleshoot-azure-monitor.md) voor het oplossen van problemen.
- Bekijk het [gebruik van de controle en de geschatte kosten in azure monitor](../azure-monitor/usage-estimated-costs.md) voor meer informatie over het beheren van uw bewakings kosten.
