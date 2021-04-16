---
title: Azure Service Bus verificatie en autorisatie | Microsoft Docs
description: Apps verifiëren voor Service Bus met Shared Access Signature (SAS)-verificatie.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516651"
---
# <a name="service-bus-authentication-and-authorization"></a>Vereenvoudigde Service Bus-verificatie en -autorisatie
Er zijn twee manieren om toegang tot uw Azure Service Bus te verifiëren en te autoriseren: Azure Activity Directory (Azure AD) en Shared Access Signatures (SAS). In dit artikel vindt u meer informatie over het gebruik van deze twee typen beveiligingsmechanismen. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD-integratie voor Service Bus-resources biedt op rollen gebaseerd toegangsbeheer (RBAC) van Azure voor een fijner beheer van de toegang van een client tot resources. U kunt Azure RBAC gebruiken om machtigingen te verlenen aan een beveiligingsprincipaal, die een gebruiker, een groep of een toepassingsservice-principal kan zijn. De beveiligingsprincipaal wordt geverifieerd door Azure AD om een OAuth 2.0-token te retourneren. Het token kan worden gebruikt om een aanvraag voor toegang tot een Service Bus resource (wachtrij, onderwerp, etc.) toe te staan.

Zie de volgende artikelen voor meer informatie over het authenticeren met Azure AD:

- [Verifiëren met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verifiëren vanuit een toepassing](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) ondersteunt OAuth-verificatie met Azure AD.

> [!IMPORTANT]
> Het autoriseren van gebruikers of toepassingen met behulp van een OAuth 2.0-token dat door Azure AD wordt geretourneerd, biedt een betere beveiliging en gebruiksgemak dan SHARED Access Signatures (SAS). Met Azure AD hoeft u de tokens niet op te slaan in uw code en potentiële beveiligingsproblemen te risico lopen. U wordt aangeraden Azure AD te gebruiken met uw Azure Service Bus toepassingen, indien mogelijk. 

## <a name="shared-access-signature"></a>Shared Access Signature
[Met SAS-verificatie](service-bus-sas.md) kunt u een gebruiker toegang verlenen tot Service Bus resources, met specifieke rechten. SAS-verificatie in Service Bus omvat de configuratie van een cryptografische sleutel met bijbehorende rechten op een Service Bus resource. Clients kunnen vervolgens toegang krijgen tot die resource door een SAS-token te presenteren, dat bestaat uit de resource-URI die wordt gebruikt en een vervaldatum die is ondertekend met de geconfigureerde sleutel.

U kunt sleutels voor SAS configureren op een Service Bus naamruimte. De sleutel is van toepassing op alle berichtenentiteiten binnen die naamruimte. U kunt ook sleutels configureren voor Service Bus wachtrijen en onderwerpen. SAS wordt ook ondersteund op [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Als u SAS wilt gebruiken, kunt u een autorisatieregel voor gedeelde toegang configureren voor een naamruimte, wachtrij of onderwerp. Deze regel bestaat uit de volgende elementen:

* *KeyName:* identificeert de regel.
* *PrimaryKey:* een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *SecondaryKey:* een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Rechten:* vertegenwoordigt de verzameling van verleende rechten **Listen,** **Send** **of** Manage.

Autorisatieregels die zijn geconfigureerd op naamruimteniveau, kunnen toegang verlenen aan alle entiteiten in een naamruimte voor clients met tokens die zijn ondertekend met de bijbehorende sleutel. U kunt maximaal 12 van dergelijke autorisatieregels configureren voor een Service Bus naamruimte, wachtrij of onderwerp. Standaard wordt een autorisatieregel voor gedeelde toegang met alle rechten geconfigureerd voor elke naamruimte wanneer deze voor het eerst wordt ingericht.

Voor toegang tot een entiteit vereist de client een SAS-token dat is gegenereerd met behulp van een specifieke autorisatieregel voor gedeelde toegang. Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resourcereeks die bestaat uit de resource-URI waarvoor toegang wordt geclaimd en een vervaldatum met een cryptografische sleutel die is gekoppeld aan de autorisatieregel.

Sas-verificatieondersteuning voor Service Bus is opgenomen in de Azure .NET SDK-versies 2.0 en hoger. SAS biedt ondersteuning voor een autorisatieregel voor gedeelde toegang. Alle API's die een connection string parameter accepteren, bieden ondersteuning voor SAS-verbindingsreeksen.

> [!IMPORTANT]
> Als u Azure Active Directory Access Control (ook wel bekend als Access Control Service of ACS) gebruikt met Service Bus, is de ondersteuning voor deze methode nu beperkt en moet u uw toepassing migreren om [SAS](service-bus-migrate-acs-sas.md) te gebruiken of OAuth 2.0-verificatie gebruiken met Azure AD (aanbevolen). Zie dit [blogbericht](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)voor meer informatie over het aftrekken van ACS.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over het authenticeren met Azure AD:

- [Verificatie met beheerde identiteiten](service-bus-managed-service-identity.md)
- [Verificatie vanuit een toepassing](authenticate-application.md)

Zie de volgende artikelen voor meer informatie over het authenticeren met SAS:

- [Verificatie met SAS](service-bus-sas.md)
