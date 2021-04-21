---
title: Azure AD Multi-Factor Authentication configureren - Azure Active Directory
description: Meer informatie over het configureren van instellingen voor Azure AD Multi-Factor Authentication in de Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829652"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Azure AD Multi-Factor Authentication-instellingen configureren

Als u de eindgebruikerservaring voor Azure AD Multi-Factor Authentication wilt aanpassen, kunt u opties configureren voor instellingen zoals de drempelwaarden voor accountvergrendeling of fraudewaarschuwingen en meldingen. Sommige instellingen zijn rechtstreeks in de Azure Portal voor Azure Active Directory (Azure AD) en sommige in een afzonderlijke Azure AD Multi-Factor Authentication-portal.

De volgende Azure AD Multi-Factor Authentication-instellingen zijn beschikbaar in de Azure Portal:

| Functie | Beschrijving |
| ------- | ----------- |
| [Accountvergrendeling](#account-lockout) | Accounts tijdelijk vergrendelen voor het gebruik van Azure AD Multi-Factor Authentication als er te veel geweigerde verificatiepogingen op een rij zijn. Deze functie is alleen van toepassing op gebruikers die een pincode invoeren om te verifiëren. (MFA-server) |
| [Gebruikers blokkeren/deblokkeren](#block-and-unblock-users) | Blokkeren dat specifieke gebruikers Azure AD Multi-Factor Authentication-aanvragen kunnen ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze worden geblokkeerd of ze worden handmatig geblokkeerd. |
| [Fraudewaarschuwing](#fraud-alert) | Configureer instellingen waarmee gebruikers frauduleuze verificatieaanvragen kunnen rapporteren. |
| [Meldingen](#notifications) | Meldingen van gebeurtenissen van de MFA-server inschakelen. |
| [OATH-tokens](concept-authentication-oath-tokens.md) | Wordt gebruikt in cloudgebaseerde Azure AD MFA-omgevingen voor het beheren van OATH-tokens voor gebruikers. |
| [Instellingen voor telefoongesprekken](#phone-call-settings) | Configureer instellingen met betrekking tot telefoongesprekken en begroetingen voor cloud- en on-premises omgevingen. |
| Providers | Hiermee worden alle bestaande verificatieproviders weer die u mogelijk aan uw account hebt gekoppeld. Nieuwe verificatieproviders worden mogelijk niet gemaakt vanaf 1 september 2018 |

![Azure Portal- Azure AD Multi-Factor Authentication-instellingen](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Accountvergrendeling

Om herhaalde MFA-pogingen als onderdeel van een aanval te voorkomen, kunt u met de instellingen voor accountvergrendeling opgeven hoeveel mislukte pogingen moeten worden toegestaan voordat het account voor een bepaalde periode wordt vergrendeld. De instellingen voor accountvergrendeling worden alleen toegepast wanneer er een pincode wordt ingevoerd voor de MFA-prompt.

De volgende instellingen zijn beschikbaar:

* Aantal MFA-weigeringen om accountvergrendeling te activeren
* Minuten totdat de teller voor accountvergrendeling opnieuw is ingesteld
* Minuten totdat account automatisch wordt geblokkeerd

Voltooi de volgende instellingen om instellingen voor accountvergrendeling te configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) beheerder.
1. Blader naar **Azure Active Directory**  >  **vergrendeling van** het  >  **MFA-account** voor  >  **beveiliging.**
1. Voer de waarden voor vereisen in voor uw omgeving en selecteer **vervolgens Opslaan.**

    ![Schermopname van de instellingen voor accountvergrendeling in de Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Gebruikers blokkeren en deblokkeren

Als het apparaat van een gebruiker is verloren of gestolen, kunt u pogingen van Azure AD Multi-Factor Authentication blokkeren voor het gekoppelde account. Pogingen van Azure AD Multi-Factor Authentication voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd. We hebben een video gepubliceerd over het blokkeren en [deblokkeren](https://www.youtube.com/watch?v=WdeE1On4S1o) van gebruikers in uw tenant om u te laten zien hoe u dit doet.

### <a name="block-a-user"></a>Een gebruiker blokkeren

Als u een gebruiker wilt blokkeren, moet u de volgende stappen voltooien:

1. Blader naar **Azure Active Directory**  >  **blokkeren**  >  **of**  >  **deblokkeren van gebruikers.**
1. Selecteer **Toevoegen om** een gebruiker te blokkeren.
1. Voer de gebruikersnaam voor de geblokkeerde gebruiker in als `username@domain.com` en geef een opmerking op in het veld *Reden.*
1. Wanneer u klaar bent, **selecteert u OK** om de gebruiker te blokkeren.

### <a name="unblock-a-user"></a>Een gebruiker deblokkeren

Als u een gebruiker wilt deblokkeren, moet u de volgende stappen voltooien:

1. Blader naar **Azure Active Directory**  >  **blokkeren/deblokkeren**  >    >  **van gebruikers.**
1. Selecteer in *de* kolom Actie naast de gewenste gebruiker de optie **Blokkering opheffen.**
1. Voer een opmerking in het *veld Reden voor blokkering in.*
1. Wanneer u klaar bent, **selecteert u OK** om de gebruiker te deblokkeren.

## <a name="fraud-alert"></a>Fraudewaarschuwing

Met de functie voor fraudewaarschuwingen kunnen gebruikers frauduleuze pogingen tot toegang tot hun resources melden. Wanneer een onbekende en verdachte MFA-prompt wordt ontvangen, kunnen gebruikers de fraudepoging melden met behulp van de Microsoft Authenticator app of via hun telefoon.

De volgende configuratieopties voor fraudewaarschuwingen zijn beschikbaar:

* **Gebruikers die** fraude melden automatisch blokkeren: als een gebruiker fraude rapporteert, worden de azure AD MFA-verificatiepogingen voor het gebruikersaccount 90 dagen geblokkeerd of totdat een beheerder zijn account deblokkeert. Een beheerder kan aanmeldingen controleren met behulp van het aanmeldingsrapport en passende maatregelen nemen om toekomstige fraude te voorkomen. Een beheerder kan vervolgens [het gebruikersaccount](#unblock-a-user) deblokkeren.
* **Code voor het melden van fraude** tijdens de eerste begroeting: wanneer gebruikers een telefoongesprek ontvangen om meervoudige verificatie uit te voeren, drukken ze normaal gesproken op om hun aanmelding te **#** bevestigen. Om fraude te melden, voert de gebruiker een code in voordat deze op **#** drukt. Deze code is **standaard 0,** maar u kunt deze aanpassen.

   > [!NOTE]
   > De standaardstemgrots van Microsoft geven gebruikers de opdracht om op **0# te drukken** om een fraudewaarschuwing in te dienen. Als u een andere code dan **0** wilt gebruiken, kunt u uw eigen aangepaste stem begroetingen opnemen en uploaden met de juiste instructies voor uw gebruikers.

Als u fraudewaarschuwingen wilt inschakelen en configureren, moet u de volgende stappen voltooien:

1. Blader naar **Azure Active Directory**  >  **waarschuwing voor** fraude met  >  **MFA** voor  >  **beveiliging.**
1. Stel de *instelling Gebruikers toestaan om fraudewaarschuwingen in te* dienen in op **Aan.**
1. Configureer *de instelling Gebruikers die fraude melden* automatisch blokkeren of Code om fraude te melden tijdens de *eerste* begroeting.
1. Selecteer **Opslaan** wanneer u klaar bent.

### <a name="view-fraud-reports"></a>Frauderapporten weergeven

Selecteer **Azure Active Directory**  >  **verificatiedetails**  >  **voor aanmeldingen.** Het frauderapport maakt nu deel uit van het standaardrapport azure  AD-aanmeldingen en wordt in de resultaatdetails als MFA geweigerd, Ingevoerde fraudecode.
 
## <a name="notifications"></a>Meldingen

E-mailmeldingen kunnen worden geconfigureerd wanneer gebruikers fraudewaarschuwingen rapporteren. Deze meldingen worden doorgaans verzonden naar identiteitsbeheerders, omdat de accountreferenties van de gebruiker waarschijnlijk zijn aangetast. In het volgende voorbeeld ziet u hoe een e-mailmelding voor fraudewaarschuwingen eruitziet:

![Voorbeeld van e-mailmeldingen voor fraudewaarschuwingen](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Voltooi de volgende instellingen om meldingen voor fraudewaarschuwingen te configureren:

1. Blader naar **Azure Active Directory**  >  **Security**  >  **Multi-Factor Authentication-meldingen**  >  **.**
1. Voer het e-mailadres in dat u in het volgende vak wilt toevoegen.
1. Als u een bestaand e-mailadres wilt verwijderen, selecteert u **de optie ...** naast het gewenste e-mailadres en selecteert u vervolgens **Verwijderen.**
1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="oath-tokens"></a>OATH-tokens

Azure AD ondersteunt het gebruik van OATH-TOTP SHA-1-tokens die codes elke 30 of 60 seconden vernieuwen. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze.

OATH TOTP-hardwaretokens worden meestal voorzien van een geheime sleutel, of seed, die vooraf is geprogrammeerd in het token. Deze sleutels moeten worden ingevoerd in Azure AD, zoals beschreven in de volgende stappen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel mag alleen de tekens *a-z* of *A-Z* en cijfers *1-7* bevatten en moet worden gecodeerd in *Base32.*

Programmeerbare OATH TOTP-hardwaretokens die opnieuw kunnen worden geseed, kunnen ook worden ingesteld met Azure AD in de installatiestroom van het softwaretoken.

OATH-hardwaretokens worden ondersteund als onderdeel van een openbare preview. Zie Aanvullende gebruiksvoorwaarden voor Microsoft Azure previews voor [meer informatie Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![OATH-tokens uploaden naar de blade MFA OATH-tokens](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Zodra tokens zijn verkregen, moeten ze worden geüpload in een BESTANDsindeling met door komma's gescheiden waarden (CSV), waaronder de UPN, het serienummer, de geheime sleutel, het tijdsinterval, de fabrikant en het model, zoals wordt weergegeven in het volgende voorbeeld:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zorg ervoor dat u de koprij in uw CSV-bestand op neemt.

Zodra het bestand correct is opgemaakt als een CSV-bestand, kan een beheerder zich aanmelden bij de Azure Portal, navigeren naar **Azure Active Directory > Security > MFA > OATH-tokens** en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren om het te verwerken. Selecteer de **knop Vernieuwen** om de huidige status op te halen. Als er fouten in het bestand zijn, kunt u een CSV-bestand downloaden waarin eventuele fouten worden vermeld die u kunt oplossen. De veldnamen in het gedownloade CSV-bestand verschillen van de geüploade versie.

Zodra eventuele fouten zijn verholpen, kan de beheerder  elke sleutel activeren door Activeren te selecteren voor het token en de OTP in te geven die op het token wordt weergegeven.

Gebruikers kunnen een combinatie van maximaal vijf OATH-hardwaretokens of authenticatortoepassingen hebben, zoals de Microsoft Authenticator-app, die op elk moment is geconfigureerd voor gebruik.

## <a name="phone-call-settings"></a>Instellingen voor telefoongesprekken

Als gebruikers telefoongesprekken ontvangen voor MFA-prompts, kunt u hun ervaring configureren, zoals de beller-id of de spraakgrot die ze horen.

Als u Verenigde Staten MFA-beller-id niet hebt geconfigureerd, zijn spraakoproepen van Microsoft afkomstig van de volgende nummers. Als u spamfilters gebruikt, moet u deze nummers uitsluiten:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Wanneer Azure AD Multi-Factor Authentication-aanroepen worden geplaatst via het openbare telefoonnetwerk, worden de oproepen soms gerouteerd via een provider die geen beller-id ondersteunt. Daarom wordt de id van de aanroeper niet gegarandeerd, ook al verzendt Azure AD Multi-Factor Authentication deze altijd. Dit geldt zowel voor telefoongesprekken als voor sms-berichten die worden geleverd door Azure AD Multi-Factor Authentication. Als u wilt controleren of een sms-bericht afkomstig is van Azure AD Multi-Factor Authentication, zie [Welke sms-korte codes](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users) worden gebruikt voor het verzenden van berichten?

Als u uw eigen nummer van de beller wilt configureren, moet u de volgende stappen voltooien:

1. Blader naar **Azure Active Directory**  >  **instellingen voor**  >  **telefoongesprekken voor beveiligings-MFA.**  >  
1. Stel het **nummer van de MFA-beller in** op het nummer dat gebruikers op hun telefoon willen zien. Alleen Amerikaanse nummers zijn toegestaan.
1. Selecteer **Opslaan** wanneer u klaar bent.

### <a name="custom-voice-messages"></a>Aangepaste spraakberichten

U kunt uw eigen opnamen of begroetingen voor Azure AD Multi-Factor Authentication gebruiken met de functie voor aangepaste spraakberichten. Deze berichten kunnen worden gebruikt naast of om de standaardopnamen van Microsoft te vervangen.

Voordat u begint, moet u rekening houden met de volgende beperkingen:

* De ondersteunde bestandsindelingen zijn *.wav* en *.mp3.*
* De limiet voor de bestandsgrootte is 1 MB.
* Verificatieberichten moeten korter zijn dan 20 seconden. Berichten die langer zijn dan 20 seconden, kunnen ertoe leiden dat de verificatie mislukt. De gebruiker reageert mogelijk niet voordat het bericht is klaar en er een times-out voor de verificatie is.

### <a name="custom-message-language-behavior"></a>Gedrag van aangepaste berichttaal

Wanneer een aangepast spraakbericht wordt afgespeeld voor de gebruiker, is de taal van het bericht afhankelijk van de volgende factoren:

* De taal van de huidige gebruiker.
  * De taal die is gedetecteerd door de browser van de gebruiker.
  * Andere verificatiescenario's gedragen zich mogelijk anders.
* De taal van alle beschikbare aangepaste berichten.
  * Deze taal wordt gekozen door de beheerder wanneer er een aangepast bericht wordt toegevoegd.

Als er bijvoorbeeld slechts één aangepast bericht is, met de taal Duits:

* Een gebruiker die zich in het Duits verifieert, hoort het aangepaste Duitse bericht.
* Een gebruiker die in het Engels wordt geverifieerd, hoort het standaard-Engelse bericht.

### <a name="custom-voice-message-defaults"></a>Standaardinstellingen voor aangepast spraakbericht

De volgende voorbeeldscripts kunnen worden gebruikt om uw eigen aangepaste berichten te maken. Deze zinnen zijn de standaardwaarden als u uw eigen aangepaste berichten niet configureert:

| Berichtnaam | Script |
| --- | --- |
| Verificatie geslaagd | Uw aanmelding is geverifieerd. Goodbye. |
| Extensieprompt | Bedankt dat u het aanmeldingsverificatiesysteem van Microsoft gebruikt. Druk op hekje om door te gaan. |
| Fraudebevestiging | Er is een fraudewaarschuwing verzonden. Neem contact op met de IT-helpdesk van uw bedrijf om uw account te deblokkeren. |
| Begroeting van fraude (standaard) | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Druk op het hekje om de verificatie te voltooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op nul pond om een fraudewaarschuwing in te dienen. Hiermee stelt u het IT-team van uw bedrijf op de hoogte en worden verdere verificatiepogingen geblokkeerd. |
| Gemelde fraude Er is een fraudewaarschuwing verzonden. | Neem contact op met de IT-helpdesk van uw bedrijf om uw account te deblokkeren. |
| Activering | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Druk op het hekje om de verificatie te voltooien. |
| Opnieuw proberen geweigerde verificatie | Verificatie is geweigerd. |
| Opnieuw proberen (Standaard) | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Druk op het hekje om de verificatie te voltooien. |
| Begroeting (standaard) | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Druk op het hekje om de verificatie te voltooien. |
| Begroeting (pincode) | Bedankt dat u het verificatiesysteem voor aanmeldingen van Microsoft gebruikt. Voer uw pincode in, gevolgd door de hekjessleutel om de verificatie te voltooien. |
| Fraudegrot (pincode) | Bedankt dat u het verificatiesysteem voor aanmeldingen van Microsoft gebruikt.  Voer uw pincode in, gevolgd door de hekjessleutel om de verificatie te voltooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op nul pond om een fraudewaarschuwing in te dienen. Hiermee stelt u het IT-team van uw bedrijf op de hoogte en worden verdere verificatiepogingen geblokkeerd. |
| Opnieuw proberen (pincode) | Bedankt dat u het aanmeldingsverificatiesysteem van Microsoft gebruikt. Voer uw pincode in, gevolgd door de hekjessleutel om de verificatie te voltooien. |
| Extensieprompt na cijfers | Als deze extensie al is, drukt u op het hekje om door te gaan. |
| Verificatie geweigerd | U kunt zich momenteel niet aanmelden. Probeert u het later nog eens. |
| Activeringsgrot (Standard) | Bedankt dat u het aanmeldingsverificatiesysteem van Microsoft gebruikt. Druk op de hekje om de verificatie te voltooien. |
| Activering opnieuw proberen (Standard) | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Druk op het hekje om de verificatie te voltooien. |
| Activeringsgrot (pincode) | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Voer uw pincode in, gevolgd door het hekje om de verificatie te voltooien. |
| Extensieprompt vóór cijfers | Bedankt voor het gebruik van het verificatiesysteem voor aanmelding bij Microsoft. Breng deze aanroep over naar de extensie ... |

### <a name="set-up-a-custom-message"></a>Een aangepast bericht instellen

Als u uw eigen aangepaste berichten wilt gebruiken, moet u de volgende stappen voltooien:

1. Blader naar **Azure Active Directory**  >  **instellingen voor**  >  **telefoongesprekken voor beveiligings-MFA.**  >  
1. Selecteer **Begroeting toevoegen.**
1. Kies het **Type** begroeting, zoals *Begroeting (standaard)* of *Verificatie is geslaagd.*
1. Selecteer de **Taal** op basis van de vorige sectie over [aangepast gedrag van de berichttaal.](#custom-message-language-behavior)
1. Blader naar en selecteer een *MP3- of* *WAV-geluidsbestand* dat u wilt uploaden.
1. Wanneer u klaar bent, **selecteert u Toevoegen** en vervolgens **Opslaan.**

## <a name="mfa-service-settings"></a>Instellingen voor MFA-service

Instellingen voor app-wachtwoorden, vertrouwde IP's, verificatieopties en meervoudige verificatie onthouden voor Azure AD Multi-Factor Authentication vindt u in service-instellingen. Dit is meer een verouderde portal en maakt geen deel uit van de reguliere Azure AD-portal.

Service-instellingen zijn toegankelijk vanuit de Azure Portal door te bladeren naar **Azure Active Directory** Security MFA Aan de slag  >    >    >    >  **Aanvullende**  >  **cloudgebaseerde MFA-instellingen configureren.** Er wordt een nieuw venster of tabblad geopend met extra *opties voor service-instellingen.*

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

De _functie Vertrouwde IP-adressen_ van Azure AD Multi-Factor Authentication omzeilt multi-factor authentication-prompts voor gebruikers die zich aanmelden vanuit een gedefinieerd IP-adresbereik. U kunt vertrouwde IP-adresbereiken instellen voor uw on-premises omgevingen, zodat wanneer gebruikers zich op een van deze locaties bevinden, er geen Azure AD Multi-Factor Authentication-prompt is. Voor _de functie Voor vertrouwde IP's_ van Azure AD Multi-Factor Authentication is Azure AD Premium P1-editie vereist. 

> [!NOTE]
> De vertrouwde IP-adressen kunnen alleen privé-IP-adresbereiken bevatten wanneer u de MFA-server gebruikt. Voor Azure AD Multi-Factor Authentication in de cloud kunt u alleen openbare IP-adresbereiken gebruiken.
>
> IPv6-bereik wordt alleen ondersteund in de [interface Benoemde locatie (preview).](../conditional-access/location-condition.md)

Als uw organisatie de NPS-extensie implementeert om MFA te bieden aan on-premises toepassingen, ziet u dat het bron-IP-adres altijd de NPS-server is waar de verificatiepoging doorheen loopt.

| Azure AD-tenanttype | Opties voor vertrouwde IP-functies |
|:--- |:--- |
| Beheerd |**Specifiek bereik van IP-adressen:** beheerders geven een bereik van IP-adressen op die meervoudige verificatie kunnen omzeilen voor gebruikers die zich aanmelden vanaf het intranet van het bedrijf. Er kunnen maximaal 50 vertrouwde IP-adresbereiken worden geconfigureerd.|
| Federatief |**Alle federatief gebruikers:** alle federatief gebruikers die zich vanuit de organisatie aanmelden, kunnen meervoudige verificatie omzeilen. De gebruikers omzeilen verificatie met behulp van een claim die is uitgegeven door Active Directory Federation Services (AD FS).<br/>**Specifiek bereik van IP-adressen:** beheerders geven een bereik van IP-adressen op die meervoudige verificatie kunnen omzeilen voor gebruikers die zich aanmelden vanaf het intranet van het bedrijf. |

Een vertrouwde IP-bypass werkt alleen vanuit het bedrijfsintranet. Als u de optie **Alle federatief** gebruikers selecteert en een gebruiker zich van buiten het bedrijfsintranet meldt, moet de gebruiker zich verifiëren met meervoudige verificatie. Het proces is hetzelfde, zelfs als de gebruiker een AD FS claim.

### <a name="end-user-experience-inside-of-corpnet"></a>Eindgebruikerservaring binnen corpnet

Wanneer de functie voor vertrouwde IP's is uitgeschakeld, is meervoudige verificatie vereist voor browserstromen. App-wachtwoorden zijn vereist voor oudere uitgebreide clienttoepassingen.

Wanneer vertrouwde IP's worden gebruikt, is meervoudige verificatie niet vereist voor browserstromen. App-wachtwoorden zijn niet vereist voor oudere uitgebreide clienttoepassingen, mits de gebruiker geen app-wachtwoord heeft gemaakt. Nadat een app-wachtwoord in gebruik is, blijft het wachtwoord vereist.

### <a name="end-user-experience-outside-corpnet"></a>Eindgebruikerservaring buiten corpnet

Ongeacht of er een vertrouwd IP-adres is gedefinieerd, is meervoudige verificatie vereist voor browserstromen. App-wachtwoorden zijn vereist voor oudere uitgebreide clienttoepassingen.

### <a name="enable-named-locations-by-using-conditional-access"></a>Benoemde locaties inschakelen met voorwaardelijke toegang

U kunt regels voor voorwaardelijke toegang gebruiken om benoemde locaties te definiëren met behulp van de volgende stappen:

1. Zoek en Azure Portal in de Azure Active Directory **en** selecteer deze. Blader vervolgens naar **Benoemde** locaties voor  >  **voorwaardelijke** toegang  >  **voor beveiliging.**
1. Selecteer **Nieuwe locatie.**
1. Voer een naam in voor de locatie.
1. Selecteer **Markeren als vertrouwde locatie.**
1. Voer het IP-bereik in CIDR-notatie in voor uw omgeving, zoals *40.77.182.32/27.*
1. Selecteer **Maken**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>De functie Vertrouwde IP's inschakelen met behulp van voorwaardelijke toegang

Als u vertrouwde IP's wilt inschakelen met beleid voor voorwaardelijke toegang, moet u de volgende stappen voltooien:

1. Zoek en Azure Portal in de Azure Active Directory **en** selecteer deze. Blader vervolgens naar **Benoemde** locaties voor  >   **voorwaardelijke** toegang  >  **voor beveiliging.**
1. Selecteer **Configure MFA trusted IPs**.
1. Kies op **de pagina** Service-instellingen onder **Vertrouwde IP's** een van de volgende twee opties:

   * **Voor aanvragen van federatief gebruikers die afkomstig zijn van mijn intranet:** schakel het selectievakje in om deze optie te kiezen. Alle federatief gebruikers die zich aanmelden vanaf het bedrijfsnetwerk multi-factor authentication omzeilen met behulp van een claim die is uitgegeven door AD FS. Zorg ervoor AD FS een regel heeft om de intranetclaim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen van een specifiek bereik van openbare IP-adressen:** als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Gebruik notatie zoals **xxx.xxx.xxx.0/24** voor IP-adressen die zich in het bereik xxx.xxx.xxx.1 tot en met xxx.xxx.254 hebben.
      * Gebruik voor één IP-adres notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer maximaal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, omzeilen meervoudige verificatie.

1. Selecteer **Opslaan**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>De functie Vertrouwde IP's inschakelen met behulp van service-instellingen

Als u geen beleid voor voorwaardelijke toegang wilt gebruiken om vertrouwde IP's in te schakelen, kunt u de *service-instellingen* voor Azure AD Multi-Factor Authentication configureren met behulp van de volgende stappen:

1. Zoek en Azure Portal in de Azure Active Directory **en** kies vervolgens **Gebruikers.**
1. Selecteer **Multi-Factor Authentication**.
1. Selecteer onder Multi-Factor Authentication de **optie service-instellingen.**
1. Kies op **de pagina** Service-instellingen onder **Vertrouwde IP's** een (of beide) van de volgende twee opties:

   * **Voor aanvragen van federatief gebruikers op mijn intranet:** schakel het selectievakje in om deze optie te kiezen. Alle federatief gebruikers die zich aanmelden vanaf het bedrijfsnetwerk multi-factor authentication omzeilen met behulp van een claim die wordt uitgegeven door AD FS. Zorg ervoor AD FS een regel heeft om de intranetclaim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * Voor aanvragen van een opgegeven bereik van **IP-adressubnetten:** als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met behulp van CIDR-notatie.
      * Gebruik voor IP-adressen binnen het bereik xxx.xxx.xxx.1 tot en met xxx.xxx.xxx.254 de notatie **zoals xxx.xxx.xxx.0/24.**
      * Gebruik voor één IP-adres een notatie zoals **xxx.xxx.xxx.xxx/32**.
      * Voer maximaal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen, omzeilen meervoudige verificatie.

1. Selecteer **Opslaan**.

## <a name="verification-methods"></a>Verificatiemethoden

U kunt de verificatiemethoden kiezen die beschikbaar zijn voor uw gebruikers in de portal voor service-instellingen. Wanneer uw gebruikers hun accounts registreren voor Azure AD Multi-Factor Authentication, kiezen ze hun voorkeursverificatiemethode uit de opties die u hebt ingeschakeld. Richtlijnen voor het gebruikersinschrijvingsproces worden gegeven in [Mijn account instellen voor meervoudige verificatie.](../user-help/multi-factor-authentication-end-user-first-time.md)

De volgende verificatiemethoden zijn beschikbaar:

| Methode | Beschrijving |
|:--- |:--- |
| Bellen naar telefoon |Plaatst een geautomatiseerde spraakoproep. De gebruiker beantwoordt het gesprek en drukt # in op de toetsenblok van de telefoon om te authenticeren. Het telefoonnummer wordt niet gesynchroniseerd met on-premises Active Directory. |
| Sms-bericht naar telefoon |Hiermee wordt een sms-bericht met een verificatiecode verzenden. De gebruiker wordt gevraagd de verificatiecode in te voeren in de aanmeldingsinterface. Dit proces wordt sms in één manier genoemd. Sms in twee punten betekent dat de gebruiker een bepaalde code moet terugteksten. Sms in twee punten is afgeschaft en wordt na 14 november 2018 niet meer ondersteund. Beheerders moeten een andere methode inschakelen voor gebruikers die eerder sms in twee punten hebben gebruikt.|
| Melding via mobiele app |Hiermee wordt een pushmelding naar uw telefoon of geregistreerde apparaat verzendt. De gebruiker bekijkt de melding en selecteert **Verifiëren om** de verificatie te voltooien. De Microsoft Authenticator-app is beschikbaar [voor Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Verificatiecode van mobiele app of hardware-token |De Microsoft Authenticator app genereert elke 30 seconden een nieuwe OATH-verificatiecode. De gebruiker voert de verificatiecode in de aanmeldingsinterface in. De Microsoft Authenticator-app is beschikbaar [voor Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

Zie Welke verificatie- en verificatiemethoden zijn beschikbaar [in Azure AD?](concept-authentication-methods.md) voor meer informatie.

### <a name="enable-and-disable-verification-methods"></a>Verificatiemethoden in- en uitschakelen

Als u verificatiemethoden wilt in- of uitschakelen, moet u de volgende stappen voltooien:

1. Zoek en Azure Portal in de Azure Active Directory **en** kies vervolgens **Gebruikers.**
1. Selecteer **Multi-Factor Authentication**.
1. Selecteer onder Multi-Factor Authentication de **optie service-instellingen.**
1. Selecteer op **de pagina Service-instellingen** onder **Verificatieopties** de methoden die aan uw gebruikers moeten worden verstrekt.
1. Klik op **Opslaan**.

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication onthouden

Met _de functie Multi-Factor Authentication_ onthouden kunnen gebruikers volgende verificaties een opgegeven aantal dagen omzeilen nadat ze zich met Behulp van Multi-Factor Authentication hebben aangemeld bij een apparaat. Selecteer een duur van 90 dagen of langer om de bruikbaarheid te verbeteren en het aantal keren dat een gebruiker MFA moet uitvoeren op hetzelfde apparaat te minimaliseren.

> [!IMPORTANT]
> Als een account of apparaat is aangetast, kan het onthouden van Multi-Factor Authentication voor vertrouwde apparaten van invloed zijn op de beveiliging. Als een bedrijfsaccount wordt aangetast of als een vertrouwd apparaat verloren of gestolen is, moet u [MFA-sessies intrekken.](howto-mfa-userdevicesettings.md)
>
> Met de herstelactie wordt de vertrouwde status van alle apparaten ingetrokken en moet de gebruiker meervoudige verificatie opnieuw uitvoeren. U kunt uw gebruikers ook instrueren om Multi-Factor Authentication te herstellen op hun eigen apparaten, zoals vermeld in Uw instellingen voor [meervoudige verificatie beheren.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Hoe de functie werkt

De functie Multi-Factor Authentication onthouden stelt een permanente cookie in op de browser wanneer een gebruiker de optie Niet opnieuw vragen **om X** dagen bij het aanmelden selecteert. De gebruiker wordt niet opnieuw om Multi-Factor Authentication gevraagd vanuit dezelfde browser totdat de cookie verloopt. Als gebruikers een andere browser op hetzelfde apparaat openen of hun cookies verwijderen, wordt de gebruiker opnieuw gevraagd om dit te controleren.

De **optie Niet opnieuw vragen** om X dagen wordt niet weergegeven in niet-browsertoepassingen, ongeacht of de app moderne verificatie ondersteunt. Deze apps gebruiken _vernieuwingstokens_ die elk uur nieuwe toegangstokens bieden. Wanneer een vernieuwings token wordt gevalideerd, controleert Azure AD of de laatste meervoudige verificatie binnen het opgegeven aantal dagen heeft plaatsgevonden.

De functie vermindert het aantal verificaties voor web-apps, wat normaal gesproken elke keer wordt gevraagd. De functie kan het aantal verificaties verhogen voor moderne verificatie-clients die normaal gesproken elke 180 dagen worden gevraagd, als een lagere duur is geconfigureerd. Kan ook het aantal verificaties verhogen in combinatie met beleid voor voorwaardelijke toegang.

> [!IMPORTANT]
> De functie **Multi-Factor Authentication** onthouden is niet compatibel met de functie **Aangemeld** blijven van AD FS wanneer gebruikers meervoudige verificatie uitvoeren voor AD FS via Azure Multi-Factor Authentication-server of een oplossing voor meervoudige verificatie van derden.
>
> Als uw  gebruikers Ervoor zorgen dat ik aangemeld blijf op AD FS en hun apparaat ook markeren als vertrouwd voor  Multi-Factor Authentication, wordt de gebruiker niet automatisch geverifieerd nadat het aantal dagen voor meervoudige verificatie onthouden is verlopen. Azure AD vraagt een nieuwe meervoudige verificatie aan, maar AD FS retourneert een token met de oorspronkelijke Multi-Factor Authentication-claim en -datum, in plaats van meervoudige verificatie opnieuw uit te voeren. **Met deze reactie wordt een verificatielus tussen Azure AD en AD FS.**
>
> De **functie Multi-Factor Authentication** onthouden is niet compatibel met B2B-gebruikers en is niet zichtbaar voor B2B-gebruikers wanneer ze zich aanmelden bij de uitgenodigde tenants.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication onthouden inschakelen

Als u de optie voor gebruikers wilt inschakelen en configureren om hun MFA-status te onthouden en prompts over te nemen, moet u de volgende stappen voltooien:

1. Zoek en Azure Portal in de Azure Active Directory **en** kies vervolgens **Gebruikers.**
1. Selecteer **Multi-Factor Authentication**.
1. Selecteer onder Multi-Factor Authentication de **optie service-instellingen.**
1. Selecteer op de pagina  **Service-instellingen** onder Meervoudige verificatie onthouden de optie Gebruikers toestaan meervoudige verificatie te onthouden op apparaten die **ze vertrouwen.**
1. Stel het aantal dagen in dat vertrouwde apparaten meervoudige verificatie mogen omzeilen. Voor een optimale gebruikerservaring kunt u de duur verlengen *naar 90* of meer dagen.
1. Selecteer **Opslaan**.

### <a name="mark-a-device-as-trusted"></a>Een apparaat markeren als vertrouwd

Nadat u de functie Multi-Factor Authentication onthouden hebt ingeschakeld, kunnen gebruikers een apparaat markeren als vertrouwd wanneer ze zich aanmelden door de optie Niet opnieuw **vragen te selecteren.**

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de beschikbare methoden voor gebruik in Azure AD Multi-Factor Authentication, zie Welke verificatie- en verificatiemethoden zijn [beschikbaar in Azure Active Directory?](concept-authentication-methods.md)
