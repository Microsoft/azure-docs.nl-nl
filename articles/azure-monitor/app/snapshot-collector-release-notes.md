---
title: Release opmerkingen voor het micro soft. ApplicationInsights. SnapshotCollector NuGet-pakket-Application Insights
description: Release opmerkingen voor het micro soft. ApplicationInsights. SnapshotCollector NuGet-pakket dat wordt gebruikt door de Application Insights Snapshot Debugger.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093257"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Opmerkingen bij de release voor micro soft. ApplicationInsights. SnapshotCollector

Dit artikel bevat de release opmerkingen voor het micro soft. ApplicationInsights. SnapshotCollector NuGet-pakket voor .NET-toepassingen, dat wordt gebruikt door de Application Insights-Snapshot Debugger.

Meer [informatie](./snapshot-debugger.md) over de Application Insights snapshot debugger voor .NET-toepassingen.

Voor fout rapporten en feedback opent u een probleem op GitHub op https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Opmerkingen bij de release

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Een release van een punt om een oplossing te backport van 1.4.0-pre.
### <a name="bug-fixes"></a>Opgeloste fouten
- Corrigeer [ObjectDisposedException bij het afsluiten](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Een punt versie om een probleem op te lossen dat is ontdekt bij het testen van het scenario voor de code in het test Azure App Service.
### <a name="changes"></a>Wijzigingen
- Het netcoreapp 3.0-doel is nu afhankelijk van micro soft. ApplicationInsights. AspNetCore >= 2.1.1 (voorheen >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Een punt versie voor het oplossen van een aantal problemen met hoge impact.
### <a name="bug-fixes"></a>Opgeloste fouten
- Vaste PDB-detectie in de map wwwroot/bin, die is verbroken toen we het symbool zoek algoritme in 1.3.6 wijzigen.
- Vaste ruis ExtractWasCalledMultipleTimesException in telemetrie.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Wijzigingen
- Het netcoreapp 2.0-doel van SnapshotCollector is afhankelijk van micro soft. ApplicationInsights. AspNetCore >= 2.1.1 (opnieuw). Hiermee wordt de werking van het gedrag voor 1.3.5 hersteld. Er is geprobeerd om de app bij te werken in 1.3.6, maar er zijn enkele Azure App Service-scenario's opgedeeld.
### <a name="new-features"></a>Nieuwe functies
- Snapshot Collector leest en parseert de Connections Tring van de APPLICATIONINSIGHTS_CONNECTION_STRING omgevings variabele of van de TelemetryConfiguration. Dit wordt voornamelijk gebruikt om het eind punt in te stellen om verbinding te maken met de service snap shot. Zie de [documentatie voor verbindings reeksen](./sdk-connection-string.md)voor meer informatie.
### <a name="bug-fixes"></a>Opgeloste fouten
- Is overgeschakeld naar het gebruik van httpclient maakt voor alle doelen behalve net45 omdat webaanvraag in sommige omgevingen is mislukt vanwege een incompatibel exemplaar (TLS 1,2 vereist).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Wijzigingen
- SnapshotCollector is nu afhankelijk van micro soft. ApplicationInsights >= 2.5.1 voor alle doel raamwerken. Dit kan een belang rijke wijziging zijn als uw toepassing afhankelijk is van een oudere versie van de micro soft. ApplicationInsights-SDK.
- De ondersteuning voor TLS 1,0 en 1,1 in snap shot Uploader verwijderen.
- Periode van PDB scans nu standaard 24 uur in plaats van 15 minuten. Kan worden geconfigureerd via PdbRescanInterval op SnapshotCollectorConfiguration.
- Met PDB scan wordt alleen op mappen op het hoogste niveau gezocht, in plaats van recursieve. Dit kan een belang rijke wijziging zijn als uw symbolen zich in submappen van de binaire map bevinden.
### <a name="new-features"></a>Nieuwe functies
- Log rotation in SnapshotUploader om te voor komen dat de map Logs wordt gevuld met oude bestanden.
- Ondersteuning voor deoptimalisatie (via ReJIT on attach) voor .NET Core 3,0-toepassingen.
- Symbolen toevoegen aan het NuGet-pakket.
- Aanvullende meta gegevens instellen bij het uploaden van minidumps.
- Er is een geïnitialiseerde eigenschap toegevoegd aan SnapshotCollectorTelemetryProcessor. Het is een CancellationToken die wordt geannuleerd wanneer de Snapshot Collector volledig is geïnitialiseerd en is verbonden met het service-eind punt.
- Moment opnamen kunnen nu worden vastgelegd voor uitzonde ringen in dynamisch gegenereerde methoden. Bijvoorbeeld de gecompileerde-expressie structuren die zijn gegenereerd door Entity Framework query's.
### <a name="bug-fixes"></a>Opgeloste fouten
- AmbiguousMatchException laden Snapshot Collector vanwege Status Monitor.
- Met de uitbreidings methode GetSnapshotCollector wordt nu gezocht in alle TelemetrySinks.
- Start de moment opname-uploader niet op platforms die niet worden ondersteund.
- InvalidOperationException afhandelen bij het deoptimalisatie van dynamische methoden (bijvoorbeeld Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Voeg ondersteuning toe voor soevereine Clouds (oudere versies werken niet in soevereine Clouds)
- Het toevoegen van een momentopname verzamelaar is eenvoudiger met behulp van AddSnapshotCollector (). Meer informatie vindt u [hier](./snapshot-debugger-appservice.md).
- Gebruik de FISMA MD5-instelling voor het controleren van BLOB-blokken. Dit voor komt het standaard algoritme voor .NET MD5 crypto grafie dat niet beschikbaar is wanneer het besturings systeem is ingesteld op de FIPS-compatibele modus.
- .NET Framework frames negeren bij het deoptimalisatieen van functie aanroepen. Dit gedrag kan worden bepaald door de configuratie-instelling DeoptimizeIgnoredModules.
- Voeg `DeoptimizeMethodCount` een configuratie-instelling toe waarmee deoptimalisatie van meer dan één functie aanroep is toegestaan. Meer informatie

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Gestructureerd instrumentatie sleutels toestaan.
- Verhoog de SnapshotUploader-robuustheid: door gaan met opstarten, zelfs als oude Uploader-logboeken niet kunnen worden verplaatst.
- Opnieuw inschakelen van rapportage aanvullende telemetrie wanneer SnapshotUploader.exe onmiddellijk afsluit (is uitgeschakeld in 1.3.3).
- Vereenvoudig interne telemetrie.
- _Experimentele functie_: Snappoint verzamelings plannen: Voeg ' SnapshotOnFirstOccurence ' toe. Meer informatie [vindt u hier](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Er is een probleem opgelost waardoor SnapshotUploader.exe niet meer reageert en geen moment opnamen voor .NET Core-Apps uploadt.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Experimentele functie_: Snappoint verzamelings plannen. Meer informatie [vindt u hier](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe wordt afgesloten wanneer de runtime het AppDomain verwijdert van waaruit SnapshotCollector wordt geladen, in plaats van te wachten tot het proces wordt afgesloten. Dit verbetert de betrouw baarheid van de Collector wanneer deze wordt gehost in IIS.
- Voeg configuratie toe om meerdere SnapshotCollector-instanties die dezelfde instrumentatie sleutel gebruiken, toe te staan om hetzelfde SnapshotUploader-proces te delen: ShareUploaderProcess (standaard ingesteld op `true` ).
- Meld extra telemetrie als SnapshotUploader.exe onmiddellijk afsluit.
- Het aantal ondersteunings bestanden dat SnapshotUploader.exe moet schrijven naar de schijf is gereduceerd.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Ondersteuning voor het verzamelen van moment opnamen met de RtlCloneUserProcess-API verwijderen en alleen PssCaptureSnapshots-API ondersteunen.
- Verhoog de standaard limiet voor het aantal opnamen dat kan worden vastgelegd in 10 minuten van 1 tot 3.
- Toestaan dat SnapshotUploader.exe onderhandelen over TLS 1,1 en 1,2
- Extra telemetrie rapporteren wanneer SnapshotUploader een waarschuwing of fout registreert
- Stoppen met het maken van moment opnamen wanneer de back-end-service rapporteert dat het dagelijkse quotum is bereikt (50 moment opnamen per dag)
- Voeg extra controle toe aan SnapshotUploader.exe om te voor komen dat twee exemplaren tegelijkertijd worden uitgevoerd.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Wijzigingen
- Voor toepassingen die zijn gericht op .NET Framework, is Snapshot Collector nu afhankelijk van micro soft. ApplicationInsights versie 2.3.0 of hoger.
U hebt deze 2.2.0 of hoger gebruikt.
We geloven dat dit geen probleem is voor de meeste toepassingen, maar laat het ons weten als deze wijziging verhindert dat u de nieuwste Snapshot Collector kunt gebruiken.
- Exponentieel uitstel vertraging gebruiken in de moment opname Uploader wanneer het opnieuw proberen van mislukte uploads is mislukt.
- Gebruik ServerTelemetryChannel (indien beschikbaar) voor meer betrouw bare rapportage van telemetrie.
- Gebruik ' SdkInternalOperationsMonitor ' voor de eerste verbinding met de Snapshot Debugger-service zodat deze wordt genegeerd door het bijhouden van afhankelijkheden.
- Verbeter de telemetrie rondom de eerste verbinding met de Snapshot Debugger service.
- Extra telemetrie rapporteren voor:
  - Azure App Service versie.
  - Azure Compute-exemplaren.
  - Containers.
  - Azure-functie-app.
### <a name="bug-fixes"></a>Opgeloste fouten
- Wanneer het interval voor het opnieuw instellen van het probleem teller is ingesteld op 24 dagen, moet u deze interpreteren als 24 uur.
- Er is een fout opgelost waarbij de moment opname van Uploader wordt gestopt met het verwerken van nieuwe moment opnamen als er een uitzonde ring is opgetreden tijdens de verwijdering van een moment opname.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Herstel sterke naam ondertekening met de binaire bestanden van de momentopname versie van Uploader.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Wijzigingen
- De bestanden die nodig zijn voor SnapshotUploader (64). exe zijn nu Inge sloten als resources in het hoofd-DLL-bestand. Dit betekent dat de SnapshotCollectorFiles-map niet meer wordt gemaakt, het bouwen en implementeren wordt vereenvoudigd en de inperking van Solution Explorer kan worden verkleind. Wees voorzichtig wanneer u een upgrade uitvoert om de wijzigingen in uw bestand te controleren `.csproj` . Het `Microsoft.ApplicationInsights.SnapshotCollector.targets` bestand is niet meer nodig.
- Telemetrie wordt geregistreerd bij uw Application Insights-resource, zelfs als ProvideAnonymousTelemetry is ingesteld op false. Zo kunnen we een status controle functie in de Azure Portal implementeren. ProvideAnonymousTelemetry is alleen van invloed op de telemetrie die wordt verzonden naar micro soft voor product ondersteuning en-verbetering.
- Wanneer de TempFolder of ShadowCopyFolder worden omgeleid naar omgevings variabelen, blijft de Collector inactief totdat deze omgevings variabelen zijn ingesteld.
- Voor toepassingen die via een proxy server verbinding maken met het Internet, detecteert Snapshot Collector nu automatisch alle proxy-instellingen en geven ze door aan SnapshotUploader.exe.
- Verlaag de prioriteit van het SnapshotUplaoder-proces (indien mogelijk). Deze prioriteit kan worden overschreven via de optie IsLowPrioirtySnapshotUploader.
- Er is een GetSnapshotCollector-extensie methode toegevoegd voor TelemetryConfiguration voor scenario's waarin u de Snapshot Collector via een programma wilt configureren.
- Stel de Application Insights SDK-versie (in plaats van de versie van de toepassing) in op klant gerichte telemetrie.
- Verzend de eerste heartbeat-gebeurtenis na twee minuten.
### <a name="bug-fixes"></a>Opgeloste fouten
- NullReferenceException corrigeren wanneer uitzonde ringen null of onveranderlijke gegevens woordenlijsten hebben.
- In de Uploader wordt een paar keer opnieuw geprobeerd om een fout bij het delen op te halen.
- Herstel dubbele telemetrie wanneer meer dan één thread aanroept in de telemetrie-pijp lijn bij het opstarten.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Wijzigingen
- Opmerkingen over XML-doc-documenten zijn nu opgenomen in het NuGet-pakket.
- Er is een ExcludeFromSnapshotting-extensie methode toegevoegd `System.Exception` voor scenario's waarin u weet dat u een slechte uitzonde ring hebt en u wilt voor komen dat er moment opnamen worden gemaakt.
- Een IsEnabledWhenProfiling-configuratie-eigenschap toegevoegd, wordt standaard ingesteld op True. Dit is een wijziging ten opzichte van vorige versies waarbij het maken van moment opnamen tijdelijk is uitgeschakeld als de Application Insights Profiler een gedetailleerde verzameling heeft uitgevoerd. Het oude gedrag kan worden hersteld door deze eigenschap in te stellen op ONWAAR.
### <a name="bug-fixes"></a>Opgeloste fouten
- SnapshotUploader64.exe correct aanmelden.
- Beveilig tegen dubbel initialisatie van de telemetrie-processor.
- Vermijd dubbele logboek registratie van telemetriegegevens in apps met meerdere pijp lijnen.
- Een bug oplossen met de verloop tijd van een verzamelings plan, waardoor moment opnamen na 24 uur kunnen worden voor komen.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
De grootste wijziging in deze versie (dus de overstap naar een nieuw secundair versie nummer) is het herschrijven van de pijp lijn voor het maken en verwerken van moment opnamen. In vorige versies werd deze functionaliteit geïmplementeerd in systeem eigen code (ProductionBreakpoints *. dll en SnapshotHolder*. exe). De nieuwe implementatie is alle beheerde code met P/Invoke. Voor deze eerste versie met behulp van de nieuwe pijp lijn zijn we niet ver van het oorspronkelijke gedrag afgebroken. De nieuwe implementatie biedt betere fout rapportage en stelt ons in voor toekomstige verbeteringen.

### <a name="other-changes-in-this-version"></a>Andere wijzigingen in deze versie
- De naam van MinidumpUploader.exe is gewijzigd in SnapshotUploader.exe (of SnapshotUploader64.exe).
- Er is een timing-telemetrie toegevoegd voor het deoptimaliseeren/opnieuw optimaliseren van aanvragen.
- Gzip-compressie toegevoegd voor uploads met mini maal.
- Er is een probleem opgelost waarbij PDBs is vergrendeld voor komen dat de site is bijgewerkt.
- De naam van de oorspronkelijke map (SnapshotCollectorFiles) registreren bij het kopiëren van schaduw kopieën.
- Pas de geheugen limieten voor 64-bits processen aan om te voor komen dat de site opnieuw wordt opgestart vanwege OOM.
- Corrigeer een probleem waarbij moment opnamen nog steeds zijn verzameld, zelfs na het uitschakelen.
- Heartbeat-gebeurtenissen vastleggen in de AI-resource van de klant.
- Verbeter de snelheid van de moment opname door source te verwijderen uit de probleem-ID.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Wijzigingen
Uitgebreid gebruik van telemetrie
- .NET-versie en-besturings systeem detecteren en rapporteren
- Extra Azure-omgevingen detecteren en rapporteren (Cloud service, Service Fabric)
- Registreer en meld uitzonderings metrieken (aantal 1e kans-uitzonde ringen en aantal TrackException-aanroepen) in heartbeat-telemetrie.
### <a name="bug-fixes"></a>Opgeloste fouten
- De verwerking van SqlException waarbij de interne uitzonde ring (Win32Exception) niet wordt gegenereerd, wordt gecorrigeerd.
- Volg spaties op symbool mappen bijsnijden, waardoor een onjuiste parse van opdracht regel argumenten voor de MinidumpUploader is veroorzaakt.
- Voorkom oneindige pogingen van mislukte verbindingen met het eind punt van de Snapshot Debugger agent.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Wijzigingen
- De geheugen beveiliging voor de host is toegevoegd. Deze functie vermindert de invloed op het geheugen van de host-computer.
- Verbeter de weergave ervaring van de Azure Portal-moment opname.