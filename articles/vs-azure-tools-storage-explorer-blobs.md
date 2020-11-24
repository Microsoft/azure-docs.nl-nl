---
title: Azure Blob Storage-resources beheren met Storage Explorer | Microsoft Docs
description: Beheer Azure Blob Storage-resources met Storage Explorer. Maak een BLOB-container, Bekijk de inhoud van de BLOB-container, verwijder of kopieer een BLOB-container en meer.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95531964"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Azure Blob Storage-resources beheren met Storage Explorer

## <a name="overview"></a>Overzicht

[Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) is een service voor het opslaan van grote hoeveel heden ongestructureerde gegevens, zoals tekst of binaire gegevens, die overal ter wereld toegankelijk zijn via http of https.
U kunt Blob Storage gebruiken om gegevens openbaar te maken of om toepassingsgegevens privé op te slaan. In dit artikel leert u hoe u Storage Explorer kunt gebruiken om te werken met Blob-containers en-blobs.

## <a name="prerequisites"></a>Vereisten

U moet het volgende doen om de stappen in dit artikel uit te voeren:

* [Storage Explorer downloaden en installeren](https://www.storageexplorer.com)
* [Verbinding maken met een Azure-opslag account of-service](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Een blobcontainer maken

Alle blobs moeten zich in een BLOB-container bevinden. Dit is simpelweg een logische groepering van blobs. Een account kan een onbeperkt aantal containers bevatten en elke container kan een onbeperkt aantal blobs opslaan.

De volgende stappen laten zien hoe u een BLOB-container maakt in Storage Explorer.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit waarin u de blobcontainer wilt maken.
3. Klik met de rechter muisknop op **BLOB-containers** en selecteer **BLOB-container maken** in het context menu.

   ![Het contextmenu Blobcontainers maken][0]
4. Er wordt een tekstvak onder de map **BLOB containers** weer gegeven. Voer een naam in voor de blobcontainer. Zie [een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor informatie over regels en beperkingen voor het benoemen van BLOB-containers.

   ![Tekstvak BLOB containers maken][1]
5. Druk op **Enter** om de blobcontainer te maken of op **Esc** om te annuleren. Zodra de BLOB-container is gemaakt, wordt deze weer gegeven onder de map **BLOB containers** voor het geselecteerde opslag account.

   ![BLOB-container gemaakt][2]

## <a name="view-a-blob-containers-contents"></a>De inhoud van een BLOB-container weer geven

BLOB-containers bevatten blobs en mappen (die ook blobs kunnen bevatten).

De volgende stappen laten zien hoe u de inhoud van een BLOB-container in Storage Explorer kunt bekijken:

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container die u wilt weer geven.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Klik met de rechter muisknop op de BLOB-container die u wilt weer geven en selecteer in het context menu **Open BLOB container editor**.
   U kunt ook dubbel klikken op de BLOB-container die u wilt weer geven.

   ![Context menu van BLOB-container editor openen][19]
5. In het hoofd venster wordt de inhoud van de BLOB-container weer gegeven.

   ![BLOB-container editor][3]

## <a name="delete-a-blob-container"></a>Een BLOB-container verwijderen

U kunt BLOB-containers eenvoudig maken en verwijderen als dat nodig is. (Als u wilt zien hoe u afzonderlijke blobs verwijdert, raadpleegt u de sectie, [blobs beheren in een BLOB-container](#managing-blobs-in-a-blob-container).)

De volgende stappen laten zien hoe u een BLOB-container in Storage Explorer kunt verwijderen:

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container die u wilt weer geven.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Klik met de rechter muisknop op de BLOB-container die u wilt verwijderen en selecteer **verwijderen** in het context menu.
   U kunt ook op **verwijderen** drukken om de geselecteerde BLOB-container te verwijderen.

   ![Context menu van BLOB-container verwijderen][4]
5. Selecteer **Ja** in het bevestigingsvenster.

   ![Bevestiging van BLOB-container verwijderen][5]

## <a name="copy-a-blob-container"></a>Een BLOB-container kopiëren

Met Storage Explorer kunt u een BLOB-container naar het klem bord kopiëren en vervolgens die BLOB-container in een ander opslag account plakken. (Als u wilt zien hoe u afzonderlijke blobs kopieert, raadpleegt u de sectie, [blobs beheren in een BLOB-container](#managing-blobs-in-a-blob-container).)

De volgende stappen laten zien hoe u een BLOB-container kopieert van het ene opslag account naar het andere.

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container die u wilt kopiëren.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Klik met de rechter muisknop op de BLOB-container die u wilt kopiëren en selecteer **BLOB-container kopiëren** in het context menu.

   ![Context menu van BLOB-container kopiëren][6]
5. Klik met de rechter muisknop op het gewenste ' doel '-opslag account waarin u de BLOB-container wilt plakken en selecteer in het context menu de optie **BLOB-container plakken**.

   ![Context menu van BLOB-container plakken][7]

## <a name="get-the-sas-for-a-blob-container"></a>De SAS ophalen voor een blobcontainer

Een [Shared Access Signature (SAS)](./storage/common/storage-sas-overview.md) biedt gedelegeerde toegang tot resources in uw opslag account.
Dit betekent dat u een client gedurende de opgegeven periode een beperkte set machtigingen kunt verlenen voor objecten in uw opslagaccount, zonder dat u hiervoor de toegangssleutels voor het account hoeft te delen.

De volgende stappen laten zien hoe u een SAS voor een BLOB-container maakt:

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container waarvoor u een SAS wilt ophalen.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Klik met de rechter muisknop op de gewenste BLOB-container en selecteer in het context menu **Get Shared Access Signature**.

   ![Het context menu van SAS ophalen][8]
5. Geef in het dialoogvenster **Handtekening voor gedeelde toegang** waarden op voor het beleid, de begin- en eindtijd, de tijdzone en de toegangsniveaus die u wilt gebruiken voor de resource.

   ![SAS-opties ophalen][9]
6. Als u alle SAS-opties hebt opgegeven, selecteert u **Maken**.
7. Er wordt dan een tweede **Shared Access Signature** dialoog venster weer gegeven met daarin de BLOB-container, samen met de URL en query strings die u kunt gebruiken voor toegang tot de opslag resource.
   Selecteer **Kopiëren** naast de URL die u naar het klembord wilt kopiëren.

   ![SAS-Url's kopiëren][10]
8. Als u klaar bent, selecteert u **Sluiten**.

## <a name="manage-access-policies-for-a-blob-container"></a>Toegangs beleid voor een BLOB-container beheren

De volgende stappen laten zien hoe u toegangs beleid voor een BLOB-container kunt beheren (toevoegen en verwijderen):

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container waarvan u het toegangs beleid wilt beheren.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Selecteer de gewenste BLOB-container en selecteer in het context menu **toegangs beleid beheren**.

   ![Contextmenu Toegangsbeleid beheren][11]
5. Het dialoog venster **toegangs beleid** bevat een lijst met alle beleids regels voor toegang die al zijn gemaakt voor de geselecteerde BLOB-container.

   ![Opties voor toegangs beleid][12]
6. Volg hieronder de stappen voor de beleidsbeheertaak die u wilt uitvoeren:

   * **Een nieuw toegangsbeleid toevoegen**: selecteer **Toevoegen**. Als de bewerking is voltooid, ziet u het dialoogvenster **Toegangsbeleid** met het zojuist toegevoegde toegangsbeleid (met de standaardinstellingen).
   * **Bewerk een toegangs beleid** : Breng de gewenste wijzigingen aan en selecteer **Opslaan**.
   * **Een toegangsbeleid verwijderen**: selecteer **Verwijderen** naast het toegangsbeleid dat u wilt verwijderen.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Het niveau van de open bare toegang voor een BLOB-container instellen

Standaard is elke BLOB-container ingesteld op ' geen open bare toegang '.

De volgende stappen laten zien hoe u een openbaar toegangs niveau kunt opgeven voor een BLOB-container.

1. Open Storage Explorer.
2. Vouw in het linkerdeel venster het opslag account uit met de BLOB-container waarvan u het toegangs beleid wilt beheren.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Selecteer de gewenste BLOB-container en klik in het context menu op **openbaar toegangs niveau instellen**.

   ![Context menu voor openbaar toegangs niveau instellen][13]
5. Geef in het dialoog venster toegankelijk niveau van de **container instellen** het gewenste toegangs niveau op.

   ![Opties voor openbaar toegangs niveau instellen][14]
6. Selecteer **Toepassen**.

## <a name="managing-blobs-in-a-blob-container"></a>Blobs in een BLOB-container beheren

Zodra u een BLOB-container hebt gemaakt, kunt u een BLOB uploaden naar die BLOB-container, een BLOB downloaden naar uw lokale computer, een BLOB openen op uw lokale computer en nog veel meer.

De volgende stappen laten zien hoe u de blobs (en mappen) binnen een BLOB-container beheert.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit met de blob-container die u wilt beheren.
3. Vouw de **BLOB-containers** van het opslag account uit.
4. Dubbel klik op de BLOB-container die u wilt weer geven.
5. In het hoofd venster wordt de inhoud van de BLOB-container weer gegeven.

   ![BLOB-container weer geven][3]
6. In het hoofd venster wordt de inhoud van de BLOB-container weer gegeven.
7. Volg hieronder de stappen voor de bewerking die u wilt uitvoeren:

   * **Bestanden uploaden naar een BLOB-container**

     1. Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Bestanden uploaden** in de vervolgkeuzelijst.

        ![Menu voor Bestanden uploaden][15]
     2. Selecteer in het dialoogvenster **Bestanden uploaden** de knop met de weglatingstekens (**...**) rechts van het tekstvak **Bestanden** om de bestanden te selecteren die u wilt uploaden.

        ![Opties voor uploaden van bestanden][16]
     3. Geef het type **BLOB-type** op. Zie [een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor meer informatie.
     4. U kunt desgewenst een doelmap opgeven waarin de geselecteerde bestanden worden geüpload. Als de doelmap niet bestaat, wordt deze gemaakt.
     5. Selecteer **Uploaden**.
   * **Een map uploaden naar een BLOB-container**

     1. Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Map uploaden** in de vervolgkeuzelijst.

        ![Menu voor uploaden van map][17]
     2. Selecteer in het dialoogvenster **Map uploaden** de knop met de weglatingstekens (**...**) rechts van het tekstvak **Map** om de map te selecteren waarvan u de inhoud wilt uploaden.

        ![Opties voor het uploaden van mappen][18]
     3. Geef het type **BLOB-type** op. Zie [een container maken](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) voor meer informatie.
     4. Geef desgewenst een doelmap op waarin de inhoud van de geselecteerde map moet worden geüpload. Als de doelmap niet bestaat, wordt deze gemaakt.
     5. Selecteer **Uploaden**.
   * **Een BLOB downloaden naar uw lokale computer**

     1. Selecteer de blob die u wilt downloaden.
     2. Selecteer **Downloaden** op de werkbalk van het hoofdvenster.
     3. In het dialoog venster **opgeven waar u het gedownloade BLOB wilt opslaan** , geeft u de locatie op waar de BLOB moet worden gedownload en de naam die u wilt geven.  
     4. Selecteer **Opslaan**.
   * **Een BLOB op uw lokale computer openen**

     1. Selecteer de blob die u wilt openen.
     2. Selecteer **Openen** op de werkbalk van het hoofdvenster.
     3. De BLOB wordt gedownload en geopend met de toepassing die is gekoppeld aan het onderliggende bestands type van de blob.
   * **Een BLOB naar het klem bord kopiëren**

     1. Selecteer de blob die u wilt kopiëren.
     2. Selecteer **Kopiëren** op de werkbalk van het hoofdvenster.
     3. Navigeer in het linkerdeel venster naar een andere blob-container en dubbel klik erop om deze weer te geven in het hoofd venster.
     4. Selecteer **Plakken** op de werk balk van het hoofd venster om een kopie van de BLOB te maken.
   * **Een blob verwijderen**

     1. Selecteer de blob die u wilt verwijderen.
     2. Selecteer **Verwijderen** op de werkbalk van het hoofdvenster.
     3. Selecteer **Ja** in het bevestigingsvenster.

## <a name="next-steps"></a>Volgende stappen

* De [meest recente releaseopmerkingen en video's van Storage Explorer](https://www.storageexplorer.com) bekijken.
* Meer informatie lezen over [het maken van toepassingen met behulp van blobs, tabellen, wachtrijen en bestanden van Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png