---
title: Overeenkomende voor waarden voor de Azure front deur Standard/Premium-regel instellingen configureren
description: Dit artikel bevat een lijst met de verschillende match-voor waarden die beschikbaar zijn in de Azure front deur Standard/Premium-regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 9e8defa9e929d21f210c48ffbd3b22e44195c17d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061618"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Voor waarden van de regel set voor Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In de Azure front deur Standard/Premium- [regelset](concept-rule-set.md)bestaat een regel uit nul of meer match voorwaarden en een actie. Dit artikel bevat gedetailleerde beschrijvingen van de match-voor waarden die u kunt gebruiken in de Azure front deur Standard/Premium-regelset.

Het eerste deel van een regel is een voorwaarde van overeenkomst of een set voorwaarden van overeenkomst. Een regel kan uit maximaal 10 voorwaarden van overeenkomst bestaan. Een voorwaarde van overeenkomst identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere voorwaarden van overeenkomst gebruikt, worden de voorwaarden van overeenkomst samen gegroepeerd met behulp van EN-logica. Voor alle match-voor waarden die meerdere waarden ondersteunen, of logica wordt gebruikt.

U kunt een match-voor waarde gebruiken voor het volgende:

* Aanvragen filteren op basis van een specifiek IP-adres, specifiek land of specifieke regio.
* Aanvragen filteren op headergegevens.
* Aanvragen filteren van mobiele apparaten of desktopapparaten.
* Aanvragen filteren op basis van de naam en bestands extensie van het aanvraag bestand.
* Aanvragen filteren op basis van de aanvraag-URL, het Protocol, het pad, de query reeks, de argumenten post, enzovoort.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="device-type"></a><a name="IsDevice"></a> Apparaattype

Gebruik de voor waarde matching van het **Apparaattype** om aanvragen te identificeren die zijn gemaakt op een mobiel apparaat of desktop apparaat.  

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-------|------------------|
| Operator | <ul><li>In de Azure Portal: `Equal` , `Not Equal`</li><li>In ARM-sjablonen: `Equal` ; Gebruik de `negateCondition` eigenschap om _niet gelijk aan_ te geven |
| Waarde | `Mobile`, `Desktop` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken die zijn gedetecteerd als afkomstig van een mobiel apparaat.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Scherm opname van de portal met de voor waarde voor de overeenkomst voor het apparaattype.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Argumenten plaatsen

Gebruik de voor waarde **argumenten na** overeenkomst om aanvragen te identificeren op basis van de argumenten die zijn opgegeven in de hoofd tekst van een post-aanvraag. Een enkele match-voor waarde komt overeen met één argument van de hoofd tekst van de aanvraag. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!NOTE]
> De voor waarde voor het vergelijken van **argumenten** is geschikt voor het `application/x-www-form-urlencoded` inhouds type.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Argumenten plaatsen | Een teken reeks waarde voor de naam van het POST-argument. |
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden die de waarde van het argument POST moeten overeenkomen. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld komen we overeen met alle POST-aanvragen waarbij een `customerName` argument is opgegeven in de hoofd tekst van de aanvraag en waar de waarde `customerName` begint met de letter `J` of `K` . We gebruiken een aanvraag transformatie om de invoer waarden om te zetten in hoofd letters, zodat waarden die beginnen met `J` , `j` , `K` , en `k` allemaal overeenkomen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Scherm opname van de portal met de voor waarde bij overeenkomst met argumenten.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Query reeks

Gebruik de voor waarde voor de overeenkomst met de **query teken reeks** om aanvragen te identificeren die een specifieke query teken reeks bevatten. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!NOTE]
> De hele query teken reeks wordt als één teken reeks vergeleken, zonder de regel afstand `?` .

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Queryreeksen | Een of meer teken reeks-of gehele waarden die de waarde van de query teken reeks moeten overeenkomen. Neem niet het `?` begin van de query teken reeks op. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarin de query reeks de teken reeks bevat `language=en-US` . We willen dat de match-voor waarde hoofdletter gevoelig is. de aanvraag wordt dus niet getransformeerd.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Scherm opname van de portal met de voor waarde query reeks overeenkomst.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Extern adres

Met de voor waarde **extern adres** overeenkomst worden aanvragen geïdentificeerd op basis van de locatie of het IP-adres van de aanvrager. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

