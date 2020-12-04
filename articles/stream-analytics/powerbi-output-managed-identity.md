---
title: Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI-uitvoer
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Power BI uitvoer.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: dd667202a329148e498d0e25ee15110de5d7448a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573405"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi-preview"></a>Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI (preview)

Met [beheerde identiteits verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor uitvoer naar Power BI hebt stream Analytics-taken direct toegang tot een werk ruimte binnen uw Power bi-account. Met deze functie kunnen implementaties van Stream Analytics taken volledig worden geautomatiseerd, omdat het niet langer nodig is dat een gebruiker zich interactief aanmeldt bij Power BI via de Azure Portal. Daarnaast zijn langlopende taken die naar Power BI schrijven, nu beter worden ondersteund, omdat u de taak niet regel matig opnieuw moet autoriseren.

In dit artikel wordt beschreven hoe u beheerde identiteit kunt inschakelen voor de Power BI uitvoer (en) van een Stream Analytics-taak via de Azure Portal en via een Azure Resource Manager-implementatie.

## <a name="prerequisites"></a>Vereisten

Het volgende is vereist voor het gebruik van deze functie:

- Een Power BI-account met een [Pro-licentie](/power-bi/service-admin-purchasing-power-bi-pro).

- Een bijgewerkte werk ruimte in uw Power BI-account. Raadpleeg [de aankondiging van Power bi](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) van deze functie voor meer informatie.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Een Stream Analytics taak maken met behulp van de Azure Portal

1. Maak een nieuwe Stream Analytics-taak of open een bestaande taak in de Azure Portal. Selecteer in de menu balk aan de linkerkant van het scherm **beheerde identiteit** onder **configureren**. Zorg ervoor dat de door het systeem toegewezen beheerde identiteit gebruiken is geselecteerd en selecteer vervolgens de knop **Opslaan** aan de onderkant van het scherm.

   ![Stream Analytics beheerde identiteit configureren](./media/common/stream-analytics-enable-managed-identity.png)

2. Voordat u de uitvoer configureert, geeft u de Stream Analytics-taak toegang tot uw Power BI-werk ruimte door de instructies in de sectie [de stream Analytics-taak toegang geven tot uw Power bi werkruimte](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) in dit artikel te volgen.

3. Navigeer naar het gedeelte **uitvoer** van de taak van de stream-analyse, selecteer **+ toevoegen** en kies vervolgens **Power bi**. Selecteer vervolgens de knop **autoriseren** en meld u aan met uw Power bi-account.

   ![Autoriseren met Power BI-account](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Na de autorisatie wordt een vervolg keuzelijst gevuld met alle werk ruimten waartoe u toegang hebt. Selecteer de werk ruimte die u in de vorige stap hebt geautoriseerd. Selecteer vervolgens **beheerde identiteit** als de ' verificatie modus '. Klik ten slotte op de knop **Opslaan** .

   ![Power BI uitvoer configureren met beheerde identiteit](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Met Azure Resource Manager kunt u de implementatie van uw Stream Analytics taak volledig automatiseren. U kunt Resource Manager-sjablonen implementeren met behulp van Azure PowerShell of de [Azure cli](/cli/azure/). In de onderstaande voor beelden wordt de Azure CLI gebruikt.


1. U kunt een **micro soft. StreamAnalytics/streamingjobs-** resource maken met een beheerde identiteit door de volgende eigenschap op te nemen in de resource sectie van uw Resource Manager-sjabloon:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Met deze eigenschap geeft u Azure Resource Manager om de identiteit voor uw Stream Analytics taak te maken en te beheren. Hieronder ziet u een voor beeld van een resource manager-sjabloon waarmee een Stream Analytics taak wordt geïmplementeerd met beheerde identiteiten ingeschakeld en een Power BI uitvoer sink die gebruikmaakt van beheerde identiteit:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    Implementeer de bovenstaande taak in de **ExampleGroup** van de resource groep met behulp van de onderstaande Azure cli-opdracht:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Nadat de taak is gemaakt, gebruikt u Azure Resource Manager om de volledige definitie van de taak op te halen.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    De bovenstaande opdracht retourneert een antwoord zoals hieronder wordt weer gegeven:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Als u van plan bent Power BI REST API te gebruiken om de Stream Analytics taak toe te voegen aan uw Power BI-werk ruimte, noteert u de geretourneerde ' principalId '.

3. Nu de taak is gemaakt, gaat u verder met de sectie [de stream Analytics-taak toegang geven tot uw Power bi werk ruimte](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) in dit artikel.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>De Stream Analytics-taak toegang geven tot uw Power BI-werk ruimte

Nu de Stream Analytics-taak is gemaakt, kan deze toegang krijgen tot een Power BI-werk ruimte. Zodra u de toegang tot uw taak hebt gegeven, moet u enkele minuten wachten totdat de identiteit is door gegeven.

### <a name="use-the-power-bi-ui"></a>De Power BI-gebruikers interface gebruiken

   > [!Note]
   > Als u de Stream Analytics-taak wilt toevoegen aan uw Power BI-werk ruimte met behulp van de gebruikers interface, moet u ook toegang tot de Service-Principal inschakelen in de **instellingen voor ontwikkel aars** in de beheer portal van Power bi. Zie [aan de slag met een Service-Principal](/power-bi/developer/embed-service-principal) voor meer informatie.

1. Navigeer naar de toegangs instellingen van de werk ruimte. Raadpleeg dit artikel voor meer informatie: [toegang verlenen tot uw werk ruimte](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Typ de naam van uw Stream Analytics-taak in het tekstvak en selecteer **Inzender** als toegangs niveau.

3. Selecteer **toevoegen** en sluiten van het deel venster.

   ![Stream Analytics taak toevoegen aan Power BI werk ruimte](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>De Power BI Power shell-cmdlets gebruiken

1. Installeer de Power BI `MicrosoftPowerBIMgmt` Power shell-cmdlets.

   > [!Important]
   > Controleer of u versie 1.0.821 of hoger van de cmdlets gebruikt.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Meld u aan bij Power BI.

```powershell
Login-PowerBI
```

3. Uw Stream Analytics-taak als Inzender toevoegen aan de werk ruimte.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>De Power BI gebruiken REST API

De Stream Analytics-taak kan ook worden toegevoegd als een bijdrager aan de werk ruimte met behulp van de REST API groeps gebruiker rechtstreeks toevoegen. Volledige documentatie voor deze API vindt u hier: [groepen: groeps gebruiker toevoegen](/rest/api/power-bi/groups/addgroupuser).

**Voorbeeldaanvraag**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Aanvraagtekst
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Beheerde identiteit verwijderen

De beheerde identiteit die is gemaakt voor een Stream Analytics taak wordt alleen verwijderd wanneer de taak wordt verwijderd. Het is niet mogelijk om de beheerde identiteit te verwijderen zonder de taak te verwijderen. Als u de beheerde identiteit niet meer wilt gebruiken, kunt u de verificatie methode voor de uitvoer wijzigen. De beheerde identiteit blijft bestaan totdat de taak is verwijderd en wordt gebruikt als u ervoor kiest beheerde identiteits verificatie opnieuw te gebruiken.

## <a name="limitations"></a>Beperkingen
Hieronder vindt u de beperkingen van deze functie:

- Klassieke Power BI-werk ruimten worden niet ondersteund.

- Azure-accounts zonder Azure Active Directory.

- Toegang met meerdere tenants wordt niet ondersteund. De service-principal die is gemaakt voor een bepaalde Stream Analytics taak moet zich in dezelfde Azure Active Directory Tenant bevinden waarin de taak is gemaakt, en kan niet worden gebruikt met een resource die zich in een andere Azure Active Directory Tenant bevindt.

- De door de [gebruiker toegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md) wordt niet ondersteund. Dit betekent dat u uw eigen Service-Principal niet kunt invoeren om te worden gebruikt door hun Stream Analytics-taak. De Service-Principal moet worden gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Integratie van dash board Power BI met Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Meer informatie over de uitvoer van Azure Stream Analytics](./stream-analytics-define-outputs.md)