---
title: Quickstart voor Microsoft Azure Data Box Disk | Microsoft Docs
description: Gebruik deze quickstart om uw Azure Data Box Disk snel te implementeren in de Azure-portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 241b7c0c07d1fbaa6a43c6be4b264424612f538a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869037"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Quickstart: Azure Data Box Disk implementeren met behulp van de Azure-portal

In deze quickstart wordt beschreven hoe u de Azure Data Box Disk implementeert met de Azure-portal. De stappen omvatten hoe u snel een order maakt, schijven ontvangt, uitpakt en verbindt, en gegevens naar schijven kopieert zodat ze naar Azure worden geüpload.

Ga voor gedetailleerde stapsgewijze instructies voor implementatie en tracering naar [Zelfstudie: Azure Data Box Disk bestellen](data-box-disk-deploy-ordered.md). 

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true) aan.

::: zone-end

::: zone target="chromeless"

In deze handleiding worden de stappen beschreven voor gebruik van de Azure Data Box Disk in de Azure-portal. Deze handleiding is handig bij het beantwoorden van de volgende vragen.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

- Zorg ervoor dat uw abonnement is ingeschakeld voor de Azure Data Box-service. [Registreer u voor de service](https://aka.ms/azuredataboxfromdiskdocs) om uw abonnement in te schakelen voor deze service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Vereisten controleren**: Controleer het aantal schijven en kabels, het besturingssysteem en overige software.
> - **Aansluiten en ontgrendelen**: Sluit het apparaat aan en ontgrendel de schijf om de gegevens te kopiëren.
> - **Gegevens kopiëren naar de schijf en valideren**: Kopieer de gegevens naar de schijven in de van tevoren gemaakte mappen.
> - **Schijven retourneren**: Retourneer de schijven naar het Azure-datacenter waar de gegevens zijn geüpload naar uw opslagaccount.
> - **Gegevens in Azure controleren**: Controleer of uw gegevens zijn geüpload naar uw opslagaccount voordat u deze van de server met brongegevens verwijdert.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Bestellen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Deze stap neemt ongeveer 5 minuten in beslag.

1. Maak een nieuwe Azure Data Box-resource in de Azure-portal. 
2. Selecteer een abonnement dat is ingeschakeld voor deze service, en kies **Importeren** als overdrachtstype. Geef het **Bronland** op waar de gegevens zijn opgeslagen, en geef de **Azure-doelregio** voor de gegevensoverdracht op.
3. Selecteer **Data Box Disk**. De maximale oplossingscapaciteit is 35 TB, en voor grotere hoeveelheden gegevens kunt u meerdere schijforders maken.  
4. Voer de order- en verzendgegevens in. Als de service beschikbaar is in uw regio, geeft u e-mailadressen voor meldingen op, controleert u de samenvatting en maakt u vervolgens de order.

Zodra de order is gemaakt, worden de schijven voorbereid voor verzending.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik deze Azure CLI-opdrachten om een Data Box Disk-taak te maken.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Gebruik de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) om een opslagaccount te maken, of gebruik een bestaand opslagaccount:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Voer de opdracht [az databox job create](/cli/azure/databox/job#az_databox_job_create) uit om een Data Box-taak te maken met de SKU DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Voer de opdracht [az databox job update](/cli/azure/databox/job#az_databox_job_update) uit om een taak bij te werken, zoals in dit voorbeeld, waar u de naam en het e-mailadres van een contactpersoon wijzigt:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Voer de opdracht [az databox job show](/cli/azure/databox/job#az_databox_job_show) uit om informatie te krijgen over de taak:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Gebruik de opdracht [az databox job list]( /cli/azure/databox/job#az_databox_job_list) om alle Data Box-taken voor een resourcegroep te zien:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Voer de opdracht [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) uit om een taak te annuleren:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Voer de opdracht [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) uit om een taak te verwijderen:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Gebruik de opdracht [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) om de referenties voor een Data Box-taak weer te geven:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Zodra de order is gemaakt, wordt het apparaat voorbereid voor verzending.

---

## <a name="unpack"></a>Uitpakken

Deze stap neemt ongeveer 5 minuten in beslag.

De Data Box Disks worden verzonden in een UPS Express Box. Open de doos en controleer of deze het volgende bevat:

- Eén tot vijf USB-schijven in bubbeltjesplastic.
- Eén verbindingskabel per schijf.
- Een verzendlabel voor retourzending.

## <a name="connect-and-unlock"></a>Verbinden en ontgrendelen

Deze stap neemt ongeveer 5 minuten in beslag.

1. Gebruik de meegeleverde kabel om de schijf aan te sluiten op een Windows-/Linux-computer met een ondersteunde versie. Ga naar [Systeemvereisten voor Azure Data Box Disk](data-box-disk-system-requirements.md) voor meer informatie over ondersteunde besturingssysteemversies. 
2. Ontgrendel de schijf als volgt:

    1. Ga in de Azure-portal naar **Algemeen > Apparaatdetails** en haal de wachtwoordsleutel op.
    2. Download en extraheer het Data Box Disk-ontgrendelingsprogramma voor het specifieke besturingssysteem op de computer die wordt gebruikt om de gegevens naar schijven te kopiëren. 
    3. Voer het Data Box Disk-ontgrendelingsprogramma uit en geef de wachtwoordsleutel op. Voor elke schijf die opnieuw wordt geplaatst, voert u het ontgrendelingshulpprogramma opnieuw uit en geeft de wachtwoordsleutel op. **Gebruik niet het BitLocker-dialoogvenster of de BitLocker-sleutel voor het ontgrendelen van de schijf.** Ga naar [Schijven ontgrendelen op een Windows-client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) of [Schijven ontgrendelen op een Linux-client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) voor meer informatie over het ontgrendelen van schijven.
    4. De stationsletter die is toegewezen aan de schijf wordt weergegeven in het hulpprogramma. Noteer de letter van het schijfstation. Deze wordt in de volgende stappen gebruikt.

## <a name="copy-data-and-validate"></a>Gegevens kopiëren en valideren

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Het station bevat de mappen *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* en *DataBoxDiskImport*. De gegevens die als blok-blobs moeten worden geïmporteerd in de *BlockBlob*-map, kunt u slepen en neerzetten om ze te kopiëren. Kopieer op dezelfde manier gegevens als VHD/VHDX naar de map *PageBlob* en bijbehorende gegevens naar *AzureFile*. Kopieer de VHD's, die u als beheerde schijven wilt uploaden, naar een map onder *ManagedDisk*.

    Er wordt voor elke submap onder de *BlockBlob*- en *PageBlob*-map een container gemaakt in het Azure-opslagaccount. Er wordt een bestandsshare gemaakt voor een submap onder *AzureFile*.

    Alle bestanden onder de mappen *BlockBlob* en *PageBlob* worden naar een standaardcontainer `$root` onder het Azure-opslagaccount gekopieerd. Kopieer bestanden naar een map in *AzureFile*. Bestanden die rechtstreeks naar de map *AzureFile* worden gekopieerd, mislukken en worden geüpload als blok-blobs.

    > [!NOTE]
    > - Alle containers, blobs en bestanden moeten voldoen aan de [Azure-naamgevingsregels](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Als u zich niet aan deze regels houdt, mislukt de gegevensupload naar Azure.
    > - Zorg ervoor dat de bestanden de circa 4,75 TiB voor blok-blobs, circa 8 TiB voor pagina-blobs en circa 1 TiB voor Azure Files niet overschrijden.

2. **(Optioneel, maar aanbevolen)** Nadat het kopiëren is voltooid, wordt u ten zeerste aangeraden dat u in elk geval de `DataBoxDiskValidation.cmd` in map *DataBoxDiskImport* uitvoert en optie 1 selecteert om de bestanden te valideren. U wordt ook aangeraden om, als de tijd het toelaat, gebruik te maken van optie 2 om ook controlesommen voor validatie te genereren (dit kan tijd kosten, afhankelijk van de grootte van de gegevens). Deze stappen verlagen de kans op storingen bij het uploaden van de gegevens naar Azure.
3. Verwijder het station op een veilige manier.

## <a name="ship-to-azure"></a>Verzenden naar Azure

Deze stap neemt ongeveer 5 tot 7 minuten in beslag.

1. Plaats alle schijven samen in de oorspronkelijke verpakking. Gebruik het meegeleverde verzendlabel. Als het label beschadigd of kwijt is, kunt u het downloaden via de portal. Ga naar **Overzicht** en klik in de opdrachtbalk op **Verzendlabel downloaden**.
2. Lever het verzegelde pakket in bij een vestiging van de verzender.  

De Data Box Disk-service verzendt een melding via e-mail en werkt de orderstatus bij in de Azure-portal.

## <a name="verify-your-data"></a>Uw gegevens verifiëren

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Wanneer de Data Box Disk met het datacenternetwerk van Azure is verbonden, wordt de gegevensupload naar Azure automatisch gestart.
2. De Azure Data Box-service laat u weten dat het kopiëren van de gegevens is voltooid via de Azure-portal.
    
    1. Controleer foutenlogboeken op eventuele fouten en onderneem toepasselijke acties.
    2. Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert.

## <a name="clean-up-resources"></a>Resources opschonen

Deze stap neemt 2 tot 3 minuten in beslag.

Als u wilt opschonen, kunt u de Data Box-order annuleren en vervolgens verwijderen.

- U kunt de Data Box-order in de Azure-portal annuleren voordat de order wordt verwerkt. Zodra de order is verwerkt, kan deze niet meer worden geannuleerd. De order doorloopt verwerkingsfasen totdat deze is voltooid.

    Als u de order wilt annuleren, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.  

- U kunt de order verwijderen zodra de status als **Voltooid** of **Geannuleerd** wordt weergegeven in de Azure-portal.

    Als u de order wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Azure Data Box Disk geïmplementeerd om u te helpen uw gegevens in Azure te importeren. Ga verder naar de volgende zelfstudie voor meer informatie over Azure Data Box Disk-beheer:

> [!div class="nextstepaction"]
> [De Azure-portal gebruiken om Data Box Disk te beheren](data-box-portal-ui-admin.md)

::: zone-end
