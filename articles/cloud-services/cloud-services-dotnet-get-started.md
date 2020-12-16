---
title: Aan de slag met Azure Cloud Services en ASP.NET | Microsoft Docs
description: Informatie over het maken van een app met meerdere lagen met ASP.NET MVC en Azure. De app wordt uitgevoerd in een cloudservice, met een webrol en een werkrol. De app maakt gebruik van Entity Framework, SQL Database, en wachtrijen en blobs van Azure Storage.
services: cloud-services, storage
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: tagore
ms.openlocfilehash: a875c036c79419357f1134c32f62fdb060fec7c6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562290"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Aan de slag met Azure Cloud Services en ASP.NET

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een .NET-toepassing met meerdere lagen maakt met een ASP.NET MVC-front-end en deze implementeert in een [Azure-cloudservice](cloud-services-choose-me.md). De toepassing gebruikt [Azure SQL Database](/previous-versions/azure/ee336279(v=azure.100)), de [Azure Blob-service](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) en de [Azure Queue-service](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). U kunt [het Visual Studio-project downloaden uit de ](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) uit de MSDN-codegalerie.

In de zelfstudie leert u hoe u de toepassing lokaal maakt en uitvoert, hoe u deze in Azure implementeert en uitvoert in de cloud, en hoe u deze van het begin af aan bouwt. Desgewenst kunt u de toepassing eerst van het begin af aan bouwen en vervolgens later de test doen en stappen implementeren.

## <a name="contoso-ads-application"></a>Toepassing Contoso Ads
De toepassing is een bulletinboard voor advertenties. Gebruikers maken een advertentie door de tekst in te voeren en een afbeelding te uploaden. Ze kunnen een lijst met advertenties bekijken met miniatuurafbeeldingen. Als ze een advertentie selecteren om de details te bekijken, kunnen ze de afbeelding op volledige grootte weergeven.

![Advertentielijst](./media/cloud-services-dotnet-get-started/list.png)

De toepassing maakt gebruik van het [wachtrijgerichte werkpatroon](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) om de CPU te ontlasten bij het maken van miniatuurweergaven voor een back-endproces (een CPU-intensieve bewerking).

## <a name="alternative-architecture-app-service-and-webjobs"></a>Alternatieve architectuur: App Service en webjobs
Deze zelfstudie laat zien hoe u de front-end en back-end uitvoert in een cloudservice van Azure. Het is ook mogelijk om de front-end uit te voeren in [Azure App Service](../app-service/index.yml) en de functie [WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226) te gebruiken voor de back-end. Zie [Aan de slag met de Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) voor een zelfstudie waarin gebruik wordt gemaakt van WebJobs. Zie [Vergelijking van Azure App Service, Virtual Machines, Service Fabric en Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree) voor meer informatie over het kiezen van de services die het meest geschikt zijn voor uw scenario.

## <a name="what-youll-learn"></a>Wat u leert
* De computer klaarmaken voor het ontwikkelen van Azure door de Azure SDK te installeren.
* Een Visual Studio-cloudserviceproject maken met een ASP.NET MVC-webrol en -werkrol.
* Het Cloud service project lokaal testen met behulp van de Azure Storage-emulator.
* Het cloudproject publiceren naar een Azure-cloudservice en het project testen met behulp van een Azure-opslagaccount.
* Bestanden uploaden en deze opslaan in de Azure Blob-service.
* De Azure Queue-service gebruiken voor communicatie tussen lagen.

## <a name="prerequisites"></a>Vereisten
In de zelfstudie wordt ervan uitgegaan dat u bekend bent met de [basisconcepten van Azure-cloudservices](cloud-services-choose-me.md), waaronder de concepten *webrol* en *werkrol*.  Ook wordt ervan uitgegaan dat u weet hoe u in Visual Studio werkt met [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)- of [Web Forms](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview)-projecten. De voorbeeldtoepassing gebruikt MVC, maar het meeste in de zelfstudie geldt ook voor Web Forms.

