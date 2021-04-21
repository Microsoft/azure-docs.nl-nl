---
title: Veelgestelde API Management Azure | Microsoft Docs
description: Meer informatie over de antwoorden op veelgestelde vragen (FAQ), patronen en best practices in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b6a138317d0cc2e10e893d1969f9d5452064d8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813736"
---
# <a name="azure-api-management-faqs"></a>Veelgestelde API Management Azure-gegevens
Krijg antwoorden op veelvoorkomende vragen, patronen en best practices voor Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
* [Wat betekent het wanneer een functie in preview is?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [How do I copy my API Management service instance to a new instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance) (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
* [Kan ik mijn API Management programmatisch beheren?](#can-i-manage-my-api-management-instance-programmatically)
* [Hoe voeg ik een gebruiker toe aan de groep Beheerders?](#how-do-i-add-a-user-to-the-administrators-group)
* [Waarom is het beleid dat ik wil toevoegen niet beschikbaar in de beleidseditor?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hoe kan ik meerdere omgevingen in één API instellen?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan ik SOAP gebruiken met API Management?](#can-i-use-soap-with-api-management)
* [Kan ik een OAuth 2.0-autorisatieserver configureren met AD FS beveiliging?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Welke routeringsmethode API Management gebruikt in implementaties naar meerdere geografische locaties?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan ik een Azure Resource Manager gebruiken om een API Management maken?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan ik een zelf-ondertekend TLS/SSL-certificaat gebruiken voor een back-end?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Waarom krijg ik een verificatiefout wanneer ik een GIT-opslagplaats wil klonen?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Werkt API Management met Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Waarom hebben we een toegewezen subnet in Resource Manager-stijl VNET's nodig wanneer API Management in deze VNET's wordt geïmplementeerd?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Wat is de minimale subnetgrootte die nodig is bij het implementeren API Management in een VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Can I move an API Management service from one subscription to another?](#can-i-move-an-api-management-service-from-one-subscription-to-another) (Kan ik een API Management-service overzetten naar een ander abonnement?)
* [Zijn er beperkingen voor of bekende problemen met het importeren van mijn API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Wat betekent het wanneer een functie in preview is?
Wanneer een functie in preview is, betekent dit dat we actief op zoek zijn naar feedback over hoe de functie voor u werkt. Een functie in de preview-versie is functioneel voltooid, maar het is mogelijk dat we een belangrijke wijziging zullen maken in reactie op feedback van klanten. U wordt aangeraden niet afhankelijk te zijn van een functie die in preview is in uw productieomgeving.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?
U hebt verschillende opties om de verbinding tussen de API Management-gateway en uw back-endservices te beveiligen. U kunt:

* Http-basisverificatie gebruiken. Zie Uw eerste API importeren [en publiceren voor meer informatie.](import-and-publish.md)
* Gebruik wederzijdse TLS-verificatie zoals beschreven in [How to secure back-end services by using client certificate authentication in Azure API Management](api-management-howto-mutual-certificates.md).
* Gebruik IP-filtering voor uw back-endservice. In alle lagen van API Management met uitzondering van de verbruikslaag, blijft het IP-adres van de gateway constant, met enkele waarschuwingen die worden beschreven in het [artikel over IP-documentatie.](api-management-howto-ip-addresses.md)
* Verbind uw API Management-exemplaar met een Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>How do I copy my API Management service instance to a new instance? (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
U hebt verschillende opties als u een exemplaar van API Management wilt kopiëren naar een nieuw exemplaar. U kunt:

* Gebruik de functie back-up en herstel in API Management. Zie How to implement disaster recovery by using service backup and restore in Azure API Management (Herstel na noodherstel implementeren met [behulp van serviceback-up en -herstel in Azure API Management) voor meer API Management.](api-management-howto-disaster-recovery-backup-restore.md)
* Maak uw eigen back-up- en herstelfunctie met behulp van [API Management REST API](/rest/api/apimanagement/). Gebruik de REST API om de entiteiten op te slaan en te herstellen van het service-exemplaar dat u wilt.
* Download de serviceconfiguratie met behulp van Git en upload deze vervolgens naar een nieuw exemplaar. Zie How to save and configure your API Management service configuration by using Git (Configuratie van uw API Management service opslaan en [configureren met behulp van Git) voor meer informatie.](api-management-configuration-repository-git.md)

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan ik mijn API Management programmatisch beheren?
Ja, u kunt uw API Management beheren met behulp van:

* De [API Management REST API](/rest/api/apimanagement/).
* De [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* De [PowerShell-cmdlets](/powershell/module/wds) [voor service-implementatie](/powershell/azure/servicemanagement/overview) en servicebeheer.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hoe voeg ik een gebruiker toe aan de groep Beheerders?
Beheerdersgroepen is een onveranderbare systeemgroep. Azure-abonnementsbeheerders zijn lid van deze groep. U kunt geen gebruiker toevoegen aan deze groep. Zie [Groepen maken en gebruiken voor het beheren van ontwikkelaarsaccounts in Azure API Management](./api-management-howto-create-groups.md) voor meer informatie.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Waarom is het beleid dat ik wil toevoegen niet beschikbaar in de beleidseditor?
Als het beleid dat u wilt toevoegen grijs of gearceerd wordt weergegeven in de beleidseditor, moet u ervoor zorgen dat u het juiste bereik voor het beleid hebt. Elke beleidsverklaring is ontworpen voor gebruik in specifieke scopes en beleidssecties. Als u de beleidssecties en -scopes voor een beleid wilt bekijken, gaat u naar de sectie Gebruik van het beleid in [API Management beleidsregels.](./api-management-policies.md)

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hoe kan ik meerdere omgevingen in één API instellen?
Als u meerdere omgevingen wilt instellen, bijvoorbeeld een testomgeving en een productieomgeving, in één API, hebt u twee opties. U kunt:

* Verschillende API's hosten op dezelfde tenant.
* Dezelfde API's hosten op verschillende tenants.

### <a name="can-i-use-soap-with-api-management"></a>Kan ik SOAP gebruiken met API Management?
[Soap Pass-Through-ondersteuning](https://azure.microsoft.com/blog/soap-pass-through/) is nu beschikbaar. Beheerders kunnen de WSDL van hun SOAP-service importeren en Azure API Management maakt een SOAP-front-end. Documentatie voor ontwikkelaarsportal, testconsole, beleidsregels en analyses zijn allemaal beschikbaar voor SOAP-services.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan ik een OAuth 2.0-autorisatieserver configureren met AD FS beveiliging?
Zie Using [ADFS in API Management (ADFS](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)gebruiken in API Management) voor meer informatie over het configureren van een OAuth 2.0-autorisatieserver met Active Directory Federation Services-beveiliging (AD FS).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Welke routeringsmethode API Management gebruikt in implementaties naar meerdere geografische locaties?
API Management maakt gebruik van [de verkeersrouteringsmethode voor prestaties](../traffic-manager/traffic-manager-routing-methods.md#performance) in implementaties naar meerdere geografische locaties. Binnenkomend verkeer wordt gerouteerd naar de dichtstbijzijnde API-gateway. Als één regio offline gaat, wordt binnenkomend verkeer automatisch doorgeleid naar de dichtstbijzijnde gateway. Meer informatie over routeringsmethoden in [Traffic Manager routeringsmethoden](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan ik een Azure Resource Manager gebruiken om een service-API Management maken?
Ja. Zie de [quickstartsjablonen voor Azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan ik een zelf-ondertekend TLS/SSL-certificaat gebruiken voor een back-end?
Ja. Dit kan worden gedaan via PowerShell of door rechtstreeks naar de API te verzenden. Hierdoor wordt validatie van de certificaatketen uitgeschakeld en kunt u zelf-ondertekende of persoonlijk ondertekende certificaten gebruiken bij het communiceren van API Management naar de back-endservices.

#### <a name="powershell-method"></a>PowerShell-methode ####
Gebruik de [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) PowerShell-cmdlets (voor nieuwe back-end) of (voor bestaande back-end) en stel de `-SkipCertificateChainValidation` parameter in op `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Directe API-updatemethode ####
1. Maak een [back-end-entiteit](/rest/api/apimanagement/) met behulp van API Management.     
2. Stel de **eigenschap skipCertificateChainValidation** in op **true**.     
3. Als u niet langer zelf-ondertekende certificaten wilt toestaan, verwijdert u de entiteit Backend of stelt u de eigenschap **skipCertificateChainValidation** in op **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Waarom krijg ik een verificatiefout wanneer ik probeer om een Git-opslagplaats te klonen?
Als u Git Aanmeldingsgegevensbeheer gebruikt of als u een Git-opslagplaats probeert te klonen met behulp van Visual Studio, kan er een bekend probleem zijn met het dialoogvenster Windows-referenties. In het dialoogvenster wordt de wachtwoordlengte beperkt tot 127 tekens en wordt het door Microsoft gegenereerde wachtwoord afgekapt. We werken aan het inkorten van het wachtwoord. Gebruik voor nu Git Bash om uw Git-opslagplaats te klonen.

### <a name="does-api-management-work-with-azure-expressroute"></a>Werkt API Management met Azure ExpressRoute?
Ja. API Management werkt met Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Waarom hebben we een toegewezen subnet in Resource Manager-stijl VNET's nodig wanneer API Management in deze VNET's wordt geïmplementeerd?
De toegewezen subnetvereiste voor API Management is het feit dat het is gebouwd op het klassieke implementatiemodel (PAAS V1-laag). Hoewel we kunnen implementeren in een Resource Manager VNET (V2-laag), heeft dat gevolgen. Het klassieke implementatiemodel in Azure is niet nauw gekoppeld aan het Resource Manager-model. Dus als u een resource in de V2-laag maakt, weet de V1-laag dit niet en kunnen er problemen ontstaan, zoals API Management probeert een IP te gebruiken die al is toegewezen aan een NIC (gebouwd op V2).
Zie verschil in implementatiemodellen voor meer Resource Manager over het verschil [tussen](../azure-resource-manager/management/deployment-models.md)klassieke en Resource Manager in Azure.

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Wat is de minimale subnetgrootte die nodig is bij het implementeren API Management in een VNET?
De minimale subnetgrootte die nodig is API Management implementatie is [/29.](../virtual-network/virtual-networks-faq.md#configuration)Dit is de minimale subnetgrootte die door Azure wordt ondersteund.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Can I move an API Management service from one subscription to another? (Kan ik een API Management-service overzetten naar een ander abonnement?)
Ja. Zie Resources verplaatsen naar een nieuwe resourcegroep of een [nieuw abonnement voor meer informatie.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Zijn er beperkingen voor of bekende problemen met het importeren van mijn API?
[Bekende problemen en beperkingen voor](api-management-api-import-restrictions.md) Open API(Swagger), WSDL- en WADL-indelingen.
