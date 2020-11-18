---
title: PaaS Web & Mobile-toepassingen beveiligen
titleSuffix: Azure App Service
description: 'Meer informatie over Azure App Service aanbevolen procedures voor beveiliging bij het beveiligen van uw PaaS-web-en mobiele toepassingen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701011"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Aanbevolen procedures voor het beveiligen van PaaS web-en mobiele toepassingen met Azure App Service

In dit artikel bespreken we een verzameling [Azure app service](../../app-service/overview.md) aanbevolen procedures voor beveiliging voor het beveiligen van uw PaaS-web-en mobiele toepassingen. Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten, zoals uzelf.

Azure App Service is een PaaS-aanbieding (platform-as-a-Service) waarmee u web-en mobiele apps voor elk platform of apparaat kunt maken en overal en op locatie verbinding kunt maken met gegevens. App Service bevat de web-en mobiele mogelijkheden die eerder afzonderlijk zijn geleverd als Azure-websites en Azure Mobile Services. Deze service bevat ook nieuwe mogelijkheden voor het automatiseren van bedrijfsprocessen en het hosten van cloud-API's. Als één geïntegreerde service biedt App Service een uitgebreide set mogelijkheden voor web-, mobiele en integratie scenario's.

## <a name="authenticate-through-azure-active-directory-ad"></a>Verifiëren via Azure Active Directory (AD)
App Service biedt een OAuth 2,0-Service voor uw ID-provider. OAuth 2,0 richt zich op de vereenvoudiging van client ontwikkelaars en biedt specifieke autorisatie stromen voor webtoepassingen, desktop toepassingen en mobiele telefoons. Azure AD maakt gebruik van OAuth 2,0 zodat u toegang tot mobiele en webtoepassingen kunt autoriseren. Zie [verificatie en autorisatie in azure app service](../../app-service/overview-authentication-authorization.md)voor meer informatie.

## <a name="restrict-access-based-on-role"></a>Toegang beperken op basis van rol
Het beperken van de toegang is van cruciaal belang voor organisaties die beveiligings beleid voor gegevens toegang willen afdwingen. U kunt Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik, zoals de beveiligings principes van de nood zaak om te kennen en te voldoen aan bevoegdheden. Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md)voor meer informatie over het verlenen van gebruikers toegang tot toepassingen.

## <a name="protect-your-keys"></a>Uw sleutels beveiligen
Het maakt niet uit hoe goed uw beveiliging is als u uw abonnements sleutels kwijtraakt. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met Key Vault kunt u sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd door Hardware Security modules (Hsm's). Voor extra zekerheid kunt u de sleutels importeren of genereren in HSM's. U kunt Key Vault ook gebruiken om uw TLS-certificaten te beheren met automatische verlenging. Zie [Wat is Azure Key Vault](../../key-vault/general/overview.md) voor meer informatie.

## <a name="restrict-incoming-source-ip-addresses"></a>IP-adressen van binnenkomende bronnen beperken
[App service omgevingen](../../app-service/environment/intro.md) hebben een functie voor integratie van virtuele netwerken waarmee u de inkomende bron-IP-adressen kunt beperken via netwerk beveiligings groepen (nsg's). Als u niet bekend bent met Azure Virtual Networks (VNETs), is dit een mogelijkheid waarmee u veel van uw Azure-resources kunt plaatsen in een niet-Internet-Routeer netwerk waarmee u de toegang tot beheert. Zie [uw app integreren met een Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)voor meer informatie.

Voor App Service in Windows kunt u IP-adressen ook dynamisch beperken door de web.config te configureren. Zie [dynamische IP-beveiliging](/iis/configuration/system.webServer/security/dynamicIpSecurity/)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een verzameling van App Service aanbevolen beveiligings procedures geïntroduceerd voor het beveiligen van uw PaaS-webtoepassingen en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-databases beveiligen in Azure](paas-applications-using-sql.md)