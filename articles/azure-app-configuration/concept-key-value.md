---
title: Informatie over Azure-app configuratie sleutel-waarde Store
description: Informatie over sleutel waarde-opslag in Azure-app configuratie, waarin configuratie gegevens worden opgeslagen als sleutel waarden. Sleutel waarden zijn een representatie van toepassings instellingen.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930242"
---
# <a name="keys-and-values"></a>Sleutels en waarden

Azure-app-configuratie worden configuratie gegevens opgeslagen als sleutel waarden. Sleutel waarden zijn een eenvoudige en flexibele representatie van toepassings instellingen die door ontwikkel aars worden gebruikt.

## <a name="keys"></a>Sleutels

Sleutels fungeren als id's voor sleutel waarden en worden gebruikt voor het opslaan en ophalen van de bijbehorende waarden. Het is een veelvoorkomende procedure om sleutels te organiseren in een hiërarchische naam ruimte met behulp van een teken scheidings tekens, zoals `/` of `:` . Gebruik een Conventie die het meest geschikt is voor uw toepassing. Voor App Configuration zijn sleutels een op zichzelf staand object. Sleutels worden niet geparseerd om erachter te komen hoe hun namen worden gestructureerd of dat er regels worden afgedwongen.

Hier volgt een voor beeld van sleutel namen die zijn gestructureerd in een hiërarchie op basis van Component Services:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

Het gebruik van configuratie gegevens in Application Frameworks kan specifieke naamgevings schema's voor sleutel waarden dicteren. In het voor beeld van de lente van een Java-Framework definieert `Environment` u bijvoorbeeld bronnen voor het leveren van instellingen aan een lente-toepassing.  Deze zijn para meters van variabelen die *toepassings naam* en- *profiel* bevatten. Sleutels voor lente-Cloud configuratie gegevens beginnen meestal met deze twee elementen gescheiden door een scheidings teken.

Sleutels die zijn opgeslagen in App Configuration zijn hoofdlettergevoelige tekenreeksen, op basis van Unicode. De sleutels *app1* en *app1* zijn uniek in een app-configuratie opslag. Houd dit in gedachten wanneer u configuratie-instellingen in een toepassing gebruikt omdat sommige Frameworks configuratie sleutels hoofdletter gevoelig verwerken. Het is niet raadzaam om cases te gebruiken om sleutels te onderscheiden.

U kunt elk Unicode-teken in sleutel namen gebruiken, behalve voor `%` . Een sleutel naam kan niet `.` of een van `..` beide zijn. Er is een gecombineerde grootte limiet van 10 KB voor een sleutel waarde. Deze limiet omvat alle tekens in de sleutel, de bijbehorende waarde en alle bijbehorende optionele kenmerken. Binnen deze limiet kunt u veel niveaus voor sleutels gebruiken.

### <a name="design-key-namespaces"></a>Naam ruimten van ontwerp sleutel

Er zijn twee algemene methoden voor naamgeving van sleutels die worden gebruikt voor configuratiegegevens: plat en hiërarchisch. Deze methoden zijn vergelijkbaar met het standpunt van een toepassings gebruik, maar hiërarchische naamgeving biedt een aantal voor delen:

* Eenvoudiger te lezen. Scheidings tekens in een naam van een hiërarchische sleutel functioneren als spaties in een zin. Ze bieden ook natuurlijke onderbrekingen tussen woorden.
* Eenvoudiger te beheren. Een hiërarchie voor sleutelnamen vertegenwoordigt logische groepen van configuratiegegevens.
* Eenvoudiger te gebruiken. Het is eenvoudiger om een query te schrijven die het patroon volgt van sleutels in een hiërarchische structuur, om zo slechts een gedeelte van de configuratiegegevens op te halen. Daarnaast hebben veel nieuwere programmeer raamwerken systeem eigen ondersteuning voor hiërarchische configuratie gegevens, zodat uw toepassing gebruik kan maken van specifieke configuratie sets.