U kunt de app lokaal zonder Azure-abonnement uitvoeren, maar u hebt wel een abonnement nodig om de toepassing in de cloud te implementeren. Als u geen account hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) of [zich aanmelden voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

De instructies voor de zelf studie werken met een van de volgende producten:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Als u deze niet hebt, wordt Visual Studio mogelijk automatisch geïnstalleerd wanneer u de Azure SDK installeert.

## <a name="application-architecture"></a>Toepassingsarchitectuur
De app slaat de advertenties met behulp van Entity Framework Code First op in een SQL-database om de tabellen te maken en toegang te krijgen tot de gegevens. Voor elke advertentie slaat de database twee URL's op: één voor de afbeelding op volledige grootte en één voor de miniatuur.

![Advertentietabel](./media/cloud-services-dotnet-get-started/adtable.png)

Wanneer een gebruiker een afbeelding uploadt, slaat de front-end (die wordt uitgevoerd in een webrol) de afbeelding op in een [Azure-blob](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage). De advertentiegegevens worden opgeslagen in de database, samen met een URL die naar de blob verwijst. Tegelijkertijd schrijft de front-end een bericht naar een Azure-wachtrij. Een back-end-proces dat wordt uitgevoerd in een werkrol, peilt de wachtrij periodiek op nieuwe berichten. Wanneer er een nieuw bericht binnenkomt, maakt de werkrol een miniatuur voor de betreffende afbeelding en werkt deze het databaseveld met de miniatuur-URL voor de advertentie bij. Het volgende diagram toont de wisselwerking tussen de onderdelen van de toepassing.

![Diagram dat laat zien hoe de onderdelen van de toepassing communiceren.](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>De voltooide oplossing downloaden en uitvoeren
1. Download de [voltooide oplossing](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) en pak deze uit.
2. Start Visual Studio.
3. Kies in het menu **File** de optie **Open Project**, ga naar de locatie waar u de oplossing hebt gedownload en open het oplossingsbestand.
4. Druk op CTRL + SHIFT + B om de oplossing te bouwen.

    Standaard herstelt Visual Studio automatisch de inhoud van het NuGet-pakket, dat niet is opgenomen in het *.zip*-bestand. Als de pakketten niet worden hersteld, installeert u deze handmatig door naar het dialoogvenster **Manage NuGet Packages for Solution** te gaan en rechts bovenaan op de knop **Restore** te klikken.
5. Zorg er in **Solution Explorer** voor dat **ContosoAdsCloudService** is geselecteerd als opstartproject.
6. Als u Visual Studio 2015 of hoger gebruikt, wijzigt u de SQL Server-verbindingsreeks in het *Web.config*-toepassingsbestand van het ContosoAdsWeb-project en in het *ServiceConfiguration.Local.cscfg*-bestand van het ContosoAdsCloudService-project. Wijzig in beide gevallen '(localdb)\v11.0' in '(localdb)\MSSQLLocalDB'.
7. Druk op CTRL + F5 om de toepassing uit te voeren.

    Wanneer u een cloudserviceproject lokaal uitvoert, roept Visual Studio automatisch de Azure-*rekenemulator* en de Azure-*opslagemulator* aan. De rekenemulator maakt gebruik van bronnen van de computer om de webrol- en werkrolomgeving te simuleren. De opslagemulator maakt gebruik van een [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)-database om Azure-cloudopslag te simuleren.

    De eerste keer dat u een cloudserviceproject uitvoert, duurt het ongeveer een minuut voordat de emulator opstart. Nadat de emulator is opgestart, wordt de standaardbrowser geopend met de startpagina van de toepassing.

    ![Architectuur van Contoso Ads](./media/cloud-services-dotnet-get-started/home.png)
8. Klik op **Create an Ad**.
9. Voer enkele testgegevens in en selecteer een *.jpg*-afbeelding om te uploaden. Klik vervolgens op **Create**.

    ![De pagina Create](./media/cloud-services-dotnet-get-started/create.png)

    De app gaat naar de indexpagina, maar geeft voor de nieuwe advertentie geen miniatuur weer omdat de betreffende bewerking nog niet heeft plaatsgevonden.
10. Wacht even en vernieuw vervolgens de indexpagina om de miniatuur te zien.

     ![De indexpagina](./media/cloud-services-dotnet-get-started/list.png)
11. Klik op **Details** voor uw advertentie om de afbeelding op volledige grootte te bekijken.

     ![De pagina Details](./media/cloud-services-dotnet-get-started/details.png)

U hebt de toepassing volledig op uw lokale computer uitgevoerd, zonder verbinding met de cloud. De opslagemulator slaat de wachtrij en de blobgegevens op in een SQL Server Express LocalDB- database en de toepassing slaat de advertentiegegevens op in een andere LocalDB-database. Entity Framework Code First heeft de advertentiedatabase automatisch gemaakt op het moment dat de web-app voor het eerst heeft geprobeerd om deze te openen.

In de volgende sectie configureert u de oplossing voor het gebruik van Azure-cloudbronnen voor wachtrijen, blobs en de toepassingsdatabase wanneer deze wordt uitgevoerd in de cloud. U kunt eventueel lokaal blijven uitvoeren maar wel cloudopslag en databaseresources gebruiken. Het is alleen een kwestie van verbindingsreeksen instellen. We leggen u uit hoe u dit doet.

## <a name="deploy-the-application-to-azure"></a>De toepassing implementeren in Azure
U voert de volgende stappen uit voor het uitvoeren van de toepassing in de cloud:

* Een Azure-cloudservice maken.
* Een database maken in Azure SQL Database.
* Een Azure-opslagaccount maken.
* Configureer de oplossing voor het gebruik van uw data base als deze wordt uitgevoerd in Azure.
* De oplossing configureren voor het gebruik van uw Azure-opslagaccount als deze wordt uitgevoerd in Azure.
* Het project implementeren in uw Azure-cloudservice.

### <a name="create-an-azure-cloud-service"></a>Een Azure-cloudservice maken
Een Azure-cloudservice is de omgeving waarin de toepassing wordt uitgevoerd.

1. Open [Azure Portal](https://portal.azure.com) in uw browser.
2. Klik op **Een resource maken > Compute > Cloudservice**.

3. Voer een URL-voorvoegsel in voor de cloudservice in het invoervak voor de DNS-naam.

    Deze URL moet uniek zijn.  U krijgt een foutmelding als het voorvoegsel dat u kiest al in gebruik is.
4. Geef een nieuwe resourcegroep op voor de service. Klik op **Nieuwe maken** en typ een naam in het invoervak Resourcegroep, zoals CS_contososadsRG.

5. Kies de regio waarin u de toepassing wilt implementeren.

    Dit veld geeft aan in welk datacenter uw cloudservice zal worden gehost. Voor een productietoepassing kiest u de regio die het dichtst bij uw klanten ligt. Voor deze zelfstudie kiest u de regio die het dichtst bij u ligt.
5. Klik op **Create**.

    Op de volgende afbeelding ziet u een cloudservice met de URL CSvccontosoads.cloudapp.net.

    ![Nieuwe cloudservice](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-a-database-in-azure-sql-database"></a>Een database maken in Azure SQL Database
Wanneer de app wordt uitgevoerd in de cloud, gebruikt deze een cloudgebaseerde database.

1. Klik in [Azure Portal](https://portal.azure.com) op **Een resource maken > Databases > SQL Database**.
2. Voer in het vak **Databasenaam***contosoads* in.
3. Klik in de **Resourcegroep** op **Bestaande gebruiken** en selecteer de resourcegroep die voor de cloudservice wordt gebruikt.
4. Klik in de volgende afbeelding op **Server - vereiste instellingen configureren** en **Een nieuwe server maken**.

    ![Tunnel naar databaseserver](./media/cloud-services-dotnet-get-started/newdb.png)

    Als uw abonnement al over een server beschikt, kunt u in de vervolgkeuzelijst ook die server selecteren.
5. Voer in het vak **Servernaam***csvccontosodbserver* in.

6. Geef een **Aanmeldingsnaam** en een **Wachtwoord** op voor de beheerder.

    Als u **Een nieuwe server maken** hebt geselecteerd, voert u hier geen bestaande naam en bestaand wachtwoord in. U voert een nieuwe naam en nieuw wachtwoord in, die u nu definieert om later te gebruiken bij het openen van de database. Als u een eerder gemaakte server hebt geselecteerd, wordt u gevraagd om het wachtwoord voor het bestaande gebruikersaccount met beheerdersrechten.
7. Kies dezelfde **Locatie** die u hebt gekozen voor de cloudservice.

    Wanneer de cloudservice en de database zich in verschillende datacenters (verschillende regio's) bevinden, neemt de latentie toe en wordt de bandbreedte buiten het datacenter aan u in rekening gebracht. Bandbreedte binnen een datacenter is gratis.
8. Schakel **Toegang van Azure-services tot server toestaan** in.
9. Klik op **Selecteren** voor de nieuwe server.

    ![Nieuwe server](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Klik op **Create**.

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken
Een Azure-opslagaccount biedt resources voor het opslaan van wachtrij- en blobgegevens in de cloud.

In een echte toepassing maakt u meestal afzonderlijke accounts voor toepassingsgegevens versus logboekgegevens, en afzonderlijke accounts voor testgegevens versus productiegegevens. Voor deze zelfstudie gebruikt u slechts één account.

1. Klik in [Azure Portal](https://portal.azure.com) op **Een resource maken > Opslag > Opslagaccount - blob, bestand, tabel, wachtrij**.
2. Voer in het vak **Naam** een URL-voorvoegsel in.

    Dit voorvoegsel wordt samen met de tekst onder het vak de unieke URL voor uw opslagaccount. Als het voorvoegsel dat u invoert, al door iemand anders wordt gebruikt, moet u een ander voorvoegsel kiezen.
3. Stel het **Implementatiemodel** in op *Klassiek*.

4. Stel de vervolgkeuzelijst **Replicatie** in op **Lokaal redundante opslag**.

    Wanneer geo-replicatie is ingeschakeld voor een opslagaccount, wordt de opgeslagen inhoud gerepliceerd naar een secundair datacenter om failover mogelijk te maken in het geval van een noodgeval op de primaire locatie. Geo-replicatie kan extra kosten met zich meebrengen. Voor test- en ontwikkelingsaccounts wilt u in het algemeen niet betalen voor geo-replicatie. Zie [Een opslagaccount maken, beheren of verwijderen](../storage/common/storage-account-create.md) voor meer informatie.

5. Klik in de **Resourcegroep** op **Bestaande gebruiken** en selecteer de resourcegroep die voor de cloudservice wordt gebruikt.
6. Stel de vervolgkeuzelijst **Locatie** in op dezelfde regio die u voor de cloudservice hebt gekozen.

    Wanneer de cloudservice en het opslagaccount zich in verschillende datacenters (verschillende regio's) bevinden, neemt de latentie toe en wordt de bandbreedte buiten het datacenter aan u in rekening gebracht. Bandbreedte binnen een datacenter is gratis.

    Azure-affiniteitsgroepen bieden een mechanisme om de afstand tussen resources in een datacenter te minimaliseren, waardoor ze de latentie kunnen verminderen. In deze zelfstudie worden geen affiniteitsgroepen gebruikt. Zie [Een affiniteitsgroep maken in Azure](/previous-versions/azure/reference/gg715317(v=azure.100)) voor meer informatie.
7. Klik op **Create**.

    ![Nieuw opslagaccount](./media/cloud-services-dotnet-get-started/newstorage.png)

    Op de afbeelding ziet u een opslagaccount met de URL `csvccontosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-database-in-azure-sql-database-when-it-runs-in-azure"></a>De oplossing configureren voor het gebruik van uw data base in Azure SQL Database wanneer deze wordt uitgevoerd in azure

Het webproject en het worker-project hebben elk een eigen data base connection string, en elk moet verwijzen naar de data base in Azure SQL Database wanneer de app wordt uitgevoerd in Azure.

U gebruikt een [Web.config-transformatie](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) voor de webrol en een cloudserviceomgevingsinstelling voor de werkrol.

> [!NOTE]
> In deze en de volgende sectie slaat u referenties op in projectbestanden. [Sla gevoelige gegevens niet op in openbare broncodeopslagplaatsen](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. Open in het ContosoAdsWeb-project het transformatiebestand *Web.Release.config* voor de toepassing *Web.config*, verwijder het opmerkingenblok met het `<connectionStrings>`-element en vervang deze door de volgende code.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Laat het bestand open, zodat u het kunt bewerken.
2. Klik in het linkerdeelvenster van [Azure Portal](https://portal.azure.com) op **SQL-databases**, klik daarna op de database die u voor deze zelfstudie hebt gemaakt en klik vervolgens op **Verbindingsreeksen weergeven**.

    ![Verbindingsreeksen weergeven](./media/cloud-services-dotnet-get-started/showcs.png)

    In de portal worden verbindingsreeksen weergegeven met een tijdelijke aanduiding voor het wachtwoord.

    ![Verbindingsreeksen](./media/cloud-services-dotnet-get-started/connstrings.png)
3. Verwijder `{connectionstring}` in het *Web.Release.config*-transformatiebestand en plak in plaats daarvan de ADO.NET-verbindingsreeks uit Azure Portal.
4. In de verbindingsreeks die u in het *Web.Release.config*-transformatiebestand hebt geplakt, vervangt u `{your_password_here}` door het wachtwoord dat u voor de nieuwe SQL-database hebt gemaakt.
5. Sla het bestand op.  
6. Selecteer en kopieer de verbindingsreeks (zonder de aanhalingstekens) voor gebruik in de volgende stappen, waarin de projectwerkrol wordt geconfigureerd.
7. Klik in **Solution Explorer** onder **Roles** in het cloudserviceproject met de rechtermuisknop op **ContosoAdsWorker**. Klik vervolgens op **Properties**.

    ![Scherm afbeelding die de menu optie Eigenschappen markeert.](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Klik op het tabblad **Settings**.
9. Wijzig **Service Configuration** in **Cloud**.
10. Selecteer het veld **Value** voor de `ContosoAdsDbConnectionString`-instelling en plak de verbindingsreeks die u hebt gekopieerd uit de vorige sectie van de zelfstudie.

     ![Databaseverbindingsreeks voor werkrol](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Sla uw wijzigingen op.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>De oplossing configureren voor het gebruik van uw Azure-opslagaccount als deze wordt uitgevoerd in Azure
Azure-opslagaccountverbindingsreeksen voor het webrolroject en het werkrolproject worden opgeslagen in de omgevingsinstellingen in het cloudserviceproject. Elk project heeft één reeks instellingen die moeten worden gebruikt wanneer de toepassing lokaal wordt uitgevoerd en een tweede reeks instellingen voor het uitvoeren van de toepassing in de cloud. U gaat de cloudomgevingsinstellingen voor zowel het webrolproject als het werkrolproject bijwerken.

1. Klik in **Solution Explorer** met de rechtermuisknop op **ContosoAdsWeb** (onder **Roles** in het **ContosoAdsCloudService**-project) en klik vervolgens op **Properties**.

    ![Roleigenschappen](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Klik op het tabblad **instellingen** . Kies **Cloud** in de vervolg keuzelijst **Service configuratie** .

    ![Cloudconfiguratie](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Selecteer de vermelding **StorageConnectionString**. Aan de rechterkant van de regel ziet u nu een knop met weglatingstekens (**...**). Klik op deze knop om het dialoogvenster **Create Storage Account Connection String** te openen.

    ![Het dialoogvenster Create Storage Connection String](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. In het dialoogvenster **Create Storage Connection String** klikt u op **Your subscription**, kiest u het opslagaccount dat u eerder hebt gemaakt en klikt u vervolgens op **OK**. Als u nog niet bent aangemeld, wordt u gevraagd om uw referenties voor uw Azure-account op te geven.

    ![Verbindingsreeks voor opslag maken](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Sla uw wijzigingen op.
6. Volg dezelfde procedure die u voor de `StorageConnectionString`-verbindingsreeks hebt gebruikt, om de `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-verbindingsreeks in te stellen.

    Deze verbindingsreeks wordt gebruikt voor logboekregistratie.
7. Volg de procedure die u hebt gebruikt voor de **ContosoAdsWeb**-rol, om beide verbindingsreeksen in te stellen voor de **ContosoAdsWorker**-rol. Vergeet niet om **Service Configuration** in te stellen op **Cloud**.

De rolomgevingsinstellingen die u met behulp van de Visual Studio-gebruikersinterface hebt geconfigureerd, worden opgeslagen in de volgende bestanden in het project ContosoAdsCloudService:

* *ServiceDefinition.csdef*: definieert de namen van de instellingen.
* *ServiceConfiguration.Cloud.cscfg*: levert waarden voor wanneer de app wordt uitgevoerd in de cloud.
* *ServiceConfiguration.Local.cscfg*: levert waarden voor wanneer de app lokaal wordt uitgevoerd.

ServiceDefinition.csdef omvat bijvoorbeeld de volgende definities:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

Het bestand *ServiceConfiguration.Cloud.cscfg* bevat de waarden die u in Visual Studio voor deze instellingen hebt ingevoerd.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

De `<Instances>`-instelling geeft het aantal virtuele machines aan waarop de werkrolcode door Azure wordt uitgevoerd. De sectie [Volgende stappen](#next-steps) bevat koppelingen naar meer informatie over het uitbreiden van een cloudservice,

### <a name="deploy-the-project-to-azure"></a>Het project implementeren in Azure
1. Klik in **Solution Explorer** met de rechtermuisknop op het **ContosoAdsCloudService**-cloudproject en selecteer vervolgens **Publish**.

   ![Het menu Publiceren](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. Klik tijdens de stap **Sign in** van de wizard **Publish Azure Application** op **Next**.

    ![Aanmeldingsstap](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. Klik in de stap **Settings** van de wizard op **Next**.

    ![De stap Settings](./media/cloud-services-dotnet-get-started/pubsettings.png)

    De standaardinstellingen op het tabblad **Advanced** zijn afdoende voor deze zelfstudie. Zie [De wizard Azure-toepassing publiceren](/visualstudio/azure/vs-azure-tools-publish-azure-application-wizard) voor meer informatie over het tabblad Advanced.
4. Klik in de stap **Summary** op **Publish**.

    ![De stap Summary](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Het venster **Azure Activity Log** wordt geopend in Visual Studio.
5. Klik op het pictogram met de pijl naar rechts om de implementatiedetails uit te vouwen.

    De implementatie kan 5 minuten of langer duren.

    ![Het venster Azure Activity Log](./media/cloud-services-dotnet-get-started/waal.png)
6. Wanneer de status van de implementatie 'complete' is, klikt u op de **Web-app-URL** om de toepassing te starten.
7. U kunt nu de app testen door advertenties te maken, weer te geven en te bewerken, zoals u deed toen de toepassing lokaal werd uitgevoerd.

> [!NOTE]
> Wanneer u klaar bent met testen, kunt u de cloudservice verwijderen of stoppen. Zelfs als u de cloudservice niet gebruikt, lopen de kosten door, omdat er op virtuele machines bronnen voor worden gereserveerd. Als u de cloudservice actief laat, kan bovendien iedereen die uw URL vindt, advertenties maken en weergeven. Ga in [Azure Portal](https://portal.azure.com) naar het tabblad **Overzicht** voor de cloudservice en klik vervolgens bovenaan de pagina op de knop **Verwijderen**. Wilt u alleen tijdelijk voorkomen dat anderen toegang hebben tot de site, klik dan op **Stoppen**. In dat geval blijven de kosten doorlopen. U kunt een vergelijkbare procedure volgen voor het verwijderen van de SQL-database en het opslagaccount wanneer u deze niet langer nodig hebt.
>
>

## <a name="create-the-application-from-scratch"></a>De toepassing vanaf het begin maken
Als u de [voltooide toepassing](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) nog niet hebt gedownload, doe dit dan nu. U kopieert de bestanden uit het gedownloade project naar het nieuwe project.

Het maken van de Contoso Ads-toepassing omvat de volgende stappen:

* Met Visual Studio een cloudserviceoplossing maken.
* NuGet-pakketten bijwerken en toevoegen.
* Projectverwijzingen instellen.
* Verbindingsreeksen configureren.
* Codebestanden toevoegen.

Nadat de oplossing is gemaakt, bekijkt u de code die uniek is voor cloudserviceprojecten en voor Azure-blobs en -wachtrijen.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Met Visual Studio een cloudserviceoplossing maken
1. Kies in Visual Studio in het menu **File** de optie **New Project**.
2. Vouw in het linkerdeelvenster van het dialoogvenster **New Project** de optie **Visual C#** uit en kies **Cloud**-sjablonen. Kies vervolgens de sjabloon voor **Azure Cloud Service**.
3. Geef het project en de oplossing de naam ContosoAdsCloudService en klik op **OK**.

    ![Nieuw project](./media/cloud-services-dotnet-get-started/newproject.png)
4. Voeg in het dialoogvenster **New Azure Cloud Service** een webrol en een werkrol toe. Geef de webrol de naam ContosoAdsWeb en de werkrol de naam ContosoAdsWorker. (Met het potloodpictogram in het rechterdeelvenster kunt u de namen van de rollen aanpassen.)

    ![Nieuw cloudserviceproject](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Wanneer u het dialoogvenster **New ASP.NET Project** voor de webrol ziet, kiest u de MVC-sjabloon en klikt u op **Change Authentication**.

    ![Verificatie wijzigen](./media/cloud-services-dotnet-get-started/chgauth.png)
6. Kies in het dialoogvenster **Change Authentication** de optie **No Authentication** en klik op **OK**.

    ![Geen verificatie](./media/cloud-services-dotnet-get-started/noauth.png)
7. Klik in het dialoogvenster **New ASP.NET Project** op **OK**.
8. Klik in **Solution Explorer** met de rechtermuisknop op de oplossing (niet op een van de projecten) en kies **Add - New Project**.
9. Kies in het dialoogvenster **Add New Project** de optie **Windows** (onder **Visual C#** in het linkerdeelvenster) en klik op de sjabloon **Class Library**.  
10. Geef het project de naam *ContosoAdsCommon* en klik vervolgens op **OK**.

    Zowel vanuit het webrolproject als vanuit het werkrolproject moet u verwijzen naar de Entity Framework- context en het bijbehorende gegevensmodel. Als alternatief kunt u de aan Entity Framework gerelateerde klassen definiëren in het webrolproject en hier vanuit het werkrolproject naar verwijzen. Gebruikt u deze alternatieve methode, dan bevat uw werkrolproject echter een verwijzing naar webassembly's die niet nodig zijn.

### <a name="update-and-add-nuget-packages"></a>NuGet-pakketten bijwerken en toevoegen
1. Open het dialoogvenster **Manage NuGet Packages** voor de oplossing.
2. Selecteer boven aan het venster de optie **Updates**.
3. Zoek het *WindowsAzure.Storage*-pakket op en selecteer dit als het in de lijst staat. Selecteer vervolgens de web- en werkprojecten die u wilt bijwerken. Klik vervolgens op **Update**.

    De opslagclientbibliotheek wordt vaker bijgewerkt dan de Visual Studio-projectsjablonen, zodat u vaak zult zien dat de versie in een nieuw gemaakt project moet worden bijgewerkt.
4. Selecteer boven aan het venster de optie **Browse**.
5. Zoek het NuGet pakket *EntityFramework* op en installeer het in alle drie de projecten.
6. Zoek het NuGet-pakket *Microsoft.WindowsAzure.ConfigurationManager* op en installeer het in het werkrolproject.

### <a name="set-project-references"></a>Projectverwijzingen instellen
1. Stel in het project ContosoAdsWeb een verwijzing in naar het project ContosoAdsCommon. Klik met de rechter muisknop op het project ContosoAdsWeb en klik vervolgens op **verwijzingen**  -  **add References**. Selecteer in het dialoogvenster **Reference Manager** de optie **Solution – Projects** (in het linkerdeelvenster). Selecteer vervolgens **ContosoAdsCommon** en klik op **OK**.
2. Stel in het project ContosoAdsWorker een verwijzing in naar het project ContosoAdsCommon.

    ContosoAdsCommon bevat de Entity Framework-contextklasse en het bijbehorende gegevensmodel die door zowel de front-end als de back-end worden gebruikt.
3. Stel in het project ContosoAdsWorker een verwijzing in naar `System.Drawing`.

    Deze assembly wordt gebruikt door de back-end om afbeeldingen te converteren naar miniatuurweergaven.

### <a name="configure-connection-strings"></a>Verbindingsreeksen configureren
In deze sectie configureert u Azure Storage- en SQL-verbindingsreeksen om lokaal te testen. In de implementatie-instructies eerder in deze handleiding wordt uitgelegd hoe u verbindingsreeksen instelt wanneer de app wordt uitgevoerd in de cloud.

1. Open in het project ContosoAdsWeb het bestand Web.config en voeg na het `configSections`-element het volgende `connectionStrings`-element toe.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Als u Visual Studio 2015 of hoger gebruikt, vervangt u 'v11.0' door 'MSSQLLocalDB'.
2. Sla uw wijzigingen op.
3. Klik in het project ContosoAdsCloudService met de rechtermuisknop op ContosoAdsWeb (onder **Roles**) en klik vervolgens op **Properties**.

    ![Scherm opname van de menu optie Eigenschappen onder rollen.](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. Klik in het eigenschappenvenster van **ContosoAdsCommon [rol]** op het tabblad **Settings** en vervolgens op **Add Setting**.

    Laat **Service Configuration** ingesteld op **All Configurations**.
5. Voeg een instelling toe met de naam *StorageConnectionString*. Stel **Type** in op *ConnectionString*. Stel **Value** in op *UseDevelopmentStorage=true*.

    ![Nieuwe verbindingsreeks](./media/cloud-services-dotnet-get-started/scall.png)
6. Sla uw wijzigingen op.
7. Volg dezelfde procedure om een verbindingsreeks voor opslag toe te voegen aan de eigenschappen van de ContosoAdsWorker-rol.
8. Terwijl u zich nog in het eigenschappenvenster **ContosoAdsWorker [rol]** bevindt, voegt u nog een verbindingsreeks toe:

   * Name: ContosoAdsDbConnectionString
   * Type: String
   * Value: plak hier dezelfde verbindingsreeks die u voor het webrolproject hebt gebruikt. (Het volgende voorbeeld is voor Visual Studio 2013. Als u Visual Studio 2015 of hoger gebruikt en dit voorbeeld kopieert, vergeet dan niet om de gegevensbron te wijzigen.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Codebestanden toevoegen
In deze sectie kopieert u codebestanden vanuit de gedownloade oplossing naar de nieuwe oplossing. In de volgende secties worden belangrijke onderdelen van deze code weergegeven en uitgelegd.

Als u bestanden wilt toevoegen aan een project of een map, klikt u met de rechter muisknop op het project of de map en klikt u op  -  **bestaand item** toevoegen. Selecteer de gewenste bestanden en klik op **Add**. Als u wordt gevraagd of u de bestaande bestanden wilt vervangen, klikt u op **Yes**.

1. Verwijder in het project ContosoAdsCommon het bestand *Class1.cs* en voeg in plaats hiervan de bestanden *Ad.cs* en *ContosoAdscontext.cs* uit het gedownloade project toe.
2. Voeg in het project ContosoAdsWeb de volgende bestanden uit het gedownloade project toe.

   * *Global.asax.cs*.  
   * In de map *Views\Shared*: *\_Layout.cshtml*.
   * In de map *Views\Home*: *Index.cshtml*.
   * In de map *Controllers*: *AdController.cs*.
   * In de map *Views\Ad* (maak eerst de map): vijf *.cshtml*-bestanden.
3. Voeg in het project ContosoAdsWorker *WorkerRole.cs* toe uit het gedownloade project.

U kunt de toepassing nu ontwikkelen en uitvoeren zoals eerder in de zelfstudie is uitgelegd. De app zal gebruikmaken van lokale database- en opslagemulatorresources.

De volgende secties geven uitleg over de code voor het werken met de Azure-omgeving, -blobs en -wachtrijen. In deze zelfstudie wordt niet ingegaan op het maken van MVC-controllers en -views met behulp van scaffolding, het schrijven van code voor Entity Framework die geschikt is voor SQL Server-databases en de basisbeginselen van asynchrone programmering in ASP.NET 4.5. Raadpleeg de volgende informatiebronnen voor meer informatie over deze onderwerpen:

* [Aan de slag met MVC 5](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Aan de slag met EF 6 en MVC 5](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Inleiding tot asynchrone programmering in .NET 4.5](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs
Het bestand Ad.cs definieert een enum voor advertentiecategorieën en een POCO-entiteitsklasse voor advertentie-informatie.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs
De klasse ContosoAdsContext geeft aan dat de Ad-klasse wordt gebruikt in een DbSet-verzameling, die door Entity Framework wordt opgeslagen in een SQL-database.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

De klasse heeft twee constructors. De eerste hiervan wordt gebruikt door het webproject en specificeert de naam van een verbindingsreeks die is opgeslagen in het bestand Web.config. Met de tweede constructor kunt u items doorgeven in de werkelijke verbindingsreeks die wordt gebruikt door het werkrolproject, omdat deze geen Web.config-bestand heeft. U weet al waar deze verbindingsreeks is opgeslagen. Later ziet u hoe de code de verbindingsreeks ophaalt bij het instantiëren van de DbContext-klasse.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs
Code die wordt aangeroepen vanuit de `Application_Start`-methode maakt een blobcontainer met *afbeeldingen* en een wachtrij met *afbeeldingen* als deze nog niet bestaan. Wanneer u begint met het gebruik van een nieuw opslagaccount of de opslagemulator op een andere computer gaat gebruiken, zorgt dit ervoor dat de vereiste blobcontainer en wachtrij automatisch worden gemaakt.

De code krijgt toegang tot het opslagaccount door gebruik te maken van de opslagverbindingsreeks uit het *.cscfg*-bestand.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Vervolgens haalt de code een verwijzing op naar de blobcontainer met *afbeeldingen*, maakt deze de container als deze nog niet bestaat en stelt deze toegangsrechten voor de nieuwe container in. Standaard staan nieuwe containers alleen clients toe met opslagaccountreferenties voor toegang tot blobs. Voor de website moeten de blobs openbaar zijn, zodat deze afbeeldingen kan weergeven met behulp van URL's die naar de afbeeldingsblobs wijzen.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Met vergelijkbare code wordt er een verwijzing naar de *afbeeldingen* wachtrij opgehaald en een nieuwe wachtrij gemaakt. In dit geval is er geen machtigingswijziging nodig.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---_layoutcshtml"></a>ContosoAdsWeb - \_Layout.cshtml
Het bestand *_Layout.cshtml* stelt de naam van de app in de kop- en voettekst in en maakt een menu-item 'Ads'.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Views\Home\Index.cshtml
Het bestand *Views\Home\Index.cshtml* geeft categoriekoppelingen weer op de startpagina. De koppelingen geven de integerwaarde van de `Category`-enum door in een queryreeksvariabele naar de pagina Ads Index.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs
De constructor roept in het *AdController.cs* -bestand de `InitializeStorage` methode aan om Azure Storage-client bibliotheek objecten te maken die een API bieden voor het werken met blobs en wacht rijen.

Vervolgens haalt de code een verwijzing op naar de blobcontainer met *afbeeldingen*, zoals u eerder hebt gezien in *Global.asax.cs*. Tijdens het uitvoeren hiervan wordt standaard [beleid voor opnieuw proberen](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) ingesteld dat geschikt is voor een web-app. Het standaard beleid voor opnieuw proberen van exponentiële uitstel kan ertoe leiden dat de web-app langer dan een minuut niet meer reageert bij herhaalde pogingen voor een tijdelijke fout. Het beleid dat hier is opgegeven, schrijft voor dat er na elke poging drie seconden wordt gewacht en dat het aantal pogingen maximaal drie bedraagt.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Door vergelijkbare code wordt een verwijzing naar de *afbeeldingen* wachtrij opgehaald.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Het grootste deel van de controllercode is typerend voor het werken met een Entity Framework-gegevensmodel met behulp van een DbContext-klasse. Een uitzondering is de HttpPost `Create`-methode, waarmee een bestand wordt geüpload en opgeslagen in blobopslag. De modelbinder levert een [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase)-object aan de methode.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Als de gebruiker een bestand heeft geselecteerd om te uploaden, wordt het door de code geüpload en opgeslagen in een blob. Tegelijkertijd wordt het Ad-databaserecord bijgewerkt met een URL die naar de blob verwijst.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

De code die het uploaden uitvoert, bevindt zich in de `UploadAndSaveBlobAsync`-methode. Deze maakt een GUID-naam voor de blob, uploadt het bestand en slaat dit op, en retourneert een verwijzing naar de opgeslagen blob.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Nadat de HttpPost `Create`-methode een blob heeft geüpload en de database heeft bijgewerkt, maakt deze een wachtrijbericht om het betreffende back-endproces te laten weten dat er een afbeelding gereed is voor conversie naar een miniatuur.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

De code voor de HttpPost `Edit`-methode is vergelijkbaar, met dit verschil dat als de gebruiker kiest voor een nieuw afbeeldingsbestand, alle bestaande blobs moeten worden verwijderd.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

Het volgende voorbeeld toont de code voor het verwijderen van blobs wanneer u een advertentie verwijdert.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml en Details.cshtml
Het bestand *Index.cshtml* geeft de miniaturen en de overige advertentiegegevens weer.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

Het bestand *Details.cshtml* geeft de afbeelding op volledige grootte weer.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml en Edit.cshtml
De bestanden *Create.cshtml* en *Edit.cshtml* specificeren formuliercodering waarmee de controller het `HttpPostedFileBase`-object kan ophalen.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Een `<input>`-element vertelt de browser dat deze een dialoogvenster voor bestandsselectie moet aanleveren.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker - WorkerRole.cs - OnStart-methode
De Azure werkrolomgeving roept de `OnStart`-methode aan in de `WorkerRole`-klasse wanneer de werkrol begint en roept de `Run`-methode aan wanneer de `OnStart`-methode is voltooid.

De `OnStart`-methode haalt de databaseverbindingsreeks op uit het bestand *.cscfg* en geeft deze door aan de Entity Framework DbContext-klasse. Er hoeft geen provider te worden opgegeven; standaard wordt de SQLClient-provider gebruikt.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Hierna haalt de methode een verwijzing op naar het opslagaccount en maakt deze de blobcontainer en de wachtrij als deze nog niet bestaan. De code hiervoor is vergelijkbaar met wat u al hebt gezien in de `Application_Start`-methode voor de webrol.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker - WorkerRole.cs - Run-methode
De `Run`-methode wordt aangeroepen wanneer het initialisatiewerk van de `OnStart`-methode is voltooid. De methode voert een oneindige lus uit die controleert op nieuwe berichten in de wachtrij en verwerkt deze wanneer ze binnenkomen.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Na elke herhaling van de lus wordt als er geen wachtrij bericht is gevonden de inactieve modus ingeschakeld gedurende een seconde. Zo wordt voorkomen dat de werkrol buitensporig veel CPU-tijd en opslagtransactiekosten verbruikt. Het Microsoft Customer Advisory Team kent het verhaal van een ontwikkelaar die was vergeten dit element op te nemen voordat hij de app live zette en op vakantie ging. Wanneer ze terugkomen, kost het toezicht meer dan de vakantie.

Soms veroorzaakt de inhoud van een wachtrijbericht een fout in de verwerking. We spreken dan van een *verontreinigd bericht*. Als u alleen een fout in het logboek hebt geregistreerd en de lus opnieuw hebt gestart, kan dit leiden tot eindeloze pogingen om het bericht te verwerken.  Daarom bevat het catch-blok een if-instructie waarmee wordt gecontroleerd hoe vaak de app heeft geprobeerd het huidige bericht te verwerken. Bij meer dan 5 pogingen wordt het bericht uit de wachtrij verwijderd.

`ProcessQueueMessage` wordt aangeroepen wanneer er een wachtrijbericht is gevonden.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Deze code leest de database om de afbeeldings-URL op te halen, converteert de afbeelding naar een miniatuur, slaat de miniatuur op in een blob, werkt de database bij met de blob-URL van de miniatuur en verwijdert het bericht uit de wachtrij.

> [!NOTE]
> Uit oogpunt van eenvoud maakt de code in de `ConvertImageToThumbnailJPG`-methode gebruik van klassen in de System.Drawing-naamruimte. De klassen in deze naamruimte zijn echter bedoeld voor gebruik met Windows Forms. Ze worden niet ondersteund voor gebruik in een Windows- of ASP.NET-service. Zie [Afbeeldingen dynamisch genereren](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) en [Alles over het wijzigen van het formaat van afbeeldingen](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na) voor meer informatie over opties voor de verwerking van afbeeldingen.
>
>

## <a name="troubleshooting"></a>Problemen oplossen
Voor het geval u tegen problemen aanloopt terwijl u de instructies in deze zelfstudie volgt, vindt u hier een aantal veelvoorkomende fouten en aanwijzingen om deze op te lossen.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Het `RoleEnvironment` object wordt door Azure verschaft wanneer u een toepassing in azure uitvoert of wanneer u lokaal uitvoert met behulp van de Azure Compute-emulator.  Als deze fout bij lokale uitvoering optreedt, controleert u of het project ContosoAdsCloudService is ingesteld als opstartproject. Hiermee stelt u in dat het project moet worden uitgevoerd met behulp van de Azure Compute-emulator.

De toepassing gebruikt de Azure RoleEnvironment onder meer voor het ophalen van verbindingsreekswaarden die zijn opgeslagen in de *.cscfg*-bestanden. Een andere oorzaak van deze uitzondering kan dan ook te maken hebben met een ontbrekende verbindingsreeks. Controleer of u de instelling StorageConnectionString in het project ContosoAdsWeb voor zowel de cloudconfiguratie als de lokale configuratie hebt gemaakt en of u ook in het project ContosoAdsWorker beide verbindingsreeksen voor beide configuraties hebt gemaakt. Als u in de hele oplossing een zoekopdracht **Find All** uitvoert voor StorageConnectionString, moet u deze in 6 bestanden 9 maal vinden.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Kan niet overschrijven naar poort xxx. Nieuwe poort onder minimaal toegestane waarde 8080 voor http-protocol
Wijzig het poortnummer dat door het webproject wordt gebruikt. Klik met de rechtermuisknop op het project ContosoAdsWeb en klik vervolgens op **Properties**. Klik op het tabblad **Web** en wijzig het poortnummer in de instelling **Project Url**.

Raadpleeg de volgende sectie voor een alternatieve oplossing voor dit probleem.

### <a name="other-errors-when-running-locally"></a>Andere fouten bij lokale uitvoering
Nieuwe Cloud service projecten gebruiken standaard de Azure Compute emulator Express om de Azure-omgeving te simuleren. Dit is een basisversie van de volledige rekenemulator. Onder bepaalde omstandigheden werkt de volledige emulator wel, maar de expresversie niet.  

Als u het project wilt wijzigen voor gebruik van de volledige emulator, klikt u met de rechtermuisknop op het project ContosoAdsCloudService en klikt u vervolgens op **Properties**. Klik in het venster **Properties** op het tabblad **Web** en schakel vervolgens het keuzerondje **Use Full Emulator** in.

Als u de toepassing wilt uitvoeren met de volledige emulator, moet u Visual Studio openen met beheerdersrechten.

## <a name="next-steps"></a>Volgende stappen
In het kader van deze 'Aan de slag'-zelfstudie is de Contoso Ads-toepassing met opzet eenvoudig gehouden. De toepassing gebruikt bijvoorbeeld geen [afhankelijkheidsinjectie](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection), geen [patronen voor opslagplaatsen en werkeenheden](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), geen [interface voor logboekregistratie](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), geen [EF Code First-migraties](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) voor het beheren van wijzigingen in het gegevensmodel, geen [EF-verbindingstolerantie](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) voor het beheren van tijdelijke netwerkfouten, enzovoort.

Hier volgen enkele voorbeelden, in oplopende volgorde van complexiteit, van cloudservicetoepassingen waarin meer code wordt gebruikt:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Qua concept soortgelijk aan Contoso Ads, maar met meer functies en meer code.
* [Azure Cloud Service-toepassing met meerdere lagen die opslagtabellen, wachtrijen en blobs bevat](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Introduceert Azure Storage-tabellen, evenals blobs en wachtrijen. Deze toepassing is gebaseerd op een oudere versie van de Azure SDK voor .NET. Voor gebruik met de huidige versie is een aantal wijzigingen nodig.

Zie [Echte cloud-apps ontwikkelen met Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction) voor algemene informatie over ontwikkelen voor de cloud.

Zie [Microsoft Azure Storage - nieuwe functies, aanbevolen procedures en patronen](https://channel9.msdn.com/Events/Build/2014/3-628) voor een video-inleiding in aanbevolen procedures en patronen voor Azure Storage.

Zie de volgende resources voor meer informatie:

* [Deel 1 Azure Cloud Services: Inleiding](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Cloud Services beheren](cloud-services-how-to-manage-portal.md)
* [Azure Storage](../storage/index.yml)
* [Een cloudserviceprovider kiezen](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
