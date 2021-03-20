---
title: 'Zelfstudie: Gegevens accepteren en ontvangen - Azure Data Share'
description: 'Zelfstudie: Gegevens accepteren en ontvangen met Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017046"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Zelfstudie: Gegevens accepteren en ontvangen met Azure Data Share  

In deze zelfstudie leert u hoe u een uitnodiging voor gegevensshares met behulp van Azure Data Share accepteert. U leert hoe u gegevens ontvangt die met u worden gedeeld, en hoe u een regelmatig vernieuwingsinterval kunt instellen om ervoor te zorgen dat u altijd de meest recente momentopname hebt van de gegevens die met u worden gedeeld. 

> [!div class="checklist"]
> * Een Azure Data Share-uitnodiging accepteren
> * Maak een Azure Data Share-account
> * Geef een doel op voor de gegevens
> * Maak een abonnement voor uw gegevensshare voor geplande vernieuwing

## <a name="prerequisites"></a>Vereisten
Voordat u een uitnodiging voor gegevensshares kunt accepteren, moet u een aantal Azure-resources inrichten. Deze worden hieronder weergegeven. 

Zorg ervoor dat alle vereisten zijn voltooid voordat u een uitnodiging voor gegevensshares accepteert. 

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Data Share-uitnodiging: Een uitnodiging van Microsoft Azure met een onderwerp met de titel: Azure Data Share-uitnodiging van **<yourdataprovider@domain.com>** .
* Registreer de [Microsoft.DataShare-resourceprovider](concepts-roles-permissions.md#resource-provider-registration) in het Azure-abonnement waarin u een Data Share-resource gaat maken, en in het Azure-abonnement waarin uw Azure-doelgegevensarchieven zich bevinden.

### <a name="receive-data-into-a-storage-account"></a>Gegevens ontvangen in een opslagaccount

* Een Azure Storage-account: Hier kunt u een [Azure Storage-account](../storage/common/storage-account-create.md) maken als u nog geen account hebt. 
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar.  

### <a name="receive-data-into-a-sql-based-target"></a>Gegevens ontvangen in een op SQL gebaseerd doel
Als u ervoor kiest om gegevens te ontvangen in Azure SQL Database of Azure Synapse Analytics, vindt u hieronder de lijst met vereisten. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Vereisten voor het ontvangen van gegevens in Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW)
U kunt de [demo met stapsgewijze instructies volgen](https://youtu.be/aeGISgK1xro) voor het configureren van vereisten.

* Een instantie van Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW).
* Machtiging om naar databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**. 
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot Azure SQL Database of Azure Synapse Analytics. U kunt dit doen via de volgende stappen: 
    1. Navigeer in Azure Portal naar de SQL-server en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Maak verbinding met de Azure SQL-database of het Azure SQL-datawarehouse met behulp van [Queryeditor](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) of SQL Server Management Studio met Azure Active Directory-verificatie. 
    1. Voer het volgende script uit om de beheerde Data Share-identiteit toe te voegen als db_datareader, db_datawriter, db_ddladmin. U moet verbinding maken met behulp van Active Directory en niet via SQL Server-verificatie. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.         

* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in SQL Server in de Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Vereisten voor het ontvangen van gegevens in de SQL-pool in Azure Synapse Analytics (werkruimte)

* Een toegewezen SQL-pool in de Synapse Analytics-werkruimte. Het ontvangen van gegevens in een serverloze SQL-pool wordt momenteel niet ondersteund.
* Machtiging om te schrijven naar de SQL-pool in de Synapse-werkruimte, die zich in *Microsoft.Synapse/workspaces/sqlPools/write* bevindt. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot de SQL-pool in de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer SQL Active Directory-beheerder in de linkernavigatiebalk en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Open Synapse Studio, selecteer *Beheren* in de linkernavigatiebalk. Selecteer *Toegangsbeheer* onder Beveiliging. Wijs uzelf de rol van **SQL-beheerder** of **werkruimtebeheerder** toe.
    1. Selecteer in Synapse Studio de optie *Beheren* in de linkernavigatiebalk. Voer in de SQL-pool het volgende script uit om de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader, db_datawriter, db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Toegang tot de firewall van de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer *Firewalls* in de linkernavigatiebalk.
    1. Klik op **AAN** bij *Toestaan dat Azure-services en -resources toegang tot deze werkruimte krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Gegevens ontvangen in een Azure Data Explorer-cluster: 

