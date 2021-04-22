---
title: Quickstart voor Microsoft Azure Data Box Heavy| Microsoft Docs
description: In deze quickstart leert u hoe u de Azure Data Box Heavy implementeert met behulp van de Azure-portal, met inbegrip van het bekabelen en configureren van de Data Box Heavy en het kopiëren van gegevens die u naar Azure wilt uploaden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 8c418f7cbeb56b94b7a85b12e833301b979bff32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871539"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Quickstart: Azure Data Box Heavy implementeren met behulp van de Azure-portal

In deze quickstart wordt beschreven hoe u de Azure Data Box Heavy implementeert met behulp van de Azure-portal. De stappen omvatten het aansluiten van de kabels, en het configureren en het kopiëren van gegevens naar de Data Box Heavy, zodat deze kunnen worden geüpload naar Azure. De quickstart wordt uitgevoerd in de Azure-portal en op de lokale webgebruikersinterface van het apparaat.

Ga voor gedetailleerde stapsgewijze instructies voor implementatie en tracering naar [Zelfstudie: Azure Data Box Heavy bestellen](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan de volgende configuratievereisten voor de installatielocatie, de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert.

### <a name="for-installation-site"></a>Voor installatielocatie

Zorg voordat u begint voor het volgende:

- Het apparaat past door alle doorgangen. Dit zijn de afmetingen van het apparaat: breedte: 66 cm lengte: 1,22 m hoogte: 71 cm.
- Er is een lift of een helling aanwezig indien u het apparaat wilt installeren op een andere verdieping dan de begane grond.
- Er zijn twee personen beschikbaar om het apparaat te verplaatsen. Het apparaat weegt ongeveer 225 kg en is voorzien van wielen.
- U moet toegang hebben tot een vlakke opstellingsplaats in het datacenter met een netwerkverbinding die een apparaat met deze footprint kan ondersteunen.

### <a name="for-service"></a>Voor de service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:

- U hebt de [veiligheidsrichtlijnen voor de Data Box Heavy](data-box-safety.md) doorgenomen.
- Er is een hostcomputer verbonden met het netwerk van het datacenter. Data Box Heavy kopieert de gegevens vanaf deze computer. Op de hostcomputer moet een [ondersteund besturingssysteem](data-box-heavy-system-requirements.md) worden uitgevoerd.
- U hebt een laptop met een RJ-45-kabel om verbinding te maken met de lokale gebruikersinterface en het apparaat te configureren. Gebruik de laptop om elk knooppunt van het apparaat eenmalig te configureren.
- Uw datacenter heeft een hogesnelheidsnetwerk en u hebt ten minste een verbinding van 10 GbE.
- U hebt per apparaatknooppunt één kabel van 40 Gbps of 10 Gbps nodig. Kies kabels die compatibel zijn met de Mellanox MCX314A- BCCT-netwerkinterface:
    - Voor de kabel van 40 Gbps moet het apparaatuiteinde van de kabel QSFP+ zijn.
    - Voor de kabel van 10 Gbps hebt u een SFP+-kabel nodig die op één kant van een 10-G-switch wordt aangesloten, met een QSFP+-naar-SFP+-adapter (of de QSA-adapter) voor het uiteinde dat op het apparaat wordt aangesloten.
- De voedingskabels bevinden zich in een lade aan de achterkant van het apparaat.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Bestellen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Deze stap neemt ongeveer 5 minuten in beslag.

1. Maak een nieuwe Azure Data Box-resource in de Azure-portal.
2. Selecteer een bestaand abonnement dat is ingeschakeld voor deze service, en kies **Importeren** als overdrachtstype. Geef het **Bronland** op waar de gegevens zijn opgeslagen, en geef de **Azure-doelregio** voor de gegevensoverdracht op.
3. Selecteer **Data Box Heavy**. De maximale bruikbare capaciteit is 770 TB, en voor grotere hoeveelheden gegevens kunt u meerdere orders maken.
4. Voer de order- en verzendgegevens in. Als de service beschikbaar is in uw regio, geeft u e-mailadressen voor meldingen op, controleert u de samenvatting en maakt u vervolgens de order.

Zodra de order is gemaakt, wordt het apparaat voorbereid voor verzending.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik deze Azure CLI-opdrachten om een Data Box Heavy-taak te maken.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurecli
   az group create --name databox-rg --location westus 
   ```

1. Gebruik de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) om een opslagaccount te maken, of gebruik een bestaand opslagaccount:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Voer de opdracht [az databox job create](/cli/azure/databox/job#az_databox_job_create) uit om een Data Box-taak te maken met de waarde **--sku** van `DataBoxHeavy`:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxheavy-job \
       --location westus --sku DataBoxHeavy --contact-name "Jim Gan" --phone 4085555555 \
       --city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA --storage-account databoxtestsa \
       --staging-storage-account databoxtestsa --resource-group-for-managed-disk rg-for-md
   ```

   > [!NOTE]
   > Controleer of uw abonnement ondersteuning biedt voor Data Box Heavy.

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

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Apparaat aansluiten en verbinding maken

Nadat u de vereisten hebt gecontroleerd, kunt u het apparaat aansluiten en een verbinding tot stand brengen.

::: zone-end

## <a name="cable-for-power"></a>Voedingskabels aansluiten

Deze stap duurt ongeveer vijf minuten.

Als u de Data Box Heavy hebt ontvangen, voert u de volgende stappen uit om de voedingskabels aan te sluiten en het apparaat in te schakelen.

1. Ga niet verder als u vermoedt dat er met het apparaat is geknoeid of dat het is beschadigd. Neem contact op met Microsoft Ondersteuning om een vervangend apparaat naar u te laten verzenden.
2. Transporteer het apparaat naar de installatielocatie en vergrendel de achterste wielen.
3. Sluit de vier stroomkabels aan op de voedingen aan de achterkant van het apparaat.
4. Gebruik de aan/uit-knoppen aan de voorzijde om de apparaatknooppunten in te schakelen.

## <a name="cable-first-node-for-network"></a>Eerste knooppunt bekabelen voor netwerk

Deze stap neemt ongeveer 10 tot 15 minuten in beslag.

1. Gebruik de RJ-45-netwerkkabel van het type CAT-6 om de hostcomputer te koppelen aan de MGMT-poort (Managementpoort) op het apparaat.
2. Gebruik de koperen Twinax-kabel om verbinding te maken met minstens één 40 Gbps-netwerkinterface (liever dan 1 Gbps), DATA1 of DATA2 voor gegevens. Als u een 10 Gbps-switch gebruikt, gebruikt u een koperen Twinax SFP+-kabel met een QSFP+-naar-SFP+-adapter (de QSA-adapter) om verbinding te maken met de 40 Gbps-netwerkinterface voor gegevens.
3. Sluit de kabels aan zoals hieronder wordt weergegeven.  

    ![Bekabelde Data Box Heavy](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Eerste knooppunt configureren

Deze stap neemt ongeveer 5 tot 7 minuten in beslag.

1. Als u het wachtwoord van het apparaat wilt ophalen, gaat u naar **Algemeen > Apparaatdetails** in de [Azure-portal](https://portal.azure.com). Voor beide knooppunten van het apparaat kunt u hetzelfde wachtwoord gebruiken.
2. Wijs het statische IP-adres 192.168.100.5 en het subnet 255.255.255.0 toe aan de Ethernet-adapter op de computer die u gebruikt om verbinding te maken met de Data Box Heavy. U hebt op `https://192.168.100.10` toegang tot de webgebruikersinterface van het apparaat. Nadat u het apparaat hebt ingeschakeld, duurt het maximaal 5 minuten om de verbinding tot stand te brengen.
3. Meld u aan met het wachtwoord uit de Azure-portal. U ziet nu een foutmelding over een probleem met het beveiligingscertificaat van de website. Volg de browserinstructies om naar de webpagina te gaan.
4. De netwerkinstellingen voor de interfaces (uitgezonderd de MGMT) worden standaard geconfigureerd als DHCP. Indien nodig kunt u deze interfaces als statisch configureren en een IP-adres opgeven.

## <a name="cable-and-configure-the-second-node"></a>Tweede knooppunt bekabelen en configureren

Deze stap neemt ongeveer 15 tot 20 minuten in beslag.

Volg de stappen voor het eerste knooppunt om ook het tweede knooppunt op het apparaat te bekabelen en configureren.  


::: zone target = "docs"

## <a name="copy-data"></a>Gegevens kopiëren

De duur van deze bewerking hangt af van de hoeveelheid gegevens en de snelheid van het netwerk waarover de gegevens worden gekopieerd.
 
1. Kopieer gegevens naar beide knooppunten op het apparaat door de twee gegevensinterfaces van 40 Gbps naast elkaar te gebruiken.

    - Als u een Windows-host gebruikt, gebruikt u een SMB-compatibel hulpprogramma voor het kopiëren van bestanden, zoals [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
    - Voor een NFS-host gebruikt u de opdracht `cp` of `rsync` om de gegevens te kopiëren.
2. Maak verbinding met de shares op het apparaat via het pad:`\\<IP address of your device>\ShareName`. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box Heavy.
3. Zorg ervoor dat de namen van shares en mappen, en de gegevens de richtlijnen volgen die worden beschreven in [Servicelimieten in Azure Storage en Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Als de gegevens zonder fouten zijn gekopieerd, gaat u naar de pagina **Voorbereiden voor verzending** in de lokale webgebruikersinterface en start u de voorbereiding voor verzending.
2. Nadat de **voorbereiding op beide** knooppunten is uitgevoerd, schakelt u het apparaat uit via de lokale webgebruikersinterface.

## <a name="ship-to-azure"></a>Verzenden naar Azure

Het voltooien van deze bewerking duurt ongeveer 15 tot 20 minuten.

1. Verwijder de kabels en leg deze terug in de opberglade aan de achterkant van het apparaat.
2. Maak een afspraak met een transportbedrijf voor het ophalen van de zending.
3. Neem contact op met [Data Box Operations](mailto:DataBoxOps@microsoft.com) om ze te informeren over het afhalen en om een retourlabel aan te vragen.
4. Het retourlabel moet duidelijk zichtbaar worden bevestigd op het voorpaneel van het apparaat.

## <a name="verify-data"></a>Gegevens controleren

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Wanneer het Data Box Heavy-apparaat wordt verbonden met het datacenternetwerk van Azure, worden de gegevens automatisch geüpload naar Azure.
2. De Data Box-service meldt via de Azure-portal dat het kopiëren van de gegevens is voltooid.

    1. Controleer foutenlogboeken op eventuele fouten en onderneem toepasselijke acties.
    2. Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert.

## <a name="clean-up-resources"></a>Resources opschonen

Deze stap neemt 2 tot 3 minuten in beslag.

- U kunt de Data Box Heavy-order in de Azure-portal annuleren voordat de order is verwerkt. Zodra de order is verwerkt, kan deze niet meer worden geannuleerd. De order doorloopt verwerkingsfasen totdat deze is voltooid. Als u de order wilt annuleren, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

- U kunt de order verwijderen zodra de status als **Voltooid** of **Geannuleerd** wordt weergegeven in de Azure-portal. Als u de order wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Data Box Heavy geïmplementeerd om u te helpen uw gegevens te importeren in Azure. Ga verder naar de volgende zelfstudie voor meer informatie over Azure Data Box Heavy-beheer:

> [!div class="nextstepaction"]
> [De Azure-portal gebruiken om Data Box Heavy te beheren](data-box-portal-admin.md)

::: zone-end
