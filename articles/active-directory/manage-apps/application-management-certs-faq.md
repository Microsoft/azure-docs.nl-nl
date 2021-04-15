---
title: Azure Active Directory veelgestelde vragen over application management-certificaten
description: Meer informatie over antwoorden op veelgestelde vragen (FAQ) over het beheren van certificaten voor apps met Azure Active Directory als id-provider (IdP).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376236"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory veelgestelde vragen over toepassingsbeheercertificaten (Azure AD)

Op deze pagina vindt u antwoorden op veelgestelde vragen over het beheren van de certificaten voor apps met Azure Active Directory (Azure AD) als id-provider (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Is er een manier om een lijst met verlopenDE SAML-handtekeningcertificaten te genereren?

U kunt alle app-registraties met verlopende geheimen, certificaten en hun eigenaren voor de opgegeven apps exporteren vanuit uw map in een CSV-bestand via [PowerShell-scripts.](app-management-powershell-samples.md) 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Waar vind ik de informatie over het binnenkort verlopen van de stappen voor het verlengen van certificaten?

U kunt de daarvoor benodigde stappen [hier](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire) vinden.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hoe kan ik de vervaldatum aanpassen voor de certificaten die zijn uitgegeven door Azure AD?

Azure AD configureert standaard een certificaat dat na drie jaar verloopt wanneer het automatisch wordt gemaakt tijdens de configuratie van een saml-aanmelding. Omdat u de datum van een certificaat niet kunt wijzigen nadat u het hebt op slaan, moet u een nieuw certificaat maken. Raadpleeg De vervaldatum voor uw federatiecertificaat aanpassen en overstappen op een nieuw certificaat voor stappen [om dit te doen.](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hoe kan ik meldingen over verlopen certificaten automatiseren?

Azure AD verzendt 60, 30 en 7 dagen voordat het SAML-certificaat verloopt een e-mailmelding. U kunt meer dan één e-mailadres toevoegen om meldingen te ontvangen. 

> [!NOTE]
> U kunt maximaal 5 e-mailadressen toevoegen aan de lijst met meldingen (inclusief het e-mailadres van de beheerder die de toepassing heeft toegevoegd). Als u meer personen op de hoogte wilt krijgen, gebruikt u de e-mailberichten in de distributielijst. 

Zie E-mailmeldingsadressen toevoegen voor het verlopen van certificaten als u de e-mailberichten wilt opgeven waar de meldingen [naar moeten worden verzonden.](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)

Er is geen optie om deze e-mailmeldingen die zijn ontvangen van te bewerken of aan te `aadnotification@microsoft.com` passen. U kunt echter app-registraties met verlopende geheimen en certificaten exporteren via [PowerShell-scripts.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Wie kan de certificaten bijwerken?

De eigenaar van de toepassing, globale beheerder of toepassingsbeheerder kan de certificaten bijwerken via Azure Portal ui, PowerShell of Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Ik heb meer informatie nodig over opties voor certificaat-ondertekening.

In Azure AD kunt u opties voor certificaat ondertekening en het algoritme voor certificaat ondertekening instellen. Zie Geavanceerde opties voor [SAML-tokencertificaat voor Azure AD-apps voor meer informatie.](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Ik moet het certificaat voor Azure AD-toepassingen toepassingsproxy vervangen en ik heb meer instructies nodig.

Zie PowerShell sample - Replace certificate in toepassingsproxy apps (PowerShell-voorbeeld: certificaat [vervangen in toepassingsproxy-apps)](scripts/powershell-get-custom-domain-replace-cert.md)om certificaten voor Azure AD-toepassingen toepassingsproxy vervangen.

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hoe kan ik certificaten voor aangepaste domeinen beheren in Azure AD toepassingsproxy?

Als u een on-premises app wilt configureren voor het gebruik van een aangepast domein, hebt u een geverifieerd Azure Active Directory aangepast domein, een PFX-certificaat voor het aangepaste domein en een on-premises app nodig om te configureren. Zie Aangepaste domeinen in Azure AD voor meer [informatie toepassingsproxy.](application-proxy-configure-custom-domain.md) 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Ik moet het certificaat voor token-ondertekening aan de toepassingszijde bijwerken. Waar vind ik deze aan de zijde van Azure AD?

U kunt een SAML X.509-certificaat vernieuwen. Zie [SAML-handtekeningcertificaat](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Wat is een rollover van azure AD-ondertekeningssleutels?

Meer informatie vindt u [hier.](../develop/active-directory-signing-key-rollover.md) 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Hoe kan ik certificaat voor toepassingsversleuteling van token vernieuwen?

Zie How to renew a [token encryption certificate for an enterprise application](howto-saml-token-encryption.md)(Een certificaat voor tokenversleuteling vernieuwen voor een bedrijfstoepassing) als u een certificaat voor tokenversleuteling voor een toepassing wilt vernieuwen. 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Hoe kan ik certificaat voor het ondertekenen van toepassings token vernieuwen?

Zie How to renew a to token signing certificate for an enterprise application (Een certificaat voor token-ondertekening vernieuwen voor een bedrijfstoepassing) als u een certificaat voor het ondertekenen van een [toepassings token wilt vernieuwen.](manage-certificates-for-federated-single-sign-on.md)

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Hoe kan ik Azure AD bijwerken na het wijzigen van mijn federatiecertificaten?

Zie Renew [federation certificates for Microsoft 365 and Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md)als u Azure AD wilt bijwerken nadat u uw federatiecertificaten hebt Azure Active Directory.
