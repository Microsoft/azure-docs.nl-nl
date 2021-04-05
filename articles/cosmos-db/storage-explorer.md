---
title: Azure Cosmos DB-resources beheren met Azure Storage Explorer
description: Meer informatie over het maken van verbinding met Azure Cosmos DB en het beheren van de resources met behulp van Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348650"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Cosmos DB-resources beheren met Azure Storage Explorer
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

U kunt Azure Storage Explorer gebruiken om verbinding te maken met Azure Cosmos DB. Hiermee kunt u verbinding maken met Azure Cosmos DB-accounts die worden gehost in Azure en onafhankelijke Clouds van Windows, macOS of Linux.

Gebruik hetzelfde hulp programma om uw verschillende Azure-entiteiten op één plek te beheren. U kunt Azure Cosmos DB entiteiten beheren, gegevens bewerken, opgeslagen procedures en triggers bijwerken, samen met andere Azure-entiteiten, zoals opslag-blobs en wacht rijen. Azure Storage Explorer ondersteunt Cosmos-accounts die zijn geconfigureerd voor SQL-, MongoDB-, Graph-en Table-Api's.

> [!NOTE]
> Integratie van Azure Cosmos DB met Storage Explorer is afgeschaft. Bestaande functionaliteit wordt gedurende minimaal één jaar niet uit deze release verwijderd. U moet in plaats daarvan de [Azure Portal](https://portal.azure.com/), de [Azure Portal desktop-app](https://portal.azure.com/App/Download) of de zelfstandige [Azure Cosmos DB Explorer](data-explorer.md) gebruiken. De alternatieve opties bevatten veel nieuwe functies die momenteel niet in Storage Explorer worden ondersteund.

## <a name="prerequisites"></a>Vereisten

Een Cosmos-account met een SQL-API of een Azure Cosmos DB-API voor MongoDB. Als u geen account hebt, kunt u er een maken in de Azure Portal. Zie [Azure Cosmos DB: een SQL API-Web-app maken met .net en de Azure Portal](create-sql-api-dotnet.md) voor meer informatie.

## <a name="installation"></a>Installatie

Als u de nieuwste Azure Storage Explorer bits wilt installeren, raadpleegt u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Windows-, Linux-en macOS-versies worden ondersteund.

## <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

1. Nadat u **Azure Storage Explorer** hebt geïnstalleerd, selecteert u het pictogram voor de **invoeg toepassing** in het linkerdeel venster.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Scherm afbeelding met het pictogram voor de invoeg toepassing in het linkerdeel venster.":::

1. Selecteer **Een Azure-account toevoegen** en selecteer vervolgens **Aanmelden**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Scherm opname van het venster verbinding maken met Azure Storage met het keuze rondje een Azure-account toevoegen geselecteerd en de vervolg keuzelijst Azure-omgeving.":::

1. Selecteer **Aanmelden** in het dialoog venster **Azure-aanmelding** en voer uw Azure-referenties in.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Scherm afbeelding van het aanmeld venster waarin wordt weer gegeven waar u uw referenties voor uw Azure-abonnement kunt invoeren.":::

1. Selecteer uw abonnement in de lijst en selecteer vervolgens **Toepassen**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Scherm opname van het deel venster account beheer, met daarin een lijst abonnementen en de knop Toep assen.":::

    Het Verkenner-deel venster wordt bijgewerkt en de accounts in het geselecteerde abonnement worden weer gegeven.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Scherm afbeelding van het deel venster Verkenner, bijgewerkt om de accounts in het geselecteerde abonnement weer te geven.":::

    Uw **Cosmos DB-account** is verbonden met uw Azure-abonnement.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Een connection string gebruiken om verbinding te maken met Azure Cosmos DB

U kunt een connection string gebruiken om verbinding te maken met een Azure Cosmos DB. Deze methode ondersteunt alleen SQL-en Table-Api's. Volg deze stappen om verbinding te maken met een connection string:

1. Zoek **lokaal en gekoppeld** in de linker boom structuur, klik met de rechter muisknop op **Cosmos DB accounts** en selecteer vervolgens **verbinding maken met Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Scherm opname waarin de vervolg keuzelijst wordt weer gegeven nadat u met de rechter muisknop hebt geklikt en verbinding maken met Azure Cosmos D B gemarkeerd.":::

2. In het venster **verbinding maken met Cosmos DB** :
   1. Selecteer de API in de vervolg keuzelijst.
   1. Plak uw connection string in het vak **verbindings reeks** . Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.
   1. Voer een **account label** in en selecteer **volgende** om de samen vatting te controleren.
   1. Selecteer **verbinden** om verbinding te maken met het Azure Cosmos DB-account.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Scherm opname van het venster verbinding maken met Cosmos D B, met daarin de vervolg keuzelijst API, het vak Verbindings reeks en het vak account label.":::

> [!NOTE]
> Als de Azure Storage Explorer laat zien dat de Azure Cosmos DB connection string een ongeldige indeling heeft, moet u ervoor zorgen dat de connection string een punt komma ( `;` ) aan het einde heeft. Een voor beeld van een geldige Azure Cosmos DB connection string zijn: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Een lokale emulator gebruiken om verbinding te maken met Azure Cosmos DB

Gebruik de volgende stappen om verbinding te maken met een Azure Cosmos DB met een emulator. Deze methode biedt alleen ondersteuning voor SQL-accounts.

1. Installeer Cosmos DB emulator en open het. Zie [Cosmos DB emulator](./local-emulator.md)voor informatie over het installeren van de emulator.

1. Zoek **lokaal en gekoppeld** in de linker boom structuur, klik met de rechter muisknop op **Cosmos DB accounts** en selecteer vervolgens **verbinding maken met Cosmos DB emulator**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Scherm afbeelding met het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt en verbinding maken met de Azure Cosmos D B-emulator is gemarkeerd.":::

1. In het venster **verbinding maken met Cosmos DB** :
   1. Plak uw connection string in het vak **verbindings reeks** . Zie [de Connection String ophalen](manage-with-powershell.md#list-keys)voor informatie over het ophalen van de primaire Connection String.
   1. Voer een **account label** in en selecteer **volgende** om de samen vatting te controleren.
   1. Selecteer **verbinden** om verbinding te maken met het Azure Cosmos DB-account.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Scherm afbeelding van het venster verbinding maken met Cosmos D B, met daarin het vak Verbindings reeks en het vak account label.":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB-resourcebeheer

Gebruik de volgende bewerkingen voor het beheren van een Azure Cosmos DB account:

* Open het account in de Azure Portal.
* Voeg de resource toe aan de lijst voor snelle toegang.
* Resources zoeken en vernieuwen.
* Data bases maken en verwijderen.
* Verzamelingen maken en verwijderen.
* Documenten maken, bewerken, verwijderen en filteren.
* Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies beheren.

### <a name="quick-access-tasks"></a>Snelle toegang tot taken

U kunt met de rechter muisknop op een abonnement in het deel venster Verkenner klikken om veel snelle actie taken uit te voeren, bijvoorbeeld:

* Klik met de rechter muisknop op een Azure Cosmos DB account of data base en selecteer vervolgens **openen in portal** om de resource te beheren in de browser op de Azure Portal.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Scherm opname van het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, met openen in portal gemarkeerd.":::

* Klik met de rechter muisknop op een Azure Cosmos DB-account,-data base of-verzameling en selecteer vervolgens **toevoegen aan snelle toegang** om deze toe te voegen aan het menu snelle toegang.

* Selecteer **hier zoeken** om het zoeken naar tref woorden onder het geselecteerde pad in te scha kelen.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Scherm opname waarin het zoekvak is gemarkeerd.":::

### <a name="database-and-collection-management"></a>Database- en verzamelingbeheer

#### <a name="create-a-database"></a>Een database maken

1. Klik met de rechter muisknop op het Azure Cosmos DB account en selecteer **Data Base maken**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Scherm afbeelding met het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, waarbij Create Data Base is gemarkeerd.":::

1. Voer de database naam in en druk op **Enter** om te volt ooien.

#### <a name="delete-a-database"></a>Een database verwijderen

1. Klik met de rechter muisknop op de data base en selecteer **Data Base verwijderen**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Scherm opname van het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, waarbij Delete Data Base is gemarkeerd.":::

1. Selecteer **Ja** in het pop-upvenster. Het databaseknooppunt wordt verwijderd en het Azure Cosmos DB die Cosmos-account wordt automatisch vernieuwd.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Scherm afbeelding van het bevestigings venster met de knop Ja gemarkeerd.":::

#### <a name="create-a-collection"></a>Een verzameling maken

1. Klik met de rechter muisknop op uw data base en selecteer vervolgens **verzameling maken**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Scherm opname met het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, waarbij verzameling maken is gemarkeerd.":::

1. Voer in het venster verzameling maken de gevraagde gegevens in, zoals de **verzamelings-id** en de **opslag capaciteit**, enzovoort. Selecteer ten slotte **OK**.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Scherm opname van het venster verzameling maken, met daarin het vak verzameling I D en de opslag capaciteit.":::

1. Selecteer **onbeperkt** zodat u een partitie sleutel kunt opgeven en selecteer **OK** om te volt ooien.

   > [!NOTE]
   > Als een partitie sleutel wordt gebruikt wanneer u een verzameling maakt en eenmaal is gemaakt, kunt u de partitie sleutel waarde niet wijzigen voor de verzameling.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Scherm opname van het venster verzameling maken, met onbeperkte selectie voor opslag capaciteit en het vak partitie sleutel is gemarkeerd.":::

#### <a name="delete-a-collection"></a>Verwijder een collectie

- Klik met de rechter muisknop op de verzameling, selecteer **verzameling verwijderen** en selecteer vervolgens **Ja** in het pop-upvenster.

    Het verzamelingsknooppunt wordt verwijderd en de database wordt automatisch vernieuwd.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Scherm opname met het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, waarbij verzameling verwijderen is gemarkeerd.":::

### <a name="document-management"></a>Documentbeheer

#### <a name="create-and-modify-documents"></a>Documenten maken en wijzigen

- Open **documenten** in het linkerdeel venster, selecteer **Nieuw document**, bewerk de inhoud in het rechterdeel venster en selecteer vervolgens **Opslaan**.
- U kunt ook een bestaand document bijwerken en vervolgens **Opslaan** selecteren. Als u de wijzigingen wilt negeren, selecteert u **negeren**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Scherm opname van documenten die zijn gemarkeerd in het linkerdeel venster. In het rechterdeel venster zijn nieuw document, opslaan en verwijderen gemarkeerd.":::

#### <a name="delete-a-document"></a>Een document verwijderen

* Selecteer de knop **verwijderen** om het geselecteerde document te verwijderen.

#### <a name="query-for-documents"></a>Query voor documenten

* Als u het document filter wilt bewerken, voert u een [SQL-query](./sql-query-getting-started.md)in en selecteert u vervolgens **Toep assen**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Scherm opname van het rechterdeel venster, met de knoppen filteren en Toep assen, het ID-nummer en het query-vak gemarkeerd.":::

### <a name="graph-management"></a>Grafiekbeheer

#### <a name="create-and-modify-a-vertex"></a>Een hoek punt maken en wijzigen

* Als u een nieuw hoek punt wilt maken, opent u **Graph** in het linkerdeel venster, selecteert u **Nieuw hoek punt**, bewerkt u de inhoud en selecteert u **OK**.
* Als u een bestaand hoek punt wilt wijzigen, selecteert u het pictogram pen in het rechterdeel venster.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Scherm opname van de geselecteerde grafiek in het linkerdeel venster en toont het nieuwe hoek punt en het pictogram van de pen dat is gemarkeerd in het rechterdeel venster.":::

#### <a name="delete-a-graph"></a>Een grafiek verwijderen

* Als u een hoek punt wilt verwijderen, selecteert u het prullenbak pictogram naast de naam van het hoek punt.

#### <a name="filter-for-graph"></a>Filteren op grafiek

* Als u het grafiek filter wilt bewerken, voert u een [Gremlin-query](gremlin-support.md)in en selecteert u vervolgens **filter Toep assen**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Scherm opname van de grafiek die is geselecteerd in het linkerdeel venster en toont het filter Toep assen en het query-vak is gemarkeerd in het rechterdeel venster.":::

### <a name="table-management"></a>Tabelbeheer

#### <a name="create-and-modify-a-table"></a>Een tabel maken en wijzigen

* Een nieuwe tabel maken:
   1. Open **entiteiten** in het linkerdeel venster en selecteer vervolgens **toevoegen**.
   1. Bewerk de inhoud in het dialoog venster **entiteit toevoegen** .
   1. Selecteer de knop **eigenschap toevoegen** om een eigenschap toe te voegen.
   1. Selecteer **Invoegen**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Scherm opname van de entiteiten die zijn gemarkeerd in het linkerdeel venster, en toont de weer gave toevoegen, bewerken, toevoegen en gemarkeerd in het rechterdeel venster.":::

* Als u een tabel wilt wijzigen, selecteert u **bewerken**, wijzigt u de inhoud en selecteert u vervolgens **bijwerken**.

   

#### <a name="import-and-export-table"></a>Tabel importeren en exporteren

* Als u wilt importeren, selecteert u de knop **importeren** en kiest u vervolgens een bestaande tabel.
* Als u wilt exporteren, selecteert u de knop **exporteren** en kiest u vervolgens een bestemming.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Scherm afbeelding met de knoppen importeren en exporteren die zijn gemarkeerd in het rechterdeel venster.":::

#### <a name="delete-entities"></a>Entiteiten verwijderen

* Selecteer de entiteiten en selecteer vervolgens de knop **verwijderen** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Scherm afbeelding met de knop verwijderen gemarkeerd in het rechterdeel venster en een bevestigings venster met Ja gemarkeerd.":::

#### <a name="query-a-table"></a>Query uitvoeren op een tabel

- Selecteer de **query** knop, voer een query voorwaarde in en selecteer vervolgens de knop **query uitvoeren** . Als u het query deel venster wilt sluiten, selecteert u de knop **query sluiten** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Scherm opname van het rechterdeel venster met daarin de knop Query uitvoeren en de knop query sluiten gemarkeerd.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Opgeslagen procedures, triggers en UDF's beheren

* Een opgeslagen procedure maken:
  1. Klik in de linker boom structuur met de rechter muisknop op **opgeslagen procedures** en selecteer vervolgens **opgeslagen procedure maken**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Scherm afbeelding van het linkerdeel venster met het menu dat wordt weer gegeven nadat u met de rechter muisknop hebt geklikt, waarbij opgeslagen procedure maken is gemarkeerd.":::
  
  1. Voer in het veld links een naam in, voer de opgeslagen procedure scripts in het rechterdeel venster in en selecteer **maken**.
  
* Als u een bestaande opgeslagen procedure wilt bewerken, dubbelklikt u op de procedure, maakt u de update en selecteert u vervolgens **bijwerken** om op te slaan. U kunt ook **negeren** selecteren om de wijziging te annuleren.

* De bewerkingen voor **Triggers** en **UDF** zijn vergelijkbaar met **opgeslagen procedures**.

## <a name="troubleshooting"></a>Problemen oplossen

Hier volgen oplossingen voor veelvoorkomende problemen die zich voordoen wanneer u Azure Cosmos DB in Storage Explorer gebruikt.

### <a name="sign-in-issues"></a>Problemen met aanmelden

Start eerst uw toepassing opnieuw om te zien of het probleem is opgelost. Als het probleem zich blijft voordoen, gaat u verder met het oplossen van problemen.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Zelfondertekend certificaat in de certificaatketen

Er zijn een aantal redenen waarom u deze fout ziet, de twee meest voorkomende:

* U bevindt zich achter een *transparante proxy*. Iemand, zoals uw IT-afdeling, onderschept HTTPS-verkeer, ontsleutelt deze en versleutelt deze vervolgens met behulp van een zelfondertekend certificaat.

* U voert software uit, zoals antivirus software. De software injecteert een zelfondertekend TLS/SSL-certificaat in de HTTPS-berichten die u ontvangt.

Wanneer Storage Explorer een zelfondertekend certificaat vindt, weet het niet of er met het ontvangen HTTPS-bericht is geknoeid. Als u een exemplaar van het zelfondertekende certificaat hebt, kunt u Storage Explorer vertellen dat het kan worden vertrouwd. Als u niet zeker weet wie het certificaat heeft geïnjecteerd, kunt u deze stappen volgen om te proberen:

1. OpenSSL installeren:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): elke licht versie is OK.
     - macOS en Linux: moeten zijn opgenomen in uw besturings systeem.

1. OpenSSL uitvoeren:
    * Windows: Ga naar de installatiemap, klik vervolgens op **/bin/** en dubbel klik op **openssl.exe**.
    * Mac en Linux: Voer **openssl** uit vanuit een Terminal.
1. Uitvoeren `s_client -showcerts -connect microsoft.com:443` .
1. Zoek naar zelfondertekende certificaten. Als u niet zeker weet wat er zelf is ondertekend, zoekt u naar een wille keurige locatie waar het onderwerp (' s: ') en de certificaat verlener (' i: ') hetzelfde zijn.
1. Als u zelfondertekende certificaten vindt, kopieert en plakt u alles uit en voegt u **-----begin certificaat-----** in **-----eind certificaat-----** aan een nieuwe. CER-bestand voor elke versie.
1. Open Storage Explorer en ga vervolgens naar certificaten voor het importeren van  >  **SSL-certificaten** bewerken  >  . Gebruik de bestands kiezer om de te zoeken, te selecteren en te openen. CER-bestanden die u hebt gemaakt.

Als u geen zelfondertekende certificaten vindt, kunt u feedback verzenden voor meer hulp.

#### <a name="unable-to-retrieve-subscriptions"></a>Kan geen abonnementen ophalen

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld, kunt u de volgende suggesties proberen:

* Controleer of uw account toegang heeft tot de abonnementen. Als u dit wilt doen, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/).
* Zorg ervoor dat u bent aangemeld bij de juiste omgeving:
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Duitsland](https://portal.microsoftazure.de/)
  * [Azure van de Amerikaanse overheid](https://portal.azure.us/)
  * Aangepaste omgeving/Azure Stack
* Als u zich achter een proxy bevindt, controleert u of de Storage Explorer proxy juist is geconfigureerd.
* Verwijder het account en voeg het opnieuw toe.
* Verwijder de volgende bestanden uit uw basismap (bijvoorbeeld: C:\Users\ContosoUser) en voeg het account opnieuw toe:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Druk op de F12-toets om de ontwikkelaars console te openen. Bekijk de console voor eventuele fout berichten wanneer u zich aanmeldt.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Scherm afbeelding van de console ontwikkel hulpprogramma's, met de gemarkeerde console.":::

#### <a name="unable-to-see-the-authentication-page"></a>Kan de verificatiepagina niet zien

Als u de verificatiepagina niet kunt zien:

* Afhankelijk van de snelheid van uw verbinding kan het even duren voordat de aanmeldings pagina is geladen. Wacht minstens één minuut voordat u het dialoog venster verificatie sluit.
* Als u zich achter een proxy bevindt, controleert u of de Storage Explorer proxy juist is geconfigureerd.
* Bekijk de antwoorden op de console voor ontwikkel hulpprogramma's (F12) om te zien of er aanwijzingen zijn waarom verificatie niet werkt.

#### <a name="cant-remove-an-account"></a>Kan een account niet verwijderen

Als u een account niet kunt verwijderen of als de koppeling voor het opnieuw verifiëren niets doet:

* Verwijder de volgende bestanden uit de basismap en voeg het account opnieuw toe:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Als u aan SAS gekoppelde opslagbronnen wilt verwijderen, verwijder dan:
  * De map %AppData%/StorageExplorer voor Windows
  * /Gebruikers/<your_name>/Library/Application-ondersteuning/StorageExplorer voor macOS
  * ~/.config/StorageExplorer voor Linux
  
  > [!NOTE]
  > Als u deze bestanden verwijdert, **moet u alle referenties opnieuw invoeren**.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS-proxy probleem

U kunt Azure Cosmos DB knooppunten in de linkernavigatiebalk niet weer geven wanneer u een HTTP/HTTPS-proxy configureert in ASE. U kunt Azure Cosmos DB Data Explorer in de Azure Portal als een tijdelijke oplossing gebruiken.

### <a name="development-node-under-local-and-attached-node-issue"></a>Probleem met het knooppunt 'Ontwikkeling' onder het knooppunt 'Lokaal en gekoppeld'

Er is geen reactie nadat u het **ontwikkel** knooppunt hebt geselecteerd onder het knoop punt **lokaal en gekoppeld** in de linker boom structuur. Dit gedrag is verwacht.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Scherm opname waarin het geselecteerde ontwikkelings knooppunt wordt weer gegeven.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Een Azure Cosmos DB-account koppelen aan het **lokale en gekoppelde** knooppunt fout

Als u de volgende fout ziet nadat u een Azure Cosmos DB-account hebt gekoppeld in het **lokale en gekoppelde** knoop punt, moet u ervoor zorgen dat u de juiste Connection String gebruikt.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Scherm opname van het pop-upvenster met fout: kan geen onderliggende resources ophalen, wat getaddrinfo ENOTFOUND aangeeft.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Fout met uitvouwen van Azure-Cosmos DB-knooppunt

Mogelijk wordt de volgende fout weer gegeven wanneer u knoop punten in de linker boom structuur probeert uit te breiden.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Scherm opname van het pop-upvenster met fout: kan geen onderliggende resources ophalen, waarmee wordt aangegeven dat er geen verbinding kan worden gemaakt met dit Cosmos D B-account.":::

Probeer deze suggesties:

* Controleer of het Azure Cosmos DB-account bezig is met het inrichten. Probeer het opnieuw wanneer het account is gemaakt.
* Als het account zich onder de **balk snelle toegang** of **lokaal en gekoppeld** bevindt, controleert u of het account is verwijderd. Als dat het geval is, moet u het knoop punt hand matig verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Bekijk deze video om te zien hoe u Azure Cosmos DB kunt gebruiken in Azure Storage Explorer: [gebruik Azure Cosmos db in azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Meer informatie over Storage Explorer en verbinding maken met meer services vindt u in [Aan de slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
