---
title: Azure Notification Hubs-sjablonen
description: Meer informatie over het gebruik van sjablonen voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635639"
---
# <a name="notification-hubs-templates"></a>Notification Hubs sjablonen

Met sjablonen kunnen een client toepassing de exacte indeling opgeven van de meldingen die ze willen ontvangen. Met behulp van sjablonen kunnen apps verschillende voor delen realiseren, waaronder de volgende:

- Een neutraal-back-end van een platform
- Gepersonaliseerde meldingen
- Client-versie onafhankelijkheid
- Eenvoudig te lokaliseren

In deze sectie vindt u twee diep gaande voor beelden van het gebruik van sjablonen voor het verzenden van neutraal meldingen die gericht zijn op alle apparaten op verschillende platforms en voor het personaliseren van broadcast meldingen op elk apparaat.

## <a name="using-templates-cross-platform"></a>Sjablonen meerdere platformen gebruiken

De standaard methode voor het verzenden van push meldingen is het verzenden van elke melding die moet worden verzonden, een specifieke Payload naar platform Notification Services (WNS, APNS). Als u bijvoorbeeld een waarschuwing naar APNS wilt verzenden, is de payload een JSON-object van de volgende vorm:

```json
{"aps": {"alert" : "Hello!" }}
```

Als u een soortgelijk pop-upbericht wilt verzenden naar een Windows Store-toepassing, is de XML-nettolading als volgt:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

U kunt vergelijk bare nettoladingen maken voor MPNS-en FCM-platforms (Windows Phone) en (Android).

Deze vereiste zorgt ervoor dat de back-end van de app verschillende nettoladingen voor elk platform produceert en de back-end effectief maakt voor een deel van de presentatielaag van de app. Enkele problemen zijn onder andere lokalisatie en grafische indelingen (met name voor Windows Store-apps die meldingen bevatten voor diverse typen tegels).

Met de functie Notification Hubs sjabloon kan een client-app speciale registraties maken, die sjabloon registraties worden genoemd, naast de set met tags, een sjabloon. Met de functie Notification Hubs sjabloon kan een client-app apparaten koppelen aan sjablonen, ongeacht of u met installaties (voor keur) of registraties werkt. Gezien de voor beelden van voor gaande Payload, is de enige platform onafhankelijke informatie het daad werkelijke waarschuwings bericht (**Hallo!**). Een sjabloon is een set instructies voor de notification hub voor het format teren van een platform onafhankelijk bericht voor de registratie van die specifieke client-app. In het vorige voor beeld is het platform onafhankelijke bericht één eigenschap: `message = Hello!` .

De volgende afbeelding illustreert het proces:

![Diagram van het proces voor het gebruik van sjablonen voor meerdere platformen](./media/notification-hubs-templates/notification-hubs-hello.png)

De sjabloon voor de registratie van de iOS-client-app is als volgt:

```json
{"aps": {"alert": "$(message)"}}
```

De bijbehorende sjabloon voor de Windows Store-client-app is:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

U ziet dat het daad werkelijke bericht wordt vervangen door de expressie `$(message)` . Met deze expressie wordt de notification hub geïnstrueerd wanneer een bericht wordt verzonden naar deze specifieke registratie, om een bericht te maken dat erop volgt en de algemene waarde invoegt.

Als u werkt met het installatie model, bevat de installatie "Sjablonen"-sleutel een JSON van meerdere sjablonen. Als u werkt met het registratie model, kan de client toepassing meerdere registraties maken om meerdere sjablonen te gebruiken. bijvoorbeeld een sjabloon voor waarschuwings berichten en een sjabloon voor het bijwerken van tegels. Client toepassingen kunnen ook systeem eigen registraties (registraties zonder sjabloon) en sjabloon registraties combi neren.

De notification hub verzendt één melding voor elke sjabloon zonder rekening te houden met de client-app. Dit gedrag kan worden gebruikt om platform onafhankelijke meldingen om te zetten in meer meldingen. Zo kan hetzelfde platform onafhankelijke bericht naar de notification hub naadloos worden vertaald in een pop-upwaarschuwing en een tegel update, zonder dat de back-end hiervan op de hoogte hoeft te zijn. Sommige platformen (bijvoorbeeld iOS) kunnen meerdere meldingen samen vouwen op hetzelfde apparaat als ze binnen korte tijd worden verzonden.

## <a name="using-templates-for-personalization"></a>Sjablonen gebruiken voor personalisatie

