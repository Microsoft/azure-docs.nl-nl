---
title: Storage Explorer gebruiken met Azure File Storage | Microsoft Docs
description: Lees hier hoe u Storage Explorer kunt gebruiken om te werken met bestandsshares en bestanden.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488388"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Opslagverkenner gebruiken met Azure File Storage

Azure File Storage is een service die bestandsshares in de cloud aanbiedt met behulp van het standaard SMB-protocol (Server Message Block). Zowel SMB 2.1 als SMB 3.0 wordt ondersteund. Met Azure File Storage kunt u oudere toepassingen die afhankelijk zijn van bestandsshares, snel en zonder kostbare regeneraties naar Azure migreren. U kunt File Storage gebruiken om gegevens openbaar te maken of om toepassingsgegevens privé op te slaan. In dit artikel leest u hoe u Storage Explorer kunt gebruiken om te werken met bestandsshares en bestanden.

## <a name="prerequisites"></a>Vereisten

U moet het volgende doen om de stappen in dit artikel uit te voeren:

- [Storage Explorer downloaden en installeren](https://www.storageexplorer.com/)

- [Verbinding maken met een Azure-opslag account of-service](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Een bestandsshare maken

Alle bestanden moeten zich bevinden in een bestandsshare, wat in feite niets meer is dan een logische groepering van bestanden. Een account kan een onbeperkt aantal bestandsshares bevatten en elke share kan een onbeperkt aantal bestanden bevatten.

Voer de volgende stappen uit om een bestandsshare te maken binnen Storage Explorer.

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit waarin u de bestandsshare wilt maken.

1. Klik met de rechtermuisknop op **Bestandsshares** en selecteer **Bestandsshare maken** in het contextmenu.

    ![Bestandsshare maken](media/vs-azure-tools-storage-explorer-files/image1.png)

1. U ziet een tekstvak onder de map **Bestandsshares**. Voer een naam in voor de bestandsshare. Zie de sectie [Naamgevingsregels voor shares](./storage/blobs/storage-quickstart-blobs-dotnet.md) voor een lijst van regels en beperkingen voor namen van bestandsshares.

    ![Een naam opgeven voor de share](media/vs-azure-tools-storage-explorer-files/image2.png)

1. Druk op **Enter** om de bestandsshare te maken of op **Esc** om te annuleren. Als de bestandsshare is gemaakt, wordt deze weergegeven onder de map **Bestandsshares** voor het geselecteerde opslagaccount.

    ![De nieuwe share](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>De inhoud van een bestandsshare weergeven

Bestandsshares bevatten bestanden en mappen (die ook weer bestanden kunnen bevatten).

Volg de volgende stappen om de inhoud van een bestandsshare weer te geven binnen Storage Explorer:+

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare die u wilt weergeven.

1. Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1. Klik met de rechtermuisknop op de bestandsshare die u wilt weergeven en selecteer **Openen** in het contextmenu. U kunt ook dubbelklikken op de bestandsshare die u wilt weergeven.

    ![Share openen](media/vs-azure-tools-storage-explorer-files/image4.png)

1. U ziet de inhoud van de bestandsshare in het hoofdvenster.
    
    ![Scherm opname van het hoofd venster voor een bestands share in Storage Explorer de inhoud van de share wordt weer gegeven.](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Een bestandsshare verwijderen

U kunt op ieder moment bestandsshares toevoegen en verwijderen. Zie [Managing files in a file share](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) (Bestanden beheren in een bestandsshare) voor informatie over het verwijderen van afzonderlijke bestanden.

Voer de volgende stappen uit om een bestandsshare te wissen binnen Storage Explorer:

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare die u wilt weergeven.

1. Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1. Klik met de rechtermuisknop op de bestandsshare die u wilt verwijderen en selecteer **Verwijderen** in het contextmenu. U kunt ook op **Delete** drukken om de geselecteerde bestandsshare te verwijderen.

    ![Verwijderen](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Selecteer **Ja** in het bevestigingsvenster.
    
    ![Bevestigingsvenster](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Een bestandsshare kopiëren

U kunt met Storage Explorer een bestandsshare naar het klembord kopiëren en de bestandsshare vervolgens in een ander opslagaccount plakken. Zie [Managing files in a file share](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) (Bestanden beheren in een bestandsshare) voor informatie over het kopiëren van afzonderlijke bestanden.

In de volgende stappen ziet u hoe u een bestandsshare van het ene opslagaccount naar het andere kopieert.

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare die u wilt kopiëren.

1. Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1. Klik met de rechtermuisknop op de bestandsshare die u wilt kopiëren en selecteer **Bestandsshare kopiëren** in het contextmenu.

    ![Bestandsshare kopiëren](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Klik met de rechtermuisknop op het opslagaccount waarnaar u de bestandsshare wilt plakken en selecteer **Bestandsshare plakken** in het contextmenu.

    ![Bestandsshare plakken](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>De SAS voor een bestandsshare ophalen

Een [Shared Access Signature (SAS)](./storage/common/storage-sas-overview.md) biedt gedelegeerde toegang tot resources in uw opslag account. Dit betekent dat u een client gedurende de opgegeven periode een beperkte set machtigingen kunt verlenen voor objecten in uw opslagaccount, zonder dat u hiervoor de toegangssleutels voor het account hoeft te delen.

Voer de volgende stappen uit om een SAS te maken voor een bestandsshare:

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare waarvoor u een SAS wilt ophalen.

1. Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1. Klik met de rechtermuisknop op de gewenste bestandsshare en selecteer **Handtekening voor gedeelde toegang ophalen** in het contextmenu.

    ![Handtekening voor gedeelde toegang ophalen](media/vs-azure-tools-storage-explorer-files/image10.png)

1. Geef in het dialoogvenster **Handtekening voor gedeelde toegang** waarden op voor het beleid, de begin- en eindtijd, de tijdzone en de toegangsniveaus die u wilt gebruiken voor de resource.

    ![SAS-dialoogvenster](media/vs-azure-tools-storage-explorer-files/image11.png)

1. Als u alle SAS-opties hebt opgegeven, selecteert u **Maken**.

1. U ziet een tweede dialoogvenster **Handtekening voor gedeelde toegang** met de bestandsshare en de URL en queryreeksen waarmee u toegang kunt krijgen tot de opslagbron. Selecteer **Kopiëren** naast de URL die u naar het klembord wilt kopiëren.
    
    ![Tweede SAS-dialoogvenster](media/vs-azure-tools-storage-explorer-files/image12.png)

1. Als u klaar bent, selecteert u **Sluiten**.

## <a name="manage-access-policies-for-a-file-share"></a>Toegangsbeleid voor een bestandsshare beheren

In de volgende stappen ziet u hoe u toegangsbeleid voor een bestandsshare kunt beheren (toevoegen en verwijderen). Toegangsbeleid wordt gebruikt om URL's voor SAS te maken waarmee personen gedurende een bepaalde periode de resource Opslagbestand kunnen gebruiken.

1. Open Storage Explorer.

1. Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare waarvan u het toegangsbeleid wilt wijzigen.

1. Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1. Selecteer de gewenste bestandsshare en selecteer **Toegangsbeleid beheren** in het contextmenu.

    ![Contextmenu Toegangsbeleid beheren](media/vs-azure-tools-storage-explorer-files/image13.png)

1. Het dialoogvenster **Toegangsbeleid** bevat alle toegangsregels die al zijn gemaakt voor de geselecteerde bestandsshare.
    
    ![Toegangsbeleid](media/vs-azure-tools-storage-explorer-files/image14.png)

1. Volg hieronder de stappen voor de beleidsbeheertaak die u wilt uitvoeren:
    
    - **Een nieuw toegangsbeleid toevoegen**: selecteer **Toevoegen**. Als de bewerking is voltooid, ziet u het dialoogvenster **Toegangsbeleid** met het zojuist toegevoegde toegangsbeleid (met de standaardinstellingen).

    - **Een toegangsbeleid bewerken**: breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    - **Een toegangsbeleid verwijderen**: selecteer **Verwijderen** naast het toegangsbeleid dat u wilt verwijderen.

1. Maak een nieuwe SAS-URL met behulp van het toegangsbeleid dat u eerder hebt gemaakt:
    
    ![SAS ophalen](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![Naam en eigenschappen va SAS](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Bestanden in een bestandsshare beheren

Als u een bestandsshare hebt gemaakt, kunt u een bestand uploaden naar die bestandsshare, een bestand downloaden naar de lokale computer, een bestand openen op uw lokale computer en nog veel meer.

De volgende stappen laten zien hoe u de bestanden (en mappen) in een bestandsshare beheert.

1.  Open Storage Explorer.

1.  Vouw in het linkerdeelvenster het opslagaccount uit met de bestandsshare die u wilt beheren.

1.  Vouw het gedeelte **Bestandsshares** van het opslagaccount uit.

1.  Dubbelklik op de bestandsshare die u wilt weergeven.

1.  U ziet de inhoud van de bestandsshare in het hoofdvenster.

    ![Scherm opname van het hoofd venster voor de bestands share myazurefileshare in Storage Explorer, waarin de inhoud van de share met de eerste geselecteerde map wordt weer gegeven.](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  U ziet de inhoud van de bestandsshare in het hoofdvenster.

1.  Volg hieronder de stappen voor de bewerking die u wilt uitvoeren:

    - **Bestanden uploaden naar een bestandsshare**

        a.  Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Bestanden uploaden** in de vervolgkeuzelijst.

        ![Bestanden uploaden](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. Selecteer in het dialoogvenster **Bestanden uploaden** de knop met de weglatingstekens (**...**) rechts van het tekstvak **Bestanden** om de bestanden te selecteren die u wilt uploaden.

        ![Bestanden toevoegen](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Selecteer **Uploaden**.

    - **Een bestand uploaden naar een bestandsshare**
        
        a. Selecteer **Uploaden** op de werkbalk van het hoofdvenster en selecteer vervolgens **Map uploaden** in de vervolgkeuzelijst.

        ![Menu voor uploaden van map](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. Selecteer in het dialoogvenster **Map uploaden** de knop met de weglatingstekens (**...**) rechts van het tekstvak **Map** om de map te selecteren waarvan u de inhoud wilt uploaden.

        c. Geef desgewenst een doelmap op waarin de inhoud van de geselecteerde map moet worden geüpload. Als de doelmap niet bestaat, wordt deze gemaakt.

        d. Selecteer **Uploaden**.

    - **Een bestand downloaden naar de lokale computer**
        
        a. Selecteer het bestand dat u wilt downloaden.
        
        b. Selecteer **Downloaden** op de werkbalk van het hoofdvenster.
        
        c. Geef in het dialoogvenster **Locatie voor opslaan van gedownloade bestand** op waar u het bestand wilt downloaden en onder welke naam.

        d. Selecteer **Opslaan**.

    - **Een bestand op de lokale computer openen**
        
        a.  Selecteer het bestand dat u wilt openen.
        
        b.  Selecteer **Openen** op de werkbalk van het hoofdvenster.
        
        c.  Het bestand wordt gedownload en geopend met de toepassing die is gekoppeld aan het onderliggende bestandstype van het bestand.

    - **Een bestand naar het klembord kopiëren**

        a. Selecteer het bestand dat u wilt kopiëren.

        b. Selecteer **Kopiëren** op de werkbalk van het hoofdvenster.

        c. Ga in het linkerdeelvenster naar een andere bestandsshare en dubbelklik hierop om de share weer te geven in het hoofdvenster.

        d. Selecteer **Plakken** op de werkbalk van het hoofdvenster om een kopie van het bestand te maken.

    - **Een bestand verwijderen**

        a. Selecteer het bestand dat u wilt verwijderen.

        b. Selecteer **Verwijderen** op de werkbalk van het hoofdvenster.

        c. Selecteer **Ja** in het bevestigingsvenster.

## <a name="next-steps"></a>Volgende stappen

- De [meest recente releaseopmerkingen en video's van Storage Explorer](https://www.storageexplorer.com/) bekijken.

- Meer informatie lezen over [het maken van toepassingen met behulp van blobs, tabellen, wachtrijen en bestanden van Azure](https://azure.microsoft.com/documentation/services/storage/).