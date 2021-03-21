---
title: JSON en XML converteren met vloeistof sjablonen
description: JSON en XML transformeren met behulp van liquide sjablonen als Maps in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992706"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>JSON en XML transformeren met behulp van liquide sjablonen als Maps in Azure Logic Apps

Als u basis-JSON-trans formaties wilt uitvoeren in uw Logic apps, kunt u systeem eigen [gegevens bewerkingen](../logic-apps/logic-apps-perform-data-operations.md) gebruiken, zoals het **opstellen** of **parseren van JSON**. Maak en gebruik sjablonen waarmee deze trans formaties worden beschreven met behulp van de [Geliquide](https://shopify.github.io/liquid/) open-source sjabloon taal voor geavanceerde en complexe JSON-trans formaties met elementen zoals herhalingen, controle stromen en variabelen. U kunt ook [andere trans formaties uitvoeren](#other-transformations), bijvoorbeeld JSON naar Text, XML naar JSON en XML naar tekst.

Voordat u een liquide trans formatie in uw logische app kunt uitvoeren, moet u eerst een vloeistof sjabloon maken die de gewenste toewijzing definieert. Vervolgens [uploadt u de sjabloon als een kaart](../logic-apps/logic-apps-enterprise-integration-maps.md) in uw [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Wanneer u de **JSON transformeren naar een JSON-liquide** actie toevoegt aan uw logische app, kunt u vervolgens de vloeistof sjabloon selecteren als de kaart voor de actie die moet worden gebruikt.

In dit artikel wordt beschreven hoe u deze taken uitvoert:

* Maak een liquide sjabloon.
* Voeg de sjabloon toe aan uw integratie account.
* Voeg de actie liquide trans formatie toe aan uw logische app.
* Selecteer de sjabloon als de kaart die u wilt gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Basis kennis over [taal van liquide sjablonen](https://shopify.github.io/liquid/)

  > [!NOTE]
  > De actie **JSON transformeren naar JSON-vloeistof** volgt de [DotLiquid-implementatie voor liquide middelen](https://github.com/dotliquid/dotliquid), die in specifieke gevallen afwijkt van de [Shopify-implementatie voor vloei stof](https://shopify.github.io/liquid). Zie [overwegingen voor liquide sjablonen](#liquid-template-considerations)voor meer informatie.

## <a name="create-the-template"></a>De sjabloon maken

1. Maak de vloeistof sjabloon die u gebruikt als toewijzing voor de JSON-trans formatie. U kunt elk gewenst bewerkings programma gebruiken.

   Voor dit voor beeld maakt u de voorbeeld sjabloon vloeistof, zoals wordt beschreven in deze sectie:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Sla de sjabloon op met behulp van de `.liquid` extensie. In dit voorbeeld wordt `SimpleJsonToJsonTemplate.liquid` gebruikt.

## <a name="upload-the-template"></a>De sjabloon uploaden

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Typ in het zoekvak Azure Portal `integration accounts` en selecteer **integratie accounts**.

   !["Integratie accounts" zoeken](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Zoek en selecteer uw integratie account.

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Selecteer in het deel venster **overzicht** onder **onderdelen** de optie **kaarten**.

    ![Tegel ' Maps ' selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Selecteer **toevoegen** in het deel venster **kaarten** en geef deze details op voor uw kaart:

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | `JsonToJsonTemplate` | De naam voor uw kaart, in dit voor beeld ' JsonToJsonTemplate ' |
   | **Toewijzings type** | **Liquid** | Het type voor uw kaart. Voor JSON naar JSON-trans formatie moet u **liquide middelen** selecteren. |
   | **Kaart** | `SimpleJsonToJsonTemplate.liquid` | In dit voor beeld wordt een bestaande vloeistof sjabloon of kaart bestand gebruikt voor trans formatie, dat "SimpleJsonToJsonTemplate. liquide" is. U kunt dit bestand vinden met behulp van de bestands kiezer. Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)voor limieten voor kaart grootte. |
   |||

   ![Vloeistof sjabloon toevoegen](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>De actie liquide trans formatie toevoegen

1. Voer in de Azure Portal de volgende stappen uit om [een lege logische app te maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Voeg in de ontwerp functie voor logische apps de [aanvraag trigger](../connectors/connectors-native-reqres.md#add-request) toe aan uw logische app.

1. Kies **nieuwe stap** onder de trigger. Typ als filter in het zoekvak `liquid` en selecteer deze actie: **JSON TRANSFORMEREN naar JSON-liquide**

   ![Een liquide actie zoeken en selecteren](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Open de **kaart** lijst en selecteer uw vloeistof sjabloon in dit voor beeld ' JsonToJsonTemplate '.

   ![Kaart selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Als de lijst met kaarten leeg is, is de logische app waarschijnlijk niet gekoppeld aan uw integratie account. 
   Voer de volgende stappen uit om uw logische app te koppelen aan het integratie account met de vloeistof sjabloon of kaart:

   1. Selecteer **werk stroom instellingen** in het menu van de logische app.

   1. Selecteer in de lijst **een integratie account selecteren** het integratie account en selecteer **Opslaan**.

      ![Logische app koppelen aan integratie account](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Voeg nu de eigenschap **Content** toe aan deze actie. Open de lijst **nieuwe para meter toevoegen** en selecteer **inhoud**.

   ![Eigenschap content toevoegen aan actie](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Als u de waarde van de eigenschap **Content** wilt instellen, klikt u in het vak **inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer het token **Body** , dat de inhoud van de hoofd tekst van de trigger vertegenwoordigt.

   ![Selecteer hoofd token voor eigenschaps waarde ' content '](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![De actie JSON naar JSON transformeren is voltooid](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Uw logische app testen

U kunt met behulp van [postman](https://www.getpostman.com/postman) of een soortgelijk hulp programma JSON-invoer plaatsen op de logische app. De getransformeerde JSON-uitvoer van uw logische app ziet eruit als in dit voor beeld:

![Voorbeelduitvoer](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Overwegingen voor liquide sjablonen

* Liquide sjablonen volgen de [maximale bestands grootte voor kaarten](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) in azure Logic apps.

* De actie **JSON transformeren naar JSON-vloeistof** volgt de [DotLiquid-implementatie voor liquide middelen](https://github.com/dotliquid/dotliquid). Deze implementatie is een poort voor de .NET Framework van de [Shopify-implementatie voor vloei stof](https://shopify.github.io/liquid/) en verschilt in [specifieke gevallen](https://github.com/dotliquid/dotliquid/issues).

  Dit zijn de bekende verschillen:

  * Met de actie **JSON transformeren naar JSON-vloeistof** wordt standaard een teken reeks uitgevoerd, die JSON, XML, HTML kan bevatten. De actie liquide alleen geeft aan dat de verwachte tekst uitvoer van de vloeistof sjabloon een JSON-teken reeks is. De actie geeft uw logische app de opdracht om invoer te parseren als een JSON-object en past een wrapper toe zodat Liquid de JSON-structuur kan interpreteren. Na de trans formatie krijgt de actie uw logische app om de tekst uitvoer van liquide terug te parseren naar JSON.

    DotLiquid heeft geen standaard inzicht in JSON. Zorg er dus voor dat u de back slash-teken ( `\` ) en andere gereserveerde JSON-tekens weglaat.

  * Als uw sjabloon gebruikmaakt van [liquide filters](https://shopify.github.io/liquid/basics/introduction/#filters), moet u ervoor zorgen dat u de [DotLiquid-en C#-naamgevings conventies](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)volgt die gebruikmaken van een *zin*. Voor alle liquide trans formaties moet u ervoor zorgen dat filter namen in uw sjabloon ook wel zin hoofdletter gebruik gebruiken. Anders werken de filters niet.

    Als u bijvoorbeeld het filter gebruikt, `replace` gebruiken `Replace` en niet `replace` . Dezelfde regel is van toepassing als u voor beelden op [DotLiquid online](http://dotliquidmarkup.org/try-online)probeert. Zie [Shopify Liquid filters](https://shopify.dev/docs/themes/liquid/reference/filters) en [DotLiquid Liquid filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)(Engelstalig) voor meer informatie. De Shopify-specificatie bevat voor beelden voor elk filter, zodat u deze voor beelden kunt uitproberen op [DotLiquid-online proberen](http://dotliquidmarkup.org/try-online).

  * Het `json` filter van de Shopify-extensie filters is momenteel [niet geïmplementeerd in DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Normaal gesp roken kunt u dit filter gebruiken om tekst uitvoer voor te bereiden voor het parseren van JSON-teken reeksen, maar in plaats daarvan moet u het `Replace` filter gebruiken.

  * Het standaard `Replace` filter in de [DotLiquid-implementatie](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) gebruikt [reguliere expressie (regex) matching](/dotnet/standard/base-types/regular-expression-language-quick-reference), terwijl de [Shopify-implementatie](https://shopify.github.io/liquid/filters/replace/) [eenvoudige teken reeks matching](https://github.com/Shopify/liquid/issues/202)gebruikt. Beide implementaties lijken op dezelfde manier te werken totdat u een door RegEx gereserveerd teken of escape-teken gebruikt in de match-para meter.

    Als u bijvoorbeeld het escape-teken voor RegEx-reserved back slash ( `\` ) wilt gebruiken, gebruikt u `| Replace: '\\', '\\'` en niet `| Replace: '\', '\\'` . In deze voor beelden ziet `Replace` u hoe het filter anders werkt wanneer u het back slash-teken wilt escapepen. Terwijl deze versie goed werkt:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Met dit resultaat:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Deze versie mislukt:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Met deze fout:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Zie voor meer informatie [vervangen standaard filter gebruikt regex-patroon vergelijking...](https://github.com/dotliquid/dotliquid/issues/385).

  * `Sort`Met het filter in de [DotLiquid-implementatie](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) worden items in een matrix of verzameling gesorteerd op eigenschap, maar met de volgende verschillen:<p>

    * Volgt [het sort_natural gedrag van Shopify](https://shopify.github.io/liquid/filters/sort_natural/), niet [het sorteer gedrag van Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Sorteert alleen in een teken reeks alfanumerieke volg orde. Zie voor meer informatie [numeriek sorteren](https://github.com/Shopify/liquid/issues/980).

    * Maakt gebruik van niet *-hoofdletter gevoelige* volg orde, niet hoofdletter gevoelig. Zie [Sorteer filter voldoet niet aan het gedrag van de behuizing van de specificatie van Shopify]( https://github.com/dotliquid/dotliquid/issues/393)voor meer informatie.

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Andere trans formaties met vloei stof

Liquid is niet beperkt tot JSON-trans formaties. U kunt ook liquide middelen gebruiken om andere trans formaties uit te voeren, bijvoorbeeld:

* [JSON naar tekst](#json-text)
* [XML naar JSON](#xml-json)
* [XML naar tekst](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON transformeren naar tekst

Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Hier volgen de voorbeeld invoer en uitvoer:

![Voorbeeld uitvoer van JSON naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML transformeren naar JSON

Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

De `JSONArrayFor` lus is een aangepast mechanisme voor het herhalen van XML-invoer, zodat u JSON-payloads kunt maken waarmee een afsluitende komma wordt voor komen. De `where` voor waarde voor dit aangepaste herhalings mechanisme maakt ook gebruik van de XML-element naam voor vergelijking, in plaats van de waarde van het element zoals andere liquide filters. Zie voor meer informatie [uitgebreide stappen voor het instellen van hoofd code beleid-verzamelingen van dingen](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Hier volgen de voorbeeld invoer en uitvoer:

![Voor beeld van uitvoer-XML naar JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML naar tekst transformeren

Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Hier volgen de voorbeeld invoer en uitvoer:

![Voor beeld van uitvoer-XML naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Volgende stappen

* [Shopify liquide taal en voor beelden](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-online proberen](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [Problemen met DotLiquid GitHub](https://github.com/dotliquid/dotliquid/issues/)
* Meer informatie over [Maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
