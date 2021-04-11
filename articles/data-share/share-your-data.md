---
title: 'Zelfstudie: Delen buiten uw organisatie - Azure Data Share'
description: 'Zelfstudie: Gegevens delen met klanten en partners met behulp van Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 8e149270d8f98cbf72d3864d238a3d8ddfd61c67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639546"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Zelfstudie: Gegevens delen met Azure Data Share  

In deze zelfstudie leert u hoe u een nieuwe Azure Data Share instelt en hoe u uw gegevens met klanten en partners buiten uw Azure-organisatie kunt delen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevensshare.
> * Voeg gegevenssets toe aan uw gegevensshare.
> * Schakel een schema voor momentopnamen in voor uw gegevensshare. 
> * Voeg ontvangers toe aan uw gegevensshare. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Het e-mailadres voor Azure van de ontvanger (de e-mailalias werkt niet).
* Als de bron-Azure-gegevensopslag zich in een ander Azure-abonnement bevindt dan de opslag die u gebruikt om een Data Share-resource te maken, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount

* Een Azure Storage-account: U kunt een gratis [Azure Storage-account](../storage/common/storage-account-create.md) aanmaken als u nog niet een hebt
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol **Eigenaar**. 


### <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL
Hieronder ziet u de lijst met vereisten voor het delen van gegevens vanuit een SQL-bron. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Vereisten voor het delen vanuit Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW)

* Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW) met tabellen en weergaven die u wilt delen.
* Machtiging om naar de databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* De beheerder van de SQL-Server **Azure Active Directory**
* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar SQL-server. Selecteer *Firewalls en virtuele netwerken* in de linkernavigatiebalk.
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Vereisten voor het delen vanuit de SQL-pool in Azure Synapse Analytics (werkruimte)

* * Een toegewezen SQL-pool in Azure Synapse Analytics (werkruimte) met tabellen die u wilt delen. Het delen van weergaven wordt momenteel niet ondersteund. Delen vanuit een serverloze SQL-pool wordt momenteel niet ondersteund.
* Machtiging om te schrijven naar de SQL-pool in de Synapse-werkruimte, die zich in *Microsoft.Synapse/workspaces/sqlPools/write* bevindt. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot de SQL-pool in de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer SQL Active Directory-beheerder in de linkernavigatiebalk en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Open Synapse Studio, selecteer *Beheren* in de linkernavigatiebalk. Selecteer *Toegangsbeheer* onder Beveiliging. Wijs uzelf de rol van **SQL-beheerder** of **werkruimtebeheerder** toe.
    1. Selecteer in Synapse Studio de optie *Beheren* in de linkernavigatiebalk. Voer in de SQL-pool het volgende script uit om de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Toegang tot de firewall van de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer *Firewalls* in de linkernavigatiebalk.
    1. Klik op **AAN** bij *Toestaan dat Azure-services en -resources toegang tot deze werkruimte krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 


### <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
* Een Azure Data Explorer-cluster met databases die u wilt delen.
* Machtiging om naar het Azure Data Explorer-cluster te schrijven, aanwezig in *Microsoft.Kusto/clusters/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een Data Share-account maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Selecteer de menuknop in de linkerbovenhoek van de portal, en selecteer vervolgens **Een resource maken** (+).

1. Zoek naar *Data Share*.

1. Selecteer Data Share en selecteer **Maken**.

1. Vul de basisgegevens van uw Azure Data Share-resource in met de volgende informatie. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevensshare-account.|
    | Resourcegroep | *testresourcegroup* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
    | Naam | *datashareaccount* | Geef een naam op voor uw gegevensshare-account. |
    | | |

1. Selecteer **Beoordelen en maken** en vervolgens **Maken** om uw gegevensshare-account in te richten. Het inrichten van een nieuw gegevensshare-account duurt doorgaans 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Maak een Azure Data Share-resource in een Azure-resourcegroep.

Begin door de omgeving voor te bereiden op de Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Gebruik deze opdrachten om de resource te maken:

1. Gebruik de opdracht [az account set](/cli/azure/account#az_account_set) om uw abonnement in te stellen als het huidige standaardabonnement:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Voer de opdracht [az provider register](/cli/azure/provider#az_provider_register) uit om de resourceprovider te registreren:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Voer de opdracht [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) uit om een Data Share-account te maken:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Voer de opdracht [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) uit om uw Data Share-account weer te geven:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Een share maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar de overzichtspagina van uw gegevensshare.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![EnterShareDetails](./media/enter-share-details.png "Gegevens van share invoeren") 

1. Selecteer **Doorgaan**.

1. Selecteer **Gegevenssets toevoegen** om gegevenssets toe te voegen aan de share. 

    ![Gegevenssets toevoegen aan de share](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Welke lijst met typen gegevensset wordt weergegeven, is afhankelijk van het type share (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. Als u deelt vanuit een Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW), wordt u gevraagd om een verificatie methode te maken voor het weer geven van tabellen. Selecteer AAD-verificatie en schakel het selectie vakje **gegevens delen toestaan om het bovenstaande script voor het maken van gebruikers namens mij uit te voeren**. 

    ![AddDatasets](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer Gegevenssets toevoegen. 

    ![SelectDatasets](./media/select-datasets.png "Gegevenssets selecteren")    

1. Voer op het tabblad Ontvangers de e-mailadressen in van uw gegevensgebruiker door + Ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**.

1. Als u het type share momentopname hebt geselecteerd, kunt u het momentopnameschema configureren om updates van uw gegevens naar uw gegevensgebruiker te sturen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad Beoordelen en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) uit om een Data Share te maken:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Gebruik de opdracht [az storage container create](/cli/azure/storage/container#az_storage_container_create) om een container te maken voor de share uit de vorige opdracht:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Voer de opdracht [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) uit om uw Data Share te maken:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Gebruik de opdracht [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) om de uitnodiging voor het opgegeven adres te maken:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Uw Azure-gegevensshare is nu gemaakt en de ontvanger van uw gegevensshare kan nu uw uitnodiging accepteren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resource niet langer nodig hebt, gaat u naar de pagina **Overzicht Data Share** en selecteert u **Verwijderen** om de resource te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare maakt en ontvangers uitnodigt. Voor meer informatie over hoe een gegevensgebruiker een gegevensshare kan accepteren en ontvangen, gaat u door naar de zelfstudie voor het accepteren en ontvangen van gegevens.

> [!div class="nextstepaction"]
> [Zelfstudie: Gegevens accepteren en ontvangen met Azure Data Share](subscribe-to-data-share.md)
