---
title: Besturings elementen voor Azure API Management-pagina | Microsoft Docs
description: Meer informatie over de pagina besturings elementen die beschikbaar zijn voor gebruik in ontwikkelaars Portal sjablonen in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b826c986a0af26bb79fb0823e4e8626f0165d460
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250019"
---
# <a name="azure-api-management-page-controls"></a>Besturings elementen van Azure API Management-pagina
Azure API Management biedt de volgende besturings elementen voor gebruik in de sjablonen voor de ontwikkelaars Portal.  
  
Als u een besturings element wilt gebruiken, plaatst u het op de gewenste locatie in de sjabloon voor de ontwikkelaars Portal. Sommige besturings elementen, zoals het besturings element voor [app-acties](#app-actions) , hebben para meters, zoals wordt weer gegeven in het volgende voor beeld:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
De waarden voor de para meters worden door gegeven als onderdeel van het gegevens model voor de sjabloon. In de meeste gevallen kunt u in het voor beeld van het besturings element gewoon plakken. Meer informatie over de parameter waarden vindt u in de sectie gegevens model voor elke sjabloon waarin een besturings element mag worden gebruikt.  

Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](./api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Pagina besturings elementen voor ontwikkelaars Portal-sjabloon  
  
-   [app-acties](#app-actions)  
-   [basis-aanmelden](#basic-signin)  
-   [paginering-besturings element](#paging-control)  
-   [hardwareproviders](#providers)  
-   [besturings element zoeken](#search-control)  
-   [registratie](#sign-up)  
-   [abonneren: knop](#subscribe-button)  
-   [abonnement-annuleren](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-acties  
 Het `app-actions` besturings element biedt een gebruikers interface voor interactie met toepassingen op de pagina gebruikers profiel in de ontwikkelaars Portal.  
  
 ![besturings element voor app-&#45;acties](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-acties beheren")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parameters  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|appId|De ID van de toepassing.|  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `app-actions` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Toepassingen](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basis-aanmelden  
 Het `basic-signin` besturings element bevat een besturings element voor het verzamelen van informatie over gebruikers aanmelding op de aanmeldings pagina in de ontwikkelaars Portal.  
  
 ![basis beheer van&#45;-aanmelding](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM Basic-besturings element voor aanmelden")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `basic-signin` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Aanmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paginering-besturings element  
 De `paging-control` pagina biedt paginerings functionaliteit op pagina's voor de ontwikkelaars portal die een lijst met items weer geven.  
  
 ![besturings element voor paginering](./media/api-management-page-controls/APIM-paging-control.png "Besturings element APIM")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `paging-control` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [API-lijst](api-management-api-templates.md#APIList)  
  
-   [Lijst met problemen](api-management-issue-templates.md#IssueList)  
  
-   [Product lijst](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> hardwareproviders  
 Het `providers` besturings element biedt een besturings element voor selectie van verificatie providers op de aanmeldings pagina in de ontwikkelaars Portal.  
  
 ![besturings element providers](./media/api-management-page-controls/APIM-providers-control.png "Besturings element APIM providers")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `providers` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Aanmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> besturings element zoeken  
 De `search-control` biedt zoek functionaliteit op pagina's van de ontwikkelaars portal die een lijst met items weer geven.  
  
 ![besturings element voor zoeken](./media/api-management-page-controls/APIM-search-control.png "Besturings element voor APIM zoeken")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `search-control` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [API-lijst](api-management-api-templates.md#APIList)  
  
-   [Product lijst](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> registratie  
 Het `sign-up` besturings element bevat een besturings element voor het verzamelen van informatie over gebruikers profielen op de registratie pagina in de ontwikkelaars Portal.  
  
 ![&#45;besturings element registreren](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-registratie beheer")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `sign-up` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Aanmelden](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> abonneren: knop  
 De `subscribe-button` biedt een controle voor het abonneren van een gebruiker op een product.  
  
 ![besturings element&#45;knop Abonneren](./media/api-management-page-controls/APIM-subscribe-button-control.png "Besturings element voor APIM-Abonneer knop")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `subscribe-button` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> abonnement-annuleren  
 Het `subscription-cancel` besturings element bevat een besturings element voor het annuleren van een abonnement op een product op de pagina gebruikers profiel in de ontwikkelaars Portal.  
  
 ![abonnement&#45;besturings element annuleren](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-abonnement: beheer annuleren")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parameters  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|subscriptionId|De ID van het abonnement dat moet worden geannuleerd.|  
|cancelUrl|Het abonnement annuleert de URL.|  
  
### <a name="developer-portal-templates"></a>Sjablonen voor ontwikkelaars Portal  
 Het `subscription-cancel` besturings element kan worden gebruikt in de volgende sjablonen voor de ontwikkelaars portal:  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
