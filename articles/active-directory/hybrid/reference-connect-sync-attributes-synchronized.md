---
title: Kenmerken gesynchroniseerd door Azure AD Connect | Microsoft Docs
description: Een lijst met de kenmerken die worden gesynchroniseerd met Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413187"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synchronisatie: kenmerken die zijn gesynchroniseerd met Azure Active Directory
In dit onderwerp vindt u de kenmerken die worden gesynchroniseerd door Azure AD Connect synchronisatie.  
De kenmerken worden gegroepeerd op basis van de verwante Azure AD-app.

## <a name="attributes-to-synchronize"></a>Te synchroniseren kenmerken
Een veelvoorkomende vraag is *Wat is de lijst met minimale kenmerken die moeten worden gesynchroniseerd*. De standaard-en aanbevolen aanpak is om de standaard kenmerken te houden, zodat een volledige GAL (algemene adres lijst) in de cloud kan worden gemaakt en alle functies in Microsoft 365 werk belastingen kunnen worden opgehaald. In sommige gevallen zijn er enkele kenmerken die uw organisatie niet wil synchroniseren met de Cloud omdat deze kenmerken gevoelige persoonlijke gegevens bevatten, zoals in dit voor beeld:  
![ongeldige kenmerken](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

In dit geval begint u met de lijst met kenmerken in dit onderwerp en identificeert u de kenmerken die persoonlijke gegevens bevatten en die niet kunnen worden gesynchroniseerd. Schakel deze kenmerken vervolgens tijdens de installatie uit met [Azure AD-app-en-kenmerk filters](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Wanneer u de selectie van kenmerken onbeschikbaar maakt, moet u voorzichtig zijn en de selectie van deze kenmerken alleen opheffen die absoluut niet mogelijk is om te synchroniseren. Het deselecteren van andere kenmerken kan een negatieve invloed hebben op de functies.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Microsoft 365-apps voor ondernemingen
| Kenmerknaam | Gebruiker | Opmerking |
| --- |:---:| --- |
| accountEnabled |X |Hiermee wordt bepaald of een account is ingeschakeld. |
| cn |X | |
| displayName |X | |
| objectSID |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| pwdLastSet |X |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
|samAccountName|X| |
| Source anchor |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| usageLocation |X |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| assistent |X |X | | |
| altRecipient |X | | |Vereist Azure AD Connect build 1.1.552.0 of After. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| bedrijf |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| beschrijving | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| Info |X |X |X |Dit kenmerk wordt momenteel niet verbruikt voor groepen. |
| Initialen |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Beschikbaar in Azure AD Connect versie 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Dit kenmerk wordt momenteel niet gebruikt door Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Dit kenmerk wordt momenteel niet gebruikt door Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Dit kenmerk wordt momenteel niet gebruikt door Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Dit kenmerk wordt momenteel niet gebruikt door Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Dit kenmerk wordt momenteel niet gebruikt door Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pagina |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door wachtwoord synchronisatie en Federatie. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |eenmaal gesynchroniseerd van Azure AD naar Exchange Online, waarna Exchange Online de bron van de autoriteit voor dit kenmerk wordt, en eventuele latere wijzigingen niet kunnen worden gesynchroniseerd vanuit on-premises. Zie ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) voor meer informatie.|
| titel |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| bedrijf |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| beschrijving |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| HomePhone |X |X | | |
| Info |X |X |X | |
| initialen |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| middelste naam |X |X | | |
| mobiel |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pagina |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Dit kenmerk wordt momenteel niet gebruikt door share point online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |eenmaal gesynchroniseerd van Azure AD naar Exchange Online, waarna Exchange Online de bron van de autoriteit voor dit kenmerk wordt, en eventuele latere wijzigingen niet kunnen worden gesynchroniseerd vanuit on-premises. Zie ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) voor meer informatie.|
| titel |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker
. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Teams en Skype voor bedrijven online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| bedrijf |X |X | | |
| department |X |X | | |
| beschrijving |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| HomePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-regel |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
| sn |X |X | | |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |eenmaal gesynchroniseerd van Azure AD naar Exchange Online, waarna Exchange Online de bron van de autoriteit voor dit kenmerk wordt, en eventuele latere wijzigingen niet kunnen worden gesynchroniseerd vanuit on-premises. Zie ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) voor meer informatie.|
| titel |X |X | | |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| cn |X | |X |Algemene naam of alias. Meestal het voor voegsel van de waarde [mail]. |
| displayName |X |X |X |Een teken reeks die de naam voor stelt die vaak wordt weer gegeven als de beschrijvende naam (achternaam achternaam). |
| mail |X |X |X |volledige e-mail adres. |
| lid | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| proxyAddresses |X |X |X |mechanische eigenschap. Gebruikt door Azure AD. Bevat alle secundaire e-mail adressen voor de gebruiker. |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |Deze UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |

