---
title: De Azure Storage-emulator gebruiken voor ontwikkelen en testen
description: De Azure Storage-emulator biedt een gratis lokale ontwikkel omgeving voor het ontwikkelen en testen van uw Azure Storage toepassingen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 6d9e0e63a3586be139620c154616acfcba2ab10f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498214"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>De Azure Storage-emulator gebruiken voor ontwikkelen en testen

De Microsoft Azure-opslagemulator is een hulp programma waarmee de Azure Blob-, wachtrij-en tabel services worden geëmuleerd voor lokale ontwikkelings doeleinden. U kunt uw toepassing lokaal testen op de opslag Services zonder een Azure-abonnement te maken of kosten te betalen. Als u tevreden bent over hoe uw toepassing werkt in de emulator, schakelt u over naar een Azure-opslag account in de Cloud.

> [!IMPORTANT]
> De Azure Storage-emulator wordt niet meer actief ontwikkeld. [**Azurite**](storage-use-azurite.md) is het opslag emulator platform. Azurite vervangt de Azure Storage-emulator. Azurite zal nog steeds worden bijgewerkt ter ondersteuning van de nieuwste versies van Azure Storage-Api's. Zie voor meer informatie [**de Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>De opslag emulator ophalen

De opslag emulator is beschikbaar als onderdeel van de [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). U kunt de opslag emulator ook installeren met behulp van het [zelfstandige installatie programma](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direct downloaden). Als u de opslag emulator wilt installeren, moet u beschikken over beheerders bevoegdheden op uw computer.

