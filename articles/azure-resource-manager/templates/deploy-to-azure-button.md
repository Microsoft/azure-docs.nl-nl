---
title: De knop Implementeren in Azure
description: Gebruik de knop om Azure Resource Manager sjablonen te implementeren vanuit een GitHub-opslag plaats.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: e25d49571347bb5ed27dbd52bb60c68cbeb4360d
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543772"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Een implementatie knop gebruiken voor het implementeren van sjablonen uit de GitHub-opslag plaats

In dit artikel wordt beschreven hoe u de knop **implementeren in azure** gebruikt om sjablonen te implementeren vanuit een github-opslag plaats. U kunt de knop rechtstreeks toevoegen aan het _README.MD_ -bestand in de GitHub-opslag plaats. U kunt ook de knop toevoegen aan een webpagina die verwijst naar de opslag plaats.

Het implementatie bereik wordt bepaald door het sjabloon schema. Zie voor meer informatie:

- [resource groepen](deploy-to-resource-group.md)
- [geabonneerd](deploy-to-subscription.md)
- [beheer groepen](deploy-to-management-group.md)
- [tenants](deploy-to-tenant.md)

## <a name="use-common-image"></a>Algemene installatie kopie gebruiken

Gebruik de volgende afbeelding om de knop toe te voegen aan uw webpagina of opslag plaats:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

De afbeelding wordt weer gegeven als:

![De knop Implementeren in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL maken voor het implementeren van de sjabloon

Als u de URL voor uw sjabloon wilt maken, begint u met de onbewerkte URL naar de sjabloon in uw opslag plaats. Als u de onbewerkte URL wilt zien, selecteert u **RAW**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="RAW selecteren":::

De indeling van de URL is:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Converteer vervolgens de URL naar een URL-gecodeerde waarde. U kunt een online coderings programma gebruiken of een opdracht uitvoeren. Het volgende Power shell-voor beeld laat zien hoe een URL een waarde codeert.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

De voor beeld-URL heeft de volgende waarde wanneer URL is gecodeerd.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Elke koppeling begint met dezelfde basis-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Voeg uw sjabloon koppeling met URL-code ring toe aan het einde van de basis-URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

U hebt de volledige URL voor de koppeling.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Als u Git gebruikt [met Azure opslag plaatsen](/azure/devops/repos/git/) in plaats van een github opslag plaats, kunt u nog steeds de knop **implementeren in azure** gebruiken. Zorg ervoor dat uw opslag plaats openbaar is. Gebruik de [bewerking items](/rest/api/azure/devops/git/items/get) om de sjabloon op te halen. Uw aanvraag moet de volgende indeling hebben:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Deze aanvraag-URL versleutelen.

## <a name="create-deploy-to-azure-button"></a>Knop voor implementeren naar Azure maken

Ten slotte plaatst u de koppeling en de afbeelding samen.

Als u de knop met prijs verlaging in het _README.MD_ -bestand in uw github-opslag plaats of een webpagina wilt toevoegen, gebruikt u:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Gebruik voor HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Voor git met Azure opslag plaats heeft de knop de volgende indeling:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende knop om de volledige oplossing te testen:

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

In de portal wordt een deel venster weer gegeven waarmee u eenvoudig parameter waarden kunt opgeven. De para meters worden vooraf ingevuld met de standaard waarden van de sjabloon.

![De portal gebruiken om te implementeren](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over sjablonen.
