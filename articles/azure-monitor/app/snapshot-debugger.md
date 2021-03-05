---
title: Azure-toepassing Insights-Snapshot Debugger voor .NET-Apps
description: Moment opnamen van fout opsporing worden automatisch verzameld wanneer uitzonde ringen worden gegenereerd in productie-.NET-Apps
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
author: cweining
ms.author: cweining
ms.reviewer: cweining
ms.openlocfilehash: bd196e60fed8a18064bfa59e343e51a878a11237
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217394"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Momentopnamen voor foutopsporing over uitzonderingen in .NET-apps
Wanneer er een uitzonde ring optreedt, kunt u automatisch een moment opname van de fout opsporing verzamelen vanuit uw Live Web-app. De moment opname toont de status van de bron code en variabelen op het moment dat de uitzonde ring werd gegenereerd. Met de Snapshot Debugger in [Azure-toepassing Insights](./app-insights-overview.md) wordt de telemetrie van de uitzonde ring van uw web-app gecontroleerd. Er worden moment opnamen van uw belangrijkste uitzonde ringen verzameld, zodat u over de benodigde informatie beschikt voor het vaststellen van problemen in de productie. Neem het [snap shot Collector NuGet-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in uw toepassing op en Configureer eventueel verzamelings parameters in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Moment opnamen worden weer gegeven op [uitzonde ringen](./asp-net-exceptions.md) in de Application Insights Portal.

U kunt foutopsporingsmomentopnamen in het portaal bekijken om de aanroepstack te zien en variabelen inspecteren bij elk aanroepstackframe. Open moment opnamen met Visual Studio 2019 Enter prise voor een krachtigere fout opsporing met de bron code. In Visual Studio kunt u Snappoints zo [instellen dat er interactief moment opnamen worden](/visualstudio/debugger/debug-live-azure-applications) gemaakt zonder dat er wordt gewacht op een uitzonde ring.

Debug-moment opnamen worden 15 dagen bewaard. Dit Bewaar beleid is ingesteld op basis van elke toepassing. Als u deze waarde wilt verhogen, kunt u een verhoging aanvragen door een ondersteunings aanvraag te openen in de Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Application Insights Snapshot Debugger inschakelen voor uw toepassing
Momentopname verzameling is beschikbaar voor:
* .NET Framework-en ASP.NET-toepassingen met .NET Framework 4,5 of hoger.
* .NET Core 2,0 en ASP.NET Core 2,0-toepassingen die worden uitgevoerd op Windows.

De volgende omgevingen worden ondersteund:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-functie](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met OS Family 4 of hoger
* [Azure service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) die worden uitgevoerd op Windows Server 2012 R2 of hoger
* [Azure virtual machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met Windows Server 2012 R2 of hoger
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met windows server 2012 R2 of hoger of Windows 8,1 of hoger

> [!NOTE]
> Client toepassingen (bijvoorbeeld WPF, Windows Forms of UWP) worden niet ondersteund.

Raadpleeg de gids voor het [oplossen van problemen](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)als u snapshot debugger hebt ingeschakeld, maar geen moment opnamen ziet.

## <a name="grant-permissions"></a>Machtigingen verlenen

Toegang tot moment opnamen wordt beschermd door Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Als u een momentopname wilt inspecteren, moet de eigenaar van het abonnement u eerst toevoegen aan de benodigde rol.

> [!NOTE]
> Eigen aren en mede werkers hebben deze rol niet automatisch. Als ze moment opnamen willen bekijken, moeten ze zichzelf aan de rol toevoegen.

Abonnements eigenaren moeten de `Application Insights Snapshot Debugger` rol toewijzen aan gebruikers die moment opnamen zullen inspecteren. Deze rol kan worden toegewezen aan afzonderlijke gebruikers of groepen op abonnements eigenaren voor het doel Application Insights bron of de resource groep of het abonnement.

1. Navigeer naar de Application Insights-resource in de Azure Portal.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op de knop **+ roltoewijzing toevoegen** .
1. Selecteer **Application Insights snapshot debugger** in de vervolg keuzelijst **rollen** .
1. Zoek en voer een naam in voor de gebruiker die u wilt toevoegen.
1. Klik op de knop **Opslaan** om de gebruiker toe te voegen aan de rol.


> [!IMPORTANT]
> Moment opnamen kunnen mogelijk persoonlijke en andere gevoelige informatie bevatten in variabele en parameter waarden.

## <a name="view-snapshots-in-the-portal"></a>Moment opnamen weer geven in de portal

Nadat er een uitzonde ring is opgetreden in uw toepassing en er een moment opname is gemaakt, moet u moment opnamen hebben om weer te geven. Het kan 5 tot 10 minuten duren als er een uitzonde ring optreedt in een moment opname die in de portal kan worden weer gegeven. Als u moment opnamen wilt weer geven, selecteert u in het deel venster **fout** de knop **bewerkingen** bij het weer geven van het tabblad **bewerkingen** of selecteert u de knop **uitzonde ringen** bij het weer geven van het tabblad **uitzonde** ringen:

![Pagina fouten](./media/snapshot-debugger/failures-page.png)

Selecteer een bewerking of uitzonde ring in het rechterdeel venster om het deel venster **end-to-end trans actie Details** te openen en selecteer vervolgens de uitzonderings gebeurtenis. Als er een moment opname beschikbaar is voor de gegeven uitzonde ring, wordt in het rechterdeel venster een knop voor het maken van een **moment opname** met details weer gegeven met informatie over de [uitzonde ring](./asp-net-exceptions.md).

![Knop voor moment opname van fout opsporing openen in uitzonde ring](./media/snapshot-debugger/e2e-transaction-page.png)

In de momentopname weergave voor fout opsporing ziet u een deel venster aanroep stack en een variabelen. Wanneer u frames van de aanroep stack selecteert in het deel venster aanroep stack, kunt u lokale variabelen en para meters voor die functie aanroep in het deel venster variabelen weer geven.

![Moment opname van fout opsporing weer geven in de portal](./media/snapshot-debugger/open-snapshot-portal.png)

Moment opnamen kunnen gevoelige informatie bevatten en zijn standaard niet zichtbaar. Als u moment opnamen wilt weer geven, moet u de `Application Insights Snapshot Debugger` rol hebben die aan u is toegewezen.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Moment opnamen weer geven in Visual Studio 2017 Enter prise of hoger
1. Klik op de knop **moment opname downloaden** om een bestand te downloaden `.diagsession` dat door Visual Studio Enter prise kan worden geopend.

2. Als u het `.diagsession` bestand wilt openen, moet u het onderdeel snapshot debugger Visual Studio geïnstalleerd hebben. Het Snapshot Debugger onderdeel is een vereist onderdeel van de ASP.net-werk belasting in Visual Studio en kan worden geselecteerd in de lijst met afzonderlijke onderdelen in het Visual Studio-installatie programma. Als u een versie van Visual Studio gebruikt die ouder is dan Visual Studio 2017 versie 15,5, moet u de uitbrei ding installeren vanuit de [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Nadat u het momentopname bestand hebt geopend, wordt de pagina mini maal fout opsporing in Visual Studio weer gegeven. Klik op **fout opsporing beheerde code** om de fout opsporing van de moment opname te starten. De moment opname wordt geopend met de regel code waar de uitzonde ring is opgetreden, zodat u de huidige status van het proces kunt opsporen.

    ![Moment opname van fout opsporing in Visual Studio weer geven](./media/snapshot-debugger/open-snapshot-visualstudio.png)

De gedownloade moment opname bevat alle symbool bestanden die op de webtoepassingsserver zijn gevonden. Deze symbool bestanden zijn vereist om momentopname gegevens te koppelen aan de bron code. Voor App Service-apps moet u de optie voor het implementeren van symbolen inschakelen wanneer u uw web-apps publiceert.

## <a name="how-snapshots-work"></a>Hoe moment opnamen werken

De Snapshot Collector wordt geïmplementeerd als een [Application Insights Telemetry-processor](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Wanneer uw toepassing wordt uitgevoerd, wordt de Snapshot Collector telemetrie-processor toegevoegd aan de telemetrie-pijp lijn van uw toepassing.
Telkens wanneer uw toepassing [TrackException](./asp-net-exceptions.md#exceptions)aanroept, wordt door de snapshot Collector een probleem-id berekend op basis van het type uitzonde ring dat wordt gegenereerd en de methode voor het activeren.
Telkens wanneer uw toepassing TrackException aanroept, wordt een teller verhoogd voor de juiste probleem-ID. Wanneer het item de `ThresholdForSnapshotting` waarde bereikt, wordt de probleem-id toegevoegd aan een verzamelings plan.

De Snapshot Collector bewaakt ook uitzonde ringen als deze worden gegenereerd door u te abonneren op de gebeurtenis [AppDomain. currentDomain. FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) . Wanneer deze gebeurtenis wordt geactiveerd, wordt de probleem-ID van de uitzonde ring berekend en vergeleken met de probleem-Id's in het verzamelings plan.
Als er een overeenkomst wordt gevonden, wordt een moment opname van het actieve proces gemaakt. Aan de moment opname is een unieke id toegewezen en er wordt een uitzonde ring met die id stempel gemaakt. Nadat de FirstChanceException-handler retourneert, wordt de gegenereerde uitzonde ring als normaal verwerkt. Uiteindelijk bereikt de uitzonde ring de TrackException-methode opnieuw, waar deze samen met de moment opname-id wordt gerapporteerd aan Application Insights.

Het belangrijkste proces blijft het uitvoeren en verwerken van verkeer naar gebruikers met een beetje onderbreking. Ondertussen wordt de moment opname doorgestuurd naar het Uploader-proces van de moment opname. De moment opname van Uploader maakt een mini maal en uploadt deze naar Application Insights samen met alle relevante Symbol-bestanden (. pdb).

> [!TIP]
> - Een proces momentopname is een gesuspendeerde kloon van het proces dat wordt uitgevoerd.
> - Het maken van de moment opname neemt ongeveer 10 tot 20 milliseconden in beslag.
> - De standaard waarde voor `ThresholdForSnapshotting` is 1. Dit is ook de minimum waarde. Uw app moet daarom dezelfde uitzonde ring **twee keer** activeren voordat een moment opname wordt gemaakt.
> - Stel deze `IsEnabledInDeveloperMode` waarde in op True als u moment opnamen wilt genereren tijdens fout opsporing in Visual Studio.
> - Het aanmaak tempo van de moment opname wordt beperkt door de `SnapshotsPerTenMinutesLimit` instelling. Standaard is de limiet één moment opname elke tien minuten.
> - Er kunnen niet meer dan 50 moment opnamen per dag worden geüpload.

## <a name="limitations"></a>Beperkingen

De standaard Bewaar periode voor gegevens is 15 dagen. Voor elke Application Insights-instantie is een maximum aantal moment opnamen van 50 per dag toegestaan.

### <a name="publish-symbols"></a>Symbolen publiceren
De Snapshot Debugger vereist symbool bestanden op de productie server om variabelen te decoderen en om een fout opsporing in Visual Studio te bieden.
Met versie 15,2 (of hoger) van Visual Studio 2017 worden symbolen voor release builds standaard gepubliceerd wanneer deze naar App Service worden gepubliceerd. In eerdere versies moet u de volgende regel toevoegen aan het bestand met het publicatie profiel `.pubxml` , zodat de symbolen in de release modus worden gepubliceerd:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Voor Azure Compute en andere typen zorgt u ervoor dat de symbool bestanden zich in dezelfde map bevinden als de hoofd toepassing. dll (normaal `wwwroot/bin` ) of beschikbaar zijn in het huidige pad.

> [!NOTE]
> Raadpleeg de [documentatie van Visual Studio](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)voor meer informatie over de verschillende beschik bare symbool opties. Voor de beste resultaten raden wij u aan ' Full ', ' Portable ' of ' embedded ' te gebruiken.

### <a name="optimized-builds"></a>Geoptimaliseerde builds
In sommige gevallen kunnen lokale variabelen niet worden weer gegeven in release builds vanwege optimalisaties die worden toegepast door de JIT-compiler.
In Azure-app Services kan de Snapshot Collector echter de methoden die deel uitmaken van het verzamelings plan, deoptimaliseeren.

> [!TIP]
> Installeer de Application Insights site-uitbrei ding in uw App Service om ondersteuning voor deoptima Lise ring te verkrijgen.

## <a name="next-steps"></a>Volgende stappen
Application Insights Snapshot Debugger voor uw toepassing inschakelen:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-functie](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Meer dan Application Insights Snapshot Debugger:
 
* [Stel snappoints in uw code in](/visualstudio/debugger/debug-live-azure-applications) om moment opnamen op te halen zonder dat er wordt gewacht op een uitzonde ring.
* [Diagnose uitzonde ringen in uw web-apps](./asp-net-exceptions.md) wordt uitgelegd hoe u meer uitzonde ringen zichtbaar maakt voor Application Insights.
* [Slimme detectie](./proactive-diagnostics.md) detecteert automatisch prestatie afwijkingen.