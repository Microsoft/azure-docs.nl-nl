---
title: Gebruikers maken en beheren
description: Gebruikers van Sens oren en de on-premises beheer console maken en beheren. Aan gebruikers kan de rol van beheerder, beveiligings analist of alleen-lezen gebruiker worden toegewezen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/03/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: dff379c99fa7383c7f7844cf8d195a345e88a335
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466266"
---
# <a name="about-defender-for-iot-console-users"></a>Over Defender voor IoT-console gebruikers

In dit artikel wordt beschreven hoe u gebruikers van Sens oren en de on-premises beheer console maakt en beheert. Gebruikers rollen omvatten Administrator, beveiligings analist of alleen-lezen gebruiker. Elke rol is gekoppeld aan een reeks machtigingen voor hulpprogram ma's voor de sensor of on-premises beheer console. Rollen zijn ontworpen om granulaire, veilige toegang tot Azure Defender voor IoT te vergemakkelijken.

Er zijn ook functies beschikbaar voor het bijhouden van gebruikers activiteiten en het inschakelen van Active Directory-aanmelding.

Elke sensor en on-premises beheer console wordt standaard geïnstalleerd met een Cyber-gebruiker *en een ondersteunings* gebruikers. Deze gebruikers hebben toegang tot geavanceerde hulpprogram ma's voor probleem oplossing en installatie. Gebruikers met beheerders rechten moeten zich aanmelden met deze gebruikers referenties, een beheerders gebruiker maken en vervolgens extra gebruikers maken voor beveiligings analisten en alleen-lezen gebruikers.

## <a name="role-based-permissions"></a>Op rollen gebaseerde machtigingen
De volgende gebruikers rollen zijn beschikbaar:

- **Alleen**-lezen: alleen-lezen gebruikers voeren taken uit, zoals het weer geven van waarschuwingen en apparaten op de apparaattoewijzing. Deze gebruikers hebben toegang tot opties die worden weer gegeven onder **Navigatie**.

- **Beveiligings analist**: beveiligings analisten hebben alleen-lezen gebruikers machtigingen. Ze kunnen ook acties uitvoeren op apparaten, waarschuwingen bevestigen en onderzoek hulpprogramma's gebruiken. Deze gebruikers hebben toegang tot de opties die worden weer gegeven onder **Navigatie** en **analyse**.

- **Beheerder**: beheerders hebben toegang tot alle hulpprogram ma's, waaronder het definiëren van systeem configuraties, het maken en beheren van gebruikers, en nog veel meer. Deze gebruikers hebben toegang tot opties die worden weer gegeven onder **Navigatie**, **analyse** en **beheer**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Op rollen gebaseerde machtigingen voor on-premises beheer console-hulpprogram ma's

In deze sectie worden de machtigingen beschreven die beschikbaar zijn voor beheerders, beveiligings analisten en alleen-lezen gebruikers voor de on-premises beheer console.  

| Machtiging | Alleen-lezen | Beveiligings analist | Beheerder |
|--|--|--|--|
| De Enter prise-kaart weer geven en filteren | ✓ | ✓ | ✓ |
| Een site bouwen |  |  | ✓ |
| Een site beheren (zones toevoegen en bewerken) |  |  | ✓ |
| Inventaris van apparaten weer geven en filteren | ✓ | ✓ | ✓ |
| Waarschuwingen weer geven en beheren: erkennen, leren en vastmaken | ✓ | ✓ | ✓ |
| Rapporten genereren |  | ✓ | ✓ |
| Rapporten over risico analyse weer geven |  | ✓ | ✓ |
| Uitsluitingen van waarschuwingen instellen |  | ✓ | ✓ |
| Toegangs groepen weer geven of definiëren |  |  | ✓ |
| Systeem instellingen beheren |  |  | ✓ |
| Gebruikers beheren |  |  | ✓ |
| Waarschuwings gegevens naar partners verzenden |  |  | ✓ |
| Certificaten beheren |  |  | ✓ |
| Sessietime-out wanneer gebruikers niet actief zijn | 30 minuten | 30 minuten  | 30 minuten  |

#### <a name="assign-users-to-access-groups"></a>Gebruikers toewijzen aan toegangs groepen

Beheerders kunnen gebruikers toegangs beheer in Defender voor IoT verbeteren door gebruikers toe te wijzen aan specifieke *toegangs groepen*. Toegangs groepen worden toegewezen aan zones, sites, regio's en bedrijfs eenheden waar een sensor zich bevindt. Door gebruikers toe te wijzen om toegang te krijgen tot groepen, krijgen beheerders specifieke controle over waar gebruikers apparaat detecties beheren en analyseren. 

