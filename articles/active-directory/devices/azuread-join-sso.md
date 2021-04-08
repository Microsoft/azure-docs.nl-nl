---
title: Hoe SSO voor on-premises resources werkt op apparaten die zijn toegevoegd aan Azure AD | Microsoft Docs
description: Meer informatie over het uitbreiden van de SSO-ervaring door hybride Azure Active Directory gekoppelde apparaten te configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da22a4e5e9ab13ec18347e58bea6cfc5f45333de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630697"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>De werking van SSO voor on-premises resources op apparaten die zijn toegevoegd aan Azure AD

Het is waarschijnlijk niet een verrassing dat een Azure Active Directory (Azure AD) aan het apparaat is gekoppeld, u een eenmalige aanmelding (SSO) hebt voor de Cloud-apps van uw Tenant. Als uw omgeving een on-premises Active Directory (AD) heeft, kunt u ook SSO-ervaring op aan Azure AD gekoppelde apparaten verkrijgen voor bronnen en toepassingen die afhankelijk zijn van on-premises AD. 

In dit artikel wordt uitgelegd hoe dit werkt.

## <a name="prerequisites"></a>Vereisten

On-premises SSO vereist een line-of-Insight-communicatie met uw on-premises AD DS domein controllers. Als aan Azure AD gekoppelde apparaten niet zijn verbonden met het netwerk van uw organisatie, is een VPN-of andere netwerk infrastructuur vereist. 

## <a name="how-it-works"></a>Uitleg 

Met een toegevoegd Azure AD-apparaat hebben uw gebruikers al een SSO-ervaring voor de Cloud-apps in uw omgeving. Als uw omgeving een Azure AD en een on-premises AD heeft, kunt u het bereik van uw SSO-ervaring uitbreiden naar uw LOB-apps (line of Business), bestands shares en printers.

Apparaten die zijn toegevoegd aan Azure AD, hebben geen kennis van uw on-premises AD-omgeving, omdat ze niet zijn gekoppeld. U kunt echter aanvullende informatie over uw on-premises AD naar deze apparaten bieden met Azure AD Connect.

Als u een hybride omgeving hebt met Azure AD en on-premises AD, is het waarschijnlijk al dat u Azure AD Connect hebt geïmplementeerd om uw on-premises identiteits gegevens te synchroniseren met de Cloud. Als onderdeel van het synchronisatie proces Azure AD Connect synchroniseert on-premises gebruikers-en domein gegevens naar Azure AD. Wanneer een gebruiker zich aanmeldt bij een in een hybride omgeving aangesloten Azure AD-apparaat:

1. Azure AD stuurt de gegevens van het on-premises domein van de gebruiker terug naar het apparaat, samen met het [primaire vernieuwings token](concept-primary-refresh-token.md)
1. De LSA-service (Local Security Authority) maakt Kerberos-en NTLM-verificatie mogelijk op het apparaat.

>[!NOTE]
> Windows hello voor bedrijven vereist aanvullende configuratie om on-premises SSO in te scha kelen vanaf een aan Azure AD toegevoegd apparaat. Zie voor meer informatie [Azure AD gekoppelde apparaten configureren voor on-premises Single-Sign op het gebruik van Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

Tijdens een poging tot het verkrijgen van toegang tot een resource die Kerberos of NTLM aanvraagt in de on-premises omgeving van de gebruiker, wordt het apparaat:

1. Hiermee worden de gegevens van het on-premises domein en de gebruikers referenties naar de gevonden DC verzonden om de gebruiker te laten verifiëren.
1. Ontvangt een Kerberos [ticket-granting ticket (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) of NTLM-token op basis van het protocol dat door de on-premises resource of toepassing wordt ondersteund. Als de poging om de Kerberos-TGT of het NTLM-token voor het domein uit te voeren, mislukt (gerelateerde DCLocator time-out kan een vertraging veroorzaken), worden de referentie beheer vermeldingen geprobeerd, of kan de gebruiker een authenticatie pop-up ontvangen met referenties voor de doel bron.

Alle apps die zijn geconfigureerd voor **geïntegreerde Windows-verificatie** , halen zichzelf SSO wanneer een gebruiker deze probeert te openen.

## <a name="what-you-get"></a>Wat u krijgt

Met SSO kunt u op een aan Azure AD gekoppelde apparaat het volgende doen: 

- Toegang krijgen tot een UNC-pad op een AD-lidserver
- Toegang tot een AD member-webserver die is geconfigureerd voor Windows-geïntegreerde beveiliging 

Als u uw on-premises AD wilt beheren vanaf een Windows-apparaat, installeert u de [Remote Server Administration Tools voor Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

U kunt gebruikmaken van:

- De module Active Directory gebruikers en computers (ADUC) om alle AD-objecten te beheren. U moet echter het domein opgeven waarmee u hand matig verbinding wilt maken.
- De DHCP-module voor het beheren van een AD-lid van een DHCP-server. Het is echter mogelijk dat u de naam of het adres van de DHCP-server moet opgeven.
 
## <a name="what-you-should-know"></a>Wat u moet weten

Mogelijk moet u uw [op domeinen gebaseerde filtering](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) aanpassen in azure AD Connect om ervoor te zorgen dat de gegevens over de vereiste domeinen worden gesynchroniseerd.

Apps en bronnen die afhankelijk zijn van Active Directory computer authenticatie, werken niet omdat apparaten die zijn toegevoegd aan Azure AD geen computer object in AD hebben. 

U kunt geen bestanden delen met andere gebruikers op een apparaat dat lid is van Azure AD.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Apparaatbeheer in azure Active Directory?](overview.md) voor meer informatie. 
