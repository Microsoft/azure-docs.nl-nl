---
title: 'Azure AD Connect: release geschiedenis archief van versie | Microsoft Docs'
description: In dit artikel vindt u een lijst met alle gearchiveerde versies van Azure AD Connect en Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836084"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: release geschiedenis archief van versie

Het Azure Active Directory (Azure AD)-team werkt Azure AD Connect regel matig bij met nieuwe functies en functionaliteit. Niet alle toevoegingen zijn van toepassing op alle doel groepen.

>[!NOTE] 
> Dit artikel bevat versie referentie-informatie over alle gearchiveerde versies van Azure AD-1.3.20.0 en ouder.  Zie de release geschiedenis van de [Azure AD Connect versie](reference-connect-version-history.md) voor actuele releases

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Status van de release 

04/24/2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 

- Ondersteuning voor domein vernieuwing toevoegen 
- Functie voor open bare mappen van Exchange mail 
- Fout afhandeling van de wizard verbeteren voor service fouten 
- Er is een waarschuwings koppeling toegevoegd aan Synchronization Service Manager gebruikers interface op de pagina eigenschappen van connector. 
- De functie voor het terugschrijven van Unified groups is nu GA 
- Er is een verbeterd SSPR-fout bericht weer gegeven wanneer de domein controller een LDAP-besturings element ontbreekt 
- Er zijn diagnostische gegevens toegevoegd voor DCOM-register fouten tijdens de installatie  
- Verbeterde tracering van PHS RPC-fouten 
- EA-referenties van een onderliggend domein toestaan 
- Database naam mag worden ingevoerd tijdens installatie (standaard naam ADSync)
- Voer een upgrade uit naar ADAL 3.19.8 om een WS-Trust oplossing voor ping op te halen en ondersteuning voor nieuwe Azure-instanties toe te voegen 
- Groeps synchronisatie regels wijzigen voor flow-samAccountName, DomainNetbios en DomainFQDN naar cloud die nodig is voor claims 
- Aangepaste verwerking van de standaard synchronisatie regel is [hier](how-to-connect-fix-default-rules.md)meer informatie.
- Er is een nieuwe agent toegevoegd die wordt uitgevoerd als een Windows-service. Deze agent, met de naam ' beheer agent ', maakt uitgebreidere externe diagnose van de Azure AD Connect-server mogelijk om micro soft-technici te helpen problemen op te lossen wanneer u een ondersteunings aanvraag opent. Deze agent is niet standaard geïnstalleerd en ingeschakeld.  Zie [Wat is de Azure AD Connect-beheer agent?](whatis-aadc-admin-agent.md)voor meer informatie over het installeren en inschakelen van de agent. 
- De gebruiksrecht overeenkomst (EULA) is bijgewerkt 
- Ondersteuning voor automatische upgrades toegevoegd voor implementaties die gebruikmaken van AD FS als aanmeldings type.  Zo is ook de vereiste verwijderd voor het bijwerken van de AD FS Azure AD Relying Party Trust als onderdeel van het upgrade proces. 
- Er is een Azure AD-vertrouwens beheer taak toegevoegd die twee opties biedt: vertrouwens relaties analyseren/bijwerken en opnieuw instellen. 
- De AD FS Azure AD-vertrouwens relatie voor Relying Party is gewijzigd, zodat altijd de schakel optie-SupportMultipleDomain wordt gebruikt (inclusief vertrouwen en Azure AD-domein updates). 
- Het installatie programma New AD FS Farm is gewijzigd, zodat een pfx-certificaat nodig is door de optie voor het gebruik van een vooraf geïnstalleerd certificaat te verwijderen.
- De nieuwe AD FS Farm-werk stroom installeren is bijgewerkt, zodat alleen 1 AD FS en 1 WAP-server kunnen worden geïmplementeerd.  Alle extra servers worden uitgevoerd na de eerste installatie. 

### <a name="fixed-issues"></a>Opgeloste problemen 

- De logica voor het opnieuw verbinden van SQL voor de ADSync-service herstellen 
- Oplossing voor het toestaan van een schone installatie met een lege SQL AOA DB 
- Script voor PS-machtigingen herstellen om GWB-machtigingen te verfijnen 
- VSS-fouten met LocalDB oplossen  
- Misleidend fout bericht oplossen wanneer het object type zich niet in het bereik bevindt 
- Er is een probleem opgelost waarbij de installatie van Azure AD Power shell op een server mogelijk een assembly conflict veroorzaakt met Azure AD Connect. 
- Vaste PHS-fout op de staging-server wanneer connector referenties worden bijgewerkt in de Synchronization Service Manager-gebruikers interface. 
- Er zijn geheugen lekken opgelost 
- Diverse oplossingen voor autoupgrade 
- Diverse oplossingen voor het exporteren en niet-bevestigde import verwerking 
- Er is een fout opgelost met het verwerken van een back slash in domein-en OE-filtering 
- Er is een probleem opgelost waarbij de ADSync-service meer dan 2 minuten in beslag neemt en een probleem veroorzaakt tijdens de upgrade. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Status van de release

12/18/2018: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen

Met deze build worden de niet-standaard connectors (bijvoorbeeld generieke LDAP-connector en algemene SQL-connector) bijgewerkt die met Azure AD Connect worden geleverd. Zie versie 1.1.911.0 in [connector version release History](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)(Engelstalig) voor meer informatie over toepasselijke connectors.


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Status van de release
12/11/2018: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build kan de gebruiker een doel domein in het opgegeven forest selecteren voor de RegisteredDevices-container bij het inschakelen van write-back van apparaat.  In de vorige versies die de nieuwe functionaliteit voor apparaat opties bevatten (1.1.819.0 – 1.2.68.0), is de locatie van de RegisteredDevices-container beperkt tot de hoofdmap van het forest en hebben onderliggende domeinen niet toegestaan.  Deze beperking is alleen op nieuwe implementaties gemanifesteerd en heeft geen invloed op de in-place upgrades.  

Als een build met de bijgewerkte functionaliteit voor de opties voor apparaten is geïmplementeerd op een nieuwe server en write-back van apparaat is ingeschakeld, moet u de locatie van de container hand matig opgeven als u deze niet wilt weer geven in de hoofdmap van het forest.  Hiervoor moet u het terugschrijven van apparaten uitschakelen en weer inschakelen, zodat u de container locatie op de pagina Writeing forest kunt opgeven.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Status van de release 

11/30/2018: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen

Met deze hotfix-build wordt een conflict opgelost waarbij een verificatie fout kan optreden vanwege de onafhankelijke aanwezigheid van de MSOnline-module PowerShell Gallery op de synchronisatie server.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Status van de release 

11/19/2018: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen

Met deze hotfix-build wordt een regressie in de vorige build opgelost waarbij het terugschrijven van wacht woorden mislukt wanneer u een domein controller toevoegen in Windows Server 2008/R2 gebruikt.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Status van de release 

10/25/2018: uitgebracht voor downloaden


### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 


- De functionaliteit van het kenmerk write-back is gewijzigd om ervoor te zorgen dat gehoste voice mail werkt zoals verwacht.  Onder bepaalde scenario's werd het kenmerk msExchUcVoicemailSettings tijdens Write-back door Azure AD overschreven met een null-waarde.  De on-premises waarde van dit kenmerk wordt nu niet langer gewist door Azure AD als de Cloud waarde niet is ingesteld.
- Diagnostische gegevens zijn toegevoegd aan de Azure AD Connect wizard om verbindings problemen met Azure AD te onderzoeken en te identificeren. Deze diagnostische gegevens kunnen ook rechtstreeks via Power shell worden uitgevoerd met de cmdlet test-AdSyncAzureServiceConnectivity. 
- Diagnostische gegevens zijn toegevoegd aan de Azure AD Connect wizard om verbindings problemen met AD te onderzoeken en te identificeren. Deze diagnostische gegevens kunnen ook rechtstreeks via Power shell worden uitgevoerd met behulp van de functie Start-ConnectivityValidation in de Power shell-module ADConnectivityTools.  Zie [Wat is de Power shell-module ADConnectivityTool?](how-to-connect-adconnectivitytools.md) voor meer informatie.
- Vooraf controle van een AD-schema versie is toegevoegd voor hybride Azure Active Directory en het terugschrijven van apparaten 
- Het kenmerk van de Directory-extensie pagina is gewijzigd. Zoek naar niet-hoofdletter gevoelig.
-   Volledige ondersteuning voor TLS 1,2 is toegevoegd. Deze versie ondersteunt alle andere protocollen die worden uitgeschakeld en alleen TLS 1,2 wordt ingeschakeld op de computer waarop Azure AD Connect is geïnstalleerd.  Zie [TLS 1,2 Enforcement voor Azure AD Connect](reference-connect-tls-enforcement.md) voor meer informatie



### <a name="fixed-issues"></a>Opgeloste problemen   

- Er is een fout opgelost waarbij Azure AD Connect upgrade mislukt als SQL always on werd gebruikt. 
- Er is een fout opgelost met een probleem met het parseren van OE-namen die een slash bevatten. 
- Er is een probleem opgelost waarbij Pass-Through verificatie wordt uitgeschakeld voor een schone installatie in de faserings modus. 
- Er is een fout opgelost waardoor de Power shell-module niet kan worden geladen bij het uitvoeren van de hulpprogram ma's voor probleem oplossing 
- Er is een fout opgelost waardoor klanten geen numerieke waarden meer kunnen gebruiken in het eerste teken van een hostnaam. 
- Er is een fout opgelost waarbij Azure AD Connect ongeldige partities en een selectie van containers zou toestaan 
- Het fout bericht ' ongeldig wacht woord ' wordt weer gegeven wanneer SSO van het bureau blad is ingeschakeld. 
- Verschillende oplossingen voor het vertrouwens beheer van AD FS  
- Bij het configureren van write-back van apparaat: de schema controle is opgelost om te zoeken naar de object klasse msDs-DeviceContainer (geïntroduceerd op WS2012 R2)


## <a name="118820"></a>1.1.882.0  

9/7/2018: vrijgegeven voor downloaden, worden niet vrijgegeven voor automatische upgrade 

### <a name="fixed-issues"></a>Opgeloste problemen  

Azure AD Connect upgrade mislukt als SQL AlwaysOn-Beschik baarheid is geconfigureerd voor de ADSync DB. Deze hotfix biedt een oplossing voor dit probleem en maakt het mogelijk om de upgrade te volt ooien. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Status van de release

8/21/2018: uitgebracht voor down loads en automatische upgrades. 

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

