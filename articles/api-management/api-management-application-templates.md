---
title: Toepassings sjablonen in azure API Management | Microsoft Docs
description: Meer informatie over het aanpassen van de inhoud van de toepassings pagina's in de ontwikkelaars Portal in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1452f380cec711fb224f532ccb02d11c5bbad697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86255180"
---
# <a name="application-templates-in-azure-api-management"></a>Toepassings sjablonen in azure API Management
Azure API Management biedt u de mogelijkheid om de inhoud van de pagina's van de ontwikkelaars portal aan te passen met behulp van een set sjablonen waarmee de inhoud wordt geconfigureerd. Met de syntaxis van de [DotLiquid](http://dotliquidmarkup.org/) en de editor van uw keuze, zoals [DotLiquid for designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een opgegeven set gelokaliseerde [teken reeks resources](api-management-template-resources.md#strings), [glyph-resources](api-management-template-resources.md#glyphs)en [pagina besturings elementen](api-management-page-controls.md), hebt u een grote flexibiliteit om de inhoud van de pagina's zo te configureren dat ze met deze sjablonen overeenkomen.  
  
 Met de sjablonen in deze sectie kunt u de inhoud van de toepassings pagina's in de ontwikkelaars portal aanpassen.  
  
-   [Lijst met toepassingen](#ProductList)  
  
-   [Toepassing](#Application)  
  
> [!NOTE]
>  Voor beelden van standaard sjablonen zijn opgenomen in de volgende documentatie, maar zijn onderhevig aan wijzigingen als gevolg van voortdurende verbeteringen. U kunt de Live standaard sjablonen in de ontwikkelaars portal weer geven door te navigeren naar de gewenste afzonderlijke sjablonen. Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](./api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="application-list"></a><a name="ProductList"></a> Lijst met toepassingen  
 Met de sjabloon **toepassings lijst** kunt u de hoofd tekst van de lijst pagina met toepassingen in de ontwikkelaars portal aanpassen.  
  
 ![Application List-pagina ontwikkelaars Portal sjablonen](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM Application List-pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Paging`|De entiteit [paging](api-management-template-data-model-reference.md#Paging) .|De paginerings gegevens voor de verzameling toepassingen.|  
|`Applications`|Verzameling [toepassings](api-management-template-data-model-reference.md#Application) entiteiten.|De toepassingen die zichtbaar zijn voor de huidige gebruiker.|  
|`CategoryName`|tekenreeks|De categorie van de toepassing.|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="application"></a><a name="Application"></a> Modules  
 Met de **toepassings** sjabloon kunt u de hoofd tekst van de toepassings pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Toepassings pagina ontwikkelaars Portal sjablonen](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM toepassings pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Besturingselementen  
 De `Application` sjabloon staat het gebruik van geen enkele [pagina besturings elementen](api-management-page-controls.md)toe.  
  
### <a name="data-model"></a>Gegevensmodel  
 [Toepassings](api-management-template-data-model-reference.md#Application) entiteit.  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
