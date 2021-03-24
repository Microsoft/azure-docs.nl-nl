---
title: Bicep-taal voor Azure Resource Manager sjablonen
description: Beschrijft de Bicep-taal voor het implementeren van de infra structuur naar Azure via Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955904"
---
# <a name="what-is-bicep-preview"></a>Wat is Bicep (preview)?

Bicep is een taal voor declaratieve implementatie van Azure-resources. U kunt Bicep gebruiken in plaats van JSON voor het ontwikkelen van uw Azure Resource Manager sjablonen (ARM-sjablonen). Bicep vereenvoudigt de ontwerp ervaring door een beknopte syntaxis te bieden, betere ondersteuning voor het hergebruik van code en verbeterde type beveiliging. Bicep is een domein-specifieke taal (DSL), wat betekent dat het is ontworpen voor een bepaald scenario of domein. Het is niet bedoeld als een algemene programmeer taal voor het schrijven van toepassingen.

De JSON-syntaxis voor het maken van een sjabloon kan uitgebreid zijn en gecompliceerde expressies vereisen. Bicep verbetert de ervaring zonder dat de mogelijkheden van een JSON-sjabloon verloren gaan. Het is een transparante abstractie voor de JSON voor ARM-sjablonen. Elk Bicep-bestand wordt gecompileerd naar een standaard ARM-sjabloon. Resource typen, API-versies en eigenschappen die geldig zijn in een ARM-sjabloon zijn geldig in een Bicep-bestand. Er zijn enkele [bekende beperkingen](#known-limitations) in de huidige release.

Bicep is momenteel beschikbaar als preview-versie. Zie de [Bicep-project opslagplaats](https://github.com/Azure/bicep)als u de status van het werk wilt bijhouden.

Zie de volgende video voor meer informatie over Bicep.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Aan de slag

Als u met Bicep wilt beginnen, [installeert u de hulpprogram ma's](bicep-install.md).

Probeer de [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md)na de installatie van de hulpprogram ma's. In de zelf studie wordt u begeleid bij de structuur en mogelijkheden van Bicep. U implementeert Bicep-bestanden en zet een ARM-sjabloon om in het equivalente Bicep-bestand.

Als u gelijkwaardige JSON-en Bicep-bestanden naast elkaar wilt weer geven, raadpleegt u de [Bicep-Playground](https://aka.ms/bicepdemo).

Als u een bestaande ARM-sjabloon hebt die u wilt converteren naar Bicep, raadpleegt u [arm-sjablonen converteren tussen JSON en Bicep](bicep-decompile.md).

## <a name="bicep-improvements"></a>Verbeteringen in Bicep

Bicep biedt een eenvoudiger en beknoptere syntaxis in vergelijking met de equivalente JSON. U gebruikt geen `[...]` expressies. In plaats daarvan roept u functies rechtstreeks aan en haalt u waarden op uit para meters en variabelen. U geeft elke geïmplementeerde resource een symbolische naam, waardoor het gemakkelijk is om naar die resource in uw sjabloon te verwijzen.

De volgende JSON retourneert bijvoorbeeld een uitvoer waarde van een bron eigenschap.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

De equivalente uitvoer expressie in Bicep is gemakkelijker te schrijven. In het volgende voor beeld wordt dezelfde eigenschap geretourneerd met behulp van de symbolische naam **publicIP** voor een resource die in de sjabloon is gedefinieerd:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Zie voor een volledige vergelijking van de syntaxis [JSON en Bicep vergelijken voor sjablonen](compare-template-syntax.md).

Bicep beheert automatisch afhankelijkheden tussen resources. U kunt instellen dat `dependsOn` de symbolische naam van een resource niet wordt gebruikt in een andere resource declaratie.

Met Bicep kunt u uw project in meerdere modules opdelen.

De structuur van het Bicep-bestand is flexibeler dan de JSON-sjabloon. U kunt de para meters, variabelen en uitvoer overal in het bestand declareren. In JSON moet u alle para meters, variabelen en uitvoer in de bijbehorende secties van de sjabloon declareren.

De VS code-extensie voor Bicep biedt uitgebreide validatie en IntelliSense. U kunt bijvoorbeeld IntelliSense gebruiken voor het ophalen van eigenschappen van een resource.

## <a name="known-limitations"></a>Bekende beperkingen

De volgende limieten bestaan momenteel:

* Kan de modus of Batch grootte niet instellen voor kopieer lussen.
* Kan geen lussen en voor waarden combi neren.
* Objecten en matrices met één regel, zoals `['a', 'b', 'c']` , worden niet ondersteund.

## <a name="faq"></a>Veelgestelde vragen

**Waarom een nieuwe taal maken in plaats van een bestaande te gebruiken?**

U kunt Bicep beschouwen als een revisie van de bestaande taal van de ARM-sjabloon in plaats van een nieuwe taal. De syntaxis is gewijzigd, maar de kern functionaliteit en runtime blijven hetzelfde.

Voordat u Bicep ontwikkelt, wordt gekeken naar een bestaande programmeer taal. We hebben besloten dat onze doel groep het eenvoudiger is om Bicep te leren, in plaats van aan de slag te gaan met een andere taal.

**Waarom richt u zich niet op uw energie op terraform of een andere infra structuur van derden als code aanbiedingen?**

Verschillende gebruikers hebben de voor keur aan verschillende configuratie talen en-hulpprogram ma's. We willen er zeker van zijn dat al deze hulpprogram ma's een fantastische ervaring bieden met Azure. Bicep maakt deel uit van die inspanning.

Als u tevreden bent met terraform, is er geen reden om over te scha kelen. Micro soft streeft ernaar om ervoor te zorgen dat terraform op Azure het beste is.

Voor klanten die een ARM-sjabloon hebben geselecteerd, geloven Bicep dat de ontwerp ervaring wordt verbeterd. Bicep helpt ook bij de overgang voor klanten die de infra structuur niet als code hebben aangenomen.

**Is alleen Bicep voor Azure?**

Bicep is een DSL gericht op het implementeren van complete oplossingen voor Azure. Voor een vergadering die doel stelling vereist, moet u werken met enkele Api's die zich buiten Azure bevinden. Er wordt verwacht dat u uitbreidings punten voor die scenario's kunt opgeven.

**Wat gebeurt er met mijn bestaande ARM-sjablonen?**

Ze blijven precies hetzelfde functioneren als ze altijd hebben. U hoeft geen wijzigingen door te voeren. De taal van de onderliggende ARM-sjabloon wordt nog steeds ondersteund. Bicep-bestanden worden gecompileerd naar JSON en die JSON wordt naar Azure verzonden voor implementatie.

Wanneer u klaar bent, kunt u [de json-bestanden converteren naar Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Volgende stappen

Aan de slag met de [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md).
