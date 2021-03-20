---
title: Een app verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van App Service resources van de ene regio naar een andere.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86524851"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Een App Service Resource verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u App Service-resources verplaatst naar een andere Azure-regio. U kunt uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, voor het implementeren van functies of services die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning.

App Service resources zijn regio-specifiek en kunnen niet worden verplaatst naar andere regio's. U moet een kopie maken van uw bestaande App Service-resources in de doel regio en vervolgens uw inhoud verplaatsen naar de nieuwe app. Als uw bron-app gebruikmaakt van een aangepast domein, kunt u [deze migreren naar de nieuwe app in de doel regio](manage-custom-dns-migrate-domain.md) wanneer u klaar bent.

Om het kopiëren van uw app gemakkelijker te maken, kunt u [een afzonderlijke app service-app klonen](app-service-web-app-cloning.md) in een app service plan in een andere regio, maar dit heeft wel [beperkingen](app-service-web-app-cloning.md#current-restrictions), met name dat het geen ondersteuning biedt voor Linux-apps.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de App Service-app zich in de Azure-regio bevindt waaruit u wilt verplaatsen.
- Zorg ervoor dat de doel regio App Service en een gerelateerde service ondersteunt waarvan u de resources wilt verplaatsen.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Voorbereiden

Identificeer alle App Service resources die u momenteel gebruikt. Bijvoorbeeld:

- App Service-apps
- [App Service-abonnementen](overview-hosting-plans.md)
- [Implementatiesites](deploy-staging-slots.md)
- [Aangepaste domeinen die zijn gekocht in azure](manage-custom-dns-buy-domain.md)
- [SSL-certificaten](configure-ssl-certificate.md)
- [Integratie van Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Hybride verbindingen](app-service-hybrid-connections.md).
- [Beheerde identiteiten](overview-managed-identity.md)
- [Back-upinstellingen](manage-backup.md)

Bepaalde resources, zoals geïmporteerde certificaten of hybride verbindingen, bevatten integratie met andere Azure-Services. Zie de documentatie voor de desbetreffende services voor informatie over het verplaatsen van deze resources over regio's.

## <a name="move"></a>Verplaatsen

1. [Maak een back-up van de bron-app](manage-backup.md).
1. [Maak een app in een nieuw app service plan in de doel regio](app-service-plan-manage.md#create-an-app-service-plan).
2. [De back-up herstellen in de doel-app](web-sites-restore.md)
2. Als u een aangepast domein gebruikt, [bindt u het preventief met de doel-app](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) met `awverify.` en [schakelt u het domein in de doel-app in](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configureer de rest van de doel-app zodat deze hetzelfde is als de bron-app en controleer uw configuratie.
4. Wanneer u klaar bent voor het aangepaste domein om te verwijzen naar de doel-app, moet u [de domein naam opnieuw toewijzen](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Bron resources opschonen

Verwijder de bron-app en het App Service plan. [Een App Service plan in de niet-gratis laag brengt kosten in rekening, zelfs als er geen app in wordt uitgevoerd.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Volgende stappen

[App-klonen Azure App Service met behulp van Power shell](app-service-web-app-cloning.md)