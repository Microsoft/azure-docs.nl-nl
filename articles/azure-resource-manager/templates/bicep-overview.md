---
title: Bicep-taal voor Azure Resource Manager sjablonen
description: Beschrijft de Bicep-taal voor het implementeren van infrastructuur in Azure via Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773743"
---
# <a name="what-is-bicep-preview"></a>Wat is Bicep (preview)?

Bicep is een taal voor het declaratief implementeren van Azure-resources. U kunt Bicep gebruiken in plaats van JSON voor het ontwikkelen van uw Azure Resource Manager-sjablonen (ARM-sjablonen). Bicep vereenvoudigt de ontwerpervaring door beknopte syntaxis te bieden, betere ondersteuning voor hergebruik van code en verbeterde veiligheid van het type. Bicep is een domeinspecifieke taal (DSL), wat betekent dat deze is ontworpen voor een bepaald scenario of domein. Het is niet bedoeld als een algemene programmeertaal voor het schrijven van toepassingen.

De JSON-syntaxis voor het maken van een sjabloon kan uitgebreid zijn en een gecompliceerde expressie vereisen. Bicep verbetert die ervaring zonder verlies van de mogelijkheden van een JSON-sjabloon. Het is een transparante abstractie over de JSON voor ARM-sjablonen. Elk Bicep-bestand wordt ge compileerd naar een standaard ARM-sjabloon. Resourcetypen, API-versies en eigenschappen die geldig zijn in een ARM-sjabloon zijn geldig in een Bicep-bestand. Er zijn enkele [bekende beperkingen](#known-limitations) in de huidige versie.

Bicep is momenteel beschikbaar als preview-versie. Zie de [Bicep-projectopslagplaats](https://github.com/Azure/bicep)om de status van het werk bij te houden.

Zie de volgende video voor meer informatie over Bicep.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Aan de slag

Installeer de hulpprogramma's om te [beginnen met](bicep-install.md)Bicep.

Nadat u de hulpprogramma's hebt geïnstalleerd, kunt u de [Bicep-zelfstudie proberen.](./bicep-tutorial-create-first-bicep.md) De reeks zelfstudies laat u zien wat de structuur en mogelijkheden van Bicep zijn. U implementeert Bicep-bestanden en converteert een ARM-sjabloon naar het equivalente Bicep-bestand.

Zie Bicep Playground als u equivalente JSON- en Bicep-bestanden naast elkaar [wilt bekijken.](https://aka.ms/bicepdemo)

Zie ARM-sjablonen converteren tussen JSON en Bicep als u een bestaande ARM-sjabloon hebt die u wilt converteren [naar Bicep.](bicep-decompile.md)

## <a name="benefits-of-bicep-versus-other-tools"></a>Voordelen van Bicep versus andere hulpprogramma's

Bicep biedt de volgende voordelen ten opzichte van andere opties:

* **Ondersteuning voor alle resourcetypen en API-versies:** Bicep ondersteunt onmiddellijk alle preview- en GA-versies voor Azure-services. Zodra een resourceprovider nieuwe resourcetypen en API-versies introduceert, kunt u deze gebruiken in uw Bicep-bestand. U hoeft niet te wachten tot de hulpprogramma's zijn bijgewerkt voordat u de nieuwe services gebruikt.
* **Ontwerpervaring:** wanneer u VS Code gebruikt om bicep-bestanden te maken, krijgt u een eersteklas ontwerpervaring. De editor biedt uitgebreide type-safety, intellisense en syntaxisvalidatie.
* **Modulariteit:** u kunt uw Bicep-code opdelen in beheerbare onderdelen met behulp van [modules](bicep-modules.md). Met de module wordt een set gerelateerde resources geïmplementeerd. Met modules kunt u code hergebruiken en de ontwikkeling vereenvoudigen. Voeg de module toe aan een Bicep-bestand wanneer u deze resources wilt implementeren.
* **Integratie met Azure-services:** Bicep is geïntegreerd met Azure-services zoals Azure Policy, sjabloonspecificaties en Blauwdrukken.
* **Geen status- of statusbestanden om te beheren:** De status Alle wordt opgeslagen in Azure. Gebruikers kunnen samenwerken en vertrouwen dat hun updates worden verwerkt zoals verwacht. Gebruik de [what-if-bewerking om](template-deploy-what-if.md) een voorbeeld van wijzigingen te bekijken voordat u de sjabloon implementeert.
* **Geen kosten en open source:** Bicep is volledig gratis. U hoeft niet te betalen voor Premium-mogelijkheden. Het wordt ook ondersteund door Microsoft Ondersteuning.

## <a name="bicep-improvements"></a>Bicep-verbeteringen

Bicep biedt een eenvoudigere en beknoptere syntaxis in vergelijking met de equivalente JSON. U gebruikt geen `[...]` expressies. In plaats daarvan roept u functies rechtstreeks aan en krijgt u waarden uit parameters en variabelen. U geeft elke geïmplementeerde resource een symbolische naam, zodat u eenvoudig naar die resource in uw sjabloon kunt verwijzen.

De volgende JSON retourneert bijvoorbeeld een uitvoerwaarde van een resource-eigenschap.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

De equivalente uitvoerexpressie in Bicep is eenvoudiger te schrijven. Het volgende voorbeeld retourneert dezelfde eigenschap met behulp van de symbolische naam **publicIP** voor een resource die is gedefinieerd in de sjabloon:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Zie JSON en Bicep vergelijken voor sjablonen voor een volledige vergelijking [van de syntaxis.](compare-template-syntax.md)

Bicep beheert automatisch afhankelijkheden tussen resources. U kunt instelling vermijden wanneer `dependsOn` de symbolische naam van een resource wordt gebruikt in een andere resourcedeclaratie.

De structuur van het Bicep-bestand is flexibeler dan de JSON-sjabloon. U kunt parameters, variabelen en uitvoer overal in het bestand declareeren. In JSON moet u alle parameters, variabelen en uitvoer declareeren in de bijbehorende secties van de sjabloon.

## <a name="known-limitations"></a>Bekende beperkingen

De volgende limieten bestaan momenteel:

* Kan de modus of batchgrootte niet instellen voor kopieerlussen.
* Kan lussen en voorwaarden niet combineren.
* Object en matrices met één regel, zoals `['a', 'b', 'c']` , worden niet ondersteund.

## <a name="faq"></a>Veelgestelde vragen

**Waarom een nieuwe taal maken in plaats van een bestaande taal gebruiken?**

U kunt Bicep zien als een revisie van de bestaande arm-sjabloontaal in plaats van een nieuwe taal. De syntaxis is gewijzigd, maar de kernfunctionaliteit en runtime blijven hetzelfde.

Voordat we Bicep ontwikkelden, hebben we overwogen een bestaande programmeertaal te gebruiken. We hebben besloten dat onze doelgroep het gemakkelijker zou vinden om Bicep te leren in plaats van aan de slag te gaan met een andere taal.

**Waarom richt u uw energie niet op Terraform of andere infrastructure as code-aanbiedingen van derden?**

Verschillende gebruikers geven de voorkeur aan verschillende configuratietalen en hulpprogramma's. We willen er zeker van zijn dat al deze hulpprogramma's een geweldige ervaring bieden in Azure. Bicep maakt deel uit van die inspanning.

Als u terraform wilt gebruiken, is er geen reden om over te schakelen. Microsoft doet er alles aan om ervoor te zorgen dat Terraform in Azure het beste is.

Voor klanten die ARM-sjablonen hebben geselecteerd, zijn we van mening dat Bicep de ontwerpervaring verbetert. Bicep helpt ook bij de overgang voor klanten die geen infrastructuur als code hebben gebruikt.

**Is Bicep alleen voor Azure?**

Bicep is een DSL die is gericht op het implementeren van volledige oplossingen in Azure. Om dit doel te bereiken, moet u werken met een aantal API's die zich buiten Azure verplaatsen. We verwachten dat we voor deze scenario's extensibility points bieden.

**Wat gebeurt er met mijn bestaande ARM-sjablonen?**

Ze blijven precies werken zoals ze altijd hebben. U hoeft geen wijzigingen aan te brengen. We blijven de onderliggende JSON-taal van de ARM-sjabloon ondersteunen. Bicep-bestanden worden ge compileerd naar JSON en die JSON wordt naar Azure verzonden voor implementatie.

Wanneer u klaar bent, kunt u de [JSON-bestanden converteren naar Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [Bicep-zelfstudie](./bicep-tutorial-create-first-bicep.md).
