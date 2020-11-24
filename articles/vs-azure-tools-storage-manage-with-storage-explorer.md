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
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545904"
---
# <a name="get-started-with-storage-explorer"></a>Aan de slag met Storage Explorer

## <a name="overview"></a>Overzicht

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage-gegevens kunt werken via Windows, macOS en Linux. In dit artikel leert u verschillende manieren om verbinding te maken met uw Azure Storage-accounts en hoe u deze kunt beheren.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Vereisten

# <a name="windows"></a>[Windows](#tab/windows)

De volgende versies van Windows-ondersteunings Storage Explorer:

* Windows 10 (aanbevolen)
* Windows 8
* Windows 7

Voor alle versies van Windows Storage Explorer .NET Framework 4.7.2 Mini maal vereist.

# <a name="macos"></a>[macOS](#tab/macos)

De volgende versies van macOS-ondersteunings Storage Explorer:

* macOS 10,12 Sierra en latere versies

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer is beschikbaar in de [snap Store](https://snapcraft.io/storage-explorer) voor de meest voorkomende distributies van Linux. We raden u aan de module Store te plaatsen voor deze installatie. Met de module Storage Explorer worden alle afhankelijkheden en updates geïnstalleerd wanneer nieuwe versies worden gepubliceerd in de snap Store.

Voor ondersteunde distributies, zie de [pagina gesnapte installatie](https://snapcraft.io/docs/installing-snapd).

Storage Explorer vereist het gebruik van een wachtwoord beheerder. Mogelijk moet u hand matig verbinding maken met een wachtwoord beheerder. U kunt Storage Explorer verbinding maken met het wachtwoord beheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer is ook beschikbaar als een *. tar. gz* -down load. U moet afhankelijkheden hand matig installeren. De volgende distributies van Linux-ondersteuning *. tar. gz* -installatie:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

De installatie van het *tar. gz* -netwerk werkt mogelijk met andere distributies, maar alleen deze vermelde items worden officieel ondersteund.

Zie [Linux-afhankelijkheden](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) in de Azure Storage Explorer Troubleshooting Guide (Engelstalig) voor meer informatie over het installeren van Storage Explorer op Linux.

---

## <a name="download-and-install"></a>Downloaden en installeren

Zie [Azure Storage Explorer](https://www.storageexplorer.com)als u Storage Explorer wilt downloaden en installeren.

## <a name="connect-to-a-storage-account-or-service"></a>Verbinding maken met een opslagaccount of -service

Storage Explorer biedt verschillende manieren om verbinding te maken met opslagaccounts. In het algemeen kunt u het volgende doen:

* [Meld u aan bij Azure om toegang te krijgen tot uw abonnementen en hun resources](#sign-in-to-azure)
* [Een specifieke opslag-of CosmosDB-resource koppelen](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

> [!NOTE]
> Om toegang te krijgen tot resources Nadat u zich hebt aangemeld, heeft Storage Explorer zowel beheer (Azure Resource Manager) als gegevenslaag machtigingen nodig. Dit betekent dat u de machtigingen voor Azure Active Directory (Azure AD) nodig hebt, waarmee u toegang krijgt tot uw opslag account, de containers in het account en de gegevens in de containers. Als u alleen machtigingen hebt op de gegevenslaag, kunt u overwegen om [een resource toe te voegen via Azure AD](#add-a-resource-via-azure-ad). Raadpleeg de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)) voor meer informatie over de specifieke machtigingen die Storage Explorer vereist.

1. Selecteer in Storage Explorer **View**  >  **account beheer** weer geven of selecteer de knop **accounts beheren** .

    ![Accounts beheren][1]

1. **Account beheer** geeft nu alle Azure-accounts weer die u hebt aangemeld bij. Selecteer **een account toevoegen** om verbinding te maken met een ander account.

1. Selecteer in **verbinding maken met Azure Storage** een Azure-cloud van **Azure-omgeving** om u aan te melden bij een nationale Cloud of een Azure stack. Nadat u uw omgeving hebt gekozen, selecteert u **volgende**.

    ![Optie om u aan te melden][2]

    Storage Explorer een pagina opent om u aan te melden. Zie voor meer informatie [verbinding maken met Storage Explorer met een Azure stack-abonnement of een opslag account](/azure-stack/user/azure-stack-storage-connect-se).

1. Nadat u zich hebt aangemeld met een Azure-account, worden het account en de Azure-abonnementen die aan dat account zijn gekoppeld, weer gegeven onder **account beheer**. Selecteer **alle abonnementen** om uw selectie te scha kelen tussen alle of geen van de vermelde Azure-abonnementen. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**.

    ![Selecteer Azure-abonnementen][3]

    In **Verkenner** worden de opslag accounts weer gegeven die zijn gekoppeld aan de geselecteerde Azure-abonnementen.

    ![Geselecteerde Azure-abonnementen][4]

### <a name="attach-a-specific-resource"></a>Een specifieke resource koppelen

Er zijn verschillende manieren om te koppelen aan een resource in Storage Explorer:

* [Voeg een resource toe via Azure AD](#add-a-resource-via-azure-ad). Als u alleen machtigingen hebt op de gegevenslaag, gebruikt u deze optie om een BLOB-container of een Azure Data Lake Storage Gen2 Blob Storage-container toe te voegen.
* [Gebruik een Connection String](#use-a-connection-string). Gebruik deze optie als u een connection string hebt voor een opslag account. Storage Explorer ondersteunt zowel de sleutel als de verbindings reeks voor de [hand tekening voor gedeelde toegang](./storage/common/storage-sas-overview.md) .
* [Gebruik een URI voor de Shared Access-hand tekening](#use-a-shared-access-signature-uri). Als u een [Shared Access Signature-URI](./storage/common/storage-sas-overview.md) hebt aan een BLOB-container, een bestands share, een wachtrij of een tabel, gebruikt u deze om aan de resource te koppelen. Als u een URI voor een gedeelde toegangs handtekening wilt ophalen, kunt u [Storage Explorer](#generate-a-sas-in-storage-explorer) of de [Azure Portal](https://portal.azure.com)gebruiken.
* [Gebruik een naam en sleutel](#use-a-name-and-key). Als u een van de account sleutels in uw opslag account kent, kunt u deze optie gebruiken om snel verbinding te maken. Zoek de sleutels op de pagina voor het opslag account door **instellingen**  >  **toegangs sleutels** te selecteren in de [Azure Portal](https://portal.azure.com).
* [Koppelen aan een lokale emulator](#attach-to-a-local-emulator). Als u een van de beschik bare Azure Storage-emulators gebruikt, gebruikt u deze optie om eenvoudig verbinding te maken met uw emulator.
* [Verbinding maken met een Azure Cosmos DB-account met behulp van een Connection String](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Gebruik deze optie als u een connection string hebt naar een CosmosDB-exemplaar.
* [Maak verbinding met Azure data Lake Store op basis van de URI](#connect-to-azure-data-lake-store-by-uri). Gebruik deze optie als u een URI hebt die moet worden Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Een resource toevoegen via Azure AD

1. Selecteer het **verbindings** symbool om **verbinding maken met Azure Storage** te openen.

    ![Verbinding maken met de optie Azure Storage][9]

1. Als u dit nog niet hebt gedaan, gebruikt u de optie **een Azure-account toevoegen** om u aan te melden bij het Azure-account dat toegang heeft tot de resource. Nadat u zich hebt aangemeld, keert u terug naar **verbinding maken met Azure Storage**.

1. Selecteer **een resource toevoegen via Azure Active Directory (Azure AD)** en selecteer vervolgens **volgende**.

1. Selecteer een Azure-account en-Tenant. Deze waarden moeten toegang hebben tot de opslag resource die u wilt koppelen. Selecteer **Next**.

1. Kies het resource type dat u wilt koppelen. Geef de gegevens op die nodig zijn om verbinding te maken. 

   De gegevens die u op deze pagina invoert, zijn afhankelijk van het type resource dat u wilt toevoegen. Zorg ervoor dat u het juiste type resource kiest. Nadat u de vereiste gegevens hebt ingevoerd, selecteert u **volgende**.

1. Bekijk de **samen vatting** van de verbinding om te controleren of alle gegevens correct zijn. Als dat het geval is, selecteert u **verbinding maken**. Selecteer anders **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur naar het knoop punt dat de verbinding vertegenwoordigt. De bron wordt weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**  >  **(gekoppelde containers)**  >  **BLOB-containers**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)).

#### <a name="use-a-connection-string"></a>Een verbindingsreeks gebruiken

1. Selecteer het **verbindings** symbool om **verbinding maken met Azure Storage** te openen.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **een Connection String gebruiken** en selecteer **volgende**.

1. Kies een weergave naam voor de verbinding en voer uw connection string in. Selecteer vervolgens **Volgende**.

1. Bekijk de **samen vatting** van de verbinding om te controleren of alle gegevens correct zijn. Als dat het geval is, selecteert u **verbinding maken**. Selecteer anders **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur naar het knoop punt dat de verbinding vertegenwoordigt. De bron wordt weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)).

#### <a name="use-a-shared-access-signature-uri"></a>Een URI voor een SAS gebruiken

1. Selecteer het **verbindings** symbool om **verbinding maken met Azure Storage** te openen.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **een SAS-URI (Shared Access Signature) gebruiken** en selecteer **volgende**.

1. Kies een weergave naam voor de verbinding en voer de URL voor de Shared Access-hand tekening in. Het service-eind punt voor het type resource dat u wilt koppelen, moet automatisch door voeren worden. Als u een aangepast eind punt gebruikt, is dit mogelijk niet het geval. Selecteer **Next**.

1. Bekijk de **samen vatting** van de verbinding om te controleren of alle gegevens correct zijn. Als dat het geval is, selecteert u **verbinding maken**. Selecteer anders **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur naar het knoop punt dat de verbinding vertegenwoordigt. De bron wordt weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**  >  **(gekoppelde containers)**  >  *het service knooppunt voor het type container dat u hebt gekoppeld*. Als Storage Explorer uw verbinding niet kunt toevoegen, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)). Raadpleeg de hand leiding voor het oplossen van problemen als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd.

#### <a name="use-a-name-and-key"></a>Een naam en sleutel gebruiken

1. Selecteer het **verbindings** symbool om **verbinding maken met Azure Storage** te openen.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **een opslag accountnaam en-sleutel gebruiken** en selecteer **volgende**.

1. Kies een weergave naam voor de verbinding.

1. Voer de naam van uw opslag account en een van de bijbehorende toegangs sleutels in.

1. Kies het **opslag domein** dat u wilt gebruiken en selecteer **volgende**.

1. Bekijk de **samen vatting** van de verbinding om te controleren of alle gegevens correct zijn. Als dat het geval is, selecteert u **verbinding maken**. Selecteer anders **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur naar het knoop punt dat de verbinding vertegenwoordigt. De bron wordt weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)).

#### <a name="attach-to-a-local-emulator"></a>Koppelen aan een lokale emulator

Storage Explorer ondersteunt momenteel twee officiële opslag emulators:

* [Azure Storage-emulator](storage/common/storage-use-emulator.md) (alleen Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS of Linux)

Als uw emulator luistert naar de standaard poorten, kunt u het knoop punt **emulator-standaard poorten** gebruiken om toegang te krijgen tot uw emulator. Zoek naar **emulator-standaard poorten** onder **lokale & gekoppelde**  >  **opslag accounts**.

Als u een andere naam voor de verbinding wilt gebruiken of als uw emulator niet wordt uitgevoerd op de standaard poorten, voert u de volgende stappen uit:

1. Start uw emulator. Voer de opdracht `AzureStorageEmulator.exe status` in om de poorten voor elk Service type weer te geven.

   > [!IMPORTANT]
   > De emulator wordt niet automatisch door Storage Explorer gestart. U moet deze hand matig starten.

1. Selecteer het **verbindings** symbool om **verbinding maken met Azure Storage** te openen.

    ![Verbinding maken met de optie Azure Storage][9]

1. Selecteer **koppelen aan een lokale emulator** en selecteer vervolgens **volgende**.

1. Kies een weergave naam voor de verbinding en voer de poorten in waarop uw emulator luistert voor elk Service type. **Als u verbinding wilt met een lokale emulator** , worden de standaard poort waarden voor de meeste emulators voorgesteld. De poort van de **bestanden** is leeg, omdat er momenteel geen officiële emulators zijn die de service files ondersteunen. Als de emulator die u gebruikt, ondersteuning biedt voor bestanden, kunt u de poort opgeven die u wilt gebruiken. Selecteer vervolgens **Volgende**.

1. Bekijk de **samen vatting** van de verbinding en zorg ervoor dat alle informatie juist is. Als dat het geval is, selecteert u **verbinding maken**. Selecteer anders **terug** om terug te keren naar de vorige pagina's om eventuele onjuiste gegevens op te lossen.

Nadat de verbinding is toegevoegd, gaat de resource structuur naar het knoop punt dat de verbinding vertegenwoordigt. Het knoop punt moet worden weer gegeven onder **lokale & gekoppelde**  >  **opslag accounts**. Als Storage Explorer uw verbinding niet kan toevoegen of als u geen toegang hebt tot uw gegevens nadat u de verbinding hebt toegevoegd, raadpleegt u de [Azure Storage Explorer Troubleshooting Guide (Engelstalig](./storage/common/storage-explorer-troubleshooting.md)).

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
>
>

## <a name="next-steps"></a>Volgende stappen

* [Azure Blob Storage-resources beheren met Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Werken met gegevens in Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store-resources beheren met Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png