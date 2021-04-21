---
title: Apps implementeren met sjablonen
description: Vind richtlijnen voor het maken Azure Resource Manager sjablonen voor het inrichten en implementeren App Service apps.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ed6edeadfb1c6f73cc10771d4a5328e7bddb3642
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835160"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Richtlijnen voor het implementeren van web-apps met behulp van Azure Resource Manager sjablonen

Dit artikel bevat aanbevelingen voor het maken van Azure Resource Manager voor het implementeren van Azure App Service oplossingen. Met deze aanbevelingen kunt u veelvoorkomende problemen voorkomen.

## <a name="define-dependencies"></a>Afhankelijkheden definiëren

Voor het definiëren van afhankelijkheden voor web-apps moet u weten hoe de resources in een web-app communiceren. Als u afhankelijkheden in een onjuiste volgorde opgeeft, kunt u implementatiefouten veroorzaken of een racevoorwaarde maken die de implementatie vastzet.

> [!WARNING]
> Als u een MSDeploy-site-extensie in uw sjabloon op te nemen, moet u configuratieresources instellen als afhankelijk van de MSDeploy-resource. Configuratiewijzigingen zorgen ervoor dat de site asynchroon opnieuw wordt opgestart. Door de configuratiebronnen afhankelijk te maken van MSDeploy, zorgt u ervoor dat MSDeploy is klaar voordat de site opnieuw wordt opgestart. Zonder deze afhankelijkheden kan de site opnieuw worden opgestart tijdens het implementatieproces van MSDeploy. Zie WordPress-sjabloon met Web Deploy Dependency voor een [voorbeeldsjabloon.](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)

In de volgende afbeelding ziet u de afhankelijkheidsvolgorde voor verschillende App Service resources:

![Afhankelijkheden van web-apps](media/web-sites-rm-template-guidance/web-dependencies.png)

U implementeert resources in de volgende volgorde:

**Tier 1**
* App Service abonnement.
* Alle andere gerelateerde resources, zoals databases of opslagaccounts.

**Tier 2**
* Web-app is afhankelijk van het App Service abonnement.
* Azure-toepassing Insights-exemplaar dat is gericht op de serverfarm, is afhankelijk van het App Service plan.

**Laag 3**
* Broncodebeheer is afhankelijk van de web-app.
* DE SITE-extensie MSDeploy is afhankelijk van de web-app.
* Azure-toepassing Insights-exemplaar dat is gericht op de web-app, is afhankelijk van de web-app.

**Laag 4**
* App Service is afhankelijk van broncodebeheer of MSDeploy als een van beide aanwezig is. Anders is dit afhankelijk van de web-app.
* Configuratie-instellingen (verbindingsreeksen, web.config waarden, app-instellingen) zijn afhankelijk van broncodebeheer of MSDeploy als een van beide aanwezig is. Anders is dit afhankelijk van de web-app.

**Laag 5**
* Hostnaambindingen zijn afhankelijk van het certificaat, indien aanwezig. Anders is dit afhankelijk van een resource op een hoger niveau.
* Site-extensies zijn afhankelijk van de configuratie-instellingen, indien aanwezig. Anders is dit afhankelijk van een resource op een hoger niveau.

Normaal gesproken bevat uw oplossing slechts enkele van deze resources en lagen. Wijs voor ontbrekende lagen lagere resources toe aan de laag next-higher.

In het volgende voorbeeld ziet u een deel van een sjabloon. De waarde van de connection string is afhankelijk van de MSDeploy-extensie. De MSDeploy-extensie is afhankelijk van de web-app en database. 

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

Zie Sjabloon: een eenvoudige [Umbraco-web-app](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)bouwen voor een kant-en-klaar voorbeeld dat gebruikmaakt van de bovenstaande code.

## <a name="find-information-about-msdeploy-errors"></a>Informatie zoeken over MSDeploy-fouten

Als uw Resource Manager MSDeploy gebruikt, kunnen de implementatiefoutberichten moeilijk te begrijpen zijn. Probeer de volgende stappen voor meer informatie na een mislukte implementatie:

1. Ga naar de [Kudu-console van de site.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
2. Blader naar de map op D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Zoek naar de appManagerStatus.xml- en appManagerLog.xml bestanden. Het eerste bestand registreert de status. Het tweede bestand registreert informatie over de fout. Als de fout voor u niet duidelijk is, kunt u deze opnemen wanneer u om hulp vraagt op het [forum](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Een unieke web-app-naam kiezen

De naam voor uw web-app moet wereldwijd uniek zijn. U kunt een naamconventie gebruiken die waarschijnlijk uniek is, of u kunt de [functie uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) gebruiken om te helpen bij het genereren van een unieke naam.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Web-app-certificaat implementeren vanuit Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als uw sjabloon een [Microsoft.Web/certificates-resource](/azure/templates/microsoft.web/certificates) voor TLS/SSL-binding bevat en het certificaat is opgeslagen in een Key Vault, moet u ervoor zorgen dat de App Service-identiteit toegang heeft tot het certificaat.

In globale Azure heeft de App Service-service-principal de id **abfa0a7c-a6b6-4736-8310-5855508787cd**. Als u toegang wilt verlenen Key Vault voor de App Service service-principal, gebruikt u:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

In Azure Government heeft de App Service-service-principal de id **6a02c803-weekd-4136-b4c3-5a6f318b4714.** Gebruik die id in het vorige voorbeeld.

Selecteer in Key Vault pagina Certificaten **en** **Genereren/importeren om** het certificaat te uploaden.

![Certificaat importeren](media/web-sites-rm-template-guidance/import-certificate.png)

Geef in uw sjabloon de naam op van het certificaat voor de `keyVaultSecretName` .

Zie Deploy a Web App certificate from Key Vault secret (Een web-app-certificaat implementeren vanuit een Key Vault-geheim) en gebruik dit voor het maken [van SSL-binding voor een voorbeeldsjabloon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)

## <a name="next-steps"></a>Volgende stappen

* Zie Voor een zelfstudie over het implementeren van web-apps met een sjabloon Microservices inrichten en implementeren [die voorspelbaar zijn in Azure.](deploy-complex-application-predictably.md)
* Zie voor meer informatie over JSON-syntaxis en eigenschappen voor resourcetypen in [sjablonen Azure Resource Manager sjabloonverwijzing](/azure/templates/).
