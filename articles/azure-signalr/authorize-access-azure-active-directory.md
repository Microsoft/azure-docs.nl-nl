---
title: Toegang autoriseren met behulp van Azure Active Directory
description: In dit artikel vindt u informatie over het verlenen van toegang tot Azure signalerings service bronnen met behulp van Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797514"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Toegang verlenen tot Azure signalerings service-resources met behulp van Azure Active Directory
De Azure signalerings service ondersteunt het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor Azure signalerings service bronnen. Met Azure AD kunt u gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) om machtigingen toe te kennen aan een beveiligingsprincipal, die een gebruiker of een service-principal van de toepassing is. Zie [informatie over de verschillende rollen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een toepassing) probeert toegang te krijgen tot een Azure signalerings service resource, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. De resource naam voor het aanvragen van een token is `https://signalr.azure.com/` .
 2. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de Azure signalerings service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime. Als uw hub-server wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure function-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources. Zie [toegang verifiëren voor Azure signalerings service resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](authenticate-managed-identity.md)voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden ingediend bij de Azure signalerings service. 

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. De Azure signalerings service biedt RBAC-rollen die sets machtigingen voor Azure signalerings bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie voor meer informatie over RBAC-rollen [Azure ingebouwde rollen voor Azure signalerings service](#azure-built-in-roles-for-azure-signalr-service). 

De seingevings hub-server die niet wordt uitgevoerd binnen een Azure-entiteit kan ook worden geautoriseerd met Azure AD. Zie toegang tot de [Azure signalerings service met Azure AD verifiëren vanuit een toepassing](authenticate-application.md)voor meer informatie over het aanvragen van een toegangs token en het gebruik ervan om aanvragen voor Azure signalerings service bronnen te autoriseren. 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Ingebouwde rollen van Azure voor de Azure signalerings service

- [Signalr app server]
- [Signalr service Reader]
- Eigenaar van de seingevings Service]

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md). De Azure signalerings service definieert een set van ingebouwde Azure-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot de Azure signalerings service. u kunt ook aangepaste rollen definiëren voor toegang tot de resource.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden toegepast op het niveau van het abonnement, de resource groep of een service resource van Azure signalering. Een beveiligings-principal van Azure AD kan een gebruiker of een toepassing zijn, of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Ingebouwde rollen voor de Azure signalerings service
Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot de Azure signalerings service resource met behulp van Azure AD en OAuth:

### <a name="signalr-app-server"></a>Seingevings app-server

Gebruik deze rol om de toegang te geven tot het ophalen van een tijdelijke toegangs sleutel voor het ondertekenen van client tokens.

### <a name="signalr-serverless-contributor"></a>Inzender server zonder signaal

Gebruik deze rol om de toegang te geven tot het rechtstreeks ophalen van een client token van de Azure signalerings service.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende verwante artikelen:

- [Een toepassing met Azure AD verifiëren om toegang te krijgen tot de Azure signalerings service](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure AD om toegang te krijgen tot de Azure signalerings service](authenticate-managed-identity.md)