U kunt sleutels in App Configuration op allerlei manieren hiërarchisch indelen. U beschouwt dergelijke sleutels als [uri's](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Elke hiërarchische sleutel is een *bronpad* dat bestaat uit een of meer onderdelen die samen worden samengevoegd door scheidings tekens. Kies welk teken moet worden gebruikt als een scheidings punt op basis van de behoeften van uw toepassing, programmeer taal of Framework. Gebruik meerdere scheidings tekens voor verschillende sleutels in de app-configuratie.

### <a name="label-keys"></a>Label sleutels

Sleutelwaarden kunnen in App Configuration optioneel een kenmerk label hebben. Labels worden gebruikt om onderscheid te maken tussen sleutelwaarden met dezelfde sleutel. Een Key *app1* met labels *A* en *B* vormt twee afzonderlijke sleutels in een app-configuratie opslag. Standaard heeft een sleutel waarde geen label. Als u expliciet wilt verwijzen naar een sleutel waarde zonder label, gebruikt u `\0` (URL gecodeerd als `%00` ).

Label biedt een handige manier om varianten van een sleutel te maken. Het gebruik van labels is gebruikelijk om meerdere omgevingen op te geven voor dezelfde sleutel:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Versie sleutel waarden

Gebruik labels als een manier om meerdere versies van een sleutel waarde te maken. Zo kunt u bijvoorbeeld een versienummer van de toepassing of een id van de Git-commit als label toevoegen om sleutelwaarden te identificeren die zijn gekoppeld aan een bepaalde software-build.

> [!NOTE]
> Als u wijzigingen doorvoert, worden de wijzigingen van een sleutel waarde in de afgelopen periode automatisch door de app-configuratie bewaard. Zie [punt-in-time-moment opname](./concept-point-time-snapshot.md) voor meer informatie.

### <a name="query-key-values"></a>Query's uitvoeren op sleutel waarden

Elke sleutelwaarde is uniek te identificeren met de sleutel en een label, welke ook `\0` kan zijn. U kunt een app-configuratie Archief voor sleutel waarden opvragen door een patroon op te geven. In het app-configuratie archief worden alle sleutel waarden geretourneerd die overeenkomen met het patroon met inbegrip van de bijbehorende waarden en kenmerken. Gebruik de volgende sleutel patronen in REST API-aanroepen naar app-configuratie:

| Sleutel | Beschrijving |
|---|---|
| `key` wordt weggelaten of `key=*` | Komt overeen met alle sleutels |
| `key=abc` | Komt exact overeen met sleutel naam **ABC** |
| `key=abc*` | Komt overeen met sleutelnamen die beginnen met **abc** |
| `key=abc,xyz` | Komt overeen met de sleutel namen **ABC** of **xyz**. Beperkt tot vijf Csv's |

U kunt ook de volgende label patronen toevoegen:

| Label | Description |
|---|---|
| `label` wordt weggelaten of `label=*` | Komt overeen met een label, inclusief `\0` |
| `label=%00` | Komt overeen met `\0` label |
| `label=1.0.0` | Komt exact overeen met label **1.0.0** |
| `label=1.0.*` | Komt overeen met labels die beginnen met **1.0.** |
| `label=%00,1.0.0` | Komt overeen met labels `\0` of **1.0.0**, beperkt tot vijf csv's |

> [!NOTE]
> `*`, `,` en `\` zijn gereserveerde tekens in query's. Als een gereserveerd teken wordt gebruikt in uw sleutel namen of labels, moet u dit met behulp `\{Reserved Character}` van in query's omescapeen.

## <a name="values"></a>Waarden

Waarden die zijn toegewezen aan sleutels zijn ook Unicode-tekenreeksen. U kunt alle Unicode-tekens gebruiken voor waarden.

### <a name="use-content-type"></a>Inhouds type gebruiken
Elke sleutel waarde in de app-configuratie heeft een inhouds type-kenmerk. U kunt dit kenmerk eventueel gebruiken om informatie op te slaan over het type waarde in een sleutel waarde die ervoor zorgt dat uw toepassing het goed kan verwerken. U kunt elke indeling gebruiken voor het inhouds type. App-configuratie maakt gebruik van [Media typen]( https://www.iana.org/assignments/media-types/media-types.xhtml) (ook wel MIME-typen genoemd) voor ingebouwde gegevens typen, zoals functie vlaggen, Key Vault verwijzingen en JSON-sleutel waarden.

## <a name="next-steps"></a>Volgende stappen

* [Point-in-time-snapshot](./concept-point-time-snapshot.md)
* [Functiebeheer](./concept-feature-management.md)
* [Gebeurtenisverwerking](./concept-app-configuration-event.md)
