---
title: Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van geografisch redundante opslag voor het ontwerpen van een Maxi maal beschik bare toepassing die flexibel genoeg is voor het afhandelen van uitval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a07acedadfaf3d5158ba8e494d4527301655425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035098"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen

Een gemeen schappelijke functie van Cloud infrastructuren zoals Azure Storage is dat ze een Maxi maal beschikbaar en duurzaam platform bieden voor het hosten van gegevens en toepassingen. Ontwikkel aars van Cloud toepassingen moeten zorgvuldig nadenken over het gebruik van dit platform om deze voor delen voor hun gebruikers te maximaliseren. Azure Storage biedt geografisch redundante opslag om te zorgen voor hoge Beschik baarheid, zelfs in het geval van een regionale storing. Opslag accounts die zijn geconfigureerd voor geo-redundante replicatie, worden synchroon gerepliceerd in de primaire regio en vervolgens asynchroon gerepliceerd naar een secundaire regio die honderden kilo meters is.

Azure Storage biedt twee opties voor geo-redundante replicatie. Het enige verschil tussen deze twee opties is hoe gegevens worden gerepliceerd in de primaire regio:

* [Geo-zone-redundante opslag (GZRS)](storage-redundancy.md): gegevens worden synchroon gerepliceerd over drie Azure-beschikbaarheids zones in de primaire regio met *zone-redundante opslag (ZRS)* en vervolgens asynchroon gerepliceerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch zone-redundante opslag met lees toegang (RA-GZRS) in.

    Micro soft raadt aan om GZRS/RA-GZRS te gebruiken voor scenario's waarvoor maximale Beschik baarheid en duurzaamheid zijn vereist.

* [Geografisch redundante opslag (GRS)](storage-redundancy.md): gegevens worden synchroon binnen de primaire regio gesynchroniseerd met *lokaal redundante opslag (LRS)* en vervolgens asynchroon gerepliceerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch redundante opslag met leestoegang (RA-GRS) in.

In dit artikel wordt uitgelegd hoe u uw toepassing kunt ontwerpen voor het afhandelen van een storing in de primaire regio. Als de primaire regio niet beschikbaar is, kan uw toepassing worden aangepast om Lees bewerkingen uit te voeren op de secundaire regio. Zorg ervoor dat uw opslag account is geconfigureerd voor RA-GRS of RA-GZRS voordat u aan de slag gaat.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Overwegingen bij het ontwerpen van toepassingen bij het lezen van de secundaire

In dit artikel wordt beschreven hoe u een toepassing kunt ontwerpen die blijft functioneren (in tegens telling tot een beperkte capaciteit), zelfs in het geval van een grote nood situatie bij het primaire Data Center. U kunt uw toepassing ontwerpen voor het afhandelen van tijdelijke of langlopende problemen door te lezen uit de secundaire regio wanneer er een probleem is met het lezen van de primaire regio. Wanneer de primaire regio weer beschikbaar is, kan uw toepassing terugkeren naar het lezen van de primaire regio.

Houd bij het ontwerpen van uw toepassing voor RA-GRS of RA-GZRS de volgende belang rijke punten in acht:

* Azure Storage onderhoudt een alleen-lezen kopie van de gegevens die u in de primaire regio opslaat in een secundaire regio. Zoals hierboven vermeld, bepaalt de opslag service de locatie van de secundaire regio.