* Gebruik CIDR-notatie wanneer IP-adres blokken worden opgegeven. Dit betekent dat de syntaxis van een IP-adres blok het basis-IP-adres is, gevolgd door een slash en de grootte van het voor voegsel. Bijvoorbeeld:
    * **IPv4-voor beeld**: `5.5.5.64/26` komt overeen met aanvragen die binnenkomen via 5.5.5.64 via 5.5.5.127.
    * **IPv6-voor beeld**: `1:2:3:/48` komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.
* Wanneer u meerdere IP-adressen en IP-adres blokken opgeeft, wordt de logica ' OR ' toegepast.
    * **IPv4-voor beeld**: als u twee IP-adressen toevoegt `1.2.3.4` en `10.20.30.40` , wordt de voor waarde vergeleken voor aanvragen die afkomstig zijn van een van de adressen 1.2.3.4 of 10.20.30.40.
    * **IPv6-voor beeld**: als u twee IP-adressen toevoegt `1:2:3:4:5:6:7:8` en `10:20:30:40:50:60:70:80` , wordt de voor waarde vergeleken voor aanvragen die afkomstig zijn van de adressen 1:2:3:4:5:6:7:8 en 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | <ul><li>In de Azure portal: `Geo Match` , `Geo Not Match` , `IP Match` of `IP Not Match`</li><li>In ARM-sjablonen: `GeoMatch` , `IPMatch` ; Gebruik de `negateCondition` eigenschap om _geo-overeenkomsten_ op te geven of het _IP-adres komt niet overeen_</li></ul> |
| Waarde | <ul><li>Voor de `IP Match` `IP Not Match` Opera tors or: Geef een of meer IP-adresbereiken op. Als meerdere IP-adresbereiken zijn opgegeven, worden ze geëvalueerd met of Logic.</li><li>Voor de `Geo Match` `Geo Not Match` Opera tors or: Geef een of meer locaties op met behulp van hun land nummer.</li></ul> |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarvan de aanvraag afkomstig is uit het Verenigde Staten.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Scherm opname van de portal met de voor waarde extern adres overeenkomst.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Aanvraag tekst

Met de voor waarde matching **Body van aanvraag** worden aanvragen geïdentificeerd op basis van specifieke tekst die wordt weer gegeven in de hoofd tekst van de aanvraag. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!NOTE]
> Als een aanvraag tekst groter is dan 64 KB, wordt alleen de eerste 64 kB in rekening gebracht voor de match-voor waarde van de **aanvraag tekst** .

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden die de waarde van de hoofd tekst van de aanvraag vertegenwoordigen. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarin de hoofd tekst van de aanvraag de teken reeks bevat `ERROR` . De hoofd tekst van de aanvraag wordt omgezet in hoofd letters voordat de overeenkomst wordt geëvalueerd, dus `error` en andere case variaties activeren ook deze overeenkomst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Scherm opname van de portal met de voor waarde aanvraag body.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Bestands naam van aanvraag

In de voor waarde voor de **aanvraag bestands naam** overeenkomst worden aanvragen geïdentificeerd die de opgegeven bestands naam in de aanvraag-URL bevatten. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden voor de waarde van de naam van het aanvraag bestand dat moet worden gevonden. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen met de naam van het aanvraag bestand `media.mp4` . De bestands naam wordt omgezet in kleine letters voordat de overeenkomst wordt geëvalueerd, dus `MEDIA.MP4` en andere case variaties activeren ook deze overeenkomst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Scherm afbeelding van de portal met de voor waarde voor de bestands naam van de aanvraag.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Bestands extensie voor aanvraag

In de voor waarde voor het vergelijken van het **aanvraag bestand** worden aanvragen geïdentificeerd die de opgegeven bestands extensie bevatten in de bestands naam in de aanvraag-URL. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!NOTE]
> Voeg geen voorloop periode toe. U kunt bijvoorbeeld `html` weergeven in plaats van `.html`.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden die de waarde van de bestands extensie van de aanvraag vertegenwoordigen die moet overeenkomen. Voeg geen voorloop periode toe. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen met de extensie van het aanvraag bestand `pdf` of opgegeven `docx` . De uitbrei ding van het aanvraag bestand wordt omgezet in kleine letters voordat de overeenkomst wordt geëvalueerd, dus, `PDF` `DocX` en voor andere case-variaties wordt deze overeenkomst ook geactiveerd.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Scherm opname van de portal met de voor waarde voor het vergelijken van de bestands extensie.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Aanvraag header

