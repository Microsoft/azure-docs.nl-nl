---
title: Service-Principal toevoegen aan Azure Analysis Services beheerdersrol | Microsoft Docs
description: Meer informatie over het toevoegen van een Automation-Service-Principal aan de rol van Azure Analysis Services-server beheerder
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100725"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Een Service-Principal toevoegen aan de rol Server beheerder 

 Voor het automatiseren van Power shell-taken zonder toezicht moet een service-principal over **Server beheerders** rechten beschikken voor de Analysis Services server die wordt beheerd. In dit artikel wordt beschreven hoe u een Service-Principal kunt toevoegen aan de rol Server Administrators op een Azure AS-server. U kunt dit doen met behulp van SQL Server Management Studio of een resource manager-sjabloon. 

> [!NOTE]
> Service-principals moeten rechtstreeks aan de rol Server beheerder worden toegevoegd. Het toevoegen van een service-principal aan een beveiligings groep en het toevoegen van die beveiligings groep aan de rol Server beheerder wordt niet ondersteund. 

## <a name="before-you-begin"></a>Voordat u begint
Voordat u deze taak voltooit, moet u een Service-Principal hebben geregistreerd in Azure Active Directory.

[Service-principal maken - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio gebruiken

U kunt Server beheerders configureren met behulp van SQL Server Management Studio (SSMS). Als u deze taak wilt volt ooien, moet u beschikken over [Server beheerders](analysis-services-server-admins.md) machtigingen voor de Azure-server. 

1. Maak in SSMS verbinding met uw Azure als server.
2. Klik in **Server eigenschappen**  >  **beveiliging** op **toevoegen**.
3. In **een gebruiker of groep selecteren** zoekt u de geregistreerde app op naam, selecteert u en klikt u vervolgens op **toevoegen**.

    ![Zoeken naar Service-Principal-account](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Controleer de account-ID van de Service-Principal en klik vervolgens op **OK**.
    
    ![Scherm afbeelding met de ID van de Service-Principal-account en wordt de knop OK gemarkeerd.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Een Resource Manager-sjabloon gebruiken

U kunt Server beheerders ook configureren door de Analysis Services server te implementeren met behulp van een Azure Resource Manager sjabloon. De identiteit waarmee de implementatie wordt uitgevoerd, moet deel uitmaken van de rol **Inzender** voor de resource in [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> De Service-Principal moet worden toegevoegd met de indeling `app:{service-principal-client-id}@{azure-ad-tenant-id}` .

De volgende Resource Manager-sjabloon implementeert een Analysis Services server met een opgegeven service-principal die is toegevoegd aan de Analysis Services beheerdersrol:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Beheerde identiteiten gebruiken

Een beheerde identiteit kan ook worden toegevoegd aan de lijst met beheerders van Analysis Services. U hebt bijvoorbeeld een [logische app met een door het systeem toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md)en wilt deze de mogelijkheid geven om uw Analysis Services server te beheren.

In de meeste delen van de Azure Portal en Api's worden beheerde identiteiten geïdentificeerd met behulp van de object-ID van de Service-Principal. Analysis Services vereist echter dat ze worden geïdentificeerd aan de hand van hun client-ID. U kunt de Azure CLI gebruiken om de client-ID voor een service-principal te verkrijgen:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

U kunt ook Power shell gebruiken:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

U kunt deze client-ID vervolgens gebruiken in combi natie met de Tenant-ID om de beheerde identiteit toe te voegen aan de Analysis Services beheerders lijst, zoals hierboven wordt beschreven.

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server Power shell-module downloaden](/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](/sql/ssms/download-sql-server-management-studio-ssms)