- De integratie van pingen in Azure AD Connect is nu beschikbaar voor algemene Beschik baarheid. [Meer informatie over federatieve Azure AD met pingen](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect maakt nu een back-up van de Azure AD-vertrouwens relatie in AD FS elke keer dat er een update wordt gemaakt en slaat deze in een afzonderlijk bestand op om zo nodig eenvoudig te kunnen herstellen. Meer [informatie over de nieuwe functionaliteit en het vertrouwens beheer van Azure AD in azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Nieuwe probleemoplossings Programma's helpen bij het oplossen van het wijzigen van het primaire e-mail adres en het verbergen van accounts uit de algemene adres lijst
- Azure AD Connect is bijgewerkt met de meest recente SQL Server 2012 native client
- Wanneer u de aanmelding van een gebruiker naar een wacht woord-hash-synchronisatie of Pass-Through-verificatie in de taak ' gebruiker aanmelden wijzigen ' wijzigt, wordt het selectie vakje naadloze enkelvoudige Sign-On standaard ingeschakeld.
- Er is ondersteuning toegevoegd voor Windows Server Essentials 2019
- De Azure AD Connect Health-Agent is bijgewerkt naar de meest recente versie 3.1.7.0
- Als het installatie programma wijzigingen in de standaard synchronisatie regels detecteert, wordt de beheerder tijdens een upgrade met een waarschuwing gevraagd voordat de gewijzigde regels worden overschreven. Hiermee kan de gebruiker corrigerende maat regelen nemen en later hervatten. Oud gedrag: als er een out-of-Box-regel is gewijzigd, wordt deze regels door de hand matige upgrade overschreven zonder dat er een waarschuwing voor de gebruiker wordt gegeven en scheduler is uitgeschakeld zonder dat de gebruiker wordt geïnformeerd. Nieuw gedrag: de gebruiker wordt gevraagd een waarschuwing te krijgen voordat de gewijzigde out-of-Box-synchronisatie regels worden overschreven. De gebruiker heeft de keuze om het upgrade proces te stoppen en later te hervatten na het uitvoeren van corrigerende maat regelen.
- Zorg voor een betere afhandeling van een probleem met FIPS-naleving, waarbij een fout bericht wordt weer gegeven voor het genereren van MD5-hashes in een FIPS-compatibele omgeving en een koppeling naar documentatie die een tijdelijke oplossing biedt voor dit probleem.
- Update van de gebruikers interface voor het verbeteren van de Federatie taken in de wizard, die nu onder een afzonderlijke subgroep voor Federatie vallen. 
- Alle onderliggende Federatie taken worden nu gegroepeerd onder een enkel vervolg menu.
- Een nieuwe, opnieuw geoldeerde ADSyncConfig posh-module (AdSyncConfig. psm1) met nieuwe AD-machtigingen functies die zijn verplaatst van de oude bestand adsyncprep. psm1 (die mogelijk binnenkort worden afgeschaft)

### <a name="fixed-issues"></a>Opgeloste problemen 

- Er is een fout opgelost waarbij de Azure AD Connect-server een hoog CPU-gebruik weergeeft na een upgrade naar .NET 4.7.2
- Er is een probleem opgelost waarbij een fout melding wordt gegenereerd voor een automatisch opgelost SQL-deadlock
- Er zijn verschillende toegankelijkheids problemen opgelost voor de editor voor synchronisatie regels en de synchronisatie Service Manager  
- Er is een fout opgelost waarbij Azure AD Connect geen informatie over register instellingen kan ophalen
- Er is een fout opgelost die problemen heeft gemaakt wanneer de gebruiker vooruit/achteruit gaat in de wizard
- Er is een fout opgelost om te voor komen dat er fouten optreden vanwege onjuiste multi thread-hand leiding in de wizard
- Wanneer met de functie voor het filteren van groeps synchronisatie een LDAP-fout optreedt tijdens het omzetten van beveiligings groepen, retourneert Azure AD Connect nu de uitzonde ring met volledige precisie.  De hoofd oorzaak van de Referral-uitzonde ring is nog steeds onbekend en wordt verholpen door een andere fout.
-  Er is een fout opgelost waarbij de machtigingen voor beurs-en NGC-sleutels (MS-DS-KeyCredentialLink-kenmerk op gebruikers-en apparaatobject voor WHfB) niet juist zijn ingesteld.     
- Er is een fout opgelost waarbij ' set-ADSyncRestrictedPermissions ' niet juist is aangeroepen
-  Ondersteuning voor het verlenen van machtigingen voor het terugschrijven van groepen in de installatie wizard van Azure ADConnect toevoegen
- Wacht woord-hash-synchronisatie is niet uitgeschakeld wanneer u de aanmeldings methode wijzigt van de wachtwoord-hash-synchronisatie naar AD FS.
- Verificatie toegevoegd voor IPv6-adressen in de AD FS configuratie
- Het meldings bericht is bijgewerkt om te melden dat er al een bestaande configuratie bestaat.
- Write-back van apparaat kan geen container detecteren in een niet-vertrouwd forest. Dit is bijgewerkt om een beter fout bericht en een koppeling naar de juiste documentatie te geven
- Het opheffen van de selectie van een OE en vervolgens synchronisatie/terugschrijven die overeenkomt met deze organisatie-eenheid geeft een algemene synchronisatie fout. Dit is gewijzigd om een duidelijker fout bericht te maken.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Status van de release

5/14/2018: uitgebracht voor automatische upgrade en downloaden.

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

Nieuwe functies en verbeteringen

- Deze release bevat de open bare preview van de integratie van PingFederate in Azure AD Connect. Met deze release kunnen klanten eenvoudig hun Azure Active Directory omgeving gemakkelijk en betrouwbaar configureren om PingFederate te gebruiken als hun Federation-provider. Zie onze [online documentatie](plan-connect-user-signin.md#federation-with-pingfederate)voor meer informatie over het gebruik van deze nieuwe functie. 
- Het hulp programma voor probleem oplossing van Azure AD Connect wizard is bijgewerkt, waar nu meer fout scenario's worden geanalyseerd, zoals gekoppelde post vakken en dynamische AD-groepen. Lees [hier](tshoot-connect-objectsync.md)meer over het hulp programma voor probleem oplossing.
- De configuratie van het terugschrijven van apparaten wordt nu alleen beheerd in de wizard Azure AD Connect.
- Er wordt een nieuwe Power shell-module met de naam ADSyncTools. psm1 toegevoegd die kan worden gebruikt voor het oplossen van problemen met SQL-connectiviteit en verschillende andere hulpprogram ma's voor probleem oplossing. Lees [hier](tshoot-connect-tshoot-sql-connectivity.md)meer over de ADSyncTools-module. 
- Er is een nieuwe extra taak ' Device-opties configureren ' toegevoegd. U kunt de taak gebruiken om de volgende twee bewerkingen te configureren: 
  - **Hybride Azure AD join**: als uw omgeving een on-PREMISes AD-footprint heeft en u ook wilt profiteren van de mogelijkheden van Azure Active Directory, kunt u aan hybride Azure AD gekoppelde apparaten implementeren. Dit zijn apparaten die zowel aan uw on-premises Active Directory als aan uw Azure Active Directory zijn gekoppeld.
  - **Write-back van apparaat**: apparaat write-back wordt gebruikt voor het inschakelen van voorwaardelijke toegang op basis van apparaten die zijn AD FS (2012 R2 of hoger) beveiligde apparaten

    >[!NOTE] 
    > - De optie om het terugschrijven van apparaten in te scha kelen voor het aanpassen van synchronisatie opties wordt grijs weer gegeven. 
    > -  De Power shell-module voor ADPrep is afgeschaft met deze release.


### <a name="fixed-issues"></a>Opgeloste problemen 

- Deze versie werkt de SQL Server Express-installatie bij naar SQL Server 2012 SP4, die onder andere oplossingen biedt voor verschillende beveiligings problemen.  Lees [hier](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) meer informatie over SQL Server 2012 SP4.
- Verwerking van de synchronisatie regel: de regels voor het synchroniseren van uitgaande invoeg toepassing zonder voor waarde voor samen voegen moet worden uitgeschakeld als de bovenliggende synchronisatie regel niet meer van toepassing is
- Er zijn verschillende toegankelijkheids oplossingen toegepast op de Synchronization Service Manager gebruikers interface en de editor voor synchronisatie regels
- Wizard Azure AD Connect: fout bij het maken van een AD Connector-account wanneer Azure AD Connect in een werk groep is
- Wizard Azure AD Connect: op de aanmeldings pagina van Azure AD wordt het selectie vakje voor de verificatie weer gegeven wanneer er geen overeenkomsten zijn in AD-domeinen en geverifieerde Azure AD-domeinen
- Automatisch bijwerken van Power Shell-oplossing om de status van automatische upgrade op de juiste wijze in te stellen na een automatische upgrade poging.
- Wizard Azure AD Connect: de telemetrie bijgewerkt om eerder ontbrekende informatie vast te leggen
- Wizard Azure AD Connect: de volgende wijzigingen zijn aangebracht wanneer u de aanmeldings taak **gebruiker wijzigen** gebruikt om over te scha kelen van AD FS naar Pass-Through-verificatie:
    - De Pass-Through-verificatie agent wordt geïnstalleerd op de Azure AD Connect-server en de functie Pass-Through-verificatie is ingeschakeld, voordat we domein (en) van federatieve naar beheerd converteren.
    - Gebruikers worden niet meer geconverteerd van federatieve naar beheerd. Alleen domein (en) worden geconverteerd.
- Wizard Azure AD Connect: AD FS regex voor meerdere domeinen is onjuist wanneer de UPN ' speciale teken-regex-update heeft voor het ondersteunen van speciale tekens
- Wizard Azure AD Connect: het bericht ' bron anker kenmerk configureren ' verwijderen wanneer er geen wijziging 
- Wizard Azure AD Connect: AD FS ondersteuning voor het scenario met de dubbele Federatie
- Wizard Azure AD Connect: AD FS claims zijn niet bijgewerkt voor het toegevoegde domein bij het converteren van een beheerd domein naar federatief
- Azure AD Connect wizard: tijdens de detectie van geïnstalleerde pakketten vinden we verouderde Dirsync/Azure AD Sync/Azure AD Connect gerelateerde producten. We zullen nu proberen de verouderde producten te verwijderen.
- Wizard Azure AD Connect: Corrigeer de toewijzing van fout berichten wanneer de installatie van de Passthrough-verificatie agent mislukt
- Wizard Azure AD Connect: de container ' configuratie ' is verwijderd van de pagina organisatie-eenheid voor domein filtering
- Installatie van de synchronisatie-engine: Verwijder overbodige, verouderde logica die af en toe mislukt van de synchronisatie-engine MSI installeren
- Wizard Azure AD Connect: Help-tekst van pop-up herstellen op de pagina optionele functies voor wachtwoord-hash-synchronisatie
- Engine-runtime synchroniseren: Corrigeer het scenario waarbij een CS-object een geïmporteerde regels voor verwijderen en synchroniseren heeft geprobeerd het object opnieuw in te richten.
- Engine-runtime synchroniseren: Help-koppeling voor het oplossen van problemen met online connectiviteit toevoegen aan het gebeurtenis logboek voor een import fout
- Engine-runtime synchroniseren: beperkt geheugen gebruik van de synchronisatie planner bij het inventariseren van connectors
- Wizard Azure AD Connect: een probleem oplossen met het oplossen van een aangepast synchronisatie service account zonder machtigingen voor AD-Lees
- Wizard Azure AD Connect: de logboek registratie van domein-en OE-filters optimaliseren
- Wizard Azure AD Connect: AD FS standaard claims toevoegen aan Federatie vertrouwensrelatie die zijn gemaakt voor MFA-scenario
- Wizard Azure AD Connect: AD FS implementeren WAP: het toevoegen van een server kan geen nieuw certificaat gebruiken
- Wizard Azure AD Connect: DSSO uitzonde ring wanneer onPremCredentials voor een domein niet zijn geïnitialiseerd 
- Het kenmerk van de AD DN-naam van het actieve gebruikers object op de voor keur plaatsen.
- Een cosmetische fout is opgelost. de prioriteit van de eerste OOB-synchronisatie regel is ingesteld op 99 in plaats van 100



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: alleen beschikbaar voor downloaden

>[!NOTE]
>Deze versie is een hotfix voor Azure AD Connect
### <a name="azure-ad-connect-sync"></a>Azure AD Connect-synchronisatie
#### <a name="fixed-issues"></a>Opgeloste problemen
Correctie van een probleem was automatische Azure-exemplaar detectie voor China-tenants af en toe mislukt.  

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issues"></a>Opgeloste problemen

Er is een probleem opgetreden in de logica voor het opnieuw proberen van de configuratie, wat zou leiden tot een ArgumentException dat er al een item met dezelfde sleutel is toegevoegd.  Dit kan leiden tot het mislukken van alle pogingen.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: vrijgegeven voor automatische upgrade en downloaden.
>[!NOTE]
>Wanneer de upgrade naar deze nieuwe versie is voltooid, wordt automatisch een volledige synchronisatie en volledige import geactiveerd voor de Azure AD-connector en een volledige synchronisatie voor de AD-connector. Dit kan enige tijd duren, afhankelijk van de grootte van uw Azure AD Connect-omgeving, moet u ervoor zorgen dat u de benodigde stappen hebt uitgevoerd om de upgrade uit te voeren of uit te kunnen voeren totdat u een geschikt moment hebt gevonden.
>[!NOTE]
>"De functie voor het afwerken van upgrades is onjuist uitgeschakeld voor sommige tenants die later builds hebben geïmplementeerd dan 1.1.524.0. Voer de volgende Power shell-cmdlet uit om ervoor te zorgen dat uw Azure AD Connect-exemplaar nog steeds in aanmerking komt voor autoupgrade: ' set-ADSyncAutoUpgrade-AutoupGradeState enabled '

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opgeloste problemen

* Met Set-ADSyncAutoUpgrade cmdlet wordt de functie voor automatisch upgraden eerder geblokkeerd als de status van de automatische upgrade is ingesteld op opgeschort. Deze functionaliteit is nu zodanig gewijzigd dat de functie voor het bijwerken van toekomstige builds niet wordt geblokkeerd.
* De optie "wachtwoord synchronisatie" voor de **aanmeldings pagina van de gebruiker** gewijzigd in "wachtwoord-hash-synchronisatie".  Azure AD Connect synchroniseert wacht woord-hashes, geen wacht woorden, zodat deze worden uitgelijnd met wat er daad werkelijk gebeurt.  Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md) voor meer informatie

## <a name="117490"></a>1.1.749.0
Status: is vrijgegeven om klanten te selecteren

>[!NOTE]
>Wanneer de upgrade naar deze nieuwe versie is voltooid, wordt automatisch een volledige synchronisatie en volledige import geactiveerd voor de Azure AD-connector en een volledige synchronisatie voor de AD-connector. Dit kan enige tijd duren, afhankelijk van de grootte van uw Azure AD Connect-omgeving, moet u ervoor zorgen dat u de benodigde stappen hebt uitgevoerd om de upgrade uit te voeren of uit te kunnen voeren totdat u een geschikt moment hebt gevonden.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opgeloste problemen
* Corrigeer het tijd venster op de achtergrond taken voor de pagina partitie filtering wanneer u overschakelt naar de volgende pagina.

* Er is een fout opgelost die de toegangs fout veroorzaakte tijdens de aangepaste ConfigDB-actie.

* Er is een fout opgelost bij het herstellen van de time-out van de SQL-verbinding.

* Er is een fout opgelost waarbij certificaten met SAN-joker tekens een controle op vereisten niet hebben.

* Er is een fout opgelost waardoor miiserver.exe vastloopt tijdens het exporteren van een Azure AD-connector.

* Er is een fout opgelost waarbij een ongeldige wachtwoord poging is geregistreerd op de domein controller bij het uitvoeren van de Azure AD Connect wizard om de configuratie te wijzigen.


#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Het toevoegen van privacy-instellingen voor de AVG (AVG).  Zie het artikel [hier](reference-connect-user-privacy.md)voor meer informatie.

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Application Telemetry: de beheerder kan deze klasse gegevens in-of uitschakelen

* Status gegevens van Azure AD-beheerder moeten de status Portal bezoeken om hun status instellingen te beheren.
   Zodra het service beleid is gewijzigd, worden deze door de agents gelezen en afgedwongen.

* De configuratie acties voor het terugschrijven van apparaten en een voortgangs balk voor het initialiseren van pagina's zijn toegevoegd

* Verbeterde algemene diagnoses met HTML-rapport en volledige gegevens verzameling in een ZIP-Text/HTML-rapport

* Verbeterde betrouw baarheid van automatische upgrade en extra telemetrie toegevoegd om te garanderen dat de status van de server kan worden bepaald

* Beschik bare machtigingen voor bevoegde accounts op het AD Connector-account beperken

  * Voor nieuwe installaties beperkt de wizard de machtigingen die privileged accounts hebben op het MSOL-account nadat het MSOL-account is gemaakt.

De volgende wijzigingen worden doorgevoerd:
1. Snelle installaties
2. Aangepaste installaties met het account automatisch maken
3. Het installatie programma is gewijzigd zodat het geen SA-bevoegdheid vereist bij een schone installatie van Azure AD Connect

* Er is een nieuw hulp programma toegevoegd om synchronisatie problemen voor een specifiek object op te lossen. De functie is beschikbaar in de optie voor het oplossen van problemen met object synchronisatie van Azure AD Connect wizard problemen met aanvullende taken oplossen. Op dit moment controleert het hulp programma op het volgende:

  * De UserPrincipalName van het gesynchroniseerde gebruikers object en het gebruikers account in de Azure AD-Tenant komen niet overeen.
  * Als het object wordt gefilterd op basis van synchronisatie vanwege domein filtering
  * Als het object wordt gefilterd op basis van synchronisatie vanwege het filteren van organisatie-eenheid (OE)

* Er is een nieuw hulp programma toegevoegd voor het synchroniseren van de huidige wacht woord-hash die is opgeslagen in het on-premises Active Directory voor een specifiek gebruikers account.

Voor het hulp programma is geen wachtwoord wijziging vereist. Deze optie is beschikbaar onder problemen met wachtwoord hash-synchronisatie oplossen van Azure AD Connect wizard problemen met aanvullende taak oplossen.






## <a name="116540"></a>1.1.654.0
Status: december twaalfde, 2017

>[!NOTE]
>Deze release is een beveiligings update die betrekking heeft op beveiliging voor Azure AD Connect
### <a name="azure-ad-connect"></a>Azure AD Connect
Er is een verbetering toegevoegd aan Azure AD Connect versie 1.1.654.0 (en later) om ervoor te zorgen dat de aanbevolen machtigings wijzigingen die worden beschreven onder sectie [vergrendeling toegang tot het AD DS account](#lock) automatisch worden toegepast wanneer Azure AD Connect het AD DS-account maakt. 

- Bij het instellen van Azure AD Connect kan de installatie beheerder een bestaand AD DS account opgeven of het account Azure AD Connect automatisch laten maken. De machtigings wijzigingen worden automatisch toegepast op het AD DS account dat door Azure AD Connect tijdens de installatie is gemaakt. Ze worden niet toegepast op het bestaande AD DS account dat is ingesteld door de installatie beheerder.
- Voor klanten die een upgrade hebben uitgevoerd van een oudere versie van Azure AD Connect naar 1.1.654.0 (of later), worden de gewijzigde machtigingen niet terugwerkt op bestaande AD DS accounts die zijn gemaakt vóór de upgrade. Ze worden alleen toegepast op nieuwe AD DS accounts die na de upgrade zijn gemaakt. Dit doet zich voor wanneer u nieuwe AD-forests toevoegt die moeten worden gesynchroniseerd met Azure AD.

>[!NOTE]
>Deze versie verwijdert alleen het beveiligings probleem voor nieuwe installaties van Azure AD Connect waarbij het service account wordt gemaakt door het installatie proces. Voor bestaande installaties of in gevallen waarin u het account zelf opgeeft, moet u ervoor zorgen dat dit beveiligings probleem niet bestaat.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> De toegang tot het AD DS-account vergren delen
Vergrendel de toegang tot het AD DS-account door de volgende machtigings wijzigingen in de on-premises AD te implementeren:  

*   Overname uitschakelen voor het opgegeven object
*   Verwijder alle Ace's van het specifieke object, met uitzonde ring van Ace's die specifiek voor zichzelf zijn. We willen de standaard machtigingen intact houden wanneer het gaat om zelf.
*   Wijs deze specifieke machtigingen toe:

Type     | Naam                          | Access               | Van toepassing op
---------|-------------------------------|----------------------|--------------|
Toestaan    | SYSTEEM                        | Volledig beheer         | Dit object  |
Toestaan    | Ondernemingsadministrators             | Volledig beheer         | Dit object  |
Toestaan    | Domeinadministrators                 | Volledig beheer         | Dit object  |
Toestaan    | Beheerders                | Volledig beheer         | Dit object  |
Toestaan    | Ondernemings domein controllers | Inhoud weer geven        | Dit object  |
Toestaan    | Ondernemings domein controllers | Alle eigenschappen lezen  | Dit object  |
Toestaan    | Ondernemings domein controllers | Lees machtigingen     | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Inhoud weer geven        | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Alle eigenschappen lezen  | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Lees machtigingen     | Dit object  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Power shell-script om een vooraf bestaand service account te verhogen

Als u het Power shell-script wilt gebruiken om deze instellingen toe te passen op een reeds bestaand AD DS-account (eer dat door uw organisatie is verschaft of door een eerdere installatie van Azure AD Connect te maken, moet u het script downloaden van de bovenstaande koppeling.

##### <a name="usage"></a>Gebruik:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Waar 

**$ObjectDN** = het Active Directory account waarvan de machtigingen moeten worden gescherpt.

**$Credential** = beheerders referenties die de benodigde bevoegdheden hebben om de machtigingen voor het $ObjectDN account te beperken. Deze bevoegdheden worden meestal opgeslagen door de beheerder van de onderneming of het domein. Gebruik de Fully Qualified Domain Name van het Administrator-account om fouten bij het opzoeken van accounts te voor komen. Voor beeld: contoso. com\admin.

>[!NOTE] 
>$credential. De gebruikers naam moet de indeling FQDN\username hebben. Voor beeld: contoso. com\admin 
##### <a name="example"></a>Voorbeeld:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Is dit beveiligings probleem gebruikt om onbevoegde toegang te krijgen?

Als u wilt weten of dit beveiligingslek is gebruikt om uw Azure AD Connect-configuratie in te zetten, moet u de datum van de laatste wachtwoord herstel van het service account controleren.  Als de tijds tempel in het geval van onverwacht, verder onderzoek, via het gebeurtenis logboek, voor die wachtwoord herstel gebeurtenis, moet worden uitgevoerd.

Zie [micro soft security advisor 4056318](/security-updates/securityadvisories/2017/4056318) voor meer informatie.

## <a name="116490"></a>1.1.649.0
Status: oktober 27 2017

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie voor automatische upgrade van Azure AD Connect.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Probleem  opgelost
* Er is een probleem opgelost met versie compatibiliteit tussen Azure AD Connect en Azure AD Connect Health Agent (voor synchronisatie). Dit probleem is van invloed op klanten die Azure AD Connect in-place upgrade uitvoeren op versie 1.1.647.0, maar die momenteel versie 3.0.127.0 van Health Agent hebben. Na de upgrade kan de Health-Agent geen status gegevens meer verzenden over Azure AD Connect synchronisatie service naar Azure AD Health Service. Met deze oplossing wordt de versie 3.0.129.0 van de status agent geïnstalleerd tijdens Azure AD Connect in-place upgrade. Versie 3.0.129.0 van Health Agent heeft geen compatibiliteits probleem met Azure AD Connect versie 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Status: oktober 19 2017

> [!IMPORTANT]
> Er is een bekend compatibiliteits probleem tussen Azure AD Connect versie 1.1.647.0 en Azure AD Connect Health Agent (voor synchronisatie) versie 3.0.127.0. Dit probleem verhindert dat de Health-Agent status gegevens verzendt over de Azure AD Connect synchronisatie service (met inbegrip van synchronisatie fouten van objecten en het uitvoeren van geschiedenis gegevens) naar Azure AD Health Service. Controleer voordat u uw Azure AD Connect-implementatie hand matig bijwerkt naar versie 1.1.647.0 de huidige versie van Azure AD Connect Health Agent die is geïnstalleerd op uw Azure AD Connect server. U kunt dit doen door via *configuratie scherm → software toevoegen* te gaan en te zoeken naar de toepassing *Microsoft Azure AD Connect Health-Agent voor synchronisatie*. Als de versie 3.0.127.0 is, is het raadzaam te wachten tot de volgende versie van Azure AD Connect beschikbaar is voordat u de upgrade uitvoert. Als de versie van de Health-Agent niet 3.0.127.0 is, is het goed om door te gaan met de hand leiding in-place upgrade. Houd er rekening mee dat dit probleem geen invloed heeft op de swing-upgrade of klanten die een nieuwe installatie van Azure AD Connect uitvoeren.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opgeloste problemen
* Er is een probleem opgelost met de *aanmeld taak change user* in azure AD Connect wizard:

  * Het probleem treedt op wanneer u een bestaande Azure AD Connect-implementatie met wachtwoord synchronisatie hebt **ingeschakeld** en u probeert de aanmeldings methode voor gebruikers in te stellen als *Pass-Through-verificatie*. Voordat de wijziging wordt toegepast, wordt in de wizard op onjuiste wijze de prompt "*wachtwoord synchronisatie uitschakelen*" weer gegeven. Wachtwoord synchronisatie blijft echter ingeschakeld nadat de wijziging is toegepast. Met deze oplossing wordt de vraag niet meer weer gegeven in de wizard.

  * De wizard schakelt wachtwoord synchronisatie niet uit wanneer u de aanmeldings methode voor gebruikers bijwerkt met behulp van de *aanmeldings taak gebruiker wijzigen* . Dit is om onderbrekingen te voor komen aan klanten die wachtwoord synchronisatie willen blijven gebruiken, zelfs als ze Pass-Through-verificatie of Federatie inschakelen als de aanmeldings methode voor de primaire gebruiker.

  * Als u wachtwoord synchronisatie wilt uitschakelen nadat u de aanmeldings methode voor gebruikers hebt bijgewerkt, moet u de taak *synchronisatie configuratie aanpassen* in de wizard uitvoeren. Als u naar de pagina *optionele functies* navigeert, schakelt u de optie voor *wachtwoord synchronisatie* uit.

  * Houd er rekening mee dat hetzelfde probleem ook optreedt als u naadloze eenmalige aanmelding wilt in-of uitschakelen. In het bijzonder hebt u een bestaande Azure AD Connect-implementatie met wachtwoord synchronisatie ingeschakeld en is de aanmeldings methode voor gebruikers al geconfigureerd als *Pass-Through-verificatie*. Met de taak *gebruiker aanmelden wijzigen* probeert u de optie *naadloze eenmalige aanmelding inschakelen in te scha kelen* terwijl de aanmeldings methode van de gebruiker geconfigureerd blijft als ' Pass-Through-verificatie '. Voordat de wijziging wordt toegepast, wordt in de wizard op onjuiste wijze de prompt "*wachtwoord synchronisatie uitschakelen*" weer gegeven. Wachtwoord synchronisatie blijft echter ingeschakeld nadat de wijziging is toegepast. Met deze oplossing wordt de vraag niet meer weer gegeven in de wizard.

* Er is een probleem opgelost met de *aanmeld taak change user* in azure AD Connect wizard:

  * Het probleem treedt op wanneer u een bestaande Azure AD Connect-implementatie met wachtwoord synchronisatie hebt **uitgeschakeld** en u probeert de aanmeldings methode voor gebruikers in te stellen als *Pass-Through-verificatie*. Wanneer de wijziging wordt toegepast, maakt de wizard verificatie via pass-through-authenticatie en wachtwoord synchronisatie mogelijk. Met deze oplossing schakelt de wizard niet langer wachtwoord synchronisatie in.

  * Voorheen was wachtwoord synchronisatie een vereiste voor het inschakelen van Pass-Through-verificatie. Wanneer u de aanmeldings methode voor gebruikers als *Pass-Through-verificatie* instelt, wordt zowel Pass-Through-verificatie als wachtwoord synchronisatie door de wizard ingeschakeld. Onlangs is wachtwoord synchronisatie verwijderd als een vereiste. Als onderdeel van Azure AD Connect versie 1.1.557.0 is er een wijziging aangebracht in Azure AD Connect om wachtwoord synchronisatie niet in te scha kelen wanneer u de aanmeldings methode voor gebruikers als *Pass-Through-verificatie* instelt. De wijziging is echter alleen toegepast op Azure AD Connect-installatie. Met deze oplossing wordt dezelfde wijziging ook toegepast op de aanmeldings taak *van de gebruiker wijzigen* .

  * Houd er rekening mee dat hetzelfde probleem ook optreedt als u naadloze eenmalige aanmelding wilt in-of uitschakelen. In het bijzonder hebt u een bestaande Azure AD Connect-implementatie met wachtwoord synchronisatie uitgeschakeld en is de aanmeldings methode voor gebruikers al geconfigureerd als *Pass-Through-verificatie*. Met de taak *gebruiker aanmelden wijzigen* probeert u de optie *naadloze eenmalige aanmelding inschakelen in te scha kelen* terwijl de aanmeldings methode van de gebruiker geconfigureerd blijft als ' Pass-Through-verificatie '. Wanneer de wijziging wordt toegepast, schakelt de wizard wachtwoord synchronisatie in. Met deze oplossing schakelt de wizard niet langer wachtwoord synchronisatie in. 

* Er is een probleem opgelost waardoor Azure AD Connect upgrade mislukt met fout '*kan geen upgrade uitvoeren voor de synchronisatie service*'. Verder kan de synchronisatie service niet meer worden gestart met de gebeurtenis Fout '*de service kan niet worden gestart omdat de versie van de data base nieuwer is dan de versie van de geïnstalleerde bestanden*'. Het probleem treedt op wanneer de beheerder die de upgrade uitvoert, geen sysadmin-bevoegdheid heeft voor de SQL Server die wordt gebruikt door Azure AD Connect. Met deze oplossing vereist Azure AD Connect alleen dat de beheerder db_owner bevoegdheid heeft voor de ADSync-data base tijdens de upgrade.

* Er is een fout opgetreden bij het upgraden van een Azure AD Connect dat betrokken klanten die [naadloze eenmalige aanmelding](./how-to-connect-sso.md)hebben ingeschakeld. Nadat Azure AD Connect is bijgewerkt, wordt naadloze single Sign-On onjuist weer gegeven als uitgeschakeld in Azure AD Connect wizard, zelfs als de functie ingeschakeld en volledig functioneel blijft. Met deze oplossing wordt de functie nu correct weer gegeven als ingeschakeld in de wizard.

* Er is een probleem opgelost dat ervoor zorgt dat Azure AD Connect wizard altijd de prompt '*bron anker configureren*' weergeeft op de pagina *gereed voor configuratie* , zelfs als er geen wijzigingen zijn aangebracht in het bron anker.

* Bij het uitvoeren van hand matige in-place upgrade van Azure AD Connect moet de klant de globale beheerders referenties van de bijbehorende Azure AD-Tenant opgeven. Voorheen kan de upgrade door gaan, zelfs als de globale beheerders referenties bij een andere Azure AD-Tenant horen. Terwijl de upgrade lijkt te zijn voltooid, worden bepaalde configuraties niet op de juiste wijze bewaard met de upgrade. Met deze wijziging voor komt u dat de upgrade door de wizard wordt voortgezet als de opgegeven referenties niet overeenkomen met de Azure AD-Tenant.

* Redundante logica verwijderd die onnodig opnieuw is opgestart Azure AD Connect Health Service aan het begin van een hand matige upgrade.


#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Logica is toegevoegd om de vereiste stappen voor het instellen van Azure AD Connect met micro soft Duitsland-Cloud te vereenvoudigen. Voorheen moet u specifieke register sleutels op de Azure AD Connect-server bijwerken zodat deze correct werkt met de micro soft Duitsland-Cloud, zoals beschreven in dit artikel. Nu kunnen Azure AD Connect automatisch detecteren of uw Tenant zich in micro soft Duitsland-Cloud bevindt op basis van de referenties van de globale beheerder die tijdens de installatie zijn ingesteld.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Opmerking: de synchronisatie service heeft een WMI-interface waarmee u uw eigen aangepaste scheduler kunt ontwikkelen. Deze interface is nu afgeschaft en wordt verwijderd uit toekomstige versies van Azure AD Connect die na 30 juni 2018 worden verzonden. Klanten die de synchronisatie planning willen aanpassen, moeten gebruikmaken [van de ingebouwde Planner](./how-to-connect-sync-feature-scheduler.md).
#### <a name="fixed-issues"></a>Opgeloste problemen
* Als Azure AD Connect wizard het AD-Connector account maakt dat vereist is voor het synchroniseren van wijzigingen van on-premises Active Directory, wordt het account dat is vereist voor het lezen van PublicFolder-objecten niet goed toegewezen. Dit probleem is van invloed op de snelle installatie en aangepaste installatie. Met deze wijziging wordt het probleem opgelost.

* Er is een probleem opgelost waardoor de pagina Azure AD Connect wizard problemen oplossen niet correct wordt weer gegeven voor beheerders die worden uitgevoerd vanaf Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Bij het oplossen van problemen met wachtwoord synchronisatie met behulp van de Azure AD Connect wizard problemen oplossen, wordt nu de domein-specifieke status geretourneerd.

* Als u eerder de synchronisatie van wacht woord-Hashs probeerde in te scha kelen, controleert Azure AD Connect niet of het AD-Connector account de vereiste machtigingen heeft voor het synchroniseren van wacht woord-hashes uit on-premises AD. Azure AD Connect wizard wordt nu geverifieerd en u wordt gewaarschuwd als het AD Connector-account niet over voldoende machtigingen beschikt.

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issue"></a>Probleem  opgelost
* Er is een probleem opgelost dat betrekking heeft op het gebruik van de functie [MS-DS-ConsistencyGuid als bron anker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . Dit probleem is van invloed op klanten die Federatie hebben geconfigureerd *met AD FS* als de aanmeldings methode voor gebruikers. Wanneer u de taak *bron anker configureren* in de wizard uitvoert, schakelt Azure AD Connect over naar het bron kenmerk * MS-DS-ConsistencyGuid als broncode voor immutableId. Als onderdeel van deze wijziging Azure AD Connect probeert de claim regels voor ImmutableId in AD FS bij te werken. Deze stap is echter mislukt omdat Azure AD Connect geen beheerders referenties hebt vereist voor het configureren van AD FS. Met deze oplossing wordt Azure AD Connect nu gevraagd om de beheerders referenties voor AD FS in te voeren wanneer u de taak *bron anker configureren* uitvoert.



## <a name="116140"></a>1.1.614.0
Status: september 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Bekende problemen
* Er is een bekend probleem dat ervoor zorgt dat Azure AD Connect upgrade mislukt met de fout '*kan geen upgrade uitvoeren voor de synchronisatie service*'. Verder kan de synchronisatie service niet meer worden gestart met de gebeurtenis Fout '*de service kan niet worden gestart omdat de versie van de data base nieuwer is dan de versie van de geïnstalleerde bestanden*'. Het probleem treedt op wanneer de beheerder die de upgrade uitvoert, geen sysadmin-bevoegdheid heeft voor de SQL Server die wordt gebruikt door Azure AD Connect. Dbo-machtigingen zijn niet voldoende.

* Er is een bekend probleem met Azure AD Connect-upgrade dat van invloed is op klanten die [naadloze eenmalige aanmelding](how-to-connect-sso.md)hebben ingeschakeld. Nadat Azure AD Connect is bijgewerkt, wordt de functie als uitgeschakeld weer gegeven in de wizard, zelfs als de functie ingeschakeld blijft. In toekomstige versies wordt een oplossing voor dit probleem verstrekt. Klanten die zich zorgen maken over dit weergave probleem kunnen het hand matig oplossen door naadloze single-Sign-On in te scha kelen in de wizard.

#### <a name="fixed-issues"></a>Opgeloste problemen
* Er is een probleem opgelost waarbij wordt voor komen dat Azure AD Connect de claim regels in on-premises AD FS bijwerkt tijdens het inschakelen van de functie [MS-DS-ConsistencyGuid als bron anker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . Het probleem treedt op als u probeert de functie in te scha kelen voor een bestaande Azure AD Connect-implementatie die AD FS geconfigureerd als de aanmeldings methode. Dit probleem doet zich voor omdat de wizard niet wordt gevraagd om ADFS-referenties voordat wordt geprobeerd de claim regels in AD FS bij te werken.
* Er is een probleem opgelost waarbij de installatie van Azure AD Connect mislukt als NTLM is uitgeschakeld voor het on-premises AD-forest. Het probleem wordt veroorzaakt doordat Azure AD Connect wizard geen volledig gekwalificeerde referenties opgeeft bij het maken van de beveiligings contexten die vereist zijn voor Kerberos-verificatie. Dit zorgt ervoor dat Kerberos-verificatie mislukt en de wizard Azure AD Connect om terug te vallen op het gebruik van NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Opgeloste problemen
* Er is een probleem opgelost waarbij een nieuwe synchronisatie regel niet kan worden gemaakt als het label kenmerk niet is ingevuld.
* Er is een probleem opgelost dat ertoe heeft geleid Azure AD Connect verbinding te maken met on-premises AD voor wachtwoord synchronisatie met NTLM, zelfs als Kerberos beschikbaar is. Dit probleem doet zich voor als de on-premises AD-topologie een of meer domein controllers heeft die zijn hersteld met een back-up.
* Er is een probleem opgelost dat ertoe heeft geleid dat de stappen voor volledige synchronisatie onnodig na de upgrade optreden. Over het algemeen is het uitvoeren van volledige synchronisatie stappen vereist na de upgrade als er wijzigingen zijn in de out-of-Box-synchronisatie regels. Het probleem is veroorzaakt door een fout in de logica van de detectie van wijzigingen die een wijziging onjuist heeft gedetecteerd bij het aankomen van een synchronisatie regel expressie met teken voor nieuwe regel. Nieuwe-regel tekens worden ingevoegd in de expressie voor synchronisatie regels om de Lees baarheid te verbeteren.
* Er is een probleem opgelost dat ertoe kan leiden dat de Azure AD Connect-server niet goed werkt na een automatische upgrade. Dit probleem is van toepassing op Azure AD Connect servers met versie 1.1.443.0 (of eerder). Raadpleeg voor meer informatie over het probleem artikel [Azure AD Connect werkt niet correct na een automatische upgrade](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Er is een probleem opgelost dat ertoe kan leiden dat een automatische upgrade elke 5 minuten opnieuw wordt uitgevoerd wanneer er fouten optreden. Met deze oplossing wordt automatische upgrade pogingen met exponentiële back-ups uitgevoerd wanneer er fouten optreden.
* Er is een probleem opgelost waarbij de wachtwoord synchronisatie gebeurtenis 611 onjuist wordt weer gegeven in gebeurtenis logboeken van Windows-toepassingen als **informatief** in plaats van **fout**. Gebeurtenis 611 wordt gegenereerd wanneer met wachtwoord synchronisatie een probleem wordt aangetroffen. 
* Er is een probleem opgelost in de wizard Azure AD Connect waarmee de functie voor het terugschrijven van groepen kan worden ingeschakeld zonder dat een organisatie-eenheid is geselecteerd die vereist is voor het terugschrijven van

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Er is een probleemoplossings taak toegevoegd aan Azure AD Connect wizard onder extra taken. Klanten kunnen deze taak gebruiken om problemen met wachtwoord synchronisatie op te lossen en algemene diagnostische gegevens te verzamelen. In de toekomst wordt de taak voor het oplossen van problemen uitgebreid met andere problemen met betrekking tot Directory synchronisatie.
* Azure AD Connect ondersteunt nu een nieuwe installatie modus met de naam **bestaande Data Base gebruiken**. Met deze installatie modus kunnen klanten Azure AD Connect installeren waarmee een bestaande ADSync-data base wordt opgegeven. Raadpleeg het artikel [een bestaande Data Base gebruiken](how-to-connect-install-existing-database.md)voor meer informatie over deze functie.
* Voor een betere beveiliging Azure AD Connect nu standaard TLS 1.2 gebruiken om verbinding te maken met Azure AD voor adreslijst synchronisatie. Voorheen was de standaard TLS 1.0.
* Wanneer Azure AD Connect-wachtwoord synchronisatie agent wordt gestart, wordt geprobeerd verbinding te maken met een bekend Azure AD-eind punt voor wachtwoord synchronisatie. Wanneer de verbinding is geslaagd, wordt deze omgeleid naar een regio-specifiek eind punt. Voorheen slaat de wachtwoord synchronisatie agent het regio-specifieke eind punt op in het cache geheugen totdat het opnieuw wordt gestart. Nu wordt de cache door de agent gewist en wordt opnieuw geprobeerd met het bekende eind punt als er een verbindings probleem met het regio-specifieke eind punt wordt aangetroffen. Met deze wijziging zorgt u ervoor dat wachtwoord synchronisatie een failover naar een ander regio-specifiek eind punt kan hebben wanneer het in de cache opgeslagen regio-specifieke eind punt niet meer beschikbaar is.
* Voor het synchroniseren van wijzigingen vanuit een on-premises AD-forest is een AD DS-account vereist. U kunt (i) zelf het AD DS-account maken en de referentie voor Azure AD Connect opgeven, of (II) Geef de referenties van een ondernemings beheerder op en laat Azure AD Connect het AD DS-account voor u maken. Voorheen, (i) is de standaard optie in de wizard Azure AD Connect. Nu is (II) de standaard optie.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health (Engelstalig)

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Er is ondersteuning toegevoegd voor Microsoft Azure Government Cloud en Microsoft Cloud Duitsland.

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issues"></a>Opgeloste problemen
* De Initialize-ADSyncNGCKeysWriteBack-cmdlet in de AD prep Power shell-module heeft onjuiste Acl's toegepast op de container voor apparaatregistratie en zou daarom alleen bestaande machtigingen overnemen.  Dit is bijgewerkt, zodat het synchronisatie service account de juiste machtigingen heeft.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* De Azure AD Connect ADFS-aanmeld taak controleren is bijgewerkt, zodat de aanmelding bij micro soft online en niet alleen het token ophalen van ADFS wordt gecontroleerd.
* Bij het instellen van een nieuwe AD FS-farm met Azure AD Connect, is de pagina die wordt gevraagd om ADFS-referenties te verplaatsen, zodat deze nu plaatsvindt voordat de gebruiker wordt gevraagd om ADFS-en WAP-servers.  Hiermee kan Azure AD Connect controleren of het opgegeven account de juiste machtigingen heeft.
* Tijdens Azure AD Connect upgrade mislukt de upgrade niet meer als de ADFS Azure AD-vertrouwens relatie niet kan worden bijgewerkt.  Als dat gebeurt, wordt de gebruiker een geschikt waarschuwings bericht weer gegeven en moet de vertrouwens relatie opnieuw worden ingesteld via de extra taak Azure AD Connect.

### <a name="seamless-single-sign-on"></a>Naadloze single Sign-On
#### <a name="fixed-issues"></a>Opgeloste problemen
* Er is een probleem opgelost waardoor Azure AD Connect wizard een fout retourneert als u [naadloze eenmalige aanmelding](how-to-connect-sso.md)probeert in te scha kelen. Het fout bericht is *configuratie van Microsoft Azure AD Connect authentication agent is mislukt.* Dit probleem is van invloed op bestaande klanten die de preview [-](how-to-connect-sso.md) versie van de verificatie agenten hand matig hebben geüpgraded op basis van de stappen die in dit [artikel](how-to-connect-pta-upgrade-preview-authentication-agents.md)worden beschreven.


## <a name="115610"></a>1.1.561.0
Status: juli 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost

* Er is een probleem opgelost waardoor de out-of-Box-synchronisatie regel naar de AD-User ImmutableId wordt verwijderd:

  * Het probleem treedt op wanneer Azure AD Connect wordt geüpgraded of wanneer de taak optie *synchronisatie configuratie* in de Azure AD Connect wizard bijwerken wordt gebruikt om de configuratie van Azure AD Connect-synchronisatie bij te werken.

  * Deze synchronisatie regel is van toepassing op klanten die de [functie MS-DS-ConsistencyGuid als bron-anker](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)hebben ingeschakeld. Deze functie is geïntroduceerd in versie 1.1.524.0 en na. Wanneer de synchronisatie regel wordt verwijderd, kan Azure AD Connect geen on-premises AD MS-DS-ConsistencyGuid-kenmerk meer invullen met de waarde van het kenmerk ObjectGuid. Er wordt niet voor komen dat nieuwe gebruikers worden ingericht in azure AD.

  * Met deze oplossing wordt ervoor gezorgd dat de synchronisatie regel tijdens de upgrade niet meer wordt verwijderd, of tijdens de configuratie wijziging, zolang de functie is ingeschakeld. Voor bestaande klanten die door dit probleem worden beïnvloed, zorgt de oplossing er ook voor dat de synchronisatie regel weer wordt toegevoegd na de upgrade naar deze versie van Azure AD Connect.

* Er is een probleem opgelost waardoor een prioriteits waarde van minder dan 100 wordt veroorzaakt door een niet-ingestelde synchronisatie regel:

  * In het algemeen zijn prioriteits waarden 0-99 gereserveerd voor aangepaste synchronisatie regels. Tijdens de upgrade worden de prioriteits waarden voor out-of-Box-synchronisatie regels bijgewerkt om wijzigingen in de synchronisatie regel aan te passen. Vanwege dit probleem kan de prioriteits waarde die kleiner is dan 100 worden toegewezen aan out-of-Box-synchronisatie regels.

  * De oplossing voor komt dat het probleem optreedt tijdens de upgrade. De prioriteits waarden worden echter niet hersteld voor bestaande klanten die van invloed zijn op het probleem. In de toekomst vindt u een afzonderlijke oplossing om de herstel bewerking uit te kunnen zetten.

* Er is een probleem opgelost waarbij het [filter scherm voor domein en organisatie-eenheid](how-to-connect-install-custom.md#domain-and-ou-filtering) in de wizard Azure AD Connect de optie *alle domeinen en organisatie-eenheden synchroniseren* wordt weer gegeven, zelfs als het filteren op basis van een organisatie is ingeschakeld.

*   Er is een probleem opgelost dat ertoe heeft geleid dat het [scherm mappartities configureren](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) in de Synchronization Service Manager een fout retourneert als op de knop *vernieuwen* wordt geklikt. Het fout bericht is *' er is een fout opgetreden bij het vernieuwen van domeinen: kan object van het type ' System. Collections. list matrix ' niet converteren naar type ' micro soft. Directory Services. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject '.* De fout treedt op wanneer er een nieuw AD-domein is toegevoegd aan een bestaand AD-forest en u probeert Azure AD Connect bij te werken met behulp van de knop Vernieuwen.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* De [functie voor automatische upgrades](how-to-connect-install-automatic-upgrade.md) is uitgebreid ter ondersteuning van klanten met de volgende configuraties:
  * U hebt de functie voor het terugschrijven van apparaten ingeschakeld.
  * U hebt de functie voor het terugschrijven van groepen ingeschakeld.
  * De installatie is geen snelle instellingen of een DirSync-upgrade.
  * U hebt meer dan 100.000 objecten in de tekst.
  * U maakt verbinding met meer dan één forest. Snelle installatie maakt alleen verbinding met één forest.
  * Het account van de AD-connector is niet de standaard MSOL_ account.
  * De server is ingesteld om te worden uitgevoerd in de faserings modus.
  * De functie voor het terugschrijven van gebruikers is ingeschakeld.

  >[!NOTE]
  >De bereik uitbreiding van de functie voor automatische upgrades is van invloed op klanten met Azure AD Connect build 1.1.105.0 en after. Als u niet wilt dat uw Azure AD Connect-server automatisch wordt bijgewerkt, moet u de volgende cmdlet uitvoeren op uw Azure AD Connect-server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Raadpleeg voor meer informatie over het in-en uitschakelen van automatische upgrade naar artikel [Azure AD Connect: automatische upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Status: wordt niet vrijgegeven. Wijzigingen in deze build zijn opgenomen in versie 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost

* Er is een probleem opgelost waardoor de out-of-Box-synchronisatie regel naar de AD-User ImmutableId wordt verwijderd wanneer filter configuratie op basis van een organisatie-eenheid wordt bijgewerkt. Deze synchronisatie regel is vereist voor de [functie MS-DS-ConsistencyGuid als bron anker](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Er is een probleem opgelost waarbij het [filter scherm voor domein en organisatie-eenheid](how-to-connect-install-custom.md#domain-and-ou-filtering) in de wizard Azure AD Connect de optie *alle domeinen en organisatie-eenheden synchroniseren* wordt weer gegeven, zelfs als het filteren op basis van een organisatie is ingeschakeld.

*   Er is een probleem opgelost dat ertoe heeft geleid dat het [scherm mappartities configureren](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) in de Synchronization Service Manager een fout retourneert als op de knop *vernieuwen* wordt geklikt. Het fout bericht is *' er is een fout opgetreden bij het vernieuwen van domeinen: kan object van het type ' System. Collections. list matrix ' niet converteren naar type ' micro soft. Directory Services. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject '.* De fout treedt op wanneer er een nieuw AD-domein is toegevoegd aan een bestaand AD-forest en u probeert Azure AD Connect bij te werken met behulp van de knop Vernieuwen.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* De [functie voor automatische upgrades](how-to-connect-install-automatic-upgrade.md) is uitgebreid ter ondersteuning van klanten met de volgende configuraties:
  * U hebt de functie voor het terugschrijven van apparaten ingeschakeld.
  * U hebt de functie voor het terugschrijven van groepen ingeschakeld.
  * De installatie is geen snelle instellingen of een DirSync-upgrade.
  * U hebt meer dan 100.000 objecten in de tekst.
  * U maakt verbinding met meer dan één forest. Snelle installatie maakt alleen verbinding met één forest.
  * Het account van de AD-connector is niet de standaard MSOL_ account.
  * De server is ingesteld om te worden uitgevoerd in de faserings modus.
  * De functie voor het terugschrijven van gebruikers is ingeschakeld.

  >[!NOTE]
  >De bereik uitbreiding van de functie voor automatische upgrades is van invloed op klanten met Azure AD Connect build 1.1.105.0 en after. Als u niet wilt dat uw Azure AD Connect-server automatisch wordt bijgewerkt, moet u de volgende cmdlet uitvoeren op uw Azure AD Connect-server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Raadpleeg voor meer informatie over het in-en uitschakelen van automatische upgrade naar artikel [Azure AD Connect: automatische upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Status: juli 2017

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie voor automatische upgrade van Azure AD Connect.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost
* Er is een probleem opgelost met de Initialize-ADSyncDomainJoinedComputerSync-cmdlet waarmee het geverifieerde domein dat is geconfigureerd op het bestaande Service Connection Point-object, moet worden gewijzigd, zelfs als het nog een geldig domein is. Dit probleem treedt op wanneer uw Azure AD-Tenant meer dan één geverifieerde domeinen heeft die kan worden gebruikt voor het configureren van het service verbindings punt.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Wacht woord terugschrijven is nu beschikbaar voor Preview met Microsoft Azure Government Cloud en Microsoft Cloud Duitsland. Raadpleeg voor meer informatie over de ondersteuning van Azure AD Connect voor de verschillende service-exemplaren artikel [Azure AD Connect: speciale overwegingen voor exemplaren](reference-connect-instances.md).

* De cmdlet Initialize-ADSyncDomainJoinedComputerSync heeft nu een nieuwe optionele para meter met de naam AzureADDomain. Met deze para meter kunt u opgeven welk geverifieerd domein moet worden gebruikt voor het configureren van het service verbindings punt.

### <a name="pass-through-authentication"></a>Pass-through-verificatie

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* De naam van de agent die vereist is voor Pass-Through-verificatie, is gewijzigd van *Microsoft Azure AD Application proxy-connector* naar *Microsoft Azure AD Connect authentication agent*.

* Als u Pass-Through-verificatie inschakelt, wordt de hash-synchronisatie van wacht woorden niet meer standaard ingeschakeld.


## <a name="115530"></a>1.1.553.0
Status: juni 2017

> [!IMPORTANT]
> Er zijn schema-en synchronisatie regel wijzigingen die in deze build zijn geïntroduceerd. Azure AD Connect-synchronisatie service worden de stappen voor volledige import en volledige synchronisatie geactiveerd na de upgrade. Details van de wijzigingen worden hieronder beschreven. Als u de stappen voor volledige import bewerking en volledige synchronisatie tijdelijk wilt uitstellen na de upgrade, raadpleegt u [het artikel volledige synchronisatie uitstellen na de upgrade](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Bekend probleem
* Er is een probleem dat van invloed is op klanten die gebruikmaken van [filtering op basis van een organisatie-eenheid](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) met Azure AD Connect synchronisatie. Wanneer u navigeert naar de [pagina domein en OE filteren](how-to-connect-install-custom.md#domain-and-ou-filtering) in de wizard Azure AD Connect, wordt het volgende gedrag verwacht:
  * Als filteren op basis van een organisatie-eenheid is ingeschakeld, wordt de optie **geselecteerde domeinen en organisatie-eenheden synchroniseren** geselecteerd.
  * Anders is de optie **alle domeinen en organisatie-eenheden synchroniseren** geselecteerd.

Het probleem dat zich voordoet, is dat de **optie alle domeinen en organisatie-eenheden synchroniseren** altijd is geselecteerd wanneer u de wizard uitvoert.  Dit gebeurt zelfs als het filteren op basis van een organisatie-eenheid eerder is geconfigureerd. Voordat u wijzigingen in de configuratie van Azure AD Connect opslaat, moet u ervoor zorgen dat de **optie geselecteerde domeinen en organisatie-eenheden synchroniseren is geselecteerd** en bevestig dat alle organisatie-eenheden die moeten worden gesynchroniseerd, opnieuw worden ingeschakeld. Anders wordt filteren op basis van de organisatie-eenheid uitgeschakeld.

#### <a name="fixed-issues"></a>Opgeloste problemen

* Er is een probleem opgelost met het terugschrijven van wacht woorden, waarmee een Azure AD-beheerder het wacht woord van een on-premises AD-privileged gebruikers account opnieuw kan instellen. Het probleem treedt op wanneer aan Azure AD Connect de machtiging wacht woord opnieuw instellen wordt verleend via het bevoegde account. Het probleem wordt opgelost in deze versie van Azure AD Connect door niet toe te staan dat een Azure AD-beheerder het wacht woord van een wille keurige on-premises AD-privileged user account opnieuw instelt, tenzij de beheerder de eigenaar van dat account is. Raadpleeg [Security advisor 4033453](/security-updates/SecurityAdvisories/2017/4033453)voor meer informatie.

* Er is een probleem opgelost dat betrekking heeft op de functie [MS-DS-ConsistencyGuid als bron-anker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) waarbij Azure AD Connect niet terugschrijven naar on-premises AD MS-DS-ConsistencyGuid-kenmerk. Het probleem treedt op wanneer er meerdere on-premises AD-forests zijn toegevoegd aan Azure AD Connect en de *optie gebruikers identiteiten bestaan in meerdere mappen* is geselecteerd. Wanneer deze configuratie wordt gebruikt, vullen de resulterende synchronisatie regels niet het kenmerk sourceAnchorBinary in de tekst in. Het kenmerk sourceAnchorBinary wordt gebruikt als het bron kenmerk voor het kenmerk MS-DS-ConsistencyGuid. Als gevolg hiervan wordt write-back naar het kenmerk MS-DSConsistencyGuid niet uitgevoerd. Om het probleem op te lossen, zijn de volgende synchronisatie regels bijgewerkt om ervoor te zorgen dat het kenmerk sourceAnchorBinary in de tekst altijd wordt ingevuld:
  * In van AD-InetOrgPerson AccountEnabled.xml
  * In van AD-InetOrgPerson Common.xml
  * In van AccountEnabled.xml van AD-gebruiker
  * In van Common.xml van AD-gebruiker
  * In van AD-gebruiker toevoegen SOAInAAD.xml

* Voorheen, zelfs als de functie [MS-DS-ConsistencyGuid als bron anker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) niet is ingeschakeld, wordt de synchronisatie regel ' out to AD-gebruiker ImmutableId ' nog steeds toegevoegd aan Azure AD Connect. Het effect is onschadelijk en leidt er niet toe dat er een write-back van het MS-DS-ConsistencyGuid-kenmerk plaatsvindt. Om Verwar ring te voor komen, is logica toegevoegd om ervoor te zorgen dat de synchronisatie regel alleen wordt toegevoegd wanneer de functie is ingeschakeld.

* Er is een probleem opgelost dat ertoe leidde dat wachtwoord-hash-synchronisatie mislukt met fout gebeurtenis 611. Dit probleem treedt op nadat een of meer domein controllers zijn verwijderd uit on-premises AD. Aan het einde van elke wachtwoord synchronisatie cyclus bevat de synchronisatie cookie die is uitgegeven door on-premises AD, aanroep-Id's van de verwijderde domein controllers met USN (Update Sequence Number) met de waarde 0. Wachtwoord synchronisatie beheer kan synchronisatie cookie met de USN-waarde 0 niet persistent maken en mislukt met fout gebeurtenis 611. Tijdens de volgende synchronisatie cyclus gebruikt de wachtwoord synchronisatie beheerder de laatste permanente synchronisatie cookie die geen USN-waarde van 0 bevat. Hierdoor worden de wijzigingen in het wacht woord opnieuw gesynchroniseerd. Met deze oplossing blijft de synchronisatie cookie goed door de beheerder voor wachtwoord synchronisatie.

* Voorheen, zelfs als automatische upgrade is uitgeschakeld met behulp van de cmdlet Set-ADSyncAutoUpgrade, blijft het automatische upgrade proces regel matig controleren op upgrade en is het gedownloade installatie programma afhankelijk van de uitschakeling. Met deze oplossing wordt het automatische upgrade proces niet meer periodiek gecontroleerd op upgrades. De oplossing wordt automatisch toegepast wanneer het upgrade-installatie programma voor deze Azure AD Connect versie één keer wordt uitgevoerd.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Voorheen was de functie [MS-DS-ConsistencyGuid als bron-anker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) alleen beschikbaar voor nieuwe implementaties. Nu is het beschikbaar voor bestaande implementaties. Met name:
  * Als u de functie wilt openen, start u de wizard Azure AD Connect en kiest u de optie *bron anker bijwerken* .
  * Deze optie is alleen zichtbaar voor bestaande implementaties die gebruikmaken van objectGuid als source Anchor-kenmerk.
  * Bij het configureren van de optie valideert de wizard de status van het kenmerk MS-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd voor een gebruikers object in de Directory, gebruikt de wizard de MS-DS-ConsistencyGuid als het kenmerk source Anchor. Als het kenmerk is geconfigureerd voor een of meer gebruikers objecten in de Directory, sluit de wizard het kenmerk dat wordt gebruikt door andere toepassingen en is het niet geschikt als source Anchor-kenmerk en staat het bron anker niet toe om door te gaan. Als u zeker weet dat het kenmerk niet door bestaande toepassingen wordt gebruikt, moet u contact opnemen met de ondersteuning voor meer informatie over het onderdrukken van de fout.

* Voor het kenmerk **userCertificate** op object-objecten zoekt Azure AD Connect nu naar certificaat waarden [die zijn vereist voor het verbinden van apparaten die aan een domein zijn gekoppeld met Azure AD voor Windows 10-ervaring](../devices/hybrid-azuread-join-plan.md) en filtert de rest voordat u synchroniseert met Azure AD. Om dit gedrag in te scha kelen, is de out-of-Box-synchronisatie regel "out to AAD-SOAInAD toevoegen" bijgewerkt.

* Azure AD Connect ondersteunt nu write-back van het kenmerk **cloudPublicDelegates** van Exchange Online naar een on-premises AD **publicDelegates** -kenmerk. Hierdoor kan het scenario met een Exchange Online-postvak SendOnBehalfTo rechten krijgen voor gebruikers met een on-premises Exchange-postvak. Ter ondersteuning van deze functie is een nieuwe out-of-Box-synchronisatie regel (out-to-AD-User Exchange Hybrid PublicDelegates write) toegevoegd. Deze synchronisatie regel wordt alleen toegevoegd aan Azure AD Connect wanneer de hybride functie van Exchange is ingeschakeld.

* Azure AD Connect ondersteunt nu het synchroniseren van het kenmerk **altRecipient** vanuit Azure AD. Ter ondersteuning van deze wijziging zijn de volgende out-of-Box-synchronisatie regels bijgewerkt, zodat deze de vereiste kenmerk stroom bevat:
  * In van AD: gebruikers uitwisseling
  * Naar AAD: gebruiker ExchangeOnline

* Het kenmerk **cloudSOAExchMailbox** in het omgekeerde geeft aan of een bepaalde gebruiker een Exchange Online-postvak heeft of niet. De definitie ervan is bijgewerkt met aanvullende Exchange Online-RecipientDisplayTypes als dergelijke apparatuur en Vergader Zaal post vakken. Om deze wijziging in te scha kelen, is de definitie van het kenmerk cloudSOAExchMailbox, dat wordt gevonden in de out-of-Box-synchronisatie regel in van AAD-User Exchange Hybrid, bijgewerkt van:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... naar het volgende:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* De volgende set X509Certificate2-compatibele functies is toegevoegd voor het maken van synchronisatie regel expressies voor het afhandelen van certificaat waarden in het kenmerk userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Selecteer
  * CertKeyAlgorithmParams
  * CertHashString
  * Waar
  * With

* De volgende schema wijzigingen zijn geïntroduceerd zodat klanten aangepaste synchronisatie regels kunnen maken voor flow sAMAccountName, domainNetBios en domainFQDN voor groeps objecten, evenals DN voor gebruikers objecten:

  * De volgende kenmerken zijn toegevoegd aan het MV-schema:
    * Groep: AccountName
    * Groep: domainNetBios
    * Groep: domainFQDN
    * Persoon: Distinguished name

  * De volgende kenmerken zijn toegevoegd aan het Azure AD-connector schema:
    * Groep: OnPremisesSamAccountName
    * Groep: NetBIOS-naam
    * Groep: DNS-
    * Gebruiker: OnPremisesDistinguishedName

* Het ADSyncDomainJoinedComputerSync-cmdlet script heeft nu een nieuwe optionele para meter met de naam AzureEnvironment. De para meter wordt gebruikt om op te geven in welke regio de bijbehorende Azure Active Directory Tenant wordt gehost. Geldige waarden zijn:
  * Cloud (standaard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* De editor voor synchronisatie regels is bijgewerkt om samen voegen (in plaats van inrichten) als de standaard waarde van het koppelings type tijdens het maken van de synchronisatie regel te gebruiken.

### <a name="ad-fs-management"></a>AD FS beheer

#### <a name="issues-fixed"></a>Problemen opgelost

* De volgende Url's zijn nieuwe WS-Federation-eind punten die worden geïntroduceerd door Azure AD om de tolerantie te verbeteren ten opzichte van de onderbreking van de verificatie en worden toegevoegd aan de configuratie van een on-AD FS premises Relying Party vertrouwens relatie:
  * https: \/ /Ests.login.microsoftonline.com/login.SRF
  * https: \/ /stamp2.login.microsoftonline.com/login.SRF
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Er is een probleem opgelost waardoor AD FS onjuiste claim waarde voor IssuerID genereren. Het probleem treedt op als er meerdere geverifieerde domeinen in de Azure AD-Tenant zijn en het domein achtervoegsel van het kenmerk userPrincipalName dat wordt gebruikt voor het genereren van de IssuerID-claim ten minste 3 niveaus diep is (bijvoorbeeld johndoe@us.contoso.com ). Het probleem wordt opgelost door de regex bij te werken die wordt gebruikt door de claim regels.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Voorheen kan de AD FS-functie voor certificaat beheer van Azure AD Connect alleen worden gebruikt met ADFS-Farms die worden beheerd via Azure AD Connect. Nu kunt u de functie gebruiken met ADFS-Farms die niet worden beheerd met Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Uitgebracht: mei 2017

> [!IMPORTANT]
> Er zijn schema-en synchronisatie regel wijzigingen die in deze build zijn geïntroduceerd. Azure AD Connect-synchronisatie service worden de stappen voor volledige import en volledige synchronisatie geactiveerd na de upgrade. Details van de wijzigingen worden hieronder beschreven.
>
>
**Opgeloste problemen:**

Azure AD Connect-synchronisatie

* Er is een probleem opgelost dat ervoor zorgt dat automatische upgrades op de Azure AD Connect-server worden uitgevoerd, zelfs als de klant de functie heeft uitgeschakeld met de Set-ADSyncAutoUpgrade-cmdlet. Met deze oplossing controleert het automatische upgrade proces op de server regel matig op upgrade, maar het gedownloade installatie programma voldoet aan de automatische upgrade configuratie.
* Tijdens het DirSync-in-place upgrade maakt Azure AD Connect een Azure AD-service account dat door de Azure AD-connector moet worden gebruikt om te synchroniseren met Azure AD. Nadat het account is gemaakt, wordt Azure AD Connect met behulp van het account geverifieerd bij Azure AD. Soms mislukt de verificatie vanwege tijdelijke problemen, waardoor DirSync in-place upgrade mislukt met fout *' er is een fout opgetreden tijdens het uitvoeren van de Aad Sync taak: AADSTS50034: om u aan te melden bij deze toepassing, moet het account worden toegevoegd aan de xxx.onmicrosoft.com-map.* Azure AD Connect probeert nu de verificatie stap opnieuw uit te proberen om de tolerantie van de upgrade van DirSync te verbeteren.
* Er is een probleem opgetreden met Build 443 waardoor DirSync in-place upgrade slagen, maar het uitvoeren van profielen die vereist zijn voor Directory synchronisatie, worden niet gemaakt. Retouch Logic is opgenomen in deze build van Azure AD Connect. Wanneer de klant upgrades naar deze build uitvoert, detecteert Azure AD Connect ontbrekende run-profielen en maakt ze deze.
* Er is een probleem opgelost waardoor het wachtwoord synchronisatie proces niet kan worden gestart met gebeurtenis-ID 6900 en fout *' een item met dezelfde sleutel is al toegevoegd '*. Dit probleem treedt op als u de OE-filter configuratie bijwerkt met AD-configuratie partitie. Om dit probleem op te lossen, synchroniseert het wachtwoord synchronisatie proces nu alleen wachtwoord wijzigingen van AD-domein partities. Niet-domein partities, zoals configuratie partitie, worden overgeslagen.
* Tijdens de snelle installatie maakt Azure AD Connect een on-premises AD DS-account dat door de AD-connector moet worden gebruikt om te communiceren met on-premises AD. Voorheen wordt het account gemaakt met de vlag PASSWD_NOTREQD ingesteld op het kenmerk User-Account-Control en wordt een wille keurig wacht woord ingesteld voor het account. Azure AD Connect wordt de vlag PASSWD_NOTREQD nu expliciet verwijderd nadat het wacht woord is ingesteld voor het account.
* Er is een probleem opgelost waardoor DirSync-upgrade mislukt met fout ' er is *een impasse opgetreden in SQL Server waarmee een toepassings vergrendeling wordt opgehaald '* wanneer het kenmerk mailnickname is gevonden in het on-premises AD-schema, maar niet is gebonden aan de AD-gebruikers object klasse.
* Er is een probleem opgelost dat ervoor zorgt dat de functie voor het terugschrijven van apparaten automatisch wordt uitgeschakeld wanneer een beheerder Azure AD Connect synchronisatie configuratie met behulp van Azure AD Connect wizard bijwerkt. Dit probleem wordt veroorzaakt door de wizard voor het uitvoeren van een controle op vereisten voor de bestaande terugschrijf configuratie van het apparaat in on-premises AD en de controle mislukt. De oplossing is de controle overs Laan als het terugschrijven van apparaten al eerder is ingeschakeld.
* Als u het filteren van OE wilt configureren, kunt u de Azure AD Connect wizard of de Synchronization Service Manager gebruiken. Als u eerder de wizard Azure AD Connect gebruikt voor het configureren van de OE-filtering, worden nieuwe organisatie-eenheden die daarna zijn gemaakt, opgenomen voor Directory synchronisatie. Als u niet wilt dat er nieuwe organisatie-eenheden worden opgenomen, moet u de OE-filtering configureren met behulp van de Synchronization Service Manager. Nu kunt u hetzelfde gedrag doen met behulp van Azure AD Connect wizard.
* Er is een probleem opgelost dat ervoor zorgt dat opgeslagen procedures die door Azure AD Connect worden vereist, worden gemaakt onder het schema van de installatie beheerder in plaats van onder het dbo-schema.
* Er is een probleem opgelost waardoor het TrackingId-kenmerk dat door Azure AD wordt geretourneerd, wordt wegge laten in de gebeurtenis logboeken van Azure AD Connect server. Het probleem treedt op als Azure AD Connect een omleidings bericht ontvangt van Azure AD en Azure AD Connect geen verbinding kan maken met het gegeven eind punt. De TrackingId wordt gebruikt door ondersteunings technici tijdens het oplossen van problemen met logboeken aan de service zijde.
* Wanneer Azure AD Connect een LargeObject-fout ontvangt van Azure AD, Azure AD Connect een gebeurtenis gegenereerd met de gebeurtenis-Event-code 6941 en *het bericht ' het ingerichte object is te groot. Het aantal kenmerk waarden voor dit object bijsnijden. "* Terzelfder tijd genereert Azure AD Connect ook een misleidende gebeurtenis met 6900 gebeurtenis-ProvisionRetryException en bericht *' micro soft. online. samen werking. ongebruikte: kan niet communiceren met de Windows Azure Active Directory-service. '* Om Verwar ring te minimaliseren, wordt Azure AD Connect niet langer de laatste gebeurtenis gegenereerd wanneer er een LargeObject-fout wordt ontvangen.
* Er is een probleem opgelost waardoor het Synchronization Service Manager niet meer reageert wanneer u de configuratie voor de algemene LDAP-connector probeert bij te werken.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Wijzigingen in regels synchroniseren: de volgende wijzigingen in de synchronisatie regel zijn geïmplementeerd:
  * De standaard instelling voor de synchronisatie regel is bijgewerkt, zodat de kenmerken **userCertificate** en **userSMIMECertificate** niet worden geëxporteerd als de kenmerken meer dan 15 waarden bevatten.
  * AD-kenmerk- **employeeID** en **msExchBypassModerationLink** zijn nu opgenomen in de standaard set met synchronisatie regels.
  * ADVERTENTIE kenmerk **foto** is verwijderd uit de standaard set voor synchronisatie regels.
  * **PreferredDataLocation** toegevoegd aan het omgekeerde schema en het Azure AD-connector schema. Klanten die beide kenmerken in azure AD willen bijwerken, kunnen hiervoor aangepaste synchronisatie regels implementeren. 
  * **User type** toegevoegd aan het omgekeerde schema en het Azure AD-connector schema. Klanten die beide kenmerken in azure AD willen bijwerken, kunnen hiervoor aangepaste synchronisatie regels implementeren.

* Azure AD Connect wordt nu automatisch het kenmerk ConsistencyGuid voor het kenmerk bron anker ingeschakeld voor on-premises AD-objecten. Verder vult Azure AD Connect het kenmerk ConsistencyGuid in met de kenmerk waarde objectGuid als dit leeg is. Deze functie is alleen van toepassing op nieuwe implementatie. Raadpleeg voor meer informatie over deze functie de sectie artikel [Azure AD Connect: ontwerp concepten-MS-DS-ConsistencyGuid gebruiken als source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Er is een nieuwe cmdlet voor het oplossen van problemen Invoke-ADSyncDiagnostics toegevoegd om te helpen bij het vaststellen van problemen met de wachtwoord-hash-synchronisatie. Raadpleeg voor meer informatie over het gebruik van de cmdlet het artikel [problemen met wachtwoord-hash-synchronisatie met Azure AD Connect synchronisatie oplossen](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect ondersteunt nu het synchroniseren van Mail-Enabled open bare map-objecten van on-premises AD naar Azure AD. U kunt de functie inschakelen met behulp van Azure AD Connect wizard onder optionele functies. Raadpleeg voor meer informatie over deze functie, artikel [Office 365 op basis van een rand ondersteuning voor open bare mappen die zijn ingeschakeld voor on-premises e-mail](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218).
* Azure AD Connect moet een AD DS-account zijn om te synchroniseren vanuit on-premises AD. Als u eerder Azure AD Connect hebt geïnstalleerd met behulp van de Express-modus, kunt u de referenties van een ondernemings Administrator-account opgeven en Azure AD Connect het vereiste AD DS account maken. Voor een aangepaste installatie en het toevoegen van forests aan een bestaande implementatie, moet u echter het AD DS-account opgeven. U hebt nu ook de mogelijkheid om de referenties van een ondernemings Administrator-account op te geven tijdens een aangepaste installatie. u kunt Azure AD Connect het AD DS account maken dat vereist is.
* Azure AD Connect ondersteunt nu SQL AOA. U moet SQL AOA inschakelen voordat u Azure AD Connect installeert. Tijdens de installatie detecteert Azure AD Connect of het gegeven SQL-exemplaar is ingeschakeld voor SQL AOA of niet. Als SQL-AOA is ingeschakeld, Azure AD Connect meer cijfers uit als SQL AOA is geconfigureerd voor het gebruik van synchrone replicatie of asynchrone replicatie. Bij het instellen van de listener voor de beschikbaarheids groep wordt aanbevolen de eigenschap RegisterAllProvidersIP in te stellen op 0. Deze aanbeveling is omdat Azure AD Connect momenteel gebruikt SQL Native Client om verbinding te maken met SQL en SQL Native Client het gebruik van de eigenschap MultiSubNetFailover niet ondersteunt.
* Als u LocalDB gebruikt als de Data Base voor uw Azure AD Connect-server en de maximale grootte van 10 GB heeft bereikt, wordt de synchronisatie service niet meer gestart. Voorheen moet u de ShrinkDatabase-bewerking uitvoeren op de LocalDB om voldoende DB-ruimte vrij te maken om de synchronisatie service te starten. Daarna kunt u de Synchronization Service Manager voor het verwijderen van de uitvoerings geschiedenis om meer DB-ruimte vrij te maken. U kunt nu Start-ADSyncPurgeRunHistory-cmdlet gebruiken om de gegevens van de uitvoerings geschiedenis uit LocalDB te verwijderen om DB-ruimte vrij te maken. Verder ondersteunt deze cmdlet een offline modus (door de para meter-offline op te geven) die kan worden gebruikt wanneer de synchronisatie service niet wordt uitgevoerd. Opmerking: de offline modus kan alleen worden gebruikt als de synchronisatie service niet wordt uitgevoerd en de gebruikte data base LocalDB is.
* Om de benodigde opslag ruimte te verminderen, worden met Azure AD Connect nu de details van de synchronisatie fouten gecomprimeerd voordat ze worden opgeslagen in LocalDB-en SQL-data bases. Wanneer u een upgrade uitvoert van een oudere versie van Azure AD Connect naar deze versie, voert Azure AD Connect een eenmalige compressie uit op bestaande synchronisatie fout gegevens.
* U moet, na het bijwerken van de OE-filter configuratie, de volledige import bewerking hand matig uitvoeren om ervoor te zorgen dat bestaande objecten goed worden opgenomen of uitgesloten van Directory synchronisatie. Nu Azure AD Connect de volledige import automatisch geactiveerd tijdens de volgende synchronisatie cyclus. Verder wordt volledige import alleen toegepast op de AD-connectors waarop de update van toepassing is. Opmerking: deze verbetering is van toepassing op OE-filter updates die zijn gemaakt met behulp van de Azure AD Connect wizard. De update is niet van toepassing op de OE-filtering die is gemaakt met behulp van de Synchronization Service Manager.
* Voorheen ondersteunt filteren op basis van een groep alleen gebruikers, groepen en objecten voor contact personen. Groeps filtering biedt nu ook ondersteuning voor computer objecten.
* Voorheen kunt u connector-ruimte gegevens verwijderen zonder Azure AD Connect synchronisatie planner uit te scha kelen. De Synchronization Service Manager blokkeert nu het verwijderen van de gegevens van de connector ruimte als wordt gedetecteerd dat de scheduler is ingeschakeld. Daarnaast wordt er een waarschuwing weer gegeven om klanten te informeren over mogelijk gegevens verlies als de gegevens over de connector ruimte worden verwijderd.
* U moet de Power shell-transcriptie voor de Azure AD Connect wizard voor een juiste uitvoering uitschakelen. Dit probleem is gedeeltelijk opgelost. U kunt Power shell transcriptie inschakelen als u Azure AD Connect wizard gebruikt voor het beheren van de synchronisatie configuratie. U moet Power shell transcriptie uitschakelen als u Azure AD Connect wizard gebruikt voor het beheren van de ADFS-configuratie.



## <a name="114860"></a>1.1.486.0
Uitgebracht: april 2017

**Opgeloste problemen:**
* Het probleem opgelost waarbij Azure AD Connect niet correct wordt geïnstalleerd op een gelokaliseerde versie van Windows Server.

## <a name="114840"></a>1.1.484.0
Uitgebracht: april 2017

**Bekende problemen:**

* Deze versie van Azure AD Connect wordt niet correct geïnstalleerd als aan de volgende voor waarden wordt voldaan:
   1. U voert DirSync in-place upgrade of een nieuwe installatie van Azure AD Connect.
   2. U gebruikt een gelokaliseerde versie van Windows Server waarvan de naam van de ingebouwde Administrator-groep op de server niet ' Administrators ' is.
   3. U gebruikt de standaard SQL Server 2012 Express LocalDB geïnstalleerd met Azure AD Connect in plaats van uw eigen volledige SQL-waarde te bieden.

**Opgeloste problemen:**

Azure AD Connect-synchronisatie
* Er is een probleem opgelost waarbij de synchronisatie planner de volledige synchronisatie stap overs laat als een of meer connectors het uitvoerings profiel voor die synchronisatie stap ontbreken. U hebt bijvoorbeeld hand matig een connector toegevoegd met behulp van de Synchronization Service Manager zonder dat er een Delta-import profiel wordt gemaakt. Met deze oplossing wordt ervoor gezorgd dat de synchronisatie planner de Delta-import voor andere connectors blijft uitvoeren.
* Er is een probleem opgelost waarbij de synchronisatie service onmiddellijk stopt met het verwerken van een uitvoerings profiel wanneer er een probleem optreedt bij een van de uitvoerings stappen. Deze oplossing zorgt ervoor dat de synchronisatie service deze stap overs laat en de rest blijft verwerken. U hebt bijvoorbeeld een Delta-import profiel voor de AD-connector met meerdere uitvoerings stappen (één voor elk on-premises AD-domein). De synchronisatie service voert Delta-import uit met de andere AD-domeinen, zelfs als een van deze problemen heeft met de netwerk verbinding.
* Er is een probleem opgelost dat ervoor zorgt dat de update van de Azure AD-connector wordt overgeslagen tijdens de automatische upgrade.
* Er is een probleem opgelost dat ervoor zorgt dat Azure AD Connect onjuist bepaalt of de server een domein controller is tijdens de installatie, waardoor DirSync-upgrade mislukt.
* Er is een probleem opgelost waardoor DirSync in-place upgrade geen uitvoerings profiel voor de Azure AD-connector maakt.
* Er is een probleem opgelost waarbij de Synchronization Service Manager gebruikers interface niet meer reageert tijdens het configureren van de algemene LDAP-connector.

AD FS beheer
* Er is een probleem opgelost waarbij de wizard Azure AD Connect mislukt als het primaire knoop punt AD FS is verplaatst naar een andere server.

Desktop-SSO
* Er is een probleem opgelost in de wizard Azure AD Connect, waarbij de Sign-In scherm niet in staat is om de functie voor eenmalige aanmelding via het bureau blad in te scha kelen als u tijdens een nieuwe installatie wachtwoord synchronisatie hebt gekozen als uw Sign-In optie.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Azure AD Connect Sync ondersteunt nu het gebruik van het virtuele-service account, het beheerde service account en het door de groep beheerde service account als service account. Dit geldt alleen voor nieuwe installatie van Azure AD Connect. Bij de installatie van Azure AD Connect:
    * Azure AD Connect wizard maakt standaard een virtueel service-account en gebruikt dit als service account.
    * Als u op een domein controller installeert, wordt Azure AD Connect terugvallen op het vorige gedrag waarbij een domein gebruikers account wordt gemaakt en in plaats daarvan als service account wordt gebruikt.
    * U kunt het standaard gedrag negeren door een van de volgende opties te bieden:
      * Een beheerd service account voor een groep
      * Een beheerd service account
      * Een domein gebruikers account
      * Een lokaal gebruikers account
* Als u eerder een upgrade uitvoert naar een nieuwe build van Azure AD Connect met connectors update of synchronisatie regel wijzigingen, wordt door Azure AD Connect een volledige synchronisatie cyclus geactiveerd. Nu Azure AD Connect selectief de stap volledige import triggers alleen voor connectors met Update en volledige synchronisatie stap alleen voor connectors met synchronisatie regel wijzigingen.
* Voorheen is de drempel waarde voor het verwijderen van exports alleen van toepassing op uitvoer die wordt geactiveerd via de synchronisatie planner. Nu wordt de functie uitgebreid met uitvoer die hand matig door de klant wordt geactiveerd met behulp van de Synchronization Service Manager.
* De Azure AD-Tenant bevat een service configuratie waarmee wordt aangegeven of de functie voor wachtwoord synchronisatie is ingeschakeld voor uw Tenant of niet. Voorheen is het eenvoudig dat de service configuratie onjuist is geconfigureerd door Azure AD Connect wanneer u een actieve en een staging-server hebt. Azure AD Connect wordt nu geprobeerd de service configuratie alleen te laten overeenkomen met uw actieve Azure AD Connect server.
* De wizard Azure AD Connect detecteert en retourneert nu een waarschuwing als on-premises AD geen AD-prullenbak heeft ingeschakeld.
* Voorheen werd een time-out van het exporteren naar Azure AD als de gezamenlijke grootte van de objecten in de batch een bepaalde drempel waarde overschrijdt. Nu zal de synchronisatie service opnieuw proberen om de objecten in afzonderlijke, kleinere batches opnieuw te verzenden als het probleem wordt aangetroffen.
* De toepassing voor sleutel beheer van de synchronisatie service is verwijderd uit het menu Start van Windows. Het beheer van de versleutelings sleutel wordt nog steeds ondersteund via de opdracht regel interface met behulp van miiskmu.exe. Raadpleeg voor meer informatie over het beheren van de versleutelings sleutel [het artikel de versleutelings sleutel voor de Azure AD Connect synchronisatie afbreken](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key).
* Als u eerder het wacht woord voor Azure AD Connect synchronisatie service-account wijzigt, kan de synchronisatie service niet goed worden gestart totdat u de versleutelings sleutel hebt verlaten en het wacht woord voor het Azure AD Connect synchronisatie service-account opnieuw hebt geïnitialiseerd. Dit proces is nu niet meer nodig.

Desktop-SSO

* Voor de wizard Azure AD Connect is poort 9090 niet meer nodig om te worden geopend op het netwerk bij het configureren van Pass-Through-verificatie en desktop-SSO. Alleen poort 443 is vereist.

## <a name="114430"></a>1.1.443.0
Uitgebracht: maart 2017

**Opgeloste problemen:**

Azure AD Connect-synchronisatie
* Er is een probleem opgelost waardoor Azure AD Connect wizard mislukt als de weergave naam van de Azure AD-connector niet het eerste onmicrosoft.com-domein bevat dat is toegewezen aan de Azure AD-Tenant.
* Er is een probleem opgelost waardoor Azure AD Connect wizard mislukt tijdens het maken van verbinding met SQL database wanneer het wacht woord van het synchronisatie service account speciale tekens bevat zoals apostrof, dubbele punt en spatie.
* Er is een probleem opgelost waardoor de fout wordt veroorzaakt door de installatie kopie die afwijkt van de installatie kopie op een Azure AD Connect server in de faserings modus, nadat u een on-premises AD-object tijdelijk hebt uitgesloten van synchronisatie en vervolgens opnieuw hebt opgenomen voor synchronisatie.
* Er is een probleem opgelost waardoor de fout ' het object dat is gevonden door DN een spook item is ' op een Azure AD Connect server in de faserings modus, nadat u een on-premises AD-object tijdelijk hebt uitgesloten van synchroniseren en het vervolgens opnieuw hebt opgenomen voor synchronisatie.

AD FS beheer
* Er is een probleem opgelost waarbij Azure AD Connect wizard AD FS configuratie niet bijwerkt en de juiste claims instelt op de Relying Party-vertrouwens relatie nadat de alternatieve aanmeldings-ID is geconfigureerd.
* Er is een probleem opgelost waarbij Azure AD Connect wizard niet op de juiste wijze AD FS servers kan afhandelen waarvan de service accounts zijn geconfigureerd met de userPrincipalName-indeling in plaats van de sAMAccountName-indeling.

Pass-through-verificatie
* Er is een probleem opgelost waardoor Azure AD Connect wizard mislukt als Pass Through-verificatie is geselecteerd, maar de registratie van de connector mislukt.
* Er is een probleem opgelost waardoor Azure AD Connect wizard validatie controles overs laat wanneer de aanmeldings methode is geselecteerd wanneer de functie voor het inschakelen van een bureau blad-SSO is ingeschakeld.

Wachtwoord opnieuw instellen
* Er is een probleem opgelost waardoor de Azure Azure AD Connect-server niet opnieuw verbinding kan maken als de verbinding door een firewall of proxy is afgebroken.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Get-ADSyncScheduler cmdlet retourneert nu een nieuwe Boole-eigenschap met de naam SyncCycleInProgress. Als de geretourneerde waarde True is, betekent dit dat er een geplande synchronisatie cyclus wordt uitgevoerd.
* De doelmap voor het opslaan van Azure AD Connect installatie-en Setup-logboeken is verplaatst van%localappdata%\AADConnect naar%programdata%\AADConnect om de toegankelijkheid van de logboek bestanden te verbeteren.

AD FS beheer
* Er is ondersteuning toegevoegd voor het bijwerken van het TLS/SSL-certificaat van AD FS farm.
* Er is ondersteuning toegevoegd voor het beheer van AD FS 2016.
* U kunt nu een bestaande gMSA (door een groep beheerd service account) opgeven tijdens de installatie van AD FS.
* U kunt SHA-256 nu configureren als handtekening hash-algoritme voor Azure AD Relying Party-vertrouwens relatie.

Wachtwoord opnieuw instellen
* Verbeteringen geïntroduceerd zodat het product kan worden gebruikt in omgevingen met strengere firewall regels.
* Verbeterde betrouw baarheid van de verbinding met Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Uitgebracht: december 2016

**Probleem opgelost:**

* Er is een probleem opgelost waarbij de issuerID-claim regel voor Active Directory Federation Services (AD FS) ontbreekt in deze build.

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie voor automatische upgrade van Azure AD Connect.
## <a name="113710"></a>1.1.371.0
Uitgebracht: december 2016

**Bekend probleem:**

* De issuerID-claim regel voor AD FS ontbreekt in deze build. De claim regel issuerID is vereist als u meerdere domeinen federeren met Azure Active Directory (Azure AD). Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, wordt de bestaande issuerID-claim regel uit uw AD FS configuratie verwijderd als u een upgrade naar deze build uitvoert. U kunt het probleem omzeilen door de claim regel issuerID toe te voegen na de installatie/upgrade. Raadpleeg dit artikel over [meerdere domein ondersteuning voor federeren met Azure AD](how-to-connect-install-multiple-domains.md)voor meer informatie over het toevoegen van de issuerID claim regel.

**Probleem opgelost:**

* Als poort 9090 niet is geopend voor de uitgaande verbinding, mislukt de Azure AD Connect-installatie of-upgrade.

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie voor automatische upgrade van Azure AD Connect.
## <a name="113700"></a>1.1.370.0
Uitgebracht: december 2016

**Bekende problemen:**

* De issuerID-claim regel voor AD FS ontbreekt in deze build. De claim regel issuerID is vereist als u meerdere domeinen met Azure AD federeren. Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, wordt de bestaande issuerID-claim regel uit uw AD FS configuratie verwijderd als u een upgrade naar deze build uitvoert. U kunt het probleem omzeilen door de claim regel issuerID toe te voegen na de installatie/upgrade. Raadpleeg dit artikel over [meerdere domein ondersteuning voor federeren met Azure AD](how-to-connect-install-multiple-domains.md)voor meer informatie over het toevoegen van issuerID claim regels.
* Poort 9090 moet open-uitgaand zijn om de installatie te volt ooien.

**Nieuwe functies:**

* Pass-Through-verificatie (preview-versie).

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie voor automatische upgrade van Azure AD Connect.
## <a name="113430"></a>1.1.343.0
Uitgebracht: november 2016

**Bekend probleem:**

* De issuerID-claim regel voor AD FS ontbreekt in deze build. De claim regel issuerID is vereist als u meerdere domeinen met Azure AD federeren. Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, wordt de bestaande issuerID-claim regel uit uw AD FS configuratie verwijderd als u een upgrade naar deze build uitvoert. U kunt het probleem omzeilen door de claim regel issuerID toe te voegen na de installatie/upgrade. Raadpleeg dit artikel over [meerdere domein ondersteuning voor federeren met Azure AD](how-to-connect-install-multiple-domains.md)voor meer informatie over het toevoegen van issuerID claim regels.

**Opgeloste problemen:**

* Soms is het installeren van Azure AD Connect mislukt omdat het geen lokale service account kan maken waarvan het wacht woord voldoet aan het niveau van complexiteit dat is opgegeven door het wachtwoord beleid van de organisatie.
* Er is een probleem opgelost waarbij de regels voor samen voegen niet opnieuw worden geëvalueerd wanneer een object in de connector ruimte gelijktijdig buiten het bereik valt voor één joinlijn en in bereik is voor een andere regel. Dit kan gebeuren als u twee of meer joinlijnen hebt waarvan de samenvoegings voorwaarden elkaar wederzijds uitsluiten.
* Er is een probleem opgelost waarbij binnenkomende synchronisatie regels (vanuit Azure AD) die geen joinlijnen bevatten, niet worden verwerkt als ze een lagere prioriteits waarde hebben dan die regels voor samen voegen.

**Verbeteringen**

* Er is ondersteuning toegevoegd voor het installeren van Azure AD Connect op Windows Server 2016 Standard of hoger.
* Er is ondersteuning toegevoegd voor het gebruik van SQL Server 2016 als de externe data base voor Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Uitgebracht: augustus 2016

**Opgeloste problemen:**

* Wijzigingen in het synchronisatie-interval worden pas uitgevoerd nadat de volgende synchronisatie cyclus is voltooid.
* Azure AD Connect wizard accepteert geen Azure AD-account waarvan de gebruikers naam begint met een onderstrepings teken ( \_ ).
* Het Azure AD-account kan niet worden geverifieerd met de wizard Azure AD Connect als het wacht woord van het account te veel speciale tekens bevat. Fout bericht ' kan de referenties niet valideren. Er is een onverwachte fout opgetreden. wordt geretourneerd.
* Als u de faserings server verwijdert, wordt wachtwoord synchronisatie in azure AD-Tenant uitgeschakeld en mislukt de wachtwoord synchronisatie met de actieve server.
* Wachtwoord synchronisatie mislukt in ongebruikelijke gevallen wanneer er geen wacht woord-hash is opgeslagen op de gebruiker.
* Wanneer Azure AD Connect server is ingeschakeld voor de faserings modus, wordt het terugschrijven van wacht woorden niet tijdelijk uitgeschakeld.
* Azure AD Connect wizard geeft niet de werkelijke wachtwoord synchronisatie en de configuratie van wacht woord terugschrijven weer wanneer de server zich in de faserings modus bevindt. Deze worden altijd weer gegeven als uitgeschakeld.
* Configuratie wijzigingen in wachtwoord synchronisatie en wacht woord terugschrijven worden niet persistent gemaakt door Azure AD Connect wizard wanneer de server zich in de faserings modus bevindt.

**Verbeteringen**

* De Start-ADSyncSyncCycle cmdlet is bijgewerkt om aan te geven of een nieuwe synchronisatie cyclus wel of niet kan worden gestart.
* De cmdlet Stop-ADSyncSyncCycle is toegevoegd om de synchronisatie cyclus en-bewerking te beëindigen, die momenteel worden uitgevoerd.
* De Stop-ADSyncScheduler cmdlet is bijgewerkt om de synchronisatie cyclus en-bewerking te beëindigen, die momenteel worden uitgevoerd.
* Bij het configureren van [Directory-extensies](how-to-connect-sync-feature-directory-extensions.md) in azure AD Connect wizard, kan het kenmerk Azure AD van het type ' Teletex string ' nu worden geselecteerd.

## <a name="111890"></a>1.1.189.0
Uitgebracht: juni 2016

**Opgeloste problemen en verbeteringen:**

* Azure AD Connect kan nu worden geïnstalleerd op een FIPS-compatibele server.
  * Zie [wacht woord hash Sync en FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)voor wachtwoord synchronisatie.
* Er is een probleem opgelost waarbij een NetBIOS-naam niet kan worden omgezet naar de FQDN in de Active Directory-connector.

## <a name="111800"></a>1.1.180.0
Uitgebracht: mei 2016

**Nieuwe functies:**

* Waarschuwt en helpt u bij het controleren van domeinen als u dit niet hebt gedaan voordat u Azure AD Connect uitvoert.
* Er is ondersteuning toegevoegd voor [Microsoft Cloud Duitsland](reference-connect-instances.md#microsoft-cloud-germany).
* Er is ondersteuning toegevoegd voor de nieuwste [Microsoft Azure Government Cloud](reference-connect-instances.md#microsoft-azure-government) infrastructuur met nieuwe URL-vereisten.

**Opgeloste problemen en verbeteringen:**

* Er zijn filtering toegevoegd aan de editor voor synchronisatie regels, zodat synchronisatie regels eenvoudig kunnen worden gevonden.
* Verbeterde prestaties bij het verwijderen van een connector ruimte.
* Er is een probleem opgelost toen hetzelfde object werd verwijderd en in dezelfde run werd toegevoegd (de naam verwijderen/toevoegen).
* Met een uitgeschakelde synchronisatie regel worden de Inge sloten objecten en kenmerken voor de upgrade of het vernieuwen van het Directory-schema niet meer opnieuw ingeschakeld.

## <a name="111300"></a>1.1.130.0
Uitgebracht: april 2016

**Nieuwe functies:**

* Er is ondersteuning toegevoegd voor kenmerken met meerdere waarden voor [Directory-extensies](how-to-connect-sync-feature-directory-extensions.md).
* Er is ondersteuning toegevoegd voor meer configuratie variaties voor [automatische upgrade](how-to-connect-install-automatic-upgrade.md) die in aanmerking komen voor een upgrade.
* Er zijn enkele cmdlets toegevoegd voor [aangepaste scheduler](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Uitgebracht: maart 2016

**Opgeloste problemen:**

* Er is vastgesteld dat de snelle installatie niet kan worden gebruikt op Windows Server 2008 (vóór R2), omdat wachtwoord synchronisatie niet wordt ondersteund voor dit besturings systeem.
* Upgraden van DirSync met een aangepaste filter configuratie werkt niet zoals verwacht.
* Wanneer u een upgrade uitvoert naar een nieuwere versie en er geen wijzigingen in de configuratie zijn, moet een volledige import/synchronisatie niet worden gepland.

## <a name="111100"></a>1.1.110.0
Uitgebracht: februari 2016

**Opgeloste problemen:**

* Upgrade van eerdere versies werkt niet als de installatie zich niet in de standaardmap C:\Program Files bevindt.
* Als u het **synchronisatie proces** aan het einde van de installatie wizard installeert en wist, wordt de scheduler niet ingeschakeld door de installatie wizard opnieuw uit te voeren.
* De scheduler werkt niet zoals verwacht op servers waarop de US-en datum/tijd-indeling niet wordt gebruikt. Er wordt ook geblokkeerd `Get-ADSyncScheduler` om de juiste tijden te retour neren.
* Als u een eerdere versie van Azure AD Connect hebt geïnstalleerd met AD FS als de aanmeldings optie en upgrade, kunt u de installatie wizard niet opnieuw uitvoeren.

## <a name="111050"></a>1.1.105.0
Uitgebracht: februari 2016

**Nieuwe functies:**

* [Automatische upgrade](how-to-connect-install-automatic-upgrade.md) functie voor klanten van snelle instellingen.
* Ondersteuning voor de globale beheerder met behulp van Azure AD Multi-Factor Authentication en Privileged Identity Management in de installatie wizard.
  * U moet ervoor zorgen dat uw proxy ook verkeer toestaat https://secure.aadcdn.microsoftonline-p.com Als u multi-factor Authentication gebruikt.
  * U moet toevoegen https://secure.aadcdn.microsoftonline-p.com aan de lijst met vertrouwde websites om multi-factor Authentication goed te kunnen werken.
* Het wijzigen van de aanmeldings methode van de gebruiker na de initiële installatie toestaan.
* [Domein-en OE-filtering](how-to-connect-install-custom.md#domain-and-ou-filtering) toestaan in de installatie wizard. Hierdoor kunt u ook verbinding maken met forests waar niet alle domeinen beschikbaar zijn.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) is ingebouwd in de synchronisatie-engine.

**Functies die worden bevorderd van preview naar GA:**

* [Write-back van apparaat](how-to-connect-device-writeback.md).
* [Directory-extensies](how-to-connect-sync-feature-directory-extensions.md).

**Nieuwe preview-functies:**

* Het nieuwe interval voor de standaard synchronisatie cyclus is 30 minuten. Wordt drie uur gebruikt voor alle eerdere releases. Voegt ondersteuning toe om het [scheduler](how-to-connect-sync-feature-scheduler.md) -gedrag te wijzigen.

**Opgeloste problemen:**

* De pagina DNS-domeinen controleren heeft de domeinen niet altijd herkend.
* Hiermee wordt u gevraagd om referenties voor de domein beheerder bij het configureren van AD FS.
* De on-premises AD-accounts worden niet herkend door de installatie wizard, indien deze zich bevindt in een domein met een andere DNS-structuur dan het hoofd domein.

## <a name="1091310"></a>1.0.9131.0
Uitgebracht: december 2015

**Opgeloste problemen:**

* Wachtwoord synchronisatie werkt mogelijk niet wanneer u wacht woorden wijzigt in Active Directory Domain Services (AD DS), maar werkt wanneer u een wacht woord instelt.
* Wanneer u een proxy server hebt, kan de verificatie van Azure AD mislukken tijdens de installatie, of als een upgrade op de configuratie pagina wordt geannuleerd.
* Het bijwerken van een eerdere versie van Azure AD Connect met een volledige SQL Server-exemplaar mislukt als u geen SQL Server systeem beheerder (SA) bent.
* Wanneer u een eerdere versie van Azure AD Connect met een externe SQL Server bijwerkt, wordt de fout ' kan geen toegang krijgen tot de ADSync SQL database ' weer gegeven.

## <a name="1091250"></a>1.0.9125.0
Uitgebracht: november 2015

**Nieuwe functies:**

* Kan AD FS opnieuw configureren voor Azure AD-vertrouwens relatie.
* Kan het Active Directory schema vernieuwen en de synchronisatie regels opnieuw genereren.
* Kan een synchronisatie regel uitschakelen.
* Kan "AuthoritativeNull" definiëren als een nieuwe letterlijke waarde in een synchronisatie regel.

**Nieuwe preview-functies:**

* [Azure AD Connect Health voor synchronisatie](how-to-connect-health-sync.md).
* Ondersteuning voor [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) wachtwoord synchronisatie.

**Nieuw ondersteund scenario:**

* Ondersteunt meerdere on-premises Exchange-organisaties. Zie [hybride implementaties met meerdere Active Directory-forests](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))voor meer informatie.

**Opgeloste problemen:**

* Problemen met wachtwoord synchronisatie:
  * Het wacht woord voor een object dat is verplaatst van out-of-scope naar in-Scope, is niet gesynchroniseerd. Dit omvat zowel de OE als de kenmerk filtering.
  * Voor het selecteren van een nieuwe organisatie-eenheid die u wilt gebruiken, is geen volledige wachtwoord synchronisatie vereist.
  * Wanneer een uitgeschakelde gebruiker is ingeschakeld, wordt het wacht woord niet gesynchroniseerd.
  * De wachtrij voor het opnieuw proberen van wacht woorden is oneindig en de vorige limiet van 5.000 objecten die buiten gebruik moet worden gesteld, is verwijderd.
* Er kan geen verbinding worden gemaakt met Active Directory met het forest-functionaliteits niveau Windows Server 2016.
* De groep die wordt gebruikt voor het filteren van groepen, kan niet worden gewijzigd na de eerste installatie.
* Maakt geen nieuw gebruikers profiel meer op de Azure AD Connect-server voor elke gebruiker die een wacht woord wijzigt met behulp van wacht woord terugschrijven is ingeschakeld.
* Er kunnen geen lange integere waarden worden gebruikt in het bereik van de synchronisatie regels.
* Het selectie vakje ' apparaat terugschrijven ' blijft uitgeschakeld als er onbereikbare domein controllers zijn.

## <a name="1086670"></a>1.0.8667.0
Uitgebracht: augustus 2015

**Nieuwe functies:**

* De installatie wizard voor Azure AD Connect is nu gelokaliseerd in alle Windows Server-talen.
* Er is ondersteuning toegevoegd voor het ontgrendelen van het account bij gebruik van Azure AD-wachtwoord beheer.

**Opgeloste problemen:**

* Azure AD Connect-installatie wizard loopt vast als een andere gebruiker de installatie voortzet in plaats van de persoon die de installatie voor het eerst heeft gestart.
* Als een eerdere verwijdering van Azure AD Connect niet kan worden verwijderd Azure AD Connect synchronisatie foutloos, is het niet mogelijk om opnieuw te installeren.
* Kan Azure AD Connect niet installeren met behulp van een snelle installatie als de gebruiker zich niet in het hoofd domein van het forest bevindt of als er een niet-Engelse versie van Active Directory wordt gebruikt.
* Als de FQDN van het Active Directory gebruikers account niet kan worden omgezet, wordt een misleidend fout bericht ' kan het schema niet door voeren ' weer gegeven.
* Als het account dat wordt gebruikt op de Active Directory-Connector buiten de wizard wordt gewijzigd, mislukt de wizard bij volgende uitvoeringen.
* Azure AD Connect kan soms niet worden geïnstalleerd op een domein controller.
* Kan de Faserings modus niet inschakelen en uitschakelen als er uitbreidings kenmerken zijn toegevoegd.
* Het terugschrijven van wacht woorden mislukt in sommige configuraties vanwege een onjuist wacht woord op de Active Directory-connector.
* DirSync kan niet worden bijgewerkt als een DN (Distinguished Name) wordt gebruikt in kenmerk filtering.
* Overmatig CPU-gebruik bij het gebruik van wacht woord opnieuw instellen.

**De preview-functies zijn verwijderd:**

* De preview-functie voor het [terugschrijven van gebruikers](how-to-connect-preview.md#user-writeback) is tijdelijk verwijderd op basis van feedback van onze preview-klanten. Deze wordt later opnieuw toegevoegd nadat de gegeven feedback is opgelost.

## <a name="1086410"></a>1.0.8641.0
Uitgebracht: juni 2015

**Eerste versie van Azure AD Connect.**

De naam is gewijzigd van Azure AD Sync naar Azure AD Connect.

**Nieuwe functies:**

* Installatie van [snelle instellingen](how-to-connect-install-express.md)
* Kan [AD FS configureren](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Kan [upgraden van DirSync](how-to-dirsync-upgrade-get-started.md)
* [Onopzettelijke verwijderingen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Geïntroduceerde [faserings modus](how-to-connect-sync-staging-server.md)

**Nieuwe preview-functies:**

* [Gebruikers terugschrijven](how-to-connect-preview.md#user-writeback)
* [Apparaat terugschrijven](how-to-connect-device-writeback.md)
* [Uitbreidingen van de directory](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Uitgebracht: mei 2015

**Nieuwe vereiste:**

* Azure AD Sync moet nu .NET Framework versie 4.5.1 worden geïnstalleerd.

**Opgeloste problemen:**

* Het terugschrijven van wacht woorden vanuit Azure AD mislukt met een Azure Service Bus verbindings fout.

## <a name="104910413"></a>1.0.491.0413
Uitgebracht: april 2015

**Opgeloste problemen en verbeteringen:**

* De Active Directory-connector wordt niet correct verwijderd als de Prullenbak is ingeschakeld en er meerdere domeinen in het forest zijn.
* De prestaties van import bewerkingen zijn verbeterd voor de Azure Active Directory-connector.
* Wanneer een groep de lidmaatschaps limiet heeft overschreden (standaard is de limiet ingesteld op 50.000 objecten), is de groep verwijderd in Azure Active Directory. Met het nieuwe gedrag wordt de groep niet verwijderd, wordt een fout gegenereerd en worden nieuwe lidmaatschappen niet geëxporteerd.
* Er kan geen nieuw object worden ingericht als een gefaseerde verwijdering met dezelfde DN al aanwezig is in de connector ruimte.
* Sommige objecten zijn gemarkeerd voor synchronisatie tijdens een Delta synchronisatie, zelfs als er geen wijziging wordt doorgeleid voor het object.
* Als u een wachtwoord synchronisatie afdwingt, wordt ook de voorkeurs lijst met domein controllers verwijderd.
* CSExportAnalyzer heeft problemen met enkele object statussen.

**Nieuwe functies:**

* Een koppeling kan nu verbinding maken met het object type ' elk ' in de MV.

## <a name="104850222"></a>1.0.485.0222
Uitgebracht: februari 2015

**Verbeteringen**

* Verbeterde prestaties bij het importeren.

**Opgeloste problemen:**

* Wachtwoord synchronisatie voldoet aan het cloudFiltered-kenmerk dat wordt gebruikt door kenmerk filters. Gefilterde objecten zijn niet langer binnen het bereik voor wachtwoord synchronisatie.
* In zeldzame gevallen waarin de topologie veel domein controllers had, werkt wachtwoord synchronisatie niet.
* ' Stopped-server ' bij het importeren van de Azure AD-connector nadat Apparaatbeheer is ingeschakeld in azure AD/intune.
* Het samen voegen van afwijkende beveiligings-principals (FSPs) vanuit meerdere domeinen in hetzelfde forest veroorzaakt een dubbel zinnige-join-fout.

## <a name="104751202"></a>1.0.475.1202
Uitgebracht: december 2014

**Nieuwe functies:**

* Wachtwoord synchronisatie met filteren op basis van een kenmerk wordt nu ondersteund. Zie [wachtwoord synchronisatie met filters](how-to-connect-sync-configure-filtering.md)voor meer informatie.
* Het kenmerk MS-DS-ExternalDirectoryObjectID wordt teruggeschreven naar Active Directory. Deze functie voegt ondersteuning toe voor Microsoft 365 toepassingen. OAuth2 wordt gebruikt voor toegang tot online en on-premises post vakken in een hybride Exchange-implementatie.

**Problemen met de upgrade:**

* Er is een nieuwere versie van de aanmeld hulp beschikbaar op de server.
* Er is een aangepast installatiepad gebruikt om Azure AD Sync te installeren.
* Een ongeldige aangepaste-samenvoegings criterium blokkeert de upgrade.

**Andere oplossingen:**

* De sjablonen voor Office Pro Plus zijn opgelost.
* Problemen met de vaste installatie die worden veroorzaakt door gebruikers namen die beginnen met een streepje.
* Opgelost de source Anchor-instelling verloren gaan wanneer u de installatie wizard een tweede keer uitvoert.
* Vaste ETW-tracering voor wachtwoord synchronisatie.

## <a name="104701023"></a>1.0.470.1023
Uitgebracht: oktober 2014

**Nieuwe functies:**

* Wachtwoord synchronisatie van meerdere on-premises Active Directory naar Azure AD.
* Gelokaliseerde installatie-UI naar alle Windows Server-talen.

**Upgraden vanaf AADSync 1,0 GA**

Als u Azure AD Sync al hebt geïnstalleerd, is er één extra stap die u moet uitvoeren voor het geval u een van de out-of-Box-synchronisatie regels hebt gewijzigd. Nadat u een upgrade hebt uitgevoerd naar de 1.0.470.1023-release, worden de synchronisatie regels die u hebt gewijzigd, gedupliceerd. Ga als volgt te werk voor elke gewijzigde synchronisatie regel:

1. Zoek de gesynchroniseerde regel die u hebt gewijzigd en noteer de wijzigingen.
1. De synchronisatie regel verwijderen.
1. Zoek de nieuwe synchronisatie regel die door Azure AD Sync is gemaakt en pas de wijzigingen vervolgens opnieuw toe.

**Machtigingen voor het Active Directory-account**

Aan het Active Directory account moeten extra machtigingen worden verleend om de wacht woord-hashes van Active Directory te kunnen lezen. De machtigingen voor het verlenen van de naam Directory wijzigingen repliceren en het repliceren van Directory wijzigingen. Beide machtigingen zijn vereist om de wacht woord-hashes te kunnen lezen.

## <a name="104190911"></a>1.0.419.0911
Uitgebracht: september 2014

**Eerste versie van Azure AD Sync.**

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).