De opslag emulator wordt momenteel alleen uitgevoerd in Windows. Als u een opslag emulator voor Linux nodig hebt, is een van de opties die door de community worden onderhouden, open-source Storage emulator- [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Gegevens die in één versie van de opslag emulator zijn gemaakt, zijn niet gegarandeerd toegankelijk als u een andere versie gebruikt. Als u uw gegevens voor de lange termijn wilt behouden, raden we u aan om die gegevens op te slaan in een Azure-opslag account, in plaats van in de-opslag emulator.
> 
> De opslag-emulator is afhankelijk van specifieke versies van de OData-bibliotheken. Het vervangen van de OData-Dll's die worden gebruikt door de opslag emulator met andere versies wordt niet ondersteund en kan leiden tot onverwacht gedrag. Elk versie van OData dat door de opslag service wordt ondersteund, kan echter worden gebruikt voor het verzenden van aanvragen naar de emulator.

## <a name="how-the-storage-emulator-works"></a>Hoe de opslag emulator werkt

De opslag emulator maakt gebruik van een lokaal Microsoft SQL Server 2012 Express LocalDB-exemplaar voor het emuleren van Azure Storage-services. U kunt ervoor kiezen om de opslag emulator te configureren voor toegang tot een lokaal exemplaar van SQL Server in plaats van het LocalDB-exemplaar. Zie de sectie [de opslag emulator starten en initialiseren](#start-and-initialize-the-storage-emulator) verderop in dit artikel voor meer informatie.

De opslag emulator maakt verbinding met SQL Server of LocalDB met behulp van Windows-verificatie.

Er zijn enkele verschillen in functionaliteit tussen de opslag-emulator en Azure Storage-services. Zie het gedeelte [verschillen tussen de opslag emulator en de Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) verderop in dit artikel voor meer informatie over deze verschillen.

## <a name="start-and-initialize-the-storage-emulator"></a>De opslag emulator starten en initialiseren

De Azure Storage-emulator starten:

1. Selecteer de knop **Start** of druk op de **Windows** -toets.
2. Begin met typen `Azure Storage Emulator` .
3. Selecteer de emulator in de lijst met weer gegeven toepassingen.

Wanneer de opslag-emulator wordt gestart, wordt een opdracht prompt venster weer gegeven. U kunt dit console venster gebruiken om de opslag emulator te starten en te stoppen. U kunt ook gegevens wissen, status ophalen en de emulator initialiseren vanaf de opdracht prompt. Zie voor meer informatie de sectie [Naslag informatie over het opdracht regel hulpprogramma voor Storage emulator](#storage-emulator-command-line-tool-reference) verderop in dit artikel.

> [!NOTE]
> De Azure Storage-emulator wordt mogelijk niet correct gestart als er een andere opslag emulator, zoals Azurite, op het systeem wordt uitgevoerd.

Wanneer de emulator wordt gestart, ziet u een pictogram in het systeemvak op de taakbalk van Windows.

Wanneer u het opdracht prompt venster voor de opslag-emulator sluit, blijft de opslag emulator actief. Als u de opslag emulator console opnieuw wilt openen, volgt u de voor gaande stappen als u de opslag emulator start.

De eerste keer dat u de opslag emulator uitvoert, wordt de lokale opslag omgeving voor u geïnitialiseerd. Tijdens het initialisatie proces wordt een Data Base gemaakt in LocalDB en worden de HTTP-poorten voor elke lokale opslag service gereserveerd.

De opslag emulator wordt standaard geïnstalleerd in `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> U kunt de [Microsoft Azure Storage Explorer](https://storageexplorer.com) gebruiken om met lokale Storage-emulator bronnen te werken. Zoek naar ' (emulator-standaard poorten) (sleutel) ' onder ' lokale & gekoppeld ' in de structuur Storage Explorer resources Nadat u de opslag emulator hebt geïnstalleerd en gestart.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialiseer de opslag emulator om een andere SQL database te gebruiken

U kunt het opdracht regel programma voor de opslag-emulator gebruiken om de opslag emulator te initialiseren zodat deze verwijst naar een ander SQL database exemplaar dan het standaard LocalDB-exemplaar:

1. Open het venster Storage emulator console, zoals beschreven in de sectie [de opslag emulator starten en initialiseren](#start-and-initialize-the-storage-emulator) .
1. Typ in het console venster de volgende opdracht, waarbij `<SQLServerInstance>` de naam is van het SQL Server exemplaar. Als u LocalDB wilt gebruiken, geeft u `(localdb)\MSSQLLocalDb` als SQL Server-exemplaar op.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   U kunt ook de volgende opdracht gebruiken, waarmee de emulator het standaard SQL Server-exemplaar gebruikt:

   `AzureStorageEmulator.exe init /server .`

   U kunt ook de volgende opdracht gebruiken, waarmee de data base wordt geïnitialiseerd naar het standaard LocalDB-exemplaar:

   `AzureStorageEmulator.exe init /forceCreate`

Zie Naslag informatie over [het opdracht regel programma voor opslag-emulator](#storage-emulator-command-line-tool-reference)voor meer gegevens over deze opdrachten.

> [!TIP]
> U kunt de [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gebruiken om uw SQL Server exemplaren te beheren, met inbegrip van de LocalDB-installatie. Geef in het dialoog venster SMSS **verbinding maken met server** op `(localdb)\MSSQLLocalDb` in het veld **Server naam:** om verbinding te maken met het LocalDB-exemplaar.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Aanvragen verifiëren op basis van de opslag emulator

Als u de opslag emulator hebt geïnstalleerd en gestart, kunt u de code hierop testen. Elke aanvraag die u voor de opslag emulator maakt, moet worden geautoriseerd, tenzij dit een anonieme aanvraag is. U kunt aanvragen voor de opslag emulator machtigen met behulp van gedeelde sleutel verificatie of met een Shared Access Signature (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoriseren met de referenties van de gedeelde sleutel

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Zie [Azure Storage-verbindings reeksen configureren](./storage-configure-connection-string.md)voor meer informatie over verbindings reeksen.

### <a name="authorize-with-a-shared-access-signature"></a>Autoriseren met een hand tekening voor gedeelde toegang

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sommige client bibliotheken van Azure Storage, zoals de Xamarin-bibliotheek, bieden alleen ondersteuning voor verificatie met een SAS-token (Shared Access Signature). U kunt het SAS-token maken met behulp van [Storage Explorer](https://storageexplorer.com/) of een andere toepassing die ondersteuning biedt voor verificatie op basis van gedeelde sleutels.

U kunt ook een SAS-token genereren met behulp van Azure PowerShell. In het volgende voor beeld wordt een SAS-token met volledige machtigingen voor een BLOB-container gegenereerd:

1. Installeer Azure PowerShell als u dit nog niet hebt gedaan (gebruik de nieuwste versie van de Azure PowerShell-cmdlets wordt aanbevolen). Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps)voor installatie-instructies.
2. Open Azure PowerShell en voer de volgende opdrachten uit, waarbij `CONTAINER_NAME` u vervangt door de naam van uw keuze:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

De resulterende URI voor de Shared Access-hand tekening voor de nieuwe container moet er ongeveer als volgt uitzien:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

De gedeelde toegangs handtekening die is gemaakt met dit voor beeld is één dag geldig. De hand tekening verleent volledige toegang (lezen, schrijven, verwijderen, lijst) aan blobs in de container.

Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Resources adresseren in de opslag emulator

De service-eind punten voor de opslag emulator wijken af van de eind punten voor een Azure-opslag account. De lokale computer voert geen domein naam omzetting uit, waardoor de emulator-eind punten van de opslag lokale adressen zijn.

Wanneer u een resource in een Azure-opslag account adresseert, gebruikt u het volgende schema. De account naam maakt deel uit van de URI-hostnaam en de bron die wordt geadresseerd, maakt deel uit van het URI-pad:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

De volgende URI is bijvoorbeeld een geldig adres voor een BLOB in een Azure-opslag account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Omdat de lokale computer geen domein naam omzetting heeft, maakt de account naam deel uit van het URI-pad in plaats van de naam van de host. Gebruik de volgende URI-indeling voor een bron in de-opslag emulator:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Het volgende adres kan bijvoorbeeld worden gebruikt om toegang te krijgen tot een BLOB in de-opslag emulator:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

De service-eind punten voor de opslag emulator zijn:

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue-service: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresseren van het account secundair met RA-GRS

Vanaf versie 3,1 ondersteunt de opslag emulator geo-redundante replicatie met lees toegang (RA-GRS). U kunt toegang krijgen tot de secundaire locatie door deze toe te voegen-secundair aan de account naam. Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob met behulp van de alleen-lezen secundair in de opslag emulator:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Gebruik de Storage-client bibliotheek voor .NET versie 3,2 of hoger voor programmatische toegang tot de secundaire met de opslag emulator. Raadpleeg de [Microsoft Azure Storage-client bibliotheek voor .net](/previous-versions/azure/dn261237(v=azure.100)) voor meer informatie.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Naslag informatie over het opdracht regel hulpprogramma voor opslag emulator

Vanaf versie 3,0 wordt een console venster weer gegeven wanneer u de opslag emulator start. Gebruik de opdracht regel in het console venster om de emulator te starten en te stoppen. U kunt ook een query uitvoeren voor de status en andere bewerkingen uitvoeren vanaf de opdracht regel.

> [!NOTE]
> Als u de Microsoft Azure Compute emulator hebt geïnstalleerd, wordt er een pictogram van een systeemvak weer gegeven wanneer u de opslag emulator start. Klik met de rechter muisknop op het pictogram om een menu weer te geven dat een grafische manier biedt om de opslag emulator te starten en te stoppen.
>
>

### <a name="command-line-syntax"></a>De syntaxis van opdrachtregel

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opties

Typ `/help` bij de opdrachtprompt om een lijst met opties te zien.

| Optie | Beschrijving | Opdracht | Argumenten |
| --- | --- | --- | --- |
| **Begin** |Hiermee wordt de opslag emulator gestart. |`AzureStorageEmulator.exe start [-inprocess]` |*-Opnieuw verwerken*: Start de emulator in het huidige proces in plaats van een nieuw proces te maken. |
| **Stoppen** |Hiermee stopt u de opslag emulator. |`AzureStorageEmulator.exe stop` | |
| **Status** |Hiermee wordt de status van de opslag emulator afgedrukt. |`AzureStorageEmulator.exe status` | |
| **Veilig** |Hiermee wist u de gegevens in alle services die zijn opgegeven op de opdracht regel. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*BLOB*: verwijdert BLOB-gegevens. <br/>*Queue*: Hiermee worden de wachtrij gegevens gewist. <br/>*tabel*: tabel gegevens worden gewist. <br/>*all*: Hiermee worden alle gegevens in alle services gewist. |
| **Init** |Voert eenmalige initialisatie uit om de emulator in te stellen. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Server serverName\instanceName*: Hiermee geeft u de server die als host fungeert voor het SQL-exemplaar. <br/>*-sqlinstance INSTANCENAME*: Hiermee geeft u de naam op van het SQL-exemplaar dat moet worden gebruikt in het standaard Server exemplaar. <br/>*-forcecreate*: Hiermee wordt het maken van de SQL database afgedwongen, zelfs als dit al bestaat. <br/>*-skipcreate*: Hiermee slaat u het maken van de SQL database over. Dit heeft prioriteit boven-forcecreate.<br/>*-reserveports*: probeert de HTTP-poorten te reserveren die aan de services zijn gekoppeld.<br/>*-unreserveports*: probeert reserve ringen te verwijderen voor de HTTP-poorten die zijn gekoppeld aan de services. Dit heeft prioriteit boven-reserveports.<br/>*-inproces*: voert de initialisatie uit in het huidige proces in plaats van een nieuw proces te starten. Het huidige proces moet worden gestart met verhoogde machtigingen als poort reserveringen worden gewijzigd. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Verschillen tussen de opslag emulator en het Azure Storage

Omdat de opslag emulator een lokale geëmuleerde omgeving is, zijn er verschillen tussen het gebruik van de emulator en een Azure-opslag account in de Cloud:

* De opslag emulator ondersteunt slechts één vast account en een bekende verificatie sleutel.
* De opslag emulator is geen schaal bare opslag service en biedt geen ondersteuning voor een groot aantal gelijktijdige clients.
* Zoals beschreven in [het adresseren van resources in de-opslag emulator](#addressing-resources-in-the-storage-emulator), worden bronnen anders behandeld in de-opslag emulator versus een Azure-opslag account. Het verschil is dat het omzetten van de domein naam beschikbaar is in de Cloud, maar niet op de lokale computer.
* Vanaf versie 3,1 ondersteunt het opslag emulator-account geo-redundante replicatie met lees toegang (RA-GRS). In de emulator hebben alle accounts RA-GRS ingeschakeld en is er nooit enige vertraging tussen de primaire en secundaire replica's. De bewerkingen BLOB-service statistieken ophalen, data base service-statistieken ophalen en Data Service statistieken ophalen worden ondersteund voor het account secundair en retour neren de waarde van het `LastSyncTime` antwoord element altijd als de huidige tijd volgens de onderliggende SQL database.
* De service-eind punten van de bestands service en het SMB-protocol worden momenteel niet ondersteund in de-opslag emulator.
* Als u een versie van de opslag Services gebruikt die niet wordt ondersteund door de emulator, retourneert de emulator een VersionNotSupportedByEmulator-fout (HTTP-status code 400-ongeldige aanvraag).

### <a name="differences-for-blob-storage"></a>Verschillen voor Blob Storage

De volgende verschillen zijn van toepassing op Blob Storage in de emulator:

* De opslag emulator ondersteunt alleen BLOB-grootten tot Maxi maal 2 GB.
* De maximale lengte van een BLOB-naam in de opslag emulator is 256 tekens, terwijl de maximum lengte van een BLOB-naam in Azure Storage 1024 tekens is.
* Met incrementele kopie kunnen moment opnamen van overschreven blobs worden gekopieerd. dit retourneert een fout bij de service.
* Het ophalen van paginabereiken verschilt niet tussen moment opnamen die zijn gekopieerd met een incrementele Kopieer-blob.
* Een put-BLOB-bewerking kan worden uitgevoerd op een blob die zich in de opslag emulator bevindt met een actieve lease, zelfs als de lease-ID niet is opgegeven in de aanvraag.
* Toevoeg-BLOB-bewerkingen worden niet ondersteund door de emulator. Als u een bewerking probeert uit te voeren op een toevoeg-blob, wordt een FeatureNotSupportedByEmulator-fout geretourneerd (HTTP-status code 400-ongeldige aanvraag).

### <a name="differences-for-table-storage"></a>Verschillen voor tabel opslag

De volgende verschillen zijn van toepassing op tabel opslag in de emulator:

* De datum eigenschappen in de Table service in de opslag emulator ondersteunen alleen het bereik dat wordt ondersteund door SQL Server 2005 (ze moeten later zijn dan 1 januari 1753). Alle datums vóór 1 januari 1753 worden gewijzigd in deze waarde. De nauw keurigheid van datums is beperkt tot de nauw keurigheid van SQL Server 2005, wat betekent dat datums nauw keurig zijn tot 1/300th van een seconde.
* De opslag emulator ondersteunt waarden van de eigenschappen van de partitie sleutel en de rijwaarden van minder dan 512 bytes. De totale grootte van de account naam, de tabel naam en de naam van de sleutel eigenschap kan niet groter zijn dan 900 bytes.
* De totale grootte van een rij in een tabel in de opslag emulator is beperkt tot minder dan 1 MB.
* In de opslag emulator, eigenschappen van het gegevens type `Edm.Guid` of worden `Edm.Binary` alleen `Equal (eq)` de `NotEqual (ne)` Opera tors en vergelijkings operatoren in query filter teken reeksen ondersteund.

### <a name="differences-for-queue-storage"></a>Verschillen voor wachtrij opslag

Er zijn geen verschillen die specifiek zijn voor de wachtrij opslag in de emulator.

## <a name="storage-emulator-release-notes"></a>Release opmerkingen bij de opslag-emulator

### <a name="version-510"></a>Versie 5,10

* De opslag emulator weigert versie 2019-07-07 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-59"></a>Versie 5,9

* De opslag emulator weigert versie 2019-02-02 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-58"></a>Versie 5,8

* De opslag emulator weigert versie 2018-11-09 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-57"></a>Versie 5,7

* Er is een fout opgelost die kan leiden tot een storing als logboek registratie is ingeschakeld.

### <a name="version-56"></a>Versie 5,6

* De opslag emulator ondersteunt nu versie 2018-03-28 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-55"></a>Versie 5,5

* De opslag emulator ondersteunt nu versie 2017-11-09 van de opslag Services op blob-, wachtrij-en Table service-eind punten.
* Er is ondersteuning toegevoegd voor de eigenschap **created** blob, waarmee de aanmaak tijd van de BLOB wordt geretourneerd.

### <a name="version-54"></a>Versie 5,4

* Ter verbetering van de stabiliteit van de installatie probeert de emulator niet langer poorten te reserveren tijdens de installatie. Als u poort reserveringen wilt, gebruikt u de optie *-reserveports* van de opdracht **init** om deze op te geven.

### <a name="version-53"></a>Versie 5,3

* De opslag emulator ondersteunt nu versie 2017-07-29 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-52"></a>Versie 5,2

* De opslag emulator ondersteunt nu versie 2017-04-17 van de opslag Services op blob-, wachtrij-en Table service-eind punten.
* Er is een fout opgelost waarbij tabel eigenschaps waarden niet goed worden gecodeerd.

### <a name="version-51"></a>Versie 5,1

* Er is een fout opgelost waarbij de opslag emulator de `DataServiceVersion` header retourneert in sommige antwoorden waarbij de service niet was.

### <a name="version-50"></a>Versie 5,0

* Het installatie programma voor de opslag emulator controleert niet meer op bestaande MSSQL-en .NET Framework-installaties.
* Het installatie programma voor de opslag emulator maakt de data base niet meer als onderdeel van de installatie. De data base wordt, indien nodig, nog steeds gemaakt als onderdeel van het opstarten.
* Voor het maken van de data base is geen uitbrei ding meer nodig.
* Poort reserveringen zijn niet meer nodig om te worden opgestart.
* Voegt de volgende opties toe aan `init` : `-reserveports` (vereist uitbrei ding), `-unreserveports` (vereist uitbrei ding), `-skipcreate` .
* Met de optie voor de gebruikers interface van de opslag emulator op het pictogram van het systeemvak wordt nu de opdracht regel interface gestart. De oude gebruikers interface is niet meer beschikbaar.
* Sommige Dll's zijn verwijderd of de naam ervan is gewijzigd.

### <a name="version-46"></a>Versie 4,6

* De opslag emulator ondersteunt nu versie 2016-05-31 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-45"></a>Versie 4,5

* Er is een fout opgelost die ertoe heeft geleid dat de installatie en initialisatie mislukt wanneer de naam van de back-up van de data base wordt gewijzigd.

### <a name="version-44"></a>Versie 4,4

* De opslag emulator ondersteunt nu versie 2015-12-11 van de opslag Services op blob-, wachtrij-en Table service-eind punten.
* De garbage collection van de opslag emulator van BLOB-gegevens is nu efficiënter bij het omgaan met een groot aantal blobs.
* Er is een fout opgelost waardoor de XML van de container-ACL iets anders kan worden gevalideerd dan hoe de opslag service dit doet.
* Er is een fout opgelost die soms de maximale en minimale datum/tijd-waarden in de verkeerde tijd zone heeft veroorzaakt.

### <a name="version-43"></a>Versie 4,3

* De opslag emulator ondersteunt nu versie 2015-07-08 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-42"></a>Versie 4,2

* De opslag emulator ondersteunt nu versie 2015-04-05 van de opslag Services op blob-, wachtrij-en Table service-eind punten.

### <a name="version-41"></a>Versie 4,1

* De opslag emulator ondersteunt nu versie 2015-02-21 van de opslag Services op blob-, wachtrij-en Table service-eind punten. De nieuwe toevoeg-BLOB-functies worden niet ondersteund.
* De emulator retourneert nu een zinvolle fout melding voor niet-ondersteunde versies van opslag Services. U kunt het beste de nieuwste versie van de emulator gebruiken. Als u een VersionNotSupportedByEmulator-fout krijgt (HTTP-status code 400-ongeldige aanvraag), downloadt u de nieuwste versie van de emulator.
* Er is een probleem opgelost waarbij een race condition heeft geleid tot onjuiste tabel entiteit gegevens tijdens gelijktijdige samenvoeg bewerkingen.

### <a name="version-40"></a>Versie 4,0

* De naam van het uitvoer bare bestand van de opslag-emulator is gewijzigd in *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versie 3,2

* De opslag emulator ondersteunt nu versie 2014-02-14 van de opslag Services op blob-, wachtrij-en Table service-eind punten. Bestands service-eind punten worden momenteel niet ondersteund in de-opslag emulator. Zie [versie beheer voor de Azure Storage-services](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) voor meer informatie over versie 2014-02-14.

### <a name="version-31"></a>Versie 3,1

* Geografisch redundante opslag met lees toegang (RA-GRS) wordt nu ondersteund in de-opslag emulator. De `Get Blob Service Stats` -, `Get Queue Service Stats` -en- `Get Table Service Stats` api's worden ondersteund voor het account secundair en retour neren altijd de waarde van het LastSyncTime-antwoord element als de huidige tijd volgens de onderliggende SQL database. Gebruik de Storage-client bibliotheek voor .NET versie 3,2 of hoger voor programmatische toegang tot de secundaire met de opslag emulator. Zie de Microsoft Azure Storage-client bibliotheek voor .NET-referentie voor meer informatie.

### <a name="version-30"></a>Versie 3,0

* De Azure Storage-emulator wordt niet meer in hetzelfde pakket geleverd als de compute-emulator.
* De Graphical User Interface van de opslag-emulator is afgeschaft. Het is vervangen door een script bare opdracht regel interface. Zie voor meer informatie over de opdracht regel interface de naslag informatie voor Storage emulator-Command-Line Hulpprogramma's. De grafische interface blijft aanwezig in versie 3,0, maar kan alleen worden geopend wanneer de compute emulator wordt geïnstalleerd door met de rechter muisknop op het pictogram van het systeemvak te klikken en de gebruikers interface van de opslag emulator weer geven te selecteren.
* Versie 2013-08-15 van de Azure Storage-services wordt nu volledig ondersteund. (Eerder deze versie werd alleen ondersteund door de preview-versie van de opslag emulator.)

## <a name="next-steps"></a>Volgende stappen

* Evalueer de platformoverschrijdende, door de Community beheerde open-source Storage emulator- [Azurite](https://github.com/azure/azurite). 
* [Azure Storage-voor beelden met behulp van .net](./storage-samples-dotnet.md) bevatten koppelingen naar verschillende code voorbeelden die u kunt gebruiken bij het ontwikkelen van uw toepassing.
* U kunt de [Microsoft Azure Storage Explorer](https://storageexplorer.com) gebruiken om te werken met resources in uw Cloud-opslag account en in de-opslag emulator.

## <a name="see-also"></a>Zie ook

* [Lokale Azure Storage ontwikkeling met Azurite, Azure Sdk's en Azure Storage Explorer](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