De voor waarde **aanvraag header** matching identificeert aanvragen die een specifieke header in de aanvraag bevatten. U kunt deze match-voor waarde gebruiken om te controleren of een header bestaat uit de waarde of om te controleren of de header overeenkomt met een opgegeven waarde. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Headernaam | Een teken reeks waarde voor de naam van het POST-argument. |
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden voor de waarde van de aanvraag header die moet worden gezocht. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarin de aanvraag een header bevat met de naam `MyCustomHeader` , onafhankelijk van de waarde.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Scherm opname van de portal met de voor waarde voor de overeenkomende aanvraag header.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Aanvraag methode

Met de voor waarde voor de matching- **methode** voor aanvragen worden aanvragen geïdentificeerd die de opgegeven HTTP-aanvraag methode gebruiken. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | <ul><li>In de Azure Portal: `Equal` , `Not Equal`</li><li>In ARM-sjablonen: `Equal` ; Gebruik de `negateCondition` eigenschap om _niet gelijk aan_ te geven |
| Aanvraagmethode | Een of meer HTTP-methoden van: `GET` , `POST` ,,,, `PUT` `DELETE` `HEAD` `OPTIONS` , `TRACE` . Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarbij de-aanvraag gebruikmaakt van de- `DELETE` methode.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Scherm opname van de portal met de voor waarde voor de matching methode.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Pad van aanvraag

In de voor waarde voor het vergelijken van **aanvragen** worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aanvraag-URL. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!NOTE]
> Het pad is het deel van de URL na de hostnaam en een slash. Het pad is bijvoorbeeld in de URL `https://www.contoso.com/files/secure/file1.pdf` `files/secure/file1.pdf` .

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden voor de waarde van het aanvraag pad dat moet worden gezocht. Neem de voorloop back slash niet op. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarbij het pad naar het aanvraag bestand begint met `files/secure/` . We transformeren de extensie van het aanvraag bestand naar kleine letters voordat u de overeenkomst evalueert, zodat aanvragen `files/SECURE/` en andere case-variaties ook deze match-voor waarde activeren.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Scherm opname van de portal met de voor waarde aanvraag Path match.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Aanvraag Protocol

De voor waarde voor het vergelijken van het **aanvraag protocol** identificeert aanvragen die gebruikmaken van het opgegeven protocol (http of https).

> [!NOTE]
> Het *protocol* wordt soms ook wel *schema* genoemd.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | <ul><li>In de Azure Portal: `Equal` , `Not Equal`</li><li>In ARM-sjablonen: `Equal` ; Gebruik de `negateCondition` eigenschap om _niet gelijk aan_ te geven |
| Aanvraagmethode | `HTTP`, `HTTPS` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarbij de aanvraag gebruikmaakt van het `HTTP` protocol.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Scherm opname van de portal met de voor waarde aanvraag protocol match.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL. De volledige URL wordt geëvalueerd, inclusief het protocol en de query teken reeks, maar niet het fragment. U kunt meerdere waarden opgeven die moeten overeenkomen, die worden gecombineerd met of logica.

