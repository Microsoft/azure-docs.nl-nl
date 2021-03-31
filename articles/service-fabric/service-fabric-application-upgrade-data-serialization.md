---
title: 'Toepassings upgrade: serialisatie van gegevens'
description: Aanbevolen procedures voor het serialiseren van gegevens en hoe dit van invloed is op upgrades van Rolling toepassingen.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 9474a0bf8041ac541389f782f60bf1220d690cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575632"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hoe gegevens serialisatie van toepassing is op een toepassings upgrade
Bij een [upgrade van een rolling toepassing](service-fabric-application-upgrade.md)wordt de upgrade toegepast op een subset knoop punten, één upgrade domein per keer. Tijdens dit proces bevinden sommige upgrade domeinen zich op de nieuwere versie van uw toepassing. sommige upgrade domeinen bevinden zich op de oudere versie van uw toepassing. Tijdens de implementatie moet de nieuwe versie van uw toepassing de oude versie van uw gegevens kunnen lezen, en de oude versie van uw toepassing moet de nieuwe versie van uw gegevens kunnen lezen. Als de gegevens indeling niet voorwaarts en achterwaarts compatibel is, kan de upgrade mislukken of kunnen de gegevens verloren gaan of beschadigd raken. In dit artikel wordt beschreven wat uw gegevens indeling vormt en worden aanbevolen procedures geboden om ervoor te zorgen dat uw gegevens voorwaarts en achterwaarts compatibel zijn.

## <a name="what-makes-up-your-data-format"></a>Wat is uw gegevens indeling?
In azure Service Fabric worden de gegevens die persistent zijn en gerepliceerd afkomstig van uw C#-klassen. Voor toepassingen die gebruikmaken van [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md), zijn deze gegevens de objecten in de betrouw bare woorden lijsten en wacht rijen. Voor toepassingen die gebruikmaken van [reliable actors](service-fabric-reliable-actors-introduction.md)is dit de status van de back-up voor de actor. Deze C#-klassen moeten serialiseerbaar zijn om persistent te kunnen worden gemaakt en gerepliceerd. Daarom wordt de gegevens indeling gedefinieerd door de velden en eigenschappen die geserialiseerd zijn, en hoe ze worden geserialiseerd. Een voor beeld: in een `IReliableDictionary<int, MyClass>` gegevens is een geserialiseerd `int` en een serialisatie `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Code wijzigingen die resulteren in een wijziging in de gegevens indeling
Omdat de gegevens indeling wordt bepaald door C#-klassen, kunnen wijzigingen aan de klassen een wijziging in de gegevens indeling veroorzaken. Zorg ervoor dat een rolling upgrade de wijziging van de gegevens indeling kan verwerken. Voor beelden die wijzigingen in de gegevens indeling kunnen veroorzaken:

* Velden of eigenschappen toevoegen of verwijderen
* Naam wijzigen van velden of eigenschappen
* Het type velden of eigenschappen wijzigen
* De klassenaam of naam ruimte wijzigen

### <a name="data-contract-as-the-default-serializer"></a>Gegevens contract als standaard-serialisatiefunctie
De serialisatiefunctie is over het algemeen verantwoordelijk voor het lezen van de gegevens en het deserialiseren ervan naar de huidige versie, zelfs als de gegevens zich in een oudere of *nieuwere* versie bevindt. De standaardserialisatiefunctie is de serialisatiefunctie voor het [gegevens contract](/dotnet/framework/wcf/feature-details/using-data-contracts), met goed gedefinieerde versies van regels. Met betrouw bare verzamelingen kan de serialisatiefunctie worden overschreven, maar Reliable Actors momenteel niet. De data serializer speelt een belang rijke rol bij het inschakelen van rolling upgrades. De serialisatiefunctie van het gegevens contract is de serialisatiefunctie die wordt aanbevolen voor Service Fabric toepassingen.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hoe de gegevens indeling van invloed is op een rolling upgrade
Tijdens een rolling upgrade zijn er twee belang rijke scenario's waarin de serialisatiefunctie een oudere of *nieuwere* versie van uw gegevens kan ondervinden:

1. Nadat een knoop punt is bijgewerkt en een back-up wordt gemaakt, worden de gegevens die door de oude versie zijn opgeslagen, door de nieuwe serialisatiefunctie geladen op schijf.
2. Tijdens de rolling upgrade bevat het cluster een combi natie van de oude en nieuwe versies van uw code. Omdat replica's in verschillende upgrade domeinen kunnen worden geplaatst en replica's gegevens naar elkaar verzenden, kunnen de nieuwe en/of oude versie van uw gegevens worden gevonden door de nieuwe en/of oude versie van de serialisatiefunctie.

> [!NOTE]
> De ' nieuwe versie ' en ' oude versie ' verwijzen hier naar de versie van de code die wordt uitgevoerd. De ' nieuwe serialisatiefunctie ' verwijst naar de serialisatiefunctie-code die wordt uitgevoerd in de nieuwe versie van uw toepassing. De ' nieuwe gegevens ' verwijst naar de geserialiseerde C#-klasse van de nieuwe versie van uw toepassing.
> 
> 

De twee versies van code en gegevens indeling moeten voorwaarts en achterwaarts compatibel zijn. Als ze niet compatibel zijn, kunnen de rolling upgrade mislukken of kunnen gegevens verloren gaan. De rolling upgrade kan mislukken omdat de code of de serialisatiefunctie uitzonde ringen of een fout kan veroorzaken wanneer de andere versie wordt aangetroffen. Gegevens kunnen verloren gaan als er bijvoorbeeld een nieuwe eigenschap is toegevoegd, maar de oude serialisatiefunctie deze negeert tijdens deserialisatie.

Data contract is de aanbevolen oplossing om ervoor te zorgen dat uw gegevens compatibel zijn. Er zijn goed gedefinieerde versie regels voor het toevoegen, verwijderen en wijzigen van velden. Het biedt ook ondersteuning voor het afhandelen van onbekende velden, het koppelen van het serialisatie proces en het afhandelen van klasse-overname. Zie [using data contract](/dotnet/framework/wcf/feature-details/using-data-contracts)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).
