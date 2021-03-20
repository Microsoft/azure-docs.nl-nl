---
title: Ongeldige sjabloon fouten
description: Hierin wordt beschreven hoe u fouten met ongeldige sjablonen oplost tijdens het implementeren van Azure Resource Manager sjablonen.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185790"
---
# <a name="resolve-errors-for-invalid-template"></a>Fouten met een ongeldige sjabloon oplossen

In dit artikel wordt beschreven hoe u ongeldige sjabloon fouten kunt oplossen.

## <a name="symptom"></a>Symptoom

Wanneer u een sjabloon implementeert, wordt er een fout bericht weer gegeven met de volgende strekking:

```
Code=InvalidTemplate
Message=<varies>
```

Het fout bericht is afhankelijk van het type fout.

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door verschillende soorten fouten. Normaal gesp roken is er sprake van een syntaxis-of structureel fout in de sjabloon.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Oplossing 1: syntaxis fout

Als er een fout bericht wordt weer gegeven dat aangeeft dat de validatie van de sjabloon is mislukt, hebt u mogelijk een syntaxis probleem in uw sjabloon.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Deze fout kan eenvoudig worden gemaakt omdat sjabloon expressies complex kunnen zijn. De volgende naam toewijzing voor een opslag account heeft bijvoorbeeld één set haken, drie functies, drie sets haakjes, één set met enkele aanhalings tekens en één eigenschap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Als u de overeenkomende syntaxis niet opgeeft, produceert de sjabloon een waarde die afwijkt van uw voor nemen.

Wanneer u dit type fout ontvangt, controleert u de syntaxis van de expressie zorgvuldig. U kunt een JSON-editor gebruiken, zoals [Visual Studio](create-visual-studio-deployment-project.md) of [Visual Studio code](quickstart-create-templates-use-visual-studio-code.md), die u kan waarschuwen over syntaxis fouten.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Oplossing 2: onjuiste segment lengten

Er treedt een andere ongeldige sjabloon fout op wanneer de resource naam niet de juiste indeling heeft.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Een resource op hoofd niveau moet een kleiner segment hebben in de naam van het resource type. Elk segment wordt onderscheiden door een slash. In het volgende voor beeld heeft het type twee segmenten en de naam heeft één segment, dus is het een **geldige naam**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Het volgende voor beeld is echter **geen geldige naam** omdat het hetzelfde aantal segmenten bevat als het type.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Voor onderliggende resources hebben het type en de naam hetzelfde aantal segmenten. Dit aantal segmenten is zinvol omdat de volledige naam en het type voor het onderliggende item de naam en het type van het bovenliggende item bevatten. De volledige naam heeft dus nog steeds één kleiner segment dan het volledige type.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Het recht segmenten ophalen kan lastig zijn bij Resource Manager-typen die worden toegepast op alle resource providers. Voor het Toep assen van een resource vergrendeling op een website is bijvoorbeeld een type met vier segmenten vereist. De naam is daarom drie segmenten:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>Oplossing 3-para meter is niet geldig

Als u een parameter waarde voor een van de toegestane waarden opgeeft, wordt een bericht weer gegeven dat vergelijkbaar is met de volgende fout:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Controleer de toegestane waarden in de sjabloon en geef er een op tijdens de implementatie. Zie [para meters in het gedeelte met Azure Resource Manager sjablonen](template-syntax.md#parameters)voor meer informatie over toegestane parameter waarden.

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Oplossing 4: te veel doel resource groepen

Mogelijk ziet u deze fout in eerdere implementaties omdat u beperkt tot vijf doel resource groepen hebt in één implementatie. In mei 2020 is die limiet verhoogd naar 800 resource groepen. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](./deploy-to-resource-group.md)voor meer informatie.

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Oplossing 5-circulaire afhankelijkheid gedetecteerd

U ontvangt deze fout melding wanneer de resources van elkaar afhankelijk zijn op een manier die verhindert dat de implementatie wordt gestart. Een combi natie van onderlinge afhankelijkheden zorgt ervoor dat twee of meer bronnen wachten op andere resources die ook wachten. Resource1 is bijvoorbeeld afhankelijk van Resource3, Resource2 is afhankelijk van Resource1 en Resource3 is afhankelijk van Resource2. U kunt dit probleem doorgaans oplossen door overbodige afhankelijkheden te verwijderen.

Een circulaire afhankelijkheid oplossen:

1. Zoek in uw sjabloon de resource die in de circulaire afhankelijkheid is geïdentificeerd.
2. Bekijk voor die resource de eigenschap **dependsOn** en het gebruik van de functie **Reference** om te zien op welke resources deze is gebaseerd.
3. Bekijk deze bronnen om te zien van welke bronnen ze afhankelijk zijn. Volg de afhankelijkheden totdat u een bron ziet die afhankelijk is van de oorspronkelijke resource.
5. Voor de resources die deel uitmaken van de circulaire afhankelijkheid, onderzoekt zorgvuldig alle toepassingen van de eigenschap **dependsOn** om eventuele afhankelijkheden te identificeren die niet nodig zijn. Verwijder deze afhankelijkheden. Als u niet zeker weet of een afhankelijkheid nodig is, kunt u deze verwijderen.
6. Implementeer de sjabloon opnieuw.

Het verwijderen van waarden van de eigenschap **dependsOn** kan fouten veroorzaken tijdens het implementeren van de sjabloon. Als er een fout optreedt, voegt u de afhankelijkheid weer toe aan de sjabloon.

Als deze aanpak de circulaire afhankelijkheid niet oplost, kunt u overwegen om een deel van uw implementatie logica te verplaatsen naar onderliggende resources (zoals extensies of configuratie-instellingen). Configureer deze onderliggende resources om te implementeren na de resources die bij de circulaire afhankelijkheid betrokken zijn. Stel bijvoorbeeld dat u twee virtuele machines implementeert, maar u moet eigenschappen instellen voor elke machine die naar de andere verwijzen. U kunt deze in de volgende volg orde implementeren:

1. vm1
2. vm2
3. De uitbrei ding van VM1 is afhankelijk van VM1 en VM2. De uitbrei ding stelt waarden in voor VM1 die worden opgehaald uit VM2.
4. De uitbrei ding van VM2 is afhankelijk van VM1 en VM2. De uitbrei ding stelt waarden in voor VM2 die worden opgehaald uit VM1.

Dezelfde benadering werkt voor App Service-apps. Overweeg configuratie waarden te verplaatsen naar een onderliggende resource van de app-resource. U kunt in de volgende volg orde twee Web-Apps implementeren:

1. webapp1
2. webapp2
3. de configuratie voor webapp1 is afhankelijk van webapp1 en webapp2. Het bevat app-instellingen met waarden van webapp2.
4. de configuratie voor webapp2 is afhankelijk van webapp1 en webapp2. Het bevat app-instellingen met waarden van webapp1.