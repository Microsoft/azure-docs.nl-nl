---
title: Product sjablonen in azure API Management | Microsoft Docs
description: Meer informatie over het aanpassen van de inhoud van de product pagina's in de Azure API Management-ontwikkelaars Portal.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4c8cd4aa3e91c5d69c40e47683818ed8bc9be338
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86249900"
---
# <a name="product-templates-in-azure-api-management"></a>Product sjablonen in azure API Management

Azure API Management biedt u de mogelijkheid om de inhoud van de pagina's van de ontwikkelaars portal aan te passen met behulp van een set sjablonen waarmee de inhoud wordt geconfigureerd. Met de syntaxis van de [DotLiquid](http://dotliquidmarkup.org/) en de editor van uw keuze, zoals [DotLiquid for designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een opgegeven set gelokaliseerde [teken reeks resources](api-management-template-resources.md#strings), [glyph-resources](api-management-template-resources.md#glyphs)en [pagina besturings elementen](api-management-page-controls.md), hebt u een grote flexibiliteit om de inhoud van de pagina's zo te configureren dat ze met deze sjablonen overeenkomen.  
  
 Met de sjablonen in deze sectie kunt u de inhoud van de product pagina's in de ontwikkelaars portal aanpassen.  
  
-   [Product lijst](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  Voor beelden van standaard sjablonen zijn opgenomen in de volgende documentatie, maar zijn onderhevig aan wijzigingen als gevolg van voortdurende verbeteringen. U kunt de Live standaard sjablonen in de ontwikkelaars portal weer geven door te navigeren naar de gewenste afzonderlijke sjablonen. Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](./api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> Product lijst  
 Met de sjabloon **producten lijst** kunt u de hoofd tekst van de product lijst pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Lijst met producten](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
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
  
### <a name="controls"></a>Besturingselementen  
 De `Product list` sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [paginering-besturings element](api-management-page-controls.md#paging-control)  
  
-   [besturings element zoeken](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|Zoekresultaten oproepen|De entiteit [paging](api-management-template-data-model-reference.md#Paging) .|De paginerings gegevens voor de verzameling producten.|  
|Filteren|[Filter](api-management-template-data-model-reference.md#Filtering) entiteit.|De filter gegevens voor de lijst pagina met producten.|  
|Producten|Verzameling van [product](api-management-template-data-model-reference.md#Product) entiteiten.|De producten die zichtbaar zijn voor de huidige gebruiker.|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
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
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
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
  
##  <a name="product"></a><a name="Product"></a> Voortplant  
 Met de **product** sjabloon kunt u de hoofd tekst van de product pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Product pagina voor ontwikkelaars Portal](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Besturingselementen  
 De `Product list` sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [abonneren: knop](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|Product|[Product](api-management-template-data-model-reference.md#Product)|Het opgegeven product.|  
|IsDeveloperSubscribed|booleaans|Hiermee wordt aangegeven of de huidige gebruiker is geabonneerd op dit product.|  
|Subscription State|getal|De status van het abonnement. Mogelijke statussen zijn:<br /><br /> -   `0 - suspended` : het abonnement is geblokkeerd en de abonnee kan geen Api's van het product aanroepen.<br />-   `1 - active` : het abonnement is actief.<br />-   `2 - expired` -het abonnement heeft de verval datum bereikt en is gedeactiveerd.<br />-   `3 - submitted` : de abonnements aanvraag is ingediend door de ontwikkelaar, maar is nog niet goedgekeurd of afgekeurd.<br />-   `4 - rejected` -de abonnements aanvraag is geweigerd door een beheerder.<br />-   `5 - cancelled` : het abonnement is geannuleerd door de ontwikkelaar of beheerder.|  
|Limieten|matrix|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
|DelegatedSubscriptionEnabled|booleaans|Of [delegering](./api-management-howto-setup-delegation.md) is ingeschakeld voor dit abonnement.|  
|DelegatedSubscriptionUrl|tekenreeks|Als delegering is ingeschakeld, wordt de URL van het overgedragen abonnement.|  
|IsAgreed|booleaans|Als het product voor waarden heeft, of de huidige gebruiker de voor waarden heeft geaccepteerd.|  
|Abonnementen|Verzameling van [overzichts](api-management-template-data-model-reference.md#SubscriptionSummary) entiteiten van abonnementen.|De abonnementen op het product.|  
|APIs|Verzameling van [API](api-management-template-data-model-reference.md#API) -entiteiten.|De Api's in dit product.|  
|CannotAddBecauseSubscriptionNumberLimitReached|booleaans|Hiermee wordt aangegeven of de huidige gebruiker in aanmerking komt voor een abonnement op dit product met betrekking tot de limiet van het abonnement.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|booleaans|Hiermee wordt aangegeven of de huidige gebruiker in aanmerking komt voor het abonneren op dit product met betrekking tot het toestaan van meerdere abonnementen.|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
