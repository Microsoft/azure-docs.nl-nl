---
title: Azure multi-factor auth-providers-Azure Active Directory
description: Wanneer moet u een verificatie provider met Azure MFA gebruiken?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8454d2ca83d4f406149e7eb73feb19ce59554f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744122"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Wanneer een Azure Multi-Factor Authentication provider gebruiken?

> [!IMPORTANT]
> Vanaf 1 september 2018 nieuwe verificatie providers kunnen niet meer worden gemaakt. Bestaande verificatie providers kunnen blijven gebruiken en worden bijgewerkt, maar migratie is niet meer mogelijk. Multi-factor Authentication blijft beschikbaar als onderdeel van Azure AD Premium-licenties.

Verificatie in twee stappen is standaard beschikbaar voor globale beheerders die Azure Active Directory hebben en Microsoft 365 gebruikers. Als u echter wilt profiteren van [geavanceerde functies](howto-mfa-mfasettings.md), moet u de volledige versie van Azure Multi-Factor Authentication (MFA) aanschaffen.

Een Azure multi-factor Authentication-provider wordt gebruikt om gebruik te maken van functies van Azure Multi-Factor Authentication voor gebruikers die **geen licenties hebben**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Aanvullende opmerkingen met betrekking tot de Azure MFA SDK

Opmerking: de SDK is afgeschaft en blijft tot 14 november 2018. Na deze datum kan de SDK niet meer worden aangeroepen.

## <a name="what-is-an-mfa-provider"></a>Wat is een MFA-provider?

Er zijn twee soorten auth-providers en het onderscheid wordt uitgelegd hoe uw Azure-abonnement in rekening wordt gebracht. De optie Per verificatie berekent het aantal verificaties die in een maand op uw tenant worden uitgevoerd. Deze optie wordt aanbevolen als u een aantal gebruikers hebt die slechts sporadisch verifiëren. De optie Per gebruiker berekent het aantal personen in uw tenant die in een maand verificatie in twee stappen uitvoeren. Deze optie wordt aanbevolen als u een aantal gebruikers met licenties hebt, maar MFA wilt uitbreiden naar meer gebruikers buiten de restricties van uw licentie.

## <a name="manage-your-mfa-provider"></a>Uw MFA-provider beheren

U kunt het gebruiksmodel (per ingeschakelde gebruiker of per verificatie) niet wijzigen nadat een MFA-provider is gemaakt.

Als u voldoende licenties hebt aangeschaft voor alle gebruikers die zijn ingeschakeld voor MFA, kunt u de MFA-provider helemaal verwijderen.

Als de MFA-provider niet is gekoppeld aan een Azure AD-tenant, of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-tenant, worden de gebruikersinstellingen en configuratieopties niet overgedragen. Daarnaast moeten bestaande Azure MFA-servers opnieuw worden geactiveerd met behulp van activerings referenties die zijn gegenereerd via de MFA-provider.

### <a name="removing-an-authentication-provider"></a>Een verificatie provider verwijderen

> [!CAUTION]
> Er wordt geen bevestiging gevraagd wanneer u een verificatie provider verwijdert. Het selecteren van **verwijderen** is een permanent proces.

Verificatie providers vindt u in de **Azure Portal**  >  **Azure Active Directory**  >  **Security**  >  **MFA**-  >  **providers**. Klik op vermelde providers om de details en configuraties te bekijken die aan deze provider zijn gekoppeld.

Voordat u een verificatie provider verwijdert, moet u rekening houden met de aangepaste instellingen die zijn geconfigureerd in uw provider. Bepaal welke instellingen moeten worden gemigreerd naar algemene MFA-instellingen van uw provider en voltooi de migratie van deze instellingen. 

Azure MFA-servers die zijn gekoppeld aan providers, moeten opnieuw worden geactiveerd met behulp van referenties die zijn gegenereerd onder **Azure Portal**  >  **Azure Active Directory** instellingen voor de  >  **beveiliging**  >  **MFA**-  >  **Server**. Voordat u opnieuw activeert, moeten de volgende bestanden worden verwijderd uit de `\Program Files\Multi-Factor Authentication Server\Data\` Directory op de Azure MFA-servers in uw omgeving:

- caCert
- certificaat
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Een authenticatie provider verwijderen uit de Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Wanneer u hebt gecontroleerd of alle instellingen zijn gemigreerd, kunt u naar de **Azure Portal**  >  **Azure Active Directory**  >  **Security**  >  **MFA**-  >  **providers** bladeren en de weglatings tekens selecteren.. **.** en selecteer **verwijderen**.

> [!WARNING]
> Wanneer u een verificatie provider verwijdert, worden alle rapportage gegevens verwijderd die aan deze provider zijn gekoppeld. Het is raadzaam om activiteiten rapporten op te slaan voordat u uw provider verwijdert.

> [!NOTE]
> Gebruikers met oudere versies van de Microsoft Authenticator-app en de Azure MFA-server moeten mogelijk hun app opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[Multi-Factor Authentication-instellingen configureren](howto-mfa-mfasettings.md)
