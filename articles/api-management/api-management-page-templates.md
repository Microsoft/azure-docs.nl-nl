---
title: Pagina sjablonen in azure API Management | Microsoft Docs
description: Meer informatie over het aanpassen van de inhoud van de pagina sjablonen voor de ontwikkelaars Portal in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335582"
---
# <a name="page-templates-in-azure-api-management"></a>Pagina sjablonen in azure API Management
Azure API Management biedt u de mogelijkheid om de inhoud van de pagina's van de ontwikkelaars portal aan te passen met behulp van een set sjablonen waarmee de inhoud wordt geconfigureerd. Met de syntaxis van de [DotLiquid](http://dotliquidmarkup.org/) en de editor van uw keuze, zoals [DotLiquid for designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een opgegeven set gelokaliseerde [teken reeks resources](api-management-template-resources.md#strings), [glyph-resources](api-management-template-resources.md#glyphs)en [pagina besturings elementen](api-management-page-controls.md), hebt u een grote flexibiliteit om de inhoud van de pagina's zo te configureren dat ze met deze sjablonen overeenkomen.  
  
 Met de sjablonen in deze sectie kunt u de inhoud van de pagina's aanmelden, registreren en pagina niet gevonden in de ontwikkelaars portal aanpassen.  
  
-   [Aanmelden](#SignIn)  
  
-   [Aanmelden](#SignUp)  
  
-   [Pagina niet gevonden](#PageNotFound)  
  
> [!NOTE]
>  Voor beelden van standaard sjablonen zijn opgenomen in de volgende documentatie, maar zijn onderhevig aan wijzigingen als gevolg van voortdurende verbeteringen. U kunt de Live standaard sjablonen in de ontwikkelaars portal weer geven door te navigeren naar de gewenste afzonderlijke sjablonen. Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](./api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> Aanmelden  
 Met de **aanmeldings** sjabloon kunt u de aanmeldings pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Aanmeldings pagina](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM Sign in-pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [basis-aanmelden](api-management-page-controls.md#basic-signin)  
  
-   [hardwareproviders](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Gegevensmodel  
 [Aanmeldings entiteit van gebruiker](api-management-template-data-model-reference.md#UseSignIn) .  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a> Aanmelden  
 Met de **registratie** sjabloon kunt u de registratie pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Pagina registreren](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM aanmelden pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan gebruikmaken van de volgende [pagina besturings elementen](api-management-page-controls.md).  
  
-   [registratie](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Gegevensmodel  
 [Gebruikers registratie](api-management-template-data-model-reference.md#UserSignUp) -entiteit.  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> Pagina niet gevonden  
 Met de sjabloon **pagina niet gevonden** kunt u de pagina niet gevonden pagina aanpassen in de ontwikkelaars Portal.  
  
 ![Pagina niet gevonden](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM niet gevonden pagina ontwikkelaars Portal sjablonen")  
  
### <a name="default-template"></a>Standaard sjabloon  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon gebruikt mogelijk geen [pagina besturings elementen](api-management-page-controls.md).  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|referenceCode|tekenreeks|Code gegenereerd als deze pagina wordt weer gegeven als gevolg van een interne fout.|  
|errorCode|tekenreeks|Code gegenereerd als deze pagina wordt weer gegeven als gevolg van een interne fout.|  
|emailBody|tekenreeks|E-mail hoofdtekst gegenereerd als deze pagina wordt weer gegeven als gevolg van een interne fout.|  
|requestedUrl|tekenreeks|De aangevraagde URL wanneer de pagina niet is gevonden.|  
|referrerUrl|tekenreeks|De verwijzende URL naar de aangevraagde URL.|  
  
### <a name="sample-template-data"></a>Voorbeeld sjabloon gegevens  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
