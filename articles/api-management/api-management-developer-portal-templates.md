---
title: De API Management ontwikkelaars portal aanpassen met behulp van sjablonen
titleSuffix: Azure API Management
description: Meer informatie over het aanpassen van de Azure API Management-ontwikkelaars Portal met behulp van sjablonen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092882"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>De Azure API Management-ontwikkelaars portal aanpassen met behulp van sjablonen

Er zijn die manieren waarop u de ontwikkelaarsportal in Azure API Management kunt aanpassen:

* [De inhoud van statische pagina's en pagina-indelingselementen bewerken][modify-content-layout]
* [De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal][customize-styles]
* [De sjablonen bewerken die worden gebruikt voor pagina's die worden gegenereerd door de portal][portal-templates] (uitgelegd in deze hand leiding)

Sjablonen worden gebruikt om de inhoud van door het systeem gegenereerde ontwikkelaars Portal pagina's aan te passen (bijvoorbeeld API-documenten, producten, gebruikers verificatie, enzovoort). Met behulp van de [DotLiquid](http://dotliquidmarkup.org/) -syntaxis en een opgegeven reeks gelokaliseerde teken reeks resources, pictogrammen en pagina besturings elementen kunt u de inhoud van de pagina's zo configureren dat deze er precies zo uitziet.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Overzicht van sjablonen voor ontwikkelaars portals

Het bewerken van sjablonen wordt uitgevoerd vanuit de **ontwikkelaars Portal** terwijl u bent aangemeld als beheerder. Ga als volgt te werk om eerst de Azure Portal te openen en klik op **ontwikkelaars Portal** op de service werkbalk van uw API Management-exemplaar.

Voor toegang tot de sjablonen voor de ontwikkelaars Portal klikt u op het pictogram aanpassen aan de linkerkant om het menu aanpassing weer te geven en klikt u op **sjablonen**.

![Scherm afbeelding die het pictogram aanpassen markeert om het menu aanpassing weer te geven.][api-management-customize-menu]

In de lijst met sjablonen worden verschillende categorieën sjablonen weer gegeven die betrekking hebben op de verschillende pagina's in de ontwikkelaars Portal. Elke sjabloon is anders, maar de stappen om deze te bewerken en de wijzigingen te publiceren, zijn hetzelfde. Als u een sjabloon wilt bewerken, klikt u op de naam van de sjabloon.

![Sjablonen voor ontwikkelaars Portal][api-management-templates-menu]

Als u op een sjabloon klikt, gaat u naar de pagina ontwikkelaars portal die kan worden aangepast door die sjabloon. In dit voor beeld wordt de sjabloon **product lijst** weer gegeven. De sjabloon **product lijst** bepaalt het gebied van het scherm dat wordt aangegeven door de rode rechthoek.

![Sjabloon producten lijst][api-management-developer-portal-templates-overview]

Bij sommige sjablonen, zoals de **gebruikers profiel** sjablonen, worden verschillende delen van dezelfde pagina aangepast.

![Gebruikers profiel sjablonen][api-management-user-profile-templates]

De editor voor elke ontwikkelaars Portal sjabloon bevat twee secties die onder aan de pagina worden weer gegeven. Aan de linkerkant wordt het bewerkings venster voor de sjabloon weer gegeven en aan de rechter kant wordt het gegevens model voor de sjabloon weer gegeven.

Het deel venster sjabloon bewerken bevat de opmaak waarmee de weer gave en het gedrag van de corresponderende pagina in de ontwikkelaars portal wordt bepaald. De opmaak in de sjabloon maakt gebruik van de [DotLiquid](http://dotliquidmarkup.org/) -syntaxis. Een populaire editor voor DotLiquid is [DotLiquid voor ontwerpers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Wijzigingen die zijn aangebracht in de sjabloon tijdens het bewerken, worden in realtime weer gegeven in de browser, maar zijn niet zichtbaar voor uw klanten totdat u de sjabloon [opslaat](#to-save-a-template) en [publiceert](#to-publish-a-template) .

![Sjabloon opmaak][api-management-template]

Het deel venster **sjabloon gegevens** bevat een hand leiding voor het gegevens model voor de entiteiten die beschikbaar zijn voor gebruik in een bepaalde sjabloon. Het biedt deze hand leiding door de Live-gegevens weer te geven die momenteel worden weer gegeven in de ontwikkelaars Portal. U kunt de sjabloon deel Vensters uitvouwen door te klikken op de rechthoek in de rechter bovenhoek van het deel venster **sjabloon gegevens** .

![Sjabloon gegevens model][api-management-template-data]

In het vorige voor beeld worden twee producten weer gegeven in de ontwikkelaars portal die zijn opgehaald uit de gegevens die worden weer gegeven in het deel venster **sjabloon gegevens** , zoals wordt weer gegeven in het volgende voor beeld:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

De markering in de **product lijst** sjabloon verwerkt de gegevens om de gewenste uitvoer te leveren door de verzameling van producten te herhalen om informatie weer te geven en een koppeling naar elk afzonderlijk product. Let `<search-control>` op de `<page-control>` elementen en in de opmaak. Hiermee bepaalt u de weer gave van de besturings elementen zoeken en paginering op de pagina. `ProductsStrings|PageTitleProducts` is een gelokaliseerde teken reeks verwijzing die de `h2` koptekst voor de pagina bevat. Zie [API Management Naslag informatie voor ontwikkelaars portals](api-management-developer-portal-templates-reference.md)voor een lijst met teken reeks resources, pagina besturings elementen en pictogrammen die beschikbaar zijn voor gebruik in sjablonen voor ontwikkelaars portals.

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Een sjabloon opslaan
Klik op opslaan in de sjabloon editor om een sjabloon op te slaan.

![Sjabloon opslaan][api-management-save-template]

Opgeslagen wijzigingen zijn pas actief in de ontwikkelaars portal als ze zijn gepubliceerd.

## <a name="to-publish-a-template"></a>Een sjabloon publiceren
Opgeslagen sjablonen kunnen afzonderlijk of gezamenlijk worden gepubliceerd. Als u een afzonderlijke sjabloon wilt publiceren, klikt u in de sjabloon editor op publiceren.

![Sjabloon publiceren][api-management-publish-template]

Klik op **Ja** om te bevestigen en de sjabloon Live te maken op de ontwikkelaars Portal.

![Scherm afbeelding die laat zien waar u Ja selecteert om de sjabloon Live te maken.][api-management-publish-template-confirm]

Als u alle momenteel niet-gepubliceerde sjabloon versies wilt publiceren, klikt u op **publiceren** in de lijst met sjablonen. Niet-gepubliceerde sjablonen worden aangeduid met een asterisk dat volgt op de naam van de sjabloon. In dit voor beeld worden de **product lijst** en **product** sjablonen gepubliceerd.

![Sjablonen publiceren][api-management-publish-templates]

Klik op **aanpassingen publiceren** om te bevestigen.

![Publiceren bevestigen][api-management-publish-customizations]

Nieuwe gepubliceerde sjablonen zijn direct van kracht in de ontwikkelaars Portal.

## <a name="to-revert-a-template-to-the-previous-version"></a>Een sjabloon herstellen naar de vorige versie
Als u een sjabloon wilt herstellen naar de vorige gepubliceerde versie, klikt u in de sjabloon editor op herstellen.

![Scherm opname van het pictogram dat u gebruikt om een sjabloon te herstellen.][api-management-revert-template]

Klik op **Ja** om te bevestigen.

![Scherm afbeelding die laat zien waar u Ja selecteert om de wijzigingen te bevestigen.][api-management-revert-template-confirm]

De eerder gepubliceerde versie van een sjabloon is in de ontwikkelaars Portal Live wanneer de herstel bewerking is voltooid.

## <a name="to-restore-a-template-to-the-default-version"></a>Een sjabloon herstellen naar de standaard versie
Het herstellen van sjablonen naar de standaard versie is een proces dat uit twee stappen bestaat. Eerst moeten de sjablonen worden hersteld, waarna de herstelde versies moeten worden gepubliceerd.

Klik op herstellen in de sjabloon editor om één sjabloon te herstellen naar de standaard versie.

![Sjabloon herstellen][api-management-reset-template]

Klik op **Ja** om te bevestigen.

![Bevestigen][api-management-reset-template-confirm]

Als u alle sjablonen naar de standaard versie wilt herstellen, klikt u op **standaard sjablonen herstellen** in de lijst met sjablonen.

![Sjablonen herstellen][api-management-restore-templates]

De herstelde sjablonen moeten vervolgens afzonderlijk of allemaal tegelijk worden gepubliceerd door de stappen in [te volgen om een sjabloon te publiceren](#to-publish-a-template).

## <a name="next-steps"></a>Volgende stappen
Zie [API Management Naslag informatie voor ontwikkelaars portals](api-management-developer-portal-templates-reference.md)voor referentie-Portal sjablonen, teken reeks resources, pictogrammen en pagina besturings elementen.

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
