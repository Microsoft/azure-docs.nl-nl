---
title: Sjablonen uitgeven in azure API Management | Microsoft Docs
description: Meer informatie over het aanpassen van de inhoud van de probleem pagina's in de ontwikkelaars Portal in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85846850"
---
# <a name="issue-templates-in-azure-api-management"></a>Sjablonen uitgeven in azure API Management
Azure API Management biedt u de mogelijkheid om de inhoud van de pagina's van de ontwikkelaars portal aan te passen met behulp van een set sjablonen waarmee de inhoud wordt geconfigureerd. Met de syntaxis van de [DotLiquid](http://dotliquidmarkup.org/) en de editor van uw keuze, zoals [DotLiquid for designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een opgegeven set gelokaliseerde [teken reeks resources](api-management-template-resources.md#strings), [glyph-resources](api-management-template-resources.md#glyphs)en [pagina besturings elementen](api-management-page-controls.md), hebt u een grote flexibiliteit om de inhoud van de pagina's zo te configureren dat ze met deze sjablonen overeenkomen.  
  
 Met de sjablonen in deze sectie kunt u de inhoud van de uitgifte pagina's in de ontwikkelaars portal aanpassen.  
  
-   [Lijst met problemen](#IssueList)  
  
> [!NOTE]
>  Voor beelden van standaard sjablonen zijn opgenomen in de volgende documentatie, maar zijn onderhevig aan wijzigingen als gevolg van voortdurende verbeteringen. U kunt de Live standaard sjablonen in de ontwikkelaars portal weer geven door te navigeren naar de gewenste afzonderlijke sjablonen. Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a> Lijst met problemen  
 Met de sjabloon **probleem lijst** kunt u de hoofd tekst van de pagina probleem lijst in de ontwikkelaars portal aanpassen.  
  
 ![Ontwikkelaars Portal probleem lijst](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Ontwikkelaars portal voor APIM issue List")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Besturingselementen  
 De `Issue list` sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [paginering-besturings element](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Issues`|Verzameling van [uitgifte](api-management-template-data-model-reference.md#Issue) -entiteiten.|De problemen die zichtbaar zijn voor de huidige gebruiker.|  
|`Paging`|De entiteit [paging](api-management-template-data-model-reference.md#Paging) .|De paginerings gegevens voor de verzameling toepassingen.|  
|`IsAuthenticated`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker is aangemeld bij de ontwikkelaars Portal.|  
|`CanReportIssues`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker machtigingen heeft om een probleem op te lossen.|  
|`Search`|tekenreeks|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
