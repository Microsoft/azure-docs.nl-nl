---
title: Gegevens verplaatsen tussen uitgeschaalde clouddatabases
description: Hierin wordt uitgelegd hoe u Shards kunt manipuleren en gegevens kunt verplaatsen via een zelf-hostende service via Elastic data base-Api's.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793463"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Gegevens verplaatsen tussen uitgeschaalde clouddatabases
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Als u software als een service ontwikkelaar bent en plotseling uw app een enorme vraag maakt, moet u de groei in de hand brengen. U voegt nu meer data bases toe (Shards). Hoe distribueert u de gegevens opnieuw naar de nieuwe data bases zonder de gegevens integriteit te onderbreken? Gebruik het **hulp programma voor splitsen en samen voegen** om gegevens van beperkte data bases te verplaatsen naar de nieuwe data bases.  

Het hulp programma voor splitsen en samen voegen wordt uitgevoerd als een Azure-webservice. Een beheerder of ontwikkelaar gebruikt het hulp programma om shardlets (gegevens van een Shard) te verplaatsen tussen verschillende data bases (Shards). Het hulp programma maakt gebruik van het Shard-toewijzings beheer voor het onderhouden van de meta gegevens database van de service en zorgt voor consistente toewijzingen.

![Overzicht][1]

## <a name="download"></a>Downloaden

