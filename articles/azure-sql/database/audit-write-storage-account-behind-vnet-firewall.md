---
title: Controleren op opslag account achter VNet en firewall
description: Configureer controle om database gebeurtenissen te schrijven op een opslag account achter het virtuele netwerk en de firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348250"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Schrijf audit naar een opslag account achter VNet en firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Controles voor [Azure SQL database](sql-database-paas-overview.md) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) bieden ondersteuning voor het schrijven van Database gebeurtenissen naar een [Azure Storage account](../../storage/common/storage-account-overview.md) achter een virtueel netwerk en een firewall.

In dit artikel worden twee manieren beschreven om Azure SQL Database en Azure Storage-account te configureren voor deze optie. De eerste gebruikt de Azure Portal, de tweede gebruikt REST.

## <a name="background"></a>Achtergrond

[Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) is de fundamentele bouw steen voor uw particuliere netwerk in Azure. Via VNet kunnen veel soorten Azure-resources, zoals virtuele Azure-machines, veilig communiceren met elkaar, internet en on-premises netwerken. VNet is vergelijkbaar met een traditioneel netwerk in uw eigen Data Center, maar biedt extra voor delen van Azure-infra structuur, zoals schaal, Beschik baarheid en isolatie.

Zie [Wat is Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)voor meer informatie over de VNet-concepten, aanbevolen procedures en nog veel meer.

Zie [Quick Start: een virtueel netwerk maken met behulp van de Azure Portal](../../virtual-network/quick-create-portal.md)voor meer informatie over het maken van een virtueel netwerk.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten zijn vereist om te schrijven naar een opslag account achter een VNet of een firewall:

> [!div class="checklist"]
>
> * Een v2-opslag account voor algemeen gebruik. Als u een v1-of Blob-opslag account voor algemeen gebruik hebt, [moet u een upgrade uitvoeren naar een v2-opslag account voor algemeen gebruik](../../storage/common/storage-account-upgrade.md). Zie [typen opslag accounts](../../storage/common/storage-account-overview.md#types-of-storage-accounts)voor meer informatie.
> * Het opslag account moet zich op hetzelfde abonnement en op dezelfde locatie als de [logische SQL Server](logical-servers.md).
> * Het Azure Storage-account vereist `Allow trusted Microsoft services to access this storage account` . Stel dit in op het opslag account **firewalls en virtuele netwerken**.
> * U moet `Microsoft.Authorization/roleAssignments/write` een machtiging hebben voor het geselecteerde opslag account. Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie.

## <a name="configure-in-azure-portal"></a>Configureren in Azure Portal

Maak verbinding met [Azure Portal](https://portal.azure.com) met uw abonnement. Navigeer naar de resource groep en server.

1. Klik op **controleren** onder de kop beveiliging. Selecteer **aan**.

2. Selecteer **Opslag**. Selecteer het opslag account waarin de logboeken worden opgeslagen. Het opslag account moet voldoen aan de vereisten die worden vermeld in [vereiste onderdelen](#prerequisites).

3. **Opslag Details** openen

  > [!NOTE]
  > Als het geselecteerde opslag account zich achter VNet bevindt, wordt het volgende bericht weer gegeven:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Als u dit bericht niet ziet, bevindt het opslag account zich niet achter een VNet.

4. Selecteer het aantal dagen voor de Bewaar periode. Klik vervolgens op **OK**. Logboeken die ouder zijn dan de Bewaar periode, worden verwijderd.

5. Selecteer **Opslaan** in de controle-instellingen.

U hebt de audit geconfigureerd om te schrijven naar een opslag account achter een VNet of firewall.

## <a name="configure-with-rest-commands"></a>Configureren met REST-opdrachten

Als alternatief voor het gebruik van de Azure Portal kunt u REST-opdrachten gebruiken om de controle te configureren voor het schrijven van database gebeurtenissen op een opslag account achter een VNet en firewall.

De voorbeeld scripts in deze sectie vereisen dat u het script bijwerkt voordat u ze uitvoert. Wijzig de volgende waarden in de scripts:

|Voorbeeldwaarde|Voorbeeld beschrijving|
|:-----|:-----|
|`<subscriptionId>`| Azure-abonnements-ID|
|`<resource group>`| Resourcegroep|
|`<logical SQL Server>`| Servernaam|
|`<administrator login>`| Administrator-account |
|`<complex password>`| Complex wacht woord voor het beheerders account|

SQL-controle configureren om gebeurtenissen te schrijven naar een opslag account achter een VNet of firewall:

1. Registreer uw server bij Azure Active Directory (Azure AD). Gebruik Power shell of REST API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**rest API**](/rest/api/sql/servers/createorupdate):

   Voorbeeld aanvraag

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Aanvraagbody

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. Open [Azure Portal](https://portal.azure.com). Ga naar uw opslagaccount. Zoek **Access Control (IAM)** en klik op **roltoewijzing toevoegen**. Wijs de Azure-rol **Storage BLOB data Inzender** toe aan de server die als host fungeert voor de data base die u hebt geregistreerd bij Azure Active Directory (Azure AD), zoals in de vorige stap.

   > [!NOTE]
   > Alleen leden met de bevoegdheid Eigenaar kunnen deze stap uitvoeren. Raadpleeg de [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor verschillende ingebouwde rollen van Azure.

3. Het [controle beleid van de server-BLOB](/rest/api/sql/server%20auditing%20settings/createorupdate)configureren zonder een *storageAccountAccessKey* op te geven:

   Voorbeeld aanvraag

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   Aanvraagbody

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

- [Controle beleid voor data bases maken of bijwerken (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Controle beleid voor servers maken of bijwerken (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken

U kunt controle configureren voor het schrijven van database gebeurtenissen op een opslag account achter het virtuele netwerk en Firewall met [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sjabloon, zoals wordt weer gegeven in het volgende voor beeld:

> [!IMPORTANT]
> Als u een opslag account achter een virtueel netwerk en een firewall wilt gebruiken, moet u de para meter **isStorageBehindVnet** instellen op True

- [Een Azure SQL Server implementeren met controle ingeschakeld om audit logboeken naar een Blob-opslag te schrijven](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> Het gekoppelde voor beeld bevindt zich op een externe open bare opslag plaats en wordt als ' as is ' gegeven, zonder garantie, en wordt niet ondersteund onder een ondersteunings programma/service van micro soft.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik Power shell om een service-eind punt voor een virtueel netwerk te maken en vervolgens een regel voor het virtuele netwerk voor Azure SQL Database.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Virtual Network regels: bewerkingen met REST-Api's](/rest/api/sql/virtualnetworkrules)
* [Virtuele netwerk service-eind punten en-regels voor servers gebruiken](vnet-service-endpoint-rule-overview.md)
