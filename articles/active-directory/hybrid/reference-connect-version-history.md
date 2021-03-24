---
title: 'Azure AD Connect: release geschiedenis van versie | Microsoft Docs'
description: In dit artikel vindt u een overzicht van alle releases van Azure AD Connect en Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7269a2435715834a2c1e6723de3fdc6e72eaad5f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955445"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: release geschiedenis van versie
Het Azure Active Directory (Azure AD)-team werkt Azure AD Connect regel matig bij met nieuwe functies en functionaliteit. Niet alle toevoegingen zijn van toepassing op alle doel groepen.

Dit artikel is bedoeld om u te helpen bij het bijhouden van de versies die zijn vrijgegeven en om te begrijpen wat de meest recente versie is.



Deze tabel bevat een lijst met verwante onderwerpen:

Onderwerp |  Details
--------- | --------- |
Stappen om een upgrade uit te voeren van Azure AD Connect | Verschillende methoden voor [het uitvoeren van een upgrade van een eerdere versie naar de nieuwste](how-to-upgrade-previous-version.md) Azure AD Connect versie.
Vereiste machtigingen | Zie [accounts en machtigingen](reference-connect-accounts-permissions.md#upgrade)voor machtigingen die vereist zijn om een update toe te passen.
Downloaden| [Down load Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Het uitgeven van een nieuwe versie van Azure AD Connect is een proces waarbij verschillende kwaliteitscontrole stappen nodig zijn om de werking van de service te waarborgen, terwijl we dit proces door lopen, het versie nummer van een nieuwe release en de release status wordt bijgewerkt op basis van de meest recente status.
Hoewel we dit proces door lopen, wordt het versie nummer van de release weer gegeven met een ' X ' in de positie van het kleine release nummer, zoals in ' 1.3. X. 0 '. Dit betekent dat de release opmerkingen in dit document geldig zijn voor alle versies die beginnen met ' 1,3 '. Zodra het release proces is voltooid, wordt het versie nummer van de release bijgewerkt naar de meest recente versie en wordt de release status bijgewerkt naar ' vrijgegeven voor downloaden en automatische upgrade '.
Niet alle versies van Azure AD Connect worden beschikbaar gesteld voor automatische upgrade. De release status geeft aan of een release beschikbaar moet worden gesteld voor automatische upgrade of alleen voor down loads. Als automatische upgrade is ingeschakeld op uw Azure AD Connect server, wordt die server automatisch bijgewerkt naar de meest recente versie van Azure AD Connect die is uitgebracht voor automatische upgrade. Houd er rekening mee dat niet alle Azure AD Connect configuraties in aanmerking komen voor automatische upgrade. 

Ter verduidelijking van het gebruik van automatische upgrade, is het bedoeld om alle belang rijke updates en essentiële oplossingen naar u te pushen. Dit is niet noodzakelijkerwijs de nieuwste versie, omdat niet alle versies een oplossing voor een kritiek beveiligings probleem vereist/bevatten (slechts een voor beeld van een groot aantal). Een probleem zoals dat kan worden opgelost met een nieuwe versie die wordt verstrekt via automatische upgrade. Als er geen problemen zijn, zijn er geen updates die zijn gepusht met behulp van automatische upgrade, en in het algemeen als u de nieuwste versie van automatische upgrade gebruikt, moet u goed zijn.
Als u echter alle nieuwste functies en updates wilt, kunt u de beste manier om te zien of er een is om deze pagina te controleren en deze te installeren zoals u dat ook ziet. 

Volg deze link voor meer informatie over [automatische upgrade](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Vanaf 1 april 2024 zullen we versies van Azure AD Connect buiten gebruik stellen die zijn uitgebracht vóór 1 mei 2018-versie 1.1.751.0 en ouder. 
>
> U moet ervoor zorgen dat u een recente versie van Azure AD Connect gebruikt om een optimale ondersteunings ervaring te krijgen. 
>
>Als u een buiten gebruik gestelde versie van Azure AD Connect hebt, hebt u mogelijk niet de nieuwste beveiligingsfixes, prestatie verbeteringen, probleem oplossing en diagnostische hulpprogram ma's en service verbeteringen, en als u ondersteuning nodig hebt, kunnen we u mogelijk niet het service niveau bieden dat uw organisatie nodig heeft.
>

>
>Raadpleeg [dit artikel](./how-to-upgrade-previous-version.md) voor meer informatie over het upgraden van Azure AD Connect naar de nieuwste versie.
>
>Zie [Azure AD Connect versie geschiedenis archief](reference-connect-version-history-archive.md) voor informatie over de versie geschiedenis van de buiten gebruik gestelde versies


## <a name="1624"></a>1.6.2.4

>[!NOTE]
> - Deze versie wordt alleen beschikbaar gesteld voor downloaden.
> - Voor de upgrade naar deze versie is een volledige synchronisatie vereist vanwege wijzigingen in de synchronisatie regel.
> - Deze versie wordt standaard ingesteld op de AADConnect-server voor het nieuwe v2-eind punt. Houd er rekening mee dat dit eind punt niet wordt ondersteund in de Duitse nationale Cloud, de Chinese nationale Cloud en de Amerikaanse overheids Cloud. Als u deze versie in deze Clouds moet implementeren, moet u [deze instructies](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) volgen om terug te gaan naar het v1-eind punt. Als u dit niet doet, zijn er fouten opgetreden in de synchronisatie.

### <a name="release-status"></a>Status van de release
3/19/2021: uitgebracht voor downloaden

### <a name="functional-changes"></a>Functionele wijzigingen

 - De standaard synchronisatie regels zijn bijgewerkt om lidmaatschap van een geschreven groep te beperken tot 50.000-leden.
   - Er zijn nieuwe standaard synchronisatie regels toegevoegd voor het beperken van het lidmaatschaps aantal in de groep write-back (van de limiet voor het terugschrijven van AD-groeps leden) en groeps synchronisatie met Azure Active Directory (uitgaande van AAD-Group writeup lid limiet) groepen.
   - Het kenmerk member is toegevoegd aan de regel ' out to AD-Group SOAInAAD-Exchange ' om leden in een geschreven groep te beperken tot 50.000
 - Bijgewerkte synchronisatie regels voor ondersteuning van write-back van groep v2-als de regel ' in van AAD-groep SOAInAAD ' is gekloond en AADConnect is bijgewerkt.
     -De bijgewerkte regel wordt standaard uitgeschakeld, zodat de targetWritebackType null is.
     - AADConnect stuurt alle Cloud groepen (met inbegrip van Azure Active Directory-beveiligings groepen die zijn ingeschakeld voor write-back) als distributie groep.
   -Als de regel ' out to AD-Group SOAInAAD ' is gekloond en AADConnect is bijgewerkt.
     - De bijgewerkte regel wordt standaard uitgeschakeld. Er wordt echter een nieuwe synchronisatie regel ' uit naar AD-Group SOAInAAD-Exchange ' weer ingeschakeld.
     - Afhankelijk van de gekloonde prioriteit van de aangepaste synchronisatie regel, worden de AADConnect en de Exchange-kenmerken door gegeven.
     - Als de gekloonde aangepaste synchronisatie regel geen e-mail-en uitwisselings kenmerken stroomt, worden deze kenmerken door nieuwe Exchange-synchronisatie regel toegevoegd.
 - Ondersteuning toegevoegd voor [selectieve wachtwoord-hash-synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - De nieuwe cmdlet voor het [synchroniseren van enkelvoudige objecten](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync)is toegevoegd. Gebruik deze cmdlet om problemen met de Azure AD Connect synchronisatie configuratie op te lossen. 
 -  Azure AD Connect ondersteunt nu de rol van de hybride identiteits beheerder voor het configureren van de service.
 - De AADConnectHealth-agent is bijgewerkt naar 3.1.83.0
 - Nieuwe versie van de [ADSyncTools Power shell-module](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools), met diverse nieuwe of verbeterde cmdlets. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Bijgewerkte fout registratie voor fouten bij het ophalen van tokens.
 - De koppelingen meer informatie op de pagina configuratie zijn bijgewerkt om meer details te geven over de gekoppelde informatie.
 - Expliciete kolom verwijderd uit CS-zoek pagina in de oude synchronisatie GEBRUIKERSINTERFACE
 - Extra gebruikers interface is toegevoegd aan de groep terugschrijf stroom om de gebruiker om referenties te vragen of hun eigen machtigingen te configureren met behulp van de ADSyncConfig-module als referenties nog niet in een eerdere stap zijn gegeven.
 - Het automatisch maken van MSA voor het ADSync-service account op een domein controller. 
 -  De mogelijkheid om terugschrijven van Azure Active Directory DirSync-functie groep in te stellen en op te halen, is toegevoegd aan de bestaande cmdlets:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - 2 cmdlets toegevoegd om de AWS-API-versie te lezen
    - Get-ADSyncAADConnectorImportApiVersion-AWS-API-versie van import ophalen
    - Get-ADSyncAADConnectorExportApiVersion-AWS-API-versie van export ophalen

 - Wijzigingen die worden aangebracht aan de synchronisatie regels, worden nu bijgehouden om problemen met wijzigingen in de service op te lossen. Met de cmdlet Get-ADSyncRuleAudit worden bijgehouden wijzigingen opgehaald.
 - De Add-ADSyncADDSConnectorAccount-cmdlet is bijgewerkt in de [Power shell-module ADSyncConfig](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) , zodat een gebruiker in de ADSyncAdmin-groep het AD DS Connector-account kan wijzigen. 

### <a name="bug-fixes"></a>Opgeloste fouten
 - Uitgeschakelde voorgrond kleur bijgewerkt om te voldoen aan de helderheids vereisten op een witte achtergrond. Extra voor waarden toegevoegd voor de navigatie structuur om de voorgrond kleur in te stellen op wit wanneer een uitgeschakelde pagina is geselecteerd om te voldoen aan de vereisten voor de helderheid.
 - Verhoog de granulariteit voor Set-ADSyncPasswordHashSyncPermissions-cmdlet-bijgewerkt PHS-machtigingen script (set-ADSyncPasswordHashSyncPermissions) met een optionele para meter ADobjectDN. 
 - Probleem oplossing voor toegankelijkheid. De scherm lezer beschrijft nu het UX-element dat de lijst met forests bevat als '**bossen** lijst ' in plaats van ' lijst met **forests**'
 - De uitvoer van de scherm lezer is bijgewerkt voor sommige items in de wizard Azure AD Connect. De kleur van de knop is bijgewerkt om te voldoen aan de contrast vereisten. De titel kleur van Synchronization Service Manager is bijgewerkt om te voldoen aan de contrast vereisten.
 - Er is een probleem opgelost met het installeren van AADConnect vanuit de geëxporteerde configuratie met aangepaste extensie kenmerken: er is een voor waarde toegevoegd om het controleren op extensie kenmerken in het doel schema over te slaan tijdens het Toep assen van de synchronisatie regel.
 - De juiste machtigingen worden toegevoegd bij de installatie als de functie voor het terugschrijven van de groep is ingeschakeld.
 - Prioriteit van duplicaten standaard synchronisatie regel bij importeren herstellen
 - Er is een probleem opgelost waardoor een faserings fout is opgetreden tijdens het importeren van een conflicterend object dat is hersteld via de Health-Portal.
 - Er is een probleem opgelost in de synchronisatie-engine waardoor CS-objecten een inconsistente koppelings status hebben
 - Import tellers zijn toegevoegd aan Get-ADSyncConnectorStatistics uitvoer.
 - Vaste onbereikbare domein de selectie (eerder geselecteerd) probleem in sommige hoek gevallen tijdens de wizard pass2.
 - Gewijzigde beleids import en-export naar mislukt als de aangepaste regel een dubbele prioriteit heeft 
 - Er is een fout opgelost in de logica van de selectie van het domein.
 - Hiermee wordt een probleem met build 1.5.18.0 opgelost als u mS-DS-ConsistencyGuid als bron anker gebruikt en de in van de AD-Group-koppelings regel hebt gekloond.
 - Nieuwe AADConnect-installaties gebruiken de drempel waarde voor het verwijderen van het exporteren, die is opgeslagen in de Cloud als er een beschikbaar is en er geen andere is opgegeven in.
 - Er is een probleem opgelost waarbij AADConnect geen wijzigingen in de AD displayName van aan Hybrid gekoppelde apparaten leest

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Status van de release
07/29/2020: uitgebracht voor downloaden

### <a name="functional-changes"></a>Functionele wijzigingen
Dit is een release van de fout oplossing. Er zijn geen functionele wijzigingen in deze release.

### <a name="fixed-issues"></a>Opgeloste problemen

- Er is een probleem opgelost waarbij de beheerder ' naadloze eenmalige aanmelding ' niet kan inschakelen als het computer account van AZUREADSSOACC al aanwezig is in de Active Directory.
- Er is een probleem opgelost waardoor een faserings fout is opgetreden tijdens het importeren van een conflicterend object dat is hersteld via de Health-Portal.
- Er is een probleem opgelost in de import/export-configuratie waar een uitgeschakelde aangepaste regel is geïmporteerd als ingeschakeld.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Status van de release
07/10/2020: uitgebracht voor downloaden

### <a name="functional-changes"></a>Functionele wijzigingen
Deze release bevat een open bare preview van de functionaliteit voor het exporteren van de configuratie van een bestaande Azure AD Connect-server naar een. JSON-bestand dat vervolgens kan worden gebruikt bij het installeren van een nieuwe Azure AD Connect server voor het maken van een kopie van de oorspronkelijke server.

In [dit artikel](./how-to-connect-import-export-config.md) vindt u een gedetailleerde beschrijving van deze nieuwe functie.

### <a name="fixed-issues"></a>Opgeloste problemen
- Er is een fout opgelost waarbij er tijdens de upgrade een onjuiste waarschuwing is over de lokale DB-grootte op de gelokaliseerde builds.
- Er is een fout opgelost waarbij er een onjuiste fout is opgetreden in de app-gebeurtenissen voor de account naam/domein naam swap.
- Er is een fout opgelost waarbij Azure AD Connect niet kan worden geïnstalleerd op een domein controller, waarbij de fout ' lid niet gevonden ' wordt weer gegeven.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Status van de release
05/07/2020: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem opgelost waarbij niet-geselecteerde domeinen onjuist zijn geselecteerd in de gebruikers interface van de wizard als er alleen grandchild-containers zijn geselecteerd.


>[!NOTE]
>Deze versie bevat de nieuwe Azure AD Connect Sync v2-eind punt-API.  Dit nieuwe v2-eind punt bevindt zich momenteel in de open bare preview.  Deze versie of hoger is vereist voor het gebruik van de nieuwe v2-eind punt-API.  Alleen als u deze versie installeert, wordt het v2-eind punt niet ingeschakeld. U blijft het v1-eind punt gebruiken tenzij u het v2-eind punt inschakelt.  U moet de stappen onder [Azure AD Connect Sync v2-eind punt-API (open bare preview)](how-to-connect-sync-endpoint-api-v2.md) volgen om deze functie in te scha kelen voor de open bare preview.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Status van de release
04/23/2020: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem opgelost dat is geïntroduceerd in Build 1.5.20.0, waarbij een Tenant beheerder met MFA de DSSO niet kan inschakelen.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Status van de release
04/20/2020: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem in Build 1.5.20.0 opgelost als u de in hebt gekloond van de regel **voor het samen voegen van de AD-groep** en de in van de regel voor het maken **van de AD-groep** niet hebt gekloond.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Status van de release
04/09/2020: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
- Met deze hotfix-build wordt een probleem met build 1.5.18.0 opgelost als u de functie voor groeps filtering hebt ingeschakeld en mS-DS-ConsistencyGuid als bron anker gebruikt.
- Er is een probleem opgelost in de Power shell-module ADSyncConfig, waarbij het aanroepen van de opdracht DSACLS die wordt gebruikt in alle cmdlets voor de set-ADSync *-machtigingen een van de volgende fouten veroorzaken:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Voer de volgende stappen uit als onderdeel van de upgrade als u de regel **in vanuit de groep voor het samen voegen van AD-groepslid** maatschap hebt gekloond en de upgrade niet hebt gekloond van de regel voor **algemene synchronisatie van AD-groep** en wilt upgraden.
> 1. Schakel tijdens de upgrade de optie het **synchronisatie proces starten wanneer de configuratie is voltooid** uit.
> 2. Bewerk de gekloonde samenvoegings regel en voeg de volgende twee trans formaties toe:
>     - Stel directe stroom `objectGUID` in op `sourceAnchorBinary` .
>     - Stel de expressie stroom `ConvertToBase64([objectGUID])` in op `sourceAnchor` .     
> 3. Activeer de scheduler met behulp van `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Status van de release
04/02/2020: uitgebracht voor downloaden

### <a name="functional-changes-adsyncautoupgrade"></a>Functionele wijzigingen ADSyncAutoUpgrade 

- Er is ondersteuning toegevoegd voor de functie mS-DS-ConsistencyGuid voor groeps objecten. Zo kunt u groepen tussen forests verplaatsen of groepen opnieuw verbinden in AD met Azure AD waar de objectID van de AD-groep is gewijzigd, bijvoorbeeld wanneer een AD-server opnieuw wordt opgebouwd na een Calamity. Zie voor meer informatie [groepen verplaatsen tussen forests](how-to-connect-migrate-groups.md).
- Het kenmerk mS-DS-ConsistencyGuid wordt automatisch ingesteld op alle gesynchroniseerde groepen en u hoeft niets te doen om deze functie in te scha kelen. 
- De Get-ADSyncRunProfile is verwijderd omdat deze niet meer in gebruik is. 
- De waarschuwing die wordt weer gegeven wanneer u probeert een ondernemings beheerder of een domein beheerders account te gebruiken voor de AD DS Connector-account, is gewijzigd om meer context te bieden. 
- Er is een nieuwe cmdlet toegevoegd om objecten uit de connector ruimte te verwijderen het oude CSDelete.exe-hulp programma wordt verwijderd en wordt vervangen door de nieuwe Remove-ADSyncCSObject-cmdlet. De Remove-ADSyncCSObject-cmdlet neemt een CsObject als invoer. Dit object kan worden opgehaald met behulp van de cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Het oude CSDelete.exe-hulp programma is verwijderd en vervangen door de nieuwe Remove-ADSyncCSObject-cmdlet 

### <a name="fixed-issues"></a>Opgeloste problemen

- Er is een fout opgelost in de groep write-back-upforest/OE-selector op het opnieuw uitvoeren van de Azure AD Connect wizard nadat de functie is uitgeschakeld. 
- Een nieuwe fout pagina geïntroduceerd die wordt weer gegeven als de vereiste DCOM-register waarden ontbreken met een nieuwe Help-koppeling. Informatie wordt ook geschreven naar logboek bestanden. 
- Er is een probleem opgelost met het maken van het Azure Active Directory synchronisatie account waar Directory-extensies of PHS kunnen mislukken omdat het account niet is door gegeven aan alle service replica's voordat een poging wordt gedaan om te gebruiken. 
- Er is een fout opgelost in het compressie hulpprogramma synchronisatie fouten dat geen surrogaat tekens goed afhandelen. 
- Er is een fout opgelost in de automatische upgrade die de server heeft verlaten in de onderbroken status van scheduler. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Status van de release
12/9/2019: vrijgeven voor downloaden. Niet beschikbaar via automatische upgrade.
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- De wachtwoord hash-synchronisatie voor Azure AD Domain Services is bijgewerkt naar een juiste account voor opvulling in Kerberos-hashes.  Dit biedt een betere prestaties tijdens het synchroniseren van wacht woorden van Azure AD naar Azure AD Domain Services.
- Er is ondersteuning toegevoegd voor betrouw bare sessies tussen de verificatie agent en service bus.
- Deze release dwingt TLS 1,2 af voor communicatie tussen verificatie agent en Cloud Services.
- Er is een DNS-cache toegevoegd voor WebSocket-verbindingen tussen verificatie agent en Cloud Services.
- We hebben de mogelijkheid toegevoegd om specifieke agents te richten vanuit de cloud om te testen op agent connectiviteit.

### <a name="fixed-issues"></a>Opgeloste problemen
- De release-1.4.18.0 heeft een bug waarbij de Power shell-cmdlet voor DSSO de aanmeld Windows-referenties gebruikt in plaats van de beheerders referenties die tijdens het uitvoeren van PS zijn ingevoerd. Als gevolg hiervan is het niet mogelijk om DSSO in meerdere forests in te scha kelen via de gebruikers interface van Azure AD Connect. 
- Er is een oplossing uitgevoerd om DSSO tegelijkertijd in alle forests in te scha kelen via de gebruikers interface van Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Status van de release
11/08/2019: uitgebracht voor downloaden. Niet beschikbaar via automatische upgrade.

>[!IMPORTANT]
>Als gevolg van een interne schema wijziging in deze versie van Azure AD Connect, als u de configuratie-instellingen voor AD FS vertrouwens relatie beheert met MSOnline Power shell, moet u uw MSOnline Power shell-module bijwerken naar versie 1.1.183.57 of hoger

### <a name="fixed-issues"></a>Opgeloste problemen

Deze versie corrigeert een probleem met bestaande hybride Azure AD-apparaten die zijn toegevoegd. Deze release bevat een nieuwe regel voor het synchroniseren van apparaten waarmee dit probleem wordt verholpen.
Houd er rekening mee dat deze regel wijziging ertoe kan leiden dat er verouderde apparaten van Azure AD worden verwijderd. Dit is geen oorzaak van bezorgdheid, omdat deze object objecten niet worden gebruikt door Azure AD tijdens de autorisatie van voorwaardelijke toegang. Voor sommige klanten kan het aantal apparaten dat wordt verwijderd via deze regel wijziging de drempel waarde voor verwijderen overschrijden. Als u het verwijderen van apparaatobject in azure AD overschrijdt, wordt de drempel waarde voor het verwijderen van het exporteren weer toegestaan. [Verwijderen van een stroom toestaan wanneer deze de drempel waarde voor verwijderen overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Status van de release
9/28/2019: vrijgegeven voor automatische upgrade om tenants te selecteren. Niet beschikbaar voor downloaden.

Deze versie corrigeert een bug waarbij sommige servers die automatisch zijn bijgewerkt van een eerdere versie naar 1.4.18.0 en problemen ondervonden met selfservice voor wachtwoord herstel (SSPR) en het terugschrijven van wacht woorden.

### <a name="fixed-issues"></a>Opgeloste problemen

Onder bepaalde omstandigheden werden servers die automatisch zijn bijgewerkt naar versie 1.4.18.0, de selfservice voor wachtwoord herstel en het terugschrijven van wacht woorden niet opnieuw inschakelen nadat de upgrade is voltooid. Deze automatische upgrade release corrigeert die de selfservice voor het opnieuw instellen van wacht woorden en het terugschrijven van wacht woorden opnieuw mogelijk maakt.

Er is een fout opgelost in het compressie hulpprogramma synchronisatie fouten dat geen surrogaat tekens goed afhandelen.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Er wordt een incident onderzocht waarbij sommige klanten een probleem ondervinden met bestaande Hybrid Azure AD-apparaten die zijn toegevoegd na de upgrade naar deze versie van Azure AD Connect. Wij adviseren klanten die hybride Azure AD-deelname hebben geïmplementeerd om de upgrade naar deze versie uit te stellen totdat de hoofd oorzaak van deze problemen volledig is begrepen en wordt verholpen. Meer informatie wordt zo snel mogelijk verstrekt.

>[!IMPORTANT]
>Met deze versie van Azure AD Connect kunnen sommige klanten sommige of alle Windows-apparaten zien, verdwijnen van Azure AD. Dit is geen oorzaak van bezorgdheid, omdat deze apparaat-id's niet worden gebruikt door Azure AD tijdens de autorisatie van voorwaardelijke toegang. Zie [Wat is Azure AD Connect 1.4. xx. x-apparaat disappearnce](reference-connect-device-disappearance.md) voor meer informatie.


### <a name="release-status"></a>Status van de release
9/25/2019: alleen uitgegeven voor automatische upgrades.

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- Nieuw hulp programma voor probleem oplossing helpt bij het oplossen van problemen ' gebruiker niet synchroniseren ', ' groep niet synchroniseren ' of ' geen synchronisatie van groeps leden '.
- Voeg ondersteuning toe voor nationale Clouds in Azure AD Connect probleemoplossings script.
- Klanten moeten worden geïnformeerd dat de afgeschafte WMI-eind punten voor MIIS_Service nu zijn verwijderd. Alle WMI-bewerkingen moeten nu worden uitgevoerd via PS-cmdlets.
- Beveiligings verbetering door beperkte delegering op AZUREADSSOACC-object opnieuw in te stellen.
- Bij het toevoegen/bewerken van een synchronisatie regel worden de kenmerken automatisch toegevoegd aan de connector als er kenmerken worden gebruikt in de regel die zich in het connector-schema bevinden, maar niet zijn toegevoegd aan de connector. Hetzelfde geldt voor het object type waarop de regel van toepassing is. Als er iets wordt toegevoegd aan de connector, wordt de connector gemarkeerd voor volledige import bij de volgende synchronisatie cyclus.
- Het gebruik van een ondernemings-of domein beheerder als Connector account wordt niet meer ondersteund in nieuwe Azure AD Connect-implementaties. De huidige Azure AD Connect implementaties die gebruikmaken van een onderneming of een domein beheerder als Connector account, worden niet beïnvloed door deze versie.
- In synchronisatie beheer wordt een volledige synchronisatie uitgevoerd bij het maken/bewerken/verwijderen van een regel. Er wordt een pop-upvenster weer gegeven bij elke regel wijziging waarmee de gebruiker wordt gewaarschuwd als volledige import of volledige synchronisatie wordt uitgevoerd.
- De stappen voor het oplossen van problemen met wacht woorden voor de pagina connectors > eigenschappen > connectiviteit zijn toegevoegd.
- Er is een waarschuwing voor afschaffing toegevoegd voor de synchronisatie Service Manager op de eigenschappen pagina van de connector. Met deze waarschuwing wordt de gebruiker ervan op de hoogte gebracht dat wijzigingen moeten worden aangebracht via de Azure AD Connect wizard.
- Er is een nieuwe fout toegevoegd voor problemen met het wachtwoord beleid van een gebruiker.
- Voorkom een onjuiste configuratie van groeps filtering op domein-en OE-filters. Met groeps filtering wordt een fout weer gegeven wanneer het domein of de organisatie-eenheid van de opgegeven groep al is gefilterd en de gebruiker verder niet kan overschakelen totdat het probleem is opgelost.
- Gebruikers kunnen geen connector meer maken voor Active Directory Domain Services of Windows Azure Active Directory in de Synchronization Service Manager gebruikers interface.
- Vaste toegankelijkheid van aangepaste UI-besturings elementen in de Synchronization Service Manager.
- Er zijn zes Federatie beheer taken ingeschakeld voor alle aanmeldings methoden in Azure AD Connect.  (Voorheen was alleen de taak ' update AD FS TLS/SSL-certificaat ' beschikbaar voor alle aanmeldingen.)
- Er is een waarschuwing toegevoegd bij het wijzigen van de aanmeldings methode van Federatie naar PHS of PTA dat alle Azure AD-domeinen en-gebruikers worden geconverteerd naar beheerde authenticatie.
- Certificaten voor token-ondertekening zijn verwijderd uit de taak ' de Azure AD-en AD FS-vertrouwens relatie opnieuw instellen ' en er is een afzonderlijke subtaak toegevoegd om deze certificaten bij te werken.
- Er is een nieuwe Federatie beheer taak met de naam ' certificaten beheren ' toegevoegd met subtaken voor het bijwerken van de TLS-of Token handtekening certificaten voor de AD FS-farm.
- Er is een nieuwe subtaak voor Federatie beheer met de naam ' primaire server opgeven ' toegevoegd waarmee beheerders een nieuwe primaire server voor de AD FS farm kunnen opgeven.
- Er is een nieuwe Federatie beheer taak met de naam "servers beheren" met subtaken toegevoegd om een AD FS server te implementeren, een webtoepassingsproxy-server te implementeren en een primaire server op te geven.
- Er is een nieuwe Federatie beheer taak met de naam ' Federatie configuratie weer geven ' toegevoegd waarin de huidige AD FS-instellingen worden weer gegeven.  (Vanwege deze toevoeging zijn AD FS instellingen verwijderd van de pagina ' uw oplossing controleren '.)

### <a name="fixed-issues"></a>Opgeloste problemen
- Er is een probleem opgelost met de synchronisatie fout voor het scenario waarbij een gebruikers object dat via het bijbehorende contact object wordt opgehaald, een verwijzing naar zichzelf heeft (bijvoorbeeld een eigen manager van de gebruiker).
- Help popups worden nu weer gegeven op de toetsenbord focus.
- Als een conflicterende app vanuit 6 uur wordt uitgevoerd, beëindigt u deze voor automatische upgrade en gaat u verder met de upgrade.
- Beperk het aantal kenmerken dat een klant kan selecteren tot 100 per object bij het selecteren van Directory-extensies. Hierdoor wordt voor komen dat de fout optreedt tijdens het exporteren, omdat Azure Maxi maal 100 extensie kenmerken per object heeft.
- Er is een fout opgelost waardoor het AD-connectiviteits script robuuster is.
- Een bug opgelost om Azure AD Connect te installeren op een computer met behulp van een bestaande named pipes WCF-service robuuster.
- Verbeterde diagnostische gegevens en probleem oplossing rond groeps beleid waarmee de ADSync-service niet kan worden gestart wanneer deze voor het eerst wordt geïnstalleerd.
- Er is een fout opgelost waarbij de weergave naam voor een Windows-computer onjuist is geschreven.
- Corrigeer een bug waarbij het besturingssysteem type voor een Windows-computer onjuist is geschreven.
- Er is een fout opgelost waarbij niet-Windows 10-computers onverwacht worden gesynchroniseerd. Houd er rekening mee dat het effect van deze wijziging is dat niet-Windows 10-computers die eerder zijn gesynchroniseerd, nu worden verwijderd. Dit heeft geen invloed op functies omdat de synchronisatie van Windows-computers alleen wordt gebruikt voor hybride Azure AD-domein deelname, die alleen werkt voor Windows-10-apparaten.
- Er zijn meerdere nieuwe (interne) cmdlets toegevoegd aan de ADSync Power shell-module.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Er is een bekend probleem met het upgraden van Azure AD Connect van een eerdere versie naar 1.3.21.0, waarbij de Microsoft 365 Portal niet overeenkomt met de bijgewerkte versie, zelfs als Azure AD Connect is bijgewerkt.
>
> Als u dit wilt oplossen, moet u de **AdSync** -module importeren en vervolgens de `Set-ADSyncDirSyncConfiguration` Power shell-cmdlet op de Azure AD Connect-server uitvoeren.  U kunt de volgende stappen uitvoeren:
>
>1. Open Power shell in de beheerders modus.
>2. Voer `Import-Module "ADSync"` uit.
>3. Voer `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` uit.
 
### <a name="release-status"></a>Status van de release 

05/14/2019: uitgebracht voor downloaden

### <a name="fixed-issues"></a>Opgeloste problemen 

- Er is een beveiligingslek met betrekking tot misbruik van bevoegdheden opgelost dat bestaat in Microsoft Azure Active Directory Connect build 1.3.20.0.  Dit beveiligingslek kan onder bepaalde voor waarden ertoe leiden dat een aanvaller twee Power shell-cmdlets kan uitvoeren in de context van een bevoegd account en geprivilegieerde acties kan uitvoeren.  Met deze beveiligings update wordt het probleem opgelost door deze cmdlets uit te scha kelen. Zie voor meer informatie [beveiligings update](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
