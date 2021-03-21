---
title: 'Zelfstudie: aangepaste acties en resources'
description: In deze zelfstudie wordt beschreven hoe u een door Azure beheerde toepassing maakt met een aangepaste Azure-provider.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50a65583c41dd94b174a33432afcf42b31e67df0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437045"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Zelfstudie: Een beheerde toepassing maken met aangepaste acties en resources

In deze zelfstudie maakt u uw eigen beheerde toepassing met aangepaste acties en resources. De beheerde toepassing bevat een aangepaste actie op de pagina `Overview`, een aangepast resourcetype dat wordt weer gegeven als een afzonderlijk menu-item in `Table of Content` en een aangepaste contextactie op de aangepaste resourcepagina.

Deze zelfstudie bevat de volgende stappen:

> [!div class="checklist"]
> * Een definitiebestand ontwerpen van de gebruikersinterface voor het maken van een exemplaar van een beheerde toepassing
> * Een implementatiesjabloon ontwerpen met de [aangepaste Azure-provider](../custom-providers/overview.md), het Azure Storage-account en een Azure-functie
> * Het artefact weergavedefinitie ontwerpen met aangepaste acties en resources
> * Een definitie voor een beheerde toepassing implementeren
> * Een exemplaar van een beheerde toepassing implementeren
> * Aangepaste acties uitvoeren en aangepaste resources maken

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u het volgende weten:

* Hoe u [een definitie van een beheerde toepassing kunt maken en publiceren](publish-service-catalog-app.md).
* Hoe u [de servicecatalogus-app via Azure Portal kunt implementeren](deploy-service-catalog-quickstart.md).
* Hoe u [een Azure Portal-gebruikersinterface maken voor uw beheerde toepassing kunt maken](create-uidefinition-overview.md).
* Mogelijkheden van het [artefact weergavedefinitie](concepts-view-definition.md).
* Mogelijkheden van de [aangepaste Azure-provider](../custom-providers/overview.md).

## <a name="user-interface-definition"></a>Definitie gebruikersinterface

In deze zelfstudie maakt u een beheerde toepassing. De bijbehorende beheerde resourcegroep bevat een aangepast providerexemplaar, een opslagaccount en een functie. Met de Azure-functie die in dit voorbeeld wordt gebruikt, wordt een API geïmplementeerd die voor acties en resources aangepaste providerbewerkingen verwerkt. Het Azure Storage-account wordt gebruikt als basisopslag voor de aangepaste providerresources.

De definitie van de gebruikersinterface voor het maken van een exemplaar van een beheerde toepassing bevat de invoerelementen `funcname` en `storagename`. De naam van het opslagaccount en de functienaam moeten globaal uniek zijn. Standaard worden functiebestanden geïmplementeerd op basis van het [pakket met voorbeeldfuncties](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip). U kunt dit wijzigen door een invoerelement voor een pakketkoppeling toe te voegen in *createUiDefinition.json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

en een uitvoer in *createUiDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

