---
title: Veelgestelde vragen over toepassings prestaties
description: Krijg antwoorden op veelgestelde vragen over de beschik baarheid, prestaties en toepassings problemen in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88958994"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over toepassings prestaties voor Web Apps in azure

> [!NOTE]
> Enkele van de onderstaande richt lijnen werken mogelijk alleen op Windows-of Linux-App Services. Linux App Services bijvoorbeeld standaard in 64-bits modus worden uitgevoerd.
>

In dit artikel vindt u antwoorden op veelgestelde vragen over problemen met de prestaties van toepassingen voor de [Web apps functie van Azure app service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Waarom is mijn app traag?

Meerdere factoren kunnen bijdragen aan het vertragen van de app-prestaties. Zie [problemen met trage prestaties van web-apps oplossen](troubleshoot-performance-degradation.md)voor gedetailleerde stappen voor probleem oplossing.

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hoe kan ik problemen met een hoog CPU-verbruiks scenario oplossen?

In sommige scenario's met een hoge CPU-verbruik is het mogelijk dat uw app echt meer computer bronnen nodig heeft. In dat geval kunt u het beste schalen naar een hogere servicelaag, zodat de toepassing alle benodigde resources krijgt. Andere tijden, een hoog CPU-verbruik wordt mogelijk veroorzaakt door een ongeldige lus of door een coderings praktijk. Het verkrijgen van inzicht in wat er wordt geactiveerd, is een proces dat uit twee delen bestaat. Maak eerst een proces dump en analyseer vervolgens de proces dump. Zie [een dump bestand vastleggen en analyseren voor een hoog CPU-verbruik voor web apps](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app)voor meer informatie.

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hoe kan ik een scenario met hoog geheugen gebruik oplossen?

In sommige scenario's met een hoog geheugen gebruik is het mogelijk dat uw app echt meer computer bronnen nodig heeft. In dat geval kunt u het beste schalen naar een hogere servicelaag, zodat de toepassing alle benodigde resources krijgt. Andere keren kan een fout in de code leiden tot een geheugenlek. Een coderings praktijk kan ook het geheugen gebruik verg Roten. Het verkrijgen van inzicht in wat is het activeren van hoog geheugen, is een proces dat uit twee delen bestaat. Maak eerst een proces dump en analyseer vervolgens de proces dump. Crash diagnose van de Azure site extension Gallery kan deze stappen efficiënt uitvoeren. Zie voor meer informatie [een dump bestand vastleggen en analyseren voor onregelmatig hoge geheugen voor web apps](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hoe kan ik App Service web-apps automatisch met behulp van Power shell?

U kunt Power shell-cmdlets gebruiken om App Service web-apps te beheren en te onderhouden. In het blog bericht [automatiseert u web-apps die worden gehost in azure app service met behulp van Power shell](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way). hier wordt beschreven hoe u op Azure Resource Manager gebaseerde Power shell-cmdlets gebruikt om algemene taken te automatiseren. Het blog bericht bevat ook voorbeeld code voor de verschillende beheer taken voor web-apps. Zie [AZ. websites](/powershell/module/az.websites)(Engelstalig) voor beschrijvingen en syntaxis voor alle app service Web apps-cmdlets.

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hoe kan ik de gebeurtenis logboeken van mijn web-app weer geven?

De gebeurtenis logboeken van uw web-app weer geven:

1. Meld u aan bij uw **kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selecteer in het menu **fout opsporing console**-  >  **cmd**.
3. Selecteer de map **logfiles** .
4. Als u gebeurtenis logboeken wilt weer geven, selecteert u het potlood pictogram naast **eventlog.xml**.
5. Voer de Power shell-cmdlet uit om de logboeken te downloaden `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hoe kan ik een gebruikers modus geheugen dump van mijn web-app vastleggen?

Een gebruikers modus geheugen dump van uw web-app vastleggen:

1. Meld u aan bij uw **kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selecteer het menu van het **proces Verkenner** .
3. Klik met de rechter muisknop op het **w3wp.exe** proces of uw webprocesproces.
4. Selecteer   >  **volledige dump** voor het downloaden van geheugen.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hoe kan ik gegevens op proces niveau voor mijn web-app weer geven?

U hebt twee opties voor het weer geven van informatie op proces niveau voor uw web-app:

*   In Azure Portal:
    1. Open de **proces Verkenner** voor de web-app.
    2. Als u de details wilt weer geven, selecteert u het **w3wp.exe** proces.
*   In de kudu-console:
    1. Meld u aan bij uw **kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
    2. Selecteer het menu van het **proces Verkenner** .
    3. Voor het **w3wp.exe** proces selecteert u **Eigenschappen**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Als ik naar mijn app Blader, zie ik ' fout 403-deze web-app is gestopt '. Hoe los ik dit op?

Drie voor waarden kunnen deze fout veroorzaken:

* De web-app heeft een facturerings limiet bereikt en uw site is uitgeschakeld.
* De web-app is gestopt in de portal.
* De web-app heeft een limiet voor resource quota bereikt die mogelijk van toepassing is op een gratis of gedeeld service plan voor schalen.

Volg de stappen in [Web apps: ' fout 403: deze web-app is gestopt '](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped)om te zien wat de oorzaak van de fout is en om het probleem op te lossen.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Waar kan ik meer informatie vinden over quota en limieten voor verschillende App Service plannen?

Zie [app service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor meer informatie over quota's en limieten. 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hoe kan ik de reactie tijd voor de eerste aanvraag na een niet-actieve periode verminderen?

Web-apps worden standaard uit het geheugen verwijderd als ze gedurende een bepaalde periode niet actief zijn. Op deze manier kan het systeem bronnen besparen. Het nadeel is dat de reactie op de eerste aanvraag na het verwijderen van de web-app langer is, zodat de web-app kan worden geladen en aan de slag kan met het leveren van reacties. In Basic-en Standard-service plannen kunt u de instelling **altijd op** inschakelen om de app altijd te laden. Dit elimineert langere laad tijden nadat de app niet actief is. De instelling **altijd aan** wijzigen:

1. Ga in het Azure Portal naar uw web-app.
2. **Configuratie** selecteren
3. Selecteer **algemene instellingen**.
4. Selecteer **aan** voor **Always on**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hoe kan ik tracering van mislukte aanvragen inschakelen?

Tracering van mislukte aanvragen inschakelen:

1. Ga in het Azure Portal naar uw web-app.
3. Selecteer **alle instellingen**  >  **Diagnostische logboeken**.
4. Selecteer **aan** voor **tracering van mislukte aanvragen**.
5. Selecteer **Opslaan**.
6. Selecteer op de Blade Web-App de optie **extra**.
7. Selecteer **Visual Studio online**.
8. Als de instelling niet is **ingeschakeld**, selecteert u **aan**.
9. Selecteer **Go**.
10. Selecteer **Web.config**.
11. Voeg in System. webserver deze configuratie toe (voor het vastleggen van een specifieke URL):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Als u problemen met trage prestaties wilt oplossen, voegt u deze configuratie toe (als de aanvraag voor vastleggen meer dan 30 seconden duurt):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Als u de traceringen van mislukte aanvragen wilt downloaden, gaat u in de [Portal](https://portal.azure.com)naar uw website.
15. Selecteer **extra**  >  **kudu**  >  **Go**.
18. Selecteer in het menu **fout opsporing console**-  >  **cmd**.
19. Selecteer de map **logfiles** en selecteer vervolgens de map met een naam die begint met **W3SVC**.
20. Als u het XML-bestand wilt weer geven, selecteert u het potlood pictogram.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Ik zie het bericht ' werk proces heeft een recycle bewerking aangevraagd als gevolg van de limiet van ' percentage geheugen '. Hoe kan ik dit probleem op te lossen?

De Maxi maal beschik bare hoeveelheid geheugen voor een 32-bits proces (zelfs op een 64-bits besturings systeem) is 2 GB. Het werk proces is standaard ingesteld op 32-bits in App Service (voor compatibiliteit met oudere webtoepassingen).

Overweeg om te scha kelen naar 64-bits processen, zodat u kunt profiteren van het extra geheugen dat beschikbaar is in uw rol op het web. Hiermee wordt het opnieuw starten van een web-app geactiveerd, dus schema dienovereenkomstig.

Houd er ook rekening mee dat een 64-bits omgeving een Basic-of Standard-Service plan vereist. Gratis en gedeelde abonnementen worden altijd uitgevoerd in een 32-bits omgeving.

Zie [Configure Web apps in app service](configure-common.md)voor meer informatie.

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Waarom treedt er een time-out op voor mijn aanvraag na 230 seconden?

Azure Load Balancer heeft een standaard instelling voor een time-out voor inactiviteit van vier minuten. Dit is over het algemeen een redelijke reactie tijd voor een webaanvraag. Als uw web-app achtergrond verwerking vereist, kunt u het beste Azure WebJobs gebruiken. De Azure-web-app kan webjobs aanroepen en worden gewaarschuwd wanneer de achtergrond verwerking is voltooid. U kunt kiezen uit meerdere methoden voor het gebruik van webjobs, waaronder wacht rijen en triggers.

Webjobs is ontworpen voor achtergrond verwerking. U kunt zoveel achtergrond verwerking uitvoeren als u wilt in een Webtaak. Zie [achtergrond taken uitvoeren met Webjobs](webjobs-create.md)voor meer informatie over webjobs.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core toepassingen die worden gehost in App Service soms niet meer reageren. Dit probleem Hoe kan ik oplossen?

Een bekend probleem met een eerdere [versie van Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan ertoe leiden dat een ASP.net Core 1,0-app die in app service wordt gehost, niet meer reageert. Dit bericht kan ook worden weer gegeven: ' er is een fout opgetreden in de opgegeven CGI-toepassing en het proces is beëindigd door de server. '

Dit probleem is opgelost in Kestrel-versie 1.0.2. Deze versie is opgenomen in de ASP.NET Core 1.0.3-update. Om dit probleem op te lossen, moet u de app-afhankelijkheden bijwerken om Kestrel 1.0.2 te gebruiken. U kunt ook een van de twee tijdelijke oplossingen gebruiken die worden beschreven in het blog bericht [ASP.NET Core 1,0 trage prestatie problemen in app service Web-apps](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Ik kan mijn logboek bestanden niet vinden in de bestands structuur van mijn web-app. Hoe kan ik deze vinden?

Als u de functie lokale cache van App Service gebruikt, heeft dit gevolgen voor de mapstructuur van de logboek bestanden en gegevens mappen voor uw App Service exemplaar. Wanneer lokale cache wordt gebruikt, worden submappen gemaakt in de opslag logboeken en gegevens mappen. De submappen gebruiken het naam patroon ' unieke id ' + tijds tempel. Elke submap komt overeen met een VM-exemplaar waarin de web-app wordt uitgevoerd of is uitgevoerd.

Controleer het tabblad App Service **Toepassings instellingen** om te bepalen of u lokale cache gebruikt. Als de lokale cache wordt gebruikt, is de app-instelling `WEBSITE_LOCAL_CACHE_OPTION` ingesteld op `Always` .

Als u geen lokale cache gebruikt en dit probleem ondervindt, dient u een ondersteunings aanvraag in.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Ik zie het bericht ' er is een poging gedaan om toegang te krijgen tot een socket op een manier die is verboden door de toegangs machtigingen. ' Hoe los ik dit op?

Deze fout treedt doorgaans op als de uitgaande TCP-verbindingen op het VM-exemplaar zijn uitgeput. In App Service worden limieten afgedwongen voor het maximum aantal uitgaande verbindingen dat kan worden gemaakt voor elk VM-exemplaar. Zie [numerieke limieten voor meerdere vm's](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)voor meer informatie.

Deze fout kan ook optreden als u probeert toegang te krijgen tot een lokaal adres vanuit uw toepassing. Zie [lokale adres aanvragen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)voor meer informatie.

Zie het blog bericht over [uitgaande verbindingen met Azure websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)voor meer informatie over uitgaande verbindingen in uw web-app.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hoe kan ik Visual Studio gebruiken voor het op afstand opsporen van mijn App Service web-app?

Zie voor een gedetailleerde beschrijving van het opsporen van fouten in uw web-app met behulp van Visual Studio [externe fout opsporing in uw app service Web-app](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).