Een ander voor deel van het gebruik van sjablonen is de mogelijkheid om Notification Hubs te gebruiken voor het uitvoeren van persoonlijke gegevens per registratie. Denk bijvoorbeeld aan een weers-app waarin een tegel met de weers omstandigheden op een specifieke locatie wordt weer gegeven. Een gebruiker kan kiezen uit Celsius of Fahrenheit graden en een enkele of vijf dagen prognose. Met behulp van sjablonen kan elke installatie van de client-app worden geregistreerd voor de vereiste indeling (1-dag Celsius, 1-dag Fahrenheit, 5-dagen Celsius, 5-dagen Fahrenheit) en moet de back-end één bericht bevatten met alle informatie die nodig is voor het invullen van deze sjablonen (bijvoorbeeld een prognose van vijf dagen met Celsius en Fahrenheit graden).

De sjabloon voor de 1-daagse prognose met Celsius temperaturen is als volgt:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Het bericht dat naar de notification hub wordt verzonden, bevat alle volgende eigenschappen:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Met dit patroon verzendt de back-end alleen één bericht zonder specifieke persoonlijke opties op te slaan voor de gebruikers van de app. In de volgende afbeelding ziet u een voor beeld van dit scenario:

![Diagram waarin wordt getoond hoe de back-end slechts één bericht naar elk platform verzendt.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Sjablonen registreren

Als u zich wilt registreren bij sjablonen met behulp van het installatie model (voor keur) of het registratie model, raadpleegt u [registratie beheer](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Taal van sjabloon expressie

Sjablonen zijn beperkt tot XML-of JSON-document indelingen. U kunt ook expressies op specifieke plaatsen plaatsen. bijvoorbeeld knooppunt kenmerken of-waarden voor XML, String-eigenschaps waarden voor JSON.

In de volgende tabel ziet u de taal die is toegestaan in sjablonen:

| Expressie       | Beschrijving |
| ---------------- | --- |
| $ (prop)          | Verwijzing naar een gebeurtenis eigenschap met de opgegeven naam. Eigenschaps namen zijn niet hoofdletter gevoelig. Deze expressie wordt omgezet in de tekst waarde van de eigenschap of in een lege teken reeks als de eigenschap niet aanwezig is. |
|$ (prop, n)       | Net als hierboven wordt de tekst expliciet afgekapt bij n tekens, bijvoorbeeld $ (title, 20), clips de inhoud van de eigenschap Title op 20 tekens. |
| . (prop, n)      | Net als hierboven, maar de tekst is een achtervoegsel met drie punten wanneer het is afgekapt. De totale grootte van de afgekapte teken reeks en het achtervoegsel mag niet langer zijn dan n tekens. (titel, 20) met een invoer eigenschap van ' Dit is de titel regel ' resulteert in **Dit is de titel...** |
| % (prop)          | Vergelijkbaar met $ (naam), behalve dat de uitvoer URI-gecodeerd is. |
| # (prop)          | Wordt gebruikt in JSON-sjablonen (bijvoorbeeld voor iOS-en Android-sjablonen).<br><br>Deze functie werkt precies hetzelfde als ' $ (prop) ' eerder opgegeven, behalve wanneer het wordt gebruikt in JSON-sjablonen (bijvoorbeeld Apple-sjablonen). Als deze functie niet is omgeven door ' {', '} ' (bijvoorbeeld ' myJsonProperty ': ' # (naam) ') en het resulteert in een getal in Java script-indeling, bijvoorbeeld regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, is de uitvoer-JSON een getal.<br><br>' Badge: ' # (name) ' wordt bijvoorbeeld ' badge ': 40 (en niet ' 40 '). |
| ' tekst ' of ' tekst ' | Een letterlijke waarde. Letterlijke waarden bevatten wille keurige tekst tussen enkele of dubbele aanhalings tekens. |
| Expr1 en Expr2    | De samenvoegings operator verbindt twee expressies tot één teken reeks. |

De expressies kunnen een van de voor gaande formulieren zijn.

Bij het gebruik van samen voegen moet de volledige expressie omgeven worden door `{}` . Bijvoorbeeld `{$(prop) + ‘ - ’ + $(prop2)}`.

De volgende sjabloon is bijvoorbeeld geen geldige XML-sjabloon:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Zoals eerder is uitgelegd, moeten expressies in accolades worden verpakt wanneer samen voeging wordt gebruikt. Bijvoorbeeld:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Notification Hubs](notification-hubs-push-notification-overview.md)
