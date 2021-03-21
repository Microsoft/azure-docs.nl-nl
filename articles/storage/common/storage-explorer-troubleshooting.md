---
title: Gids voor het oplossen van problemen met Azure Storage Explorer | Microsoft Docs
description: Overzicht van technieken voor fout opsporing voor Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 15df9b38abe35fe3eefad2fa160e1c1f16fe7aa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439456"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Gids voor probleemoplossing voor Azure Storage Explorer

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage-gegevens kunt werken via Windows, macOS en Linux. De app kan verbinding maken met opslagaccounts die worden gehost op Azure, nationale clouds en Azure Stack.

In deze hand leiding vindt u een overzicht van oplossingen voor problemen die vaak worden weer gegeven in Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problemen met Azure RBAC-machtigingen

Azure [RBAC](../../role-based-access-control/overview.md) -toegangs beheer op basis van rollen maakt zeer nauw keuriger toegang tot Azure-resources door sets machtigingen te combi neren in _rollen_. Hier volgen enkele strategieën om optimaal gebruik te maken van Azure RBAC in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Heb ik toegang tot mijn resources in Storage Explorer Hoe kan ik?

Als u problemen ondervindt bij het openen van opslag resources via Azure RBAC, hebt u mogelijk niet de juiste rollen toegewezen. In de volgende secties worden de machtigingen beschreven Storage Explorer momenteel vereist voor toegang tot uw opslag resources. Neem contact op met de beheerder van uw Azure-account als u niet zeker weet of u de juiste rollen of machtigingen hebt.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Probleem met de machtiging ' lezen: lijst/ophalen van opslag account (s) '

U moet gemachtigd zijn om opslag accounts weer te geven. Als u deze machtiging wilt ontvangen, moet u de rol van _lezer_ toewijzen.

#### <a name="list-storage-account-keys"></a>Sleutels van opslag account weer geven

Storage Explorer kunt ook account sleutels gebruiken om aanvragen te verifiëren. U kunt toegang krijgen tot account sleutels via krachtigere rollen, zoals de rol _Inzender_ .

