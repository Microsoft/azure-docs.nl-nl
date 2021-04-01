---
title: Wat is apparaat-id in Azure Active Directory?
description: Leer hoe apparaat-id-beheer u kan helpen de apparaten te beheren die toegang verkrijgen tot resources in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8399f7101697af429b8c073c101dbfea203e98ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87025588"
---
# <a name="what-is-a-device-identity"></a>Wat is een apparaat-id?

Als gevolg van een toenemend aantal apparaten van alle vormen en maten en het concept Bring-Your-Own-Device (BYOD), hebben IT-professionals te maken met twee tegengestelde doelen:

- Eindgebruikers de mogelijkheid bieden overal en altijd productief te zijn
- De bedrijfsactiva beschermen

Voor het beveiligen van deze assets moeten IT-medewerkers eerst de apparaat-id's beheren. IT-medewerkers kunnen op de identiteit van het apparaat voortbouwen met hulpprogramma's zoals Microsoft Intune om ervoor te zorgen dat aan de normen voor beveiliging en compliance wordt voldaan. Azure Active Directory (Azure AD) maakt eenmalige aanmelding bij apparaten, apps en services vanaf elke locatie mogelijk via deze apparaten.

- Uw gebruikers krijgen toegang tot de assets van uw organisatie die ze nodig hebben. 
- Uw IT-medewerkers krijgen de besturingselementen die ze nodig hebben om uw organisatie te beveiligen.

Apparaat-id-beheer vormt ook de basis voor [apparaatgebaseerde voorwaardelijke toegang](../conditional-access/require-managed-devices.md). Met apparaatgebaseerde voorwaardelijke toegangsbeleidsregels kunt u ervoor zorgen dat toegang tot resources in uw omgeving alleen mogelijk is via beheerde apparaten.

## <a name="getting-devices-in-azure-ad"></a>Apparaten ophalen in Azure Active Directory

U kunt een apparaat op verschillende manieren in Azure AD ophalen:

- **Geregistreerd bij Azure AD**
   - Apparaten die zijn geregistreerd bij Azure AD, zijn doorgaans apparaten in persoonlijk eigendom of mobiele apparaten en zijn aangemeld met een persoonlijk Microsoft-account of een ander lokaal account.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Toegevoegd aan Azure AD**
   - Apparaten die zijn toegevoegd aan Azure AD zijn het eigendom van een organisatie en zijn aangemeld met een Azure AD-account dat bij die organisatie hoort. Ze bestaan alleen in de cloud.
      - Windows 10 
      - [Virtuele Windows Server 2019-machines die worden uitgevoerd in Azure](howto-vm-sign-in-azure-ad-windows.md) (serverkern wordt niet ondersteund)
- **lid is van hybride Azure Active Directory**
   - Apparaten die zijn gekoppeld aan hybride Azure AD zijn het eigendom van een organisatie en zijn aangemeld met een Azure Active Directory Domain Services-account dat bij die organisatie hoort. Ze bevinden zich in de cloud en on-premises.
      - Windows 7, 8.1 of 10
      - Windows Server 2008 of hoger

![Weergave van apparaten in het deelvenster Azure AD-apparaten](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Een hybride status verwijst naar meer dan alleen de status van een apparaat. Een geldige Azure AD-gebruiker is ook vereist voor een geldige hybride status.

## <a name="device-management"></a>Apparaatbeheer

Apparaten in Azure AD kunnen worden beheerd met MDM-hulpprogramma's (Mobile Device Management) zoals Microsoft Intune, Microsoft Endpoint Configuration Manager, groepsbeleid (aan Hybrid Azure AD gekoppeld), MAM-hulpprogramma's (Mobile Application Management) of andere hulpprogramma's van derden.

## <a name="resource-access"></a>Resourcetoegang

Het registreren bij en koppelen van apparaten aan Azure AD biedt uw gebruikers naadloze eenmalige aanmelding (SSO) bij cloudresources. Dit proces biedt beheerders de mogelijkheid om beleid voor voorwaardelijke toegang toe te passen op resources op basis van het apparaat waartoe ze toegang hebben. 

> [!NOTE]
> Voor voorwaardelijk toegangsbeleid op basis van apparaten zijn aan Hybride Azure AD gekoppelde apparaten of compatibele apparaten die aan Azure AD zijn gekoppelde of bij Azure Ad zijn geregistreerd vereist.

Het primaire vernieuwingstoken (PRT) bevat informatie over het apparaat en is vereist voor SSO. Als u een op apparaten gebaseerd beleid voor voorwaardelijke toegang hebt ingesteld voor een toepassing zonder de PRT, wordt toegang geweigerd. Voor beleidsregels voor hybride voorwaardelijke toegang is een apparaat met hybride status en een geldige, aangemelde gebruiker vereist.

Apparaten die aan Azure AD of Hybrid Azure AD zijn gekoppeld, profiteren van SSO voor de on-premises resources van uw organisatie, evenals voor de cloudresources. U vindt meer informatie in het artikel [Hoe SSO bij on-premises resources werkt op aan Azure AD gekoppelde apparaten](azuread-join-sso.md).

## <a name="device-security"></a>Apparaatbeveiliging

- Voor **bij Azure AD geregistreerde apparaten** wordt een account gebruikt dat door de eindgebruiker wordt beheerd. Dit account is een Microsoft-account of een andere lokaal beheerde referentie die is beveiligd met een of meer van de volgende items.
   - Wachtwoord
   - PIN
   - Patroon
   - Windows Hello
- Voor **aan Azure AD of hybride Azure AD-gekoppeld apparaten** wordt een organisatorisch account in Azure AD gebruikt dat is beveiligd met een of meer van de volgende items.
   - Wachtwoord
   - Windows Hello voor Bedrijven

## <a name="provisioning"></a>Inrichten

Het ophalen van apparaten in Azure AD kunt u zelf uitvoeren of door een beheerder via een beheerd inrichtingsproces laten uitvoeren.

## <a name="summary"></a>Samenvatting

Met apparaat-d-beheer in Azure AD kunt u:

- Het proces van het brengen en beheren van apparaten in Azure AD vereenvoudigen
- Uw gebruikers gemakkelijke toegang tot de cloudresources van uw organisatie bieden

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bij Azure AD geregistreerde apparaten](concept-azure-ad-register.md)
- Meer informatie over [aan Azure AD gekoppelde apparaten](concept-azure-ad-join.md)
- Meer informatie over [aan hybride Azure AD-gekoppelde apparaten](concept-azure-ad-join-hybrid.md)
- Zie [Apparaat-id's beheren met behulp van Azure Portal](device-management-azure-portal.md) voor een overzicht van hoe u apparaat-id's in Azure Portal kunt beheren.
- Zie [Beleid configureren voor apparaatgebaseerde voorwaardelijke toegang van Azure Active Directory](../conditional-access/require-managed-devices.md) voor meer informatie over apparaatgebaseerde voorwaardelijke toegang.
