---
title: Prestatietracering aan de clientzijde maken
description: Aanbevolen procedures voor het profileren van prestaties aan client zijde met WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92204092"
---
# <a name="create-client-side-performance-traces"></a>Prestatietracering aan de clientzijde maken

Er zijn verschillende redenen waarom de prestaties van de externe rendering van Azure niet zo goed mogelijk zijn. Afgezien van de zuivere rendering-prestaties op de Cloud Server, met name de kwaliteit van de netwerk verbinding, heeft dit een grote invloed op de ervaring. Als u de prestaties van de server wilt profileren, raadpleegt u de hoofd stukken voor [prestatie query's](../overview/features/performance-queries.md)op de server.

Dit hoofd stuk richt zich op het identificeren van mogelijke knel punten aan de client zijde via *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Aan de slag

Als u niet bekend bent met de Windows :::no-loc text="performance tracing"::: -functionaliteit, wordt in deze sectie de meest fundamentele voor waarden en toepassingen vermeld om u op weg te helpen.

### <a name="installation"></a>Installatie

De toepassingen die worden gebruikt voor tracering met ARR zijn hulpprogram ma's voor algemeen gebruik die kunnen worden gebruikt voor alle Windows-ontwikkel aars. Ze worden via de [Windows-prestatie Toolkit](/windows-hardware/test/wpt/)verschaft. Als u deze Toolkit wilt downloaden, downloadt u de [Windows Assessment and Deployment Kit](/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologie

Wanneer u zoekt naar informatie over prestatie traceringen, zult u onvermijdelijk over een reeks voor waarden komen. De belangrijkste zijn:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**Etw** staat voor [ **E** ventilator **T**-race voor **W** Windows](/windows/win32/etw/about-event-tracing). Het is gewoon de naam van de overkoepelend voor de efficiënte tracering faciliteit op kernelniveau die is ingebouwd in Windows. Het wordt *gebeurtenis* tracering genoemd, omdat toepassingen die ondersteuning bieden voor etw, gebeurtenissen kunnen verzenden naar logboek acties die kunnen helpen bij het volgen van prestatie problemen. Standaard verzendt het besturings systeem al gebeurtenissen voor zaken als schijf toegang, taak switches en dergelijke. Toepassingen zoals ARR verzenden bovendien aangepaste gebeurtenissen, bijvoorbeeld het geval van verwijderde frames, netwerk vertraging, enzovoort.

**ETL** staat voor **E** ventilator **T** race **L** ogging. Dit betekent gewoon dat een tracering is verzameld (vastgelegd) en wordt daarom doorgaans gebruikt als de bestands extensie voor bestanden die de tracerings gegevens opslaan. Als u een tracering volgt, hebt u meestal een \* ETL-bestand.

De **aanvraag staat voor** [ **W** Windows **P** erformance **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder) en is de naam van de toepassing die de registratie van gebeurtenis traceringen start en stopt. Voor het aanmeldingen wordt een profiel bestand ( \* . wprp) gebruikt dat de exacte gebeurtenissen configureert die moeten worden geregistreerd. Een dergelijk `wprp` bestand wordt meegeleverd met de ARR-SDK. Bij het uitvoeren van traceringen op een desktop computer, kunt u direct op de slag starten. Wanneer u een tracering op de HoloLens uitvoert, gaat u doorgaans door de webinterface.

**WPA** staat voor [ **W** Windows **P** erformance **A** nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) en is de naam van de GUI-toepassing die wordt gebruikt voor \* het openen van. etl-bestanden en SIFT door de gegevens om prestatie problemen te identificeren. Met WPA kunt u gegevens sorteren op verschillende criteria, de gegevens op verschillende manieren weer geven, meer informatie bekijken en gegevens correleren.

U kunt ETL-traceringen maken op elk Windows-apparaat (lokale PC, HoloLens, Cloud Server, enzovoort), ze worden meestal opgeslagen op schijf en geanalyseerd met WPA op een desktop-PC. ETL-bestanden kunnen naar andere ontwikkel aars worden verzonden om ze te laten zien. Houd er rekening mee dat gevoelige informatie, zoals bestands paden en IP-adressen, in ETL-traceringen kan worden vastgelegd. U kunt ETW op twee manieren gebruiken: voor het vastleggen van traceringen of voor het analyseren van traceringen. Registratie traceringen zijn recht vooruit en vereisen een minimale installatie. Voor het analyseren van traceringen voor de andere kant is een goede uitleg vereist van zowel het WPA-hulp programma als het probleem dat u onderzoekt. Algemeen materiaal voor Learning WPA vindt u hieronder en richt lijnen voor het interpreteren van ARR-specifieke traceringen.

## <a name="recording-a-trace-on-a-local-pc"></a>Een tracering op een lokale computer vastleggen

Als u problemen met ARR-prestaties wilt identificeren, kunt u het beste rechtstreeks op een HoloLens volgen, omdat dit de enige manier is om een moment opname van de werkelijke prestatie kenmerken te verkrijgen. Als u echter specifiek een tracering wilt uitvoeren zonder de prestatie beperkingen van HoloLens of als u wilt weten hoe u WPA kunt gebruiken en geen realistische tracering nodig hebt, kunt u dit ook doen.

### <a name="wpr-configuration"></a>Configuratie van de aanaan

1. Start de [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) vanuit het *menu Start*.
1. **Meer opties** uitbreiden
1. Klik op **profielen toevoegen...**
1. Selecteer het bestand *AzureRemoteRenderingNetworkProfiling. wprp*. U kunt dit bestand vinden in de ARR SDK onder *tools/ETLProfiles*.
   Het profiel wordt nu weer gegeven in de lijst met *maat eenheden onder Aangepaste metingen*. Zorg ervoor dat dit het enige ingeschakelde profiel is.
1. Sorteren van het *eerste niveau* uitvouwen:
    * Als u alleen een snelle tracering van de ARR-netwerk gebeurtenissen wilt vastleggen, schakelt u deze optie **uit** .
    * Schakel deze optie **in** als u ARR-netwerk gebeurtenissen moet correleren met andere systeem kenmerken, zoals CPU of geheugen gebruik.
    * Als u deze optie inschakelt, is de tracering waarschijnlijk meerdere gigabytes groot en kan het lang duren om op te slaan en te openen in WPA.

Daarna moet de configuratie van uw aangaan eruitzien als volgt:

![Configuratie van de aanaan](./media/wpr.png)

### <a name="recording"></a>Opnemen

Klik op **starten** om het vastleggen van een tracering te starten. U kunt de opname op elk gewenst moment starten en stoppen. u hoeft uw toepassing niet te sluiten voordat u dit doet. Zoals u kunt zien, is het niet nodig om op te geven welke toepassing moet worden getraceerd, omdat ETW altijd een tracering vastlegt voor het hele systeem. `wprp`In het bestand wordt opgegeven welke typen gebeurtenissen moeten worden vastgelegd.

Klik op **Opslaan** om de opname te stoppen en op te geven waar het ETL-bestand moet worden opgeslagen.

U hebt nu een ETL-bestand dat u rechtstreeks kunt openen in WPA of naar iemand anders wilt verzenden.

## <a name="recording-a-trace-on-a-hololens"></a>Een tracering op een HoloLens vastleggen

Als u een tracering op een HoloLens wilt vastleggen, start u uw apparaat op en voert u het IP-adres in een browser in om de portal van het *apparaat* te openen.

![Portal voor apparaten](./media/wpr-hl.png)

1. Ga aan de linkerkant naar *prestaties > prestatie tracering*.
1. **Aangepaste profielen** selecteren
1. Schakel **:::no-loc text="Browse...":::**
1. Selecteer het bestand *AzureRemoteRenderingNetworkProfiling. wprp*. U kunt dit bestand vinden in de ARR SDK onder *tools/ETLProfiles*.
1. Klik op **Tracering starten**
1. De HoloLens is nu bezig met het vastleggen van een tracering. Zorg ervoor dat de prestatie problemen die u wilt onderzoeken, worden geactiveerd. Klik vervolgens op **tracering stoppen**.
1. De tracering wordt weer gegeven aan de onderkant van de webpagina. Klik aan de rechter kant op het schijf pictogram om het ETL-bestand te downloaden.

U hebt nu een ETL-bestand dat u rechtstreeks kunt openen in WPA of naar iemand anders wilt verzenden.

## <a name="analyzing-traces-with-wpa"></a>Traceringen analyseren met WPA

### <a name="wpa-basics"></a>Basis beginselen van WPA

Windows Performance Analyzer is het standaard programma voor het openen van ETL-bestanden en het controleren van de traceringen. Een uitleg hoe WPA werkt buiten het bereik van dit artikel. Bekijk de volgende bronnen om aan de slag te gaan:

* Bekijk de [inleidende Video's](/windows-hardware/test/wpt/windows-performance-analyzer) voor een eerste overzicht.
* WPA zelf bevat een tabblad *aan de slag* , waarin algemene stappen worden uitgelegd. Bekijk de beschik bare onderwerpen. Met name bij ' gegevens weer geven ' krijgt u een snelle inleiding hoe u grafieken voor specifieke gegevens kunt maken.
* Er is een uitstekende informatie [over deze website](https://randomascii.wordpress.com/2015/09/24/etw-central/), maar dit is niet alle relevant voor beginners.

### <a name="graphing-data"></a>Grafiek gegevens

Om aan de slag te gaan met ARR-tracering zijn de volgende onderdelen goed te weten.

![Prestatie grafiek](./media/wpa-graph.png)

In de bovenstaande afbeelding ziet u een tabel met tracerings gegevens en een grafiek weergave van dezelfde gegevens.

Kijk in de tabel aan de onderkant van de gele (gouden) balk en de blauwe balk. U kunt deze balken slepen en op elke positie plaatsen.

Alle **kolommen links van de gele balk** worden als **sleutels** geïnterpreteerd. Sleutels worden gebruikt om de structuur te structureren in het venster linksboven. Hier ziet u twee *belang rijke* kolommen: ' provider naam ' en ' taak naam '. De boom structuur in het venster linksboven is dus twee niveaus dieper. Als u de volg orde van de kolommen of het toevoegen of verwijderen van kolommen uit het sleutel gebied wijzigt, wordt de structuur in de structuur weergave gewijzigd.

**Kolommen aan de rechter kant van de blauwe balk** worden gebruikt voor de **grafiek weergave** in het venster rechtsboven. De meeste tijd wordt alleen de eerste kolom gebruikt, maar voor sommige grafiek modi zijn meerdere kolommen met gegevens vereist. Lijn grafieken werkt alleen als de *aggregatie modus* voor die kolom is ingesteld. Gebruik ' AVG ' of ' Max '. De aggregatie modus wordt gebruikt om de waarde van de grafiek te bepalen op basis van een pixel, wanneer een pixel een bereik met meerdere gebeurtenissen bedekt. Dit kan worden waargenomen door aggregatie in te stellen op Sum en vervolgens in en uit te zoomen.

De kolommen in het midden hebben geen speciale betekenis.

![Weer gave gebeurtenissen](./media/wpa-event-view.png)

In de *algemene gebeurtenissen weergave-editor* kunt u alle kolommen configureren die worden weer gegeven, de samenvoegings modus, sorteren en welke kolommen worden gebruikt als sleutels of voor het maken van grafieken. In het bovenstaande voor beeld is **veld 2** ingeschakeld en wordt veld 3-6 uitgeschakeld. Veld 2 is doorgaans het eerste *aangepaste gegevens* veld van een etw-gebeurtenis en dus voor ARR "FrameStatistics"-gebeurtenissen, die een bepaalde netwerk latentie waarde vertegenwoordigen. Andere veld kolommen inschakelen om verdere waarden van deze gebeurtenis weer te geven.

### <a name="presets"></a>Stations

Als u een tracering correct wilt analyseren, moet u uw eigen werk stroom en de voor keur voor de gegevens weergave ervan bepalen. Als u echter een snel overzicht wilt krijgen van de ARR-specifieke gebeurtenissen, bevatten we Windows Software Protection platform profiel en voor instellingen bestanden in de map *tools/ETLProfiles*. Als u een volledig profiel wilt laden, selecteert u *profielen > Toep assen...* via de menu balk van WPA of opent u het deel venster *mijn voor instellingen* (*venster > mijn voor* keuren) en selecteert u *importeren*. Met de eerste wordt een volledige WPA-configuratie ingesteld, zoals in de onderstaande afbeelding. Er worden alleen voor instellingen gemaakt voor de verschillende weergave configuraties die beschikbaar zijn en u kunt snel een weer gave openen om een specifiek stukje gebeurtenis gegevens te bekijken.

![Stations](./media/wpa-arr-trace.png)

In de bovenstaande afbeelding ziet u weer gaven van verschillende ARR-specifieke gebeurtenissen plus een weer gave van het totale CPU-gebruik.

## <a name="next-steps"></a>Volgende stappen

* [Prestatiequery's aan serverzijde](../overview/features/performance-queries.md)