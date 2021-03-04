---
title: Service-principals beveiligen in Azure Active Directory
description: Zoek, evalueer en beveilig service-principals.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fff9f9e809c61761ae22bc64cb0810b6e8b98f07
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122694"
---
# <a name="securing-service-principals"></a>Service-principals beveiligen

Een [Service-Principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) voor Azure Active Directory (Azure AD) is de lokale weer gave van een toepassings object in één Tenant of directory.  De functie fungeert als de identiteit van het toepassings exemplaar. Met Service-principals definieert u wie toegang heeft tot de toepassing en welke bronnen de toepassing kan openen. Een service-principal wordt gemaakt in elke Tenant waar de toepassing wordt gebruikt en verwijst naar het wereld wijde unieke toepassings object. De Tenant beveiligt de aanmelding van de Service-Principal en de toegang tot resources.  

### <a name="tenant-service-principal-relationships"></a>Pachter-Service-Principal-relaties
Een toepassing met één Tenant heeft slechts één service-principal in de thuis Tenant. Voor een webtoepassing met meerdere tenants of een API is een Service-Principal vereist in elke Tenant. Een service-principal wordt gemaakt wanneer een gebruiker van die Tenant toestemming heeft gegeven voor het gebruik van de toepassing of de API. Deze toestemming maakt een een-op-veel-relatie tussen de multi tenant-toepassing en de bijbehorende service-principals.

Een toepassing met meerdere tenants bevindt zich in één Tenant en is ontworpen om exemplaren te hebben in andere tenants. De meeste SaaS-toepassingen (Software-as-a-Service) zijn ontworpen voor multitenancy. Gebruik service-principals om ervoor te zorgen dat de juiste beveiligings-postuur voor de toepassing en de gebruikers in zowel individuele tenants als multi tenant-use cases.

## <a name="applicationid-and-objectid"></a>ApplicationID en ObjectID

Een gegeven toepassings exemplaar heeft twee verschillende eigenschappen: de ApplicationID (ook wel ClientID genoemd) en de ObjectID.

> [!NOTE] 
> Het is mogelijk dat de termen toepassing en Service-Principal door elkaar worden gebruikt wanneer u snel naar een toepassing verwijst in de context van met verificatie gerelateerde taken. Ze zijn echter twee verschillende representaties van toepassingen in azure AD.
 

De ApplicationID vertegenwoordigt de globale toepassing en is hetzelfde voor alle toepassings exemplaren tussen tenants. De ObjectID is een unieke waarde voor een toepassings object en vertegenwoordigt de Service-Principal. Net als bij gebruikers, groepen en andere resources, kan de ObjectID een exemplaar van een toepassing in azure AD identificeren.

Zie [toepassing en Service-Principal Relationship](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)(Engelstalig) voor meer informatie over dit onderwerp.

U kunt ook een toepassing en de bijbehorende service principal object (ObjectID) in een Tenant maken met behulp van Azure PowerShell, Azure CLI, Microsoft Graph, de Azure Portal en andere hulpprogram ma's. 

