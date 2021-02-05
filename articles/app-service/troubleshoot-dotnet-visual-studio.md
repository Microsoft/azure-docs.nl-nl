---
title: Problemen met Visual Studio oplossen
description: Meer informatie over het oplossen van problemen met een App Service-app met externe fout opsporing, tracering en logboek registratie van hulpprogram ma's die zijn ingebouwd in Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: a177b22f0f91d82013956bff36eaa57a084c27d1
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576579"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Problemen met een app in Azure App Service oplossen met Visual Studio
## <a name="overview"></a>Overzicht
Deze zelf studie laat zien hoe u met behulp van Visual Studio-hulpprogram ma's fouten opspoort in [app service](./overview.md), door [op afstand de foutopsporingsmodus uit](/visualstudio/debugger/) te voeren of door toepassings logboeken en webserver logboeken te bekijken.

U leert het volgende:

* Welke app-beheer functies zijn beschikbaar in Visual Studio.
* Hoe u de externe weer gave van Visual Studio kunt gebruiken om snel wijzigingen in een externe app aan te brengen.
* De foutopsporingsmodus op afstand uitvoeren terwijl een project wordt uitgevoerd in azure, zowel voor een app als voor een Webtaak.
* Het maken van Logboeken voor toepassings tracering en het weer geven ervan terwijl de toepassing deze maakt.
* Webserver logboeken weer geven, inclusief gedetailleerde fout berichten en tracering van mislukte aanvragen.
* Diagnostische logboeken verzenden naar een Azure Storage-account en hier weer geven.

Als u Visual Studio Ultimate hebt, kunt u ook [IntelliTrace](/visualstudio/debugger/intellitrace) gebruiken voor fout opsporing. IntelliTrace wordt niet behandeld in deze zelf studie.

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten
Deze zelf studie werkt met de ontwikkel omgeving, webproject en App Service-app die u hebt ingesteld in [een ASP.net-app maken in azure app service](quickstart-dotnet-framework.md). Voor de secties webjobs hebt u de toepassing nodig die u in aan [de slag met de Azure WEBJOBS SDK][GetStartedWJ]hebt gemaakt.

De code voorbeelden die in deze zelf studie worden weer gegeven, zijn voor een C# MVC-webtoepassing, maar de procedures voor het oplossen van problemen zijn hetzelfde voor Visual Basic en Web Forms-toepassingen.

In deze zelf studie wordt ervan uitgegaan dat u Visual Studio 2019 gebruikt. 

De functie voor het streamen van Logboeken werkt alleen voor toepassingen die op .NET Framework 4 of hoger zijn gericht.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>App-configuratie en-beheer
Visual Studio biedt toegang tot een subset van de app-beheer functies en configuratie-instellingen die beschikbaar zijn in de [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715). In deze sectie ziet u wat er beschikbaar is via **Server Explorer**. Als u de nieuwste functies voor Azure-integratie wilt zien, kunt u ook de **Cloud Explorer** uitproberen. U kunt beide vensters openen vanuit het menu **weer gave** .

1. Als u nog niet bent aangemeld bij Azure in Visual Studio, klikt u met de rechter muisknop op **Azure** en selecteert u verbinding maken met **Microsoft Azure abonnement** in **Server Explorer**.

    U kunt ook een beheer certificaat installeren waarmee toegang tot uw account mogelijk wordt. Als u ervoor kiest om een certificaat te installeren, klikt u met de rechter muisknop op het **Azure** -knoop punt in **Server Explorer** en selecteert u vervolgens **Abonnementen beheren en filteren** in het context menu. Klik in het dialoog venster **Microsoft Azure abonnementen beheren** op het tabblad **certificaten** en klik vervolgens op **importeren**. Volg de instructies voor het downloaden en importeren van een abonnements bestand (ook wel een *. publishsettings* -bestand genoemd) voor uw Azure-account.

   > [!NOTE]
   > Als u een abonnements bestand downloadt, slaat u het op in een map buiten uw bron code mappen (bijvoorbeeld in de map down Loads) en verwijdert u deze nadat het importeren is voltooid. Een kwaadwillende gebruiker die toegang tot het abonnements bestand krijgt, kan uw Azure-Services bewerken, maken en verwijderen.
   >
   >

    Zie [accounts, abonnementen en beheerders rollen beheren](../role-based-access-control/role-assignments-portal.md)voor meer informatie over het maken van verbinding met Azure-resources vanuit Visual Studio.
