---
title: Gebruikers profiel sjablonen in azure API Management | Microsoft Docs
description: Meer informatie over het aanpassen van de inhoud van de gebruikers profiel pagina's in de ontwikkelaars Portal in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2e3b73ef-d223-44fe-9280-c3af3fd4a030
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1aef238ec0b947dda1417b567b343ae9d92754d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86249509"
---
# <a name="user-profile-templates-in-azure-api-management"></a>Gebruikers profiel sjablonen in azure API Management
Azure API Management biedt u de mogelijkheid om de inhoud van de pagina's van de ontwikkelaars portal aan te passen met behulp van een set sjablonen waarmee de inhoud wordt geconfigureerd. Met de syntaxis van de [DotLiquid](http://dotliquidmarkup.org/) en de editor van uw keuze, zoals [DotLiquid for designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een opgegeven set gelokaliseerde [teken reeks resources](api-management-template-resources.md#strings), [glyph-resources](api-management-template-resources.md#glyphs)en [pagina besturings elementen](api-management-page-controls.md), hebt u een grote flexibiliteit om de inhoud van de pagina's zo te configureren dat ze met deze sjablonen overeenkomen.  
  
 Met de sjablonen in deze sectie kunt u de inhoud van de gebruikers profiel pagina's in de ontwikkelaars portal aanpassen.  
  
