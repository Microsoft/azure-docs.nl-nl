---
title: Apps implementeren met sjablonen
description: Vind hulp bij het maken van Azure Resource Manager sjablonen voor het inrichten en implementeren van App Service-apps.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961615"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Richt lijnen voor het implementeren van web-apps met behulp van Azure Resource Manager sjablonen

In dit artikel vindt u aanbevelingen voor het maken van Azure Resource Manager sjablonen voor het implementeren van Azure App Service oplossingen. Deze aanbevelingen kunnen u helpen veelvoorkomende problemen te voor komen.

## <a name="define-dependencies"></a>Afhankelijkheden definiëren

Voor het definiëren van afhankelijkheden voor web-apps moet u weten hoe de resources in een web-app werken. Als u afhankelijkheden in een onjuiste volg orde opgeeft, kunt u implementatie fouten veroorzaken of een race voorwaarde maken waarmee de implementatie wordt gestallion.

> [!WARNING]
> Als u een MSDeploy-site-extensie in uw sjabloon opneemt, moet u configuratie resources instellen als afhankelijk van de MSDeploy-resource. Configuratie wijzigingen zorgen ervoor dat de site asynchroon opnieuw wordt gestart. Door de configuratie bronnen afhankelijk van MSDeploy te maken, zorgt u ervoor dat MSDeploy eindigt voordat de site opnieuw wordt opgestart. Zonder deze afhankelijkheden wordt de site mogelijk opnieuw opgestart tijdens het implementatie proces van MSDeploy. Zie voor een voorbeeld sjabloon [wordpress-sjabloon met de afhankelijkheid van Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

In de volgende afbeelding ziet u de afhankelijkheids volgorde voor verschillende App Service resources:

![Web-app-afhankelijkheden](media/web-sites-rm-template-guidance/web-dependencies.png)

U implementeert resources in de volgende volg orde:

**Tier 1**
* App Service plan.
* Alle andere gerelateerde resources, zoals data bases of opslag accounts.

**Tier 2**
* Web-app--is afhankelijk van het App Service-abonnement.
* Azure-toepassing Insights-exemplaar dat de server farm bedoelt, is afhankelijk van het App Service plan.

**Laag 3**
* Broncode beheer: is afhankelijk van de web-app.
* Site-uitbrei ding MSDeploy--is afhankelijk van de web-app.
* Azure-toepassing Insights-exemplaar dat de web-app bedoelt, is afhankelijk van de web-app.

**Laag 4**
* App Service certificaat: afhankelijk van broncode beheer of MSDeploy, indien aanwezig. Anders is dit afhankelijk van de web-app.
* Configuratie-instellingen (verbindings reeksen, web.config waarden, app-instellingen): afhankelijk van broncode beheer of MSDeploy, indien aanwezig. Anders is dit afhankelijk van de web-app.

**Laag 5**
* Host-naam bindingen: afhankelijk van het certificaat, indien aanwezig. Anders is dit afhankelijk van een resource van een hoger niveau.
* Site-extensies: afhankelijk van de huidige configuratie-instellingen. Anders is dit afhankelijk van een resource van een hoger niveau.

Normaal gesp roken bevat uw oplossing slechts enkele van deze resources en lagen. Voor ontbrekende lagen wijst u lagere resources toe aan de volgende hogere laag.

In het volgende voor beeld ziet u een deel van een sjabloon. De waarde van de connection string configuratie is afhankelijk van de MSDeploy-extensie. De MSDeploy-extensie is afhankelijk van de web-app en de data base. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Zie voor een kant-en-klaar voor beeld dat gebruikmaakt van de bovenstaande code [sjabloon: een eenvoudige Umbraco-web-app bouwen](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Informatie over MSDeploy-fouten zoeken

Als uw Resource Manager-sjabloon gebruikmaakt van MSDeploy, kan het lastig zijn om de implementatie fout berichten te begrijpen. Als u meer informatie wilt over het uitvoeren van een mislukte implementatie, voert u de volgende stappen uit:

1. Ga naar de kudu- [console](https://github.com/projectkudu/kudu/wiki/Kudu-console)van de site.
2. Blader naar de map op D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Zoek naar de appManagerStatus.xml-en appManagerLog.xml-bestanden. Het eerste bestand registreert de status. Het tweede bestand registreert informatie over de fout. Als de fout niet aan u is toegevoegd, kunt u deze toevoegen wanneer u hulp nodig hebt bij het [forum](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Kies een unieke naam voor de web-app

De naam van uw web-app moet wereld wijd uniek zijn. U kunt een naam Conventie gebruiken die waarschijnlijk uniek is, of u kunt de [functie Unique string](../azure-resource-manager/templates/template-functions-string.md#uniquestring) gebruiken om te helpen bij het genereren van een unieke naam.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Web-app-certificaat van Key Vault implementeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als uw sjabloon een [micro soft. Web/certificates](/azure/templates/microsoft.web/certificates) -bron bevat voor TLS/SSL-binding en het certificaat wordt opgeslagen in een Key Vault, moet u ervoor zorgen dat de app service identiteit toegang heeft tot het certificaat.

In Global Azure heeft de App Service Service-Principal de ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Als u toegang wilt verlenen aan Key Vault voor de App Service Service-Principal, gebruikt u:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

In Azure Government heeft de App Service Service-Principal de ID **6a02c803-DaFD-4136-b4c3-5a6f318b4714**. Gebruik die ID in het vorige voor beeld.

Selecteer in uw Key Vault **certificaten** en **genereren/importeren** om het certificaat te uploaden.

![Certificaat importeren](media/web-sites-rm-template-guidance/import-certificate.png)

Geef in uw sjabloon de naam op van het certificaat voor `keyVaultSecretName` .

Zie [een web-app-certificaat implementeren vanuit Key Vault Secret en dit gebruiken voor het maken van SSL-binding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)voor een voorbeeld sjabloon.

## <a name="next-steps"></a>Volgende stappen

* Zie micro [services zoals verwacht inrichten en implementeren in azure](deploy-complex-application-predictably.md)voor een zelf studie over het implementeren van web-apps met een sjabloon.
* Zie [Azure Resource Manager-sjabloon verwijzing](/azure/templates/)voor meer informatie over de JSON-syntaxis en eigenschappen voor resource typen in sjablonen.