---
title: Veelgestelde vragen over het Azure Active Directory van toepassings beheer certificaten
description: Meer informatie over antwoorden op veelgestelde vragen over het beheren van certificaten voor apps met behulp van Azure Active Directory als een id-provider (IdP).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803717"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Veelgestelde vragen over Azure Active Directory (Azure AD)-toepassings beheer certificaten

Op deze pagina vindt u antwoorden op veelgestelde vragen over het beheren van de certificaten voor apps die gebruikmaken van Azure Active Directory (Azure AD) als id-provider (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Is er een manier om een lijst met verlopende SAML-handtekening certificaten te genereren?

U kunt alle app-registraties met verlopen geheimen, certificaten en hun eigen aren voor de opgegeven apps vanuit uw map in een CSV-bestand exporteren via [Power shell-scripts](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Waar vind ik de informatie over binnenkort om de stappen voor het vernieuwen van certificaten te laten verlopen?

U kunt de daarvoor benodigde stappen [hier](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire) vinden.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hoe kan ik de verval datum aanpassen voor de certificaten die zijn uitgegeven door Azure AD?

Standaard configureert Azure AD een certificaat dat na drie jaar verloopt wanneer het automatisch wordt gemaakt tijdens de configuratie van eenmalige SAML-aanmelding. Omdat u de datum van een certificaat niet kunt wijzigen nadat u het hebt opgeslagen, moet u een nieuw certificaat maken. Voor stappen over hoe u dit doet, raadpleegt u [de verval datum voor uw Federatie certificaat aanpassen en deze naar een nieuw certificaat](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)overstappen.

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hoe kan ik de verlopen meldingen voor certificaten automatiseren?

Azure AD verzendt een e-mail melding 60, 30 en 7 dagen voordat het SAML-certificaat is verlopen. U kunt meer dan één e-mail adres toevoegen om meldingen te ontvangen. 

> [!NOTE]
> U kunt Maxi maal 5 e-mail adressen toevoegen aan de lijst met meldingen (met inbegrip van het e-mail adres van de beheerder die de toepassing heeft toegevoegd). Als u meer mensen wilt laten weten dat ze een melding moeten ontvangen, gebruikt u de e-mail berichten distributie lijst. 

Als u de e-mail berichten wilt opgeven waarnaar de meldingen moeten worden verzonden, raadpleegt u [e-mail meldings adressen toevoegen voor het verlopen van certificaten](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Er is geen optie voor het bewerken of aanpassen van deze e-mail meldingen die zijn ontvangen van `aadnotification@microsoft.com` . U kunt echter wel app-registraties met verlopen geheimen en certificaten exporteren via [Power shell-scripts](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Wie kan de certificaten bijwerken?

De eigenaar van de toepassing of globale beheerder of toepassings beheerder kan de certificaten bijwerken via Azure Portal gebruikers interface, Power shell of Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Ik heb meer informatie nodig over de opties voor certificaat ondertekening.

In azure AD kunt u opties voor het ondertekenen van certificaten en het algoritme voor het ondertekenen van certificaten instellen. Zie [Geavanceerde opties voor het ondertekenen van SAML-token certificaten voor Azure AD-apps voor](certificate-signing-options.md)meer informatie.

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Ik moet het certificaat voor Azure AD-toepassingsproxy-toepassingen vervangen en meer instructies hebben.

Als u certificaten voor Azure AD-toepassingsproxy-toepassingen wilt vervangen, raadpleegt u [Power shell-voor beeld-vervangen certificaat in Application proxy-apps](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hoe kan ik certificaten voor aangepaste domeinen in azure AD-toepassingsproxy beheren?

Als u een on-premises app wilt configureren voor het gebruik van een aangepast domein, hebt u een geverifieerd Azure Active Directory aangepast domein, een PFX-certificaat voor het aangepaste domein en een on-premises app die moet worden geconfigureerd. Zie [aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie. 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Ik moet het certificaat voor token-ondertekening aan de kant van de toepassing bijwerken. Waar kan ik de app aan Azure AD zijde krijgen?

U kunt een SAML X. 509-certificaat vernieuwen Zie het [SAML-handtekening certificaat](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Wat is de rollover van de Azure AD-handtekening sleutel?

U kunt [hier](../develop/active-directory-signing-key-rollover.md)meer informatie vinden. 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Het versleutelings certificaat voor het toepassings token Hoe kan ik vernieuwen?

Zie [een certificaat voor token versleuteling vernieuwen voor een bedrijfs toepassing](howto-saml-token-encryption.md)om een certificaat voor het versleutelen van een toepassings token te vernieuwen. 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Certificaat voor ondertekening van toepassings token Hoe kan ik vernieuwen?

Zie [een certificaat voor token-ondertekening vernieuwen voor een bedrijfs toepassing](manage-certificates-for-federated-single-sign-on.md)om een certificaat voor het ondertekenen van een toepassings token te vernieuwen.

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Hoe kan ik Azure AD bijwerken na het wijzigen van mijn Federatie certificaten?

Als u Azure AD wilt bijwerken nadat u uw Federatie certificaten hebt gewijzigd, raadpleegt u [Federation-certificaten vernieuwen voor Microsoft 365 en Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