-   [Profiel](#Profile)  
  
-   [Abonnementen](#Subscriptions)  
  
-   [Toepassingen](#Applications)  
  
-   [Account gegevens bijwerken](#UpdateAccountInfo)  
  
> [!NOTE]
>  Voor beelden van standaard sjablonen zijn opgenomen in de volgende documentatie, maar zijn onderhevig aan wijzigingen als gevolg van voortdurende verbeteringen. U kunt de Live standaard sjablonen in de ontwikkelaars portal weer geven door te navigeren naar de gewenste afzonderlijke sjablonen. Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](./api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="profile"></a><a name="Profile"></a> Uplinkpoortprofiel  
 Met de **profiel** sjabloon kunt u de sectie gebruikers profiel van de pagina gebruikers profiel in de ontwikkelaars portal aanpassen.  
  
 ![Gebruikers profiel pagina](./media/api-management-user-profile-templates/APIM-User-Profile-Page.png "Pagina APIM-gebruikers profiel")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<div class="pull-right">  
  {% if canChangePassword == true %}  
  <a class="btn btn-default" id="ChangePassword" role="button" href="{{changePasswordUrl}}">{% localized "UserProfile|ButtonLabelChangePassword" %}</a>  
  {% endif %}  
  <a id="changeAccountInfo" href="{{changeNameOrEmailUrl}}" class="btn btn-default">  
    <span class="glyphicon glyphicon-user"></span>  
    <span>{% localized "UserProfile|ButtonLabelChangeAccountInfo" %}</span>  
  </a>  
</div>  
<h2>{% localized "SubscriptionStrings|PageTitleDeveloperProfile" %}</h2>  
<div class="container-fluid">  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="Email">{% localized "UserProfile|TextboxLabelEmail" %}</label>  
    </div>  
    <div class="col-sm-9" id="Email">{{email}}</div>  
  </div>  
  
  {% if isSystemUser != true %}  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="FirstName">{% localized "UserProfile|TextboxLabelEmailFirstName" %}</label>  
    </div>  
    <div class="col-sm-9" id="FirstName">{{FirstName}}</div>  
  </div>  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="LastName">{% localized "UserProfile|TextboxLabelEmailLastName" %}</label>  
    </div>  
    <div class="col-sm-9" id="LastName">{{LastName}}</div>  
  </div>  
  {% else %}  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="CompanyName">{% localized "UserProfile|TextboxLabelOrganizationName" %}</label>  
    </div>  
    <div class="col-sm-9" id="CompanyName">{{CompanyName}}</div>  
  </div>  
  <div class="row">  
    <div class="col-sm-3">  
      <label for="AddresserEmail">{% localized "UserProfile|TextboxLabelNotificationsSenderEmail" %}</label>  
    </div>  
    <div class="col-sm-9" id="AddresserEmail">{{AddresserEmail}}</div>  
  </div>  
  {% endif %}  
  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon gebruikt mogelijk geen [pagina besturings elementen](api-management-page-controls.md).  
  
### <a name="data-model"></a>Gegevensmodel  
  
> [!NOTE]
>  De sjablonen [profiel](#Profile), [toepassingen](#Applications)en [abonnementen](#Subscriptions) delen hetzelfde gegevens model en ontvangen dezelfde sjabloon gegevens.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`firstName`|tekenreeks|De voor naam van de huidige gebruiker.|  
|`lastName`|tekenreeks|De achternaam van de huidige gebruiker.|  
|`companyName`|tekenreeks|De bedrijfs naam van de huidige gebruiker.|  
|`addresserEmail`|tekenreeks|E-mailadres van de huidige gebruiker.|  
|`developersUsageStatisticsLink`|tekenreeks|Relatieve URL voor het weer geven van analyses voor de huidige gebruiker.|  
|`subscriptions`|Verzameling [abonnements](api-management-template-data-model-reference.md#Subscription) entiteiten.|De abonnementen voor de huidige gebruiker.|  
|`applications`|Verzameling [toepassings](api-management-template-data-model-reference.md#Application) entiteiten.|De toepassingen van de huidige gebruiker.|  
|`changePasswordUrl`|tekenreeks|De relatieve URL voor het wijzigen van het wacht woord van de huidige gebruiker.|  
|`changeNameOrEmailUrl`|tekenreeks|De relatieve URL voor het wijzigen van de naam en het e-mail adres van de huidige gebruiker.|  
|`canChangePassword`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker het wacht woord kan wijzigen.|  
|`isSystemUser`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker lid is van een van de ingebouwde [groepen](api-management-key-concepts.md#groups).|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="subscriptions"></a><a name="Subscriptions"></a> Geabonneerd  
 Met de **abonnementen** sjabloon kunt u de sectie abonnementen van de pagina gebruikers profiel in de ontwikkelaars portal aanpassen.  
  
 ![Pagina abonnements gebruiker](./media/api-management-user-profile-templates/APIM-User-Subscription-Page.png "Pagina APIM-gebruikers abonnement")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<div class="ap-account-subscriptions">  
  <a href="{{developersUsageStatisticsLink}}" id="UsageStatistics" class="btn btn-default pull-right">  
    <span class="glyphicon glyphicon-stats"></span>  
    <span>{% localized "SubscriptionListStrings|WebDevelopersUsageStatisticsLink" %}</span>  
  </a>  
  
  <h2>{% localized "SubscriptionListStrings|WebDevelopersYourSubscriptions" %}</h2>  
  
  <table class="table">  
    <thead>  
      <tr>  
        <th>Subscription details</th>  
        <th>Product</th>  
        <th>{% localized "SubscriptionListStrings|WebDevelopersSubscriptionTableStateHeader" %}</th>  
        <th>Action</th>  
      </tr>  
    </thead>  
    <tbody>  
      {% if subscriptions.size == 0 %}  
      <tr>  
        <td class="text-center" colspan="4">  
          {% localized "CommonResources|NoItemsToDisplay" %}  
        </td>  
      </tr>  
      {% else %}  
      {% for subscription in subscriptions %}  
      <tr id="{{subscription.id}}" {% if subscription.hasExpired %} class="expired" {% endif %}>  
        <td>  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelName" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.displayName }}  
            </div>  
            <div class="col-lg-2">  
              <a class="btn-link" href="/Subscriptions/{{subscription.id}}/Rename">Rename</a>  
            </div>  
            <div class="clearfix"></div>  
          </div>  
          {% if subscription.isAwaitingApproval %}  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelRequestedDate" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.createdDate | date:"MM/dd/yyyy" }}  
            </div>  
          </div>  
          {% else %}  
          {% if subscription.isRejected == false %}  
          {% if subscription.startDate %}  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|SubscriptionPropertyLabelStartedDate" %}</label>  
            <div class="col-lg-6">  
              {{ subscription.startDate | date:"MM/dd/yyyy" }}  
            </div>  
          </div>  
          {% endif %}  
  
          <!-- ko with: Developers.Account.Root.account.key('{{subscription.primaryKey}}', '{{subscription.id}}', true) -->  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|WebDevelopersPrimaryKey" %}</label>  
            <div class="col-lg-6">  
              <code data-bind="text: $data.displayKey()" id="primary_{{subscription.id}}"></code>  
            </div>  
            <div class="col-lg-2">  
              <!-- ko if: !requestInProgress() -->  
              <div class="nowrap">  
                <a href="#" class="btn-link" id="togglePrimary_{{subscription.id}}" data-bind="click: toggleKeyDisplay, text: toggleKeyLabel"></a>  
                |  
                <a href="#" class="btn-link" id="regeneratePrimary_{{subscription.id}}" data-bind="click: regenerateKey, text: regenerateKeyLabel"></a>  
              </div>  
              <!-- /ko -->  
              <!-- ko if: requestInProgress() -->  
              <div class="progress progress-striped active">  
                <div class="progress-bar" role="progressbar" aria-valuenow="100" aria-valuemin="0" aria-valuemax="100" style="width: 100%">  
                  <span class="sr-only"></span>  
                </div>  
              </div>  
              <!-- /ko -->  
            </div>  
            <div class="clearfix"></div>  
          </div>  
          <!-- /ko -->  
          <!-- ko with: Developers.Account.Root.account.key('{{subscription.secondaryKey}}', '{{subscription.id}}', false) -->  
          <div class="row">  
            <label class="col-lg-3">{% localized "SubscriptionListStrings|WebDevelopersSecondaryKey" %}</label>  
            <div class="col-lg-6">  
              <code data-bind="text: $data.displayKey()" id="secondary_{{subscription.id}}"></code>  
            </div>  
            <div class="col-lg-2">  
              <div class="nowrap">  
                <a href="#" class="btn-link" id="toggleSecondary_{{subscription.id}}" data-bind="click: toggleKeyDisplay, text: toggleKeyLabel">{% localized "SubscriptionListStrings|ButtonLabelShowKey" %}</a>  
                |  
                <a href="#" class="btn-link" id="regenerateSecondary_{{subscription.id}}" data-bind="click: regenerateKey, text: regenerateKeyLabel">{% localized "SubscriptionListStrings|WebDevelopersRegenerateLink" %}</a>  
              </div>  
            </div>  
            <div class="clearfix"> </div>  
          </div>  
          <!-- /ko -->  
          {% endif %}  
          {% endif %}  
        </td>  
        <td>  
          <a href="{{subscription.productDetailsUrl}}">{{subscription.productTitle}}</a>  
        </td>  
        <td>  
          <strong>  
            {{subscription.state}}  
          </strong>  
        </td>  
        <td>  
          <div class="nowrap">  
            {% if subscription.canBeCancelled %}  
            <subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }"></subscription-cancel>  
            {% endif %}  
          </div>  
        </td>  
      </tr>  
      {% endfor %}  
      {% endif %}  
    </tbody>  
  </table>  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [abonnement-annuleren](api-management-page-controls.md#subscription-cancel)  
  
### <a name="data-model"></a>Gegevensmodel  
  
> [!NOTE]
>  De sjablonen [profiel](#Profile), [toepassingen](#Applications)en [abonnementen](#Subscriptions) delen hetzelfde gegevens model en ontvangen dezelfde sjabloon gegevens.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`firstName`|tekenreeks|De voor naam van de huidige gebruiker.|  
|`lastName`|tekenreeks|De achternaam van de huidige gebruiker.|  
|`companyName`|tekenreeks|De bedrijfs naam van de huidige gebruiker.|  
|`addresserEmail`|tekenreeks|E-mailadres van de huidige gebruiker.|  
|`developersUsageStatisticsLink`|tekenreeks|Relatieve URL voor het weer geven van analyses voor de huidige gebruiker.|  
|`subscriptions`|Verzameling [abonnements](api-management-template-data-model-reference.md#Subscription) entiteiten.|De abonnementen voor de huidige gebruiker.|  
|`applications`|Verzameling [toepassings](api-management-template-data-model-reference.md#Application) entiteiten.|De toepassingen van de huidige gebruiker.|  
|`changePasswordUrl`|tekenreeks|De relatieve URL voor het wijzigen van het wacht woord van de huidige gebruiker.|  
|`changeNameOrEmailUrl`|tekenreeks|De relatieve URL voor het wijzigen van de naam en het e-mail adres van de huidige gebruiker.|  
|`canChangePassword`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker het wacht woord kan wijzigen.|  
|`isSystemUser`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker lid is van een van de ingebouwde [groepen](api-management-key-concepts.md#groups).|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="applications"></a><a name="Applications"></a> Toepassingen  
 Met de sjabloon **toepassingen** kunt u de sectie abonnementen van de pagina gebruikers profiel in de ontwikkelaars portal aanpassen.  
  
 ![De pagina gebruikers account toepassingen](./media/api-management-user-profile-templates/APIM-User-Account-Applications-Page.png "Pagina APIM-gebruikers accounts")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<div class="ap-account-applications">  
  <a id="RegisterApplication" href="/Developer/Applications/Register" class="btn btn-success pull-right">  
    <span class="glyphicon glyphicon-plus"></span>  
    <span>{% localized "ApplicationListStrings|WebDevelopersRegisterAppLink" %}</span>  
  </a>  
  <h2>{% localized "ApplicationListStrings|WebDevelopersYourApplicationsHeader" %}</h2>  
  
  <table class="table">  
    <thead>  
      <tr>  
        <th class="col-md-8">{% localized "ApplicationListStrings|WebDevelopersAppTableNameHeader" %}</th>  
        <th class="col-md-2">{% localized "ApplicationListStrings|WebDevelopersAppTableCategoryHeader" %}</th>  
        <th class="col-md-2" colspan="2">{% localized "ApplicationListStrings|WebDevelopersAppTableStateHeader" %}</th>  
      </tr>  
    </thead>  
    <tbody>  
  
      {% if applications.size == 0 %}  
  
      <tr>  
        <td class="col-md-12 text-center" colspan="4">  
          {% localized "CommonResources|NoItemsToDisplay" %}  
        </td>  
      </tr>  
  
      {% else %}  
  
      {% for app in applications %}  
      <tr>  
        <td class="col-md-8">  
          {{app.title}}  
        </td>  
        <td class="col-md-2">  
          {{app.categoryName}}  
        </td>  
        <td class="col-md-2">  
          <strong>  
            {% case app.state %}  
            {% when ApplicationStateModel.Registered %}  
            {% localized "ApplicationListStrings|WebDevelopersAppNotSubmitted" %}  
  
            {% when ApplicationStateModel.Unpublished %}  
            {% localized "ApplicationListStrings|WebDevelopersAppNotPublished" %}  
  
            {% else %}  
            {{ app.state }}  
            {% endcase %}  
          </strong>  
        </td>  
        <td class="col-md-1">  
          <div class="nowrap">  
            {% if app.state != ApplicationStateModel.Submitted and app.state != ApplicationStateModel.Published %}  
            <app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
            {% endif %}  
          </div>  
        </td>  
      </tr>  
      {% endfor %}  
  
      {% endif %}  
    </tbody>  
  </table>  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [app-acties](api-management-page-controls.md#app-actions)  
  
### <a name="data-model"></a>Gegevensmodel  
  
> [!NOTE]
>  De sjablonen [profiel](#Profile), [toepassingen](#Applications)en [abonnementen](#Subscriptions) delen hetzelfde gegevens model en ontvangen dezelfde sjabloon gegevens.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`firstName`|tekenreeks|De voor naam van de huidige gebruiker.|  
|`lastName`|tekenreeks|De achternaam van de huidige gebruiker.|  
|`companyName`|tekenreeks|De bedrijfs naam van de huidige gebruiker.|  
|`addresserEmail`|tekenreeks|E-mailadres van de huidige gebruiker.|  
|`developersUsageStatisticsLink`|tekenreeks|Relatieve URL voor het weer geven van analyses voor de huidige gebruiker.|  
|`subscriptions`|Verzameling [abonnements](api-management-template-data-model-reference.md#Subscription) entiteiten.|De abonnementen voor de huidige gebruiker.|  
|`applications`|Verzameling [toepassings](api-management-template-data-model-reference.md#Application) entiteiten.|De toepassingen van de huidige gebruiker.|  
|`changePasswordUrl`|tekenreeks|De relatieve URL voor het wijzigen van het wacht woord van de huidige gebruiker.|  
|`changeNameOrEmailUrl`|tekenreeks|De relatieve URL voor het wijzigen van de naam en het e-mail adres van de huidige gebruiker.|  
|`canChangePassword`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker het wacht woord kan wijzigen.|  
|`isSystemUser`|booleaans|Hiermee wordt aangegeven of de huidige gebruiker lid is van een van de ingebouwde [groepen](api-management-key-concepts.md#groups).|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "firstName": "Administrator",  
    "lastName": "",  
    "companyName": "Contoso",  
    "addresserEmail": "apimgmt-noreply@mail.windowsazure.com",  
    "email": "admin@live.com",  
    "developersUsageStatisticsLink": "/Developer/Analytics",  
    "subscriptions": [  
        {  
            "Id": "57026e30de15d80041070001",  
            "ProductId": "57026e30de15d80041060001",  
            "ProductTitle": "Starter",  
            "ProductDescription": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060001",  
            "State": "Active",  
            "DisplayName": "Starter  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.847",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "b6b2870953d04420a4e02c58f2c08e74",  
            "SecondaryKey": "cfe28d5a1cd04d8abc93f48352076ea5",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070001/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070001/Renew"  
        },  
        {  
            "Id": "57026e30de15d80041070002",  
            "ProductId": "57026e30de15d80041060002",  
            "ProductTitle": "Unlimited",  
            "ProductDescription": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "ProductDetailsUrl": "/Products/57026e30de15d80041060002",  
            "State": "Active",  
            "DisplayName": "Unlimited  (default)",  
            "CreatedDate": "2016-04-04T13:37:52.923",  
            "CanBeCancelled": true,  
            "IsAwaitingApproval": false,  
            "StartDate": null,  
            "ExpirationDate": null,  
            "NotificationDate": null,  
            "PrimaryKey": "8fe7843c36de4cceb4728e6cae297336",  
            "SecondaryKey": "96c850d217e74acf9b514ff8a5b38551",  
            "UserId": 1,  
            "CanBeRenewed": false,  
            "HasExpired": false,  
            "IsRejected": false,  
            "CancelUrl": "/Subscriptions/57026e30de15d80041070002/Cancel",  
            "RenewUrl": "/Subscriptions/57026e30de15d80041070002/Renew"  
        }  
    ],  
    "applications": [],  
    "changePasswordUrl": "/account/password/change",  
    "changeNameOrEmailUrl": "/account/update",  
    "canChangePassword": false,  
    "isSystemUser": true  
}  
```  
  
##  <a name="update-account-info"></a><a name="UpdateAccountInfo"></a> Account gegevens bijwerken  
 Met de sjabloon **Update account info** kunt u de pagina **account gegevens bijwerken** aanpassen in de ontwikkelaars Portal.  
  
 ![Gebruikers account informatie pagina ontwikkelaars Portal sjablonen](./media/api-management-user-profile-templates/APIM-User-Account-Info-Page-Developer-Portal-Templates.png "APIM-gebruikers account informatie pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<div class="row">  
  <div class="col-sm-6 col-md-6">  
    <div class="form-group">  
      <label for="Email">{% localized "SigninResources|TextboxLabelEmail" %}</label>  
      <input autofocus="autofocus" class="form-control" id="Email" name="Email" type="text" value="{{email}}">  
    </div>  
    <div class="form-group">  
      <label for="FirstName">{% localized "SigninResources|TextboxLabelEmailFirstName" %}</label>  
      <input class="form-control" id="FirstName" name="FirstName" type="text" value="{{firstName}}">  
    </div>  
    <div class="form-group">  
      <label for="LastName">{% localized "SigninResources|TextboxLabelEmailLastName" %}</label>  
      <input class="form-control" id="LastName" name="LastName" type="text" value="{{lastName}}">  
    </div>  
    <div class="form-group">  
      <label for="Password">{% localized "SigninResources|WebAuthenticationSigninPasswordLabel" %}</label>  
      <input class="form-control" id="Password" name="Password" type="password">  
    </div>  
  </div>  
</div>  
  
<button type="submit" class="btn btn-primary" id="UpdateProfile">  
  {% localized "UpdateProfileStrings|ButtonLabelUpdateProfile" %}  
</button>  
<a class="btn btn-default" href="/developer" role="button">  
  {% localized "CommonStrings|ButtonLabelCancel" %}  
</a>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon gebruikt mogelijk geen [pagina besturings elementen](api-management-page-controls.md).  
  
### <a name="data-model"></a>Gegevensmodel  
 Entiteit [gebruikers account info](api-management-template-data-model-reference.md#UserAccountInfo) .  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "FirstName": "Administrator",  
    "LastName": "",  
    "Email": "admin@live.com",  
    "Password": null,  
    "NameIdentifier": null,  
    "ProviderName": null,  
    "IsBasicAccount": false  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
