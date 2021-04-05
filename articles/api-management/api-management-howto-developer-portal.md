---
title: Overzicht van de ontwikkelaars Portal in azure API Management
titleSuffix: Azure API Management
description: Meer informatie over de ontwikkelaars Portal in API Management-een aanpas bare website, waar API-gebruikers uw Api's kunnen verkennen.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074794"
---
# <a name="overview-of-the-developer-portal"></a>Overzicht van de ontwikkelaars Portal

Ontwikkelaars Portal is een automatisch gegenereerde, volledig aanpas bare website met de documentatie van uw Api's. Het is waar API-consumers uw Api's kunnen detecteren, meer informatie over het gebruik ervan moeten gebruiken, toegang te vragen en uit te proberen.

In dit artikel worden de verschillen beschreven tussen zelf-hostende en beheerde versies van de ontwikkelaars Portal in API Management. Ook vindt u hier antwoorden op veelgestelde vragen.

![API Management ontwikkelaars Portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migratie van de verouderde Portal

> [!IMPORTANT]
> De verouderde ontwikkelaars Portal is nu afgeschaft en er worden alleen beveiligings updates ontvangen. U kunt deze zoals gebruikelijk blijven gebruiken tot de buitengebruikstelling in oktober 2023, wanneer de portal wordt verwijderd uit alle API Management Services.

Migratie naar de nieuwe ontwikkelaars portal wordt beschreven in het [artikel speciale documentatie](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Aanpassing en opmaak

De ontwikkelaars Portal kan worden aangepast en opgemaakt met behulp van de ingebouwde, visuele editor voor slepen en neerzetten. Raadpleeg [deze zelf studie](api-management-howto-developer-portal-customize.md) voor meer informatie.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Setup

Uw API Management-service bevat een ingebouwde, altijd actuele, **beheerde** ontwikkelaars Portal. U kunt deze openen via de Azure Portal-interface.

Als u deze wilt uitbreiden met aangepaste logica, die niet out-of-the-box wordt ondersteund, kunt u de code base wijzigen. De code base van de portal is [beschikbaar in een github-opslag plaats][1]. U kunt bijvoorbeeld een nieuwe widget implementeren die kan worden geïntegreerd met een ondersteunings systeem van derden. Wanneer u nieuwe functionaliteit implementeert, kunt u een van de volgende opties kiezen:

- Zelf de resulterende Portal buiten uw API Management-service **hosten** . Wanneer u de portal zelf host, wordt u een onderhouds beheerder en bent u verantwoordelijk voor de upgrades. Ondersteunings ondersteuning van Azure is alleen beperkt tot de basis installatie van zelf-hostende portals, zoals beschreven in de [sectie wiki van de opslag plaats][2].
- Open een pull-aanvraag voor het API Management team om nieuwe functionaliteit in de code basis van de **beheerde** Portal samen te voegen.

Raadpleeg de [github-opslag plaats][1] en [de zelf studies voor het implementeren van een widget][3]voor meer informatie over uitbreid baarheid en instructies. De [zelf studie voor het aanpassen van de beheerde Portal](api-management-howto-developer-portal-customize.md) leidt u door het beheer paneel van de portal, wat gebruikelijk is voor **beheerde** en **zelf-hostende** versies.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Veelgestelde vragen

In deze sectie beantwoorden we veelgestelde vragen over de ontwikkelaars Portal, die algemeen zijn. Raadpleeg [de sectie wiki van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/wiki)voor vragen die specifiek zijn voor de zelf-hostende versie.

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Hoe kan ik migreren vanuit de preview-versie van de portal?

Wanneer u de preview-versie van de ontwikkelaars portal voor het eerst hebt gestart, hebt u de preview-versie van de standaard inhoud in uw API Management-service ingericht. De standaard inhoud is aanzienlijk gewijzigd in de algemeen beschik bare versie. De preview-versie van standaard inhoud bevat echter geen OAuth-knoppen in de aanmeldings pagina's, maar gebruikt verschillende widgets voor het weer geven van Api's en is afhankelijk van de beperkte mogelijkheden voor het structureren van ontwikkelaars Portal pagina's. Hoewel er verschillen zijn in de inhoud, wordt de engine van de portal (inclusief onderliggende objecten) automatisch bijgewerkt telkens wanneer u uw ontwikkelaars Portal publiceert.

Als u uw portal sterk hebt aangepast op basis van de preview-versie van de inhoud, kunt u deze gewoon blijven gebruiken en nieuwe widgets hand matig op de pagina's van de portal plaatsen. Anders is het raadzaam om de inhoud van uw portal te vervangen door de nieuwe standaard inhoud.

Als u de inhoud in een beheerde Portal opnieuw wilt instellen, selecteert u **inhoud opnieuw instellen** in de sectie **bewerkingen** menu. Met deze bewerking wordt de inhoud van de portal verwijderd en worden nieuwe standaard inhoud ingericht. Alle aanpassingen en wijzigingen van de ontwikkelaars portal gaan verloren. **U kunt deze actie niet ongedaan maken**.

![Portal-inhoud opnieuw instellen](media/api-management-howto-developer-portal/reset-content.png)

Als u de zelf-hostende versie gebruikt, voert `scripts.v2/cleanup.bat` u `scripts.v2/generate.bat` scripts uit vanuit de GitHub-opslag plaats om bestaande inhoud te verwijderen en nieuwe inhoud in te richten. Zorg ervoor dat u de code van uw portal bijwerkt naar de nieuwste versie van de GitHub-opslag plaats.

Als u de portal voor het eerst hebt geopend na de aankondiging van de algemene Beschik baarheid in november 2019, moet deze de nieuwe standaard inhoud al gebruiken en hoeft er verder geen actie te worden ondernomen.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>De benodigde functionaliteit wordt niet ondersteund in de portal

U kunt een functie aanvraag openen in de [github-opslag plaats][1] of [de ontbrekende functionaliteit zelf implementeren][3]. Zie de sectie **uitbreid baarheid** hierboven voor meer informatie.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Hoe kan ik Portal implementaties automatiseren?

U kunt via de REST API programmatisch toegang krijgen tot de inhoud van de ontwikkelaars Portal en deze beheren, ongeacht of u een beheerde of zelf-hostende versie gebruikt.

De API wordt beschreven in [de sectie wiki van de GitHub-opslag plaats][2]. Het kan worden gebruikt voor het automatiseren van de migratie van Portal-inhoud tussen omgevingen, bijvoorbeeld vanuit een test omgeving naar de productie omgeving. Meer informatie over dit proces vindt u [in dit documentatie artikel](https://aka.ms/apimdocs/migrateportal) op github.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Hoe kan ik van het beheerde naar de zelf-hostende versie?

Zie het gedetailleerde artikel in [het gedeelte wiki van de opslag plaats voor ontwikkel aars van de ontwikkelaars Portal op github][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kan ik meerdere portals voor ontwikkel aars hebben in één API Management service?

U kunt één beheerde Portal en meerdere zelf-hostende Port alen hebben. De inhoud van alle portals wordt opgeslagen in dezelfde API Management-service, zodat ze identiek zijn. Als u de vormgeving en functionaliteit van portals wilt onderscheiden, kunt u ze zelf hosten met uw eigen aangepaste widgets die de pagina's in runtime dynamisch aanpassen, bijvoorbeeld op basis van de URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Ondersteunt de portal Azure Resource Manager sjablonen en/of is deze compatibel met API Management DevOps Resource Kit?

Nee.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Is de inhoud van de portal opgeslagen met de functionaliteit voor back-up/herstel in API Management?

Nee.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Moet ik extra VNet-connectiviteit inschakelen voor de afhankelijkheden van de beheerde Portal?

In de meeste gevallen-Nee.

Als uw API Management-service zich in een intern VNet bevindt, is uw ontwikkelaars Portal alleen toegankelijk vanuit het netwerk. De hostnaam van het beheer eindpunt moet worden omgezet naar het interne VIP van de service van de computer die u gebruikt voor toegang tot de beheer interface van de portal. Controleer of het beheer eindpunt is geregistreerd in de DNS. In het geval van een onjuiste configuratie ziet u een fout: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Als uw API Management-service zich in een intern VNet bevindt en u deze via Application Gateway via internet opent, moet u ervoor zorgen dat u verbinding met de ontwikkelaars Portal en de beheer eindpunten van API Management inschakelt. Mogelijk moet u de firewall regels voor webtoepassingen uitschakelen. Raadpleeg [Dit documentatie artikel](api-management-howto-integrate-internal-vnet-appgateway.md) voor meer informatie.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ik heb een aangepast API Management domein toegewezen en de gepubliceerde Portal werkt niet

Nadat u het domein hebt bijgewerkt, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ik heb een id-provider toegevoegd en deze kan niet worden weer geven in de portal

Nadat u een id-provider (bijvoorbeeld Azure AD, Azure AD B2C) hebt geconfigureerd, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden. Zorg ervoor dat de pagina's van de ontwikkelaars Portal de widget OAuth buttons bevatten.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ik heb delegering ingesteld en de portal gebruikt deze niet

Nadat u de overdracht hebt ingesteld, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>De wijzigingen in de configuratie van mijn andere API Management zijn niet door gegeven in de ontwikkelaars Portal

Voor de meeste configuratie wijzigingen (bijvoorbeeld VNet, aanmelden, product voorwaarden) moet [de portal opnieuw worden gepubliceerd](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Ik krijg een CORS-fout bij het gebruik van de interactieve console

De interactieve console maakt een API aan de client zijde via de browser. Los het CORS-probleem op door [een CORS-beleid](api-management-cross-domain-policies.md#CORS) toe te voegen aan uw API (s).

U kunt de status van het CORS-beleid controleren in het overzicht van de **Portal** van uw API Management-service in de Azure Portal. Een waarschuwing geeft aan dat het beleid ontbreekt of onjuist is geconfigureerd.

![Scherm afbeelding die laat zien waar u de status van het CORS-beleid kunt controleren.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Pas het CORS-beleid automatisch toe door op de knop **CORS inschakelen** te klikken.

U kunt CORS ook hand matig inschakelen.

1. Selecteer het veld **hand matig Toep assen op de koppeling globaal niveau** om de gegenereerde beleids code te zien.
2. Ga naar **alle api's** in de sectie **api's** van uw API Management-service in de Azure Portal.
3. Selecteer het **</>** pictogram in de sectie **binnenkomende verwerking** .
4. Voeg het beleid in de **<inbound>** sectie van het XML-bestand in. Zorg ervoor dat de **<origin>** waarde overeenkomt met het domein van uw ontwikkelaars Portal.

> [!NOTE]
> 
> Als u het CORS-beleid toepast in het product bereik, in plaats van de API (s)-scope, en uw API gebruikmaakt van abonnements sleutel verificatie via een header, werkt de console niet.
>
> De browser verleent automatisch een HTTP-aanvraag voor opties, die geen header met de abonnements sleutel bevat. Vanwege de ontbrekende abonnements sleutel kan API Management de aanroep van de opties niet koppelen aan een product, zodat het CORS-beleid niet kan worden toegepast.
>
> Als tijdelijke oplossing kunt u de abonnements sleutel in een query parameter door geven.

> [!NOTE]
> 
> Er wordt slechts één CORS-beleid uitgevoerd. Als u meerdere CORS-beleids regels hebt opgegeven (bijvoorbeeld op het API-niveau en op het niveau all-Api's), werkt uw interactieve console mogelijk niet zoals verwacht.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welke machtigingen heb ik nodig om de ontwikkelaars portal te bewerken?

Als u de `Oops. Something went wrong. Please try again later.` fout melding ziet wanneer u de portal in de beheer modus opent, hebt u mogelijk geen vereiste machtigingen (Azure RBAC).

De verouderde portals vereist de machtiging `Microsoft.ApiManagement/service/getssotoken/action` bij het service bereik ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) om de beheerder toe te staan om toegang te krijgen tot de portals. De nieuwe portal vereist de machtiging `Microsoft.ApiManagement/service/users/token/action` voor het bereik `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

U kunt het volgende Power shell-script gebruiken om een rol te maken met de vereiste machtiging. Vergeet niet om de `<subscription-id>` para meter te wijzigen. 

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
 
Zodra de rol is gemaakt, kan deze worden verleend aan een gebruiker uit de sectie **Access Control (IAM)** in de Azure Portal. Als u deze rol aan een gebruiker toewijst, wordt de machtiging in het service bereik toegewezen. De gebruiker kan SAS-tokens genereren namens *elke* gebruiker in de service. Aan het minimum moet deze rol worden toegewezen aan de beheerder van de service. De volgende Power shell-opdracht laat zien hoe u de rol kunt toewijzen aan een gebruiker `user1` op het laagste bereik om te voor komen dat er overbodige machtigingen aan de gebruiker worden verleend: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nadat de machtigingen zijn verleend aan een gebruiker, moet de gebruiker zich afmelden en opnieuw aanmelden bij de Azure Portal om de nieuwe machtigingen van kracht te laten worden.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Ik zie de `Unable to start the portal. See if settings are specified correctly (...)` fout

Deze fout wordt weer gegeven wanneer een `GET` aanroep `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` mislukt. De aanroep wordt uitgegeven vanuit de browser door de beheer interface van de portal.

Als uw API Management-service zich in een VNet bevindt, raadpleegt u de bovenstaande VNet-connectiviteit.

De aanroep fout kan ook worden veroorzaakt door een TLS/SSL-certificaat, dat wordt toegewezen aan een aangepast domein en niet wordt vertrouwd door de browser. Als oplossing kunt u het aangepaste domein van het beheer eindpunt verwijderen-API Management het standaard eindpunt met een vertrouwd certificaat terugvallen.

### <a name="whats-the-browser-support-for-the-portal"></a>Wat is de browser ondersteuning voor de portal?

| Browser                     | Ondersteund       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> wordt ondersteund in de twee nieuwste productie versies.</small>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaars portal:

- [De beheerde ontwikkelaars Portal openen en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf-hostende versie van de Portal instellen][2]
- [Uw eigen widget implementeren][3]

Bladeren door andere resources:

- [GitHub-opslag plaats met de bron code][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