Op deze manier kunt u grote organisaties gebruiken waar gebruikers machtigingen complex zijn of kunnen worden bepaald door een globaal beveiligings beleid van de organisatie. Zie [globaal toegangs beheer definiëren](how-to-define-global-user-access-control.md)voor meer informatie.

### <a name="role-based-permissions-to-sensor-tools"></a>Op rollen gebaseerde machtigingen voor sensor hulpprogramma's

In deze sectie worden de machtigingen beschreven die beschikbaar zijn voor sensor beheerders, beveiligings analisten en alleen-lezen gebruikers.  

| Machtiging | Alleen-lezen | Beveiligings analist | Beheerder |
|--|--|--|--|
| Het dashboard weergeven | ✓ | ✓ | ✓ |
| Zoom weergave van kaart instellen |  |  | ✓ |
| Waarschuwingen weergeven | ✓ | ✓ | ✓ |
| Waarschuwingen beheren: erkennen, leren en vastmaken |  | ✓ | ✓ |
| Gebeurtenissen in een tijd lijn weer geven |  | ✓ | ✓ |
| Apparaten toestaan, bekende scan apparaten, programmeer apparaten |  | ✓ | ✓ |
| Onderzoek gegevens weer geven | ✓ | ✓ | ✓ |
| Systeem instellingen beheren |  |  | ✓ |
| Gebruikers beheren |  |  | ✓ |
| DNS-servers voor reverse lookup |  |  | ✓ |
| Waarschuwings gegevens naar partners verzenden |  | ✓ | ✓ |
| Waarschuwings opmerkingen maken |  | ✓ | ✓ |
| Geschiedenis van de wijziging van het programma weer geven | ✓ | ✓ | ✓ |
| Aangepaste waarschuwings regels maken |  | ✓ | ✓ |
| Meerdere meldingen tegelijk beheren |  | ✓ | ✓ |
| Certificaten beheren |  |  | ✓ |
| Sessietime-out wanneer gebruikers niet actief zijn | 30 minuten | 30 minuten | 30 minuten |

## <a name="define-users"></a>Gebruikers definiëren

In deze sectie wordt beschreven hoe u gebruikers definieert. Gebruikers van cyberx, ondersteuning en beheerders kunnen andere gebruikers definities toevoegen, verwijderen en bijwerken.

Een gebruiker definiëren:

