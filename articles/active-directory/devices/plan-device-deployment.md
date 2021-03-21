---
title: De implementatie van uw Azure Active Directory-apparaat plannen
description: Kies de strategieën voor het integreren van Azure AD-apparaten die voldoen aan de behoeften van uw organisatie.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc631e4329c1df2cdcbfe57c2b43ccccf14afa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936444"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>De implementatie van uw Azure Active Directory-apparaat plannen

Dit artikel helpt u bij het evalueren van de methoden om uw apparaat te integreren met Azure AD, het implementatie plan te kiezen en de belangrijkste koppelingen naar ondersteunde hulpprogram ma's voor Apparaatbeheer.

De liggende apparaten waarvan uw gebruikers zich aanmelden, worden uitgebreid. Organisaties kunnen Desk tops, laptops, telefoons, Tablets en andere apparaten bieden. Uw gebruikers kunnen hun eigen matrix van apparaten meebrengen en toegang krijgen tot informatie op verschillende locaties. In deze omgeving is uw taak als beheerder om de resources van uw organisatie veilig te laten zijn op alle apparaten.

Met Azure Active Directory (Azure AD) kan uw organisatie voldoen aan deze doel stellingen met het beheer van apparaat-id's. U kunt uw apparaten nu in azure AD ontvangen en deze beheren vanaf een centrale locatie in de [Azure Portal](https://portal.azure.com/). Dit biedt u een uniforme ervaring, verbeterde beveiliging en vermindert de tijd die nodig is voor het configureren van een nieuw apparaat.

Er zijn meerdere methoden om uw apparaten te integreren in azure AD:

* U kunt [apparaten registreren](concept-azure-ad-register.md) bij Azure AD

* [Apparaten toevoegen](concept-azure-ad-join.md) aan Azure AD (alleen Cloud) of

* [Maak een hybride Azure AD-koppeling](concept-azure-ad-join-hybrid.md) tussen apparaten in uw on-premises Active Directory en Azure AD. 

## <a name="learn"></a>Learn

