---
title: Vragen & antwoorden over Microsoft Authenticator app-Azure AD
description: Veelgestelde vragen en antwoorden over de micro soft-verificatie-app en verificatie met twee factoren.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 369ec11050fa7dbc09159d88793685ad5fbdb3e5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053957"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Veelgestelde vragen over de Microsoft Authenticator-app

In dit artikel vindt u antwoorden op veelgestelde vragen over de app Microsoft Authenticator. Als u geen antwoord op uw vraag ziet, gaat u naar het [Microsoft Authenticator app-forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

De Microsoft Authenticator-app heeft de Azure Authenticator-app vervangen en de aanbevolen app wanneer u Azure AD Multi-Factor Authentication gebruikt. De Microsoft Authenticator-app is beschikbaar voor [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) en [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="registering-a-device"></a>Een apparaat registreren

**V**: wordt een apparaat geregistreerd om het bedrijf of de service toegang te geven tot mijn apparaat?

**A**: het registreren van een apparaat geeft uw apparaat toegang tot de services van uw organisatie en biedt uw organisatie geen toegang tot uw apparaat.

### <a name="error-adding-account"></a>Fout bij het toevoegen van het account

**V**: als ik mijn account probeer toe te voegen, krijg ik een fout bericht met de melding dat het account dat u probeert toe te voegen op dit moment niet geldig is. Neem contact op met de beheerder om dit probleem op te lossen (validatie van uniekheid). " Wat moet ik doen?

**A**: Neem contact op met uw beheerder en laat ze weten dat u uw account niet aan de verificator kunt toevoegen vanwege een validatie probleem van uniekheid. U moet uw aanmeldings naam opgeven zodat uw beheerder u in uw organisatie kan zien.

### <a name="legacy-apns-support-deprecated"></a>Verouderde APNs-ondersteuning afgeschaft

**V**: de verouderde binaire interface voor Apple Push Notification Service wordt afgeschaft in november 2020, hoe kan ik Microsoft Authenticator/telefoon factor blijven gebruiken om je aan te melden?

**A**: [Apple afschaffing](https://developer.apple.com/news/?id=11042019a) van push meldingen die gebruikmaken van de binaire interface voor IOS-apparaten, zoals die worden gebruikt door de telefoon factor. Als u push meldingen wilt blijven ontvangen, raden we u aan de verificator-app bij te werken naar de meest recente versie van de app. Ondertussen kunt u het probleem omzeilen door hand matig te controleren op meldingen in de verificator-app.

### <a name="app-lock-feature"></a>App-vergrendelings functie

**V**: wat is app-vergren deling en hoe kan ik het gebruiken om ik beter beveiligd te houden?

**A**: app-vergren deling helpt uw eenmalige verificatie codes, app-gegevens en app-instellingen veiliger te laten blijven. Wanneer app-vergren deling is ingeschakeld, wordt u gevraagd om te verifiëren met behulp van de pincode van uw apparaat of biometrisch telkens wanneer u een verificator opent. App-vergren deling zorgt er ook voor dat u de enige bent die meldingen kan goed keuren door te vragen om uw pincode of biometrisch telkens wanneer u een aanmeldings melding goedkeurt. U kunt app-vergren deling in-of uitschakelen op de instellingen pagina van de verificator. App-vergren deling is standaard ingeschakeld wanneer u een pincode of biometrische op uw apparaat instelt.<br><br>Helaas is er geen garantie dat een app-vergren deling geen toegang meer heeft tot de verificator. Dat komt doordat apparaatregistratie kan plaatsvinden op andere locaties buiten de verificator, zoals in de instellingen van een Android-account of in de bedrijfsportal-app.

### <a name="windows-mobile-retired"></a>Windows Mobile buiten gebruik gesteld

**V**: Ik heb een Windows Mobile-apparaat en de Microsoft Authenticator op Windows Mobile is afgeschaft. Kan ik de verificatie voortzetten met de app?

**A**: alle verificaties die gebruikmaken van de Microsoft Authenticator op Windows Mobile, worden na 15 juli 2020 afgetrokken. We raden u ten zeerste aan een alternatieve verificatie methode te gebruiken om te voor komen dat uw accounts worden vergrendeld.<br>Alternatieve opties voor zakelijke gebruikers zijn onder andere:<br><ul><li>Instellen van de Microsoft Authenticator voor [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [IOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[SMS instellen](multi-factor-authentication-setup-phone-number.md) voor het ontvangen van verificatie codes.</li><li>Stel telefoon nummer in om [telefoon gesprekken te ontvangen om hun identiteit te verifiëren](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Alternatieve opties voor persoonlijke Microsoft-account gebruikers zijn onder andere:<br><ul><li>Instellen van de Microsoft Authenticator voor [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [IOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Instellen van een alternatieve aanmeldings methode (SMS of e-mail bericht) door uw beveiligings gegevens op de [pagina beveiliging van micro soft-accounts](https://account.microsoft.com/security/)bij te werken.</li></ul>

### <a name="android-screenshots"></a>Android-scherm afbeeldingen

**V**: kan ik scherm opnamen maken van mijn otp-codes (one-time password) op de Android-verificator?

**A**: vanaf de release-6.2003.1704 van de verificator Android worden standaard alle otp-codes verborgen wanneer er een scherm opname van de verificator wordt gemaakt. Als u uw OTP-codes in scherm afbeeldingen wilt zien of andere apps toestaan het scherm van de verificator vast te leggen, kunt u. Schakel de instelling **scherm opname** in verificator in en start de app opnieuw.

### <a name="delete-stored-data"></a>Opgeslagen gegevens verwijderen

**V**: welke gegevens worden door de verificator in mijn naam opgeslagen en hoe kan ik deze verwijderen?

**A**: met de verificator-app worden drie soorten informatie verzameld:

- Account gegevens die u opgeeft wanneer u uw account toevoegt. Deze gegevens kunnen worden verwijderd door uw account te verwijderen.
- Diagnostische logboek gegevens die alleen in de app blijven totdat u **feedback verzendt** in het bovenste menu van de app om logboeken naar micro soft te verzenden. Deze logboeken kunnen persoonlijke gegevens bevatten, zoals e-mail adressen, server adressen of IP-adressen. Ze kunnen ook apparaatgegevens bevatten, zoals de naam van het apparaat en de versie van het besturings systeem. Persoonlijke gegevens die worden verzameld, zijn beperkt tot informatie die nodig is om app-problemen op te lossen. U kunt op elk gewenst moment door deze logboek bestanden in de app bladeren om de gegevens te zien die worden verzameld. Als u uw logboek bestanden verzendt, zullen de technici van de verificatie-app deze alleen gebruiken om problemen met door klanten gemelde problemen op te lossen.
- Niet-persoonlijk identificeer bare gebruiks gegevens, zoals ' Start account flow toevoegen/account toegevoegd ' of ' melding goedgekeurd '. Deze gegevens vormen een integraal onderdeel van onze technische beslissingen. Uw gebruik helpt ons te bepalen waar de apps kunnen worden verbeterd op een manier die belang rijk voor u is. U ziet een melding van deze gegevens verzameling wanneer u de app voor de eerste keer gebruikt. U krijgt een melding dat deze kan worden uitgeschakeld op de pagina **instellingen** van de app   . U kunt deze instelling op elk gewenst moment in-of uitschakelen.

### <a name="codes-in-the-app"></a>Codes in de app

**V**: wat zijn de codes in de app voor?

**A**: wanneer u verificator opent, worden de toegevoegde accounts als tegels weer geven. Uw werk-of school account en uw persoonlijke micro soft-accounts hebben zes of acht cijfer nummers zichtbaar in de weer gave volledig scherm van het account (toegankelijk door te tikken op de tegel account). Voor andere accounts ziet u een getal van zes of acht cijfers op de pagina **accounts** van de app.<br>U gebruikt deze codes als wacht woord voor eenmalig gebruik om te controleren of u bent wie u zegt. Nadat u zich hebt aangemeld met uw gebruikers naam en wacht woord, typt u de verificatie code die aan dat account is gekoppeld. Als u zich bijvoorbeeld Katy aanmeldt bij uw contoso-account, tikt u op de tegel account en gebruikt u de verificatie code 895823. Volg dezelfde stappen voor het Outlook-account.<br>Tik op de tegel contoso-account.<br>![Account tegels in de verificator-app](media/user-help-auth-app-faq/katy-signin.png)<br>Nadat u op de tegel contoso-account hebt getikt, wordt de verificatie code weer gegeven in het volledige scherm.<br>![Verificatie code in de account tegel in verificator](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Aftellings timer

**V**: Waarom houdt het nummer naast de code over van de telling?

**A**: er wordt mogelijk een timer van 30 seconden weer geven naast uw actieve verificatie code. Deze timer is zo dat u zich nooit twee keer aanmeldt met dezelfde code. In tegens telling tot een wacht woord is het niet meer nodig om dit nummer te onthouden. Het idee is dat alleen iemand die toegang heeft tot uw telefoon, uw code weet.

### <a name="grayed-account-tile"></a>Tegel voor grijze accounts

**V**: Waarom is mijn account tegel grijs?

**A**: sommige organisaties vereisen dat verificator werkt met eenmalige aanmelding en om organisatie bronnen te beveiligen. In dit geval wordt het account niet gebruikt voor verificatie in twee stappen en wordt het grijs weer gegeven of niet actief. Dit type account wordt vaak een Broker-account genoemd.

### <a name="device-registration"></a>Apparaatregistratie

**V**: wat is apparaatregistratie?
**A**: uw organisatie vereist mogelijk dat u het apparaat registreert om de toegang tot beveiligde bronnen, zoals bestanden en apps, bij te houden. Ze kunnen ook voorwaardelijke toegang inschakelen om het risico van ongewenste toegang tot deze bronnen te verminderen. U kunt de registratie van uw apparaat bij **instellingen** opheffen, maar u kunt de toegang tot e-mail berichten in Outlook, bestanden in OneDrive verliezen en u verliest de mogelijkheid om de aanmelding via de telefoon te gebruiken.

### <a name="verification-codes-when-connected"></a>Verificatie codes bij verbinding

**V**: moet ik verbinding hebben met internet of mijn netwerk om de verificatie codes op te halen en te gebruiken?

**A**: voor de codes is het niet vereist dat u op Internet bent of als u verbinding hebt met gegevens, dus u hebt geen telefoon service nodig om u aan te melden. Omdat de app niet meer wordt uitgevoerd zodra u deze sluit, wordt de accu niet leeg gemaakt.

### <a name="no-notifications-when-app-is-closed"></a>Geen meldingen wanneer de app wordt gesloten

**V**: Waarom krijg ik alleen meldingen wanneer de app is geopend? Wanneer de app is gesloten, worden er geen meldingen weer geven.

**A**: als u meldingen ontvangt, maar geen waarschuwing, zelfs met uw beltoon op, moet u de app-instellingen controleren. Zorg ervoor dat de app is ingeschakeld om geluid te gebruiken of te trillen voor meldingen. Als u helemaal geen meldingen ontvangt, controleert u de volgende voor waarden:<ul><li>Is uw telefoon in geen enkele Store of Stille modus? Deze modi kunnen verhinderen dat apps meldingen verzenden.</li><li>Kunt u meldingen ontvangen van andere apps? Als dat niet het geval is, is het mogelijk dat er een probleem is met de netwerk verbindingen op uw telefoon of het meldingen kanaal van Android of Apple. U kunt proberen om uw netwerk verbindingen op te lossen via uw telefoon instellingen. Mogelijk moet u contact opnemen met uw service provider om u te helpen bij het Android-of Apple-meldings kanaal.</li><li>Kunt u meldingen ontvangen voor sommige accounts in de app, maar niet voor anderen? Als dit het geval is, verwijdert u het problematische account uit uw app, voegt u het opnieuw toe, zodat er meldingen worden weer geven en kunt u zien of het probleem is opgelost.</li></ul>Als u al deze stappen hebt uitgevoerd en nog steeds problemen ondervindt, kunt u het beste uw logboek bestanden verzenden voor diagnostische gegevens. Open de app, ga naar het menu op het hoogste niveau van de app en selecteer vervolgens **Feedback verzenden**. Daarna gaat u naar het [Microsoft Authenticator-app-forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) en vertelt u micro soft het probleem dat u ziet en de stappen die u hebt geprobeerd.

### <a name="switch-to-push-notifications"></a>Overschakelen naar Push meldingen

**V**: Ik gebruik de verificatie codes in de app, maar hoe schakel ik over naar de push meldingen?

**A**: u kunt meldingen instellen voor uw werk-of school account (indien toegestaan door uw beheerder) of voor uw persoonlijke Microsoft-account. Meldingen werken niet voor accounts van derden, zoals Google of Facebook.<br>Als u uw persoonlijke account wilt overschakelen op meldingen, moet u uw apparaat opnieuw registreren bij het account. Ga naar **account toevoegen**, selecteer **persoonlijk micro soft-account** en meld u vervolgens aan met uw gebruikers naam en wacht woord.<br>Voor uw werk-of school account beslist uw organisatie of u meldingen met één klik wilt toestaan.

### <a name="notifications-for-other-accounts"></a>Meldingen voor andere accounts

**V**: meldingen werken voor niet-micro soft-accounts?

**A**: Nee, meldingen werken alleen met micro soft-accounts en Azure Active Directory-accounts. Als uw werk of school gebruikmaakt van Azure AD-accounts, kunnen ze deze functie uitschakelen.

### <a name="backup-and-recovery"></a>Back-ups maken en herstellen

**V**: Ik heb een nieuw apparaat of ik heb mijn apparaat teruggezet vanuit een back-up. Hoe kan ik mijn accounts in verificator opnieuw instellen?

**A**: als u **Cloud back-up** op uw oude apparaat hebt ingeschakeld, kunt u uw oude back-up gebruiken om uw account referenties op uw nieuwe IOS-of Android-apparaat te herstellen. Zie het artikel [back-ups maken en account referenties herstellen met verificator](user-help-auth-app-backup-recovery.md) voor meer informatie.

### <a name="lost-device"></a>Apparaat verloren

**V**: Ik ben mijn apparaat kwijt of verplaatst naar een nieuw apparaat. Hoe kan ik weet u zeker dat meldingen niet meer naar mijn oude apparaat gaan?

**A**: door verificator toe te voegen aan het nieuwe apparaat, wordt de app niet automatisch van het oude apparaat verwijderd. Zelfs het verwijderen van de app van uw oude apparaat is niet voldoende. U moet de app van uw oude apparaat verwijderen en micro soft of uw organisatie laten weten dat het apparaat moet worden verg eten en de registratie ongedaan maken.<ul><li>**De app verwijderen van een apparaat met behulp van een persoonlijke Microsoft-account.** Ga naar het verificatie gebied in de twee stappen van de pagina beveiliging van uw [account](https://account.microsoft.com/security)   en kies ervoor om verificatie voor uw oude apparaat uit te scha kelen.</li><li>**De app verwijderen van een apparaat met behulp van een werk-of school Microsoft-account.** Ga naar het verificatie gebied in de twee stappen van uw [MyApps-pagina](https://myapps.microsoft.com/) of de aangepaste portal van uw organisatie om de verificatie van uw oude apparaat uit te scha kelen.</li></ul>

### <a name="remove-account-from-app"></a>Account uit de app verwijderen

**V**: hoe kan ik een account verwijderen uit de app?

**A**: Tik op de account tegel voor het account dat u wilt verwijderen uit de app om het volledige scherm van de account weer te geven. Tik op **account verwijderen** om het account uit de app te verwijderen.<br>Als u een apparaat hebt dat bij uw organisatie is geregistreerd, hebt u mogelijk een extra stap nodig om uw account te verwijderen. Op deze apparaten wordt verificator automatisch geregistreerd als een apparaat-beheerder. Als u de app volledig wilt verwijderen, moet u de registratie van de app eerst ongedaan maken in de app-instellingen.

### <a name="too-many-permissions"></a>Te veel machtigingen

**V**: waarom verzoekt de app zoveel machtigingen?

**A**: Hier vindt u de volledige lijst met machtigingen die kunnen worden gevraagd en hoe deze worden gebruikt door de app. De specifieke machtigingen die u ziet, zijn afhankelijk van het type telefoon dat u hebt.<ul><li>**Locatie**. Soms wil uw organisatie uw locatie weten voordat u toegang krijgt tot bepaalde bronnen. Deze machtiging wordt door de app alleen aangevraagd als uw organisatie een beleid heeft waarvoor de locatie is vereist.</li><li>**Biometrische hardware gebruiken.** Voor sommige werk-en school accounts is een extra pincode vereist wanneer u uw identiteit verifieert. De app vereist dat u toestemming hebt om biometrische of gezichts herkenning te gebruiken in plaats van de pincode in te voeren.</li><li>**S.** Wordt gebruikt voor het scannen van QR-codes wanneer u een werk, school of niet-Microsoft-account toevoegt.</li><li>**Contact personen en telefoon.** Deze machtiging is vereist voor de app om micro soft-accounts voor werk of school te zoeken op uw telefoon en deze voor u toe te voegen aan de app.</li><li>**SMS.** Wordt gebruikt om ervoor te zorgen dat uw telefoon nummer overeenkomt met het nummer dat wordt geregistreerd wanneer u zich voor de eerste keer aanmeldt met uw persoonlijke Microsoft-account. Er wordt een SMS-bericht verzonden naar de telefoon waarop u de app hebt geïnstalleerd en die een 6-8 cijferige verificatie code bevat. U hoeft deze code niet te vinden en deze op te geven omdat de verificator deze automatisch in het tekst bericht opzoekt.</li><li>**Trekken van andere apps.** De melding die u krijgt dat uw identiteit wordt gecontroleerd, wordt ook weer gegeven op alle andere apps die worden uitgevoerd.</li><li>**Gegevens ontvangen van het internet.** Deze machtiging is vereist voor het verzenden van meldingen.</li><li>**Voor komen dat telefoon wordt geslapend.** Als u uw apparaat registreert bij uw organisatie, kan uw organisatie dit beleid op uw telefoon wijzigen.</li><li>**Trilling beheren.** U kunt kiezen of u een trilling wilt wanneer u een melding ontvangt om uw identiteit te verifiëren.</li><li>**Vingerafdruk hardware gebruiken.** Voor sommige werk-en school accounts is een extra pincode vereist wanneer u uw identiteit verifieert. Om het proces gemakkelijker te maken, kunt u uw vinger afdruk gebruiken in plaats van de pincode in te voeren.</li><li> **Netwerk verbindingen weer geven.** Wanneer u een Microsoft-account toevoegt, is voor de app netwerk/Internet verbinding vereist.</li><li>**Lees de inhoud van uw opslag**. Deze machtiging wordt alleen gebruikt wanneer u een technisch probleem meldt via de app-instellingen. Er wordt een aantal gegevens uit uw opslag verzameld om het probleem op te sporen.</li><li>**Volledige netwerk toegang.** Deze machtiging is vereist voor het verzenden van meldingen om uw identiteit te verifiëren.</li><li>**Wordt uitgevoerd bij het opstarten.** Als u uw telefoon opnieuw opstart, zorgt u ervoor dat u nog steeds meldingen ontvangt om uw identiteit te verifiëren.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Aanvragen goed keuren zonder de vergren deling

**V**: waarom biedt verificator u de mogelijkheid om een aanvraag goed te keuren zonder het apparaat te ontgrendelen?

**A**: u hoeft uw apparaat niet te ontgrendelen om verificatie aanvragen goed te keuren omdat u er zeker van moet zijn dat u uw telefoon met u hebt. Verificatie in twee stappen vereist twee dingen: een ding die u kent en wat u hebt. Wat u precies weet, is uw wacht woord. Wat u hebt, is uw telefoon (ingesteld met verificator en geregistreerd als een verificatie op basis van meerdere factoren). De telefoon en het goed keuren van de aanvraag voldoen daarom aan de criteria voor de tweede verificatie factor.

### <a name="activity-notifications"></a>Activiteiten meldingen

**V**: Waarom krijg ik meldingen over mijn account activiteit?

**A**: activiteiten worden direct naar verificator verzonden wanneer een wijziging wordt aangebracht in uw persoonlijke micro soft-accounts, zodat u beter beveiligd blijft. We hebben deze meldingen alleen eerder verzonden via e-mail en SMS. Zie [Wat gebeurt er als er sprake is van een ongebruikelijke aanmelding bij uw account](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)voor meer informatie over deze activiteiten. Meld u aan bij de pagina [waar kunnen we contact met u opnemen met niet-kritieke account waarschuwingen](https://account.live.com/SecurityNotifications/Update) van uw account om te wijzigen waar u uw meldingen ontvangt.

### <a name="one-time-passcodes"></a>Eenmalige wachtwoord codes

**V**: mijn eenmalige wachtwoord codes werken niet. Wat moet ik doen?

**A**: Controleer of de datum en tijd op het apparaat juist zijn en automatisch worden gesynchroniseerd. Als de datum en tijd onjuist zijn of niet synchroon zijn, werkt de code niet.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**V**: het Windows 10 Mobile-besturings systeem is afgeschaft op 2019 december. Zullen de Microsoft Authenticator op Windows Mobile-besturings systemen ook worden afgeschaft?

**A**: verificator op alle Windows Mobile-besturings systemen wordt niet ondersteund na 28 februari 2020. Gebruikers komen niet in aanmerking voor het ontvangen van nieuwe updates van de app, die de bovengenoemde datum post. Na 28 februari 2020 micro soft-services die momenteel ondersteuning bieden voor verificaties met behulp van de Microsoft Authenticator op alle Windows Mobile-besturings systemen, worden hun ondersteuning buiten gebruik gesteld. Als u zich wilt aanmelden bij micro soft-Services, raden wij u ten zeerste aan om over te scha kelen naar een ander verificatie mechanisme vóór deze datum.

### <a name="default-mail-app"></a>Standaard e-mail-app

**V**: als u zich aanmeldt bij mijn werk-of school account met behulp van de standaard e-mail-app die wordt geleverd met IOS, wordt u door verificator gevraagd om de gegevens van de beveiligings verificatie. Ik krijg een fout melding wanneer ik deze informatie heb ingevoerd en terugkeert naar de e-mail-app. Wat kan ik doen?

**A**: dit is waarschijnlijk omdat uw aanmelding en uw e-mail-app zich in twee verschillende apps voordoen, waardoor het eerste aanmeldings proces van de achtergrond niet meer werkt en niet kan worden uitgevoerd. Om dit probleem op te lossen, raden we u aan om het **Safari** -pictogram aan de rechter kant van het scherm te selecteren terwijl u zich aanmeldt bij uw e-mail-app. Als u overstapt op Safari, wordt het hele aanmeldings proces uitgevoerd in één app, zodat u zich kunt aanmelden bij de app.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**V**: Waarom heb ik problemen met Apple Watch op watchOS 7?

**A**: er is een probleem met het goed keuren van meldingen op watchOS 7 en we werken met Apple om dit vast te krijgen. In de tussen tijd moet u in plaats daarvan alle meldingen die de Microsoft Authenticator watchOS-app vereisen, op uw telefoon goed keuren.

### <a name="apple-watch-doesnt-show-accounts"></a>Accounts worden niet weer gegeven in Apple Watch

**V**: Waarom worden niet alle accounts weer gegeven wanneer ik verificator open voor mijn Apple Watch?

**A**: verificator ondersteunt alleen micro soft-accounts voor persoonlijk of school of werk met push meldingen voor de Apple Watch-Companion-app. Voor uw andere accounts, zoals Google of Facebook, moet u de verificator-app op uw telefoon openen om uw verificatie codes te bekijken.

### <a name="apple-watch-notifications"></a>Apple Watch-meldingen

**V**: Waarom kan ik geen meldingen op mijn Apple Watch goed keuren of weigeren?

**A**: Controleer eerst of u een upgrade hebt uitgevoerd naar verificator versie 6.0.0 of hoger op uw iPhone. Daarna opent u de Microsoft Authenticator Companion-app op uw Apple Watch en zoekt u naar alle accounts met een knop **instellen** eronder. Voltooi het installatie proces om meldingen voor deze accounts goed te keuren.

### <a name="apple-watch-communication-error"></a>Communicatie fout van Apple Watch

**V**: Ik krijg een communicatie fout tussen de Apple Watch en mijn telefoon. Wat kan ik doen om problemen op te lossen?

**A**: deze fout treedt op wanneer het controle scherm naar de slaap stand gaat voordat de communicatie met uw telefoon is voltooid.<br><b>Als de fout optreedt tijdens de installatie:</b><br>Probeer Setup opnieuw uit te voeren, zodat u zeker weet dat uw controle actief blijft totdat het proces is voltooid. Op hetzelfde moment opent u de app op uw telefoon en reageert u op prompts die worden weer gegeven.<br>Als uw telefoon en kijken nog steeds niet communiceren, kunt u de volgende acties uitvoeren:<ol><li>Geforceerd de Microsoft Authenticator telefoon-app afsluiten en opnieuw openen op uw iPhone.</li><li>Geforceerd afsluiten van de Companion-app op uw Apple Watch.<ol><li> Open de Microsoft Authenticator Companion-app op uw horloge</li><li>Houd de knop aan de zijkant ingedrukt totdat het **afsluit** scherm wordt weer gegeven.</li><li>Laat de knop aan de zijkant los en houd de digitale kroon ingedrukt om de actieve app af te dwingen.</li></ol></li><li>Schakel zowel Bluetooth als Wi-Fi uit voor zowel uw telefoon als uw horloge en schakel ze vervolgens weer in.</li><li>Start uw iPhone en uw horloge opnieuw op.</li></ol><b>Als de fout optreedt wanneer u een melding probeert goed te keuren:</b><br>De volgende keer dat u een melding op uw Apple Watch wilt goed keuren, blijft het scherm actief totdat de aanvraag is voltooid en hoort u het geluid dat aangeeft dat het is gelukt.

### <a name="apple-watch-companion-app-not-syncing"></a>De niet-gesynchroniseerde Companion-app van Apple Watch

**V**: Waarom is de Microsoft Authenticator Companion-app voor Apple Watch-synchronisatie of worden deze niet weer gegeven in mijn horloge?

**A**: als de app niet wordt weer gegeven op uw horloge, voert u de volgende acties uit: <ol><li>Zorg ervoor dat watchOS 4,0 of hoger wordt uitgevoerd op uw horloge.</li><li>Synchroniseer uw Watch opnieuw.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>De Apple Watch-Companion-app is vastgelopen

**V**: mijn app voor Apple Watch is vastgelopen. Kan ik mijn crash logboeken verzenden zodat je het kunt onderzoeken?

**A**: u moet er eerst voor zorgen dat u ervoor hebt gekozen om uw analyses met ons te delen. Als u een TestFlight-gebruiker bent, bent u al aangemeld. Als dat niet het geval is, gaat u naar **instellingen > Privacy > Analytics** en selecteert u de opties **share iPhone & watch bekijken** en **delen met app-ontwikkel aars** .<br>Nadat u zich hebt aangemeld, kunt u proberen om uw crash te reproduceren zodat uw crash logboeken automatisch naar ons worden verzonden om te worden onderzocht. Als u uw crash echter niet kunt reproduceren, kunt u de logboek bestanden hand matig kopiëren en naar ons verzenden.<ol><li>Open de controle-app op uw telefoon, ga naar **instellingen > algemeen** en klik vervolgens op **Watch-analyse kopiëren**.</li><li>Zoek de overeenkomstige crash onder **instellingen > privacybeleid > analytics > Analytics-gegevens** en kopieer de hele tekst vervolgens hand matig.</li><li>Open de verificator op uw telefoon en plak de gekopieerde tekst in het vak  **Geef het probleem dat u** op het punt staat, onder **problemen?** op de pagina  **Feedback verzenden** . </li></ol>

## <a name="autofill-with-authenticator"></a>Automatisch door voeren met verificator

**V**: wat is automatisch door voeren met verificator?

**A**: de verificator-app slaat nu veilig wacht woorden op voor apps en websites die u op uw telefoon bezoekt. U kunt automatisch invullen gebruiken om uw wacht woorden op uw iOS-en Android-apparaten te synchroniseren en in te voeren. Nadat u de verificator-app hebt ingesteld als een automatische provider op uw telefoon, wordt u gevraagd uw wacht woorden op te slaan wanneer u deze invoert op een site of op een aanmeldings pagina voor een app. De wacht woorden worden opgeslagen als onderdeel van [uw persoonlijke Microsoft-account](https://account.microsoft.com/account) en zijn ook beschikbaar wanneer u zich aanmeldt bij micro soft Edge met uw persoonlijke Microsoft-account.

**V**: welke gegevens kunnen door de verificator worden door voeren?

**A**: de verificator kan gebruikers namen en wacht woorden invullen voor sites en apps die u op uw telefoon bezoekt.

**V**: hoe kan ik het automatisch invullen van wacht woorden inschakelen in verificator op mijn telefoon?

**A**: Volg deze stappen:

1. Open de verificator-app.
1. Op het tabblad **wacht woorden** in verificator selecteert u **Aanmelden met Microsoft** en meldt u zich aan met [uw Microsoft-account](https://account.microsoft.com/account). Deze functie ondersteunt momenteel alleen micro soft-accounts en biedt nog geen ondersteuning voor werk-of school accounts.

**V**: hoe kan ik de standaard provider voor automatisch invullen op mijn telefoon instellen als verificator?

**A**: Volg deze stappen:

1. Open de verificator-app.
1. Selecteer **Aanmelden met Microsoft** op het tabblad **wacht woorden** in de app en meld u aan met [uw Microsoft-account](https://account.microsoft.com/account).
1. Voer een van de volgende handelingen uit:

   - Selecteer in iOS onder **instellingen** de optie **automatisch invullen inschakelen** in de sectie instellingen voor automatisch door voeren voor informatie over het instellen van verificator als standaard provider voor automatisch door voeren.
   - Selecteer in Android onder **instellingen** de optie **instellen als automatische provider** in het gedeelte instellingen voor automatisch door voeren.

**V**: wat moet ik doen als de schakel optie voor **automatisch door voeren** niet beschikbaar is in instellingen?

**A**: als automatisch door voeren niet beschikbaar is in verificator, kan dit zijn omdat automatisch door voeren nog niet is toegestaan voor uw organisatie of account type. U kunt deze functie gebruiken op een apparaat waar uw werk-of school account niet is toegevoegd. Zie [automatisch door voeren voor IT-beheerders](#autofill-for-it-admins)voor meer informatie over het toestaan van automatisch door voeren voor uw organisatie.

**V**: hoe kan ik het synchroniseren van wacht woorden niet langer?

**A**: als u wilt stoppen met het synchroniseren van wacht woorden in de verificator-app, opent u **instellingen** van instellingen voor  >  **automatisch door voeren**  >  **synchronisatie-account**. Op het volgende scherm kunt u de optie **synchroniseren stoppen en alle gegevens van automatisch door voeren verwijderen**. Hiermee worden wacht woorden en andere gegevens over automatisch door voeren van het apparaat verwijderd. Het verwijderen van gegevens over automatisch door voeren heeft geen invloed op multi-factor Authentication.

**V**: hoe worden mijn wacht woorden beschermd door de verificator-app?

**A**: verificator-app biedt al een hoge mate van beveiliging voor multi-factor Authentication en account beheer, en dezelfde High Security-balk is ook uitgebreid voor het beheren van uw wacht woorden.

- **Sterke verificatie is vereist voor de verificator-app**: aanmelden bij verificator vereist een tweede factor. Dit betekent dat uw wacht woorden in de verificator-app worden beveiligd, zelfs als iemand uw Microsoft-account wacht woord heeft.
- **Gegevens over automatisch door voeren worden beveiligd met biometrie en de wachtwoord code**: voordat u een wacht woord voor een app of site kunt invoeren, is voor de verificator biometrisch of wachtwoord code voor het apparaat vereist. Dit helpt extra beveiliging toe te voegen, zelfs als iemand anders toegang heeft tot uw apparaat, het niet mogelijk is om uw wacht woord in te vullen of weer te geven, omdat de invoer van biometrische apparaten of PINCODEs niet kan worden opgegeven. Een gebruiker kan de pagina met wacht woorden ook alleen openen als ze biometrische of pincode hebben, zelfs als ze app-vergren deling in app-instellingen uitschakelen.
- Versleutelde **wacht woorden op het apparaat**: wacht woorden op het apparaat zijn versleuteld en sleutels voor versleuteling/ontsleuteling worden nooit opgeslagen en worden altijd gegenereerd wanneer dat nodig is. Wacht woorden worden alleen ontsleuteld wanneer een gebruiker dat wil, dat wil zeggen tijdens het door voeren of wanneer de gebruiker het wacht woord wil zien, beide biometrische of pincode vereisen.
- **Cloud-en netwerk beveiliging**: uw wacht woorden in de cloud worden alleen versleuteld en ontsleuteld wanneer ze uw apparaat bereiken. Wacht woorden worden gesynchroniseerd via een met SSL beveiligde HTTPS-verbinding, waarmee wordt voor komen dat een aanvaller gevoelige gegevens afluistert wanneer deze wordt gesynchroniseerd. We zorgen er ook voor dat de Sanity van gegevens die via het netwerk worden gesynchroniseerd, wordt gecontroleerd met behulp van cryptografische gehashte functies (met name op hash-gebaseerde bericht verificatie code).

## <a name="autofill-for-it-admins"></a>Automatisch door voeren voor IT-beheerders

**V**: zullen mijn werk nemers of studenten gebruikmaken van het automatisch invullen van wacht woorden in de verificator-app?

**A**: Ja, automatisch door voeren is nu geschikt voor de meeste zakelijke gebruikers, zelfs wanneer een werk-of school account wordt toegevoegd aan de verificator-app. U kunt een formulier invullen om automatisch door voeren voor uw organisatie te configureren (toestaan of weigeren) en [dit te verzenden naar het verificator-team](https://aka.ms/ConfigureAutofillInAuthenticator).

**V**: wordt het wacht woord voor het werk-of school account van mijn gebruikers automatisch gesynchroniseerd?

**A**: Nee. Met wacht woord door voeren wordt geen werk-of school account-wacht woord voor uw gebruikers gesynchroniseerd. Wanneer gebruikers een site of een app bezoeken, bieden verificator het wacht woord voor die site of app op te slaan, en wordt het wacht woord alleen opgeslagen wanneer de gebruiker kiest.
  
**V**: kan ik allowlist alleen bepaalde gebruikers van mijn organisatie voor automatisch door voeren?

**A**: Nee. Ondernemingen kunnen op dit moment alleen automatisch invullen van wacht woorden inschakelen voor alle of geen van hun werk nemers.

**V**: wat moet ik doen als mijn werk nemer of student meerdere werk-of school accounts heeft? Mijn werk nemer heeft bijvoorbeeld accounts van meerdere ondernemingen of scholen in hun Microsoft Authenticator.

**A**: alle ondernemingen of scholen die in de verificator-app worden toegevoegd, moeten worden aangegeven voor het automatisch door voeren van de app-eigenaar om deze in te kunnen gebruiken. De enige uitzonde ring op deze beperking is wanneer uw werk nemer of student het account work of school toevoegt aan micro soft Cloud multi-factor Authentication als een [extern of een account van derden](user-help-auth-app-add-non-ms-account.md).

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt met het verkrijgen van uw verificatie code voor uw persoonlijke Microsoft-account, raadpleegt u de sectie **problemen met verificatie code oplossen** van het artikel [Microsoft-account security info & verificatie codes](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Als u meer informatie wilt over verificatie in twee stappen, raadpleegt u [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md)

- Zie voor meer informatie over beveiligings informatie [overzicht van beveiligings gegevens (preview)](./security-info-setup-signin.md)

- Als uw vraag hier niet is beantwoord, willen we u graag van u horen. Ga naar het [Microsoft Authenticator app-forum](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) om uw vraag te plaatsen en hulp van de community te ontvangen of laat een opmerking op deze pagina staan.
