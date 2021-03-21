---
title: BLOB-uitvoer verifiëren met beheerde identiteits Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure Blob Storage-uitvoer.
author: kim-ale
ms.author: kimal
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 369348133f7395f5db5b5923bd438cec8e4ad733
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954375"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>Beheerde identiteit (preview) gebruiken om uw Azure Stream Analytics-taak te verifiëren bij Azure Blob Storage

Met [beheerde identiteits verificatie](../active-directory/managed-identities-azure-resources/overview.md) (preview) voor uitvoer naar Azure Blob-opslag kunt u Stream Analytics Jobs direct toegang tot een opslag account geven in plaats van een Connection String te gebruiken. Naast verbeterde beveiliging kunt u met deze functie ook gegevens schrijven naar een opslag account in een Virtual Network (VNET) in Azure.

Dit artikel laat u zien hoe u beheerde identiteit voor de BLOB-uitvoer (en) van een Stream Analytics-taak kunt inschakelen via de Azure Portal en via een Azure Resource Manager-implementatie.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Maak de Stream Analytics taak met behulp van de Azure Portal

1. Maak een nieuwe Stream Analytics-taak of open een bestaande taak in de Azure Portal. Selecteer in de menu balk aan de linkerkant van het scherm **beheerde identiteit** onder **configureren**. Zorg ervoor dat de door het systeem toegewezen beheerde identiteit gebruiken is geselecteerd en klik vervolgens op de knop **Opslaan** aan de onderkant van het scherm.

   ![Stream Analytics beheerde identiteit configureren](./media/common/stream-analytics-enable-managed-identity.png)

