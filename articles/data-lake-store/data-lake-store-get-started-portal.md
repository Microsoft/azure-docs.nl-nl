---
title: Aan de slag met Azure Data Lake Storage Gen1-Portal
description: Gebruik de Azure Portal om een Data Lake Storage Gen1 account te maken en basis bewerkingen in het account uit te voeren.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578577"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure Portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Informatie over het gebruik van de Azure Portal om een Data Lake Storage Gen1 account te maken en basis bewerkingen uit te voeren, zoals het maken van mappen, uploaden en downloaden van gegevens bestanden, verwijderen van uw account enzovoort. Zie [overzicht van Azure data Lake Storage gen1](data-lake-store-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende items:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken

1. Meld u aan bij de nieuwe [Azure Portal](https://portal.azure.com).
2. Klik op **een resource maken > opslag > data Lake Storage gen1**.
3. Geef op de Blade **nieuw data Lake Storage gen1** de waarden op zoals weer gegeven in de volgende scherm afbeelding:

    ![Een nieuw Data Lake Storage Gen1-account maken](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Een nieuw Data Lake Storage Gen1-account maken")

   * **Naam**. Voer een unieke naam in voor het Data Lake Storage Gen1-account.
   * **Abonnement**. Selecteer het abonnement waaronder u een nieuw Data Lake Storage Gen1 account wilt maken.
   * **Resourcegroep**. Selecteer een bestaande resourcegroep of selecteer de optie **Nieuwe maken** om er een te maken. Een resourcegroep is een container met verwante resources voor een toepassing. Zie [Resourcegroepen in Azure](../azure-resource-manager/management/overview.md#resource-groups) voor meer informatie.
   * **Locatie**: Selecteer een locatie waar u het data Lake Storage gen1 account wilt maken.
   * **Versleutelingsinstellingen**. Er zijn drie opties:

     * **Geen versleuteling inschakelen**.
     * **Gebruik sleutels die worden beheerd door data Lake Storage gen1** als u wilt dat data Lake Storage gen1 uw versleutelings sleutels beheert.
     * **Sleutels gebruiken uit uw eigen Key Vault**. U kunt een bestaande Azure Key Vault selecteren of een nieuwe Key Vault maken. Als u de sleutels van een Key Vault wilt gebruiken, moet u machtigingen voor het Data Lake Storage Gen1-account toewijzen voor toegang tot de Azure Key Vault. Zie [Machtigingen toewijzen aan Azure Key Vault](#assign-permissions-to-azure-key-vault) voor instructies.

        ![Scherm opname van de Blade nieuw Data Lake Storage gen 1 en het tabblad versleutelings instellingen.](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Versleuteling Data Lake Storage Gen1")

        Klik op **OK** in de blade **Versleutelingsinstellingen**.

        Zie [versleuteling van gegevens in azure data Lake Storage gen1](./data-lake-store-encryption.md)voor meer informatie.

4. Klik op **Create**. Als u ervoor hebt gekozen om het account vast te maken aan het dash board, gaat u terug naar het dash board en ziet u de voortgang van het inrichten van uw Data Lake Storage Gen1-account. Zodra het Data Lake Storage Gen1-account is ingericht, wordt de Blade account weer gegeven.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Machtigingen toewijzen aan Azure Key Vault

Als u sleutels van een Azure Key Vault hebt gebruikt om versleuteling voor het Data Lake Storage Gen1 account te configureren, moet u de toegang configureren tussen het Data Lake Storage Gen1-account en het Azure Key Vault-account. Volg hiervoor de volgende stappen.

1. Als u sleutels uit de Azure Key Vault hebt gebruikt, wordt in de Blade voor het Data Lake Storage Gen1-account een waarschuwing bovenaan weer gegeven. Klik op de waarschuwing om **Versleuteling** te openen.

    ![Scherm opname van de Blade Data Lake Storage Gen1 account met de waarschuwing met de tekst "sleutel kluis-machtigings configuratie vereist. Klik hier om het installatie programma in te stellen.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Versleuteling Data Lake Storage Gen1")
2. Op de blade ziet u twee opties om toegang te configureren.

    ![Scherm opname van de Blade versleuteling.](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Versleuteling Data Lake Storage Gen1")

   * Klik in de eerste optie op **Machtigingen verlenen** om toegang te configureren. De eerste optie is alleen ingeschakeld als de gebruiker die het Data Lake Storage Gen1 account heeft gemaakt ook een beheerder voor de Azure Key Vault is.
   * De andere optie is om de PowerShell-cmdlet uit te voeren die op de blade wordt weergegeven. U moet de eigenaar van de Azure Key Vault zijn of zijn gemachtigd om machtigingen voor de Azure Key Vault te verlenen. Wanneer u de cmdlet hebt uitgevoerd, keert u terug naar de blade en klikt u op **Inschakelen** om toegang te configureren.

> [!NOTE]
> U kunt ook een Data Lake Storage Gen1-account maken met behulp van Azure Resource Manager sjablonen. Deze sjablonen zijn toegankelijk vanaf [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Zonder gegevens versleuteling: [distribueer Azure data Lake Storage gen1 account zonder gegevens versleuteling](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Met gegevens versleuteling met behulp van Data Lake Storage Gen1: [Data Lake Storage gen1-account implementeren met versleuteling (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Met gegevens versleuteling met behulp van Azure Key Vault: [Data Lake Storage gen1-account implementeren met versleuteling (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Mappen maken

U kunt mappen maken onder uw Data Lake Storage Gen1-account om gegevens te beheren en op te slaan.

1. Open het Data Lake Storage Gen1-account dat u hebt gemaakt. Klik in het linkerdeel venster op **alle resources** en klik vervolgens op de Blade **alle resources** op de account naam waaronder u mappen wilt maken. Als u het account hebt vastgemaakt aan het startboard, klikt u op de tegel voor dat account.
2. Klik op de Blade Data Lake Storage Gen1 account op **Data Explorer**.

    ![Scherm opname van de Blade Data Lake Storage gen 1 met de optie Data Explorer aangeroepen.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Mappen maken in een Data Lake Storage Gen1-account")
3. Klik op de blade Data Explorer op **Nieuwe map**, voer een naam in voor de nieuwe map en klik op **OK**.

    ![Scherm afbeelding van de Blade Data Explorer met de optie nieuwe map en het tekstvak nieuwe map maken heet out.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Mappen maken in een Data Lake Storage Gen1-account")

    De zojuist gemaakte map wordt vermeld op de blade **Data Explorer**. U kunt geneste mappen tot elk gewenst niveau maken.

    ![Mappen maken in een Data Lake-account](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Mappen maken in een Data Lake-account")

## <a name="upload-data"></a><a name="uploaddata"></a>Gegevens uploaden

U kunt uw gegevens rechtstreeks naar een Data Lake Storage Gen1-account uploaden op het hoofd niveau of naar een map die u in het account hebt gemaakt.

1. Klik op de blade **Data Explorer** op **Uploaden**.
2. Navigeer op de blade **Bestanden uploaden** naar de bestanden die u wilt uploaden en klik op **Geselecteerde bestanden toevoegen**. U kunt meer dan één bestand selecteren om te uploaden.

    ![Gegevens uploaden](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Gegevens uploaden")

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Acties die beschikbaar zijn voor de opgeslagen gegevens

Klik op het beletselteken bij een bestand en klik in het pop-upmenu op de actie die u wilt uitvoeren met de gegevens.

![Eigenschappen van de gegevens](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Eigenschappen van de gegevens")

## <a name="secure-your-data"></a>Uw gegevens beveiligen

U kunt de gegevens die zijn opgeslagen in uw Data Lake Storage Gen1-account beveiligen met behulp van Azure Active Directory en toegangs beheer (Acl's). Zie [gegevens beveiligen in azure data Lake Storage gen1](data-lake-store-secure-data.md)voor instructies over hoe u dit doet.

## <a name="delete-your-account"></a>Uw account verwijderen

Als u een Data Lake Storage Gen1 account wilt verwijderen, klikt u op de Blade Data Lake Storage Gen1 op **verwijderen**. Om de actie te bevestigen, wordt u gevraagd de naam in te voeren van het account dat u wilt verwijderen. Voer de naam in van het account en klik op **Verwijderen**.

![Data Lake Storage Gen1 account verwijderen](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-account verwijderen")

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Lake Storage Gen1 gebruiken voor big data vereisten](data-lake-store-data-scenarios.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
