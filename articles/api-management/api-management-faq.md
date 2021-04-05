---
title: Veelgestelde vragen over Azure API Management | Microsoft Docs
description: Meer informatie over de antwoorden op veelgestelde vragen (FAQ), patronen en aanbevolen procedures in azure API Management.
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
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94697982"
---
# <a name="azure-api-management-faqs"></a>Veelgestelde vragen over Azure API Management
Krijg antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
* [Wat betekent dit wanneer een functie in preview is?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [How do I copy my API Management service instance to a new instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance) (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
* [Kan ik mijn API Management-exemplaar programmatisch beheren?](#can-i-manage-my-api-management-instance-programmatically)
* [Hoe voeg ik een gebruiker toe aan de groep Beheerders?](#how-do-i-add-a-user-to-the-administrators-group)
* [Waarom is het beleid dat ik wil toevoegen, niet beschikbaar in de beleids editor?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hoe kan ik meerdere omgevingen in één API in te stellen?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan ik SOAP gebruiken met API Management?](#can-i-use-soap-with-api-management)
* [Kan ik een OAuth 2,0-autorisatie server met AD FS beveiliging configureren?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Welke routerings methode gebruikt API Management in implementaties naar meerdere geografische locaties?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan ik een Azure Resource Manager sjabloon gebruiken om een API Management service-exemplaar te maken?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan ik een zelfondertekend TLS/SSL-certificaat gebruiken voor een back-end?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Waarom krijg ik een verificatie fout wanneer ik een GIT-opslag plaats probeer te klonen?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Werkt API Management met Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Waarom is een toegewezen subnet in Resource Manager-stijl VNETs vereist wanneer API Management in deze wordt geïmplementeerd?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Wat is de minimale grootte van het subnet dat nodig is voor het implementeren van API Management in een VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Can I move an API Management service from one subscription to another?](#can-i-move-an-api-management-service-from-one-subscription-to-another) (Kan ik een API Management-service overzetten naar een ander abonnement?)
* [Zijn er beperkingen voor of bekende problemen met het importeren van mijn API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Wat betekent dit wanneer een functie in preview is?
Wanneer een functie in preview is, betekent dit dat er actief feedback wordt gegeven over de manier waarop de functie werkt. Een functie in preview is functioneel voltooid, maar het is mogelijk dat we een belang rijke wijziging aanbrengen in reactie op feedback van klanten. Het is raadzaam dat u niet afhankelijk bent van een functie die in uw productie omgeving wordt weer gegeven.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?
U hebt verschillende opties om de verbinding tussen de API Management Gateway en uw back-end-services te beveiligen. U kunt:

* Gebruik HTTP-basis verificatie. Zie [uw eerste API importeren en publiceren](import-and-publish.md)voor meer informatie.
* Gebruik verificatie op basis van TLS, zoals beschreven in [How to Secure back-end services met behulp van client certificaat authenticatie in Azure API Management](api-management-howto-mutual-certificates.md).
* Gebruik IP-filtering voor uw back-end-service. In alle lagen van API Management, met uitzonde ring van de laag verbruik, blijft het IP-adres van de gateway constant, met enkele voor behoud die worden beschreven in [het artikel over de IP-documentatie](api-management-howto-ip-addresses.md).
* Verbind uw API Management-exemplaar met een Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>How do I copy my API Management service instance to a new instance? (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
U hebt verschillende opties als u een API Management exemplaar wilt kopiëren naar een nieuw exemplaar. U kunt:

* Gebruik de functie back-up en herstellen in API Management. Zie herstel [na nood gevallen implementeren met behulp van service back-up en herstellen in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)voor meer informatie.
* Maak uw eigen functie voor back-up en herstel met behulp van de [API Management rest API](/rest/api/apimanagement/). Gebruik de REST API om de entiteiten van het gewenste service-exemplaar op te slaan en te herstellen.
* Down load de service configuratie met behulp van Git en upload deze naar een nieuw exemplaar. Zie [de configuratie van uw API Management-service opslaan en configureren met behulp van Git](api-management-configuration-repository-git.md)voor meer informatie.

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan ik mijn API Management-exemplaar programmatisch beheren?
Ja, u kunt API Management programmatisch beheren met behulp van:

* Het [API Management rest API](/rest/api/apimanagement/).
* De [SDK voor de ApiManagement-Service beheer bibliotheek van Microsoft Azure](https://aka.ms/apimsdk).
* De [service-implementatie](/powershell/module/wds) -en [Service Management](/powershell/azure/servicemanagement/overview) Power shell-cmdlets.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hoe voeg ik een gebruiker toe aan de groep Beheerders?
Beheerders groepen is een onveranderbare systeem groep. Beheerders van Azure-abonnementen zijn lid van deze groep. U kunt geen gebruiker toevoegen aan deze groep. Zie [groepen maken en gebruiken voor het beheren van ontwikkelaars accounts in Azure API Management](./api-management-howto-create-groups.md) voor meer informatie.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Waarom is het beleid dat ik wil toevoegen, niet beschikbaar in de beleids editor?
Als het beleid dat u wilt toevoegen lichter gekleurd of grijs wordt weer gegeven in de beleids editor, moet u ervoor zorgen dat u het juiste bereik voor het beleid hebt. Elke beleids instructie is ontworpen voor gebruik in specifieke bereiken en beleids secties. Zie de sectie gebruik van het beleid in [API management-beleid](./api-management-policies.md)voor het controleren van de beleids secties en-bereiken voor een beleid.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hoe kan ik meerdere omgevingen in één API in te stellen?
Als u meerdere omgevingen, bijvoorbeeld een test omgeving en een productie omgeving, in één API wilt instellen, hebt u twee opties. U kunt:

* Host verschillende Api's op dezelfde Tenant.
* Host dezelfde Api's op verschillende tenants.

### <a name="can-i-use-soap-with-api-management"></a>Kan ik SOAP gebruiken met API Management?
[SOAP Pass-Through-](https://azure.microsoft.com/blog/soap-pass-through/) ondersteuning is nu beschikbaar. Beheerders kunnen de WSDL van de SOAP-service importeren en Azure API Management maakt een SOAP-front-end. Documentatie voor de ontwikkelaars Portal, de test console, het beleid en de analyse zijn allemaal beschikbaar voor SOAP-services.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan ik een OAuth 2,0-autorisatie server met AD FS beveiliging configureren?
Zie [ADFS gebruiken in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)voor meer informatie over het configureren van een OAuth 2,0-autorisatie server met Active Directory Federation Services (AD FS) beveiliging.

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Welke routerings methode gebruikt API Management in implementaties naar meerdere geografische locaties?
API Management gebruikt de [routerings methode voor prestatie verkeer](../traffic-manager/traffic-manager-routing-methods.md#performance) in implementaties naar meerdere geografische locaties. Binnenkomend verkeer wordt doorgestuurd naar de dichtstbijzijnde API-gateway. Als één regio offline gaat, wordt binnenkomend verkeer automatisch doorgestuurd naar de dichtstbijzijnde gateway. Meer informatie over routerings methoden vindt u in [Traffic Manager routerings methoden](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan ik een Azure Resource Manager sjabloon gebruiken om een API Management service-exemplaar te maken?
Ja. Bekijk de Quick Start-sjablonen voor [Azure API Management service](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan ik een zelfondertekend TLS/SSL-certificaat gebruiken voor een back-end?
Ja. Dit kan worden gedaan via Power shell of door rechtstreeks naar de API te verzenden. Hiermee wordt validatie van de certificaat keten uitgeschakeld en kunt u zelfondertekende of privé-ondertekende certificaten gebruiken bij de communicatie van API Management naar de back-end-services.

#### <a name="powershell-method"></a>Power shell-methode ####
Gebruik de [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (voor nieuwe back-end) of [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (voor bestaande back-end) Power shell-cmdlets en stel de `-SkipCertificateChainValidation` para meter in op `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Direct API-update methode ####
1. Maak een [back-end](/rest/api/apimanagement/) -entiteit met behulp van API management.     
2. Stel de eigenschap **skipCertificateChainValidation** in op **waar**.     
3. Als u geen zelfondertekende certificaten meer wilt toestaan, verwijdert u de back-end-entiteit of stelt u de eigenschap **skipCertificateChainValidation** in op **False**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Waarom krijg ik een verificatiefout wanneer ik probeer om een Git-opslagplaats te klonen?
Als u Git Credential Manager gebruikt, of als u een Git-opslag plaats probeert te klonen met behulp van Visual Studio, kunt u een bekend probleem ondervindt in het dialoog venster Windows-referenties. In het dialoog venster wordt de wachtwoord lengte beperkt tot 127 tekens en wordt het door micro soft gegenereerde wacht woord afgekapt. Er wordt gewerkt aan het verkorten van het wacht woord. Gebruik voor Taan Git bash om uw Git-opslag plaats te klonen.

### <a name="does-api-management-work-with-azure-expressroute"></a>Werkt API Management met Azure ExpressRoute?
Ja. API Management werkt met Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Waarom is een toegewezen subnet in Resource Manager-stijl VNETs vereist wanneer API Management in deze wordt geïmplementeerd?
De toegewezen subnet vereiste voor API Management is afkomstig uit het feit dat het is gebouwd op een klassiek (PAAS v1 Layer)-implementatie model. Hoewel we kunnen implementeren in een resource manager VNET (v2-laag), zijn er consequenties. Het klassieke implementatie model in Azure is niet nauw verbonden met het Resource Manager-model, dus als u een resource in v2-laag maakt, is de V1-laag niet bekend en er kunnen problemen optreden, zoals API Management een IP-adres wilt gebruiken dat al is toegewezen aan een NIC (gebouwd op v2).
Zie voor meer informatie over het verschil in het klassieke en het Resource Manager-model in azure het [verschil in implementatie modellen](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Wat is de minimale grootte van het subnet dat nodig is voor het implementeren van API Management in een VNET?
De minimale grootte van het subnet dat nodig is voor de implementatie van API Management is [/29](../virtual-network/virtual-networks-faq.md#configuration), de minimale grootte van het subnet die door Azure wordt ondersteund.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Can I move an API Management service from one subscription to another? (Kan ik een API Management-service overzetten naar een ander abonnement?)
Ja. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie.

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Zijn er beperkingen voor of bekende problemen met het importeren van mijn API?
[Bekende problemen en beperkingen](api-management-api-import-restrictions.md) voor de indelingen open API (Swagger), WSDL en WADL.
