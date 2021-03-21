---
title: Aan de slag met Storage Explorer | Microsoft Docs
description: Beginnen met het beheren van Azure storage-resources met Storage Explorer. Down load en installeer Azure Storage Explorer, maak verbinding met een opslag account of-service, en meer.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441558"
---
# <a name="get-started-with-storage-explorer"></a>Aan de slag met Storage Explorer

## <a name="overview"></a>Overzicht

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage-gegevens kunt werken via Windows, macOS en Linux.

In dit artikel leert u verschillende manieren om verbinding te maken met uw Azure Storage-accounts en hoe u deze kunt beheren.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Vereisten

# <a name="windows"></a>[Windows](#tab/windows)

De volgende versies van Windows-ondersteunings Storage Explorer:

* Windows 10 (aanbevolen)
* Windows 8
* Windows 7

Voor alle versies van Windows Storage Explorer .NET Framework 4.7.2 Mini maal vereist.

# <a name="macos"></a>[MacOS](#tab/macos)

De volgende versies van macOS-ondersteunings Storage Explorer:

* macOS 10,12 Sierra en latere versies

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer is beschikbaar in de [snap Store](https://snapcraft.io/storage-explorer) voor de meest voorkomende distributies van Linux. We raden u aan de module Store te plaatsen voor deze installatie. Met de module Storage Explorer worden alle afhankelijkheden en updates geïnstalleerd wanneer nieuwe versies worden gepubliceerd in de snap Store.

Zie de [ `snapd` installatie pagina](https://snapcraft.io/docs/installing-snapd)voor ondersteunde distributies.

Storage Explorer vereist het gebruik van een wachtwoord beheerder. Mogelijk moet u hand matig verbinding maken met een wachtwoord beheerder. U kunt Storage Explorer verbinding maken met het wachtwoord beheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer is ook beschikbaar als een *. tar. gz* -down load. Als u het *. tar. gz* gebruikt, moet u afhankelijkheden hand matig installeren. De volgende distributies van Linux-ondersteuning *. tar. gz* -installatie:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

De installatie van het *tar. gz* -netwerk werkt mogelijk met andere distributies, maar alleen deze vermelde items worden officieel ondersteund.

Zie [Linux-afhankelijkheden](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) in de Azure Storage Explorer Troubleshooting Guide (Engelstalig) voor meer informatie over het installeren van Storage Explorer op Linux.

---

## <a name="download-and-install"></a>Downloaden en installeren

Zie [Azure Storage Explorer](https://www.storageexplorer.com)als u Storage Explorer wilt downloaden en installeren.

## <a name="connect-to-a-storage-account-or-service"></a>Verbinding maken met een opslagaccount of -service

Storage Explorer biedt verschillende manieren om verbinding te maken met Azure-resources:

* [Meld u aan bij Azure om toegang te krijgen tot uw abonnementen en hun resources](#sign-in-to-azure)
* [Koppelen aan een afzonderlijke Azure Storage Resource](#attach-to-an-individual-resource)
* [Koppelen aan een CosmosDB-resource](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

> [!NOTE]
> Om toegang te krijgen tot resources Nadat u zich hebt aangemeld, heeft Storage Explorer zowel beheer (Azure Resource Manager) als gegevenslaag machtigingen nodig. Dit betekent dat u Azure Active Directory (Azure AD)-machtigingen nodig hebt om toegang te krijgen tot uw opslag account, de containers in het account en de gegevens in de containers. Als u alleen machtigingen hebt op de gegevenslaag, kunt u de optie **Aanmelden met Azure Active Directory (Azure AD)** kiezen bij het koppelen aan een resource. Raadpleeg de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)) voor meer informatie over de specifieke machtigingen die Storage Explorer vereist.

1. Selecteer in Storage Explorer   >  **account beheer** weer geven of selecteer de knop **accounts beheren** .

    :::image type="content" alt-text="Accounts beheren" source ="./vs-storage-explorer-manage-accounts.png":::

1. **Account beheer** geeft nu alle Azure-accounts waarop u bent aangemeld. Als u verbinding wilt maken met een ander account, selecteert u **een account toevoegen...**.

1. Het dialoog venster **verbinding maken met Azure Storage** wordt geopend. Selecteer in het deel venster **resource selecteren** de optie **abonnement**.

    :::image type="content" alt-text="Dialoog venster verbinding maken" source="./vs-storage-explorer-connect-dialog.png":::

1. Selecteer in het deel venster **Azure-omgeving selecteren** een Azure-omgeving om u aan te melden. U kunt zich aanmelden bij wereld wijd Azure, een nationale Cloud of een Azure Stack-exemplaar. Selecteer vervolgens **Volgende**.

    :::image type="content" alt-text="Optie om u aan te melden" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Zie voor meer informatie over Azure Stack [verbinding maken met Storage Explorer een Azure stack abonnement of een opslag account](/azure-stack/user/azure-stack-storage-connect-se).

1. Storage Explorer wordt een webpagina geopend waarin u zich kunt aanmelden.

1. Nadat u zich hebt aangemeld met een Azure-account, worden het account en de Azure-abonnementen die aan dat account zijn gekoppeld, weer gegeven onder **account beheer**. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**.

    :::image type="content" alt-text="Selecteer Azure-abonnementen" source="./vs-storage-explorer-account-panel.png":::

1. In **Verkenner** worden de opslag accounts weer gegeven die zijn gekoppeld aan de geselecteerde Azure-abonnementen.

    :::image type="content" alt-text="Geselecteerde Azure-abonnementen" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Koppelen aan een afzonderlijke resource

Met Storage Explorer kunt u verbinding maken met afzonderlijke bronnen, zoals een Azure Data Lake Storage Gen2 container, met behulp van verschillende verificatie methoden. Sommige verificatie methoden worden alleen ondersteund voor bepaalde resource typen.

| Resourcetype    | Azure AD | Account naam en-sleutel | Shared Access Signature (SAS)  | Openbaar (anoniem) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Opslagaccounts | Ja      | Ja                  | Ja (connection string of URL) | Nee                 |
| Blobcontainers  | Ja      | Nee                   | Ja (URL)                      | Ja                |
| Gen2-containers  | Ja      | Nee                   | Ja (URL)                      | Ja                |
| Gen2 mappen | Ja      | Nee                   | Ja (URL)                      | Ja                |
| Bestandsshares      | Nee       | Nee                   | Ja (URL)                      | Nee                 |
| Wachtrijen           | Ja      | Nee                   | Ja (URL)                      | Nee                 |
| Tables           | Nee       | Nee                   | Ja (URL)                      | Nee                 |
 
Storage Explorer kunt ook verbinding maken met een [lokale opslag emulator](#local-storage-emulator) via de geconfigureerde poorten van de emulator.

Als u verbinding wilt maken met een afzonderlijke resource, selecteert u de knop **verbinding maken** op de werk balk aan de linkerkant. Volg vervolgens de instructies voor het bron type waarmee u verbinding wilt maken.

:::image type="content" alt-text="Verbinding maken met de optie Azure Storage" source="./vs-storage-explorer-connect-button.png":::

Wanneer een verbinding met een opslag account is toegevoegd, wordt er een nieuw structuur knooppunt weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**.

Voor andere bron typen wordt een nieuw knoop punt toegevoegd onder **lokale & gekoppelde**  >  **opslag accounts**  >  **(gekoppelde containers)**. Het knoop punt wordt weer gegeven onder een groeps knooppunt dat overeenkomt met het type. Er wordt bijvoorbeeld een nieuwe verbinding met een Azure Data Lake Storage Gen2 container weer gegeven onder **BLOB-containers**.

Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)).

In de volgende secties worden de verschillende verificatie methoden beschreven die u kunt gebruiken om verbinding te maken met afzonderlijke bronnen.

#### <a name="azure-ad"></a>Azure AD

Storage Explorer kunt uw Azure-account gebruiken om verbinding te maken met de volgende resource typen:
* Blobcontainers
* Azure Data Lake Storage Gen2 containers
* Azure Data Lake Storage Gen2 mappen
* Wachtrijen
 
Azure AD is de voorkeurs optie als u een gegevenslaag hebt die toegang heeft tot uw resource, maar geen beheer lagen hebt.

1. Meld u aan bij ten minste één Azure-account met behulp van de [stappen die hierboven worden beschreven](#sign-in-to-azure).
1. Selecteer in het deel venster **resource selecteren** van het dialoog venster **verbinding maken met Azure Storage** de optie **BLOB-container**, **ADLS Gen2 container** of **wachtrij**.
1. Selecteer **Aanmelden met Azure Active Directory (Azure AD)** en selecteer **volgende**.
1. Selecteer een Azure-account en-Tenant. Het account en de Tenant moeten toegang hebben tot de opslag resource die u wilt koppelen. Selecteer **Next**.
1. Voer een weergave naam in voor uw verbinding en de URL van de resource. Selecteer **Next**.
1. Controleer uw verbindings gegevens in het deel venster **samen vatting** . Als de verbindings gegevens correct zijn, selecteert u **verbinding maken**.

#### <a name="account-name-and-key"></a>Account naam en-sleutel

Storage Explorer kunt verbinding maken met een opslag account met behulp van de naam en sleutel van het opslag account.

U vindt uw account sleutels in de [Azure Portal](https://portal.azure.com). Open de pagina voor het opslag account en selecteer **instellingen**  >  **toegangs sleutels**.

1. Selecteer **opslag account** in het deel venster **resource selecteren** van het dialoog venster **verbinding maken met Azure Storage** .
1. Selecteer **account naam en sleutel** en selecteer **volgende**.
1. Voer een weergave naam in voor de verbinding, de naam van het account en een van de account sleutels. Selecteer de juiste Azure-omgeving. Selecteer **Next**.
1. Controleer uw verbindings gegevens in het deel venster **samen vatting** . Als de verbindings gegevens correct zijn, selecteert u **verbinding maken**.

#### <a name="shared-access-signature-sas-connection-string"></a>Shared Access Signature (SAS) connection string

Storage Explorer kunt verbinding maken met een opslag account met behulp van een connection string met een Shared Access Signature (SAS). Een SAS-connection string ziet er als volgt uit:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. Selecteer **opslag account** in het deel venster **resource selecteren** van het dialoog venster **verbinding maken met Azure Storage** .
1. Selecteer **Shared Access Signature (SAS)** en selecteer **volgende**.
1. Voer een weergave naam in voor uw verbinding en de SAS-connection string voor het opslag account. Selecteer **Next**.
1. Controleer uw verbindings gegevens in het deel venster **samen vatting** . Als de verbindings gegevens correct zijn, selecteert u **verbinding maken**.

#### <a name="shared-access-signature-sas-url"></a>URL voor Shared Access Signature (SAS)

Storage Explorer kunt verbinding maken met de volgende bron typen met behulp van een SAS-URI:
* Blobcontainer
* Azure Data Lake Storage Gen2 container of map
* Bestandsshare
* Wachtrij
* Tabel

Een SAS-URI ziet er als volgt uit:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. Selecteer in het deel venster **resource selecteren** van het dialoog venster **verbinding maken met Azure Storage** de resource waarmee u verbinding wilt maken.
1. Selecteer **Shared Access Signature (SAS)** en selecteer **volgende**.
1. Voer een weergave naam in voor de verbinding en de SAS-URI voor de resource. Selecteer **Next**.
1. Controleer uw verbindings gegevens in het deel venster **samen vatting** . Als de verbindings gegevens correct zijn, selecteert u **verbinding maken**.

#### <a name="local-storage-emulator"></a>Lokale opslag emulator

Storage Explorer kunt verbinding maken met een Azure Storage-emulator. Op dit moment worden er twee emulators ondersteund:

* [Azure Storage-emulator](storage/common/storage-use-emulator.md) (alleen Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS of Linux)

Als uw emulator luistert naar de standaard poorten, kunt u het **lokale & gekoppelde**  >  **opslag accounts**  >  **emulator-standaard poorten-** knoop punt gebruiken om toegang te krijgen tot uw emulator.

Als u een andere naam wilt gebruiken voor uw verbinding of als uw emulator niet wordt uitgevoerd op de standaard poorten:

1. Start uw emulator.

   > [!IMPORTANT]
   > De emulator wordt niet automatisch door Storage Explorer gestart. U moet deze hand matig starten.

1. Selecteer **lokale-opslag emulator** in het deel venster **resource selecteren** van het dialoog venster **verbinding maken met Azure Storage** .
1. Voer een weergave naam in voor uw verbinding en het poort nummer voor elke geëmuleerde service die u wilt gebruiken. Als u niet wilt gebruiken voor een service, laat u de bijbehorende poort leeg. Selecteer **Next**.
1. Controleer uw verbindings gegevens in het deel venster **samen vatting** . Als de verbindings gegevens correct zijn, selecteert u **verbinding maken**.

### <a name="connect-to-azure-cosmos-db"></a>Verbinding maken met Azure Cosmos DB

Storage Explorer biedt ook ondersteuning voor het maken van verbinding met Azure Cosmos DB resources.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Verbinding maken met een Azure Cosmos DB-account met behulp van een connection string

In plaats van Azure Cosmos DB accounts via een Azure-abonnement te beheren, kunt u verbinding maken met Azure Cosmos DB met behulp van een connection string. Volg deze stappen om verbinding te maken:

1. Vouw onder **Explorer** de optie **lokale & toevoegen** uit, klik met de rechter muisknop op **Cosmos DB accounts** en selecteer **verbinding maken met Azure Cosmos DB**.

    ![Verbinding maken met Azure Cosmos DB met een verbindingsreeks][21]

1. Selecteer de Azure Cosmos DB-API, voer de gegevens voor de **verbindings reeks** in en selecteer **OK** om verbinding te maken met het Azure Cosmos DB-account. Zie [een Azure Cosmos-account beheren](./cosmos-db/how-to-manage-database-account.md)voor meer informatie over het ophalen van de Connection String.

    ![Verbindingsreeks][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Verbinding maken met Azure Data Lake Store op URI

U kunt toegang krijgen tot een bron die niet in uw abonnement is. U hebt iemand die toegang heeft tot deze resource nodig om u de resource-URI te geven. Nadat u zich hebt aangemeld, maakt u verbinding met Data Lake Store met behulp van de URI. Volg deze stappen om verbinding te maken:

1. Vouw onder **Explorer** de optie **lokale & toevoegen** toe.

1. Klik met de rechter muisknop op **Data Lake Storage gen1** en selecteer **verbinding maken met data Lake Storage gen1**.

    ![Verbinding maken met het context menu van Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Voer de URI in en selecteer **OK**. Uw Data Lake Store wordt weer gegeven onder **Data Lake Storage**.

    ![Verbinding maken met Data Lake Store resultaat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In dit voor beeld wordt Data Lake Storage Gen1 gebruikt. Azure Data Lake Storage Gen2 is nu beschikbaar. Zie [Wat is Azure data Lake Storage gen1](./data-lake-store/data-lake-store-overview.md)voor meer informatie.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Een hand tekening voor gedeelde toegang in Storage Explorer genereren<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Shared Access Signature op account niveau

1. Klik met de rechter muisknop op het opslag account dat u wilt delen en selecteer vervolgens **Shared Access Signature ophalen**.

    ![Menu optie voor de context van de Shared Access-hand tekening ophalen][14]

1. Geef in **Shared Access Signature** het tijds bestek en de gewenste machtigingen voor het account op en selecteer vervolgens **maken**.

    ![Een Shared Access Signature ophalen][15]

1. Kopieer de **verbindings reeks** of de onbewerkte **query teken reeks** naar het klem bord.

### <a name="service-level-shared-access-signature"></a>Hand tekening voor gedeelde toegang op service niveau

U kunt een gedeelde toegangs handtekening verkrijgen op service niveau. Zie voor meer informatie [de SAS ophalen voor een BLOB-container](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Zoeken naar opslagaccounts

Als u een opslag resource wilt zoeken, kunt u zoeken in het deel venster **Verkenner** .

Wanneer u tekst in het zoekvak invoert, worden in Storage Explorer alle resources weer gegeven die overeenkomen met de zoek waarde die u tot dat punt hebt ingevoerd. In dit voor beeld wordt een zoek opdracht voor **eind punten** weer gegeven:

![Zoeken naar Storage-account][23]

> [!NOTE]
> Als u uw zoek opdracht wilt versnellen, gebruikt u **account beheer** om abonnementen te deselecteren die niet het item bevatten dat u zoekt. U kunt ook met de rechter muisknop op een knoop punt klikken en **hier zoeken** selecteren om te beginnen met zoeken vanaf een specifiek knoop punt.

## <a name="next-steps"></a>Volgende stappen

* [Azure Blob Storage-resources beheren met Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Werken met gegevens in Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store-resources beheren met Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