1. Selecteer in het linkerdeel venster voor de sensor of de on-premises beheer console de optie **gebruikers**.
1. In het venster **gebruikers** selecteert u **gebruiker maken**.
1. Definieer de volgende para meters in het deel venster **gebruiker maken** :

   - **Gebruikers naam**: Voer een gebruikers naam in.
   - **E-mail**: Voer het e-mail adres van de gebruiker in.
   - **Voor naam**: Voer de voor naam van de gebruiker in.
   - **Achternaam**: Voer de achternaam van de gebruiker in.
   - **Rol**: Definieer de rol van de gebruiker. Zie [machtigingen op basis van rollen](#role-based-permissions).
   - **Toegangs groep**: als u een gebruiker voor de on-premises beheer console maakt, definieert u de toegangs groep van de gebruiker. Zie [globaal toegangs beheer definiëren](how-to-define-global-user-access-control.md).
   - **Wacht woord**: Selecteer het gebruikers type als volgt:
     - **Lokale gebruiker**: Definieer een wacht woord voor de gebruiker van een sensor of een on-premises beheer console. Het wacht woord moet uit ten minste zes tekens bestaan en mag alleen letters en cijfers bevatten.
     - **Active Directory gebruiker**: u kunt gebruikers toestaan zich aan te melden bij de sensor of beheer console met behulp van Active Directory referenties. Gedefinieerde Active Directory groepen kunnen worden gekoppeld aan specifieke machtigings niveaus. U kunt bijvoorbeeld een specifieke Active Directory groep configureren en alle gebruikers in de groep toewijzen aan het gebruikers type met het kenmerk alleen-lezen.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Uw gebruikers beheren.":::

## <a name="user-session-timeout"></a>Time-out van gebruikers sessie

Als gebruikers gedurende een bepaalde tijd niet actief zijn op het toetsen bord of de muis, worden ze afgemeld bij hun sessie en moeten ze zich opnieuw aanmelden.

Wanneer gebruikers gedurende een periode van 30 minuten niet hebben gewerkt met de muis of het toetsen bord van de console, wordt een afmelding van de sessie geforceerd.

Deze functie is standaard ingeschakeld en bij een upgrade, maar kan worden uitgeschakeld. Daarnaast kunnen sessie tellings tijden worden bijgewerkt. Sessie tijden worden in seconden gedefinieerd. Definities worden toegepast per sensor en on-premises beheer console.

Er wordt een sessietime-out-bericht weer gegeven in de console wanneer de time-out voor inactiviteit is verstreken.

### <a name="control-inactivity-sign-out"></a>Uitloggen van inactiviteit controleren

Gebruikers met beheerders rechten kunnen afmeldingen van inactiviteit in-en uitschakelen en de drempel waarden voor inactiviteit aanpassen.

Voor toegang tot de opdracht:

1. Meld u aan bij de CLI voor de sensor of on-premises beheer console met behulp van Defender voor IoT-beheerders referenties.

1. Voer `sudo nano /var/cyberx/properties/authentication` in.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Als u de functie wilt uitschakelen, wijzigt `infinity_session_expiration = true` u in `infinity_session_expiration = false` .

Als u tellings perioden wilt bijwerken, wijzigt `= <number>` u de waarde in de gewenste tijd.

## <a name="track-user-activity"></a>Gebruikers activiteit volgen 

U kunt de gebruikers activiteit volgen in de tijd lijn van de gebeurtenis op elke sensor. De tijd lijn geeft het gebeurtenis-of betrokken apparaat weer en de tijd en datum waarop de gebruiker de activiteit heeft uitgevoerd.

Gebruikers activiteiten weer geven:

1. Meld u aan bij de sensor.
1. Schakel de optie **gebruikers bewerkingen** in de tijd lijn van de gebeurtenis in. 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="De activiteit van een gebruiker weer geven.":::

## <a name="integrate-with-active-directory-servers"></a>Integreren met Active Directory-servers 

Configureer de sensor of on-premises beheer console om met Active Directory te werken. Hiermee kunnen Active Directory gebruikers toegang krijgen tot de Defender voor IoT-consoles met behulp van hun Active Directory referenties.

Er worden twee typen verificatie op basis van LDAP ondersteund:

- **Volledige verificatie**: de gebruikers gegevens worden opgehaald van de LDAP-server. Voor beelden zijn de voor naam, achternaam, e-mail adres en gebruikers machtigingen.

- **Vertrouwde gebruiker**: alleen het gebruikers wachtwoord wordt opgehaald. Andere gebruikers gegevens die worden opgehaald, zijn gebaseerd op gebruikers die zijn gedefinieerd in de sensor.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory en Defender voor IoT-machtigingen

U kunt Active Directory groepen die hier zijn gedefinieerd, koppelen met specifieke machtigings niveaus. U kunt bijvoorbeeld een specifieke Active Directory groep configureren en alleen-lezen machtigingen toewijzen aan alle gebruikers in de groep.

Active Directory configureren:

1. Selecteer **systeem instellingen** in het linkerdeel venster.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Bekijk de instellingen van uw Active Directory-systeem.":::

2. Selecteer **Active Directory** in het deel venster **systeem instellingen** .

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Bewerk uw Active Directory configuraties.":::

3. Selecteer **Active Directory integratie ingeschakeld** opslaan in het dialoog venster **Active Directory configuratie bewerken**  >  . Het dialoog venster **Active Directory configuratie bewerken** wordt uitgevouwen en u kunt nu de para meters voor het configureren van Active Directory opgeven.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Voer de para meters in om Active Directory te configureren.":::

    > [!NOTE]
    > - U moet de LDAP-para meters hier precies definiëren als ze worden weer gegeven in Active Directory.
    > - Gebruik alleen kleine letters voor alle para meters Active Directory. Gebruik kleine letters, zelfs wanneer de configuraties in Active Directory hoofd letters gebruiken.
    > - U kunt niet zowel LDAP als LDAPS configureren voor hetzelfde domein. U kunt echter beide voor verschillende domeinen tegelijk gebruiken.

4. Stel de Active Directory server-para meters als volgt in:

   | Server parameter | Beschrijving |
   |--|--|
   | FQDN van domein controller | Stel de Fully Qualified Domain Name (FQDN) precies zo in als deze op uw LDAP-server wordt weer gegeven. Voer bijvoorbeeld `host1.subdomain.domain.com` in. |
   | Domein controller poort | Definieer de poort waarop uw LDAP is geconfigureerd. |
   | Primair domein | Stel de domein naam (bijvoorbeeld `subdomain.domain.com` ) en het verbindings type in op basis van uw LDAP-configuratie. |
   | Active Directory groepen | Geef de groeps namen op die zijn gedefinieerd in uw Active Directory configuratie op de LDAP-server. |
   | Vertrouwde domeinen | Als u een vertrouwd domein wilt toevoegen, voegt u de domein naam en het verbindings type van een vertrouwd domein toe. <br />U kunt alleen vertrouwde domeinen configureren voor gebruikers die zijn gedefinieerd onder gebruikers. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Active Directory-groepen voor de on-premises beheer console

Als u Active Directory groepen maakt voor gebruikers van on-premises beheer console, moet u een regel voor een toegangs groep maken voor elke Active Directory groep. De on-premises beheer console Active Directory referenties werken niet als er geen regel voor een toegangs groep bestaat voor de Active Directory gebruikers groep. Zie [globaal toegangs beheer definiëren](how-to-define-global-user-access-control.md).

1. Selecteer **Opslaan**.

2. Selecteer **server toevoegen** en een andere server configureren om een vertrouwde server toe te voegen.

## <a name="resetting-passwords"></a>Wacht woorden opnieuw instellen

### <a name="cyberx-or-support-user"></a>Gebruiker van cyberx of ondersteuning

Alleen de gebruiker met **cyberx** en **ondersteuning** heeft toegang tot de functie voor **wachtwoord herstel** . Als de gebruiker van **cyberx** of **ondersteuning** het wacht woord heeft verg eten, kunnen ze het wacht woord opnieuw instellen via de optie voor **wachtwoord herstel** op de aanmeldings pagina voor Defender voor IOT.

Het wacht woord opnieuw instellen voor een gebruiker met Cyberx of ondersteuning:

1. Selecteer in het aanmeldings scherm van Defender voor IoT de optie  **wacht woord herstellen**. Het scherm voor **wachtwoord herstel** wordt geopend.

1. Selecteer **cyberx** of **support** en kopieer de unieke id.

1. Ga naar de Azure Portal en selecteer **sites en Sens oren**.  

1. Selecteer het pictogram **abonnement filter** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  op de bovenste werk balk en selecteer het abonnement waarmee uw sensor is verbonden.

1. Selecteer het tabblad **wacht woord van on-premises beheer console herstellen** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecteer de knop on-premises beheer herstellen om het herstel bestand te downloaden.":::

1. Voer de unieke id in die u hebt ontvangen op het scherm voor **wachtwoord herstel** en selecteer **herstellen**. Het `password_recovery.zip` bestand wordt gedownload.

    > [!NOTE]
    > Wijzig het wachtwoord herstel bestand niet. Het is een ondertekend bestand dat niet werkt als u ermee knoeit.

1. Selecteer **uploaden** in het scherm **wacht woord herstellen** . **Het venster bestand voor wachtwoord herstel uploaden** wordt geopend.

1. Selecteer **Bladeren** om het bestand te zoeken `password_recovery.zip` of sleep het `password_recovery.zip` naar het venster.

    > [!NOTE]
    > Er wordt mogelijk een fout bericht weer gegeven waarin staat dat het bestand ongeldig is. Als u dit fout bericht wilt herstellen, moet u ervoor zorgen dat u het juiste abonnement hebt geselecteerd voordat u het downloadt `password_recovery.zip` en opnieuw downloadt.  

1. Selecteer **volgende** en uw gebruiker, en het door het systeem gegenereerde wacht woord voor uw beheer console worden weer gegeven.

### <a name="administrator-security-analyst-and-read-only-user"></a>Beheerder, beveiligings analist en alleen-lezen gebruiker

Alleen-lezen en beveiligings analisten kunnen hun eigen wacht woord niet opnieuw instellen en moeten contact opnemen met een gebruiker met de rol beheerder, ondersteuning of Cyberx om hun wacht woord opnieuw in te stellen. Een gebruikers beheerder moet contact opnemen met de gebruiker van **cyberx** of **ondersteuning** om hun wacht woord opnieuw in te stellen.

Het wacht woord van een gebruiker op de sensor opnieuw instellen:

1. De gebruiker van de rol beheerder, ondersteuning of Cyberx meldt zich aan te melden bij de sensor.

1. Selecteer **gebruikers** in het linkerdeel venster.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Selecteer de optie gebruiker in het deel venster aan de linkerkant.":::

1. Zoek de gebruiker en selecteer **bewerken** in het vervolg keuzemenu **acties** .

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Selecteer Bewerken in het vervolg menu acties.":::

1. Voer het nieuwe wacht woord in het veld **Nieuw wacht woord** in en **Bevestig nieuwe wacht woord** .

1. Selecteer **Update**.

Het wacht woord van een gebruiker opnieuw instellen op de on-premises beheer console:

1. De gebruiker van de rol beheerder, ondersteuning of Cyberx meldt zich aan te melden bij de sensor.

1. Selecteer **gebruikers** in het linkerdeel venster.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Selecteer de optie van de gebruiker in het linkerdeel venster.":::

1. Zoek uw gebruiker en selecteer het bewerkings pictogram :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Voer het nieuwe wacht woord in het veld **Nieuw wacht woord** in en **Bevestig nieuwe wacht woord** .

1. Selecteer **Update**.

## <a name="see-also"></a>Zie ook

[Uw sensor](how-to-activate-and-set-up-your-sensor.md) 
 activeren en instellen [Uw on-premises beheer console](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 activeren en instellen [Sensor activiteit volgen](how-to-track-sensor-activity.md)
