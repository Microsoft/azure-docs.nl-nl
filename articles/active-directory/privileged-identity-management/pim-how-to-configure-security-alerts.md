---
title: Beveiligings waarschuwingen voor Azure AD-rollen in PIM-Azure AD | Microsoft Docs
description: Beveiligings waarschuwingen configureren voor Azure AD-rollen Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b519ce11747ab3374d9bd286800a6c93129019
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565228"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Beveiligings waarschuwingen configureren voor Azure AD-rollen in Privileged Identity Management

Privileged Identity Management (PIM) genereert waarschuwingen wanneer er verdachte of onveilige activiteit is in uw Azure Active Directory-organisatie (Azure AD). Wanneer een waarschuwing wordt geactiveerd, wordt deze weer gegeven op het Privileged Identity Management dash board. Selecteer de waarschuwing om een rapport weer te geven met een lijst met de gebruikers of rollen die de waarschuwing hebben geactiveerd.

## <a name="determine-your-version-of-pim"></a>Uw versie van PIM bepalen

Vanaf november 2019 wordt het gedeelte van de Azure AD-functies van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen van Azure-resource rollen. Hiermee maakt u aanvullende functies en [wijzigingen in de bestaande API](azure-ad-roles-features.md#api-changes). Terwijl de nieuwe versie wordt geïmplementeerd, zijn de procedures die u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management weet, kunt u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een gebruiker die zich in de beheerdersrol met [geprivilegieerde rol](../roles/permissions-reference.md#privileged-role-administrator) bevindt.
1. Open **Azure AD privileged Identity Management**. Als u een banner aan de bovenkant van de overzichts pagina hebt, volgt u de instructies op het tabblad **nieuwe versie** van dit artikel. Als dat niet het geval is, volgt u de instructies op het tabblad **vorige versie** .

  [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel om beveiligings waarschuwingen voor Azure AD-rollen te onderzoeken.

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

![Scherm afbeelding waarin de pagina waarschuwingen wordt weer gegeven met een lijst met waarschuwingen en de ernst ervan.](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

In deze sectie vindt u alle beveiligings waarschuwingen voor Azure AD-rollen, samen met informatie over hoe u kunt voor komen. Ernst heeft de volgende betekenis:

- **Hoog**: vereist direct actie vanwege een beleids schending.
- **Medium**: vereist geen onmiddellijke actie, maar signaleert een mogelijke beleids schending.
- **Low**: vereist geen onmiddellijke actie, maar stelt een voor keur voor een wijziging van het beleid voor.

### <a name="administrators-arent-using-their-privileged-roles"></a>Beheerders gebruiken hun geprivilegieerde rollen niet

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Gebruikers aan wie een Privileged is toegewezen, zijn niet meer nodig om de kans op een aanval te verhogen. Het is ook gemakkelijker voor kwaadwillende personen om niet te worden opgemerkt in accounts die niet actief worden gebruikt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder ze uit geprivilegieerde rollen die ze niet nodig hebben. |
| **Preventie** | Ken alleen geprivilegieerde rollen toe aan gebruikers die een zakelijke reden hebben. </br>Plan regel matige [toegangs beoordelingen](pim-how-to-start-security-review.md) om te controleren of gebruikers nog steeds hun toegang nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als een gebruiker het opgegeven aantal dagen overschrijdt zonder een rol te activeren. |
| **Aantal dagen** | Met deze instelling bepaalt u het maximum aantal dagen van 0 tot 100 dat een gebruiker kan door gaan zonder een rol te activeren.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Voor rollen is multi-factor Authentication niet vereist voor activering

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Zonder multi-factor Authentication kunnen gemanipuleerde gebruikers bevoegde rollen activeren. |
| **Hoe kunt u het probleem oplossen?** | Bekijk de lijst met rollen en [vereis multi-factor Authentication](pim-how-to-change-default-settings.md) voor elke rol. |
| **Preventie** | [Vereis MFA vereisen](pim-how-to-change-default-settings.md) voor elke rol.  |
| **Actie voor het verkleinen van de portal** | Hiermee wordt multi-factor Authentication vereist voor de activering van de bevoorrechte rol. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>De organisatie heeft geen Azure AD Premium P2

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | De huidige Azure AD-organisatie heeft geen Azure AD Premium P2. |
| **Hoe kunt u het probleem oplossen?** | Lees de informatie over [Azure AD-edities](../fundamentals/active-directory-whatis.md). Voer een upgrade uit naar Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Mogelijke verlopen accounts in een geprivilegieerde rol

Ernst: **gemiddeld**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Accounts in een geprivilegieerde rol hebben in de afgelopen 90 dagen geen wacht woord gewijzigd. Deze accounts zijn mogelijk service of gedeelde accounts die niet worden onderhouden en kwetsbaar zijn voor aanvallen. |
| **Hoe kunt u het probleem oplossen?** | Controleer de accounts in de lijst. Als ze geen toegang meer nodig hebben, moet u ze verwijderen uit hun geprivilegieerde rollen. |
| **Preventie** | Zorg ervoor dat accounts die worden gedeeld sterke wacht woorden draaien wanneer er een wijziging is in de gebruikers die het wacht woord kennen. </br>Controleer regel matig accounts met geprivilegieerde rollen met [toegangs beoordelingen](pim-how-to-start-security-review.md) en verwijder roltoewijzingen die niet meer nodig zijn. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Aanbevolen procedures** | Gedeelde, service-en toegangs accounts voor toegang die worden geverifieerd met een wacht woord en die zijn toegewezen aan streng beschermde administratieve rollen, zoals globale beheerder of beveiligings beheerder, moeten hun wacht woorden draaien voor de volgende gevallen:<ul><li>Na een beveiligings incident met betrekking tot misbruik of inbreuk op beheerders toegangs rechten</li><li>Nadat de bevoegdheden van een gebruiker zijn gewijzigd, zodat ze geen beheerder meer zijn (bijvoorbeeld nadat een werk nemer die een beheerder is, de organisatie verlaat of verlaat)</li><li>Met regel matige intervallen (bijvoorbeeld elk kwar taal of per jaar), zelfs als er geen sprake is van een onbekende schending of wijziging van IT-personeel</li></ul>Omdat meerdere personen toegang hebben tot de referenties van deze accounts, moeten de referenties worden geroteerd om ervoor te zorgen dat mensen die hun rollen hebben verlaten, geen toegang meer hebben tot de accounts. [Meer informatie over het beveiligen van accounts](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen worden buiten Privileged Identity Management toegewezen

Ernst: **hoog**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Geprivilegieerde roltoewijzingen buiten Privileged Identity Management worden niet op de juiste wijze gecontroleerd en kunnen duiden op een actieve aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder deze uit de geprivilegieerde rollen die buiten Privileged Identity Management zijn toegewezen. |
| **Preventie** | Onderzoek waar gebruikers geprivilegieerde rollen worden toegewezen buiten Privileged Identity Management en de volgende toewijzingen van daaruit niet kunnen verbieden. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u de gebruiker uit hun geprivilegieerde rol. |

### <a name="there-are-too-many-global-administrators"></a>Er zijn te veel globale beheerders

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | De globale beheerder is de rol met de hoogste bevoegdheden. Als een globale beheerder is aangetast, krijgt de aanvaller toegang tot alle machtigingen, waardoor het hele systeem risico loopt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder alle die de rol globale beheerder niet absoluut nodig hebben. </br>Wijs in plaats daarvan beperkte rollen toe aan deze gebruikers. |
| **Preventie** | Wijs gebruikers de minst bevoegde rol toe die ze nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als aan twee verschillende criteria wordt voldaan en u deze beide kunt configureren. Eerst moet u een bepaalde drempel waarde van globale beheerdersrol bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders zijn. Als u slechts een van deze metingen voldoet, wordt de waarschuwing niet weer gegeven. |
| **Minimum aantal globale beheerders** | Met deze instelling bepaalt u het aantal globale beheerdersrol, van 2 tot 100, die u overweegt te weinig voor uw Azure AD-organisatie. |
| **Percentage van globale beheerders** | Met deze instelling bepaalt u het minimum percentage van beheerders die globale beheerders zijn, van 0% tot 100%, waaronder u niet wilt dat uw Azure AD-organisatie dip. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen worden te vaak geactiveerd

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Meerdere activeringen naar dezelfde bevoorrechte rol door dezelfde gebruiker is een aantekening van een aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor de bijbehorende rol lang genoeg is ingesteld om hun taken uit te voeren. |
| **Preventie** | Zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen lang genoeg is ingesteld voor gebruikers om hun taken uit te voeren.</br>[Multi-factor Authentication vereisen](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen met accounts die worden gedeeld door meerdere beheerders. |
| **Actie voor het verkleinen van de portal** | N.v.t. |
| **Trigger** | Wordt geactiveerd als een gebruiker dezelfde geprivilegieerde rol meerdere keren binnen een opgegeven periode activeert. U kunt zowel de tijds periode als het aantal activeringen configureren. |
| **Periode activering verlenging** | Met deze instelling geeft u in dagen, uren, minuten en seconden de periode op die u wilt gebruiken voor het bijhouden van verdachte vernieuwingen. |
| **Aantal activerings verlengingen** | Met deze instelling bepaalt u het aantal activeringen, van 2 tot 100, op basis waarvan u een melding wilt ontvangen binnen het tijds bestek dat u hebt gekozen. U kunt deze instelling wijzigen door de schuif regelaar te verplaatsen of door een getal in het tekstvak te typen. |

## <a name="customize-security-alert-settings"></a>Instellingen voor beveiligings waarschuwingen aanpassen

Selecteer op de pagina **waarschuwingen** de optie **instellingen**.

![Pagina waarschuwingen met gemarkeerde instellingen](media/pim-how-to-configure-security-alerts/alert-settings.png)

Pas de instellingen voor de verschillende waarschuwingen aan om te werken met uw omgeving en beveiligings doelen.

![Pagina instellen voor een waarschuwing om instellingen in te scha kelen en te configureren](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

![Azure AD-rollen: waarschuwing voor vermelding in waarschuwings venster en ernst](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Details van beveiligingswaarschuwing

In deze sectie vindt u alle beveiligings waarschuwingen voor Azure AD-rollen, samen met informatie over hoe u kunt voor komen. Ernst heeft de volgende betekenis:

- **Hoog**: vereist direct actie vanwege een beleids schending.
- **Medium**: vereist geen onmiddellijke actie, maar signaleert een mogelijke beleids schending.
- **Low**: vereist geen onmiddellijke actie, maar stelt een voor keur voor een wijziging van het beleid voor.

### <a name="administrators-arent-using-their-privileged-roles"></a>Beheerders gebruiken hun geprivilegieerde rollen niet

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Gebruikers aan wie een Privileged is toegewezen, zijn niet meer nodig om de kans op een aanval te verhogen. Het is ook gemakkelijker voor kwaadwillende personen om niet te worden opgemerkt in accounts die niet actief worden gebruikt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder ze uit geprivilegieerde rollen die ze niet nodig hebben. |
| **Preventie** | Ken alleen geprivilegieerde rollen toe aan gebruikers die een zakelijke reden hebben. </br>Plan regel matige [toegangs beoordelingen](pim-how-to-start-security-review.md) om te controleren of gebruikers nog steeds hun toegang nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als een gebruiker het opgegeven aantal dagen overschrijdt zonder een rol te activeren. |
| **Aantal dagen** | Met deze instelling bepaalt u het maximum aantal dagen van 0 tot 100 dat een gebruiker kan door gaan zonder een rol te activeren.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Voor rollen is multi-factor Authentication niet vereist voor activering

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Zonder multi-factor Authentication kunnen gemanipuleerde gebruikers bevoegde rollen activeren. |
| **Hoe kunt u het probleem oplossen?** | Bekijk de lijst met rollen en [vereis multi-factor Authentication](pim-how-to-change-default-settings.md) voor elke rol. |
| **Preventie** | [Vereis MFA vereisen](pim-how-to-change-default-settings.md) voor elke rol.  |
| **Actie voor het verkleinen van de portal** | Hiermee wordt multi-factor Authentication vereist voor de activering van de bevoorrechte rol. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>De organisatie heeft geen Azure AD Premium P2

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | De huidige Azure AD-organisatie heeft geen Azure AD Premium P2. |
| **Hoe kunt u het probleem oplossen?** | Lees de informatie over [Azure AD-edities](../fundamentals/active-directory-whatis.md). Voer een upgrade uit naar Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Mogelijke verlopen accounts in een geprivilegieerde rol

Ernst: **gemiddeld**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Accounts in een geprivilegieerde rol hebben in de afgelopen 90 dagen geen wacht woord gewijzigd. Deze accounts zijn mogelijk service of gedeelde accounts die niet worden onderhouden en kwetsbaar zijn voor aanvallen. |
| **Hoe kunt u het probleem oplossen?** | Controleer de accounts in de lijst. Als ze geen toegang meer nodig hebben, moet u ze verwijderen uit hun geprivilegieerde rollen. |
| **Preventie** | Zorg ervoor dat accounts die worden gedeeld sterke wacht woorden draaien wanneer er een wijziging is in de gebruikers die het wacht woord kennen. </br>Controleer regel matig accounts met geprivilegieerde rollen met [toegangs beoordelingen](pim-how-to-start-security-review.md) en verwijder roltoewijzingen die niet meer nodig zijn. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Aanbevolen procedures** | Gedeelde, service-en toegangs accounts voor toegang die worden geverifieerd met een wacht woord en die zijn toegewezen aan streng beschermde administratieve rollen, zoals globale beheerder of beveiligings beheerder, moeten hun wacht woorden draaien voor de volgende gevallen:<ul><li>Na een beveiligings incident met betrekking tot misbruik of inbreuk op beheerders toegangs rechten</li><li>Nadat de bevoegdheden van een gebruiker zijn gewijzigd, zodat ze geen beheerder meer zijn (bijvoorbeeld nadat een werk nemer die een beheerder is, de organisatie verlaat of verlaat)</li><li>Met regel matige intervallen (bijvoorbeeld elk kwar taal of per jaar), zelfs als er geen sprake is van een onbekende schending of wijziging van IT-personeel</li></ul>Omdat meerdere personen toegang hebben tot de referenties van deze accounts, moeten de referenties worden geroteerd om ervoor te zorgen dat mensen die hun rollen hebben verlaten, geen toegang meer hebben tot de accounts. [Meer informatie](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen worden buiten Privileged Identity Management toegewezen

Ernst: **hoog**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Geprivilegieerde roltoewijzingen buiten Privileged Identity Management worden niet op de juiste wijze gecontroleerd en kunnen duiden op een actieve aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder deze uit de geprivilegieerde rollen die buiten Privileged Identity Management zijn toegewezen. |
| **Preventie** | Onderzoek waar gebruikers geprivilegieerde rollen worden toegewezen buiten Privileged Identity Management en de volgende toewijzingen van daaruit niet kunnen verbieden. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u de gebruiker uit hun geprivilegieerde rol. |

### <a name="there-are-too-many-global-administrators"></a>Er zijn te veel globale beheerders

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | De globale beheerder is de rol met de hoogste bevoegdheden. Als een globale beheerder is aangetast, krijgt de aanvaller toegang tot alle machtigingen, waardoor het hele systeem risico loopt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder alle die de rol globale beheerder niet absoluut nodig hebben. </br>Wijs in plaats daarvan beperkte rollen toe aan deze gebruikers. |
| **Preventie** | Wijs gebruikers de minst bevoegde rol toe die ze nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als aan twee verschillende criteria wordt voldaan en u deze beide kunt configureren. Eerst moet u een bepaalde drempel waarde van globale beheerders bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders zijn. Als u slechts een van deze metingen voldoet, wordt de waarschuwing niet weer gegeven. |
| **Minimum aantal globale beheerders** | Met deze instelling bepaalt u het aantal globale beheerders, van 2 tot en met 100, die u overweegt te weinig voor uw Azure AD-organisatie. |
| **Percentage van globale beheerders** | Met deze instelling bepaalt u het minimum percentage van beheerders die globale beheerders zijn, van 0% tot 100%, waaronder u niet wilt dat uw Azure AD-organisatie dip. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen worden te vaak geactiveerd

Ernst: **laag**

| | Description |
| --- | --- |
| **Waarom krijg ik deze waarschuwing?** | Meerdere activeringen naar dezelfde bevoorrechte rol door dezelfde gebruiker is een aantekening van een aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor de bijbehorende rol lang genoeg is ingesteld om hun taken uit te voeren. |
| **Preventie** | Zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen lang genoeg is ingesteld voor gebruikers om hun taken uit te voeren.</br>[Multi-factor Authentication vereisen](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen met accounts die worden gedeeld door meerdere beheerders. |
| **Actie voor het verkleinen van de portal** | N.v.t. |
| **Trigger** | Wordt geactiveerd als een gebruiker dezelfde geprivilegieerde rol meerdere keren binnen een opgegeven periode activeert. U kunt zowel de tijds periode als het aantal activeringen configureren. |
| **Periode activering verlenging** | Met deze instelling geeft u in dagen, uren, minuten en seconden de periode op die u wilt gebruiken voor het bijhouden van verdachte vernieuwingen. |
| **Aantal activerings verlengingen** | Met deze instelling bepaalt u het aantal activeringen, van 2 tot 100, op basis waarvan u een melding wilt ontvangen binnen het tijds bestek dat u hebt gekozen. U kunt deze instelling wijzigen door de schuif regelaar te verplaatsen of door een getal in het tekstvak te typen. |

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligings waarschuwingen configureren

U kunt enkele van de beveiligings waarschuwingen in Privileged Identity Management aanpassen om te werken met de behoeften van uw organisatie en de beveiligings doelen. Volg deze stappen om de instellingen voor beveiligings waarschuwingen te openen:

1. Open **privileged Identity Management** in azure AD.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **instellingen** en vervolgens op **waarschuwingen**.

    ![Azure AD-rollen: instellingen waarvoor waarschuwingen zijn geselecteerd](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecteer een naam voor de waarschuwing om de instelling voor die waarschuwing te configureren.

    ![Voor de geselecteerde waarschuwing, het deel venster instellingen voor beveiligings waarschuwingen](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-functies configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)