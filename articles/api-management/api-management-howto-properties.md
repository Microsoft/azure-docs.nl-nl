---
title: Benoemde waarden gebruiken in Azure API Management-beleid
description: Meer informatie over het gebruik van benoemde waarden in Azure API Management beleid. Benoemde waarden kunnen letterlijke tekenreeksen, beleidsexpressie en geheimen bevatten die zijn opgeslagen in Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: b0e076f3b248942870ba58a51c85c3df1f1277a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750604"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Benoemde waarden gebruiken in Azure API Management beleid

[API Management zijn een](api-management-howto-policies.md) krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Beleidsverklaringen kunnen worden samengesteld met letterlijke tekstwaarden, beleidsexpressie en benoemde waarden.

*Benoemde* waarden zijn een globale verzameling naam-waardeparen in elk API Management-instantie. Er is geen limiet voor het aantal items in de verzameling. Benoemde waarden kunnen worden gebruikt voor het beheren van constante tekenreekswaarden en geheimen in alle API-configuraties en -beleidsregels. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Benoemde waarden in het Azure Portal":::

## <a name="value-types"></a>Waardetypen

|Type  |Description  |
|---------|---------|
|Plain     |  Letterlijke tekenreeks of beleidsexpressie     |
|Geheim     |   Letterlijke tekenreeks of beleidsexpressie die wordt versleuteld door API Management      |
|[Sleutelkluis](#key-vault-secrets)     |  Id van een geheim dat is opgeslagen in een Azure-sleutelkluis.      |

Gewone waarden of geheimen kunnen [beleidsexpressies bevatten.](./api-management-policy-expressions.md) De expressie retourneert `@(DateTime.Now.ToString())` bijvoorbeeld een tekenreeks met de huidige datum en tijd.

Zie de naslaginformatie voor API Management [REST API over de kenmerken van de REST API benoemde waarde.](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)

## <a name="key-vault-secrets"></a>Key Vault-geheimen

Geheime waarden kunnen worden opgeslagen als versleutelde tekenreeksen in API Management (aangepaste geheimen) of door te verwijzen naar geheimen in [Azure Key Vault](../key-vault/general/overview.md). 

Het gebruik van Key Vault-geheimen wordt aanbevolen omdat dit helpt om de beveiliging API Management verbeteren:

* Geheimen die zijn opgeslagen in sleutelkluizen kunnen opnieuw worden gebruikt in verschillende services
* Gedetailleerd [toegangsbeleid kan](../key-vault/general/security-overview.md#privileged-access) worden toegepast op geheimen
* Geheimen die zijn bijgewerkt in de sleutelkluis, worden automatisch geroteerd in API Management. Na de update in de sleutelkluis wordt een benoemde waarde in API Management binnen vier uur bijgewerkt. U kunt het geheim ook handmatig vernieuwen met behulp van de Azure Portal of via de REST API.

### <a name="prerequisites-for-key-vault-integration"></a>Vereisten voor key vault-integratie

1. Zie Voor stappen voor het maken van een sleutelkluis [Quickstart: Een sleutelkluis maken met behulp van de Azure Portal.](../key-vault/general/quick-create-portal.md)
1. Schakel een door het systeem toegewezen of door de gebruiker toegewezen [beheerde](api-management-howto-use-managed-service-identity.md) identiteit in het API Management in.
1. Wijs een [toegangsbeleid voor een](../key-vault/general/assign-access-policy-portal.md) sleutelkluis toe aan de beheerde identiteit met machtigingen om geheimen op te halen en weer te geven uit de kluis. Het beleid toevoegen:
    1. Navigeer in de portal naar uw sleutelkluis.
    1. Selecteer **Instellingen > toegangsbeleid > +Toegangsbeleid toevoegen.**
    1. Selecteer **Geheime machtigingen** en selecteer vervolgens **Get** en **List.**
    1. Selecteer **in Principal selecteren** de resourcenaam van uw beheerde identiteit. Als u een door het systeem toegewezen identiteit gebruikt, is de principal de naam van uw API Management-exemplaar.
1. Maak of importeer een geheim in de sleutelkluis. Zie [Quickstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van Azure Portal](../key-vault/secrets/quick-create-portal.md).

Als u het sleutelkluisgeheim wilt gebruiken, [voegt u een benoemde waarde](#add-or-edit-a-named-value)toe of bewerkt u deze, en geeft u het type Sleutelkluis **op.** Selecteer het geheim in de sleutelkluis.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Een benoemde waarde toevoegen of bewerken

### <a name="add-a-key-vault-secret"></a>Een sleutelkluisgeheim toevoegen

Zie [Vereisten voor key vault-integratie.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Wanneer u een sleutelkluisgeheim gebruikt in API Management, moet u ervoor oppassen dat u het geheim, de sleutelkluis of de beheerde identiteit die wordt gebruikt voor toegang tot de sleutelkluis, niet verwijdert.

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **onder API's** **benoemde waarden**  >  **+Toevoegen.**
1. Voer een **Naam-id** in en voer een **Weergavenaam in die** wordt gebruikt om in beleidsregels naar de eigenschap te verwijzen.
1. Selecteer **in Waardetype** de optie **Sleutelkluis.**
1. Voer de id van een sleutelkluisgeheim in (zonder versie) of kies **Selecteren om** een geheim uit een sleutelkluis te selecteren.
    > [!IMPORTANT]
    > Als u zelf een geheime id voor een sleutelkluis in typt, moet u ervoor zorgen dat deze geen versiegegevens heeft. Anders wordt het geheim niet automatisch geroteerd in API Management na een update in de sleutelkluis.
1. Selecteer **in Clientidentiteit** een door het systeem toegewezen of een bestaande door de gebruiker toegewezen beheerde identiteit. Meer informatie over het [toevoegen of wijzigen van beheerde identiteiten in uw API Management service](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > De identiteit heeft machtigingen nodig om geheimen uit de sleutelkluis op te halen en weer te geven. Als u de toegang tot de sleutelkluis nog niet hebt geconfigureerd, wordt API Management gevraagd zodat de identiteit automatisch kan worden geconfigureerd met de benodigde machtigingen.
1. Voeg een of meer optionele tags toe om uw benoemde waarden te organiseren en klik vervolgens **op Opslaan.**
1. Selecteer **Maken**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Geheime waarde voor sleutelkluis toevoegen":::

### <a name="add-a-plain-or-secret-value"></a>Een gewone of geheime waarde toevoegen

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **onder API's** **benoemde waarden**  >  **+Toevoegen.**
1. Voer een **Naam-id** in en voer een **Weergavenaam in die** wordt gebruikt om in beleidsregels naar de eigenschap te verwijzen.
1. Bij **Waardetype** selecteert **u Gewoon of** **Geheim.**
1. Voer **in Waarde** een tekenreeks of beleidsexpressie in.
1. Voeg een of meer optionele tags toe om uw benoemde waarden te organiseren en vervolgens **Opslaan.**
1. Selecteer **Maken**.

Zodra de benoemde waarde is gemaakt, kunt u deze bewerken door de naam te selecteren. Als u de weergavenaam wijzigt, worden beleidsregels die verwijzen naar die benoemde waarde automatisch bijgewerkt om de nieuwe weergavenaam te gebruiken.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gaan gebruiken, gaat u als volgende te werk:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Als u een benoemde waarde wilt toevoegen, gebruikt u [de opdracht az apim nv create:](/cli/azure/apim/nv#az_apim_nv_create)

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Nadat u een benoemde waarde hebt gemaakt, kunt u deze bijwerken met behulp van de [opdracht az apim nv update.](/cli/azure/apim/nv#az_apim_nv_update) Voer de opdracht [az apim nv list](/cli/azure/apim/nv#az_apim_nv_list) uit om al uw benoemde waarden te zien:

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Voer de opdracht [az apim nv show](/cli/azure/apim/nv#az_apim_nv_show) uit om de details te zien van de benoemde waarde die u voor dit voorbeeld hebt gemaakt:

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Dit voorbeeld is een geheime waarde. De vorige opdracht retourneert de waarde niet. Voer de opdracht [az apim nv show-secret](/cli/azure/apim/nv#az_apim_nv_show_secret) uit om de waarde te zien:

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Als u een benoemde waarde wilt verwijderen, gebruikt u [de opdracht az apim nv delete:](/cli/azure/apim/nv#az_apim_nv_delete)

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Een benoemde waarde gebruiken

In de voorbeelden in deze sectie worden de benoemde waarden gebruikt die worden weergegeven in de volgende tabel.

| Name               | Waarde                      | Geheim | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Niet waar  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Waar   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Niet waar  | 

Als u een benoemde waarde in een beleid wilt gebruiken, moet u de weergavenaam binnen een dubbel paar accolades plaatsen, zoals `{{ContosoHeader}}` wordt weergegeven in het volgende voorbeeld:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In dit voorbeeld wordt gebruikt als de naam van een header in een beleid en wordt gebruikt als de `ContosoHeader` `set-header` waarde van die `ContosoHeaderValue` header. Wanneer dit beleid wordt geëvalueerd tijdens een aanvraag of reactie op API Management gateway en `{{ContosoHeader}}` wordt vervangen door de respectieve `{{ContosoHeaderValue}}` waarden.

Benoemde waarden kunnen worden gebruikt als volledige kenmerk- of elementwaarden zoals weergegeven in het vorige voorbeeld, maar ze kunnen ook worden ingevoegd in of gecombineerd met een deel van een letterlijke tekstexpressie, zoals wordt weergegeven in het volgende voorbeeld: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Benoemde waarden kunnen ook beleidsexpressie bevatten. In het volgende voorbeeld wordt de `ExpressionProperty` expressie gebruikt.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Wanneer dit beleid wordt geëvalueerd, `{{ExpressionProperty}}` wordt vervangen door de waarde ervan, `@(DateTime.Now.ToString())` . Omdat de waarde een beleidsexpressie is, wordt de expressie geëvalueerd en wordt het beleid uitgevoerd.

U kunt dit testen in de Azure Portal of de [ontwikkelaarsportal](api-management-howto-developer-portal.md) door een bewerking aan te roepen die een beleid met benoemde waarden binnen het bereik heeft. In het volgende voorbeeld wordt een bewerking aangeroepen met de twee vorige `set-header` voorbeeldbeleidsregels met benoemde waarden. U ziet dat het antwoord twee aangepaste headers bevat die zijn geconfigureerd met behulp van beleidsregels met benoemde waarden.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-antwoord testen":::

Als u de uitgaande [API-traceer](api-management-howto-api-inspector.md) bekijkt voor een aanroep die de twee vorige voorbeeldbeleidsregels met benoemde waarden bevat, ziet u de twee beleidsregels met de benoemde waarden die zijn ingevoegd, evenals de evaluatie van de beleidsexpressie voor de benoemde waarde die de beleidsexpressie `set-header` bevat.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector-trace":::

> [!CAUTION]
> Als een beleid verwijst naar een geheim in Azure Key Vault, is de waarde uit de sleutelkluis zichtbaar voor gebruikers die toegang hebben tot abonnementen die zijn ingeschakeld voor het traceren van [API-aanvragen.](api-management-howto-api-inspector.md)

Benoemde waarden kunnen beleidsexpressies bevatten, maar ze kunnen geen andere benoemde waarden bevatten. Als tekst met een verwijzing naar een benoemde waarde wordt gebruikt voor een waarde, zoals , wordt die verwijzing niet `Text: {{MyProperty}}` opgelost en vervangen.

## <a name="delete-a-named-value"></a>Een benoemde waarde verwijderen

Als u een benoemde waarde wilt verwijderen, selecteert u de naam en selecteert **u vervolgens Verwijderen** in het contextmenu (**...**).

> [!IMPORTANT]
> Als naar de benoemde waarde wordt verwezen door een API Management-beleid, kunt u deze pas verwijderen als u de benoemde waarde verwijdert uit alle beleidsregels die deze gebruiken.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over het werken met beleidsregels
    -   [Beleidsregels in API Management](api-management-howto-policies.md)
    -   [Naslaginformatie over beleidsregels](./api-management-policies.md)
    -   [Beleidsexpressies](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