U kunt het volledige *createUiDefinition.json*-voorbeeld vinden op [Naslaginformatie: Artefacten voor gebruikersinterface-elementen](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Sjabloon met aangepaste provider

Als u een exemplaar van een beheerde toepassing wilt maken met een aangepaste provider, moet u in uw **mainTemplate.json** een aangepaste providerresource definiëren met de naam **public** en het type **Microsoft.CustomProviders/resourceProviders**. In die resource definieert u de resourcetypen en acties voor uw service. Als u exemplaren van de Azure-functie en het Azure Storage-account wilt implementeren, moet u resources van het type `Microsoft.Web/sites` respectievelijk `Microsoft.Storage/storageAccounts` definiëren.

In deze zelfstudie maakt u een resourcetype `users`, een aangepaste actie `ping` en een aangepaste actie `users/contextAction`, die worden uitgevoerd in een context van een aangepaste resource `users`. Voor elk resourcetype en elke actie geeft u een eindpunt op dat verwijst naar de functie met de naam die is opgegeven in [createUiDefinition.json](#user-interface-definition). Geef **routingType** op als `Proxy,Cache` voor resourcetypen en `Proxy` voor acties:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

U kunt het volledige *mainTemplate.json*-voorbeeld vinden op [Naslaginformatie: Artefact implementatiesjabloon](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Definitie-artefact weergeven

Als u een gebruikersinterface wilt definiëren waar in uw beheerde toepassing aangepaste acties en aangepaste resources zijn opgenomen, moet u artefact **viewDefinition.json** ontwerpen. Zie [Artefact weergavedefinitie in Azure Managed Applications](concepts-view-definition.md) voor meer informatie over het artefact weergavedefinitie.

In deze zelfstudie definieert u het volgende:
* Een pagina *Overzicht* met een werkbalkknop waarmee de aangepaste actie `TestAction` wordt aangegeven met de eenvoudige tekstinvoer.
* Een pagina *Gebruikers* waarmee een aangepast resourcetype `users` wordt aangegeven.
* Een aangepaste resourceactie `users/contextAction` op de pagina *Gebruikers* die wordt uitgevoerd in een context van een aangepaste resource van het type `users`.

In het volgende voorbeeld ziet u de weergaveconfiguratie voor een pagina Overzicht:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Het onderstaande voorbeeld bevat de configuratie van de resourcespagina Gebruikers met de aangepaste resourceactie:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

U kunt het volledige *viewDefinition.json*-voorbeeld vinden op [Naslaginformatie: Artefact weergavedefinitie](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definitie van beheerde toepassingen

Verpak de volgende beheerde-toepassingsartefacten in een zip-archief en upload het naar opslag:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Alle bestanden moeten op hoofdniveau zijn. Het pakket met artefacten kan in elke opslagruimte worden opgeslagen, bijvoorbeeld in GitHub-blob of Azure Storage-accountblob. Hier volgt een script voor het uploaden van het toepassingspakket naar het opslagaccount: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Voer het Azure CLI-script hieronder uit of volg de stappen in Azure Portal om een definitie van een door een servicecatalogus beheerde toepassing te implementeren:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de Azure-portal de optie **Alle services**. In de lijst met resources typt en selecteert u **Centrum voor beheerde toepassingen**.
2. Kies in het **Managed Applications Center** **Definitie van servicecatalogustoepassing** en klik op **Toevoegen**. 
    
    ![Servicecatalogus toevoegen](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Geef waarden op voor het maken van een servicecatalogusdefinitie:

    * Geef een unieke **naam**, een **weergavenaam** en een *beschrijving* (optioneel) voor de servicecatalogusdefinitie op.
    * Selecteer het **abonnement**, de **resourcegroep** en de **locatie** waar de toepassingsdefinitie moet worden gemaakt. U kunt dezelfde resourcegroep gebruiken die wordt gebruikt voor een zip-pakket of voor het maken van een nieuwe resourcegroep.
    * Geef voor een **pakketbestand-URI** het pad op naar het zip-bestand dat u in de vorige stap hebt gemaakt.

    ![Waarden opgeven](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. In de sectie Verificatie en vergrendelingsniveau selecteert u **Autorisatie toevoegen**.

    ![Autorisatie toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecteer een Azure Active Directory-groep om resources te beheren en selecteer **OK**.

   ![Autorisatiegroep toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Wanneer u alle waarden hebt ingevoerd, selecteert u **Maken**.

   ![Definitie van beheerde toepassing maken](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Exemplaar van beheerde toepassing

Wanneer de definitie van de beheerde toepassing is geïmplementeerd, voert u het onderstaande script uit of volgt u de stappen in Azure Portal om uw exemplaar van de beheerde toepassing te implementeren met een aangepaste provider:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de Azure-portal de optie **Alle services**. In de lijst met resources typt en selecteert u **Centrum voor beheerde toepassingen**.
2. Kies in het **Centrum voor beheerde toepassingen** **Servicecatalogustoepassing** en klik op **Toevoegen**. 

    ![Beheerde toepassing toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Op de pagina **Servicecatalogustoepassingen** typt u de weergavenaam van de servicecatalogusdefinitie in het zoekvak. Selecteer de definitie die u in de vorige stap hebt gemaakt en klik op **Maken**.

    ![Servicecatalogus selecteren](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Geef waarden op om een exemplaar van een beheerde toepassing te maken op basis van een servicecatalogusdefinitie:

    * Selecteer het **abonnement**, de **resourcegroep** en de **locatie** waar het toepassingsexemplaar moet worden gemaakt.
    * Geef een unieke naam op voor de Azure-functie en het Azure Storage-account.

    ![Toepassingsinstellingen](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Wanneer de validatie is voltooid, klikt u op **OK** om een exemplaar van een beheerde toepassing te implementeren. 
    
    ![Beheerde toepassing implementeren](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Aangepaste acties en resources

Nadat het exemplaar van de servicecatalogustoepassing is geïmplementeerd, hebt u twee nieuwe resourcegroepen. De eerste de resource groep, `applicationGroup`, bevat een exemplaar van de beheerde toepassing, de tweede resourcegroep, `managedResourceGroup`, bevat de resources voor de beheerde toepassing, inclusief de **aangepaste provider**.

![Toepassingsresourcegroepen](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

U kunt naar het exemplaar van de beheerde toepassing gaan en een **aangepaste actie** uitvoeren op de pagina Overview, de aangepaste resource **users** maken op de pagina Users en de **aangepaste contextactie** uitvoeren voor de aangepaste resource.

* Ga naar de pagina Overview en klik op de knop Ping Action:

![Aangepaste actie uitvoeren](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Ga naar de pagina Users en klik op de knop Add. Geef invoer voor het maken van een resource op en verzend het formulier:

![Schermopname van de knop Toevoegen die is geselecteerd bij Gebruikers.](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Ga naar de pagina Users, selecteer een resource voor users en klik op Custom Context Action:

![Schermopname van de optie Custom Context Action die is geselecteerd.](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Hulp nodig?

Als u vragen hebt over Azure Managed Applications, kunt u dit vragen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Misschien is er een soortgelijke vraag gesteld en beantwoord, dus controleer dit eerst vóór u een vraag stelt. Voeg de tag `azure-managedapps` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

Zie [Door Azure beheerde toepassingen in Marketplace](../../marketplace/create-new-azure-apps-offer.md) voor het in Azure Marketplace publiceren van uw beheerde toepassing.

Meer informatie over [aangepaste Azure-providers](../custom-providers/overview.md).
