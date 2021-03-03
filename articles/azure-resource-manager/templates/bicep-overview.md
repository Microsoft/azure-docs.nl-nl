---
title: Bicep-taal voor Azure Resource Manager sjablonen
description: Beschrijft de Bicep-taal voor het implementeren van de infra structuur naar Azure via Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746074"
---
# <a name="what-is-bicep-preview"></a>Wat is Bicep (preview)?

Bicep is een taal voor declaratieve implementatie van Azure-resources. Het vereenvoudigt de ontwerp ervaring door een beknopte syntaxis te bieden, en biedt betere ondersteuning voor modulariteit en code hergebruik. Bicep is een domein-specifieke taal (DSL), wat betekent dat het is ontworpen voor een bepaald scenario of domein. Bicep is niet bedoeld als een algemene programmeer taal voor het schrijven van toepassingen.

Bicep is een transparante abstractie over Azure Resource Manager sjablonen (ARM-sjablonen). Elk Bicep-bestand wordt gecompileerd naar een standaard ARM-sjabloon. Resource typen, API-versies en eigenschappen die geldig zijn in een ARM-sjabloon zijn geldig in een Bicep-bestand.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Aan de slag

Als u met Bicep wilt beginnen, [installeert u de hulpprogram ma's](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Probeer de [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md)na de installatie van de hulpprogram ma's. In de zelf studie wordt u begeleid bij de structuur en mogelijkheden van Bicep. U implementeert Bicep-bestanden en zet een ARM-sjabloon om in het equivalente Bicep-bestand.

Als u gelijkwaardige JSON-en Bicep-bestanden naast elkaar wilt weer geven, raadpleegt u de [Bicep-Playground](https://aka.ms/bicepdemo).

Als u een bestaande ARM-sjabloon hebt die u wilt converteren naar Bicep, raadpleegt u [JSON decompileren op Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Verbeteringen in Bicep

Bicep biedt een eenvoudiger en beknoptere syntaxis in vergelijking met de equivalente JSON. U gebruikt geen `[...]` expressies. In plaats daarvan roept u functies rechtstreeks aan, haalt u waarden op uit para meters en variabelen en naslag bronnen. Zie voor een volledige vergelijking van de syntaxis [JSON en Bicep vergelijken voor sjablonen](compare-template-syntax.md).

Bicep beheert automatisch afhankelijkheden tussen resources. U kunt instellen dat `dependsOn` de symbolische naam van een resource niet wordt gebruikt in een andere resource declaratie.

Met Bicep kunt u uw project in meerdere modules opdelen.

De structuur van het Bicep-bestand is flexibeler dan de JSON-sjabloon. U kunt de para meters, variabelen en uitvoer overal in het bestand declareren. In JSON moet u alle para meters, variabelen en uitvoer in de bijbehorende secties van de sjabloon declareren.

De VS code-extensie voor Bicep biedt uitgebreide validatie en IntelliSense. De uitbrei ding heeft bijvoorbeeld IntelliSense voor het ophalen van eigenschappen van een resource.

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

Wanneer u klaar bent, kunt u [de json-bestanden converteren naar Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Volgende stappen

Aan de slag met de [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md).
