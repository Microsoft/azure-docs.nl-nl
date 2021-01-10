---
title: Verificatie en autorisatie Azure Service Bus | Microsoft Docs
description: Verifieer apps om te Service Bus met de verificatie van Shared Access Signature (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7b287b209fbcd5bc2782505095aeae4390107803
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060211"
---
# <a name="service-bus-authentication-and-authorization"></a>Vereenvoudigde Service Bus-verificatie en -autorisatie
Er zijn twee manieren om toegang tot Azure Service Bus-resources te verifiëren en te autoriseren: Azure activity Directory (Azure AD) en Shared Access signatures (SAS). In dit artikel vindt u informatie over het gebruik van deze twee typen beveiligings mechanismen. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD-integratie voor Service Bus resources biedt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) voor een nauw keurige controle over de toegang van een client tot resources. U kunt Azure RBAC gebruiken om machtigingen toe te kennen aan een beveiligingsprincipal, die een gebruiker, een groep of een Application Service-Principal kan zijn. De beveiligingsprincipal wordt door Azure AD geverifieerd om een OAuth 2,0-token te retour neren. Het token kan worden gebruikt voor het autoriseren van een aanvraag voor toegang tot een Service Bus resource (wachtrij, onderwerp, enz.).

Raadpleeg de volgende artikelen voor meer informatie over verificatie met Azure AD:

- [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verifiëren vanuit een toepassing](authenticate-application.md)

> [!NOTE]
> [Service Bus rest API](/rest/api/servicebus/) ondersteunt OAuth-verificatie met Azure AD.

> [!IMPORTANT]
> Het autoriseren van gebruikers of toepassingen die gebruikmaken van het OAuth 2,0-token dat wordt geretourneerd door Azure AD, biedt een superieure beveiliging en gebruiks vriendelijk gebruik van Shared Access signatures (SAS). Met Azure AD hoeft u geen tokens op te slaan in uw code en mogelijke beveiligings problemen met Risico's. U wordt aangeraden Azure AD zo mogelijk te gebruiken met uw Azure Service Bus toepassingen. 

## <a name="shared-access-signature"></a>Shared Access Signature
Met [SAS-verificatie](service-bus-sas.md) kunt u een gebruiker toegang verlenen tot Service Bus resources, met specifieke rechten. SAS-verificatie in Service Bus omvat de configuratie van een cryptografische sleutel met bijbehorende rechten op een Service Bus bron. Clients kunnen vervolgens toegang krijgen tot deze bron door een SAS-token te presen teren, dat bestaat uit de bron-URI waartoe toegang wordt verkregen en een verloop datum dat met de geconfigureerde sleutel is ondertekend.

U kunt sleutels voor SAS voor een Service Bus naam ruimte configureren. De sleutel is van toepassing op alle Messa ging-entiteiten binnen die naam ruimte. U kunt ook sleutels configureren voor Service Bus-wacht rijen en-onderwerpen. SAS wordt ook ondersteund op [Azure relay](../azure-relay/relay-authentication-and-authorization.md).

Als u SAS wilt gebruiken, kunt u een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -object configureren in een naam ruimte, wachtrij of onderwerp. Deze regel bestaat uit de volgende elementen:

* *Naam* van de regel: geeft aan.
* *PrimaryKey*: een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Secundaire sleutel*: een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Rechten*: vertegenwoordigt de **verzameling van** verleende, **verzonden** of **beheerde** rechten.

Autorisatie regels die op het niveau van de naam ruimte zijn geconfigureerd, kunnen toegang verlenen tot alle entiteiten in een naam ruimte voor clients met tokens die zijn ondertekend met behulp van de bijbehorende sleutel. U kunt Maxi maal 12 dergelijke autorisatie regels configureren voor een Service Bus naam ruimte, wachtrij of onderwerp. Standaard wordt een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met alle rechten voor elke naam ruimte geconfigureerd wanneer deze voor het eerst wordt ingericht.

Voor toegang tot een entiteit vereist de client een SAS-token dat is gegenereerd met behulp van een specifieke [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resource teken reeks die bestaat uit de resource-URI waarvoor toegang wordt gevraagd en een verval datum met een cryptografische sleutel die is gekoppeld aan de autorisatie regel.

Ondersteuning voor SAS-verificatie voor Service Bus is opgenomen in de Azure .NET SDK-versie 2,0 en hoger. SAS bevat ondersteuning voor een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle Api's die een connection string als para meter accepteren, bieden ondersteuning voor SAS-verbindings reeksen.

> [!IMPORTANT]
> Als u Azure Active Directory Access Control (ook wel bekend als Access Control Service of ACS) met Service Bus, houd er dan rekening mee dat de ondersteuning voor deze methode nu beperkt is en dat u [uw toepassing moet migreren om SAS te gebruiken](service-bus-migrate-acs-sas.md) of OAuth 2,0-verificatie met Azure ad te gebruiken (aanbevolen). Zie [dit blog bericht](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)voor meer informatie over de afschaffing van ACS.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over verificatie met Azure AD:

- [Verificatie met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verificatie vanuit een toepassing](authenticate-application.md)

Raadpleeg de volgende artikelen voor meer informatie over verificatie met SAS:

- [Verificatie met SAS](service-bus-sas.md)