![Scherm opname met een nieuwe toepassings registratie, met de velden toepassings-ID en object-ID gemarkeerd.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Verificatie van service-principal

Er zijn twee mechanismen voor verificatie met Service-principals: client certificaten en client geheimen. 

![ Scherm opname van de nieuwe app-pagina met de certificaten en client geheimen-gebieden gemarkeerd.](./media/securing-service-accounts/secure-principal-certificates.png)

Certificaten zijn veiliger: gebruik client certificaten, indien mogelijk. In tegens telling tot client geheimen kunnen client certificaten niet per ongeluk in code worden inge sloten. Gebruik Azure Key Vault voor certificaat-en geheimen beheer wanneer dit mogelijk is om de volgende assets te versleutelen met behulp van sleutels die worden beveiligd door Hardware Security modules:

* verificatie sleutels

* sleutels voor het opslag account

* sleutels voor gegevens versleuteling

* pfx-bestanden

* appwachtwoorden 

Voor meer informatie over Azure Key Vault en hoe u deze kunt gebruiken voor certificaat-en geheim beheer raadpleegt u [over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) en [wijst u een Key Vault toegangs beleid toe met behulp van de Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal). 

 ### <a name="challenges-and-mitigations"></a>Uitdagingen en oplossingen
De volgende tabel bevat oplossingen voor problemen die kunnen optreden bij het gebruik van service-principals.


| Uitdagingen| Oplossingen |
| - | - |
| Toegangs Beoordelingen voor service-principals die zijn toegewezen aan geprivilegieerde rollen.| Deze functionaliteit is beschikbaar als preview-versie en is nog niet algemeen beschikbaar. |
| Controleert de toegang van service-principals| Hand matige controle van de toegangs beheer lijst van de resource met behulp van de Azure Portal. |
| Via met toestemming geplaatste service-principals| Wanneer u Automation-Service accounts of service-principals maakt, geeft u alleen de machtigingen op die vereist zijn voor de taak. Evalueer bestaande service-principals om te zien of u de bevoegdheden kunt beperken. |
|Wijzigingen aanbrengen in de referenties of verificatie methoden van de service-principals |Gebruik de werkmap van het rapport gevoelige bewerkingen, waarmee u dit probleem kunt oplossen. [Zie de uitleg in dit blog bericht](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Accounts zoeken met Service-principals
Voer de volgende opdrachten uit om accounts te vinden met Service-principals.

Azure CLI gebruiken


`az ad sp list`

PowerShell gebruiken

`Get-AzureADServicePrincipal -All:$true` 


Zie [Get-azureadserviceprincipal namelijk niet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) voor meer informatie

## <a name="assess-service-principal-security"></a>Service-Principal-beveiliging beoordelen

Als u de beveiliging van de service-principals wilt beoordelen, moet u de bevoegdheden en de referentie opslag evalueren.

Beperk mogelijke uitdagingen met behulp van de volgende gegevens.
|Uitdagingen | Oplossingen|
| - | - |
| De gebruiker detecteren die is gezonden naar een multi tenant-app en illegale toestemming verleent aan een multi tenant-app | Voer de volgende Power shell uit om multi tenant-apps te zoeken.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Toestemming van de gebruiker uitschakelen. <br>Toestemming van de gebruiker van geverifieerde uitgevers toestaan voor geselecteerde machtigingen (aanbevolen) <br> Gebruik voorwaardelijke toegang om service-principals van niet-vertrouwde locaties te blok keren. Configureer deze onder de gebruikers context en hun tokens moeten worden gebruikt om de service-principal te activeren.|
|Gebruik van een in code vastgelegd gedeeld geheim in een script met behulp van een service-principal.|Gebruik een certificaat of Azure Key Vault.|
|Bijhouden wie het certificaat of het geheim gebruikt| Bewaak de aanmeldingen van de service-principal met behulp van de aanmeldings logboeken van Azure AD.|
Kan aanmelden met een Service-Principal niet beheren met voorwaardelijke toegang.| De aanmeldingen bewaken met behulp van de logboeken van Azure AD-aanmelding
| De standaard Azure RBAC-rol is Inzender. |Evalueer de behoeften en pas de rol toe met de mini maal mogelijke machtigingen om te voldoen aan die behoefte.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Van een gebruikers account naar een Service-Principal verplaatsen  
Als u een Azure-gebruikers account als Service-Principal gebruikt, evalueer dan of u kunt overstappen naar een [beheerde identiteit](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) of een service-principal. Als u geen beheerde identiteit kunt gebruiken, moet u een Service-Principal inrichten die net voldoende machtigingen en bereik heeft om de vereiste taken uit te voeren. U kunt een service-principal maken door [een toepassing te registreren](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)of met [Power shell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell).

Wanneer u Microsoft Graph gebruikt, raadpleegt u de documentatie van de specifieke API, [zoals in dit voor beeld](/powershell/azure/create-azure-service-principal-azureps), en zorgt u ervoor dat het machtigings type voor de toepassing wordt weer gegeven als ondersteund.

## <a name="next-steps"></a>Volgende stappen

**Meer informatie over service-principals:**

[Een service-principal maken](../develop/howto-create-service-principal-portal.md)

 [Service-Principal-aanmeldingen bewaken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#sign-ins-report)

**Meer informatie over het beveiligen van service accounts:**

[Inleiding tot Azure-service accounts](service-accounts-introduction-azure.md)

[Beheerde identiteiten beveiligen](service-accounts-managed-identities.md)

[Azure-service accounts beheren](service-accounts-governing-azure.md)

[Inleiding tot on-premises service accounts](service-accounts-on-premises.md)