* Een Azure Data Explorer-cluster in hetzelfde Azure-datacenter als het Data Explorer-cluster van de gegevensprovider: Hier kunt u een [Azure Data Explorer-account](/azure/data-explorer/create-cluster-database-portal) maken als u nog geen account hebt. Als u het Azure-datacenter van het cluster van de gegevensprovider niet weet, kunt u het cluster later in het proces maken.
* Machtiging om naar het Azure Data Explorer-cluster te schrijven, aanwezig in *Microsoft.Kusto/clusters/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging om roltoewijzing toe te voegen aan het Azure Data Explorer-cluster, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Uitnodiging openen

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. U kunt de uitnodiging openen vanuit uw e-mail of rechtstreeks vanuit de Azure-portal. 

   Als u de uitnodiging wilt openen vanuit uw e-mail, gaat u in uw Postvak IN naar de uitnodiging van de gegevensprovider. De uitnodiging is van Microsoft Azure en heeft de titel **Azure Data Share-uitnodiging van <yourdataprovider@domain.com>** . Klik op **Uitnodiging weergeven** om de uitnodiging te bekijken in Azure. 

   Als u de uitnodiging rechtstreeks vanuit de Azure-portal wilt openen, gaat u in de Azure-portal naar **Data Share-uitnodigingen**. Met deze actie kunt u de lijst met Data Share-uitnodigingen openen.

   ![Lijst met uitnodigingen](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer de share die u wilt bekijken. 

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Bereid uw Azure CLI-omgeving voor en bekijk vervolgens uw uitnodigingen.

Begin door de omgeving voor te bereiden op de Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Voer de opdracht [az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) uit om uw huidige uitnodigingen te bekijken:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Kopieer uw uitnodigings-id om deze in de volgende sectie te gebruiken.

---

## <a name="accept-invitation"></a>Uitnodiging accepteren

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zorg ervoor dat u alle velden controleert, met inbegrip van de **Gebruiksvoorwaarden**. Als u akkoord gaat met de gebruiksvoorwaarden, moet u het vakje inschakelen om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Selecteer onder *Data Share-doelaccount* het abonnement en de resourcegroep waarin u de Data Share-gegevens wilt implementeren. 

   Selecteer voor het veld **Data Share-account** de optie **Nieuwe maken** als u geen bestaand Data Share-account hebt. Anders selecteert u een bestaand Data Share-account waarin u de informatie uit uw gegevensshare wilt accepteren. 

   In het veld **Ontvangen sharenaam** kunt u de standaardwaarde laten staan die is opgegeven door de gegevensprovider. U kunt er ook voor kiezen een nieuwe naam op te geven voor de ontvangen share. 

   Zodra u akkoord bent gegaan met de gebruiksvoorwaarden en een Data Share-account hebt opgegeven voor het beheren van de ontvangen share, selecteert u **Accepteren en configureren**. Er wordt een shareabonnement gemaakt. 

   ![Opties voor accepteren](./media/accept-options.png "Opties voor accepteren") 

   Met deze actie gaat u naar de ontvangen share in uw Data Share-account. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *Weigeren*. 

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) om de Data Share te maken.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Ontvangen share configureren

### <a name="portal"></a>[Portal](#tab/azure-portal)

Volg de onderstaande stappen om te configureren waar u gegevens wilt ontvangen.