Voordat u begint, moet u ervoor zorgen dat u bekend bent met het [beheer van apparaat-id's](overview.md).

### <a name="benefits"></a>Voordelen

De belangrijkste voor delen van het geven van uw apparaten een Azure AD-identiteit:

* Verhoog de productiviteit – met Azure AD kunnen uw gebruikers [naadloos aanmelden (SSO)](./azuread-join-sso.md) uitvoeren op uw on-premises en cloud resources, waardoor ze productief kunnen zijn, waar ze ook zijn.

* Beveiliging verhogen: met Azure AD-apparaten kunt u [beleid voor voorwaardelijke toegang](../conditional-access/require-managed-devices.md) Toep assen op resources op basis van de identiteit van het apparaat of de gebruiker. Beleid voor voorwaardelijke toegang kan extra beveiliging bieden met behulp van [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md). Het toevoegen van een apparaat aan Azure AD is een vereiste om uw veiligheid te verbeteren met een verificatie strategie zonder [wacht woord](../authentication/concept-authentication-passwordless.md) .

* Verbeter de gebruikers ervaring – met apparaat-id's in azure AD kunt u uw gebruikers eenvoudig toegang bieden tot de cloud resources van uw organisatie op zowel persoonlijke als zakelijke apparaten. Beheerders kunnen [Enterprise State roaming](enterprise-state-roaming-overview.md) voor een uniforme ervaring op alle Windows-apparaten inschakelen.

* Vereenvoudig de implementatie en het beheer van apparaat-Identity Management vereenvoudigt het proces van het naar Azure AD brengen van apparaten met [Windows auto pilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [Bulksgewijs inrichten](/mem/intune/enrollment/windows-bulk-enroll)en [self-service: out of Box Experience (OOBE)](../user-help/user-help-join-device-on-network.md). U kunt deze apparaten beheren met Mobile Device Management (MDM)-hulpprogram ma's zoals [Microsoft intune](/mem/intune/fundamentals/what-is-intune), en hun identiteiten in [Azure Portal](https://portal.azure.com/).

### <a name="training-resources"></a>Trainings bronnen

Video:  [voorwaardelijke toegang met apparaatbesturing](https://youtu.be/NcONUf-jeS4)

Veelgestelde vragen: [Veelgestelde vragen over Azure AD-Apparaatbeheer](faq.md)  en [instellingen en veelgestelde vragen over gegevens roaming](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Houd rekening met de behoeften van uw organisatie terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden](../fundamentals/active-directory-deployment-plans.md) en de rol van belanghebbenden in het project goed begrijpen. 

Voor dit abonnement voegt u de volgende belanghebbenden toe aan uw lijst:

| Rol| Beschrijving |
| - | - |
| Apparaat-beheerder| Een vertegenwoordiger van het team dat kan controleren of het plan voldoet aan de apparaat vereisten van uw organisatie. |
| Netwerk beheerder| Een vertegenwoordiger van het netwerk team die ervoor kan zorgen dat aan de netwerk vereisten wordt voldaan. |
| Team voor Apparaatbeheer| Team dat de inventaris van apparaten beheert. |
| Specifieke beheerders teams van het besturings systeem| Teams die specifieke versies van besturings systemen ondersteunen en beheren. Er kan bijvoorbeeld een Mac-of iOS-team zijn. |

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden u aan de initiële configuratie van uw integratie methode in een test omgeving te hebben of met een kleine groep test apparaten. Bekijk [Aanbevolen procedures voor een pilot](../fundamentals/active-directory-deployment-plans.md).

De hybride implementatie van Azure AD is eenvoudig, en het 100% van de taak van een beheerder zonder dat hiervoor een eind gebruiker actie nood zakelijk is. U kunt een [gecontroleerde validatie van hybride Azure AD-deelname](hybrid-azuread-join-control.md) uitvoeren voordat u deze in de hele organisatie in één keer inschakelt.

## <a name="choose-your-integration-methods"></a>Uw integratie methoden kiezen

Uw organisatie kan meerdere integratie methoden voor apparaten gebruiken in één Azure AD-Tenant. Het doel is om de methode (n) te kiezen die geschikt is om uw apparaten veilig te beheren in azure AD. Er zijn veel para meters die deze beslissing, waaronder eigendom, apparaattypen, primaire doel groep en de infra structuur van uw organisatie, kunnen aansturen.

Aan de hand van de volgende informatie kunt u bepalen welke integratie methoden u wilt gebruiken.

### <a name="decision-tree-for-devices-integration"></a>Beslissings structuur voor apparaten-integratie

Gebruik deze structuur om opties te bepalen voor apparaten die eigendom zijn van de organisatie. 

> [!NOTE]
> BYOD-scenario's (Personal of til your-own Device) worden niet in dit diagram weer geven. Ze resulteren altijd in azure AD-registratie.

 ![Beslissingsstructuur](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Vergelijkings matrix

iOS-en Android-apparaten kunnen alleen worden geregistreerd bij Azure AD. De volgende tabel geeft een hoog niveau van overwegingen voor Windows-client apparaten. Gebruik dit als een overzicht en verken de verschillende integratie methoden uitvoerig.

| Overweging | Azure AD-geregistreerd| Azure AD-koppeling| Hybride Azure AD-koppeling |
| - | - | - | - |
| **Client besturingssystemen**| | |  |
| Windows 10-apparaten| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Windows down level-apparaten (Windows 8,1 of Windows 7)| | | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
|**Opties voor aanmelden**| | |  |
| Lokale referenties voor eind gebruikers| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| |  |
| Wachtwoord| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Pincode voor apparaat| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello voor Bedrijven| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| FIDO 2,0-beveiligings sleutels| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator-app (met wacht woord)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
|**Belangrijkste mogelijkheden**| | |  |
| Eenmalige aanmelding bij cloud resources| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Eenmalige aanmelding voor on-premises resources| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Voorwaardelijke toegang <br> (Vereisen dat apparaten als compatibel worden gemarkeerd) <br> (Moet worden beheerd door MDM)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)|![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
Voorwaardelijke toegang <br>(Hybride Azure AD-apparaten vereisen)| | | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)
| Self-service voor wachtwoord herstel vanuit het Windows-aanmeldings scherm| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| PINCODE voor Windows hello opnieuw instellen| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |
| Enter prise State roaming op alle apparaten| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD-registratie 

Geregistreerde apparaten worden vaak beheerd met [Microsoft intune](/mem/intune/enrollment/device-enrollment). Apparaten worden op een aantal manieren Inge schreven bij intune, afhankelijk van het besturings systeem. 

Geregistreerde Azure AD-apparaten bieden ondersteuning voor het meenemen van uw eigen apparaten (BYOD) en apparaten in bedrijfs eigendom tot SSO met cloud resources. Toegang tot resources is gebaseerd op het beleid voor [voorwaardelijke toegang](../conditional-access/require-managed-devices.md) van Azure AD dat wordt toegepast op het apparaat en de gebruiker.

### <a name="registering-devices"></a>Apparaten registreren

Geregistreerde apparaten worden vaak beheerd met [Microsoft intune](/mem/intune/enrollment/device-enrollment). Apparaten worden op een aantal manieren Inge schreven bij intune, afhankelijk van het besturings systeem. 

BYOD en mobiele apparaten in bedrijfs eigendom worden geregistreerd door gebruikers die de bedrijfs portal-app installeren.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Als het registreren van uw apparaten de beste optie is voor uw organisatie, raadpleegt u de volgende bronnen:

* Dit overzicht van [geregistreerde Azure ad-apparaten](concept-azure-ad-register.md).

* Deze documentatie voor eind gebruikers over [het registreren van uw persoonlijke apparaat in het netwerk van uw organisatie](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Azure AD-koppeling

Met Azure AD-deelname kunt u overstappen op een Cloud-First-model met Windows. Het biedt een fantastische basis als u van plan bent om uw Apparaatbeheer te moderniseren en de IT-kosten te verlagen. Azure AD-deelname werkt alleen met Windows 10-apparaten. Beschouw het als de eerste keuze voor nieuwe apparaten.

Apparaten die zijn [toegevoegd aan Azure AD kunnen echter wel SSO hebben op on-premises resources](azuread-join-sso.md) wanneer ze zich op het netwerk van de organisatie bevinden, kunnen worden geverifieerd bij on-premises servers zoals bestanden, printers en andere toepassingen.

Als dit de beste optie is voor uw organisatie, raadpleegt u de volgende bronnen:

* Dit overzicht van [apparaten die zijn toegevoegd aan Azure AD](concept-azure-ad-join.md).

* Meer vertrouwd zijn met het [Azure AD-implementatie plan voor deelname](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Azure AD-deelname aan uw apparaten inrichten

Als u Azure AD-deelname wilt inrichten, hebt u de volgende methoden:

* Self-service: [Windows 10 First-Run-ervaring](azuread-joined-devices-frx.md)

Als Windows 10 Professional of Windows 10 Enterprise op een apparaat is geïnstalleerd, wordt de uitvoering standaard ingesteld op het installatieproces voor apparaten die eigendom van het bedrijf zijn.

* [Windows out of Box Experience (OOBE) of Windows-instellingen](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Bulk inschrijving](/mem/intune/enrollment/windows-bulk-enroll)

Kies uw implementatie procedure na een zorgvuldige [vergelijking van deze benaderingen](azureadjoin-plan.md).

U kunt bepalen dat Azure AD-deelname de beste oplossing voor een apparaat is, en dat het apparaat mogelijk al een andere status heeft. Hier volgen de overwegingen voor upgrades.

| Huidige Apparaatstatus| Gewenste Apparaatstatus| Uitleg |
| - | - | - |
| On-premises domein toegevoegd| Azure AD-Join| Het apparaat loskoppelen van het on-premises domein voordat u deelneemt aan Azure AD |
| Hybride Azure AD-deelname| Azure AD-Join| Het apparaat loskoppelen van on-premises domein en van Azure AD voordat u deelneemt aan Azure AD |
| Azure AD-geregistreerd| Azure AD-Join| De registratie van het apparaat ongedaan maken voordat u deelneemt aan Azure AD |


## <a name="hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling

Als u een on-premises Active Directory omgeving hebt en u wilt deel nemen aan uw Active Directory-computers die lid zijn van Azure AD, kunt u dit doen met hybride Azure AD join. Het ondersteunt een [breed scala aan Windows-apparaten](hybrid-azuread-join-plan.md), met inbegrip van Windows huidige en Windows-apparaten op lagere niveaus.

De meeste organisaties hebben al lid van een domein en beheren ze via groepsbeleid of System Center Configuration Manager (SCCM). In dat geval kunt u het beste hybride Azure AD-deelname configureren om te beginnen met het verkrijgen van voor delen terwijl u gebruikmaakt van bestaande investeringen.

Als hybride Azure AD-deelname de beste optie is voor uw organisatie, raadpleegt u de volgende bronnen:

* Dit overzicht van [hybride Azure AD gekoppelde apparaten](concept-azure-ad-join-hybrid.md).

* Meer vertrouwd zijn met het [hybride implementatie plan voor deelname aan Azure AD](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Hybride Azure AD-deelname inrichten op uw apparaten

[Controleer uw identiteits infrastructuur](hybrid-azuread-join-plan.md). Azure AD Connect biedt u een wizard voor het configureren van hybride Azure AD-deelname voor:

* [Federatieve domeinen](hybrid-azuread-join-federated-domains.md)

* [Beheerde domeinen](hybrid-azuread-join-managed-domains.md)

Als het installeren van de vereiste versie van Azure AD Connect geen optie voor u is, Zie [How to configure Hybrid Azure AD join's hand matig](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Het on-premises domein dat lid is van een Windows 10-apparaat dat automatisch lid wordt van Azure AD om standaard lid te worden van hybride Azure AD. Dit wordt alleen uitgevoerd als u de juiste omgeving hebt ingesteld. 

U kunt bepalen dat hybride Azure AD-deelname de beste oplossing voor een apparaat is, en dat het apparaat mogelijk al een andere status heeft. Hier volgen de overwegingen voor upgrades.

| Huidige Apparaatstatus| Gewenste Apparaatstatus| Uitleg |
| - | - | - |
| On-premises domein deelname| Hybride Azure AD-deelname| Azure AD Connect of AD FS gebruiken om lid te worden van Azure |
| On-premises werk groep toegevoegd of nieuw| Hybride Azure AD-deelname| Ondersteund met [Windows auto pilot](/windows/deployment/windows-autopilot/windows-autopilot). Anders moet het apparaat een on-premises domein zijn dat is gekoppeld aan een hybride Azure AD join |
| Azure AD-deelname| Hybride Azure AD-deelname| Ontkoppelen van Azure AD, waarmee het wordt geplaatst in de on-premises werk groep of nieuwe status. |
| Azure AD geregistreerd| Hybride Azure AD-deelname| Is afhankelijk van de Windows-versie. [Zie deze overwegingen](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Uw apparaten beheren

Wanneer u uw apparaten hebt geregistreerd of aan Azure AD hebt toegevoegd, gebruikt u de [Azure Portal](https://portal.azure.com/) als centrale locatie voor het beheren van uw apparaat-id's. Op de pagina Azure Active Directory apparaten kunt u het volgende doen:

* [Apparaatinstellingen configureren](device-management-azure-portal.md#configure-device-settings)
* U moet een lokale beheerder zijn om Windows-apparaten te kunnen beheren. [Azure AD werkt dit lidmaatschap bij van aan Azure AD gekoppelde apparaten](assign-local-admin.md)en voegt deze automatisch toe met de rol Apparaatbeheer als Administrators voor alle gekoppelde apparaten.

Zorg ervoor dat u de omgeving schoon blijft door [verouderde apparaten te beheren](manage-stale-devices.md)en uw resources te richten op het beheren van de huidige apparaten.

* [Audit logboeken met betrekking tot het apparaat controleren](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Ondersteunde hulpprogram ma's voor Apparaatbeheer

Beheerders kunnen deze geregistreerde en gekoppelde apparaten beveiligen en verder beheren met extra hulpprogram ma's voor Apparaatbeheer. Deze hulpprogram ma's bieden een manier om organisatie-vereiste configuraties af te dwingen, zoals het vereisen van een versleutelde opslag, wachtwoord complexiteit, software-installatie en software-updates. 

Ondersteunde en niet-ondersteunde platforms voor geïntegreerde apparaten bekijken:

| Hulpprogramma’s voor apparaatbeheer| Azure AD-geregistreerd| Azure AD-koppeling| Hybride Azure AD-koppeling|
| - | - | - | - |
| [Beheer van mobiele apparaten (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Voor beeld: Microsoft Intune| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)|  |
| [Co-beheer met Microsoft Intune en micro soft-eind punt Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 en hoger)| | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)| ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)|  |
| [Groepsbeleid](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Alleen Windows)| | | ![Vinkje voor deze waarden.](./media/plan-device-deployment/check.png)|  |



 U wordt aangeraden [Microsoft intune Mobile Application Management (mam)](/mem/intune/apps/app-management) met of zonder Apparaatbeheer te controleren op geregistreerde IOS-of Android-apparaten.

 Beheerders kunnen ook [VDI-platforms (Virtual Desktop Infrastructure)](howto-device-identity-virtual-desktop-infrastructure.md) die als host fungeren voor Windows-besturings systemen in hun organisaties, gebruiken om het beheer te stroom lijnen en de kosten te verlagen door consolidatie en gecentraliseering van resources. 

### <a name="troubleshoot-device-identities"></a>Problemen met apparaatidentiteiten oplossen

* [Problemen met apparaten oplossen met behulp van de dsregcmd-opdracht](troubleshoot-device-dsregcmd.md)

* [Problemen met Enterprise State Roaming instellingen in Azure Active Directory oplossen](enterprise-state-roaming-troubleshooting.md)

Als er problemen zijn met het voltooien van de hybride Azure AD-koppeling voor domein-gekoppelde Windows-apparaten, raadpleegt u:

* [Problemen met hybride Azure AD-koppeling oplossen voor actuele Windows-apparaten](troubleshoot-hybrid-join-windows-current.md)

* [Problemen met hybride Azure AD-deelname voor Windows-apparaten op lagere niveaus oplossen](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

* [De implementatie van Azure AD-deelname plannen](azureadjoin-plan.md)
* [Uw hybride implementatie van Azure AD-deelname plannen](hybrid-azuread-join-plan.md)
* [Apparaatidentiteiten beheren](device-management-azure-portal.md)