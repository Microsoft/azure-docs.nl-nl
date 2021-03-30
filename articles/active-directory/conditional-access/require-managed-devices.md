---
title: Voor voorwaardelijke toegang is een beheerd apparaat vereist-Azure Active Directory
description: Meer informatie over het configureren van op apparaten gebaseerde beleids regels voor voorwaardelijke toegang van Azure Active Directory (Azure AD) waarvoor beheerde apparaten zijn vereist voor toegang tot Cloud-apps.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077758"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Procedure: beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang

In een mobiele wereld, Azure Active Directory (Azure AD) in de Cloud, kunt u eenmalige aanmelding voor apps en services vanaf elke locatie. Geautoriseerde gebruikers hebben toegang tot uw Cloud-apps vanuit een breed scala aan apparaten, zoals mobiele apparaten en ook persoonlijke apparatuur. Veel omgevingen hebben echter ten minste enkele apps die alleen toegankelijk moeten zijn voor apparaten die voldoen aan uw normen voor beveiliging en naleving. Deze apparaten worden ook wel beheerde apparaten genoemd. 

In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang kunt configureren waarvoor beheerde apparaten toegang hebben tot bepaalde Cloud-apps in uw omgeving. 

## <a name="prerequisites"></a>Vereisten

Het vereisen van beheerde apparaten voor Cloud app Access bindt **voorwaardelijke toegang van Azure AD** en het **beheer van Azure ad-apparaten** samen. Als u nog niet bekend bent met een van deze gebieden, lees dan eerst de volgende onderwerpen:

