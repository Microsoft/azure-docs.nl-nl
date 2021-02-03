---
title: Benoemde waarden gebruiken in azure API Management-beleid
description: Meer informatie over het gebruik van benoemde waarden in azure API Management-beleid. Benoemde waarden kunnen letterlijke teken reeksen, beleids expressies en geheimen bevatten die zijn opgeslagen in Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 344500d5635f591b34a45130c7dd6b63659ad84d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491006"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Benoemde waarden gebruiken in azure API Management-beleid

[API management-beleid](api-management-howto-policies.md) is een krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Beleids instructies kunnen worden samengesteld met behulp van letterlijke tekst waarden, beleids expressies en benoemde waarden.

*Benoemde waarden* zijn een globale verzameling naam/waarde-paren in elk API Management-exemplaar. Er is geen limiet ingesteld voor het aantal items in de verzameling. Benoemde waarden kunnen worden gebruikt voor het beheren van constante teken reeks waarden en geheimen over alle API-configuraties en-beleids regels. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Benoemde waarden in de Azure Portal":::

## <a name="value-types"></a>Waardetypen

|Type  |Beschrijving  |
|---------|---------|
|Spoor     |  Expressie voor letterlijke teken reeks of beleid     |
|Geheim     |   Letterlijke teken reeks of beleids expressie die is versleuteld met API Management      |
|[Sleutel kluis](#key-vault-secrets)     |  Id van een geheim dat is opgeslagen in een Azure-sleutel kluis.      |

Onbewerkte waarden of geheimen kunnen [beleids expressies](./api-management-policy-expressions.md)bevatten. De expressie `@(DateTime.Now.ToString())` retourneert bijvoorbeeld een teken reeks met de huidige datum en tijd.

Zie de naslag informatie over het API Management [rest API](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)voor meer informatie over de kenmerken van benoemde waarden.

## <a name="key-vault-secrets"></a>Sleutel kluis geheimen

Geheime waarden kunnen worden opgeslagen als versleutelde teken reeksen in API Management (aangepaste geheimen) of door te verwijzen naar geheimen in [Azure Key Vault](../key-vault/general/overview.md). 

Het gebruik van sleutel kluis geheimen wordt aanbevolen omdat hiermee API Management beveiliging kan worden verbeterd:

* Geheimen die zijn opgeslagen in sleutel kluizen, kunnen opnieuw worden gebruikt in verschillende services
* Gedetailleerd [toegangs beleid](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) kan worden toegepast op geheimen
* Geheimen die in de sleutel kluis zijn bijgewerkt, worden automatisch geroteerd in API Management. Na de update in de sleutel kluis wordt een benoemde waarde in API Management bijgewerkt binnen vier uur. U kunt het geheim ook hand matig vernieuwen met behulp van de Azure Portal of via de beheer REST API.

### <a name="prerequisites-for-key-vault-integration"></a>Vereisten voor sleutel kluis integratie

1. Voor stappen voor het maken van een sleutel kluis raadpleegt u [Quick Start: een sleutel kluis maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).
1. Een door het systeem toegewezen of door de gebruiker toegewezen [beheerde identiteit](api-management-howto-use-managed-service-identity.md) inschakelen in het API Management-exemplaar.
1. Wijs een [sleutel kluis toegangs beleid](../key-vault/general/assign-access-policy-portal.md) toe aan de beheerde identiteit met machtigingen om geheimen van de kluis op te halen en weer te geven. Het beleid toevoegen:
    1. Navigeer in de portal naar uw sleutel kluis.
    1. Selecteer **instellingen > toegangs beleid > + toegangs beleid toe te voegen**.
    1. Selecteer **geheime machtigingen** en selecteer **ophalen** en **lijst**.
    1. Selecteer in **Principal selecteren** de resource naam van uw beheerde identiteit. Als u een door het systeem toegewezen identiteit gebruikt, is de principal de naam van uw API Management-exemplaar.
1. Maak of importeer een geheim in de sleutel kluis. Zie [Quick Start: een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure Portal](../key-vault/secrets/quick-create-portal.md).

Als u het sleutel kluis geheim wilt gebruiken, moet u [een benoemde waarde toevoegen of bewerken](#add-or-edit-a-named-value)en een type **sleutel kluis** opgeven. Selecteer het geheim in de sleutel kluis.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Een benoemde waarde toevoegen of bewerken

### <a name="add-a-key-vault-secret"></a>Een sleutel kluis geheim toevoegen

Zie [vereisten voor de integratie van sleutel kluis](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Wanneer u een sleutel kluis geheim in API Management gebruikt, moet u ervoor zorgen dat u het geheim, de sleutel kluis of de beheerde identiteit die wordt gebruikt voor toegang tot de sleutel kluis niet verwijdert.

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer onder **api's** de optie **benoemde waarden**  >  **+ toevoegen**.
1. Voer een **naam** -id in en voer een **weergave naam** in die wordt gebruikt om naar de eigenschap in beleids regels te verwijzen.
1. Selecteer in **waardetype** **sleutel kluis**.
1. Voer de id van een geheim voor sleutel kluis in (zonder versie) of kies **selecteren** om een geheim te selecteren in een sleutel kluis.
    > [!IMPORTANT]
    > Als u zelf een geheime geheim-ID invoert, moet u ervoor zorgen dat deze geen versie gegevens bevat. Anders wordt het geheim niet automatisch gedraaid in API Management na een update in de sleutel kluis.
1. Selecteer in **client identiteit** een door het systeem toegewezen of een bestaande door de gebruiker toegewezen beheerde identiteit. Meer informatie over het [toevoegen of wijzigen van beheerde identiteiten in uw API Management-service](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > De identiteit moet machtigingen hebben om geheimen van de sleutel kluis op te halen en weer te geven. Als u de toegang tot de sleutel kluis nog niet hebt geconfigureerd, API Management u gevraagd om de identiteit automatisch te configureren met de benodigde machtigingen.
1. Voeg een of meer optionele tags toe om uw benoemde waarden te ordenen en vervolgens op te **slaan**.
1. Selecteer **Maken**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Geheime waarde voor sleutel kluis toevoegen":::

### <a name="add-a-plain-or-secret-value"></a>Een normale of geheime waarde toevoegen

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer onder **api's** de optie **benoemde waarden**  >  **+ toevoegen**.
1. Voer een **naam** -id in en voer een **weergave naam** in die wordt gebruikt om naar de eigenschap in beleids regels te verwijzen.
1. Selecteer bij **waardetype** de optie **Plain** of **geheim**.
1. Voer in **waarde** een teken reeks-of beleids expressie in.
1. Voeg een of meer optionele tags toe om uw benoemde waarden te ordenen en vervolgens op te **slaan**.
1. Selecteer **Maken**.

Zodra de benoemde waarde is gemaakt, kunt u deze bewerken door de naam te selecteren. Als u de weergave naam wijzigt, worden alle beleids regels die verwijzen naar deze benoemde waarde, automatisch bijgewerkt voor gebruik van de nieuwe weergave naam.

## <a name="use-a-named-value"></a>Een benoemde waarde gebruiken

In de voor beelden in deze sectie worden de genoemde waarden gebruikt die in de volgende tabel worden weer gegeven.

| Name               | Waarde                      | Geheim | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Niet waar  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Waar   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Niet waar  | 

Als u een benoemde waarde in een beleid wilt gebruiken, plaatst u de weergave naam ervan binnen een dubbel paar accolades `{{ContosoHeader}}` , zoals wordt weer gegeven in het volgende voor beeld:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In dit voor beeld `ContosoHeader` wordt gebruikt als de naam van een koptekst in een `set-header` beleid en `ContosoHeaderValue` wordt gebruikt als de waarde van die koptekst. Wanneer dit beleid wordt geëvalueerd tijdens een aanvraag of reactie op de API Management Gateway, `{{ContosoHeader}}` en `{{ContosoHeaderValue}}` worden vervangen door hun respectievelijke waarden.

Benoemde waarden kunnen worden gebruikt als volledige kenmerk-of element waarden, zoals wordt weer gegeven in het vorige voor beeld, maar ze kunnen ook worden ingevoegd in of gecombineerd met een letterlijke tekst expressie, zoals wordt weer gegeven in het volgende voor beeld: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Benoemde waarden kunnen ook beleids expressies bevatten. In het volgende voor beeld `ExpressionProperty` wordt de expressie gebruikt.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Wanneer dit beleid wordt geëvalueerd, `{{ExpressionProperty}}` wordt vervangen door de waarde ervan `@(DateTime.Now.ToString())` . Omdat de waarde een beleids expressie is, wordt de expressie geëvalueerd en wordt het beleid voortgezet met de uitvoering ervan.

U kunt dit testen in de Azure Portal of de [ontwikkelaars Portal](api-management-howto-developer-portal.md) door een bewerking aan te roepen die een beleid met benoemde waarden in het bereik heeft. In het volgende voor beeld wordt een bewerking aangeroepen met de twee voor gaande voorbeeld `set-header` beleidsregels met benoemde waarden. U ziet dat het antwoord twee aangepaste kopteksten bevat die zijn geconfigureerd met behulp van beleids regels met benoemde waarden.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-antwoord testen":::

Als u de uitgaande API- [tracering](api-management-howto-api-inspector.md) bekijkt voor een aanroep met de twee voor gaande voorbeeld beleidsregels met benoemde waarden, ziet u de twee `set-header` beleids regels met de naam waarden ingevoegd en de evaluatie van de beleids expressie voor de naam waarde die de beleids expressie bevat.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector-tracering":::

> [!CAUTION]
> Als een beleid verwijst naar een geheim in Azure Key Vault, is de waarde van de sleutel kluis zichtbaar voor gebruikers die toegang hebben tot abonnementen die zijn ingeschakeld voor het [traceren](api-management-howto-api-inspector.md)van de API-aanvraag.

Hoewel benoemde waarden beleids expressies kunnen bevatten, kunnen ze geen andere benoemde waarden bevatten. Als tekst met een verwijzing naar een benoemde waarde wordt gebruikt voor een waarde, zoals `Text: {{MyProperty}}` , wordt die verwijzing niet opgelost en vervangen.

## <a name="delete-a-named-value"></a>Een benoemde waarde verwijderen

Als u een benoemde waarde wilt verwijderen, selecteert u de naam en selecteert u vervolgens **verwijderen** in het context menu (**...**).

> [!IMPORTANT]
> Als er door een API Management beleid naar de benoemde waarde wordt verwezen, kunt u deze pas verwijderen als u de genoemde waarde verwijdert uit alle beleids regels die er gebruik van maken.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over het werken met beleids regels
    -   [Beleid in API Management](api-management-howto-policies.md)
    -   [Naslaginformatie over beleidsregels](./api-management-policies.md)
    -   [Beleidsexpressies](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