> [!TIP]
> Wanneer u deze regel voorwaarde gebruikt, moet u ervoor zorgen dat u het protocol bijvoegt. Gebruik bijvoorbeeld `https://www.contoso.com` in plaats van alleen `www.contoso.com` .

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-|-|
| Operator | Een operator uit de [lijst met standaard operators](#operator-list). |
| Waarde | Een of meer teken reeks-of gehele waarden die de waarde van de aanvraag-URL moeten overeenkomen. Als er meerdere waarden worden opgegeven, worden ze geëvalueerd met of logica. |
| Transformatie van hoofdletters en kleine letters | `Lowercase`, `Uppercase` |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen vergeleken waarbij de aanvraag-URL begint met `https://api.contoso.com/customers/123` . We transformeren de extensie van het aanvraag bestand naar kleine letters voordat u de overeenkomst evalueert, zodat aanvragen `https://api.contoso.com/Customers/123` en andere case-variaties ook deze match-voor waarde activeren.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Scherm afbeelding van de portal toont de voor waarde voor de overeenkomst-URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Operator lijst

De volgende operators zijn geldig voor regels die waarden accepteren van de lijst met standaardoperators:

| Operator                   | Beschrijving                                                                                                                    | Ondersteuning voor ARM-sjablonen                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Alle                        | Komt overeen met een wille keurige waarde, ongeacht wat het is.                                                                     | `operator`: `Any`                                               |
| Is gelijk aan                      | Komt overeen wanneer de waarde precies overeenkomt met de opgegeven teken reeks.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Komt overeen wanneer de waarde de opgegeven teken reeks bevat.                                                                          | `operator`: `Contains`                                          |
| Kleiner dan                  | Komt overeen wanneer de lengte van de waarde kleiner is dan het opgegeven gehele getal.                                                       | `operator`: `LessThan`                                          |
| Greater Than               | Komt overeen wanneer de lengte van de waarde groter is dan het opgegeven gehele getal.                                                    | `operator`: `GreaterThan`                                       |
| Kleiner dan of gelijk aan         | Komt overeen wanneer de lengte van de waarde kleiner is dan of gelijk is aan het opgegeven gehele getal.                                           | `operator`: `LessThanOrEqual`                                   |
| Groter dan of gelijk aan      | Komt overeen wanneer de lengte van de waarde groter is dan of gelijk is aan het opgegeven gehele getal.                                        | `operator`: `GreaterThanOrEqual`                                |
| Begint met                | Komt overeen wanneer de waarde begint met de opgegeven teken reeks.                                                                       | `operator`: `BeginsWith`                                        |
| Eindigt op                  | Komt overeen wanneer de waarde eindigt met de opgegeven teken reeks.                                                                         | `operator`: `EndsWith`                                          |
| Reguliere                      | Komt overeen wanneer de waarde overeenkomt met de opgegeven reguliere expressie. [Zie hieronder voor meer informatie.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Geen                    | Komt overeen wanneer er geen waarde is.                                                                                                | `operator`: `Any` en `negateCondition` : `true`                |
| Niet gelijk aan                  | Komt overeen wanneer de waarde niet overeenkomt met de opgegeven teken reeks.                                                                    | `operator`: `Equal` en `negateCondition` : `true`              |
| Bevat geen               | Komt overeen wanneer de waarde de opgegeven teken reeks niet bevat.                                                                  | `operator`: `Contains` en `negateCondition` : `true`           |
| Niet kleiner dan              | Komt overeen wanneer de lengte van de waarde niet kleiner is dan het opgegeven gehele getal.                                                   | `operator`: `LessThan` en `negateCondition` : `true`           |
| Niet groter dan           | Komt overeen wanneer de lengte van de waarde niet groter is dan het opgegeven gehele getal.                                                | `operator`: `GreaterThan` en `negateCondition` : `true`        |
| Niet kleiner dan of gelijk aan     | Komt overeen wanneer de lengte van de waarde niet kleiner is dan of gelijk is aan het opgegeven geheel getal.                                       | `operator`: `LessThanOrEqual` en `negateCondition` : `true`    |
| Niet groter dan of gelijk aan | Komt overeen wanneer de lengte van de waarde niet groter is dan of gelijk is aan het opgegeven gehele getal.                                    | `operator`: `GreaterThanOrEqual` en `negateCondition` : `true` |
| Begint niet met            | Komt overeen wanneer de waarde niet begint met de opgegeven teken reeks.                                                               | `operator`: `BeginsWith` en `negateCondition` : `true`         |
| Eindigt niet met              | Komt overeen wanneer de waarde niet eindigt met de opgegeven teken reeks.                                                                 | `operator`: `EndsWith` en `negateCondition` : `true`           |
| Geen RegEx                  | Komt overeen wanneer de waarde niet overeenkomt met de opgegeven reguliere expressie. [Zie hieronder voor meer informatie.](#regular-expressions) | `operator`: `RegEx` en `negateCondition` : `true`              |

> [!TIP]
> Voor numerieke operators zoals *Kleiner dan* en *Groter dan of gelijk aan* wordt de gebruikte vergelijking gebaseerd op lengte. De waarde in de match-voor waarde moet een geheel getal zijn dat de lengte aangeeft die u wilt vergelijken.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Reguliere expressies

Reguliere expressies bieden geen ondersteuning voor de volgende bewerkingen:

* Backreferences en vastleggen van subexpressies.
* Wille keurige verklaringen met een breedte nul.
* Subroutine verwijzingen en recursieve patronen.
* Voorwaardelijke patronen.
* Backtracking-besturings woorden.
* De `\C` instructie single-byte.
* De `\R` instructie nieuwe regel komt overeen.
* De `\K` instructie start of match reset.
* Bijschriften en Inge sloten code.
* Atomische groepering en behorende Kwant.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [regel instellingen](concept-rule-set.md).
* Meer informatie over het [configureren van uw eerste regelset](how-to-configure-rule-set.md).
* Meer informatie over [acties voor regel sets](concept-rule-set-actions.md).
