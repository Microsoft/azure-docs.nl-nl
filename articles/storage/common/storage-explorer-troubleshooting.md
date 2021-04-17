---
title: Azure Storage Explorer gids voor probleemoplossing | Microsoft Docs
description: Overzicht van de technieken voor het debuggen van Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568536"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Gids voor probleemoplossing voor Azure Storage Explorer

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage-gegevens kunt werken via Windows, macOS en Linux. De app kan verbinding maken met opslagaccounts die worden gehost op Azure, nationale clouds en Azure Stack.

Deze handleiding bevat een overzicht van oplossingen voor problemen die vaak voorkomen in Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problemen met Azure RBAC-machtigingen

Op rollen gebaseerd toegangsbeheer [van Azure Azure RBAC](../../role-based-access-control/overview.md) maakt uiterst gedetailleerd toegangsbeheer van Azure-resources mogelijk door sets machtigingen te combineren in _rollen_. Hier volgen enkele strategieën om Azure RBAC optimaal te laten werken in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hoe kan ik toegang tot mijn resources in Storage Explorer?

Als u problemen hebt met het openen van opslagbronnen via Azure RBAC, hebt u mogelijk niet de juiste rollen toegewezen. In de volgende secties worden de machtigingen beschreven die Storage Explorer momenteel nodig zijn voor toegang tot uw opslagbronnen. Neem contact op met uw Azure-accountbeheerder als u niet zeker weet of u de juiste rollen of machtigingen hebt.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Probleem met machtigingen voor 'Lezen: Opslagaccount(s) opsnachtigingen opsnachtiging

U moet machtigingen hebben om opslagaccounts weer te geven. Als u deze machtiging wilt krijgen, moet de rol Lezer _aan u zijn_ toegewezen.

#### <a name="list-storage-account-keys"></a>Opslagaccountsleutels op een lijst zetten

Storage Explorer kunt ook accountsleutels gebruiken om aanvragen te verifiëren. U kunt toegang krijgen tot accountsleutels via krachtigere rollen, zoals de _rol Inzender._