1. Selecteer het tabblad **Gegevenssets**. Schakel het selectievakje in van de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **+ Toewijzen aan doel** om een doelgegevensarchief te kiezen. 

   ![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

1. Selecteer het type doelgegevensarchief waarin u de gegevens wilt opslaan. Alle gegevensbestanden of tabellen in het doelgegevensarchief met hetzelfde pad en dezelfde naam worden overschreven. 

   Selecteer voor in-place delen een gegevensarchief op de opgegeven locatie. De locatie is het Azure-datacenter waar het brongegevensarchief van de gegevensprovider zich bevindt. Zodra de gegevensset is toegewezen, kunt u de koppeling in het doelpad volgen om toegang te krijgen tot de gegevens.

   ![Doelopslagaccount](./media/dataset-map-target-sql.png "Doelopslag") 

1. Als u wilt delen met behulp van momentopnamen, en de gegevensprovider een schema heeft opgesteld voor het maken van momentopnamen om de gegevens regelmatig bij te werken, kunt u het schema voor het maken van momentopnamen ook inschakelen door het tabblad **Schema voor momentopnamen** te selecteren. Schakel het selectievakje naast het schema voor momentopnamen in en selecteer **+ Inschakelen**.

   ![Schema voor momentopnamen inschakelen](./media/enable-snapshot-schedule.png "Schema voor momentopnamen inschakelen")

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik deze opdrachten om te configureren waar u gegevens wilt ontvangen.

1. Voer de opdracht [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) uit om de gegevensset-id op te halen:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Voer de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) uit om een opslagaccount voor deze Data Share te maken:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Gebruik de opdracht [az storage account show](/cli/azure/storage/account#az_storage_account_show) om de opslagaccount-id op te halen:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Gebruik de volgende opdracht om de account-principal-id op te halen:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Gebruik de opdracht [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) om een roltoewijzing voor de account-principal te maken:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Een variabele voor de toewijzing maken op basis van de gegevensset-id:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Gebruik de opdracht [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) om de toewijzing voor de gegevensset te maken:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Voer de opdracht [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) uit om de synchronisatie van de gegevensset te starten.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Voer de opdracht [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) uit om een lijst van uw synchronisaties weer te geven:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Gebruik de opdracht [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) om synchronisatie-instellingen te zien die zijn ingesteld voor uw share.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Een momentopname activeren

### <a name="portal"></a>[Portal](#tab/azure-portal)

Deze stappen zijn alleen van toepassing bij delen op basis van momentopnamen.

1. U kunt een momentopname activeren door **Momentopname activeren** te selecteren op het tabblad **Details**. Hier kunt u een volledige of incrementele momentopname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevensprovider ontvangt, selecteert u volledig kopiëren. 

   ![Momentopname activeren](./media/trigger-snapshot.png "Momentopname activeren") 

1. Wanneer de status van de laatste uitvoering *Geslaagd* is, gaat u naar het doelgegevensarchief om de ontvangen gegevens te bekijken. Selecteer **Gegevenssets** en klik op de koppeling in het doelpad. 

   ![Gegevenssets van consumenten](./media/consumer-datasets.png "Toewijzing voor gegevenssets van consumenten") 

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de opdracht [az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) uit om het maken van een momentopname te activeren:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Gebruik deze opdracht alleen voor delen op basis van een momentopname.

---

## <a name="view-history"></a>Geschiedenis weergeven
Deze stap geldt alleen voor delen op basis van momentopnamen. Selecteer het tabblad **Geschiedenis** om de geschiedenis van uw momentopnamen weer te geven. Hier vindt u een geschiedenis van alle momentopnamen die zijn gegenereerd voor de afgelopen dertig dagen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resource niet langer nodig hebt, gaat u naar de pagina **Overzicht Data Share** en selecteert u **Verwijderen** om de resource te verwijderen.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare accepteert en ontvangt. Voor meer informatie over Azure Data Share-concepten gaat u naar Azure Data Share-terminologie.

> [!div class="nextstepaction"]
> [Azure Data Share-concepten](terminology.md)