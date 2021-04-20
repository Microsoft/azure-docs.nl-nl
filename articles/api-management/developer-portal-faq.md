---
title: 'Ontwikkelaarsportal : veelgestelde vragen'
titleSuffix: Azure API Management
description: Veelgestelde vragen over de ontwikkelaarsportal in API Management. De ontwikkelaarsportal is een aanpasbare website waar API-consumenten uw API's kunnen verkennen.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741608"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management-ontwikkelaarsportal - veelgestelde vragen

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Wat moet ik doen als ik functionaliteit nodig heb die niet wordt ondersteund in de portal?

U kunt een functieaanvraag openen in de [GitHub-opslagplaats](https://github.com/Azure/api-management-developer-portal) of [zelf de ontbrekende functionaliteit implementeren.](developer-portal-implement-widgets.md) Meer informatie over de [extensibility van de ontwikkelaarsportal.](api-management-howto-developer-portal.md#managed-vs-self-hosted)


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kan ik meerdere ontwikkelaarsportals in één API Management hebben?

U kunt één beheerde portal en meerdere zelf-hostende portals hebben. De inhoud van alle portals wordt opgeslagen in dezelfde API Management service, zodat ze identiek zijn. Als u het uiterlijk en de functionaliteit van portals wilt onderscheiden, kunt u ze zelf hosten met uw eigen aangepaste widgets die pagina's dynamisch aanpassen tijdens runtime, bijvoorbeeld op basis van de URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Ondersteunt de portal Azure Resource Manager sjablonen en/of is deze compatibel met API Management DevOps Resource Kit?

Nee.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Wordt de inhoud van de portal opgeslagen met de back-up-/herstelfunctionaliteit in API Management?

Nee.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Moet ik extra VNet-connectiviteit inschakelen voor de afhankelijkheden van de beheerde portal?

In de meeste gevallen - nee.

Als uw API Management zich in een intern VNet, is uw ontwikkelaarsportal alleen toegankelijk vanuit het netwerk. De hostnaam van het beheerpunt moet worden omgeholpen in het interne VIP van de service vanaf de computer die u gebruikt voor toegang tot de beheerinterface van de portal. Zorg ervoor dat het beheer-eindpunt is geregistreerd in de DNS. In het geval van een onjuiste configuratie ziet u een fout: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Als uw API Management-service zich in een intern VNet en u deze via Application Gateway via internet gebruikt, moet u connectiviteit met de ontwikkelaarsportal en de beheer eindpunten van API Management. Mogelijk moet u de Web Application Firewall uitschakelen. Zie [dit documentatieartikel](api-management-howto-integrate-internal-vnet-appgateway.md) voor meer informatie.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ik heb een aangepast API Management toegewezen en de gepubliceerde portal werkt niet

Nadat u het domein hebt bijgewerkt, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen door te voeren.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ik heb een id-provider toegevoegd en ik kan deze niet zien in de portal

Nadat u een id-provider hebt geconfigureerd (bijvoorbeeld Azure AD, Azure AD B2C), moet u de [portal](api-management-howto-developer-portal-customize.md#publish) opnieuw publiceren om de wijzigingen door te voeren. Zorg ervoor dat uw ontwikkelaarsportalpagina's de widget OAuth-knoppen bevatten.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ik heb delegatie ingesteld en de portal gebruikt deze niet

Nadat u delegering hebt ingesteld, moet u [de portal](api-management-howto-developer-portal-customize.md#publish) opnieuw publiceren om de wijzigingen door te voeren.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mijn andere API Management configuratiewijzigingen zijn niet doorgegeven in de ontwikkelaarsportal

Voor de meeste configuratiewijzigingen (bijvoorbeeld VNet, aanmelding, productvoorwaarden) moet [de portal opnieuw worden gepubliceerd.](api-management-howto-developer-portal-customize.md#publish)

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Ik krijg een CORS-fout bij het gebruik van de interactieve console

De interactieve console maakt een API-aanvraag aan de clientzijde vanuit de browser. Los het CORS-probleem op door een [CORS-beleid toe te](api-management-cross-domain-policies.md#CORS) voegen aan uw API('s).

U kunt de status van het  CORS-beleid controleren in de sectie Portaloverzicht van uw API Management service in de Azure Portal. Een waarschuwingsvak geeft een niet-geconfigureerd of onjuist geconfigureerd beleid aan.

> [!NOTE]
> 
> Er wordt slechts één CORS-beleid uitgevoerd. Als u meerdere CORS-beleidsregels hebt opgegeven (bijvoorbeeld op API-niveau en op het niveau van alle API's), werkt uw interactieve console mogelijk niet zoals verwacht.

![Schermopname die laat zien waar u de status van uw CORS-beleid kunt controleren.](media/developer-portal-faq/cors-azure-portal.png)

Pas het CORS-beleid automatisch toe door te klikken op de **knop CORS inschakelen.**

U kunt CORS ook handmatig inschakelen.

1. Selecteer de **koppeling Handmatig toepassen op de koppeling op globaal niveau** om de gegenereerde beleidscode weer te geven.
2. **Navigeer naar Alle API's** in de sectie **API's** van API Management service in de Azure Portal.
3. Selecteer het **</>** pictogram in de sectie **Binnenkomende** verwerking.
4. Voeg het beleid in de **<inbound>** sectie van het XML-bestand in. Zorg ervoor dat **<origin>** de waarde overeenkomt met het domein van uw ontwikkelaarsportal.

> [!NOTE]
> 
> Als u het CORS-beleid in het productbereik toe passen in plaats van het bereik van de API('s) en uw API gebruikmaakt van verificatie van abonnementssleutels via een header, werkt uw console niet.
>
> De browser geeft automatisch een `OPTIONS` HTTP-aanvraag uit, die geen header met de abonnementssleutel bevat. Vanwege de ontbrekende abonnementssleutel kan API Management aanroep niet koppelen aan een product, zodat het `OPTIONS` CORS-beleid niet kan worden toegepast.
>
> Als tijdelijke oplossing kunt u de abonnementssleutel doorgeven in een queryparameter.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Wat is de CORS-proxyfunctie en wanneer moet ik deze gebruiken?

Selecteer de optie **CORS-proxy** gebruiken in de configuratie van de widget DETAILS API-bewerking om de API-aanroepen van de interactieve console door te sturen via de back-end van de portal in uw API Management service. In deze configuratie hoeft u geen CORS-beleid meer toe te passen voor uw API's en is connectiviteit met het gateway-eindpunt vanaf de lokale computer niet vereist. Als de API's worden blootgesteld via een zelf-hostende gateway of als uw service zich in een virtueel netwerk, is de connectiviteit van de back-API Management-service naar de gateway vereist. Als u de zelf-hostende portal gebruikt, geeft u het back-end-eindpunt van de portal op met behulp van `backendUrl` de optie in de configuratiebestanden. Anders is de zelf-hostende portal niet op de hoogte van de locatie van de back-endservice.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welke machtigingen heb ik nodig om de ontwikkelaarsportal te bewerken?

Als u de fout ziet wanneer u de portal opent in de beheermodus, hebt u mogelijk niet de vereiste machtigingen `Oops. Something went wrong. Please try again later.` (Azure RBAC).

De verouderde portals vereist de machtiging voor het servicebereik ( ) om de `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` gebruikersbeheerder toegang te geven tot de portals. De nieuwe portal vereist de machtiging `Microsoft.ApiManagement/service/users/token/action` voor het bereik `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

U kunt het volgende PowerShell-script gebruiken om een rol te maken met de vereiste machtiging. Vergeet niet om de `<subscription-id>` parameter te wijzigen. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Zodra de rol is gemaakt, kan deze aan elke gebruiker worden verleend vanuit de sectie **Access Control (IAM)** in de Azure Portal. Als u deze rol toewijst aan een gebruiker, wordt de machtiging toegewezen aan het servicebereik. De gebruiker kan SAS-tokens genereren namens een *gebruiker* in de service. Deze rol moet minimaal worden toegewezen aan de beheerder van de service. De volgende PowerShell-opdracht laat zien hoe u de rol toewijst aan een gebruiker met het laagste bereik om te voorkomen dat de gebruiker onnodige `user1` machtigingen verleent: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nadat de machtigingen aan een gebruiker zijn verleend, moet de gebruiker zich af- en weer aanmelden bij de Azure Portal om de nieuwe machtigingen van kracht te laten worden.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Ik zie de `Unable to start the portal. See if settings are specified correctly (...)` fout

Deze fout wordt weergegeven wanneer een `GET` aanroep naar `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` mislukt. De aanroep wordt vanuit de browser uitgegeven door de beheerinterface van de portal.

Als uw API Management-service zich in een VNet, raadpleegt u de [vraag over VNet-connectiviteit.](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)

De aanroepfout kan ook worden veroorzaakt door een TLS/SSL-certificaat, dat is toegewezen aan een aangepast domein en niet wordt vertrouwd door de browser. Als oplossing kunt u het aangepaste domein voor het beheer-eindpunt verwijderen API Management terugvallen op het standaard eindpunt met een vertrouwd certificaat.

## <a name="whats-the-browser-support-for-the-portal"></a>Wat is de browserondersteuning voor de portal?

| Browser                     | Ondersteund       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> Ondersteund in de twee nieuwste productieversies.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Lokale ontwikkeling van mijn zelf-hostende portal werkt niet meer

Als uw lokale versie van de ontwikkelaarsportal geen gegevens kan opslaan of ophalen uit het opslagaccount of API Management-exemplaar, zijn de SAS-tokens mogelijk verlopen. U kunt dit oplossen door nieuwe tokens te genereren. Raadpleeg de zelfstudie voor het zelf hosten van de [ontwikkelaarsportal voor instructies.](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Hoe kan ik de inhoud van de ontwikkelaarsportal verwijderen die is ingericht voor mijn API Management service?

Geef de vereiste parameters op in het `scripts.v3/cleanup.bat` script in de [GitHub-opslagplaats](https://github.com/Azure/api-management-developer-portal)van de ontwikkelaarsportal en voer het script uit

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Hoe kan ik eenmalige aanmelding (SSO) inschakelen voor zelf-hostend ontwikkelaarsportal?

De ontwikkelaarsportal biedt onder andere ondersteuning voor eenmalige aanmelding (SSO). Als u wilt verifiëren met deze methode, moet u een aanroep naar doen `/signin-sso` met het token in de queryparameter:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Gebruikerstokens genereren
U kunt *gebruikersspecifieke tokens* (inclusief beheerderstokens) genereren met behulp van de bewerking [Token](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) voor gedeelde toegang van de [API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest).

> [!NOTE]
> Het token moet URL-gecodeerd zijn.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaarsportal:

- [De beheerde ontwikkelaarsportal openen en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf-hostende versie van de portal instellen](developer-portal-self-host.md)
- [Uw eigen widget implementeren](developer-portal-implement-widgets.md)

Blader door andere resources:

- [GitHub-opslagplaats met de broncode](https://github.com/Azure/api-management-developer-portal)