* De alleen-lezen kopie is [uiteindelijk consistent](https://en.wikipedia.org/wiki/Eventual_consistency) met de gegevens in de primaire regio.

* Voor blobs, tabellen en wacht rijen kunt u een query uitvoeren op de secundaire regio voor de waarde van de *laatste synchronisatie tijd* die aangeeft wanneer de laatste replicatie van de primaire naar de secundaire regio is opgetreden. (Dit wordt niet ondersteund voor Azure Files, die op dit moment geen RA-GRS-redundantie heeft.)

* U kunt de Storage-client bibliotheek gebruiken voor het lezen en schrijven van gegevens in de primaire of secundaire regio. U kunt ook automatisch Lees aanvragen omleiden naar de secundaire regio als er een time-out optreedt voor een lees aanvraag naar de primaire regio.

* Als de primaire regio niet beschikbaar is, kunt u een failover voor het account initiëren. Wanneer u een failover naar de secundaire regio maakt, worden de DNS-vermeldingen die verwijzen naar de primaire regio, gewijzigd zodat ze naar de secundaire regio wijzen. Nadat de failover is voltooid, wordt de schrijf toegang voor GRS-en RA-GRS-accounts hersteld. Zie [nood herstel en failover van het opslag account](storage-disaster-recovery-guidance.md)voor meer informatie.

### <a name="using-eventually-consistent-data"></a>Uiteindelijk consistente gegevens gebruiken

In de voorgestelde oplossing wordt ervan uitgegaan dat het acceptabel is om mogelijk verouderde gegevens te retour neren naar de aanroepende toepassing. Omdat de gegevens in de secundaire regio uiteindelijk consistent zijn, is het mogelijk dat de primaire regio niet meer toegankelijk is voordat een update naar de secundaire regio is gerepliceerd.

Stel bijvoorbeeld dat uw klant een update verzendt, maar de primaire regio is mislukt voordat de update wordt door gegeven aan de secundaire regio. Wanneer de klant vraagt om de gegevens terug te lezen, ontvangen ze de verouderde gegevens uit de secundaire regio in plaats van de bijgewerkte gegevens. Bij het ontwerpen van uw toepassing moet u bepalen of dit acceptabel is en, als dat het geval is, hoe u een bericht ontvangt van de klant. 

Verderop in dit artikel laten we zien hoe u de laatste synchronisatie tijd voor de secundaire gegevens controleert om te controleren of de secundaire is bijgewerkt.

### <a name="handling-services-separately-or-all-together"></a>Services afzonderlijk of allemaal samen verwerken

Hoewel het niet waarschijnlijk is, is het mogelijk dat één service niet meer beschikbaar is terwijl de andere services nog steeds volledig functioneel zijn. U kunt de nieuwe pogingen en de modus alleen-lezen voor elke service afzonderlijk (blobs, wacht rijen, tabellen) afhandelen of u kunt nieuwe pogingen voor alle opslag Services op een algemene manier afhandelen.

Als u bijvoorbeeld wacht rijen en blobs gebruikt in uw toepassing, kunt u ervoor kiezen om afzonderlijke code te plaatsen voor het afhandelen van herstel bare fouten voor elk van deze. Als u vervolgens een nieuwe poging van de BLOB-service krijgt, maar de wachtrij service nog steeds werkt, wordt alleen het deel van uw toepassing dat blobs verwerkt, beïnvloed. Als u besluit alle pogingen van alle opslag Services algemeen uit te voeren en een aanroep naar de BLOB-service een herstel bare fout retourneert, worden de aanvragen voor zowel de BLOB-service als de wachtrij service beïnvloed.

Uiteindelijk is dit afhankelijk van de complexiteit van uw toepassing. U kunt ervoor kiezen om de fouten op de service niet af te handelen, maar in plaats daarvan de Lees aanvragen voor alle opslag Services om te leiden naar de secundaire regio en de toepassing uit te voeren in de modus alleen-lezen wanneer u een probleem met een opslag service in de primaire regio detecteert.

### <a name="other-considerations"></a>Andere overwegingen

Dit zijn de andere overwegingen die in de rest van dit artikel worden besproken.

* Verwerkings pogingen van Lees aanvragen met het patroon circuit onderbreker

* Uiteindelijk consistente gegevens en de laatste synchronisatie tijd

* Testen

## <a name="running-your-application-in-read-only-mode"></a>Uw toepassing uitvoeren in de modus alleen-lezen

Om effectief een storing in de primaire regio voor te bereiden, moet u zowel mislukte lees aanvragen als mislukte update aanvragen kunnen verwerken (met update in dit geval worden invoeg-, update-en verwijderingen uitgevoerd). Als er een fout optreedt in de primaire regio, kunnen Lees aanvragen worden omgeleid naar de secundaire regio. Update aanvragen kunnen echter niet worden omgeleid naar het secundaire omdat de secundaire alleen-lezen is. Daarom moet u uw toepassing ontwerpen om te worden uitgevoerd in de modus alleen-lezen.

U kunt bijvoorbeeld een vlag instellen die wordt gecontroleerd voordat update aanvragen worden ingediend bij Azure Storage. Wanneer een van de update aanvragen doorloopt, kunt u deze overs Laan en een juiste reactie op de klant retour neren. U kunt zelfs bepaalde functies helemaal uitschakelen totdat het probleem is opgelost en gebruikers ervan op de hoogte stellen dat deze functies tijdelijk niet beschikbaar zijn.

Als u besluit fouten voor elke service afzonderlijk af te handelen, moet u ook de mogelijkheid afhandelen om uw toepassing uit te voeren in de modus alleen-lezen door de service. U kunt bijvoorbeeld alleen-lezen vlaggen hebben voor elke service die kan worden ingeschakeld en uitgeschakeld. Vervolgens kunt u de markering op de juiste plaatsen in uw code afhandelen.

Het uitvoeren van uw toepassing in de modus alleen-lezen heeft een ander voor deel: het biedt u de mogelijkheid om beperkte functionaliteit te garanderen tijdens een belang rijke toepassings upgrade. U kunt ervoor zorgen dat uw toepassing wordt uitgevoerd in de modus alleen-lezen en het tweede Data Center aanwijst, zodat niemand toegang heeft tot de gegevens in de primaire regio terwijl u upgrades gaat maken.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Updates verwerken tijdens het uitvoeren in de modus alleen-lezen

Er zijn veel manieren om update-aanvragen af te handelen wanneer ze worden uitgevoerd in de modus alleen-lezen. Dit wordt niet uitgebreid besproken, maar over het algemeen zijn er een aantal patronen die u overweegt.

1. U kunt reageren op uw gebruiker en hen laten weten dat u momenteel geen updates accepteert. Een beheer systeem voor contact personen kan bijvoorbeeld klanten in staat stellen om toegang te krijgen tot contact gegevens, maar geen updates te maken.

2. U kunt uw updates in een andere regio in de wachtrij plaatsen. In dit geval schrijft u uw update aanvragen in behandeling naar een wachtrij in een andere regio en vervolgens een manier om deze aanvragen te verwerken nadat het primaire Data Center weer online is. In dit scenario moet de klant weten dat de aangevraagde update in de wachtrij staat voor latere verwerking.

3. U kunt uw updates schrijven naar een opslag account in een andere regio. Wanneer het primaire Data Center weer online komt, kunt u de updates ook samen voegen in de primaire gegevens, afhankelijk van de structuur van de gegevens. Als u bijvoorbeeld afzonderlijke bestanden met een datum/tijds tempel in de naam maakt, kunt u deze bestanden weer kopiëren naar de primaire regio. Dit werkt voor sommige werk belastingen, zoals logboek registratie en iOT-gegevens.

## <a name="handling-retries"></a>Verwerkings pogingen

De Azure Storage-client bibliotheek helpt u te bepalen welke fouten opnieuw kunnen worden uitgevoerd. Er wordt bijvoorbeeld een 404-fout (resource niet gevonden) opnieuw uitgevoerd, omdat het niet waarschijnlijk is dat het opnieuw wordt geprobeerd. Aan de andere kant kan een 500-fout opnieuw worden uitgevoerd omdat het een server fout is en het probleem mogelijk alleen een tijdelijk probleem is. Bekijk voor meer informatie de open- [bron code voor de klasse ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) in de client bibliotheek voor .net-opslag. (Zoek naar de methode ShouldRetry.)

### <a name="read-requests"></a>Aanvragen lezen

Lees aanvragen kunnen worden omgeleid naar secundaire opslag als er een probleem is met primaire opslag. Zoals hierboven vermeld in het [gebruik van uiteindelijk consistente gegevens](#using-eventually-consistent-data), moet het acceptabel zijn voor uw toepassing om mogelijk verouderde gegevens te lezen. Als u de Storage-client bibliotheek gebruikt voor toegang tot gegevens van de secundaire, kunt u het gedrag voor opnieuw proberen van een lees aanvraag opgeven door een waarde voor de eigenschap **LocationMode** in te stellen op een van de volgende:

* **PrimaryOnly** (de standaard instelling)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Wanneer u de **LocationMode** instelt op **PrimaryThenSecondary** en de eerste Lees aanvraag naar het primaire eind punt mislukt met een fout die opnieuw kan worden geprobeerd, maakt de client automatisch een andere Lees aanvraag naar het secundaire eind punt. Als de fout een time-out voor de server is, moet de client wachten tot de time-out is verstreken voordat er een herstel bare fout van de service wordt ontvangen.

Er zijn in feite twee scenario's waarmee u rekening moet houden wanneer u beslist hoe u kunt reageren op een herstel bare fout:

* Dit is een geïsoleerd probleem en de volgende aanvragen voor het primaire eind punt retour neren geen herstel bare fout. Een voor beeld hiervan is dat dit kan gebeuren wanneer zich een tijdelijke netwerk fout voordoet.

    In dit scenario is er geen aanzienlijke prestatie vermindering in de **LocationMode** ingesteld op **PrimaryThenSecondary** , omdat dit alleen zelden gebeurt.

* Dit is een probleem met ten minste één van de opslag Services in de primaire regio en alle daaropvolgende aanvragen voor die service in de primaire regio zullen waarschijnlijk voor een bepaalde tijd herstel bare fouten retour neren. Een voor beeld hiervan is als de primaire regio volledig niet toegankelijk is.

    In dit scenario is er sprake van een prestatie vermindering, omdat al uw Lees aanvragen eerst het primaire eind punt proberen. wacht totdat de time-out is verlopen en schakel vervolgens over naar het secundaire eind punt.

Voor deze scenario's moet u nagaan of er een doorlopend probleem met het primaire eind punt is en alle Lees aanvragen rechtstreeks naar het secundaire eind punt verzenden door de eigenschap **LocationMode** in te stellen op **SecondaryOnly**. Op dit moment moet u ook de toepassing wijzigen zodat deze wordt uitgevoerd in de modus alleen-lezen. Deze benadering wordt het [patroon circuit onderbreker](/azure/architecture/patterns/circuit-breaker)genoemd.

### <a name="update-requests"></a>Update aanvragen

Het patroon circuit onderbreker kan ook worden toegepast op update aanvragen. Update aanvragen kunnen echter niet worden omgeleid naar secundaire opslag, die alleen-lezen is. Voor deze aanvragen moet u de eigenschap **LocationMode** instellen op **PrimaryOnly** (de standaard instelling). Als u deze fouten wilt afhandelen, kunt u een metriek Toep assen op deze aanvragen, zoals 10 fouten in een rij, en wanneer aan de drempel waarde wordt voldaan, de toepassing overschakelen naar de modus alleen-lezen. U kunt dezelfde methoden gebruiken om terug te gaan naar de update modus, zoals hieronder wordt beschreven in de volgende sectie over het patroon circuit onderbreker.

## <a name="circuit-breaker-pattern"></a>Patroon Circuitonderbreker

Het gebruik van het patroon circuit onderbreker in uw toepassing kan voor komen dat het opnieuw proberen van een bewerking die zich waarschijnlijk herhaaldelijk kan voordoen. Hiermee kan de toepassing blijven worden uitgevoerd in plaats van dat er tijd wordt bespaard wanneer de bewerking exponentieel wordt herhaald. Er wordt ook gedetecteerd wanneer de fout is opgelost, op het moment dat de toepassing de bewerking opnieuw kan proberen.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Het circuit onderbreker-patroon implementeren

Als u wilt weten dat er een probleem is met een primair eind punt, kunt u bewaken hoe vaak de client herstel bare fouten tegen komt. Omdat elke case afwijkt, moet u beslissen over de drempel waarde die u wilt gebruiken voor de beslissing om over te scha kelen naar het secundaire eind punt en de toepassing uit te voeren in de modus alleen-lezen. U kunt er bijvoorbeeld voor kiezen om de switch uit te voeren als er 10 storingen zijn in een rij zonder succes. Een ander voor beeld is om te scha kelen als 90% van de aanvragen in een periode van twee minuten mislukt.

In het eerste scenario kunt u eenvoudig een telling van de fouten bedenken. als er een succes is voordat het maximum wordt bereikt, stelt u het aantal in op nul. Voor het tweede scenario is het een manier om deze te implementeren met behulp van het Memory Cache-object (in .NET). Voor elke aanvraag voegt u een CacheItem toe aan de cache, stelt u de waarde in op geslaagd (1) of mislukt (0) en stelt u de verloop tijd in op twee minuten vanaf nu (of op een wille keurige tijds beperking). Wanneer de verloop tijd van een vermelding is bereikt, wordt de vermelding automatisch verwijderd. Hiermee krijgt u een rolling periode van twee minuten. Telkens wanneer u een aanvraag voor de opslag service maakt, gebruikt u eerst een LINQ-query in het Memory Cache-object om het percentage succes te berekenen door de waarden op te tellen en te delen door het aantal. Als het voltooiings percentage onder een bepaalde drempel waarde (zoals 10%) daalt, stelt u de eigenschap **LocationMode** voor lees aanvragen in op **SecondaryOnly** en schakelt u de toepassing over naar alleen-lezen modus voordat u doorgaat.

De drempel van fouten die worden gebruikt om te bepalen wanneer de switch kan variëren van service naar service in uw toepassing, moet u overwegen om deze Configureer bare para meters te maken. Dit is ook de plek waar u herstel bare fouten van elke service afzonderlijk of als één kunt afhandelen, zoals eerder is besproken.

Een andere overweging is hoe u meerdere exemplaren van een toepassing kunt verwerken en wat u moet doen wanneer u herstel bare fouten in elk exemplaar detecteert. U kunt bijvoorbeeld 20 Vm's uitvoeren met dezelfde toepassing geladen. Gaat u elk exemplaar afzonderlijk verwerken? Als er met één exemplaar wordt gestart, wilt u het antwoord op slechts één instantie beperken of wilt u proberen alle instanties op dezelfde manier te laten reageren als er een probleem is met één exemplaar? Het afhandelen van de instanties is veel eenvoudiger dan het maken van de reactie op de afzonderlijke items, maar hoe u dit doet, is afhankelijk van de architectuur van uw toepassing.

### <a name="options-for-monitoring-the-error-frequency"></a>Opties voor het bewaken van de fout frequentie

Er zijn drie belang rijke opties voor het bewaken van de frequentie van nieuwe pogingen in de primaire regio om te bepalen wanneer moet worden overgeschakeld naar de secundaire regio en de toepassing te wijzigen zodat deze wordt uitgevoerd in de modus alleen-lezen.

* Een handler toevoegen [**voor het**](/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) [**OperationContext**](/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) -object dat u aan uw opslag aanvragen door gegeven. Dit is de methode die wordt weer gegeven in dit artikel en wordt gebruikt in het bijbehorende voor beeld. Deze gebeurtenissen worden geactiveerd wanneer de client een aanvraag opnieuw probeert, zodat u kunt bijhouden hoe vaak de client herstel bare fouten op een primair eind punt tegen komt.

    # <a name="net-v12"></a>[.NET v12](#tab/current)

    We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

    # <a name="net-v11"></a>[.NET v11](#tab/legacy)

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```
    ---

* In de methode [**Evaluate**](/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) in een aangepast beleid voor opnieuw proberen kunt u aangepaste code uitvoeren wanneer een nieuwe poging plaatsvindt. Naast het vastleggen wanneer een nieuwe poging gebeurt, biedt dit ook de mogelijkheid om het gedrag voor opnieuw proberen aan te passen.

    # <a name="net-v12"></a>[.NET v12](#tab/current)

    We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

    # <a name="net-v11"></a>[.NET v11](#tab/legacy)

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```
    ---

* De derde benadering is het implementeren van een aangepast bewakings onderdeel in uw toepassing dat uw primaire opslag eindpunt continu pingt met Dummy Lees aanvragen (zoals het lezen van een kleine blob) om de status te bepalen. Dit kan enkele resources duren, maar dit is geen aanzienlijke hoeveelheid. Wanneer er een probleem wordt gedetecteerd dat de drempel waarde bereikt, voert u de switch uit naar **SecondaryOnly** en de modus alleen-lezen.

Op een bepaald moment wilt u terugschakelen naar het primaire eind punt en kunnen updates worden toegestaan. Als u een van de eerste twee hierboven vermelde methoden gebruikt, kunt u gewoon teruggaan naar het primaire eind punt en de update modus inschakelen na een wille keurige geselecteerde hoeveelheid tijd of aantal bewerkingen is uitgevoerd. U kunt deze vervolgens opnieuw laten door lopen met de logica voor opnieuw proberen. Als het probleem is opgelost, blijft het het primaire eind punt gebruiken en kunnen updates worden toegestaan. Als er nog steeds een probleem is, keert het opnieuw naar het secundaire eind punt en de modus alleen-lezen nadat de criteria die u hebt ingesteld, zijn mislukt.

Bij het derde scenario wordt het pingen van het primaire opslag eindpunt weer geslaagd door de switch terug te zetten naar **PrimaryOnly** en door te gaan met het toestaan van updates.

## <a name="handling-eventually-consistent-data"></a>Uiteindelijk consistente gegevens verwerken

Geografisch redundante opslag werkt door trans acties te repliceren van de primaire naar de secundaire regio. Dit replicatie proces garandeert dat de gegevens in de secundaire regio *uiteindelijk consistent* zijn. Dit betekent dat alle trans acties in de primaire regio uiteindelijk worden weer gegeven in de secundaire regio, maar dat er een vertraging kan zijn voordat ze worden weer gegeven en dat er geen garantie is dat de trans acties in de secundaire regio worden ontvangen in dezelfde volg orde als die waarin ze oorspronkelijk werden toegepast in de primaire regio. Als uw trans acties in de secundaire regio buiten de juiste volg orde arriveren, *kunt u de* gegevens in de secundaire regio inconsistent maken, totdat de service wordt opgevangen.

In de volgende tabel ziet u een voor beeld van wat er kan gebeuren wanneer u de details van een werk nemer bijwerkt om ze lid te maken van de rol *Administrators* . Voor dit voor beeld moet u de entiteit **werk nemer** bijwerken en een entiteit **rol beheerder** bijwerken met een telling van het totale aantal beheerders. U ziet hoe de updates in de secundaire regio in de juiste volg orde worden toegepast.

| **Tijd** | **Transactie**                                            | **Replicatie**                       | **Tijdstip van de laatste synchronisatie** | **Resultaat** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Trans actie A: <br> Werk nemer invoegen <br> entiteit in primaire |                                   |                    | Trans actie A ingevoegd op primaire,<br> nog niet gerepliceerd. |
| T1       |                                                            | Trans actie A <br> gerepliceerd naar<br> primaire | T1 | Trans actie A gerepliceerd naar secundair. <br>Laatste synchronisatie tijd bijgewerkt.    |
| T2       | Trans actie B:<br>Bijwerken<br> entiteit werk nemer<br> in primaire  |                                | T1                 | Trans actie B, geschreven naar primair,<br> nog niet gerepliceerd.  |
| T3       | Trans actie C:<br> Bijwerken <br>beheerder<br>rol-entiteit in<br>basis |                    | T1                 | Trans actie C geschreven naar primair,<br> nog niet gerepliceerd.  |
| *T4*     |                                                       | Trans actie C <br>gerepliceerd naar<br> primaire | T1         | Trans actie C gerepliceerd naar secundair.<br>LastSyncTime is niet bijgewerkt omdat <br>trans actie B is nog niet gerepliceerd.|
| *T5*     | Entiteiten lezen <br>van secundaire                           |                                  | T1                 | U krijgt de verouderde waarde voor werk nemer <br> entiteit omdat trans actie B niet <br> gerepliceerd. U krijgt de nieuwe waarde voor<br> entiteit van beheerdersrol omdat C<br> bijgewerkt. Laatste synchronisatie tijd nog niet<br> bijgewerkt omdat trans actie B<br> is niet gerepliceerd. U kunt de<br>entiteit van beheerdersrol is inconsistent <br>omdat de datum/tijd van de entiteit na <br>de laatste synchronisatie tijd. |
| *T6*     |                                                      | Trans actie B<br> gerepliceerd naar<br> primaire | T6                 | *T6* : alle trans acties via C hebben <br>gerepliceerd, laatste synchronisatie tijd<br> is bijgewerkt. |

In dit voor beeld wordt ervan uitgegaan dat de client overschakelt van de secundaire regio op t 5. De entiteit **rol van beheerder** kan op dit moment worden gelezen, maar de entiteit bevat een waarde voor het aantal beheerders dat niet consistent is met het aantal **werknemers** entiteiten dat op dit moment als Administrators is gemarkeerd. Uw client zou deze waarde gewoon kunnen weer geven, met het risico dat het inconsistente informatie is. De client kan ook proberen te bepalen dat de beheerdersrol een mogelijk inconsistente **status heeft omdat** de updates in de juiste volg orde zijn verlopen en vervolgens de gebruiker op de hoogte stellen van dit feit.

Om te herkennen dat het mogelijk inconsistente gegevens bevat, kan de client de waarde van de *laatste synchronisatie tijd* gebruiken die u op elk gewenst moment kunt bereiken door een opslag service te doorzoeken. Dit geeft u het tijdstip waarop de gegevens in de secundaire regio voor het laatst consistent zijn en toen de service alle trans acties vóór dat moment heeft toegepast. In het bovenstaande voor beeld, nadat de **werknemers** entiteit in de secundaire regio is ingevoegd, wordt de laatste synchronisatie tijd ingesteld op *T1*. Deze blijft op *T1* totdat de service de werknemers entiteit in de secundaire regio **bijwerkt** wanneer deze is ingesteld op *T6*. Als de client de laatste synchronisatie tijd ophaalt bij het lezen van de entiteit op *T5*, kan deze worden vergeleken met de tijds tempel van de entiteit. Als de tijds tempel van de entiteit later is dan de laatste synchronisatie tijd, heeft de entiteit een mogelijk inconsistente status en kunt u de juiste actie ondernemen voor uw toepassing. Als u dit veld wilt gebruiken, moet u weten wanneer de laatste update voor de primaire is voltooid.

Zie voor meer informatie over het controleren van de laatste synchronisatie tijd [de eigenschap laatste synchronisatie tijd voor een opslag account](last-sync-time-get.md).

## <a name="testing"></a>Testen

Het is belang rijk om te testen of uw toepassing werkt zoals verwacht wanneer er herstel bare fouten optreden. U moet bijvoorbeeld testen of de toepassing overschakelt naar de secundaire modus en alleen-lezen als er een probleem wordt gedetecteerd en overschakelen wanneer de primaire regio weer beschikbaar is. Hiervoor moet u een manier hebben om herstel bare fouten te simuleren en te bepalen hoe vaak deze optreden.

U kunt [Fiddler](https://www.telerik.com/fiddler) gebruiken om HTTP-antwoorden in een script te onderscheppen en te wijzigen. Met dit script kunnen reacties worden geïdentificeerd die afkomstig zijn van uw primaire eind punt en de HTTP-status code wijzigen in een die door de Storage-client bibliotheek wordt herkend als een herstel bare fout. Dit code fragment toont een eenvoudig voor beeld van een Fiddler-script waarmee antwoorden op Lees aanvragen worden onderschept in de **employeedata** -tabel om een status van 502 te retour neren:


# <a name="java-v12"></a>[Java-V12](#tab/current)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="java-v11"></a>[Java-V11](#tab/legacy)

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

U kunt dit voor beeld uitbreiden om een breder aantal aanvragen te onderscheppen en alleen de **responseCode** van een aantal verzoeken te wijzigen om een echt praktijk scenario beter te simuleren. Zie [een aanvraag of antwoord](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) aanpassen in de Fiddler-documentatie voor meer informatie over het aanpassen van Fiddler-scripts.

Als u de drempel waarden hebt gemaakt voor het overschakelen van uw toepassing naar de alleen-lezen modus die kan worden geconfigureerd, is het eenvoudiger om het gedrag te testen met niet-productie transactie volumes.

## <a name="next-steps"></a>Volgende stappen

Voor een volledig voor beeld waarin wordt weer gegeven hoe u de switch tussen de primaire en secundaire eind punten kunt weer geven, raadpleegt u [Azure-voor beelden: met behulp van het circuit onderbreker-patroon met Ra-GRS-opslag](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