> [!NOTE]
> Toegangssleutels verlenen onbeperkte machtigingen aan iedereen die ze bevat. Daarom raden we u niet aan deze sleutels uit te geven aan accountgebruikers. Als u toegangssleutels moet intrekken, kunt u deze opnieuw [Azure Portal.](https://portal.azure.com/)

#### <a name="data-roles"></a>Gegevensrollen

Er moet ten minste één rol aan u zijn toegewezen die toegang verleent tot leesgegevens uit resources. Als u bijvoorbeeld blobs wilt in een lijst of downloaden, hebt u ten minste de rol _Lezer_ van opslagblobgegevens nodig.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Waarom heb ik een beheerlaagrol nodig om mijn resources in de Storage Explorer?

Azure Storage heeft twee toegangslagen: _beheer_ en _gegevens._ Abonnementen en opslagaccounts zijn toegankelijk via de beheerlaag. Containers, blobs en andere gegevensbronnen zijn toegankelijk via de gegevenslaag. Als u bijvoorbeeld een lijst met uw opslagaccounts van Azure wilt ontvangen, verzendt u een aanvraag naar het beheer-eindpunt. Als u een lijst met blobcontainers in een account wilt, verzendt u een aanvraag naar het juiste service-eindpunt.

Azure-rollen kunnen u machtigingen verlenen voor beheer of toegang tot gegevenslagen. De rol Lezer verleent bijvoorbeeld alleen-lezentoegang tot resources in de beheerlaag.

Strikt genomen biedt de rol Lezer geen machtigingen voor de gegevenslaag en is deze niet nodig voor toegang tot de gegevenslaag.

Storage Explorer kunt u eenvoudig toegang krijgen tot uw resources door de benodigde informatie te verzamelen om verbinding te maken met uw Azure-resources. Als u bijvoorbeeld uw blobcontainers wilt weergeven, Storage Explorer een aanvraag voor 'lijstcontainers' naar het eindpunt van de blobservice. Om dat eindpunt op te halen, Storage Explorer in de lijst met abonnementen en opslagaccounts waar u toegang toe hebt. Als u uw abonnementen en opslagaccounts wilt vinden, Storage Explorer ook toegang nodig tot de beheerlaag.

Als u geen rol hebt die machtigingen voor de beheerlaag verleent, kan Storage Explorer niet de informatie krijgen die nodig is om verbinding te maken met de gegevenslaag.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wat gebeurt er als ik de machtigingen voor de beheerlaag die ik nodig heb, niet kan krijgen van mijn beheerder?

Als u toegang wilt krijgen tot blobcontainers, ADLS Gen2 containers, directories of wachtrijen, kunt u deze resources koppelen met behulp van uw Azure-referenties.

1. Open het dialoogvenster Verbinding maken.
1. Selecteer het resourcetype dat u wilt verbinden.
1. Selecteer **Aanmelden met Azure Active Directory (Azure AD)**. Selecteer **Next**.
1. Selecteer het gebruikersaccount en de tenant die zijn gekoppeld aan de resource die u wilt koppelen. Selecteer **Next**.
1. Voer de URL naar de resource in en voer een unieke weergavenaam in voor de verbinding. Selecteer **Volgende** en vervolgens **Verbinding maken.**

Voor andere resourcetypen hebben we momenteel geen Azure RBAC-gerelateerde oplossing. Als tijdelijke oplossing kunt u een SAS-URL aanvragen en deze vervolgens aan uw resource koppelen door de volgende stappen uit te voeren:

1. Open het dialoogvenster Verbinding maken.
1. Selecteer het resourcetype dat u wilt verbinden.
1. Selecteer **Shared Access Signature (SAS)**. Selecteer **Next**.
1. Voer de SAS-URL in die u hebt ontvangen en voer een unieke weergavenaam in voor de verbinding. Selecteer **Volgende** en vervolgens **Verbinding maken.**
 
Zie Koppelen aan een afzonderlijke resource voor meer informatie over het koppelen [aan resources.](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)

### <a name="recommended-azure-built-in-roles"></a>Aanbevolen ingebouwde Azure-rollen

Er zijn verschillende ingebouwde Azure-rollen die de machtigingen kunnen bieden die nodig zijn voor het gebruik van Storage Explorer. Enkele van deze rollen zijn:
- [Eigenaar:](../../role-based-access-control/built-in-roles.md#owner)beheer alles, inclusief toegang tot resources.
- [Inzender:](../../role-based-access-control/built-in-roles.md#contributor)beheer alles, met uitzondering van toegang tot resources.
- [Lezer:](../../role-based-access-control/built-in-roles.md#reader)lees en vermeld resources.
- [Inzender voor opslagaccounts:](../../role-based-access-control/built-in-roles.md#storage-account-contributor)volledig beheer van opslagaccounts.
- [Eigenaar van opslagblobgegevens:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)volledige toegang tot Azure Storage blobcontainers en -gegevens.
- [Bijdrager voor opslagblobgegevens:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)lees-, schrijf- en Azure Storage containers en blobs.
- [Lezer voor opslagblobgegevens:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)lees- en Azure Storage containers en blobs.

> [!NOTE]
> De rollen Eigenaar, Inzender en Inzender voor opslagaccounts verlenen toegang tot accountsleutels.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fout: Zelf-ondertekend certificaat in certificaatketen (en vergelijkbare fouten)

Certificaatfouten treden doorgaans op in een van de volgende situaties:

- De app is verbonden via een _transparante proxy_. Dit betekent dat een server (zoals uw bedrijfsserver) HTTPS-verkeer onderschept, ontsleutelt en vervolgens versleutelt met behulp van een zelf-ondertekend certificaat.
- U gebruikt een toepassing die een zelf-ondertekend TLS/SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt. Voorbeelden van toepassingen die certificaten injecteren, zijn antivirussoftware en software voor netwerkverkeersinspectie.

Wanneer Storage Explorer een zelf-ondertekend of niet-vertrouwd certificaat ziet, weet het niet meer of het ontvangen HTTPS-bericht is gewijzigd. Als u een kopie van het zelf-ondertekende certificaat hebt, kunt u u instrueren Storage Explorer deze te vertrouwen door de volgende stappen uit te voeren:

1. Verkrijg een met Base 64 gecodeerde X.509-kopie (.cer) van het certificaat.
2. Ga naar  >  **Importcertificaten voor SSL-certificaten** bewerken en gebruik vervolgens de  >  **bestands** kiezen om het CER-bestand te zoeken, te selecteren en te openen.

Dit probleem kan ook optreden als er meerdere certificaten zijn (root en tussenliggend). Om deze fout op te lossen, moeten beide certificaten worden toegevoegd.

Als u niet zeker weet waar het certificaat vandaan komt, volgt u deze stappen om het te vinden:

1. Installeer OpenSSL.
    * [Windows:](https://slproweb.com/products/Win32OpenSSL.html)een van de lichte versies moet voldoende zijn.
    * Mac en Linux: moet worden opgenomen in uw besturingssysteem.
2. Voer OpenSSL uit.
    * Windows: open de installatiemap, selecteer **/bin/** en dubbelklik vervolgens op **openssl.exe**.
    * Mac en Linux: Voer `openssl` uit vanuit een terminal.
3. Voer `s_client -showcerts -connect microsoft.com:443` uit.
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten zelf-ondertekend zijn, noteert u overal waar het onderwerp en de vergever `("s:")` `("i:")` hetzelfde zijn.
5. Wanneer u zelf-ondertekende certificaten vindt, kopieert en plakt u voor elk certificaat alles van (en inclusief) in een `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` nieuw CER-bestand.
6. Open Storage Explorer en ga naar  >  **SSL-certificaten importeren**  >  **van certificaten bewerken.** Gebruik vervolgens de bestands picker om de CER-bestanden die u hebt gemaakt te zoeken, te selecteren en te openen.

Als u geen zelf-ondertekende certificaten kunt vinden door deze stappen te volgen, neemt u contact met ons op via het feedbackprogramma. U kunt de Storage Explorer ook openen vanaf de opdrachtregel met behulp van de `--ignore-certificate-errors` vlag . Bij het openen met deze vlag worden Storage Explorer certificaatfouten genegeerd.

## <a name="sign-in-issues"></a>Aanmeldingsproblemen

### <a name="understanding-sign-in"></a>Meer informatie over aanmelden

Zorg ervoor dat u de documentatie [Aanmelden bij Storage Explorer](./storage-explorer-sign-in.md) gelezen.

### <a name="frequently-having-to-reenter-credentials"></a>Vaak moeten referenties opnieuw worden gebruikt

Het opnieuw moeten ingeven van referenties is waarschijnlijk het resultaat van beleid voor voorwaardelijke toegang dat is ingesteld door uw AAD-beheerder. Wanneer Storage Explorer u vanuit het deelvenster Account referenties opnieuw wilt ingeven, ziet u de koppeling **Foutdetails...** . Klik hierop om te zien waarom Storage Explorer u vraagt referenties opnieuw in te stellen. Fouten in het beleid voor voorwaardelijke toegang waarvoor referenties opnieuw moeten worden gebruikt, kunnen er als de volgende uitzien:
- Het vernieuwings token is verlopen...
- U moet meervoudige verificatie gebruiken om toegang te krijgen tot...
- Vanwege een configuratiewijziging die is aangebracht door uw beheerder...

Als u de frequentie wilt verminderen van het opnieuw moeten ingeven van referenties vanwege fouten zoals hierboven, moet u met uw AAD-beheerder praten.

### <a name="conditional-access-policies"></a>Beleid voor voorwaardelijke toegang

Als u beleid voor voorwaardelijke toegang hebt dat moet worden voldaan  voor uw account, moet u ervoor zorgen dat u de waarde Standaardwebbrowser gebruikt voor de instelling Aanmelden **met.** Zie Wijzigen waar aanmelden gebeurt voor meer informatie [over deze instelling.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Kan token niet verkrijgen, tenant wordt uitgefilterd

Als er een foutbericht wordt weergegeven met de melding dat een token niet kan worden verkregen omdat een tenant is gefilterd, betekent dit dat u toegang probeert te krijgen tot een resource die zich in een tenant die u hebt uitgefilterd. Als u het filter voor de tenant wilt verwijderen, gaat u naar het **deelvenster Account** en controleert u of het selectievakje voor de tenant die is opgegeven in de fout is ingeschakeld. Raadpleeg Accounts [beheren voor](./storage-explorer-sign-in.md#managing-accounts) meer informatie over het filteren van tenants in Storage Explorer.

## <a name="authentication-library-failed-to-start-properly"></a>Verificatiebibliotheek is niet goed starten

Als u bij het opstarten een foutbericht ziet met de melding dat de verificatiebibliotheek van Storage Explorer niet goed is gestart, moet u ervoor zorgen dat uw installatieomgeving voldoet [aan alle vereisten.](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites) Niet voldoen aan vereisten is de meest waarschijnlijke oorzaak van dit foutbericht.

Als u denkt dat uw installatieomgeving aan alle vereisten voldoet, opent [u een probleem op GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new). Wanneer u het probleem opent, moet u het volgende opnemen:
- Uw besturingssysteem.
- Welke versie van Storage Explorer u wilt gebruiken.
- Als u de vereisten hebt gecontroleerd.
- [Verificatielogboeken](#authentication-logs) na een mislukte start van Storage Explorer. Uitgebreide verificatielogregistratie wordt automatisch ingeschakeld nadat dit type fout optreedt.

### <a name="blank-window-when-using-integrated-sign-in"></a>Leeg venster bij gebruik van geïntegreerde aanmelding

Als u ervoor hebt gekozen geïntegreerde aanmelding te gebruiken **en** een leeg aanmeldingsvenster ziet, moet u waarschijnlijk overschakelen naar een andere aanmeldingsmethode. Lege aanmeldingsdialoogvensters treden meestal op wanneer een Active Directory Federation Services-server (ADFS) Storage Explorer om een omleiding uit te voeren die niet wordt ondersteund door Electron.

Als u wilt wijzigen in een andere aanmeldingsmethode, wijzigt u de instelling Aanmelden **met** onder **Instellingen**  >    >  **Toepassings aanmelden.** Zie Wijzigen waar aanmelden gebeurt voor meer informatie over de verschillende soorten [aanmeldingsmethoden.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="reauthentication-loop-or-upn-change"></a>Lus voor opnieuw autorisatie of UPN-wijziging

Als u een reauthentication-lus hebt of de UPN van een van uw accounts hebt gewijzigd, probeert u deze stappen:

1. Open Storage Explorer
2. Ga naar Help > opnieuw instellen
3. Zorg ervoor dat ten minste Verificatie is ingeschakeld. U kunt andere items die u niet opnieuw wilt instellen, verwijderen.
4. Klik op de knop Opnieuw instellen
5. Start Storage Explorer opnieuw en probeer u opnieuw aan te melden.

Als u problemen blijft hebben na het opnieuw instellen, probeert u deze stappen:

1. Open Storage Explorer
2. Verwijder alle accounts en sluit Storage Explorer.
3. Verwijder de `.IdentityService` map van uw computer. In Windows bevindt de map zich op `C:\users\<username>\AppData\Local` . Voor Mac en Linux vindt u de map in de hoofdmap van uw gebruikersmap.
4. Als u Mac of Linux gebruikt, moet u ook de vermelding Microsoft.Developer.IdentityService verwijderen uit de sleutelstore van uw besturingssysteem. Op de Mac is de sleutelstore de *Gnome-sleutelhangertoepassing.* In Linux wordt de toepassing doorgaans _Sleutelhanger genoemd,_ maar de naam kan verschillen afhankelijk van uw distributie.
6. Start Storage Explorer opnieuw en probeer u opnieuw aan te melden.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: sleutelhangerfouten of geen aanmeldingsvenster

De macOS-sleutelhanger kan soms een status invoeren die problemen veroorzaakt voor de Storage Explorer verificatiebibliotheek. Volg deze stappen om de sleutelhanger uit deze status te halen:

1. Sluit Storage Explorer.
2. Open Sleutelhanger (druk op Opdracht + spatiebalk, typ **sleutelhanger** en druk op Enter).
3. Selecteer de sleutelhanger 'aanmelden'.
4. Selecteer het hangslotpictogram om de sleutelhanger te vergrendelen. (Het hangslot wordt vergrendeld weergegeven wanneer het proces is voltooid. Dit kan enkele seconden duren, afhankelijk van welke apps u hebt geopend).

    ![Hangslotpictogram](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Open Storage Explorer.
6. U wordt gevraagd om een bericht als 'Service Hub wil toegang krijgen tot de sleutelhanger'. Voer het wachtwoord van uw Mac-beheerdersaccount in en **selecteer Altijd toestaan** (of **Toestaan** als **Altijd toestaan** niet beschikbaar is).
7. Meld u aan.

### <a name="default-browser-doesnt-open"></a>Standaardbrowser wordt niet geopend

Als uw standaardbrowser niet wordt geopend wanneer u zich probeert aan te melden, probeert u alle volgende technieken:
- Start Storage Explorer
- Open uw browser handmatig voordat u zich begint aan te melden
- Probeer geïntegreerde **aanmelding te gebruiken.** Zie [Wijzigen waar het aanmelden gebeurt](./storage-explorer-sign-in.md#changing-where-sign-in-happens) voor instructies over hoe u dit doet.

### <a name="other-sign-in-issues"></a>Andere aanmeldingsproblemen

Als geen van de bovenstaande problemen van toepassing is op uw aanmeldingsprobleem of als het niet lukt om uw aanmeldingsprobleem op te lossen, opent u een [probleem op GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Ontbrekende abonnementen en defecte tenants

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aanmelden, kunt u de volgende methoden voor probleemoplossing proberen:

* Controleer of uw account toegang heeft tot de abonnementen die u verwacht. U kunt uw toegang controleren door u aan te melden bij de portal voor de Azure-omgeving die u wilt gebruiken.
* Zorg ervoor dat u zich hebt aangemeld via de juiste Azure-omgeving (Azure, Azure China 21Vianet, Azure Duitsland, Azure US Government of Aangepaste omgeving).
* Als u zich achter een proxyserver zit, moet u ervoor zorgen dat u de proxy Storage Explorer geconfigureerd.
* Probeer het account te verwijderen en opnieuw toe te voegen.
* Als er een koppeling 'Meer informatie' of 'Foutdetails' is, controleert u welke foutberichten worden gerapporteerd voor de tenants die niet werken. Als u niet zeker weet hoe u op de foutberichten moet reageren, kunt u een [probleem openen in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Kan een gekoppeld opslagaccount of gekoppelde resource niet verwijderen

Als u een gekoppeld account of een gekoppelde opslagresource niet kunt verwijderen via de gebruikersinterface, kunt u alle gekoppelde resources handmatig verwijderen door de volgende mappen te verwijderen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Sluit Storage Explorer voordat u deze mappen verwijdert.

> [!NOTE]
> Als u ooit SSL-certificaten hebt geïmporteerd, moet u een back-up maken van de inhoud van de `certs` map. Later kunt u de back-up gebruiken om uw SSL-certificaten opnieuw teimporteren.

## <a name="proxy-issues"></a>Proxyproblemen

Storage Explorer biedt ondersteuning voor het maken van verbinding Azure Storage resources via een proxyserver. Als u problemen hebt met het maken van verbinding met Azure via een proxy, vindt u hier enkele suggesties.

> [!NOTE]
> Storage Explorer ondersteunt alleen basisverificatie met proxyservers. Andere verificatiemethoden, zoals NTLM, worden niet ondersteund.

> [!NOTE]
> Storage Explorer biedt geen ondersteuning voor bestanden voor automatische configuratie van proxy's voor het configureren van proxyinstellingen.

### <a name="verify-storage-explorer-proxy-settings"></a>Proxy Storage Explorer instellingen controleren

De **configuratie-→ application → proxy bepaalt** van welke Storage Explorer de proxyconfiguratie wordt afkomstig.

Als u Omgevingsvariabelen gebruiken selecteert, moet u de omgevingsvariabelen of instellen (omgevingsvariabelen zijn `HTTPS_PROXY` case-gevoelig, dus zorg ervoor dat u de juiste `HTTP_PROXY` variabelen in stelt). Als deze variabelen niet zijn gedefinieerd of ongeldig zijn, Storage Explorer geen proxy gebruiken. Start Storage Explorer opnieuw op na het wijzigen van omgevingsvariabelen.

Als u App-proxyinstellingen gebruiken selecteert, moet u ervoor zorgen dat de proxy-instellingen in de app juist zijn.

### <a name="steps-for-diagnosing-issues"></a>Stappen voor het diagnosticeren van problemen

Als u nog steeds problemen ondervindt, kunt u deze methoden voor probleemoplossing proberen:

1. Als u verbinding kunt maken met internet zonder uw proxy te gebruiken, controleert u of Storage Explorer proxyinstellingen zijn ingeschakeld. Als Storage Explorer verbinding hebt, is er mogelijk een probleem met uw proxyserver. Werk samen met uw beheerder om de problemen te identificeren.
2. Controleer of andere toepassingen die gebruikmaken van de proxyserver werken zoals verwacht.
3. Controleer of u verbinding kunt maken met de portal voor de Azure-omgeving die u wilt gebruiken.
4. Controleer of u antwoorden van uw service-eindpunten kunt ontvangen. Voer een van uw eindpunt-URL's in uw browser in. Als u verbinding kunt maken, ontvangt u een `InvalidQueryParameterValue` of vergelijkbaar XML-antwoord.
5. Controleer of iemand anders die Storage Explorer met dezelfde proxyserver verbinding kan maken. Als dat mogelijk is, moet u mogelijk contact opnemen met de beheerder van uw proxyserver.

### <a name="tools-for-diagnosing-issues"></a>Hulpprogramma's voor het diagnosticeren van problemen

Een netwerkhulpprogramma, zoals Fiddler, kan u helpen bij het vaststellen van problemen.

1. Configureer uw netwerkhulpprogramma als een proxyserver die wordt uitgevoerd op de lokale host. Als u achter een werkelijke proxy moet blijven werken, moet u mogelijk uw netwerkhulpprogramma configureren om verbinding te maken via de proxy.
2. Controleer het poortnummer dat wordt gebruikt door uw netwerkhulpprogramma.
3. Configureer Storage Explorer proxy-instellingen voor het gebruik van de lokale host en het poortnummer van het netwerkhulpprogramma (zoals 'localhost:8888').
 
Wanneer deze juist zijn ingesteld, worden met uw netwerkhulpprogramma netwerkaanvragen van Storage Explorer naar beheer- en service-eindpunten.
 
Als uw netwerkhulpprogramma geen logboekregistratie van Storage Explorer lijkt te zijn, test u uw hulpprogramma met een andere toepassing. Voer bijvoorbeeld de eindpunt-URL voor een van uw opslagbronnen (zoals ) in een webbrowser in en u ontvangt een antwoord dat lijkt `https://contoso.blob.core.windows.net/` op:

  ![Codevoorbeeld](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Het antwoord geeft aan dat de resource bestaat, ook al hebt u er geen toegang toe.

Als uw netwerkhulpprogramma alleen verkeer van andere toepassingen toont, moet u mogelijk de proxy-instellingen in de Storage Explorer. Anders moest u de instellingen van uw hulpprogramma aanpassen.

### <a name="contact-proxy-server-admin"></a>Neem contact op met de beheerder van de proxyserver

Als uw proxyinstellingen juist zijn, moet u mogelijk contact opnemen met de beheerder van de proxyserver voor het volgende:

* Zorg ervoor dat uw proxy geen verkeer naar Azure-beheer- of resource-eindpunten blokkeert.
* Controleer het verificatieprotocol dat wordt gebruikt door uw proxyserver. Storage Explorer ondersteunt alleen basisverificatieprotocollen. Storage Explorer biedt geen ondersteuning voor NTLM-proxies.

## <a name="unable-to-retrieve-children-error-message"></a>Foutbericht ' Kan geen kinderen ophalen'

Als u via een proxy bent verbonden met Azure, controleert u of uw proxy-instellingen juist zijn.

Als de eigenaar van een abonnement of account u toegang heeft verleend tot een resource, controleert u of u lees- of lijstmachtigingen hebt voor die resource.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Verbindingsreeks heeft geen volledige configuratie-instellingen

Als u dit foutbericht ontvangt, is het mogelijk dat u niet de benodigde machtigingen hebt om de sleutels voor uw opslagaccount te verkrijgen. Als u wilt controleren of dit het geval is, gaat u naar de portal en zoekt u uw opslagaccount. U kunt dit doen door met de rechtermuisknop op het knooppunt voor uw opslagaccount te klikken en **Openen in portal te selecteren.** Ga vervolgens naar de blade **Toegangssleutels.** Als u geen machtigingen hebt om sleutels weer te geven, ziet u het bericht U hebt geen toegang. U kunt dit probleem oplossen door de accountsleutel te verkrijgen van iemand anders en deze te koppelen via de naam en sleutel, of u kunt iemand om een SAS vragen bij het opslagaccount en deze gebruiken om het opslagaccount te koppelen.

Als u de accountsleutels wel ziet, kunt u een probleem indienen in GitHub, zodat we u kunnen helpen het probleem op te lossen.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Er is een fout opgetreden tijdens het toevoegen van een nieuwe verbinding: TypeError: Kan de eigenschap 'versie' van niet-gedefinieerd niet lezen

Als u dit foutbericht ontvangt wanneer u een aangepaste verbinding probeert toe te voegen, zijn de verbindingsgegevens die zijn opgeslagen in het lokale referentiebeheer mogelijk beschadigd. U kunt dit probleem oplossen door uw beschadigde lokale verbindingen te verwijderen en ze vervolgens opnieuw toe te voegen:

1. Begin Storage Explorer. Ga in het menu naar **Help**  >  **Toggle Ontwikkelhulpprogramma's**.
2. Ga in het geopende venster **op het** tabblad Toepassing naar **Lokale** opslag (links) > **file://.**
3. Afhankelijk van het type verbinding waar u een probleem mee hebt, zoek dan naar de sleutel en kopieer de waarde ervan naar een teksteditor. De waarde is een matrix van uw aangepaste verbindingsnamen, zoals:
    * Opslagaccounts
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobcontainers
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Bestandsshares
        * `StorageExplorer_CustomConnections_Files_v1`
    * Wachtrijen
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabellen
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Nadat u de huidige verbindingsnamen hebt op slaan, stelt u de waarde in Ontwikkelhulpprogramma's in op `[]` .

Als u de verbindingen die niet beschadigd zijn wilt behouden, kunt u de volgende stappen gebruiken om de beschadigde verbindingen te vinden. Als u het niet erg vindt om alle bestaande verbindingen te verliezen, kunt u deze stappen overslaan en de platformspecifieke instructies volgen om uw verbindingsgegevens te verwijderen.

1. Voeg vanuit een teksteditor elke verbindingsnaam opnieuw toe aan Ontwikkelhulpprogramma's controleer vervolgens of de verbinding nog steeds werkt.
2. Als een verbinding correct werkt, is deze niet beschadigd en kunt u deze daar veilig laten staan. Als een verbinding niet werkt, verwijdert u de waarde van Ontwikkelhulpprogramma's en neemt u deze op zodat u deze later weer kunt toevoegen.
3. Herhaal dit totdat u al uw verbindingen hebt onderzocht.

Nadat u al uw verbindingen hebt doorlopen, moet u voor alle verbindingsnamen die niet opnieuw worden toegevoegd, de beschadigde gegevens verwijderen (indien aanwezig) en deze opnieuw toevoegen met behulp van de standaardstappen in Storage Explorer:

### <a name="windows"></a>[Windows](#tab/Windows)

1. Zoek in **het** menu Start naar **Aanmeldingsgegevensbeheer** en open het.
2. Ga naar **Windows-referenties.**
3. Zoek **onder Algemene referenties** naar vermeldingen die de sleutel hebben `<connection_type_key>/<corrupted_connection_name>` (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

### <a name="macos"></a>[MacOS](#tab/macOS)

1. Open Spotlight (opdracht+ spatiebalk) en zoek naar **Sleutelhangertoegang.**
2. Zoek naar vermeldingen die de sleutel `<connection_type_key>/<corrupted_connection_name>` hebben (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

### <a name="linux"></a>[Linux](#tab/Linux)

Lokaal referentiebeheer is afhankelijk van de Linux-distributie. Als uw Linux-distributie geen ingebouwd GUI-hulpprogramma biedt voor lokaal referentiebeheer, kunt u een hulpprogramma van derden installeren om uw lokale referenties te beheren. U kunt bijvoorbeeld [Sea horse](https://wiki.gnome.org/Apps/Seahorse/)gebruiken, een opensource-GUI-hulpprogramma voor het beheren van lokale Linux-referenties.

1. Open uw lokale hulpprogramma voor referentiebeheer en zoek uw opgeslagen referenties.
2. Zoek naar vermeldingen die de sleutel `<connection_type_key>/<corrupted_connection_name>` hebben (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.
---

Als u deze fout nog steeds ondervindt nadat u deze stappen hebt uitgevoerd, of als u wilt delen wat u vermoedt dat de verbindingen zijn [beschadigd,](https://github.com/microsoft/AzureStorageExplorer/issues) opent u een probleem op onze GitHub-pagina.

## <a name="issues-with-sas-url"></a>Problemen met SAS-URL

Als u verbinding maakt met een service via een SAS-URL en een fout ondervindt:

* Controleer of de URL de benodigde machtigingen biedt om resources te lezen of weer te geven.
* Controleer of de URL niet is verlopen.
* Als de SAS-URL is gebaseerd op een toegangsbeleid, controleert u of het toegangsbeleid niet is ingetrokken.

Als u per ongeluk een ongeldige SAS-URL hebt gekoppeld en u deze nu niet kunt loskoppelen, volgt u deze stappen:

1. Wanneer u een Storage Explorer, drukt u op F12 om het venster Ontwikkelhulpprogramma's openen.
2. Selecteer op **het** tabblad Toepassing **de optie Lokale**  >  **file://** in de structuur aan de linkerkant.
3. Zoek de sleutel die is gekoppeld aan het servicetype van de problematische SAS-URI. Als de slechte SAS-URI bijvoorbeeld voor een blobcontainer is, zoek dan naar de sleutel met de naam `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. De waarde van de sleutel moet een JSON-matrix zijn. Zoek het object dat is gekoppeld aan de slechte URI en verwijder het.
5. Druk op Ctrl+R om de Storage Explorer.

## <a name="linux-dependencies"></a>Linux-afhankelijkheden

### <a name="snap"></a>Module

Storage Explorer 1.10.0 en hoger is beschikbaar als een module van de Module Store. De Storage Explorer-module installeert alle afhankelijkheden automatisch en wordt bijgewerkt wanneer er een nieuwe versie van de module beschikbaar is. Het installeren van Storage Explorer module is de aanbevolen installatiemethode.

Storage Explorer vereist het gebruik van wachtwoordbeheer. Mogelijk moet u handmatig verbinding maken voordat Storage Explorer goed werkt. U kunt verbinding Storage Explorer wachtwoordbeheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz-bestand

U kunt de toepassing ook downloaden als een .tar.gz-bestand, maar u moet afhankelijkheden handmatig installeren.

Storage Explorer zoals opgegeven in de .tar.gz-download wordt alleen ondersteund voor de volgende versies van Ubuntu. Storage Explorer werken mogelijk op andere Linux-distributies, maar ze worden niet officieel ondersteund.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer vereist dat .NET Core op uw systeem is geïnstalleerd. We raden .NET Core 2.1 aan, maar Storage Explorer werkt ook met 2.2.

> [!NOTE]
> Storage Explorer versie 1.7.0 en eerder is .NET Core 2.0 vereist. Als u een nieuwere versie van .NET Core hebt geïnstalleerd, moet u [een patch voor Storage Explorer.](#patching-storage-explorer-for-newer-versions-of-net-core) Als u met Storage Explorer 1.8.0 of hoger, hebt u ten minste .NET Core 2.1 nodig.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Download het Storage Explorer .tar.gz.
2. Installeer [de .NET Core Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Download het Storage Explorer .tar.gz.
2. Installeer [de .NET Core Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Download het Storage Explorer .tar.gz.
2. Installeer [de .NET Core Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Veel bibliotheken die nodig zijn Storage Explorer zijn vooraf geïnstalleerd met de standaardinstallaties van Canonical van Ubuntu. Sommige van deze bibliotheken ontbreken mogelijk in aangepaste omgevingen. Als u problemen hebt met het starten Storage Explorer, raden we u aan om ervoor te zorgen dat de volgende pakketten op uw systeem zijn geïnstalleerd:

- iproute2
- libalibalib2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer voor nieuwere versies van .NET Core

Voor Storage Explorer versie 1.7.0 of eerder moet u mogelijk een patch gebruiken voor de versie van .NET Core die wordt gebruikt door Storage Explorer:

1. Download versie 1.5.43 van StreamJsonRpc [van NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Zoek naar de koppeling Pakket downloaden aan de rechterkant van de pagina.
2. Nadat u het pakket hebt gedownload, wijzigt u de bestandsextensie van `.nupkg` in `.zip` .
3. Pak het pakket uit.
4. Open de map `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieer `StreamJsonRpc.dll` naar de volgende locaties in de Storage Explorer map:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Open In Explorer vanuit de Azure Portal werkt niet

Als de **knop Openen in** Verkenner op Azure Portal werkt, moet u ervoor zorgen dat u een compatibele browser gebruikt. De volgende browsers zijn getest op compatibiliteit:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Logboeken verzamelen

Wanneer u een probleem aan GitHub rapporteert, wordt u mogelijk gevraagd bepaalde logboeken te verzamelen om u te helpen bij het vaststellen van uw probleem.

### <a name="storage-explorer-logs"></a>Storage Explorer logboeken

Vanaf versie 1.16.0 registreert Storage Explorer verschillende zaken in de eigen toepassingslogboeken. U kunt deze logboeken eenvoudig openen door te klikken op Help > Map logboeken openen. Standaard Storage Explorer logboeken op een laag niveau van verbossing. Als u het verbosity-niveau wilt wijzigen, voegt u een omgevingsvariabele toe met de naam `STG_EX_LOG_LEVEL` , en een van de volgende waarden:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (standaardniveau)
- `verbose`
- `debug`

Logboeken worden gesplitst in mappen voor elke sessie van Storage Explorer die u hebt uitgevoerd. Voor de logboekbestanden die u wilt delen, is het raadzaam om ze in een ZIP-archief te plaatsen, met bestanden van verschillende sessies in verschillende mappen.

### <a name="authentication-logs"></a>Verificatielogboeken

Voor problemen met betrekking tot aanmelden of Storage Explorer van de verificatiebibliotheek van de gebruiker, moet u waarschijnlijk verificatielogboeken verzamelen. Verificatielogboeken worden opgeslagen op:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS en Linux `~/.ServiceHub/logs`

Over het algemeen kunt u deze stappen volgen om de logboeken te verzamelen:

1. Ga naar Instellingen > Aanmelden > uitgebreide verificatielogregistratie in te schakelen. Als Storage Explorer niet kan worden starten vanwege een probleem met de verificatiebibliotheek, wordt dit voor u gedaan.
2. Sluit Storage Explorer.
1. Optioneel/aanbevolen: bestaande logboeken uit de map `logs` verwijderen. Als u dit doet, vermindert u de hoeveelheid informatie die u ons moet sturen.
4. Open Storage Explorer en reproduceer uw probleem
5. Sluit Storage Explorer
6. Zip de inhoud van de `log` map.

### <a name="azcopy-logs"></a>AzCopy-logboeken

Als u problemen hebt met het overdragen van gegevens, moet u mogelijk de AzCopy-logboeken op halen. AzCopy-logboeken kunnen eenvoudig worden gevonden via twee verschillende methoden:
- Klik voor mislukte overdrachten nog steeds in het activiteitenlogboek op Ga naar AzCopy-logboekbestand
- Voor overdrachten die in het verleden zijn mislukt, gaat u naar de map AzCopy-logboeken. Deze map vindt u op:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS en Linux '~/.azcopy

### <a name="network-logs"></a>Netwerklogboeken

Voor sommige problemen moet u logboeken verstrekken van de netwerkoproepen van Storage Explorer. In Windows kunt u dit doen met fiddler.

> [!NOTE]
> Fiddler-traceringen kunnen wachtwoorden bevatten die u tijdens het verzamelen van de traceer hebt ingevoerd/verzonden in uw browser. Lees de instructies voor het ops manier waarop een Fiddler-traceer moet worden opsinstructies. Upload Fiddler-traceringen niet naar GitHub. U wordt verteld waar u uw Fiddler-trace veilig kunt verzenden.

Deel 1: Fiddler installeren en configureren

1. Fiddler installeren
2. Fiddler starten
3. Ga naar Hulpprogramma> opties
4. Klik op het tabblad HTTPS
5. Zorg ervoor dat Capture CONNECTs and Decrypt HTTPS traffic (CONNET's vastleggen en HTTPS-verkeer ontsleutelen) is ingeschakeld
6. Klik op de knop Acties
7. Kies Basiscertificaat vertrouwen en vervolgens Ja in het volgende dialoogvenster
8. Klik opnieuw op de knop Acties
9. Kies Basiscertificaat exporteren naar desktop
10. Ga naar uw bureaublad
11. Het bestand FiddlerRoot.cer zoeken
12. Dubbelklik om te openen
13. Ga naar het tabblad Details
14. Klik op Kopiëren naar bestand...
15. Kies in de wizard Exporteren de volgende opties
    - Base-64 gecodeerde X.509
    - Voor de bestandsnaam bladert u... naar C:\Users \<your user dir> \AppData\Roaming\StorageExplorer\certs en vervolgens kunt u het opslaan als een bestandsnaam
16. Het certificaatvenster sluiten
17. Start Storage Explorer
18. Ga naar Proxy > bewerken
19. Kies in het dialoogvenster 'App-proxyinstellingen gebruiken' en stel de URL in op http://localhost en de poort op 8888
20. Klik op OK
21. Start Storage Explorer
22. U ziet nu netwerkoproepen van een `storageexplorer:` proces in Fiddler

Deel 2: Het probleem reproduceren
1. Alle andere apps dan Fiddler sluiten
2. Wis het Fiddler-logboek (X-pictogram linksboven, bij het menu Weergave)
3. Optioneel/aanbevolen: laat Fiddler enkele minuten instellen. Als u netwerkoproepen ziet verschijnen, klikt u er met de rechtermuisknop op en kiest u Nu filteren > <process name> 'Verbergen'
4. Start Storage Explorer
5. Reproduceer het probleem
6. Klik op Bestand > Opslaan > alle sessies... , sla ergens op dat u niet vergeet
7. Sluit Fiddler en Storage Explorer

Deel 3: De Fiddler-traceer opsparen
1. Dubbelklik op de traceer fiddler (.saz file)
2. Pers `ctrl`+`f`
3. Zorg ervoor dat in het dialoogvenster dat wordt weergegeven, de volgende opties zijn ingesteld: Zoeken = aanvragen en antwoorden, Onderzoeken = kopteksten en hoofdteksten
4. Zoek naar wachtwoorden die u hebt gebruikt tijdens het verzamelen van de fiddler-traceer, alle gemarkeerde vermeldingen, klik met de rechtermuisknop en kies Verwijderen > Geselecteerde sessies
5. Als u tijdens het verzamelen van de traceer zeker wachtwoorden hebt ingevoerd in uw browser, maar u geen vermeldingen vindt wanneer u ctrl+f gebruikt en u uw wachtwoorden/de wachtwoorden die u hebt gebruikt voor andere accounts niet wilt wijzigen, kunt u het verzenden van het .saz-bestand overslaan. Het is beter om veilig te zijn dan sorry. :)
6. Sla de trace opnieuw op met een nieuwe naam
7. Optioneel: de oorspronkelijke traceer verwijderen

## <a name="next-steps"></a>Volgende stappen

Als geen van deze oplossingen voor u werkt, kunt u het volgende doen:
- Een ondersteuningsticket maken
- [Open een probleem op GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). U kunt dit ook doen door in de **linkerbenedenhoek de** knop Probleem melden bij GitHub te selecteren.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)