## <a name="intune"></a>Intune
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| c |X |X | | |
| cn |X | |X | |
| beschrijving |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| lid | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| bedrijf |X |X | | |
| countryCode |X |X | | |
| beschrijving |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
| sn |X |X | | |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| titel |X |X | | |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |

## <a name="3rd-party-applications"></a>toepassingen van derden
Deze groep is een set kenmerken die wordt gebruikt als de minimale kenmerken die nodig zijn voor een algemene werk belasting of toepassing. Het kan worden gebruikt voor een werk belasting die niet in een andere sectie of voor een niet-micro soft-app wordt vermeld. Deze wordt expliciet gebruikt voor het volgende:

* Yammer (alleen gebruiker wordt gebruikt)
* [Hybride Business-to-Business (B2B) samenwerkings scenario's voor meerdere organisatie die worden aangeboden door resources zoals share point](/sharepoint/create-b2b-extranet)

Deze groep is een set kenmerken die kan worden gebruikt als de Azure AD-Directory niet wordt gebruikt voor de ondersteuning van Microsoft 365, Dynamics of intune. Het bevat een kleine set kern kenmerken. Houd er rekening mee dat eenmalige aanmelding of inrichting van sommige toepassingen van derden een synchronisatie van kenmerken moet configureren naast de kenmerken die hier worden beschreven. Toepassings vereisten worden beschreven in de [zelf studie](../saas-apps/tutorial-list.md) over de SaaS-app voor elke toepassing.

| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Hiermee wordt bepaald of een account is ingeschakeld. |
| cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| beheerdDoor | | |X | |
| mailNickName |X |X |X | |
| lid | | |X | |
| objectSID |X | | |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt voor het onderhouden van de synchronisatie tussen Azure AD en AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Wordt gebruikt om te weten wanneer al uitgegeven tokens ongeldig worden gemaakt. Wordt gebruikt door authenticatie van wacht woord-hash, Pass Through-verificatie en Federatie. |
| sn |X |X | | |
| Source anchor |X |X |X |mechanische eigenschap. Onveranderbare id voor het onderhouden van de relatie tussen toevoegingen en Azure AD. |
| usageLocation |X | | |mechanische eigenschap. Het land of de regio van de gebruiker. Wordt gebruikt voor licentie toewijzing. |
| userPrincipalName |X | | |UPN is de aanmeldings-ID voor de gebruiker. Meestal hetzelfde als de waarde [mail]. |

## <a name="windows-10"></a>Windows 10
Een computer met Windows 10 die lid is van een domein (apparaat) synchroniseert enkele kenmerken met Azure AD. Zie [apparaten koppelen aan Azure AD voor Windows 10](../devices/hybrid-azuread-join-plan.md)voor meer informatie over de scenario's. Deze kenmerken worden altijd gesynchroniseerd en Windows 10 wordt niet weer gegeven als een app. u kunt de selectie opheffen. Een computer die lid is van een domein in Windows 10 wordt geïdentificeerd door het kenmerk userCertificate te hebben ingevuld.

| Kenmerknaam | Apparaat | Opmerking |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hardcoded waarde voor computers die lid zijn van een domein. |
| displayName |X | |
| MS-DS-CreatorSID |X |Ook wel registeredOwnerReference genoemd. |
| GUID |X |Ook wel deviceID genoemd. |
| objectSID |X |Ook wel onPremisesSecurityIdentifier genoemd. |
| operatingSystem |X |Ook wel deviceOSType genoemd. |
| Waarde operatingSystemVersion |X |Ook wel deviceOSVersion genoemd. |
| userCertificate |X | |

Deze kenmerken voor de **gebruiker** zijn naast de andere apps die u hebt geselecteerd.  

| Kenmerknaam | Gebruiker | Opmerking |
| --- |:---:| --- |
| domainFQDN |X |Ook wel DNS genoemd. Bijvoorbeeld contoso.com. |
| domainNetBios |X |Ook wel NetBIOS-naam genoemd. Bijvoorbeeld CONTOSO. |
| msDS-KeyCredentialLink |X |Zodra de gebruiker is inge schreven in Windows hello voor bedrijven. | 

## <a name="exchange-hybrid-writeback"></a>Hybride Exchange-write-back
Deze kenmerken worden teruggeschreven van Azure AD naar on-premises Active Directory wanneer u **Exchange Hybrid** inschakelt. Afhankelijk van uw Exchange-versie kunnen minder kenmerken worden gesynchroniseerd.

| Kenmerk naam (on-premises AD) | Kenmerk naam (gebruikers interface verbinden) | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| MS-DS-externe-Directory-object-id |X | | |Afgeleid van cloudAnchor in azure AD. Dit kenmerk is nieuw in Exchange 2016 en Windows Server 2016 AD. |
| msExchArchiveStatus| MS-Herw-ArchiveStatus |X | | |Online archief: klanten kunnen e-mail archiveren. |
| msExchBlockedSendersHash| MS-Herw-BlockedSendersHash |X | | |Filteren: schrijft terug on-premises filteren en online veilige en geblokkeerde afzender gegevens van clients. |
| msExchSafeRecipientsHash| MS-Herw-SafeRecipientsHash  |X | | |Filteren: schrijft terug on-premises filteren en online veilige en geblokkeerde afzender gegevens van clients. |
| msExchSafeSendersHash| MS-Herw-SafeSendersHash  |X | | |Filteren: schrijft terug on-premises filteren en online veilige en geblokkeerde afzender gegevens van clients. |
| msExchUCVoiceMailSettings| MS-Herw-UCVoiceMailSettings |X | | |Unified Messa ging (UM)-online voice mail inschakelen: wordt gebruikt door micro soft Lync Server-integratie om aan te geven dat de gebruiker voice mail in onlineservices heeft. |
| msExchUserHoldPolicies| MS-Herw-UserHoldPolicies |X | | |Juridische ruimte: Hiermee kunnen Cloud Services bepalen welke gebruikers in het kader van een rechts zaak worden bewaard. |
| proxyAddresses| proxyAddresses |X |X |X |Alleen het een x500-adres van Exchange Online wordt ingevoegd. |
| publicDelegates| MS-Herw-Public-gemachtigden  |X | | |Hiermee kan een Exchange Online-postvak SendOnBehalfTo rechten krijgen voor gebruikers met een on-premises Exchange-postvak. Vereist Azure AD Connect build 1.1.552.0 of After. |

## <a name="exchange-mail-public-folder"></a>Open bare map voor Exchange-e-mail
Deze kenmerken worden gesynchroniseerd van on-premises Active Directory naar Azure AD wanneer u de **open bare map voor Exchange-e-mail** inschakelt.

| Kenmerknaam | PublicFolder | Opmerking |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| GUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Apparaat terugschrijven
Device-objecten worden gemaakt in Active Directory. Deze objecten kunnen apparaten zijn gekoppeld aan Azure AD of Windows 10-computers die lid zijn van een domein.

| Kenmerknaam | Apparaat | Opmerking |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Alleen met Windows Server 2016 AD-schema |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notities
* Wanneer u een alternatieve ID gebruikt, wordt het on-premises kenmerk userPrincipalName gesynchroniseerd met het Azure AD-kenmerk onPremisesUserPrincipalName. Het kenmerk alternatieve ID, bijvoorbeeld e-mail, is gesynchroniseerd met het Azure AD-kenmerk userPrincipalName.
* In de bovenstaande lijsten is de object type- **gebruiker** ook van toepassing op het object type **inetOrgPerson**.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).