- **[Voorwaardelijke toegang in azure Active Directory: in](./overview.md)** dit artikel vindt u een conceptueel overzicht van voorwaardelijke toegang en de gerelateerde terminologie.
- **[Inleiding tot Apparaatbeheer in azure Active Directory: in](../devices/overview.md)** dit artikel vindt u een overzicht van de verschillende opties die u nodig hebt om apparaten te verkrijgen onder het beheer van de organisatie. 
- Voor Chrome-ondersteuning in **Windows 10 Crea tors update (versie 1703)** of nieuwer, installeert u de [extensie Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Deze uitbrei ding is vereist wanneer voor een beleid voor voorwaardelijke toegang apparaatspecifieke Details vereist zijn.

>[!NOTE] 
> We raden u aan om Azure AD-beleid voor voorwaardelijke toegang te gebruiken om de beste afdwinging te verkrijgen na de initiële verificatie van het apparaat. Dit omvat het sluiten van sessies als het apparaat niet meer compatibel is en de code stroom van het apparaat.


## <a name="scenario-description"></a>Scenariobeschrijving

Het is een goed overzicht van het evenwicht tussen beveiliging en productiviteit. De verspreiding van ondersteunde apparaten om toegang te krijgen tot uw Cloud bronnen helpt u bij het verbeteren van de productiviteit van uw gebruikers. Op de gespiegelde zijde wilt u waarschijnlijk niet dat bepaalde resources in uw omgeving toegankelijk zijn voor apparaten met een onbekend beveiligings niveau. Voor de betrokken resources moet u vereisen dat gebruikers alleen toegang hebben tot ze via een beheerd apparaat. 

Met voorwaardelijke toegang van Azure AD kunt u deze vereiste aanpakken met één beleid dat toegang verleent:

- Naar geselecteerde Cloud-apps
- Voor geselecteerde gebruikers en groepen
- Een beheerd apparaat vereisen

## <a name="managed-devices"></a>Beheerde apparaten  

In eenvoudige termen zijn beheerde apparaten apparaten die onder *een bepaald soort* organisatie beheer vallen. In azure AD is de vereiste voor een beheerd apparaat dat is geregistreerd bij Azure AD. Als u een apparaat registreert, wordt er een identiteit voor het apparaat in de vorm van een apparaatobject gemaakt. Dit object wordt door Azure gebruikt om status informatie over een apparaat bij te houden. Als Azure AD-beheerder kunt u dit object al gebruiken om de status van een apparaat in of uit te scha kelen.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Scherm opname van het deel venster met apparaten in azure A D. in-en uitschakelen worden items gemarkeerd." border="false":::

Als u een apparaat wilt ontvangen dat is geregistreerd bij Azure AD, hebt u drie opties: 

- **Geregistreerde Azure ad-apparaten** : voor het verkrijgen van een persoonlijk apparaat dat is geregistreerd bij Azure AD
- Aan **Azure AD gekoppelde apparaten** : voor een organisatie Windows 10-apparaat dat niet is gekoppeld aan een on-premises AD die is geregistreerd bij Azure AD. 
- **Hybride Azure AD gekoppelde apparaten** : voor het verkrijgen van een apparaat met Windows 10 of een ondersteund lager niveau dat is gekoppeld aan een on-premises AD die is geregistreerd bij Azure AD.

Deze drie opties worden beschreven in het artikel [Wat is een apparaat-id?](../devices/overview.md)

Om een beheerd apparaat te worden, moet een geregistreerd apparaat een **hybride Azure AD-apparaat** zijn of een **apparaat dat is gemarkeerd als compatibel**.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Scherm afbeelding van het deel venster toekenning van Azure A D. Toegang verlenen is geselecteerd, evenals de selectie vakjes voor apparaten die voldoen aan het beleid en hybride Azure A D join." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-aangesloten apparaten vereisen

In het beleid voor voorwaardelijke toegang kunt u **vereisen dat Hybrid Azure AD is toegevoegd** aan een status die alleen toegankelijk is voor de geselecteerde Cloud-apps met behulp van een beheerd apparaat. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Scherm afbeelding van het deel venster toekenning van Azure A D. Toegang verlenen is geselecteerd. Er is ook een selectie vakje vereist dat voor hybride Azure een lid D is toegevoegd." border="false":::

Deze instelling geldt alleen voor apparaten met Windows 10 of lager niveau, zoals Windows 7 of Windows 8, die zijn gekoppeld aan een on-premises AD. U kunt deze apparaten alleen registreren bij Azure AD met behulp van een hybride Azure AD-deelname, een [geautomatiseerd proces](../devices/hybrid-azuread-join-plan.md) voor het verkrijgen van een Windows 10-apparaat dat is geregistreerd. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Tabel waarin de naam, de ingeschakelde status, de O S, de versie, het jointype, de eigenaar, de M D M en de status van het apparaat worden weer gegeven. De status conform is nee." border="false":::

Wat is een hybride Azure AD-apparaat dat is gekoppeld aan een beheerd apparaat?  Voor apparaten die zijn gekoppeld aan een on-premises AD, wordt ervan uitgegaan dat de controle over deze apparaten wordt afgedwongen met behulp van beheer oplossingen als **Configuration Manager** of **groeps beleid (GP)** om ze te beheren. Omdat er geen methode voor Azure AD is om te bepalen of een van deze methoden is toegepast op een apparaat, is het vereisen van een hybride Azure AD-apparaat dat is gekoppeld aan een relatief zwak mechanisme om een beheerd apparaat te vereisen. Het is aan u als beheerder om te beoordelen of de methoden die worden toegepast op uw on-premises apparaten die lid zijn van een domein krachtig genoeg zijn om een beheerd apparaat te vormen als een dergelijk apparaat ook een hybride Azure AD-apparaat is.

## <a name="require-device-to-be-marked-as-compliant"></a>Vereisen dat het apparaat moet worden gemarkeerd als compatibel

De optie om ervoor te zorgen dat *een apparaat moet worden gemarkeerd als compatibel* , is het sterkste formulier voor het aanvragen van een beheerd apparaat.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Scherm afbeelding van het deel venster toekenning van Azure A D. Toegang verlenen is geselecteerd. Een selectie vakje vereist dat een apparaat moet worden gemarkeerd als compatibel is ook geselecteerd." border="false":::

Voor deze optie moet een apparaat zijn geregistreerd bij Azure AD en moet het ook worden gemarkeerd als compatibel door:
         
- Intune
- Een Mobile Device Management MDM-systeem (van derden) dat Windows 10-apparaten beheert via Azure AD-integratie. MDM-systemen van derden voor apparaten met een ander besturings systeem dan Windows 10 worden niet ondersteund.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Tabel waarin de naam, de ingeschakelde status, de O S, de versie, het jointype, de eigenaar, de M D M en de status van het apparaat worden weer gegeven. De status conform is gemarkeerd." border="false":::

Voor een apparaat dat is gemarkeerd als compatibel, kunt u aannemen dat: 

- De mobiele apparaten die uw werk nemers gebruiken voor toegang tot Bedrijfs gegevens worden beheerd
- Mobiele apps die uw werk nemers gebruiken, worden beheerd
- Uw bedrijfs gegevens worden beveiligd door u te helpen de manier te bepalen waarop uw werk nemers toegang krijgen en delen
- Het apparaat en de bijbehorende apps voldoen aan de beveiligings vereisten van het bedrijf

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Scenario: apparaatregistratie vereisen voor iOS-en Android-apparaten

In dit scenario heeft Contoso besloten dat alle mobiele toegang tot Microsoft 365 resources een geregistreerd apparaat moeten gebruiken. Al hun gebruikers aanmelden met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende stappen uitvoeren om het gebruik van een Inge schreven mobiel apparaat te vereisen.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **opnemen** selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert u **Office 365**.
1. Onder **voor waarden** selecteert u **apparaat platforms**.
   1. Stel **configureren** in op **Ja**.
   1. Voeg **Android** en **IOS** toe.
1. Selecteer onder **toegangs beheer**  >  **verlenen** de volgende opties:
   - **Vereisen dat het apparaat moet worden gemarkeerd als compatibel**
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

### <a name="known-behavior"></a>Bekend gedrag

Wanneer u de [code van de OAuth-stroom](../develop/v2-oauth2-device-code.md)van het apparaat gebruikt, wordt het beleid voor het toekennen van beheerde apparaten of de status van een apparaat niet ondersteund. Dit komt doordat het apparaat dat de verificatie uitvoert, de apparaats status niet kan verstrekken aan het apparaat dat een code levert en de apparaatstatus in het token is vergrendeld op het apparaat dat de verificatie uitvoert. Gebruik in plaats daarvan het besturings element multi-factor Authentication-toekenning vereisen.

In Windows 7, iOS, Android, macOS en sommige webbrowsers van derden identificeert het apparaat met behulp van een client certificaat dat is ingericht op het moment dat het apparaat wordt geregistreerd bij Azure AD. Wanneer een gebruiker zich voor het eerst aanmeldt via de browser, wordt de gebruiker gevraagd het certificaat te selecteren. De eind gebruiker moet dit certificaat selecteren om de browser te kunnen blijven gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Evalueer de impact van het beleid voor voorwaardelijke toegang voordat u het inschakelt in de modus alleen rapport](concept-conditional-access-report-only.md).