> [!NOTE]
> Met toegangs sleutels worden onbeperkte machtigingen verleend aan iedereen die deze bevat. Daarom raden we u aan deze sleutels niet te leveren aan gebruikers. Als u toegangs sleutels wilt intrekken, kunt u ze opnieuw genereren op basis van de [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Gegevens rollen

U moet ten minste één rol toewijzen die toegang verleent tot het lezen van gegevens uit resources. Als u bijvoorbeeld blobs wilt weer geven of downloaden, hebt u Mini maal de rol _Storage BLOB data Reader_ nodig.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Waarom heb ik een rol van beheer laag nodig om mijn resources in Storage Explorer te zien?

Azure Storage heeft twee toegangs niveaus: _beheer_ en _gegevens_. Abonnementen en opslag accounts zijn toegankelijk via de Management-laag. Containers, blobs en andere gegevens bronnen worden geopend via de gegevenslaag. Als u bijvoorbeeld een lijst wilt ophalen van uw opslag accounts van Azure, verzendt u een aanvraag naar het beheer eindpunt. Als u een lijst met Blob-containers in een account wilt, verzendt u een aanvraag naar het juiste service-eind punt.

Azure-rollen kunnen u machtigingen verlenen voor toegang tot beheer of gegevenslaag. De rol van lezer geeft bijvoorbeeld alleen-lezen toegang tot management Layer-resources.

De rol van lezer biedt geen gegevenslaag machtigingen en is niet nodig om toegang te krijgen tot de gegevenslaag.

Met Storage Explorer kunt u eenvoudig toegang krijgen tot uw resources door de gegevens te verzamelen die nodig zijn om verbinding te maken met uw Azure-resources. Als u bijvoorbeeld uw BLOB-containers wilt weer geven, verzendt Storage Explorer een aanvraag ' lijst containers ' naar het eind punt van de BLOB-service. Storage Explorer zoekt in de lijst met abonnementen en opslag accounts waartoe u toegang hebt om dit eind punt op te halen. Als u uw abonnementen en opslag accounts wilt vinden, moet Storage Explorer ook toegang hebben tot de Management laag.

Als u geen rol hebt die machtigingen voor een beheer laag verleent, kan Storage Explorer de informatie die nodig is om verbinding te maken met de gegevenslaag niet ophalen.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wat moet ik doen als ik de beheer laag machtigingen niet kan ophalen die ik nodig heb van mijn beheerder?

Als u toegang wilt krijgen tot BLOB-containers, ADLS Gen2 containers of mappen of wacht rijen, kunt u deze resources koppelen met uw Azure-referenties.

1. Open het dialoog venster verbinding maken.
1. Selecteer het resource type waarmee u verbinding wilt maken.
1. Selecteer **Aanmelden met Azure Active Directory (Azure AD)**. Selecteer **Next**.
1. Selecteer het gebruikers account en de Tenant die zijn gekoppeld aan de resource waaraan u wilt koppelen. Selecteer **Next**.
1. Voer de URL naar de resource in en voer een unieke weergave naam voor de verbinding in. Selecteer **volgende** en vervolgens **verbinding maken**.

Voor andere resource typen hebben we momenteel geen oplossing met betrekking tot Azure RBAC. Als tijdelijke oplossing kunt u een SAS-URL aanvragen en vervolgens aan uw resource koppelen door de volgende stappen uit te voeren:

1. Open het dialoog venster verbinding maken.
1. Selecteer het resource type waarmee u verbinding wilt maken.
1. Selecteer **Shared Access Signature (SAS)**. Selecteer **Next**.
1. Voer de SAS-URL in die u hebt ontvangen en voer een unieke weergave naam voor de verbinding in. Selecteer **volgende** en vervolgens **verbinding maken**.
 
Zie [koppelen aan een afzonderlijke resource](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)voor meer informatie over het koppelen aan resources.

### <a name="recommended-azure-built-in-roles"></a>Aanbevolen ingebouwde rollen van Azure

Er zijn verschillende ingebouwde rollen van Azure die de machtigingen kunnen bieden die nodig zijn om Storage Explorer te gebruiken. Enkele van deze rollen zijn:
- [Eigenaar](../../role-based-access-control/built-in-roles.md#owner): alles beheren, inclusief toegang tot resources.
- [Inzender](../../role-based-access-control/built-in-roles.md#contributor): alles beheren, met uitzonde ring van toegang tot resources.
- [Lezer](../../role-based-access-control/built-in-roles.md#reader): bronnen lezen en weer geven.
- [Inzender voor het opslag account](../../role-based-access-control/built-in-roles.md#storage-account-contributor): volledig beheer van opslag accounts.
- [Eigenaar](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)van de opslag-BLOB-gegevens: volledige toegang tot Azure Storage BLOB-containers en-gegevens.
- [Inzender voor gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)van de opslag-blob: Lees-, schrijf-en verwijder Azure Storage containers en blobs.
- [Gegevens lezer](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)van de opslag-Blob: Azure Storage containers en blobs voor lezen en weer geven.

> [!NOTE]
> De Inzender rollen eigenaar, Inzender en opslag account verlenen toegang tot de sleutel tot het account.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fout: zelfondertekend certificaat in certificaat keten (en vergelijk bare fouten)

Certificaat fouten treden doorgaans op in een van de volgende situaties:

- De app is verbonden via een _transparante proxy_. Dit betekent dat een server (zoals uw bedrijfs server) het HTTPS-verkeer onderschept, ontsleutelt en vervolgens versleutelt met behulp van een zelfondertekend certificaat.
- U voert een toepassing uit die een zelfondertekend TLS/SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt. Voor beelden van toepassingen die certificaten injecteren, zijn onder andere software voor de inspectie van virussen en netwerk verkeer.

Wanneer Storage Explorer een zelfondertekend of niet-vertrouwd certificaat ziet, weet het niet meer of het ontvangen HTTPS-bericht is gewijzigd. Als u een exemplaar van het zelfondertekende certificaat hebt, kunt u Storage Explorer om dit te vertrouwen door de volgende stappen uit te voeren:

1. Een met base 64 gecodeerde X. 509 (. CER)-kopie van het certificaat verkrijgen.
2. Ga naar **bewerken**  >  **SSL-certificaten**  >  **certificaten importeren**, en gebruik vervolgens de bestands kiezer om het. cer-bestand te zoeken, te selecteren en te openen.

Dit probleem kan ook optreden als er meerdere certificaten (root en tussenliggend) zijn. Om deze fout op te lossen, moeten beide certificaten worden toegevoegd.

Als u niet zeker weet waar het certificaat vandaan komt, volgt u deze stappen om het te vinden:

1. Installeer OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): een van de lichte versies moet voldoende zijn.
    * Mac en Linux: moeten zijn opgenomen in uw besturings systeem.
2. Voer OpenSSL uit.
    * Windows: Open de installatiemap, selecteer **/bin/** en dubbel klik vervolgens op **openssl.exe**.
    * Mac en Linux: uitvoeren `openssl` vanaf een Terminal.
3. Voer `s_client -showcerts -connect microsoft.com:443` uit.
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten u zelf hebt ondertekend, noteert u de locatie van het onderwerp `("s:")` en de uitgever `("i:")` .
5. Wanneer u zelfondertekende certificaten vindt, kopieert en plakt u alles uit (en inclusief) `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` in een nieuw CER-bestand.
6. Open Storage Explorer en ga naar   >    >  **import certificaten** voor SSL-certificaten bewerken. Gebruik vervolgens de bestands kiezer om de CER-bestanden die u hebt gemaakt, te zoeken, te selecteren en te openen.

Als u geen zelfondertekende certificaten kunt vinden door deze stappen te volgen, neemt u contact met ons op via het feedback programma. U kunt Storage Explorer ook openen vanaf de opdracht regel met behulp van de `--ignore-certificate-errors` vlag. Wanneer deze vlag wordt geopend, worden certificaat fouten Storage Explorer genegeerd.

## <a name="sign-in-issues"></a>Aanmeldingsproblemen

### <a name="blank-sign-in-dialog-box"></a>Het dialoog venster lege aanmelding

De dialoog vensters met een leeg aanmeld probleem worden meestal beschreven wanneer Active Directory Federation Services (AD FS) Storage Explorer om een omleiding uit te voeren, wat niet wordt ondersteund door elektron. U kunt dit probleem omzeilen door de apparaatcode stroom te gebruiken om u aan te melden. Voer hiervoor de volgende stappen uit:

1. Open op de werk balk links verticaal de knop **instellingen**. Ga in het deel venster instellingen naar **toepassing**  >  **Aanmelden**. Schakel **Aanmelden met apparaatcode stroom in**.
2. Open het dialoog venster **verbinding maken** (via het pictogram met de plug-in de verticale balk aan de linkerkant of selecteer **account toevoegen** in het deel venster account).
3. Kies de omgeving waarin u zich wilt aanmelden.
4. Selecteer **Aanmelden.**
5. Volg de instructies op het volgende scherm.

Als u zich niet kunt aanmelden bij het account dat u wilt gebruiken omdat uw standaard browser al is aangemeld bij een ander account, voert u een van de volgende handelingen uit:

- Kopieer de koppeling en code hand matig naar een persoonlijke sessie van uw browser.
- Kopieer de koppeling en code hand matig naar een andere browser.

### <a name="reauthentication-loop-or-upn-change"></a>Herauthenticatie lus of UPN-wijziging

Voer de volgende stappen uit als u een lus voor opnieuw verifiëren hebt of als u de UPN van een van uw accounts hebt gewijzigd:

1. Verwijder alle accounts en sluit Storage Explorer.
2. Verwijder de. De map IdentityService van uw computer. In Windows bevindt de map zich op `C:\users\<username>\AppData\Local` . Voor Mac en Linux vindt u de map in de hoofdmap van de gebruikers lijst.
3. Als u Mac of Linux uitvoert, moet u ook de vermelding micro soft. developer. IdentityService verwijderen uit de opslag van uw besturings systeem. Op de Mac is het sleutel archief de *gnome-keten* toepassing. In Linux wordt de toepassing meestal _sleutel hanger_ genoemd, maar de naam kan variëren, afhankelijk van uw distributie.

### <a name="conditional-access"></a>Voorwaardelijke toegang

Vanwege een beperking in de Azure AD-bibliotheek die wordt gebruikt door Storage Explorer, wordt voorwaardelijke toegang niet ondersteund wanneer Storage Explorer wordt gebruikt in Windows 10, Linux of macOS.

## <a name="mac-keychain-errors"></a>Mac-sleutel keten fouten

De macOS-sleutel hanger kan soms een status invoeren die problemen veroorzaakt voor de Storage Explorer-verificatie bibliotheek. Voer de volgende stappen uit om de sleutel hanger uit deze staat te halen:

1. Storage Explorer sluiten.
2. Open sleutel hanger (druk op Command + spatie balk, typ **sleutel keten** en druk op ENTER).
3. Selecteer de sleutel hanger aanmelden.
4. Selecteer het hang slot pictogram om de sleutel hanger te vergren delen. (Het hang slot wordt vergrendeld weer gegeven wanneer het proces is voltooid. Het kan een paar seconden duren, afhankelijk van de apps die u hebt geopend.

    ![Pictogram hang slot](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Open Storage Explorer.
6. U wordt gevraagd een bericht te ontvangen met de melding ' service hub heeft toegang tot de sleutel keten? ' Voer het wacht woord voor uw Mac-beheerders account in en selecteer **altijd toestaan** (of **toestaan** als de optie **altijd toestaan** niet beschikbaar is).
7. Probeer u aan te melden.

### <a name="general-sign-in-troubleshooting-steps"></a>Algemene stappen voor het oplossen van problemen met aanmelding

* Als u zich in macOS bevindt en het aanmeld venster nooit wordt weer gegeven in het dialoog venster **wachten op verificatie** , voert u de [volgende stappen uit](#mac-keychain-errors).
* Start Storage Explorer opnieuw.
* Als het verificatie venster leeg is, wacht u ten minste één minuut voordat u het dialoog venster verificatie sluit.
* Zorg ervoor dat uw proxy-en certificaat instellingen juist zijn geconfigureerd voor zowel uw computer als Storage Explorer.
* Als u Windows gebruikt en toegang hebt tot Visual Studio 2019 op dezelfde computer en de aanmeldings referenties, probeert u zich aan te melden bij Visual Studio 2019. Nadat u zich hebt aangemeld bij Visual Studio 2019, kunt u Storage Explorer openen en uw account bekijken in het deel venster account.

Als geen van deze methoden werkt, [opent u een probleem in github](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Ontbrekende abonnementen en verbroken tenants

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld, kunt u de volgende probleemoplossings methoden proberen:

* Controleer of uw account toegang heeft tot de abonnementen die u verwacht. U kunt uw toegang controleren door u aan te melden bij de portal voor de Azure-omgeving die u wilt gebruiken.
* Zorg ervoor dat u zich hebt aangemeld via de juiste Azure-omgeving (Azure, Azure-China 21Vianet, Azure Duitsland, Azure Amerikaanse overheid of aangepaste omgeving).
* Als u zich achter een proxy server bevindt, moet u ervoor zorgen dat u de Storage Explorer proxy op de juiste wijze hebt geconfigureerd.
* Probeer het account te verwijderen en opnieuw toe te voegen.
* Als er een koppeling meer informatie is, controleert u welke fout berichten worden gerapporteerd voor de tenants die zijn mislukt. Als u niet zeker weet hoe u kunt reageren op de fout berichten, kunt u [een probleem openen in github](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Kan een gekoppeld account of opslag resource niet verwijderen

Als u een gekoppeld account of opslag resource niet kunt verwijderen via de gebruikers interface, kunt u alle gekoppelde resources hand matig verwijderen door de volgende mappen te verwijderen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Sluit Storage Explorer voordat u deze mappen verwijdert.

> [!NOTE]
> Als u ooit SSL-certificaten hebt geïmporteerd, maakt u een back-up van de inhoud van de `certs` map. Later kunt u de back-up gebruiken om uw SSL-certificaten opnieuw te importeren.

## <a name="proxy-issues"></a>Proxy problemen

Storage Explorer ondersteunt het verbinding maken met Azure Storage bronnen via een proxy server. Als u problemen ondervindt met het verbinden met Azure via een proxy, zijn hier enkele suggesties.

> [!NOTE]
> Storage Explorer ondersteunt alleen basis verificatie met proxy servers. Andere verificatie methoden, zoals NTLM, worden niet ondersteund.

> [!NOTE]
> Storage Explorer biedt geen ondersteuning voor automatische proxy-configuratie bestanden voor het configureren van proxy-instellingen.

### <a name="verify-storage-explorer-proxy-settings"></a>Proxy-instellingen voor Storage Explorer controleren

De **toepassing → proxy → proxy configuratie** -instelling bepaalt welke bron-Storage Explorer de proxy configuratie haalt.

Als u omgevings variabelen gebruiken selecteert, moet u de omgevings `HTTPS_PROXY` variabelen instellen `HTTP_PROXY` (omgevings variabelen zijn hoofdletter gevoelig, dus zorg ervoor dat u de juiste variabelen instelt). Als deze variabelen niet zijn gedefinieerd of ongeldig zijn, maakt Storage Explorer geen gebruik van een proxy. Start Storage Explorer opnieuw nadat u omgevings variabelen hebt gewijzigd.

Als u app-proxy-instellingen gebruiken selecteert, controleert u of de proxy-instellingen in de app juist zijn.

### <a name="steps-for-diagnosing-issues"></a>Stappen voor het oplossen van problemen

Als u nog steeds problemen ondervindt, kunt u de volgende probleemoplossings methoden proberen:

1. Als u verbinding kunt maken met internet zonder uw proxy te gebruiken, controleert u of Storage Explorer werkt zonder dat er proxy instellingen zijn ingeschakeld. Als Storage Explorer verbinding maakt, is er mogelijk een probleem met de proxy server. Werk samen met uw beheerder om de problemen te identificeren.
2. Controleer of andere toepassingen die gebruikmaken van de proxy server naar verwachting werken.
3. Controleer of u verbinding kunt maken met de portal voor de Azure-omgeving die u wilt gebruiken.
4. Controleer of u antwoorden kunt ontvangen van uw service-eind punten. Voer een van uw eind punt-Url's in uw browser in. Als u verbinding kunt maken, ontvangt u een `InvalidQueryParameterValue` of hetzelfde XML-antwoord.
5. Controleer of iemand anders met Storage Explorer met dezelfde proxy server verbinding kan maken. Als dat mogelijk is, moet u contact opnemen met de beheerder van de proxy server.

### <a name="tools-for-diagnosing-issues"></a>Hulpprogram ma's voor het oplossen van problemen

Een netwerk programma, zoals Fiddler, kan u helpen bij het onderzoeken van problemen.

1. Configureer uw netwerk hulpprogramma als een proxy server die wordt uitgevoerd op de lokale host. Als u achter een daad werkelijke proxy moet blijven werken, moet u mogelijk uw netwerk hulpprogramma configureren om verbinding te maken via de proxy.
2. Controleer het poort nummer dat wordt gebruikt door uw netwerk programma.
3. Configureer Storage Explorer proxy-instellingen voor het gebruik van de lokale host en het poort nummer van het netwerk hulpprogramma (zoals ' localhost: 8888 ').
 
Wanneer de instelling correct is ingesteld, worden netwerk aanvragen van Storage Explorer naar beheer-en service-eind punten in het netwerk hulpprogramma vastgelegd.
 
Als uw netwerk hulpprogramma geen logboek registratie Storage Explorer verkeer bevat, kunt u uw hulp programma testen met een andere toepassing. Voer bijvoorbeeld de eind punt-URL in voor een van uw opslag resources (zoals `https://contoso.blob.core.windows.net/` ) in een webbrowser en ontvang een antwoord dat lijkt op:

  ![Codevoorbeeld](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Het antwoord geeft aan dat de resource bestaat, ook al hebt u er geen toegang toe.

Als uw netwerk hulpprogramma alleen verkeer van andere toepassingen weergeeft, moet u mogelijk de proxy-instellingen in Storage Explorer aanpassen. Anders moet u de instellingen van uw hulp programma aanpassen.

### <a name="contact-proxy-server-admin"></a>Beheerder van de proxy server

Als uw proxy-instellingen juist zijn, moet u wellicht contact opnemen met de beheerder van de proxy server voor het volgende:

* Zorg ervoor dat uw proxy geen verkeer naar Azure-beheer-of resource-eind punten blokkeert.
* Controleer het verificatie protocol dat door uw proxy server wordt gebruikt. Storage Explorer ondersteunt alleen basis verificatie protocollen. Storage Explorer ondersteunt geen NTLM-proxy's.

## <a name="unable-to-retrieve-children-error-message"></a>Fout bericht ' kan geen onderliggende items ophalen '

Als u met Azure bent verbonden via een proxy, controleert u of de proxy-instellingen juist zijn.

Als de eigenaar van een abonnement of account u toegang heeft verleend tot een resource, controleert u of u lees-of lijst machtigingen hebt voor die bron.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>De verbindings reeks heeft geen volledige configuratie-instellingen

Als dit fout bericht wordt weer gegeven, is het mogelijk dat u niet over de benodigde machtigingen beschikt om de sleutels voor uw opslag account te verkrijgen. Om te bevestigen dat dit het geval is, gaat u naar de portal en zoekt u naar uw opslag account. U kunt dit doen door met de rechter muisknop op het knoop punt voor uw opslag account te klikken en **openen in portal** te selecteren. Ga vervolgens naar de Blade **toegangs sleutels** . Als u geen machtigingen hebt om sleutels weer te geven, ziet u het bericht ' u hebt geen toegang '. U kunt dit probleem omzeilen door de account sleutel van iemand anders te verkrijgen en de naam en sleutel te koppelen, of u kunt iemand voor een SAS vragen aan het opslag account en deze gebruiken om het opslag account te koppelen.

Als u de account sleutels ziet, moet u een probleem in GitHub oplossen zodat we u kunnen helpen het probleem op te lossen.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Er is een fout opgetreden tijdens het toevoegen van een nieuwe verbinding: TypeError: kan de eigenschap Version niet lezen

Als dit fout bericht wordt weer gegeven wanneer u een aangepaste verbinding probeert toe te voegen, zijn de verbindings gegevens die zijn opgeslagen in de lokale referentie beheerder mogelijk beschadigd. U kunt dit probleem omzeilen door de beschadigde lokale verbindingen te verwijderen en vervolgens opnieuw toe te voegen:

1. Start Storage Explorer. Ga in het menu naar **Help**  >  **in-/uitschakelen Ontwikkelhulpprogramma's**.
2. Ga in het geopende venster op het tabblad **toepassing** naar **lokale opslag** (links) > **File://**.
3. Afhankelijk van het type verbinding waarmee u een probleem ondervindt, zoekt u de sleutel en kopieert u de waarde ervan naar een tekst editor. De waarde is een matrix met uw aangepaste verbindings namen, zoals de volgende:
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
4. Nadat u de huidige verbindings namen hebt opgeslagen, stelt u de waarde in Ontwikkelhulpprogramma's in op `[]` .

Als u de niet-beschadigde verbindingen wilt behouden, kunt u de volgende stappen gebruiken om de beschadigde verbindingen te vinden. Als u geen enkele bestaande verbinding verliest, kunt u deze stappen overs Laan en de specifieke instructies voor het verwijderen van uw verbindings gegevens volgen.

1. Voeg in een tekst editor elke verbindings naam opnieuw toe aan Ontwikkelhulpprogramma's en controleer vervolgens of de verbinding nog steeds werkt.
2. Als een verbinding correct werkt, is deze niet beschadigd en kunt u deze daar veilig laten staan. Als een verbinding niet werkt, verwijdert u de waarde uit Ontwikkelhulpprogramma's en registreert u deze zodat u deze later opnieuw kunt toevoegen.
3. Herhaal deze stap totdat u al uw verbindingen hebt gecontroleerd.

Nadat u alle verbindingen hebt door lopen, moet u voor alle verbindings namen die niet meer worden toegevoegd, de beschadigde gegevens wissen (indien aanwezig) en deze opnieuw toevoegen met behulp van de standaard stappen in Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Zoek in het menu **Start** naar **referentie beheer** en open het.
2. Ga naar **Windows-referenties**.
3. Onder **algemene referenties** zoekt u naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="macos"></a>[MacOS](#tab/macOS)

1. Open Spotlight (Command + spatie balk) en zoek naar **sleutel hanger toegang**.
2. Zoek naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="linux"></a>[Linux](#tab/Linux)

Het lokale referentie beheer varieert afhankelijk van de Linux-distributie. Als uw Linux-distributie geen ingebouwd GUI-hulp programma biedt voor lokaal referentie beheer, kunt u een hulp programma van derden installeren om uw lokale referenties te beheren. U kunt bijvoorbeeld [seahorse](https://wiki.gnome.org/Apps/Seahorse/)gebruiken, een open-source GUI-hulp programma voor het beheren van lokale referenties voor Linux.

1. Open het hulp programma lokaal referentie beheer en zoek uw opgeslagen referenties.
2. Zoek naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben (bijvoorbeeld `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.
---

Als deze fout blijft optreden nadat u deze stappen hebt uitgevoerd, of als u wilt delen wat er vermoedt dat de verbindingen zijn beschadigd, [opent u een probleem](https://github.com/microsoft/AzureStorageExplorer/issues) op onze github-pagina.

## <a name="issues-with-sas-url"></a>Problemen met SAS-URL

Als u verbinding maakt met een service via een SAS-URL en er zich een fout voordoet:

* Controleer of de URL de benodigde machtigingen heeft om resources te lezen of weer te geven.
* Controleer of de URL niet is verlopen.
* Als de SAS-URL is gebaseerd op een toegangs beleid, controleert u of het toegangs beleid niet is ingetrokken.

Als u per ongeluk een ongeldige SAS-URL hebt aangesloten en nu niet kunt ontkoppelen, voert u de volgende stappen uit:

1. Wanneer u Storage Explorer uitvoert, drukt u op F12 om het venster Ontwikkelhulpprogramma's te openen.
2. Selecteer op het tabblad **toepassing** de optie **lokale opslag**  >  **File://** in de structuur aan de linkerkant.
3. Zoek de sleutel die is gekoppeld aan het Service type van de problematische SAS-URI. Als de beschadigde SAS-URI bijvoorbeeld voor een BLOB-container is, zoekt u naar de sleutel met de naam `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. De waarde van de sleutel moet een JSON-matrix zijn. Zoek het object dat is gekoppeld aan de ongeldige URI en verwijder het.
5. Druk op CTRL + R om Storage Explorer opnieuw te laden.

## <a name="linux-dependencies"></a>Linux-afhankelijkheden

### <a name="snap"></a>Module

Storage Explorer 1.10.0 en hoger is beschikbaar als een module van de snap Store. Met de Storage Explorer-uitlijning worden alle afhankelijkheden automatisch geïnstalleerd en wordt deze bijgewerkt wanneer er een nieuwe versie van de magneet beschikbaar is. Het installeren van de Storage Explorer-module is de aanbevolen installatie methode.

Voor Storage Explorer is het gebruik van een wachtwoord beheerder vereist. u moet mogelijk hand matig verbinding maken voordat Storage Explorer goed werkt. U kunt Storage Explorer verbinding maken met het wachtwoord beheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>. tar. gz-bestand

U kunt de toepassing ook downloaden als een. tar. gz-bestand, maar u moet afhankelijkheden hand matig installeren.

Storage Explorer zoals aangegeven in de. tar. gz-down load wordt alleen ondersteund voor de volgende versies van Ubuntu. Storage Explorer kan werken aan andere Linux-distributies, maar ze worden niet officieel ondersteund.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Voor Storage Explorer moet .NET Core zijn geïnstalleerd op uw systeem. U wordt aangeraden .NET Core 2,1, maar Storage Explorer werkt ook met 2,2.

> [!NOTE]
> Voor Storage Explorer versie 1.7.0 en eerder is .NET Core 2,0 vereist. Als u een nieuwere versie van .NET core hebt geïnstalleerd, moet u een [patch Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Als u Storage Explorer 1.8.0 of hoger gebruikt, hebt u ten minste .NET Core 2,1 nodig.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Down load het bestand Storage Explorer. tar. gz.
2. Installeer de [.net core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Down load het bestand Storage Explorer. tar. gz.
2. Installeer de [.net core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Down load het bestand Storage Explorer. tar. gz.
2. Installeer de [.net core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Veel bibliotheken die nodig zijn voor Storage Explorer, zijn vooraf geïnstalleerd met canonieke standaard installaties van Ubuntu. Er ontbreken mogelijk enkele van deze bibliotheken in aangepaste omgevingen. Als u problemen ondervindt met het starten van Storage Explorer, raden we u aan om ervoor te zorgen dat de volgende pakketten op uw systeem zijn geïnstalleerd:

- iproute2
- libasound2
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
- xdg-hulppr.

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patches Storage Explorer voor nieuwere versies van .NET core

Voor Storage Explorer 1.7.0 of eerder moet u de versie van .NET core die door Storage Explorer wordt gebruikt, wellicht patchen:

1. Down load versie 1.5.43 van StreamJsonRpc van [NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Zoek naar de koppeling ' pakket downloaden ' aan de rechter kant van de pagina.
2. Nadat u het pakket hebt gedownload, wijzigt u de bestands extensie van `.nupkg` in `.zip` .
3. Pak het pakket uit.
4. Open de map `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieer `StreamJsonRpc.dll` naar de volgende locaties in de map Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>' Openen in Verkenner ' van de Azure Portal werkt niet

Als de knop **openen in Verkenner** op de Azure Portal niet werkt, moet u ervoor zorgen dat u een compatibele browser gebruikt. De volgende browsers zijn getest op compatibiliteit:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Volgende stappen

Als geen van deze oplossingen voor u werkt, [opent u een probleem in github](https://github.com/Microsoft/AzureStorageExplorer/issues). U kunt dit ook doen door in de linkerbenedenhoek op de knop **probleem melden met github te** klikken.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)