[Micro soft. Azure. SqlDatabase. ElasticScale. service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentatie

1. [Zelf studie voor splitsen en samen voegen met Elastic data base](elastic-scale-configure-deploy-split-and-merge.md)
2. [Beveiligings configuratie splitsen en samen voegen](elastic-scale-split-merge-security-configuration.md)
3. [Beveiligings overwegingen voor splitsen en samen voegen](elastic-scale-split-merge-security-configuration.md)
4. [Shard-toewijzingsbeheer](elastic-scale-shard-map-management.md)
5. [Bestaande databases migreren voor uitschaling](elastic-convert-to-use-elastic-tools.md)
6. [Hulpprogram ma's voor Elastic data base](elastic-scale-introduction.md)
7. [Woordenlijst voor hulpprogramma's voor Elastic Database](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Waarom zou u het hulp programma voor splitsen en samen voegen gebruiken?

- **Flexibiliteit**

  Toepassingen moeten flexibeler worden uitgerekt dan de limieten van één data base in Azure SQL Database. Gebruik het hulp programma om gegevens naar nieuwe data bases te verplaatsen en behoud de integriteit.

- **Splitsen tot toename**

  Als u de totale capaciteit wilt verg Roten om explosieve groei te verwerken, maakt u extra capaciteit door de gegevens te sharding en door deze te distribueren over incrementele data bases totdat aan de capaciteits behoeften is voldaan. Dit is een voor beeld van de functie **splitsen** .

- **Samen voegen tot verkleinen**

  De capaciteits behoeften worden verkleind als gevolg van de seizoensgebonden aard van een bedrijf. Met het hulp programma kunt u omlaag schalen naar minder schaal eenheden wanneer het bedrijf langzaam verloopt. De functie merge in de Elastic Scale Split-Merge-service behandelt deze vereiste.

- **HOTS pots beheren door shardlets te verplaatsen**

  Met meerdere tenants per data base kan de toewijzing van shardlets aan Shards leiden tot capaciteits knelpunten op sommige Shards. Hiervoor moet u shardlets opnieuw toewijzen of shardlets verplaatsen naar een nieuwe of minder gebruikte Shards.

## <a name="concepts--key-features"></a>Concepten & belang rijke functies

- **Door de klant gehoste services**

  De Split-Merge wordt geleverd als een door de klant gehoste service. U moet de service implementeren en hosten in uw Microsoft Azure-abonnement. Het pakket dat u downloadt van NuGet bevat een configuratie sjabloon die u kunt volt ooien met de informatie voor uw specifieke implementatie. Raadpleeg de [zelf studie voor splitsen en samen voegen](elastic-scale-configure-deploy-split-and-merge.md) voor meer informatie. Omdat de service wordt uitgevoerd in uw Azure-abonnement, kunt u de meeste beveiligings aspecten van de service beheren en configureren. De standaard sjabloon bevat de opties voor het configureren van TLS, client authenticatie op basis van certificaten, versleuteling voor opgeslagen referenties, DoS-beveiliging en IP-beperkingen. Meer informatie over de beveiligings aspecten vindt u in de volgende [beveiligings configuratie voor het samen voegen](elastic-scale-split-merge-security-configuration.md)van documenten.

  De standaard geïmplementeerde service wordt uitgevoerd met één werk nemer en één webrole. Elk maakt gebruik van de a1 VM-grootte in azure Cloud Services. Hoewel u deze instellingen niet kunt wijzigen wanneer u het pakket implementeert, kunt u deze wijzigen na een geslaagde implementatie in de actieve Cloud service, (via de Azure Portal). Houd er rekening mee dat de rol van werk nemers om technische redenen niet voor meer dan één instantie mag worden geconfigureerd.

- **Integratie van Shard-toewijzing**

  De service voor splitsen en samen voegen communiceert met de Shard-kaart van de toepassing. Wanneer u de service voor splitsen en samen voegen gebruikt om bereiken te splitsen of samen te voegen of om shardlets tussen Shards te verplaatsen, blijft de toewijzing van de Shard automatisch bijgewerkt met de service. Als u dit wilt doen, maakt de service verbinding met de Shard-kaart beheer database van de toepassing en worden de bereiken en toewijzingen onderhouden als de voortgang van splitsen/samen voegen/verplaatsen van aanvragen. Zo zorgt u ervoor dat de Shard-kaart altijd een actuele weer gave biedt wanneer Split-Merge-bewerkingen worden uitgevoerd. Splitsen, samen voegen en shardlet verplaatsings bewerkingen worden geïmplementeerd door het verplaatsen van een batch-shardlets van de bron Shard naar het doel Shard. Tijdens de shardlet-verplaatsings bewerking wordt de shardlets onderhevig aan de huidige batch gemarkeerd als offline in de Shard-toewijzing en zijn deze niet beschikbaar voor gegevens afhankelijke routerings verbindingen met behulp van de **OpenConnectionForKey** -API.

- **Consistente shardlet-verbindingen**

  Wanneer het verplaatsen van gegevens wordt gestart voor een nieuwe batch met shardlets, worden door een Shard toegewezen gegevensgebonden routerings verbindingen naar de Shard die de shardlet opslaat, beëindigd en worden de volgende verbindingen van de Shard-toewijzings-Api's naar de shardlets geblokkeerd terwijl de gegevens verplaatsing wordt uitgevoerd om inconsistenties te voor komen. Verbindingen met andere shardlets in dezelfde Shard worden ook afgebroken, maar worden direct opnieuw uitgevoerd bij een nieuwe poging. Zodra de batch is verplaatst, worden de shardlets weer online gemarkeerd voor de doel-Shard en worden de bron gegevens verwijderd uit de bron Shard. De service doorloopt deze stappen voor elke batch totdat alle shardlets zijn verplaatst. Dit leidt tot verschillende beëindigings bewerkingen voor verbindingen tijdens de volledige bewerking splitsen/samen voegen/verplaatsen.  

- **Beschik baarheid van shardlet beheren**

  Het beperken van de verbinding met de huidige batch van shardlets zoals hierboven wordt beschreven, beperkt het bereik van niet-beschik baarheid tot één batch met shardlets per keer. Dit heeft de voor keur boven een aanpak waarbij de volledige Shard tijdens de uitvoering van een split-of samenvoeg bewerking voor alle shardlets offline blijft. De grootte van een batch, gedefinieerd als het aantal afzonderlijke shardlets dat per keer moet worden verplaatst, is een configuratie parameter. Het kan worden gedefinieerd voor elke Split-en samenvoeg bewerking, afhankelijk van de beschik baarheid en prestatie behoeften van de toepassing. Houd er rekening mee dat het bereik dat in de Shard-toewijzing wordt vergrendeld, groter mag zijn dan de opgegeven Batch grootte. Dit komt doordat de bereik grootte door de service wordt gekozen, zodat het werkelijke aantal sharding sleutel waarden in de gegevens ongeveer overeenkomt met de Batch grootte. Dit is belang rijk om vooral te onthouden voor sharding-sleutels met sparse vulling.

- **Opslag van meta gegevens**

  De service voor splitsen en samen voegen maakt gebruik van een Data Base om de status te behouden en om logboeken te bewaren tijdens de verwerking van aanvragen. De gebruiker maakt deze data base in het bijbehorende abonnement en biedt de connection string voor het in het configuratie bestand voor de service-implementatie. Beheerders van de organisatie van de gebruiker kunnen ook verbinding maken met deze data base om de voortgang van aanvragen te controleren en gedetailleerde informatie over mogelijke storingen te onderzoeken.

- **Sharding-bewustzijn**

  De service voor splitsen en samen voegen onderscheidt van (1) Shard tabellen, (2) referentie tabellen en (3) normale tabellen. De semantiek van een splitsing/samen voeging/verplaatsings bewerking is afhankelijk van het type tabel dat wordt gebruikt en worden als volgt gedefinieerd:

  - **Shard-tabellen**

    Met splitsen, samen voegen en verplaatsen worden shardlets verplaatst van bron naar doel-Shard. Nadat de algemene aanvraag is voltooid, zijn deze shardlets niet meer aanwezig op de bron. Houd er rekening mee dat de doel tabellen moeten bestaan op de doel-Shard en mag geen gegevens bevatten in het doel bereik voordat de bewerking wordt verwerkt.

  - **Verwijzings tabellen**

    Voor referentie tabellen kopieert de bewerking splitsen, samen voegen en verplaatsen de gegevens van de bron naar de doel-Shard. Houd er echter rekening mee dat er geen wijzigingen worden aangebracht op de doel-Shard voor een bepaalde tabel als een rij al aanwezig is in deze tabel op het doel. De tabel moet leeg zijn voor een Kopieer bewerking van de verwijzings tabel om te kunnen worden verwerkt.

  - **Andere tabellen**

    Er kunnen andere tabellen aanwezig zijn op de bron of het doel van een split-en samenvoeg bewerking. De Split-Merge-service negeert deze tabellen voor het verplaatsen of kopiëren van gegevens. Houd er echter rekening mee dat ze deze bewerkingen in geval van beperkingen kunnen verstoren.

    De informatie over verwijzing tegenover Shard-tabellen wordt verstrekt door de `SchemaInfo` api's op de Shard-kaart. In het volgende voor beeld ziet u hoe u deze Api's kunt gebruiken op een bepaald Shard Manager-object:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    De tabellen ' regio ' en ' land ' worden gedefinieerd als referentie tabellen en worden gekopieerd met splitsen/samen voegen/verplaatsen. klant en orders worden op zijn beurt gedefinieerd als Shard-tabellen. `C_CUSTKEY` en `O_CUSTKEY` dienen als de sharding-sleutel.

- **Referentiële integriteit**

  De Split-Merge-service analyseert afhankelijkheden tussen tabellen en maakt gebruik van refererende sleutel-primaire sleutel relaties om de bewerkingen voor het verplaatsen van verwijzings tabellen en shardlets te faseren. In het algemeen worden verwijzings tabellen eerst in de afhankelijkheids volgorde gekopieerd en vervolgens worden shardlets gekopieerd in volg orde van hun afhankelijkheden binnen elke batch. Dit is nodig zodat FK-PK-beperkingen voor de doel-Shard worden gehonoreerd als de nieuwe gegevens binnenkomen.

- **Consistentie van Shard en uiteindelijke voltooiing**

  Als er fouten optreden, hervat de Split-Merge-service bewerkingen na storingen en wordt beoogd om aanvragen te volt ooien. Er kunnen echter onherstelbare situaties zijn, bijvoorbeeld wanneer de doel-Shard verloren is gegaan of niet langer dan het herstel is aangetast. Onder deze omstandigheden kunnen bepaalde shardlets die moeten worden verplaatst, zich blijven bevinden op de bron-Shard. De service zorgt ervoor dat shardlet-toewijzingen alleen worden bijgewerkt nadat de benodigde gegevens zijn gekopieerd naar het doel. Shardlets worden alleen verwijderd uit de bron wanneer alle gegevens zijn gekopieerd naar het doel en de bijbehorende toewijzingen zijn bijgewerkt. De verwijderings bewerking vindt plaats op de achtergrond terwijl het bereik al online is op de doel-Shard. De Split-Merge-service garandeert altijd de juistheid van de toewijzingen die zijn opgeslagen in de Shard-kaart.

## <a name="the-split-merge-user-interface"></a>De gebruikers interface voor splitsen en samen voegen

Het service pakket voor splitsen en samen voegen bevat een werk rollen en een webrol. De webrol wordt gebruikt om gesplitste samenvoeg aanvragen op een interactieve manier te verzenden. De belangrijkste onderdelen van de gebruikers interface zijn als volgt:

- **Het type bewerking**

  Het bewerkings type is een keuze rondje dat bepaalt welk soort bewerking door de service voor deze aanvraag wordt uitgevoerd. U kunt kiezen tussen de scenario's voor splitsen, samen voegen en verplaatsen. U kunt ook een eerder ingediende bewerking annuleren. U kunt split-, samenvoeg-en Move-aanvragen gebruiken voor bereik Shard-kaarten. List Shard Maps alleen ondersteuning voor het verplaatsen van bewerkingen.

- **Shard-kaart**

  De volgende sectie van aanvraag parameters bevat informatie over de Shard-kaart en de data base die als host fungeert voor uw Shard-kaart. In het bijzonder moet u de naam opgeven van de server en de data base die als host fungeert voor de shardmap, referenties om verbinding te maken met de Shard-toewijzings database, ten slotte de naam van de Shard-kaart. Op dit moment wordt met de bewerking slechts één set referenties geaccepteerd. Deze referenties moeten voldoende machtigingen hebben om wijzigingen in de Shard-kaart en de gebruikers gegevens op de Shards uit te voeren.

- **Bron bereik (splitsen en samen voegen)**

  Met een bewerking voor splitsen en samen voegen wordt een bereik verwerkt met de lage en hoge sleutel. Als u een bewerking met een niet-gebonden hoge sleutel waarde wilt opgeven, schakelt u het selectie vakje hoge sleutel is Max in en laat u het veld hoge sleutel leeg. De bereik sleutel waarden die u opgeeft, hoeven niet exact overeen te komen met een toewijzing en de grenzen daarvan in uw Shard-kaart. Als u geen bereik grenzen opgeeft bij alle services, wordt het dichtstbijzijnde bereik automatisch afleiden. U kunt het Power shell-script GetMappings.ps1 gebruiken om de huidige toewijzingen in een bepaalde Shard-toewijzing op te halen.

- **Gesplitste bron gedrag (splitsen)**

  Voor Split-bewerkingen definieert u het punt om het bron bereik te splitsen. U doet dit door de sharding-sleutel op te geven waar u de splitsing wilt laten plaatsvinden. Gebruik het keuze rondje om op te geven of u het onderste deel van het bereik (met uitzonde ring van de Splits toets) wilt verplaatsen, of dat het bovenste deel moet worden verplaatst (inclusief de Splits toets).

- **Bron-shardlet (verplaatsen)**

  Verplaats bewerkingen verschillen van Split-of samenvoeg bewerkingen, omdat er geen bereik is vereist om de bron te beschrijven. Een bron voor verplaatsing wordt eenvoudigweg geïdentificeerd door de sharding-sleutel waarde die u wilt verplaatsen.

- **Doel-Shard (splitsen)**

  Zodra u de gegevens op de bron van de gesplitste bewerking hebt opgegeven, moet u opgeven waar u de gegevens wilt kopiëren door de server-en database naam voor het doel op te geven.

- **Doel bereik (samen voegen)**

  Met samenvoeg bewerkingen wordt shardlets verplaatst naar een bestaande Shard. U identificeert de bestaande Shard door de bereik grenzen te leveren van het bestaande bereik dat u wilt samen voegen.

- **Batch grootte**

  De Batch grootte bepaalt het aantal shardlets dat offline gaat tijdens het verplaatsen van gegevens. Dit is een geheel getal waar u kleinere waarden kunt gebruiken wanneer u gevoelig bent voor lange tijd uitval voor shardlets. Grotere waarden verhogen de tijd dat een bepaalde shardlet offline is, maar kan de prestaties verbeteren.

- **Bewerkings-ID (annuleren)**

  Als u een doorlopende bewerking hebt die niet meer nodig is, kunt u de bewerking annuleren door de bewerkings-ID in dit veld op te geven. U kunt de bewerkings-ID ophalen uit de tabel status van aanvraag (zie sectie 8,1) of vanuit de uitvoer in de webbrowser waar u de aanvraag hebt ingediend.

## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

De huidige implementatie van de service Split-Merge is onderhevig aan de volgende vereisten en beperkingen:

- De Shards moet bestaan en zijn geregistreerd in de Shard-toewijzing voordat een Split-Merge-bewerking op deze Shards kan worden uitgevoerd.
- De service maakt geen tabellen of andere database objecten automatisch als onderdeel van de bewerkingen. Dit betekent dat het schema voor alle Shard-tabellen en-referentie tabellen moet bestaan op de doel-Shard vóór een split/samen voeging/verplaatsing-bewerking. Shard-tabellen met name moeten leeg zijn in het bereik waar nieuwe shardlets moeten worden toegevoegd door een split/samen voeging/verplaatsings bewerking. Anders mislukt de eerste consistentie controle op de doel-Shard. Houd er ook rekening mee dat referentie gegevens alleen worden gekopieerd als de verwijzings tabel leeg is en dat er geen consistentie garanties zijn ten aanzien van andere gelijktijdige schrijf bewerkingen op de verwijzings tabellen. We raden dit aan: wanneer u splitsen/samen voegen uitvoert, worden er geen andere schrijf bewerkingen meer aangebracht in de verwijzings tabellen.
- De service is afhankelijk van de rij-identiteit die is ingesteld door een unieke index of sleutel die de sharding-sleutel bevat om de prestaties en betrouw baarheid voor grote shardlets te verbeteren. Hierdoor kan de service gegevens verplaatsen met een nauw keurigere granulatie dan alleen de sharding sleutel waarde. Dit helpt bij het verminderen van de maximale hoeveelheid logboek ruimte en vergren delingen die tijdens de bewerking zijn vereist. Overweeg om een unieke index of primaire sleutel te maken, inclusief de sleutel sharding in een bepaalde tabel als u deze tabel wilt gebruiken met splitsen/samen voegen/verplaatsings aanvragen. Uit prestatie overwegingen moet de sharding-sleutel de voorloop kolom zijn in de sleutel of de index.
- Tijdens de verwerking van aanvragen kunnen bepaalde shardlet gegevens aanwezig zijn op de bron-en doel-Shard. Dit is nodig om te beschermen tegen fouten tijdens de shardlet-verplaatsing. De integratie van splitsen en samen voegen met de Shard-kaart zorgt ervoor dat verbindingen via de gegevens afhankelijke routerings-Api's die gebruikmaken van de methode **OpenConnectionForKey** op de Shard-kaart, geen inconsistente tussenliggende statussen zien. Wanneer u echter verbinding maakt met de bron-of doel-Shards zonder de methode **OpenConnectionForKey** te gebruiken, zijn inconsistente tussenliggende statussen mogelijk zichtbaar wanneer er aanvragen voor splitsen/samen voegen of verplaatsen worden verzonden. Deze verbindingen kunnen gedeeltelijke of dubbele resultaten weer geven, afhankelijk van de timing of de Shard onderliggende verbinding. Deze beperking omvat momenteel de verbindingen die zijn gemaakt door elastische schaal multi-Shard-Query's.
- De meta gegevens database voor de service voor splitsen en samen voegen mag niet worden gedeeld tussen verschillende rollen. Een rol van de service voor splitsen en samen voegen die wordt uitgevoerd in staging moet bijvoorbeeld verwijzen naar een andere meta gegevens database dan de rol van productie.

## <a name="billing"></a>Billing

De Split-Merge-service wordt uitgevoerd als een Cloud service in uw Microsoft Azure-abonnement. Kosten voor Cloud Services zijn daarom van toepassing op uw exemplaar van de service. Tenzij u regel matig splitsen/samen voegen/verplaatsen uitvoert, raden we u aan de Cloud service voor splitsen en samen voegen te verwijderen. Hiermee bespaart u kosten voor het uitvoeren of implementeren van Cloud service-exemplaren. U kunt de uitvoer bare-configuratie opnieuw implementeren en starten wanneer u een split-of samenvoeg bewerking moet uitvoeren.

## <a name="monitoring"></a>Bewaking

### <a name="status-tables"></a>Status tabellen

De Split-Merge-service biedt de **RequestStatus** -tabel in de meta gegevensopslag database voor de bewaking van voltooide en lopende aanvragen. De tabel bevat een rij voor elke aanvraag voor splitsen en samen voegen die is verzonden naar dit exemplaar van de service voor splitsen en samen voegen. Het geeft de volgende informatie voor elke aanvraag:

- **Tijdstempel**

  De datum en tijd waarop de aanvraag is gestart.

- **OperationId**

  Een GUID waarmee de aanvraag uniek wordt geïdentificeerd. Deze aanvraag kan ook worden gebruikt om de bewerking te annuleren terwijl deze nog steeds actief is.

- **Status**

  De huidige status van de aanvraag. Voor lopende aanvragen wordt er ook een lijst weer gegeven met de huidige fase waarin de aanvraag is.

- **CancelRequest**

  Een vlag die aangeeft of de aanvraag is geannuleerd.

- **Voortgang**

  Een percentage van de voltooiings schatting voor de bewerking. Een waarde van 50 geeft aan dat de bewerking ongeveer 50% is voltooid.

- **Details**

  Een XML-waarde die een gedetailleerd voortgangs rapport biedt. Het voortgangs rapport wordt regel matig bijgewerkt als er sets rijen worden gekopieerd van de bron naar het doel. In geval van fouten of uitzonde ringen bevat deze kolom ook gedetailleerde informatie over de fout.

### <a name="azure-diagnostics"></a>Azure Diagnostics

De Split-Merge-service maakt gebruik van Azure Diagnostics op basis van Azure SDK 2,5 voor bewaking en diagnose. U beheert de diagnostische configuratie, zoals hier wordt uitgelegd: [Diagnostische gegevens inschakelen in Azure Cloud Services en virtual machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). Het Download pakket bevat twee diagnostische configuraties: een voor de webrole en één voor de rol van de werk nemer. Het bevat de definities voor het vastleggen van prestatie meter items, IIS-logboeken, Windows-gebeurtenis logboeken en het splitsen en samen voegen van toepassings gebeurtenis Logboeken.

## <a name="deploy-diagnostics"></a>Diagnostische gegevens implementeren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Als u controle en diagnostische gegevens wilt inschakelen met behulp van de diagnostische configuratie voor de web-en werk rollen van het NuGet-pakket, voert u de volgende opdrachten uit met Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

U kunt hier meer informatie vinden over het configureren en implementeren van diagnostische instellingen: [Diagnostische gegevens inschakelen in Azure Cloud Services en virtual machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Diagnostische gegevens ophalen

U kunt eenvoudig toegang krijgen tot uw diagnostische gegevens via de Visual Studio-Server Explorer in het gedeelte Azure van de Server Explorer boom structuur. Open een exemplaar van Visual Studio en klik in de menu balk op weer gave en Server Explorer. Klik op het pictogram van Azure om verbinding te maken met uw Azure-abonnement. Ga vervolgens naar Azure-> Storage-> `<your storage account>` -> tabellen-> WADLogsTable. Zie [Server Explorer](/previous-versions/x603htbk(v=vs.140))voor meer informatie.

![WADLogsTable][2]

De WADLogsTable die in de bovenstaande afbeelding is gemarkeerd, bevat gedetailleerde gebeurtenissen uit het toepassings logboek van de Split-Merge-service. Houd er rekening mee dat de standaard configuratie van het gedownloade pakket is gericht op een productie-implementatie. Daarom is het interval waarmee logboeken en prestatie meter items worden opgehaald uit de service-exemplaren groot (5 minuten). Voor testen en ontwikkeling verlaagt u het interval door de diagnostische instellingen van de web-of werk rollen aan uw behoeften aan te passen. Klik met de rechter muisknop op de rol in de Visual Studio-Server Explorer (zie hierboven) en pas de overdrachts periode aan in het dialoog venster voor de diagnostische configuratie-instellingen:

![Configuratie][3]

## <a name="performance"></a>Prestaties

Over het algemeen is betere prestaties te verwachten van hogere, meer uitvoerende service lagen. Hogere i/o-, CPU-en geheugen toewijzingen voor de hogere service lagen profiteren van de bulksgewijze Kopieer-en verwijder bewerkingen die door de service voor splitsen en samen voegen worden gebruikt. Om die reden moet u de servicelaag alleen voor deze data bases verhogen voor een bepaalde, beperkte periode.

De service voert ook validatie query's uit als onderdeel van de normale bewerkingen. Deze validatie query's controleren op onverwachte aanwezigheid van gegevens in het doel bereik en zorgen ervoor dat de bewerking splitsen/samen voegen/verplaatsen wordt gestart vanuit een consistente status. Deze query's werken allemaal via sharding-sleutel reeksen die zijn gedefinieerd door het bereik van de bewerking en de Batch grootte die is opgegeven als onderdeel van de definitie van de aanvraag. Deze query's worden het beste uitgevoerd wanneer er een index aanwezig is die de sharding-sleutel als de voorloop kolom heeft.

Daarnaast kunt u met een eigenschap UniqueName met de sharding-sleutel als de eerste kolom een geoptimaliseerde benadering gebruiken waarmee het resource gebruik wordt beperkt in termen van de logboek ruimte en het geheugen. Deze eigenschap UniqueName is vereist voor het verplaatsen van grote gegevens grootten (meestal 1 GB).

## <a name="how-to-upgrade"></a>Upgrade uitvoeren

1. Volg de stappen in [een service voor splitsen en samen voegen implementeren](elastic-scale-configure-deploy-split-and-merge.md).
2. Wijzig het configuratie bestand van de Cloud service voor uw implementatie voor splitsen en samen voegen om de nieuwe configuratie parameters weer te geven. Een nieuwe vereiste para meter is de informatie over het certificaat dat wordt gebruikt voor versleuteling. Een eenvoudige manier om dit te doen is door het nieuwe configuratie sjabloon bestand te vergelijken met de down load voor uw bestaande configuratie. Zorg ervoor dat u de instellingen voor ' DataEncryptionPrimaryCertificateThumbprint ' en ' DataEncryptionPrimary ' toevoegt voor zowel het web als de rol Worker.
3. Voordat u de update naar Azure implementeert, moet u ervoor zorgen dat alle actieve bewerkingen voor splitsen en samen voegen zijn voltooid. U kunt dit eenvoudig doen door query's uit te voeren op de tabellen RequestStatus en PendingWorkflows in de meta gegevens database voor gesplitste samen voeging voor lopende aanvragen.
4. Werk uw bestaande Cloud service-implementatie bij voor splitsen en samen voegen in uw Azure-abonnement met het nieuwe pakket en het bijgewerkte Service configuratie bestand.

U hoeft geen nieuwe meta gegevens database in te richten voor Split-Merge om een upgrade uit te voeren. De nieuwe versie van de meta gegevens database wordt automatisch bijgewerkt naar de nieuwe versie.

## <a name="best-practices--troubleshooting"></a>Best practices en probleemoplossing

- Definieer een test Tenant en oefen uw belangrijkste bewerkingen voor splitsen en samen voegen/verplaatsen uit met de test Tenant in verschillende Shards. Zorg ervoor dat alle meta gegevens correct zijn gedefinieerd in uw Shard-kaart en dat de bewerkingen geen beperkingen of refererende sleutels schenden.
- Behoud de grootte van de gegevens van de test Tenant boven de maximale gegevens grootte van uw grootste Tenant om ervoor te zorgen dat er geen problemen met de gegevens grootte optreden. Zo kunt u een bovengrens beoordelen op de tijd die nodig is om één Tenant rond te verplaatsen.
- Zorg ervoor dat het schema verwijderingen toestaat. De service voor splitsen en samen voegen vereist de mogelijkheid om gegevens van de bron Shard te verwijderen nadat de gegevens naar het doel zijn gekopieerd. **Verwijder triggers** kunnen er bijvoorbeeld voor zorgen dat de service de gegevens op de bron niet kan verwijderen en kan ertoe leiden dat bewerkingen mislukken.
- De sharding-sleutel moet de leidende kolom zijn in uw primaire sleutel of unieke index definitie. Dat zorgt voor de beste prestaties voor het splitsen of samen voegen van validatie query's en voor de daad werkelijke gegevens verplaatsing en verwijder bewerkingen die altijd worden uitgevoerd op sharding.
- Termijnen uw service voor splitsen en samen voegen in de regio en het Data Center waar uw data bases zich bevinden.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png