2. Selecteer in het venster uitvoer eigenschappen van de Azure Blob Storage-uitvoer Sink de vervolg keuzelijst authenticatie modus en kies **beheerde identiteit**. Zie voor meer informatie over de andere uitvoer eigenschappen [inzicht in de uitvoer van Azure stream Analytics](./stream-analytics-define-outputs.md). Klik op **Opslaan** als u klaar bent.

   ![Azure Blob Storage-uitvoer configureren](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Nu de taak is gemaakt, raadpleegt u de sectie [de stream Analytics-taak toegang geven tot uw opslag account](#give-the-stream-analytics-job-access-to-your-storage-account) in dit artikel.

## <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Met behulp van Azure Resource Manager kunt u de implementatie van uw Stream Analytics taak volledig automatiseren. U kunt Resource Manager-sjablonen implementeren met behulp van Azure PowerShell of de [Azure cli](/cli/azure/). In de onderstaande voor beelden wordt de Azure CLI gebruikt.


1. U kunt een **micro soft. StreamAnalytics/streamingjobs-** resource maken met een beheerde identiteit door de volgende eigenschap op te nemen in de resource sectie van uw Resource Manager-sjabloon:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Met deze eigenschap geeft u Azure Resource Manager om de identiteit voor uw Stream Analytics taak te maken en te beheren. Hieronder ziet u een voor beeld van een resource manager-sjabloon waarmee een Stream Analytics taak wordt geïmplementeerd met beheerde identiteiten ingeschakeld en een BLOB-uitvoer filter dat gebruikmaakt van beheerde identiteit:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    De bovenstaande taak kan worden geïmplementeerd in de **ExampleGroup** van de resource groep met behulp van de onderstaande Azure cli-opdracht:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Nadat de taak is gemaakt, kunt u Azure Resource Manager gebruiken om de volledige definitie van de taak op te halen.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    De bovenstaande opdracht retourneert een antwoord zoals hieronder wordt weer gegeven:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Noteer de **principalId** van de definitie van de taak, waarmee de beheerde identiteit van uw taak wordt geïdentificeerd binnen Azure Active Directory en in de volgende stap wordt gebruikt om de stream Analytics-taak toegang te verlenen tot het opslag account.

3. Nu de taak is gemaakt, raadpleegt u de sectie [de stream Analytics-taak toegang geven tot uw opslag account](#give-the-stream-analytics-job-access-to-your-storage-account) in dit artikel.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>De Stream Analytics-taak toegang geven tot uw opslag account

Er zijn twee toegangs niveaus die u kunt kiezen om uw Stream Analytics taak te geven:

1. **Toegang tot container niveau:** met deze optie krijgt de taak toegang tot een specifieke bestaande container.
2. **Toegang tot account niveau:** deze optie geeft de taak algemene toegang tot het opslag account, inclusief de mogelijkheid om nieuwe containers te maken.

Tenzij u de taak nodig hebt om namens u containers te maken, moet u **toegang op container niveau** kiezen, omdat met deze optie de taak het minimale niveau van toegang dat vereist is. Beide opties worden hieronder uitgelegd voor de Azure Portal en de opdracht regel.

### <a name="grant-access-via-the-azure-portal"></a>Toegang verlenen via de Azure Portal

#### <a name="container-level-access"></a>Toegang op container niveau

1. Ga naar het configuratie deel venster van de container in uw opslag account.

2. Selecteer **Access Control (IAM)** aan de linkerkant.

3. Klik onder de sectie toewijzing van een rol toevoegen op **toevoegen**.

4. In het deel venster roltoewijzing:

    1. De **rol** instellen op ' BLOB gegevens Inzender '
    2. Zorg ervoor dat de vervolg keuzelijst **toegang toewijzen aan** is ingesteld op Azure AD-gebruiker,-groep of Service-Principal.
    3. Typ de naam van uw Stream Analytics-taak in het zoek veld.
    4. Selecteer uw Stream Analytics-taak en klik op **Opslaan**.

   ![Toegang tot de container verlenen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Toegang tot account niveau

1. Ga naar uw opslagaccount.

2. Selecteer **Access Control (IAM)** aan de linkerkant.

3. Klik onder de sectie toewijzing van een rol toevoegen op **toevoegen**.

4. In het deel venster roltoewijzing:

    1. De **rol** instellen op ' BLOB gegevens Inzender '
    2. Zorg ervoor dat de vervolg keuzelijst **toegang toewijzen aan** is ingesteld op Azure AD-gebruiker,-groep of Service-Principal.
    3. Typ de naam van uw Stream Analytics-taak in het zoek veld.
    4. Selecteer uw Stream Analytics-taak en klik op **Opslaan**.

   ![Account toegang verlenen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Toegang verlenen via de opdracht regel

#### <a name="container-level-access"></a>Toegang op container niveau

Voer de volgende opdracht uit met behulp van de Azure CLI om toegang te geven tot een specifieke container:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Toegang tot account niveau

Voer de volgende opdracht uit met behulp van de Azure CLI om toegang te krijgen tot het hele account:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET-toegang inschakelen

Bij het configureren van **firewalls en virtuele netwerken** van uw opslag account kunt u optioneel netwerk verkeer van andere vertrouwde micro soft-Services toestaan. Wanneer Stream Analytics verifieert met behulp van beheerde identiteit, geeft het bewijs dat de aanvraag afkomstig is van een vertrouwde service. Hieronder vindt u instructies om deze VNET-toegangs uitzondering in te scha kelen.

1.    Ga naar het deel venster firewalls en virtuele netwerken in het configuratie venster van het opslag account.
2.    Zorg ervoor dat de optie ' vertrouwde micro soft-Services voor toegang tot dit opslag account toestaan ' is ingeschakeld.
3.    Als u het hebt ingeschakeld, klikt u op **Opslaan**.

   ![VNET-toegang inschakelen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Beheerde identiteit verwijderen

De beheerde identiteit die is gemaakt voor een Stream Analytics taak wordt alleen verwijderd wanneer de taak wordt verwijderd. Het is niet mogelijk om de beheerde identiteit te verwijderen zonder de taak te verwijderen. Als u de beheerde identiteit niet meer wilt gebruiken, kunt u de verificatie methode voor de uitvoer wijzigen. De beheerde identiteit blijft bestaan totdat de taak is verwijderd en wordt gebruikt als u ervoor kiest beheerde identiteits verificatie opnieuw te gebruiken.

## <a name="limitations"></a>Beperkingen
Hieronder vindt u de huidige beperkingen van deze functie:

1. Klassieke Azure Storage-accounts.

2. Azure-accounts zonder Azure Active Directory.

3. Toegang met meerdere tenants wordt niet ondersteund. De service-principal die is gemaakt voor een bepaalde Stream Analytics taak moet zich in dezelfde Azure Active Directory Tenant bevinden waarin de taak is gemaakt, en kan niet worden gebruikt met een resource die zich in een andere Azure Active Directory Tenant bevindt.

4. De door de [gebruiker toegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md) wordt niet ondersteund. Dit betekent dat de gebruiker niet in staat is om hun eigen Service-Principal in te voeren om te worden gebruikt door hun Stream Analytics-taak. De Service-Principal moet worden gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de uitvoer van Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
