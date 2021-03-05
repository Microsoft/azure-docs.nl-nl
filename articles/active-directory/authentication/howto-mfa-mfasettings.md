---
title: Azure AD-Multi-Factor Authentication-Azure Active Directory configureren
description: Meer informatie over het configureren van instellingen voor Azure AD-Multi-Factor Authentication in de Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8f2bd316c733f4680a266d609e1cc95a4879016d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198520"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Azure AD Multi-Factor Authentication-instellingen configureren

Als u de gebruikers ervaring voor Azure AD Multi-Factor Authentication wilt aanpassen, kunt u opties configureren voor instellingen zoals de drempel waarden voor account vergrendeling of fraude waarschuwingen en meldingen. Sommige instellingen zijn rechtstreeks in de Azure Portal voor Azure Active Directory (Azure AD) en enkele in een afzonderlijke Azure AD-Multi-Factor Authentication Portal.

De volgende Azure AD Multi-Factor Authentication-instellingen zijn beschikbaar in de Azure Portal:

| Functie | Beschrijving |
| ------- | ----------- |
| [Account vergrendeling](#account-lockout) | Vergren deling van accounts voor het gebruik van Azure AD Multi-Factor Authentication als er te veel verificatie pogingen in een rij zijn geweigerd. Deze functie is alleen van toepassing op gebruikers die een pincode invoeren om te verifiëren. (MFA-server) |
| [Gebruikers blok keren/deblokkeren](#block-and-unblock-users) | Voor komen dat specifieke gebruikers Azure AD Multi-Factor Authentication-aanvragen kunnen ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven gedurende 90 dagen geblokkeerd vanaf het moment dat ze worden geblokkeerd of hand matig worden gedeblokkeerd. |
| [Fraudewaarschuwing](#fraud-alert) | Instellingen configureren waarmee gebruikers frauduleuze verificatie aanvragen kunnen rapporteren. |
| [Meldingen](#notifications) | Meldingen van gebeurtenissen van MFA-server inschakelen. |
| [OATH-tokens](concept-authentication-oath-tokens.md) | Wordt gebruikt in Cloud omgevingen met Azure AD MFA voor het beheren van OATH-tokens voor gebruikers. |
| [Instellingen voor telefoon gesprek](#phone-call-settings) | Instellingen configureren met betrekking tot telefoon gesprekken en begroetingen voor Cloud-en on-premises omgevingen. |
| Providers | Hiermee worden alle bestaande verificatie providers weer gegeven die u mogelijk aan uw account hebt gekoppeld. Nieuwe verificatie providers mogen niet worden gemaakt vanaf 1 september 2018 |

![Azure Portal-Azure AD-Multi-Factor Authentication-instellingen](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Account vergrendeling

Om herhaalde MFA-pogingen als onderdeel van een aanval te voor komen, kunt u met de instellingen voor account vergrendeling opgeven hoeveel mislukte pogingen moeten worden toegestaan voordat het account gedurende een bepaalde tijd wordt vergrendeld. De account vergrendelings instellingen worden alleen toegepast wanneer een pincode wordt ingevoerd voor de MFA-prompt.

De volgende instellingen zijn beschikbaar:

* Aantal MFA-weigeringen om account vergrendeling te activeren
* Minuten totdat de teller voor account vergrendeling opnieuw is ingesteld
* Minuten totdat het account automatisch wordt gedeblokkeerd

Als u account vergrendelings instellingen wilt configureren, voert u de volgende instellingen in:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
1. Blader naar **Azure Active Directory**  >  **beveiliging**  >  **MFA**-  >  **account vergren delen**.
1. Voer de vereiste waarden in voor uw omgeving en selecteer vervolgens **Opslaan**.

    ![Scherm afbeelding van de instellingen voor account vergrendeling in de Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Gebruikers blok keren en de blok kering opheffen

Als het apparaat van een gebruiker is verloren of is gestolen, kunt u Azure AD-Multi-Factor Authentication pogingen voor het gekoppelde account blok keren. Alle Azure AD Multi-Factor Authentication-pogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd. We hebben een video gepubliceerd over [het blok keren of deblokkeren van gebruikers in uw Tenant](https://www.youtube.com/watch?v=WdeE1On4S1o) om u te laten zien hoe u dit moet doen.

### <a name="block-a-user"></a>Een gebruiker blok keren

Voer de volgende stappen uit om een gebruiker te blok keren:

1. Blader naar **Azure Active Directory**  >  **beveiliging**  >  **MFA**  >  **blok keren/deblokkeren van gebruikers**.
1. Selecteer **toevoegen** om een gebruiker te blok keren.
1. Voer de gebruikers naam in voor de geblokkeerde gebruiker als `username@domain.com` en geef een opmerking op in het veld *reden* .
1. Wanneer u klaar bent, selecteert u **OK** om de gebruiker te blok keren.

### <a name="unblock-a-user"></a>Deblokkeren van een gebruiker

Als u de blok kering van een gebruiker wilt opheffen, voert u de volgende stappen uit:

1. Blader naar **Azure Active Directory**  >  **beveiliging**  >  **MFA**  >  **blok keren/deblokkeren van gebruikers**.
1. Selecteer in de kolom *actie* naast de gewenste gebruiker **blok kering opheffen**.
1. Voer een opmerking in het veld *reden voor blok kering opheffen* in.
1. Wanneer u klaar bent, selecteert u **OK** om de gebruiker te deblokkeren.

## <a name="fraud-alert"></a>Fraudewaarschuwing

Met de functie fraude waarschuwing kunnen gebruikers frauduleuze pogingen om toegang te krijgen tot hun resources rapporteren. Wanneer er een onbekende en verdachte MFA-prompt wordt ontvangen, kunnen gebruikers de fraude poging melden met de Microsoft Authenticator-app of via hun telefoon.

De volgende configuratie opties voor fraude waarschuwingen zijn beschikbaar:

* **Gebruikers die fraude rapporteren automatisch blok keren**: als een gebruiker fraude meldt, worden de Azure AD MFA-verificatie pogingen voor het gebruikers account geblokkeerd gedurende 90 dagen of totdat een beheerder zijn of haar account heeft gedeblokkeerd. Een beheerder kan aanmeldingen controleren met behulp van het aanmeldings rapport en de juiste actie ondernemen om fraude te voor komen. Een beheerder kan de [blok kering](#unblock-a-user) van het gebruikers account opheffen.
* **Code voor het melden van fraude tijdens de eerste begroeting**: wanneer gebruikers een telefoon oproep ontvangen om multi-factor Authentication uit te voeren, drukken ze normaal gesp roken **#** in om de aanmelding te bevestigen. Voor het melden van fraude, voert de gebruiker een code in voordat u op drukt **#** . Deze code is standaard **0** , maar u kunt deze aanpassen.

   > [!NOTE]
   > De standaard voicemail begroetingen van micro soft geven gebruikers de opdracht om op **0 #** te drukken om een fraude waarschuwing te verzenden. Als u een andere code dan **0** wilt gebruiken, moet u uw eigen aangepaste spraak begroetingen opnemen en uploaden met de juiste instructies voor uw gebruikers.

Voer de volgende stappen uit om fraude waarschuwingen in te scha kelen en te configureren:

1. Blader naar **Azure Active Directory**  >  **beveiliging**  >  **MFA**  >  **fraude waarschuwing**.
1. Stel de instelling *gebruikers toestaan voor het indienen van fraude waarschuwingen* **in op aan**.
1. Configureer de *gebruikers automatisch blok keren die fraude* of *code rapporteren om fraude te melden tijdens de eerste begroeting naar* wens.
1. Selecteer **Opslaan** wanneer u klaar bent.

### <a name="view-fraud-reports"></a>Fraude rapporten weer geven

Selecteer **Azure Active Directory**  >  verificatie gegevens voor **aanmeldingen**  >  . Het fraude rapport maakt nu deel uit van het standaard rapport van Azure AD-aanmeldingen en wordt weer gegeven in de **"resultaat Details"** als MFA is geweigerd, er is een fraude code ingevoerd.
 
## <a name="notifications"></a>Meldingen

E-mail meldingen kunnen worden geconfigureerd wanneer gebruikers fraude waarschuwingen rapporteren. Deze meldingen worden doorgaans verzonden naar identiteits beheerders, aangezien de referenties van de gebruikers account waarschijnlijk worden aangetast. In het volgende voor beeld ziet u hoe een e-mail bericht met fraude waarschuwing eruit ziet:

![Voor beeld van een e-mail melding met fraude waarschuwing](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Voor het configureren van fraude waarschuwings meldingen moet u de volgende instellingen volt ooien:

1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **multi-factor Authentication**  >  **meldingen**.
1. Voer het e-mail adres in dat u wilt toevoegen aan het volgende vak.
1. Als u een bestaand e-mail adres wilt verwijderen, selecteert u de optie **...** naast het gewenste e-mail adres en selecteert u vervolgens **verwijderen**.
1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="oath-tokens"></a>OATH-tokens

Azure AD ondersteunt het gebruik van OATH-mobiele TOTP SHA-1-tokens waarmee codes elke 30 of 60 seconden worden vernieuwd. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze.

OATH mobiele TOTP-hardware-tokens worden doorgaans geleverd met een geheime sleutel, of Seed, vooraf geprogrammeerd in het token. Deze sleutels moeten worden ingevoerd in azure AD, zoals beschreven in de volgende stappen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel mag alleen de tekens *a-z* of *a-* z en cijfers *1-7* bevatten en moet worden gecodeerd in *Base32*.

Programmeer bare OATH-mobiele TOTP die kunnen worden geseedd, kunnen ook worden ingesteld met Azure AD in de instellings stroom van het software token.

![OATH-tokens uploaden naar de Blade MFA OATH-tokens](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Wanneer de tokens zijn verkregen, moeten ze worden geüpload in een CSV-bestand (Comma-Separated Values), met inbegrip van de UPN, het serie nummer, de geheime sleutel, het tijds interval, de fabrikant en het model, zoals wordt weer gegeven in het volgende voor beeld:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zorg ervoor dat u de rij met koppen in het CSV-bestand opneemt.

Als een beheerder zich heeft geformatteerd als een CSV-bestand, kunt u zich aanmelden bij de Azure Portal, naar **Azure Active Directory > Security > MFA > OATH-tokens** gaan en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren voordat het proces is uitgevoerd. Selecteer de knop **vernieuwen** om de huidige status op te halen. Als het bestand fouten bevat, kunt u een CSV-bestand downloaden met een lijst met fouten die u moet oplossen. De veld namen in het gedownloade CSV-bestand wijken af van de geüploade versie.

Zodra er fouten zijn opgelost, kan de beheerder elke sleutel activeren door **Activate** voor het token te selecteren en de otp op te geven die op het token wordt weer gegeven.

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de app Microsoft Authenticator, die op elk gewenst moment worden geconfigureerd voor gebruik.

## <a name="phone-call-settings"></a>Instellingen voor telefoon gesprek

Als gebruikers telefoon gesprekken ontvangen voor MFA-prompts, kunt u hun ervaring configureren, zoals de beller-ID of spraak begroeting die ze horen.

Als u de MFA-beller-ID niet hebt geconfigureerd in de Verenigde Staten, zijn Voice-aanroepen van micro soft afkomstig uit de volgende nummers. Als u spam filters gebruikt, moet u deze nummers uitsluiten:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Wanneer Azure AD Multi-Factor Authentication-aanroepen worden geplaatst via het open bare telefoonnet werk, worden de aanroepen soms gerouteerd via een transporteur die geen beller-ID ondersteunt. Daarom is de beller-ID niet gegarandeerd, zelfs als Azure AD Multi-Factor Authentication deze altijd verzendt. Dit geldt zowel voor telefoon gesprekken als voor tekst berichten die door Azure AD Multi-Factor Authentication worden geleverd. Als u wilt valideren dat een tekst bericht afkomstig is van Azure AD Multi-Factor Authentication, raadpleegt u [welke SMS-code worden gebruikt voor het verzenden van berichten?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Voer de volgende stappen uit om uw eigen beller-ID-nummer te configureren:

1. Blader naar **Azure Active Directory** instellingen voor de  >    >    >  **telefoon oproep** voor de beveiliging MFA.
1. Stel het **ID-nummer van de MFA-beller** in op het aantal dat gebruikers op hun telefoon willen zien. Alleen op basis van Amerikaanse nummers zijn toegestaan.
1. Selecteer **Opslaan** wanneer u klaar bent.

### <a name="custom-voice-messages"></a>Aangepaste spraak berichten

U kunt uw eigen opnamen of begroetingen voor Azure AD-Multi-Factor Authentication gebruiken met de functie voor aangepaste spraak berichten. Deze berichten kunnen worden gebruikt naast of ter vervanging van de standaard micro soft-opnamen.

Voordat u begint, moet u rekening houden met de volgende beperkingen:

* De ondersteunde bestands indelingen zijn *. WAV* en *. mp3*.
* De maximale bestands grootte is 1 MB.
* Verificatie berichten moeten korter dan 20 seconden zijn. Berichten van meer dan 20 seconden kunnen ervoor zorgen dat de verificatie mislukt. De gebruiker reageert mogelijk niet voordat het bericht is voltooid en er wordt een time-out van de verificatie uitgevoerd.

### <a name="custom-message-language-behavior"></a>Taal gedrag aangepaste berichten

Wanneer een aangepast voicemail bericht wordt afgespeeld naar de gebruiker, is de taal van het bericht afhankelijk van de volgende factoren:

* De taal van de huidige gebruiker.
  * De taal die wordt gedetecteerd door de browser van de gebruiker.
  * Andere verificatie scenario's kunnen zich anders gedragen.
* De taal van alle beschik bare aangepaste berichten.
  * Deze taal wordt gekozen door de beheerder wanneer een aangepast bericht wordt toegevoegd.

Als er bijvoorbeeld slechts één aangepast bericht is, met een taal van het Duits:

* Een gebruiker die zich verifieert in de Duitse taal, hoort het aangepaste Duitse bericht.
* Een gebruiker die zich in het Engels verifieert, hoort het standaard Engelse bericht.

### <a name="custom-voice-message-defaults"></a>Aangepaste standaard waarden voor spraak berichten

De volgende voorbeeld scripts kunnen worden gebruikt voor het maken van uw eigen aangepaste berichten. Deze zin zijn de standaard waarden als u uw eigen aangepaste berichten niet configureert:

| Bericht naam | Script |
| --- | --- |
| Verificatie geslaagd | Uw aanmelding is geverifieerd. Zien. |
| Prompt voor verlenging | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Druk op de toets hekje om door te gaan. |
| Fraude bevestiging | Er is een fraude waarschuwing verzonden. Als u uw account wilt blok keren, neemt u contact op met de IT-Help Desk van uw bedrijf. |
| Fraude begroeting (standaard) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Druk op het hekje om uw verificatie te volt ooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op het hekje nul om een fraude waarschuwing te verzenden. Hiermee wordt het IT-team van uw bedrijf op de hoogte gebracht en worden verdere verificatie pogingen geblokkeerd. |
| Fraude gemeld dat er een fraude waarschuwing is verzonden. | Als u uw account wilt blok keren, neemt u contact op met de IT-Help Desk van uw bedrijf. |
| Activering | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Poging tot authenticatie geweigerd | Verificatie geweigerd. |
| Nieuwe poging (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Begroeting (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Fraude begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt.  Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op het hekje nul om een fraude waarschuwing te verzenden. Hiermee wordt het IT-team van uw bedrijf op de hoogte gebracht en worden verdere verificatie pogingen geblokkeerd. |
| Nieuwe poging (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Vragen om toestel na cijfers | Als u deze uitbrei ding al hebt, drukt u op de toets hekje om door te gaan. |
| Verificatie geweigerd | Je kunt je op dit moment niet aanmelden. Probeert u het later nog eens. |
| Activerings begroeting (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Nieuwe activerings poging (standaard) | Hartelijk dank dat u het micro soft-aanmeld verificatie systeem gebruikt. Druk op het hekje om uw verificatie te volt ooien. |
| Activerings begroeting (pincode) | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Voer uw pincode in, gevolgd door de hekje om uw verificatie te volt ooien. |
| Vragen om toestel voor cijfers | Hartelijk dank dat u het aanmeldings verificatie systeem van micro soft gebruikt. Zet deze oproep over naar de extensie... |

### <a name="set-up-a-custom-message"></a>Een aangepast bericht instellen

Als u uw eigen aangepaste berichten wilt gebruiken, voert u de volgende stappen uit:

1. Blader naar **Azure Active Directory** instellingen voor de  >    >    >  **telefoon oproep** voor de beveiliging MFA.
1. Selecteer **begroeting toevoegen**.
1. Kies het **type** begroeting, zoals *begroeting (standaard)* of de  *verificatie is geslaagd*.
1. Selecteer de **taal** op basis van de vorige sectie op het gedrag van de [aangepaste taal voor berichten](#custom-message-language-behavior).
1. Blader naar en selecteer een *. mp3* -of *. WAV* -geluids bestand dat u wilt uploaden.
1. Wanneer u klaar bent, selecteert u **toevoegen** en vervolgens **Opslaan**.

## <a name="mfa-service-settings"></a>Instellingen voor MFA-service

Instellingen voor app-wacht woorden, vertrouwde IP-adressen, verificatie opties en onthoud multi-factor Authentication voor Azure AD Multi-Factor Authentication kunnen worden gevonden in service-instellingen. Dit is meer van een verouderde Portal en maakt geen deel uit van de normale Azure AD-Portal.

U kunt toegang krijgen tot de service-instellingen via de Azure portal door te bladeren naar **Azure Active Directory**  >  **Security** MFA aan de slag  >    >    >    >  **Extra op de cloud gebaseerde MFA-instellingen** configureren. Een nieuw venster of tabblad wordt geopend met aanvullende opties voor *Service-instellingen* .

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

Met de functie voor _vertrouwde ip's_ van Azure AD multi-factor Authentication worden multi-factor Authentication-prompts omzeild voor gebruikers die zich aanmelden vanuit een gedefinieerd IP-adres bereik. U kunt vertrouwde IP-adresbereiken voor uw on-premises omgevingen instellen wanneer gebruikers zich op een van deze locaties bevinden, is er geen Azure AD-Multi-Factor Authentication prompt.

> [!NOTE]
> De vertrouwde Ip's kunnen alleen particuliere IP-bereiken bevatten wanneer u MFA server gebruikt. Voor Azure AD-Multi-Factor Authentication in de Cloud, kunt u alleen open bare IP-adresbereiken gebruiken.
>
> IPv6-bereiken worden alleen ondersteund in de interface van de [benoemde locatie (preview)](../conditional-access/location-condition.md#preview-features) .

Als uw organisatie de NPS-extensie implementeert voor het leveren van MFA aan on-premises toepassingen, ziet u dat het bron-IP-adres altijd de NPS-server is die de verificatie poging doorloopt.

| Type Azure AD-Tenant | Opties voor vertrouwde IP-functies |
|:--- |:--- |
| Beheerd |**Specifiek bereik van IP-adressen**: beheerders geven een bereik van IP-adressen op waarmee multi-factor Authentication kan worden omzeild voor gebruikers die zich aanmelden vanaf het bedrijfs intranet. Er kunnen Maxi maal 50 vertrouwde IP-adresbereiken worden geconfigureerd.|
| Federatief |**Alle federatieve gebruikers**: alle federatieve gebruikers die zich aanmelden bij binnen van de organisatie, kunnen multi-factor Authentication overs Laan. De gebruikers Bypass de verificatie met behulp van een claim die is uitgegeven door Active Directory Federation Services (AD FS).<br/>**Specifiek bereik van IP-adressen**: beheerders geven een bereik van IP-adressen op waarmee multi-factor Authentication kan worden omzeild voor gebruikers die zich aanmelden vanaf het bedrijfs intranet. |

De functie voor het overs laan van vertrouwde IP-adressen werkt alleen binnen het bedrijfs intranet. Als u de optie **alle federatieve gebruikers** selecteert en de gebruiker zich aanmeldt van buiten het bedrijfs intranet, moet de gebruiker verifiëren met behulp van multi-factor Authentication. Het proces is hetzelfde, zelfs als de gebruiker een AD FS claim presenteert.

### <a name="end-user-experience-inside-of-corpnet"></a>Ervaring van de eind gebruiker in Corpnet

Wanneer de functie voor vertrouwde IP-adressen is uitgeschakeld, is multi-factor Authentication vereist voor browser stromen. App-wacht woorden zijn vereist voor oudere uitgebreide client toepassingen.

Wanneer vertrouwde IP-adressen worden gebruikt, is multi-factor Authentication niet vereist voor browser stromen. App-wacht woorden zijn niet vereist voor oudere uitgebreide client toepassingen, mits de gebruiker geen app-wacht woord heeft gemaakt. Wanneer een app-wacht woord wordt gebruikt, blijft het wacht woord vereist.

### <a name="end-user-experience-outside-corpnet"></a>Ervaring voor de eind gebruiker buiten Corpnet

Ongeacht of er een vertrouwd IP-adres is gedefinieerd, is multi-factor Authentication vereist voor browser stromen. App-wacht woorden zijn vereist voor oudere uitgebreide client toepassingen.

### <a name="enable-named-locations-by-using-conditional-access"></a>Benoemde locaties inschakelen met behulp van voorwaardelijke toegang

U kunt regels voor voorwaardelijke toegang gebruiken om benoemde locaties te definiëren met behulp van de volgende stappen:

1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory** en blader vervolgens naar de **beveiligings**  >  **voorwaardelijke toegang**  >  **benoemde locaties**.
1. Selecteer een **nieuwe locatie**.
1. Voer een naam in voor de locatie.
1. Selecteer **markeren als vertrouwde locatie**.
1. Voer het IP-bereik in CIDR-notatie in voor uw omgeving, zoals *40.77.182.32/27*.
1. Selecteer **Maken**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>De functie voor vertrouwde IP-adressen inschakelen met behulp van voorwaardelijke toegang

Voer de volgende stappen uit om vertrouwde IP-adressen in te scha kelen met behulp van beleid voor voorwaardelijke toegang:

1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory** en blader vervolgens naar de **beveiligings**  >   **voorwaardelijke toegang**  >  **benoemde locaties**.
1. Selecteer **vertrouwde IP-adressen voor MFA configureren**.
1. Kies op de pagina **Service-instellingen** onder **vertrouwde IP-adressen** een van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers die afkomstig zijn van mijn intranet**: als u deze optie kiest, schakelt u het selectie vakje in. Alle federatieve gebruikers die zich aanmelden vanuit het bedrijfs netwerk, hebben multi-factor Authentication overs laan met behulp van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranet claim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen van een specifiek bereik van open bare ip's**: als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Voor IP-adressen in het bereik xxx. xxx. xxx. 1 t/m xxx. xxx. xxx. 254 gebruikt u notatie als **xxx. xxx. xxx. 0/24**.
      * Gebruik voor één IP-adres notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer Maxi maal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, passeren multi-factor Authentication.

1. Selecteer **Opslaan**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>De functie voor vertrouwde IP-adressen inschakelen met behulp van service-instellingen

Als u geen beleid voor voorwaardelijke toegang wilt gebruiken om vertrouwde IP-adressen in te scha kelen, kunt u de *Service-instellingen* voor Azure AD multi-factor Authentication configureren met behulp van de volgende stappen:

1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory** en kies vervolgens **gebruikers**.
1. Selecteer **Multi-Factor Authentication**.
1. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
1. Kies op de pagina **Service-instellingen** onder **betrouw bare ip's** een (of beide) van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers op mijn intranet**: als u deze optie wilt selecteren, schakelt u het selectie vakje in. Alle federatieve gebruikers die zich aanmelden vanuit het bedrijfs netwerk, hebben multi-factor Authentication overs laan met behulp van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranet claim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen van een opgegeven bereik van subnetten met IP-adressen**: als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Voor IP-adressen in het bereik xxx. xxx. xxx. 1 t/m xxx. xxx. xxx. 254 gebruikt u notatie als **xxx. xxx. xxx. 0/24**.
      * Gebruik voor één IP-adres notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer Maxi maal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, passeren multi-factor Authentication.

1. Selecteer **Opslaan**.

## <a name="verification-methods"></a>Verificatie methoden

U kunt kiezen welke verificatie methoden beschikbaar zijn voor uw gebruikers in de portal voor service-instellingen. Wanneer uw gebruikers hun accounts registreren voor Azure AD Multi-Factor Authentication, kiezen ze hun voorkeurs verificatie methode van de opties die u hebt ingeschakeld. Richt lijnen voor het proces van gebruikers registratie vindt [u in mijn account instellen voor multi-factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).

De volgende verificatie methoden zijn beschikbaar:

| Methode | Beschrijving |
|:--- |:--- |
| Bellen naar telefoon |Hiermee wordt een geautomatiseerd telefoon gesprek geplaatst. De gebruiker beantwoordt het gesprek en drukt # in op de toetsenblok van de telefoon om te authenticeren. Het telefoon nummer is niet gesynchroniseerd met on-premises Active Directory. |
| SMS-bericht naar telefoon |Hiermee verzendt u een tekst bericht met een verificatie code. De gebruiker wordt gevraagd de verificatie code in te voeren in de aanmeldings interface. Dit proces wordt eenrichtings-SMS genoemd. Twee richtings-SMS betekent dat de gebruiker een bepaalde code moet herstellen. SMS-in twee richtingen is afgeschaft en wordt niet ondersteund na 14 november 2018. Beheerders moeten een andere methode inschakelen voor gebruikers die eerder twee richtings SM'S hebben gebruikt.|
| Melding via mobiele app |Hiermee verzendt u een push melding naar uw telefoon of geregistreerd apparaat. De gebruiker bekijkt de melding en selecteert **verifiëren** om de verificatie te volt ooien. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verificatie code van de mobiele app of het hardware-token |De Microsoft Authenticator-app genereert elke 30 seconden een nieuwe OATH-verificatie code. De gebruiker voert de verificatie code in de aanmeldings interface in. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Zie [welke verificatie-en verificatie methoden zijn er beschikbaar in azure AD?](concept-authentication-methods.md) voor meer informatie.

### <a name="enable-and-disable-verification-methods"></a>Verificatie methoden in-en uitschakelen

Voer de volgende stappen uit om verificatie methoden in of uit te scha kelen:

1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory** en kies vervolgens **gebruikers**.
1. Selecteer **Multi-Factor Authentication**.
1. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
1. Selecteer op de pagina **Service-instellingen** onder **verificatie opties** de methoden die aan uw gebruikers moeten worden verstrekt.
1. Klik op **Opslaan**.

## <a name="remember-multi-factor-authentication"></a>Onthouden Multi-Factor Authentication

Met de functie _multi-factor Authentication onthouden_ kunnen gebruikers volgende controles over een bepaald aantal dagen overs Laan nadat ze zich hebben aangemeld bij een apparaat met behulp van multi-factor Authentication. Selecteer een duur van 90 dagen of meer om de bruikbaarheid te verg Roten en zo het aantal keren dat een gebruiker MFA moet uitvoeren op hetzelfde apparaat.

> [!IMPORTANT]
> Als een account of apparaat is aangetast, kan de beveiliging worden beïnvloed door Multi-Factor Authentication voor vertrouwde apparaten. Als er een bedrijfs account wordt aangetast of een vertrouwd apparaat verloren is gegaan of wordt gestolen, moet u [MFA-sessies intrekken](howto-mfa-userdevicesettings.md).
>
> De herstel actie trekt de vertrouwde status van alle apparaten in en de gebruiker is verplicht om multi-factor Authentication opnieuw uit te voeren. U kunt uw gebruikers ook de opdracht geven om Multi-Factor Authentication op hun eigen apparaten te herstellen, zoals wordt vermeld in [uw instellingen voor multi-factor Authentication beheren](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Hoe de functie werkt

De functie Multi-Factor Authentication onthouden stelt een permanente cookie in op de browser wanneer een gebruiker de optie **niet opnieuw vragen voor X dagen** selecteert tijdens het aanmelden. De gebruiker wordt niet opnieuw gevraagd om Multi-Factor Authentication van dezelfde browser totdat de cookie verloopt. Als de gebruiker een andere browser op hetzelfde apparaat opent of de cookies wist, wordt deze opnieuw gevraagd om te verifiëren.

De optie **niet opnieuw vragen voor X dagen** wordt niet weer gegeven voor niet-browser toepassingen, ongeacht of de app moderne verificatie ondersteunt. Deze apps maken gebruik van _vernieuwings tokens_ die elk uur nieuwe toegangs tokens bieden. Wanneer een vernieuwings token is gevalideerd, controleert Azure AD of de laatste multi-factor Authentication binnen het opgegeven aantal dagen is uitgevoerd.

De functie beperkt het aantal authenticaties op Web-apps, die Norma liter elke keer worden gevraagd. De functie kan het aantal authenticaties verhogen voor moderne authenticatie clients die Norma liter elke 90 dagen vragen, als een lagere duur is geconfigureerd. Kan ook het aantal authenticaties verhogen in combi natie met beleid voor voorwaardelijke toegang.

> [!IMPORTANT]
> De functie **multi-factor Authentication onthouden** is niet compatibel met de functie **aangemeld blijven** van AD FS, wanneer gebruikers multi-factor Authentication uitvoeren voor AD FS via Azure multi-factor Authentication-Server of een multi-factor Authentication-oplossing van derden.
>
> Als uw gebruikers **ingelogd blijven aangemeld** bij AD FS en hun apparaat ook markeren als vertrouwd voor multi-factor Authentication, wordt de gebruiker niet automatisch gecontroleerd wanneer het aantal dagen van de **Meervoudige multi-factor Authentication** is verlopen. Azure AD vraagt een nieuwe multi-factor Authentication aan, maar AD FS retourneert een token met de oorspronkelijke Multi-Factor Authentication claim en datum, in plaats van multi-factor Authentication opnieuw uit te voeren. **Met deze reactie wordt een verificatie lus tussen Azure AD en AD FS.**
>
> De functie **multi-factor Authentication onthouden** is niet compatibel met B2B-gebruikers en is niet zichtbaar voor B2B-gebruikers wanneer ze zich aanmelden bij de uitgenodigde tenants.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication onthouden inschakelen

Voer de volgende stappen uit om de optie in te scha kelen en zo te configureren dat gebruikers hun MFA-status en bypass prompts kunnen onthouden:

1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory** en kies vervolgens **gebruikers**.
1. Selecteer **Multi-Factor Authentication**.
1. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
1. Selecteer op de pagina **Service-instellingen** onder **multi-factor Authentication onthouden** de optie **gebruikers toestaan om multi-factor Authentication te onthouden op apparaten die ze vertrouwen** .
1. Stel in hoeveel dagen multi-factor Authentication door vertrouwde apparaten mag worden omzeild. Voor de optimale gebruikers ervaring breidt u de duur naar *90* of meer dagen uit.
1. Selecteer **Opslaan**.

### <a name="mark-a-device-as-trusted"></a>Een apparaat als vertrouwd markeren

Nadat u de functie Multi-Factor Authentication onthouden hebt ingeschakeld, kunnen gebruikers een apparaat als vertrouwd markeren wanneer ze zich aanmelden door de optie te selecteren voor **niet opnieuw vragen**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beschik bare methoden voor gebruik in azure AD Multi-Factor Authentication [welke verificatie-en verificatie methoden beschikbaar zijn in azure Active Directory?](concept-authentication-methods.md)