2. In **Server Explorer** vouwt u **Azure** uit en vouwt u **app service** uit.
3. Vouw de resource groep uit met de app die u hebt gemaakt in [een ASP.net-app maken in azure app service](quickstart-dotnet-framework.md), klik met de rechter muisknop op het app-knoop punt en klik op **instellingen weer geven**.

    ![Instellingen in Server Explorer weer geven](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Het tabblad van de **Azure-web-app** wordt weer gegeven en u kunt de app-beheer-en configuratie taken zien die beschikbaar zijn in Visual Studio.

    ![Venster van de Azure-web-app](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    In deze zelf studie gebruikt u de vervolg keuzelijst logboek registratie en tracering. U gebruikt ook externe fout opsporing, maar u gebruikt een andere methode om deze functie in te scha kelen.

    Zie [Azure app service: hoe toepassings reeksen en verbindings reeksen werken](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)voor meer informatie over de vakken app-instellingen en verbindings reeksen in dit venster.

    Als u een app-beheer taak wilt uitvoeren die niet in dit venster kan worden uitgevoerd, klikt u op **openen in Beheerportal** om een browser venster te openen voor de Azure Portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Toegang tot app-bestanden in Server Explorer
U implementeert doorgaans een webproject met de `customErrors` markering in het Web.config-bestand ingesteld op `On` of `RemoteOnly` , wat betekent dat er geen handig fout bericht wordt weer gegeven wanneer iets verkeerd gaat. Voor veel fouten krijgt u alleen een pagina als een van de volgende:

**Server fout in/-toepassing:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Scherm opname met een server fout in de toepassings fout in een webbrowser.":::

**Er is een fout opgetreden:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Scherm afbeelding met een voor beeld van een algemene fout in een webbrowser.":::

**De website kan de pagina niet weer geven**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Scherm opname met een website kan de pagina fout niet weer geven in een webbrowser.":::

De eenvoudigste manier om de oorzaak van de fout te vinden, is vaak om gedetailleerde fout berichten in te scha kelen, zoals in de eerste van de voor gaande scherm afbeeldingen wordt uitgelegd hoe u dit moet doen. Hiervoor is een wijziging in het geïmplementeerde Web.config-bestand vereist. U kunt het *Web.config* -bestand in het project bewerken en het project opnieuw implementeren, of een [Web.config-trans formatie](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) maken en een debug-build implementeren, maar er is een snellere manier: in **Solution Explorer** u de bestanden in de externe app rechtstreeks weer geven en bewerken met behulp van de functie *externe weer gave* .

1. Vouw in **Server Explorer** **Azure** uit, vouw **app service** uit, vouw de resource groep uit waarin uw app zich bevindt en vouw vervolgens het knoop punt voor uw app uit.

    U ziet knoop punten die u toegang geven tot de inhouds bestanden en logboek bestanden van de app.
2. Vouw het knoop punt **bestanden** uit en dubbel klik op het bestand *Web.config* .

    ![Web.config openen](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio opent het Web.config-bestand van de externe app en toont [Remote] naast de naam van het bestand in de titel balk.
3. Voeg de volgende regel toe aan het `system.web` element:

    `<customErrors mode="Off"></customErrors>`

    ![Web.config bewerken](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Vernieuw de browser waarin het fout bericht niet meer wordt weer gegeven en u krijgt nu een gedetailleerd fout bericht, zoals het volgende voor beeld:

    ![Gedetailleerd foutbericht](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (De fout die wordt weer gegeven, is gemaakt door de regel die wordt weer gegeven in rood, toe te voegen aan *Views\Home\Index.cshtml*.)

Het bewerken van het Web.config bestand is slechts één voor beeld van scenario's waarin de mogelijkheid om bestanden op uw App Service app te lezen en te bewerken eenvoudiger te maken is om problemen op te lossen.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Apps voor externe fout opsporing
Als het gedetailleerde fout bericht niet voldoende informatie biedt en u de fout niet lokaal opnieuw kunt maken, kunt u het probleem op een andere manier oplossen door op afstand de foutopsporingsmodus uit te voeren. U kunt onderbrekings punten instellen, het geheugen rechtstreeks manipuleren, de code stapsgewijs door lopen en zelfs het codepad wijzigen.

Externe fout opsporing werkt niet in Express-edities van Visual Studio.

In deze sectie wordt uitgelegd hoe u op afstand fouten oplost met het project dat u maakt in [een ASP.net-app maken in azure app service](quickstart-dotnet-framework.md).

1. Open het webproject dat u hebt gemaakt in [een ASP.net-app maken in azure app service](quickstart-dotnet-framework.md).

1. Open *Controllers\HomeController.cs*.

1. Verwijder de- `About()` methode en voeg de volgende code toe.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Stel een onderbrekings punt](/visualstudio/debugger/) in op de `ViewBag.Message` regel.

1. Klik in **Solution Explorer** met de rechter muisknop op het project en klik op **publiceren**.

1. Selecteer in de vervolg keuzelijst **profiel** het profiel dat u hebt gebruikt bij het maken van [een ASP.net-app in azure app service](quickstart-dotnet-framework.md). Klik vervolgens op instellingen.

1. Klik in het dialoog venster **publiceren** op het tabblad **instellingen** , wijzig de **configuratie** in **fout opsporing** en klik vervolgens op **Opslaan**.

    ![Publiceren in foutopsporingsmodus](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klik op **Publish**. Nadat de implementatie is voltooid en uw browser wordt geopend met de Azure-URL van uw app, sluit u de browser.

1. Klik in **Server Explorer** met de rechter muisknop op uw app en klik vervolgens op **fout opsporing toevoegen**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Scherm opname van het Server Explorer venster waarin een app is geselecteerd en klik vervolgens op fout opsporing toevoegen.":::

    De browser wordt automatisch geopend op uw start pagina die in azure wordt uitgevoerd. Mogelijk moet u 20 seconden wachten, terwijl Azure de server instelt voor fout opsporing. Deze vertraging treedt alleen op wanneer u voor de eerste keer in de foutopsporingsmodus op een app in een periode van 48 uur wordt uitgevoerd. Wanneer u de fout opsporing opnieuw in dezelfde periode start, is er geen vertraging.

    > [!NOTE] 
    > Als u problemen ondervindt met het starten van het fout opsporingsprogramma, kunt u dit doen met behulp van **Cloud Explorer** in plaats van **Server Explorer**.
    >

1. Klik op **info** in het menu.

    Visual Studio stopt op het onderbrekings punt en de code wordt uitgevoerd in azure, niet op uw lokale computer.

1. Beweeg de muis aanwijzer over de `currentTime` variabele om de tijd waarde weer te geven.

    ![De variabele weer geven in de foutopsporingsmodus die in azure wordt uitgevoerd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    De tijd die u ziet, is de Azure-server tijd, die mogelijk in een andere tijd zone ligt dan uw lokale computer.

1. Voer een nieuwe waarde in voor de `currentTime` variabele, zoals ' nu uitgevoerd in azure '.

1. Druk op F5 om door te gaan met uitvoeren.

     Op de pagina over die in azure wordt uitgevoerd, wordt de nieuwe waarde weer gegeven die u hebt ingevoerd in de variabele currentTime.

     ![Pagina over een nieuwe waarde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> Webjobs voor fout opsporing op afstand
In deze sectie wordt uitgelegd hoe u op afstand fouten oplost met het project en de app die u maakt in aan [de slag met de Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

De functies die in deze sectie worden weer gegeven, zijn alleen beschikbaar in Visual Studio 2013 met update 4 of hoger.

Externe fout opsporing werkt alleen met doorlopende webjobs. Geplande en on-demand webjobs bieden geen ondersteuning voor fout opsporing.

1. Open het webproject dat u hebt gemaakt in aan [de slag met de Azure WEBJOBS SDK][GetStartedWJ].

2. Open *functions.cs* in het project ContosoAdsWebJob.

3. [Stel een onderbrekings punt](/visualstudio/debugger/) in op de eerste instructie in de `GnerateThumbnail` methode.

    ![Onderbrekings punt instellen](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Klik in **Solution Explorer** met de rechter muisknop op het webproject (niet het project taak) en klik op **publiceren**.

5. Selecteer in de vervolg keuzelijst **profiel** het profiel dat u hebt gebruikt om [aan de slag te gaan met de Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klik op het tabblad **instellingen** en wijzig de **configuratie** in **fout opsporing** en klik vervolgens op **publiceren**.

    Visual Studio implementeert de web-en Webtaak projecten en uw browser wordt geopend met de Azure-URL van uw app.

7. Vouw in **Server Explorer** **Azure > app service > uw resource groep > uw app > webjobs > doorlopend** en klik vervolgens met de rechter muisknop op **ContosoAdsWebJob**.

8. Klik op **fout opsporing toevoegen**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Scherm opname van Server Explorer weer geven dat ContosoAdsWebJob is geselecteerd in de vervolg keuzelijst en het fout opsporingsprogramma koppelen is geselecteerd.":::

    De browser wordt automatisch geopend op uw start pagina die in azure wordt uitgevoerd. Mogelijk moet u 20 seconden wachten, terwijl Azure de server instelt voor fout opsporing. Deze vertraging treedt alleen op wanneer u voor de eerste keer in de foutopsporingsmodus op een app in een periode van 48 uur wordt uitgevoerd. Wanneer u de fout opsporing opnieuw in dezelfde periode start, is er geen vertraging.

9. Maak in de webbrowser die is geopend op de start pagina van Contoso Ads een nieuwe advertentie.

    Als u een AD maakt, wordt er een wachtrij bericht gemaakt dat wordt opgehaald door de Webtaak en verwerkt. Wanneer de webjobs SDK de functie aanroept om het wachtrij bericht te verwerken, komt de code voor uw onderbrekings punt.

10. Wanneer het fout opsporingsprogramma op uw onderbrekings punt breekt, kunt u de waarden van de variabelen bekijken en wijzigen terwijl de Cloud wordt uitgevoerd. In de volgende afbeelding ziet u het fout opsporingsprogramma de inhoud van het blobInfo-object dat is door gegeven aan de- `GenerateThumbnail` methode.

     ![blobInfo-object in fout opsporing](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Druk op F5 om door te gaan met uitvoeren.

     De `GenerateThumbnail` methode is klaar met het maken van de miniatuur.

12. Vernieuw in de browser de index pagina en u ziet de miniatuur.

13. Druk in Visual Studio op SHIFT + F5 om de fout opsporing te stoppen.

14. Klik in **Server Explorer** met de rechter muisknop op het knoop punt ContosoAdsWebJob en klik op **dash board weer geven**.

15. Meld u aan met uw Azure-referenties en klik vervolgens op de naam van de Webtaak om naar de pagina voor uw Webtaak te gaan.

     ![Klik op ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Het dash board laat zien dat de `GenerateThumbnail` functie onlangs is uitgevoerd.

     (De volgende keer dat u op **dash board weer geven** klikt, hoeft u zich niet aan te melden en de browser gaat rechtstreeks naar de pagina voor uw Webtaak.)

16. Klik op de naam van de functie om details over de uitvoering van de functie weer te geven.

     ![Functie Details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Als uw functie [Logboeken heeft geschreven](https://github.com/Azure/azure-webjobs-sdk/wiki), kunt u op **ToggleOutput** klikken om ze weer te geven.

## <a name="notes-about-remote-debugging"></a>Opmerkingen over foutopsporing op afstand

* Het uitvoeren van de foutopsporingsmodus in de productie omgeving wordt niet aanbevolen. Als uw productie-app niet naar meerdere server instanties kan worden geschaald, voor komt fout opsporing dat de webserver reageert op andere aanvragen. Als u meerdere webserver instanties hebt, wanneer u aan het fout opsporingsprogramma koppelt, krijgt u een wille keurig exemplaar en hebt u geen enkele manier om ervoor te zorgen dat volgende browser aanvragen naar hetzelfde exemplaar gaan. Het is ook niet mogelijk om een build voor fout opsporing te implementeren voor productie en compilatie optimalisaties voor builds voor het maken van een compiler kan ertoe leiden dat het niet mogelijk is om te laten zien wat er in de bron code op regel staat. Voor het oplossen van problemen met productie zijn de beste bronnen voor toepassings tracering en webserver logboeken.
* Vermijd lange stops bij onderbrekings punten wanneer externe fout opsporing. Azure behandelt een proces dat langer dan een paar minuten wordt gestopt als een proces dat niet reageert en sluit het af.
* Tijdens het opsporen van fouten, verzendt de server gegevens naar Visual Studio. Dit kan van invloed zijn op de bandbreedte kosten. Zie [Azure-prijzen](https://azure.microsoft.com/pricing/calculator/)voor meer informatie over de bandbreedte tarieven.
* Zorg ervoor dat het `debug` kenmerk van het `compilation` element in het *Web.config* bestand is ingesteld op True. De instelling is standaard ingesteld op true wanneer u een configuratie voor het maken van fout opsporing publiceert.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Als u merkt dat het fout opsporingsprogramma de code die u wilt opsporen niet Step Into, moet u mogelijk de Just My Code instelling wijzigen.  Zie voor meer informatie [opgeven of u alleen fouten wilt opsporen in gebruikers code met behulp van Just my code in Visual Studio](/visualstudio/debugger/just-my-code).
* Een timer wordt gestart op de server wanneer u de functie voor fout opsporing op afstand inschakelt en na 48 uur wordt de functie automatisch uitgeschakeld. Deze limiet van 48 uur wordt uitgevoerd om veiligheids-en prestatie redenen. U kunt de functie zo vaak als u wilt weer inschakelen. U wordt aangeraden de functie uitgeschakeld te laten wanneer u niet actief fouten opspoort.
* U kunt het fout opsporingsprogramma hand matig koppelen aan elk proces, niet alleen het app-proces (w3wp.exe). Zie [fout opsporing in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio)voor meer informatie over het gebruik van de foutopsporingsmodus in Visual Studio.

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Overzicht van Diagnostische logboeken
Een ASP.NET-toepassing die wordt uitgevoerd in een App Service-app, kan de volgende soorten logboeken maken:

* **Logboeken voor tracering van toepassingen**<br/>
  De toepassing maakt deze logboeken door methoden van de klasse [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) aan te roepen.
* **Webserver logboeken**<br/>
  De webserver maakt een logboek vermelding voor elke HTTP-aanvraag bij de app.
* **Gedetailleerde logboeken voor fout berichten**<br/>
  De webserver maakt een HTML-pagina met aanvullende informatie voor mislukte HTTP-aanvragen (aanvragen die resulteren in status code 400 of hoger).
* **Logboeken voor tracering van mislukte aanvragen**<br/>
  De webserver maakt een XML-bestand met gedetailleerde tracerings informatie voor mislukte HTTP-aanvragen. De webserver biedt ook een XSL-bestand om de XML in een browser op te maken.

Logboek registratie is van invloed op de prestaties van de app, zodat Azure u de mogelijkheid biedt om elk type logboek naar wens in of uit te scha kelen. Voor toepassings Logboeken kunt u opgeven dat alleen logboeken boven een bepaald Ernst niveau moeten worden geschreven. Wanneer u een nieuwe app maakt, wordt standaard alle logboek registratie uitgeschakeld.

Logboeken worden geschreven naar bestanden in een map *logfiles* in het bestands systeem van uw app en zijn toegankelijk via FTP. Webserver logboeken en toepassings logboeken kunnen ook worden geschreven naar een Azure Storage-account. U kunt een groter volume aan logboeken in een opslag account bewaren dan mogelijk is in het bestands systeem. Wanneer u het bestands systeem gebruikt, bent u beperkt tot een maximum van 100 MB aan logboeken. (Bestandssysteem logboeken zijn alleen bedoeld voor retentie op korte termijn. Azure verwijdert oude logboek bestanden om ruimte te maken voor nieuwe, nadat de limiet is bereikt.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Tracerings logboeken voor toepassingen maken en weer geven
In deze sectie voert u de volgende taken uit:

* Voeg tracerings instructies toe aan het webproject dat u hebt gemaakt in aan de [slag met Azure en ASP.net](quickstart-dotnet-framework.md).
* Bekijk de logboeken wanneer u het project lokaal uitvoert.
* Bekijk de logboeken zoals deze worden gegenereerd door de toepassing die wordt uitgevoerd in Azure.

Voor informatie over het maken van toepassings Logboeken in webjobs, raadpleegt [u werken met Azure Queue Storage met de Webjobs SDK-logboeken schrijven](https://github.com/Azure/azure-webjobs-sdk/wiki). De volgende instructies voor het weer geven van Logboeken en bepalen hoe ze worden opgeslagen in azure zijn ook van toepassing op toepassings logboeken die zijn gemaakt door webjobs.

### <a name="add-tracing-statements-to-the-application"></a>Tracerings instructies toevoegen aan de toepassing
1. Open *Controllers\HomeController.cs* en vervang de `Index` methoden, `About` , en door `Contact` de volgende code om instructies toe te voegen `Trace` en een `using` instructie voor `System.Diagnostics` :

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Een `using System.Diagnostics;` instructie boven aan het bestand toevoegen.

### <a name="view-the-tracing-output-locally"></a>De tracerings uitvoer lokaal weer geven
1. Druk op F5 om de toepassing uit te voeren in de foutopsporingsmodus.

    De default Trace listener schrijft alle uitvoer van de tracering naar het **uitvoer** venster, samen met andere uitvoer van fout opsporing. In de volgende afbeelding ziet u de uitvoer van de trace-instructies die u aan de methode hebt toegevoegd `Index` .

    ![Traceren in het venster fout opsporing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    De volgende stappen laten zien hoe u de tracerings uitvoer in een webpagina kunt weer geven zonder te compileren in de foutopsporingsmodus.
1. Open het Web.config bestand van de toepassing (de map die zich in de projectmap bevindt) en voeg `<system.diagnostics>` aan het einde van het bestand een element toe, net vóór het eind `</configuration>` element:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

`WebPageTraceListener`Met de kunt u tracerings uitvoer weer geven door te bladeren naar `/trace.axd` .
1. Voeg een <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">tracerings element</a> toe onder `<system.web>` in het Web.config-bestand, zoals in het volgende voor beeld:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Druk op CTRL + F5 om de toepassing uit te voeren.
1. Voeg in de adres balk van het browser venster *tracering. axd* toe aan de URL en druk op ENTER (de URL is vergelijkbaar met `http://localhost:53370/trace.axd` ).
1. Klik op de pagina **toepassings tracering** op **Details weer geven** op de eerste regel (niet op de regel BrowserLink).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Scherm afbeelding van de pagina Toepassings tracering in een webbrowser met weer gave-details die zijn geselecteerd op de eerste regel.":::

    De pagina **aanvraag Details** wordt weer gegeven en in de sectie **tracerings gegevens** ziet u de uitvoer van de trace-instructies die u hebt toegevoegd aan de- `Index` methode.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Scherm afbeelding van de pagina met details van de aanvraag in een webbrowser waarin een bericht wordt weer gegeven dat is gemarkeerd in de sectie informatie over tracering.":::

    `trace.axd`Is standaard alleen lokaal beschikbaar. Als u het beschikbaar wilt maken vanuit een externe app, kunt u dit toevoegen `localOnly="false"` aan het `trace` -element in het *Web.config* -bestand, zoals wordt weer gegeven in het volgende voor beeld:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    `trace.axd`Het inschakelen van een productie-app wordt echter niet aanbevolen om veiligheids redenen. In de volgende secties ziet u een eenvoudige manier om traceer Logboeken in een App Service-app te lezen.

### <a name="view-the-tracing-output-in-azure"></a>De tracerings uitvoer in azure weer geven
1. Klik in **Solution Explorer** met de rechter muisknop op het webproject en klik op **publiceren**.
2. Klik in het dialoog venster **Publish Web** op **Publish**.

    Nadat Visual Studio uw update heeft gepubliceerd, wordt er een browser venster geopend op uw start pagina (ervan uitgaande dat u de **doel-URL** niet hebt gewist op het tabblad **verbinding** ).
3. Klik in **Server Explorer** met de rechter muisknop op uw app en selecteer **streaming-logboeken weer geven**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Scherm opname van Server Explorer nadat u met de rechter muisknop op uw app hebt geklikt, waarbij streaming-logboeken weer geven in een nieuw venster zijn geselecteerd.":::

    In het **uitvoer** venster ziet u dat u verbonden bent met de service voor logboek registratie en wordt elke minuut een meldings regel toegevoegd zonder dat een logboek wordt weer gegeven.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Scherm afbeelding van het uitvoer venster met een voor beeld van een verbinding met een service voor logboek registratie met behulp van meldings regels.":::

4. Klik in het browser venster waarin de start pagina van uw toepassing wordt weer gegeven op **contact persoon**.

    Binnen een paar seconden wordt de uitvoer van de tracering op fout niveau die u hebt toegevoegd aan de- `Contact` methode weer gegeven in het **uitvoer** venster.

    ![Fout traceren in uitvoer venster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio toont alleen traceringen op fout niveau omdat dit de standaard instelling is wanneer u de logboek bewakings service inschakelt. Wanneer u een nieuwe App Service-app maakt, wordt standaard alle logboek registratie uitgeschakeld, omdat u hebt gezien wanneer u de pagina instellingen eerder hebt geopend:

    ![Application logging uit](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Wanneer u echter streaming- **Logboeken weer geven** hebt geselecteerd, heeft Visual Studio automatisch de **toepassings Logboeken (bestands systeem)** gewijzigd in **fout**, wat betekent dat Logboeken op fout niveau worden gerapporteerd. Als u al uw tracerings logboeken wilt weer geven, kunt u deze instelling wijzigen in **uitgebreid**. Wanneer u een lager niveau dan fout selecteert, worden alle logboeken voor hogere Ernst niveaus ook gerapporteerd. Wanneer u uitgebreid selecteert, ziet u ook informatie, waarschuwingen en fout Logboeken.  

5. Klik in **Server Explorer** met de rechter muisknop op de app en klik vervolgens op **instellingen weer geven** zoals u eerder hebt gedaan.
6. Wijzig **toepassings Logboeken (bestands systeem)** in **uitgebreid** en klik vervolgens op **Opslaan**.

    ![Het tracerings niveau instellen op uitgebreid](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Klik in het browser venster dat nu uw **contact** pagina weergeeft op **Start**, klik op **info** en klik vervolgens op **contact persoon**.

    Binnen een paar seconden wordt in het **uitvoer** venster al uw tracerings uitvoer weer gegeven.

    ![Uitgebreide tracerings uitvoer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    In deze sectie hebt u logboek registratie ingeschakeld en uitgeschakeld met behulp van app-instellingen. U kunt traceer listeners ook in-en uitschakelen door het Web.config-bestand te wijzigen. Als u echter het Web.config bestand wijzigt, wordt het app-domein opnieuw gerecycled, terwijl het inschakelen van logboek registratie via de app-configuratie dit niet doet. Als het probleem veel tijd in beslag neemt of als er een terugkerende fout optreedt, kan het app-domein worden gerepareerd en kunt u wachten totdat het opnieuw wordt uitgevoerd. Door Diagnostische gegevens in te scha kelen in azure kunt u meteen beginnen met het vastleggen van fout gegevens zonder het app-domein te recyclen.

### <a name="output-window-features"></a>Functies van het uitvoer venster
Het tabblad **Microsoft Azure logboeken** van het **uitvoer** venster bevat verschillende knoppen en een tekstvak:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Scherm afbeelding met de knoppen en het tekstvak van het tabblad Microsoft Azure Logboeken in het uitvoer venster.":::

U voert de volgende functies uit:

* Het **uitvoer** venster wissen.
* Tekst terugloop in-of uitschakelen.
* Bewakings Logboeken starten of stoppen.
* Opgeven welke logboeken moeten worden bewaakt.
* Logboeken downloaden.
* Logboeken filteren op basis van een zoek reeks of een reguliere expressie.
* Sluit het **uitvoer** venster.

Als u een zoek reeks of reguliere expressie invoert, filtert Visual Studio de registratie gegevens op de client. Dit betekent dat u de criteria kunt invoeren nadat de logboeken worden weer gegeven in het **uitvoer** venster en u kunt filter criteria wijzigen zonder de logboeken opnieuw te hoeven genereren.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Webserver logboeken weer geven
Webserver logboeken registreren alle HTTP-activiteiten voor de app. Als u deze wilt weer geven in het **uitvoer** venster, moet u deze inschakelen voor de app en Visual Studio vertellen dat u ze wilt bewaken.

1. Op het tabblad **configuratie van Azure-web-app** dat u hebt geopend vanuit **Server Explorer**, wijzigt u de logboek registratie van webserver in **op** aan en klikt u vervolgens op **Opslaan**.

    ![Logboek registratie van webserver inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Klik in het venster **uitvoer** op de knop **opgeven welke Microsoft Azures logboeken wilt bewaken** .

    ![Opgeven welke Azure-logboeken moeten worden bewaakt](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Selecteer in het dialoog venster **Opties voor Microsoft Azure logboek registratie** de optie **webserver logboeken** en klik vervolgens op **OK**.

    ![Webserver logboeken bewaken](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. In het browser venster waarin de app wordt weer gegeven, klikt u op **Start**, klikt u op **about** en klikt u vervolgens op **contact persoon**.

    De toepassings logboeken worden doorgaans eerst weer gegeven, gevolgd door de logboeken van de webserver. Mogelijk moet u een tijdje wachten totdat de logboeken worden weer gegeven.

    ![Webserver logboeken in uitvoer venster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Wanneer u webserver logboeken voor het eerst inschakelt met behulp van Visual Studio, schrijft Azure de logboeken standaard naar het bestands systeem. Als alternatief kunt u de Azure Portal gebruiken om op te geven dat webserver logboeken moeten worden geschreven naar een BLOB-container in een opslag account.

Als u de portal gebruikt om logboek registratie van een webserver in te scha kelen in een Azure-opslag account en vervolgens logboek registratie uit te scha kelen in Visual Studio, worden de instellingen van uw opslag account hersteld wanneer u logboek registratie opnieuw inschakelt in Visual Studio.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Gedetailleerde logboeken voor fout berichten weer geven
Gedetailleerde fout logboeken bevatten aanvullende informatie over HTTP-aanvragen die resulteren in fout code codes (400 of hoger). Als u deze wilt weer geven in het **uitvoer** venster, moet u deze inschakelen voor de app en Visual Studio vertellen dat u ze wilt bewaken.

1. Op het tabblad **configuratie van Azure-web-app** dat u hebt geopend vanuit **Server Explorer**, wijzigt u **gedetailleerde fout berichten** in **op** aan en klikt u vervolgens op **Opslaan**.

    ![Gedetailleerde fout berichten inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Klik in het venster **uitvoer** op de knop **opgeven welke Microsoft Azures logboeken wilt bewaken** .

3. Klik in het dialoog venster **Opties voor Microsoft Azure logboek registratie** op **alle logboeken** en klik vervolgens op **OK**.

    ![Alle logboeken bewaken](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Voeg in de adres balk van het browser venster een extra teken toe aan de URL om een 404-fout te veroorzaken (bijvoorbeeld `http://localhost:53370/Home/Contactx` ) en druk op ENTER.

    Na enkele seconden wordt het gedetailleerde fouten logboek weer gegeven in het Visual Studio- **uitvoer** venster.

    ![Gedetailleerd venster voor het uitvoeren van fouten logboeken](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control + klik op de koppeling om de logboek uitvoer weer te geven die in een browser is opgemaakt:

    ![Gedetailleerd fouten logboek: browser venster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Bestandssysteem logboeken downloaden
Logboeken die u kunt bewaken in het **uitvoer** venster kunnen ook worden gedownload als *zip* -bestand.

1. Klik in het venster **uitvoer** op **streaming-logboek bestanden downloaden**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Scherm afbeelding van het uitvoer venster met de knop streaming-logboeken downloaden gemarkeerd.":::

    Bestanden Verkenner wordt geopend in de map *down loads* met het gedownloade bestand dat is geselecteerd.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Scherm afbeelding van de map down loads in Verkenner waarin een gedownload bestand is geselecteerd.":::

2. Pak het *zip* -bestand uit en u ziet de volgende mapstructuur:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Scherm afbeelding van de mapstructuur van het zip-bestand nadat het bestand is geëxtraheerd.":::

   * Logboeken voor toepassings tracering bevinden zich in *. txt* -bestanden in de map *LogFiles\Application*
   * Webserver logboeken bevinden zich in *. log* -bestanden in de map *LogFiles\http\RawLogs* . U kunt een hulp programma zoals [log parser](https://www.iis.net/downloads/community/2010/04/log-parser-22) gebruiken om deze bestanden weer te geven en te bewerken.
   * Gedetailleerde logboeken voor fout berichten bevinden zich in *. html-* bestanden in de map *LogFiles\DetailedErrors*

     (De map *implementaties* is voor bestanden die zijn gemaakt door de publicatie van broncode beheer, maar heeft niets te maken met Visual Studio Publishing. De map *Git* is voor traceringen met betrekking tot broncode beheer en de service voor het streamen van logboek bestanden.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Logboeken voor tracering van mislukte aanvragen weer geven
Logboeken voor tracering van mislukte aanvragen zijn handig als u meer informatie wilt over de wijze waarop IIS een HTTP-aanvraag afhandelt, in scenario's zoals het herschrijven van URL'S of het oplossen van problemen.

App Service-apps gebruiken dezelfde functionaliteit voor tracering van mislukte aanvragen die beschikbaar is in IIS 7,0 en hoger. U hebt geen toegang tot de IIS-instellingen die bepalen welke fouten worden vastgelegd. Wanneer u tracering van mislukte aanvragen inschakelt, worden alle fouten vastgelegd.

U kunt het traceren van mislukte aanvragen inschakelen met behulp van Visual Studio, maar dit kan niet worden weer gegeven in Visual Studio. Deze logboeken zijn XML-bestanden. De streaming-logboek Service bewaakt alleen bestanden die als leesbaar worden beschouwd in de modus voor tekst zonder opmaak:  *. txt*, *. html* en *. log* -bestanden.

U kunt Logboeken voor tracering van mislukte aanvragen bekijken in een browser rechtstreeks via FTP of lokaal nadat u een FTP-programma hebt gebruikt om ze te downloaden naar uw lokale computer. In deze sectie bekijkt u deze rechtstreeks in een browser.

1. Op het tabblad **configuratie** van het venster van de **Azure-web-app** dat u hebt geopend vanuit **Server Explorer**, wijzigt u het **traceren van mislukte aanvragen** in **op** aan en klikt u vervolgens op **Opslaan**.

    ![Tracering van mislukte aanvragen inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. In de adres balk van het browser venster waarin de app wordt weer gegeven, voegt u een extra teken toe aan de URL en klikt u op ENTER om een 404-fout te veroorzaken.

    Dit leidt ertoe dat een logboek voor tracering van mislukte aanvragen wordt gemaakt. in de volgende stappen ziet u hoe u het logboek kunt weer geven of downloaden.

3. Ga in Visual Studio naar het tabblad **configuratie** van het venster van de **Azure-web-app** en klik op **openen in Beheerportal**.

4. Klik op de pagina **instellingen** van [Azure Portal](https://portal.azure.com) voor uw app op **implementatie referenties** en voer vervolgens een nieuwe gebruikers naam en wacht woord in.

    ![Nieuwe FTP-gebruikers naam en-wacht woord](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Wanneer u zich aanmeldt, moet u de volledige gebruikers naam met de naam van de app gebruiken. Als u bijvoorbeeld ' MyId ' opgeeft als een gebruikers naam en de site is ' myExample ', meldt u zich aan als ' myexample\myid '.
    >

5. Ga in een nieuw browser venster naar de URL die wordt weer gegeven onder **ftp hostname** of **FTPS hostnaam** op de pagina **overzicht** voor uw app.

6. Meld u aan met de FTP-referenties die u eerder hebt gemaakt (met inbegrip van het voor voegsel van de app-naam voor de gebruikers naam).

    In de browser wordt de hoofdmap van de app weer gegeven.

7. Open de map *logfiles* .

    ![Map met logboek bestanden openen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Open de map met de naam W3SVC plus een numerieke waarde.

    ![Map W3SVC openen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    De map bevat XML-bestanden voor fouten die zijn vastgelegd nadat u het traceren van mislukte aanvragen hebt ingeschakeld, en een XSL-bestand dat een browser kan gebruiken om de XML op te maken.

    ![Map W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klik op het XML-bestand voor de mislukte aanvraag waarvoor u tracerings informatie wilt weer geven.

    In de volgende afbeelding ziet u een deel van de tracerings gegevens voor een voorbeeld fout.

    ![Tracering van mislukte aanvragen in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Volgende stappen
U hebt gezien hoe u met Visual Studio eenvoudig Logboeken kunt weer geven die zijn gemaakt met een App Service-app. De volgende secties bevatten koppelingen naar meer bronnen over verwante onderwerpen:

* App Service probleem oplossing
* Fout opsporing in Visual Studio
* Externe fout opsporing in azure
* Tracering in ASP.NET-toepassingen
* Webserver logboeken analyseren
* Logboeken voor tracering van mislukte aanvragen analyseren
* Fout opsporing Cloud Services

### <a name="app-service-troubleshooting"></a>App Service probleem oplossing
Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen met apps in Azure App Service:

* [Apps controleren](web-sites-monitor.md)
* Het [onderzoeken van geheugen lekken in azure app service met Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Micro soft ALM blog bericht over de functies van Visual Studio voor het analyseren van problemen met beheerde geheugen.
* [Azure app service online hulp middelen waarover u moet weten](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blog bericht van Amit Apple.

Start een thread in een van de volgende forums voor hulp bij een specifieke vraag om problemen op te lossen:

* [Het Azure-forum op de ASP.net-site](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Het Azure-forum op micro soft Q&A](/answers/topics/azure-webapps.html).
* [Stackoverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Fout opsporing in Visual Studio
Zie [fout opsporing in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) en [Tips voor fout opsporing](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)in Visual Studio 2010 voor meer informatie over het gebruik van de foutopsporingsmodus in Visual Studio.

### <a name="remote-debugging-in-azure"></a>Externe fout opsporing in azure
Raadpleeg de volgende bronnen voor meer informatie over fout opsporing op afstand voor App Service-apps en webjobs:

* [Inleiding tot externe fout opsporing Azure app service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Inleiding tot externe fout opsporing Azure App Service deel 2-in externe fout opsporing](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Inleiding tot externe fout opsporing op Azure App Service deel 3-omgeving met meerdere instanties en GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Webjobs fout opsporing (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Als uw app gebruikmaakt van een Azure Web API of Mobile Services back-end en u fouten wilt opsporen, raadpleegt u [fout opsporing in .net-back-end in Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio).

### <a name="tracing-in-aspnet-applications"></a>Tracering in ASP.NET-toepassingen
Er zijn geen uitgebreide en recente inleidingen voor ASP.NET tracering beschikbaar op internet. U kunt het beste aan de slag gaan met oude inleidende materialen die zijn geschreven voor webformulieren, omdat MVC nog niet bestaat en als aanvulling op de nieuwe blog berichten die gericht zijn op specifieke problemen. Een aantal goede locaties om te starten zijn de volgende resources:

* [Bewaking en telemetrie (buil ding Real-World Cloud-apps met Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-Book hoofd stuk met aanbevelingen voor tracering in azure-Cloud toepassingen.
* [ASP.NET tracering](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Oud, maar nog steeds een goede resource voor een basis kennis van het onderwerp.
* [Traceer listeners](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informatie over traceer listeners, maar geen vermelding van de [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Walkthrough: ASP.NET tracering integreren met System. Diagnostics-tracering](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Dit artikel is ook oud, maar bevat aanvullende informatie die niet geldt voor het inleidende artikel.
* [Tracering in ASP.NET MVC-weer gaven](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Naast het traceren in haar weer gaven, wordt in het bericht ook uitgelegd hoe u een fout filter maakt om alle niet-verwerkte uitzonde ringen in een MVC-toepassing te registreren. Zie voor meer informatie over het registreren van alle niet-verwerkte uitzonde ringen in een Web Forms-toepassing het voor beeld Global. asax in het [volledige voor beeld voor fout afhandeling](/previous-versions/bb397417(v=vs.140)) op MSDN. Als u in MVC-of webformulieren bepaalde uitzonde ringen wilt vastleggen, maar de standaard verwerking van het Framework wilt laten gelden, kunt u deze ondervangen en opnieuw genereren, zoals in het volgende voor beeld:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Traceer logboek registratie van streaming Diagnostics vanaf de Azure-opdracht regel (plus idee!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  De opdracht regel gebruiken om te doen in deze zelf studie wordt uitgelegd hoe u in Visual Studio kunt doen. [Idee](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) is een hulp programma voor het opsporen van fouten in ASP.NET-toepassingen.
* [Web apps logboek registratie en diagnostische gegevens gebruiken: met David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) [-en streaming-logboeken van web apps-met David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Video's van Scott Hanselman en David Ebbo.

Voor fouten logboek registratie is het gebruik van een open source-framework voor logboek registratie, zoals [ELMAH](https://nuget.org/packages/elmah/), een alternatief voor het schrijven van uw eigen tracerings code. Zie [de blog berichten van Scott Hanselman over ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)voor meer informatie.

Bovendien hoeft u ASP.NET of `System.Diagnostics` tracering niet te gebruiken voor het ophalen van streaming-logboeken vanuit Azure. Met de App Service-logboek service voor het streamen van apps worden alle *txt*-, *HTML-* of *log* -bestanden in de map *logfiles* gestreamd. Daarom kunt u uw eigen logboek registratie systeem maken dat naar het bestands systeem van de app schrijft, waarna het bestand automatisch wordt gestreamd en gedownload. U hoeft alleen maar een toepassings code te schrijven waarmee bestanden worden gemaakt in de map *d:\home\logfiles* .

### <a name="analyzing-web-server-logs"></a>Webserver logboeken analyseren
Raadpleeg de volgende bronnen voor meer informatie over het analyseren van webserver logboeken:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Een hulp programma voor het weer geven van gegevens in webserver logboeken (*logboek* bestanden).
* [Problemen met IIS-prestatie problemen of toepassings fouten oplossen met LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Een inleiding tot het hulp programma log parser dat u kunt gebruiken om webserver logboeken te analyseren.
* [Blog berichten door Robert McMurray met LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [De HTTP-status code in IIS 7,0, IIS 7,5 en IIS 8,0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Logboeken voor tracering van mislukte aanvragen analyseren
De micro soft TechNet-website bevat een sectie voor het [traceren van mislukte aanvragen](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) . Dit kan handig zijn als u wilt weten hoe u deze logboeken kunt gebruiken. Deze documentatie is echter voornamelijk gericht op het configureren van tracering van mislukte aanvragen in IIS, wat u niet kunt doen in Azure App Service.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki