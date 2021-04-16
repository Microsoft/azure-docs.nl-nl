---
title: 'Azure AD Connect: Versiegeschiedenis van release | Microsoft Docs'
description: In dit artikel worden alle releases van Azure AD Connect en Azure AD Sync.
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
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576360"
---
# <a name="azure-ad-connect-version-release-history"></a>Versiegeschiedenis van Azure AD Connect
Het Azure Active Directory (Azure AD) wordt regelmatig bijgewerkt Azure AD Connect nieuwe functies en functionaliteit. Niet alle toevoegingen zijn van toepassing op alle doelgroepen.

Dit artikel is ontworpen om u te helpen bij te houden welke versies zijn uitgebracht en om te begrijpen wat de wijzigingen zijn in de nieuwste versie.



Deze tabel is een lijst met gerelateerde onderwerpen:

Onderwerp |  Details
--------- | --------- |
Stappen voor het upgraden van Azure AD Connect | Verschillende methoden om [een upgrade uit te voeren van een vorige versie naar de](how-to-upgrade-previous-version.md) Azure AD Connect release.
Vereiste machtigingen | Zie Accounts en machtigingen voor machtigingen die zijn vereist om een update [toe te passen.](reference-connect-accounts-permissions.md#upgrade)
Downloaden| [Download Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Het vrijgeven van een nieuwe versie van Azure AD Connect is een proces dat verschillende kwaliteitscontrolestaps vereist om de functionaliteit van de service te garanderen. Tijdens dit proces wordt het versienummer van een nieuwe release en de releasestatus bijgewerkt met de meest recente status.
Terwijl we dit proces doorlopen, wordt het versienummer van de release weergegeven met een 'X' in de positie van het secundaire releasenummer, zoals in 1.3.X.0. Dit geeft aan dat de releasenotities in dit document geldig zijn voor alle versies die beginnen met 1.3. Zodra het releaseproces is afgerond, wordt het versienummer van de release bijgewerkt naar de meest recente versie en wordt de releasestatus bijgewerkt naar 'Uitgebracht voor downloaden en automatisch upgraden'.
Niet alle versies van Azure AD Connect worden beschikbaar gesteld voor automatische upgrade. De releasestatus geeft aan of een release beschikbaar wordt gesteld voor automatische upgrade of alleen voor downloaden. Als automatische upgrade is ingeschakeld op uw Azure AD Connect server, wordt die server automatisch bijgewerkt naar de nieuwste versie van Azure AD Connect die wordt uitgebracht voor automatische upgrade. Houd er rekening mee dat niet Azure AD Connect configuraties in aanmerking komen voor automatische upgrade. 

Ter verduidelijking van het gebruik van automatische upgrade is het bedoeld om alle belangrijke updates en kritieke oplossingen naar u te pushen. Dit is niet noodzakelijkerwijs de meest recente versie, omdat niet voor alle versies een oplossing voor een kritiek beveiligingsprobleem nodig is/bevat (slechts één voorbeeld van veel). Een probleem zoals dat wordt opgelost met een nieuwe versie die wordt geleverd via automatische upgrade. Als er geen dergelijke problemen zijn, worden er geen updates naar buiten ge pusht met behulp van automatische upgrade. Als u over het algemeen de nieuwste versie van de automatische upgrade gebruikt, zou u goed moeten zijn.
Als u echter alle nieuwste functies en updates wilt, is het de beste manier om te zien of er een is door deze pagina te controleren en naar eigen goed idee te installeren. 

Volg deze koppeling voor meer informatie over automatische [upgrade](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Vanaf 1 april 2024 worden versies van Azure AD Connect die zijn uitgebracht vóór 1 mei 2018 - versie 1.1.751.0 en ouder, uit gebruik gesteld. 
>
> U moet ervoor zorgen dat u een recente versie van de Azure AD Connect voor een optimale ondersteuningservaring. 
>
>Als u een uit gebruik genomen versie van Azure AD Connect hebt u mogelijk niet de nieuwste beveiligingsoplossingen, prestatieverbeteringen, hulpprogramma's voor probleemoplossing en diagnostische hulpprogramma's en serviceverbeteringen. Als u ondersteuning nodig hebt, kunnen we u mogelijk niet voorzien van het serviceniveau dat uw organisatie nodig heeft.
>

>
>Raadpleeg dit [artikel voor meer](./how-to-upgrade-previous-version.md) informatie over het upgraden van Azure AD Connect naar de nieuwste versie.
>
>Zie voor informatie over de versiegeschiedenis over niet-Azure AD Connect versiegeschiedenisarchief [](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> De Azure AD Connect sync V2-eindpunt-API is nu beschikbaar in deze Azure-omgevingen:
> - Azure Commercial
> - Azure China-cloud
> - Azure US Government-cloud Deze wordt niet beschikbaar gesteld in de Duitse Azure-cloud

### <a name="release-status"></a>Status van de release
31-3-2021: alleen beschikbaar voor downloaden, niet beschikbaar voor automatische upgrade

### <a name="bug-fixes"></a>Opgeloste fouten
- Deze release lost een fout op in versie 1.6.2.4, waarbij na de upgrade naar die release de functie Azure AD Connect Health niet juist is geregistreerd en niet werkt. Klanten die build 1.6.2.4 hebben geïmplementeerd, worden gevraagd hun Azure AD Connect-server bij te werken met deze build, waardoor de health-functie correct wordt geregistreerd. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Update per 30 maart 2021: er is een probleem ontdekt in deze build. Na de installatie van deze build worden de Health-services niet geregistreerd. U wordt aangeraden deze build niet te installeren. Binnenkort wordt een hotfix uitgebracht.
> Als u deze build al hebt geïnstalleerd, kunt u de Health-services handmatig registreren met behulp van de cmdlet , zoals wordt weergegeven in [dit artikel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Deze release wordt alleen beschikbaar gesteld voor downloaden.
> - Voor de upgrade naar deze release is een volledige synchronisatie vereist vanwege wijzigingen in de synchronisatieregel.
> - In deze release wordt de AADConnect-server standaard ingesteld op het nieuwe V2-eindpunt. Houd er rekening mee dat dit eindpunt niet wordt ondersteund in de Duitse nationale [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) cloud. Als u deze versie in deze omgeving wilt implementeren, moet u deze instructies volgen om terug te keren naar het V1-eindpunt. Als u dit niet doet, resulteert dit in fouten in de synchronisatie.

### <a name="release-status"></a>Status van de release
19-3-2021: uitgebracht om te downloaden, niet beschikbaar voor automatische upgrade

### <a name="functional-changes"></a>Functionele wijzigingen

 - Standaardsynchronisatieregels bijgewerkt om lidmaatschap van teruggeschreven groepen te beperken tot 50.000 leden.
   - Er zijn nieuwe standaardsynchronisatieregels toegevoegd voor het beperken van het aantal lidmaatschappen in groep terugschrijven (naar AD - limiet voor groep terugschrijven-leden) en groepssynchronisatie naar Azure Active Directory-groepen (naar AAD - limiet voor groep write-uplid).
   - Lidkenmerk toegevoegd aan de regel Out to AD - Group SOAInAAD - Exchange om leden in teruggeschreven groepen te beperken tot 50.000
 - Synchronisatieregels bijgewerkt ter ondersteuning van Group Writeback v2: als de regel 'In from AAD - Group SOAInAAD' is gekloond en AADConnect wordt bijgewerkt.
     -De bijgewerkte regel wordt standaard uitgeschakeld en daarom is targetWritebackType null.
     - AADConnect schrijft alle cloudgroepen terug (inclusief Azure Active Directory beveiligingsgroepen die zijn ingeschakeld voor terugschrijven) als distributiegroepen.
   -Als de regel Out to AD - Group SOAInAAD is gekloond en AADConnect wordt bijgewerkt.
     - De bijgewerkte regel wordt standaard uitgeschakeld. Er wordt echter een nieuwe synchronisatieregel 'Out to AD - Group SOAInAAD - Exchange' ingeschakeld die wordt toegevoegd.
     - Afhankelijk van de prioriteit van de gekloonde aangepaste synchronisatieregel, worden de E-mail- en Exchange-kenmerken door AADConnect verzonden.
     - Als de aangepaste synchronisatieregel voor gekloonde e-mail en Exchange-kenmerken niet wordt gestroomd, worden deze kenmerken door de nieuwe Exchange-synchronisatieregel toe voegen.
 - Ondersteuning toegevoegd voor [synchronisatie van hash-selectief wachtwoord](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - De nieuwe [cmdlet Single Object Sync is toegevoegd.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync) Gebruik deze cmdlet om problemen met uw synchronisatieconfiguratie Azure AD Connect oplossen. 
 -  Azure AD Connect ondersteunt nu de rol Hybride identiteitsbeheerder voor het configureren van de service.
 - AADConnectHealth-agent bijgewerkt naar 3.1.83.0
 - Nieuwe versie van de [POWERShell-module ADSyncTools,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)die verschillende nieuwe of verbeterde cmdlets bevat. 
 
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

 - Logboekregistratie van fouten bijgewerkt voor fouten bij het verkrijgen van een token.
 - Koppelingen voor meer informatie op de configuratiepagina bijgewerkt voor meer informatie over de gekoppelde informatie.
 - Expliciete kolom verwijderd van de cs-zoekpagina in de gebruikersinterface voor oude synchronisatie
 - Er is een extra gebruikersinterface toegevoegd aan de stroom Terugschrijven van groepen om de gebruiker om referenties te vragen of om hun eigen machtigingen te configureren met behulp van de module ADSyncConfig als er in een eerdere stap nog geen referenties zijn opgegeven.
 - Automatisch MSA maken voor ADSync-serviceaccount op een dc. 
 -  Mogelijkheid toegevoegd voor het instellen en Azure Active Directory DirSync-functie Group Writeback V2 in de bestaande cmdlets:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Er zijn 2 cmdlets toegevoegd om de AWS API-versie te lezen
    - Get-ADSyncAADConnectorImportApiVersion: om de AWS API-versie te importeren
    - Get-ADSyncAADConnectorExportApiVersion: om de AWS API-versie te exporteren

 - Wijzigingen in synchronisatieregels worden nu bij te houden om problemen met wijzigingen in de service op te lossen. De cmdlet Get-ADSyncRuleAudit haalt bijgespoorde wijzigingen op.
 - De cmdlet Add-ADSyncADDSConnectorAccount [adsyncconfig in de PowerShell-module ADSyncConfig](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) is bijgewerkt, zodat een gebruiker in de adSyncAdmin-groep het AD DS Connector-account kan wijzigen. 

### <a name="bug-fixes"></a>Opgeloste fouten
 - De uitgeschakelde voorgrondkleur is bijgewerkt om te voldoen aan de vereisten voor gelijkenissen op een witte achtergrond. Er zijn aanvullende voorwaarden toegevoegd voor de navigatiestructuur om de kleur van de voorgrondtekst in te stellen op wit wanneer een uitgeschakelde pagina wordt geselecteerd om te voldoen aan de vereisten voor de voorgrond.
 - Granulariteit verhogen voor Set-ADSyncPasswordHashSyncPermissions-cmdlet: phs-machtigingenscript bijgewerkt (Set-ADSyncPasswordHashSyncPermissions) met een optionele parameter 'ADobjectDN'. 
 - Oplossing voor toegankelijkheids bug. De schermlezer zou nu het UX-element beschrijven dat de lijst met forests bevat als **"Forests list**" in plaats van "**Forest List list**"
 - De uitvoer van de schermlezer is bijgewerkt voor sommige items in Azure AD Connect wizard. De kleur van de knopaanwijzer is bijgewerkt om te voldoen aan de contrastvereisten. De Synchronization Service Manager bijgewerkt om te voldoen aan de contrastvereisten.
 - Er is een probleem opgelost met het installeren van AADConnect vanuit een geëxporteerde configuratie met aangepaste extensiekenmerken: er is een voorwaarde toegevoegd om het controleren op extensiekenmerken in het doelschema over te slaan tijdens het toepassen van de synchronisatieregel.
 - De juiste machtigingen worden toegevoegd bij de installatie als de functie Groeps terugschrijven is ingeschakeld.
 - Dubbele prioriteit van standaardsynchronisatieregel bij importeren opgelost
 - Er is een probleem opgelost dat een faseringsfout heeft veroorzaakt tijdens het importeren van V2 API-delta voor een conflicterend object dat is gerepareerd via de statusportal.
 - Er is een probleem opgelost in de synchronisatie-engine waardoor CS-objecten een inconsistente koppelingstoestand hadden
 - Importtellers toegevoegd aan Get-ADSyncConnectorStatistics uitvoer.
 - Probleem opgelost waarbij de selectie van domeinen niet bereikbaar was (eerder geselecteerd) in bepaalde hoekgevallen tijdens de wizard pass2.
 - Het importeren en exporteren van beleid is gewijzigd om te mislukken als de aangepaste regel dubbele prioriteit heeft 
 - Er is een fout opgelost in de domeinselectielogica.
 - Lost een probleem op met build 1.5.18.0 als u mS-DS-ConsistencyGuid als bronanker gebruikt en de regel In hebt gekloond vanuit AD - Groeperen.
 - Nieuwe AADConnect-installaties gebruiken de drempelwaarde voor verwijdering exporteren die is opgeslagen in de cloud als er een beschikbaar is en er geen andere is doorgegeven.
 - Er is een probleem opgelost waarbij AADConnect ad displayName-wijzigingen van hybride gekoppelde apparaten niet leest

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Status van de release
29-07-2020: uitgebracht om te downloaden

### <a name="functional-changes"></a>Functionele wijzigingen
Dit is een release voor het oplossen van fouten. Er zijn geen functionele wijzigingen in deze release.

### <a name="fixed-issues"></a>Opgeloste problemen

- Er is een probleem opgelost waarbij de beheerder naadloze eenmalige aanmelding niet kan inschakelen als het computeraccount AZUREADSSOACC al aanwezig is in Active Directory.
- Er is een probleem opgelost dat een faseringsfout heeft veroorzaakt tijdens het importeren van V2 API-delta voor een conflicterend object dat is hersteld via de statusportal.
- Er is een probleem opgelost in de import/export-configuratie waarbij de uitgeschakelde aangepaste regel is geïmporteerd als ingeschakeld.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Status van de release
10-07-2020: uitgebracht om te downloaden

### <a name="functional-changes"></a>Functionele wijzigingen
Deze release bevat een openbare preview van de functionaliteit voor het exporteren van de configuratie van een bestaande Azure AD Connect server naar een . JSON-bestand dat vervolgens kan worden gebruikt bij het installeren van een Azure AD Connect server om een kopie van de oorspronkelijke server te maken.

Een gedetailleerde beschrijving van deze nieuwe functie vindt u in [dit artikel](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Opgeloste problemen
- Er is een fout opgelost waarbij er een foutmelding zou worden gegeven over de grootte van de lokale database op de gelokaliseerde builds tijdens de upgrade.
- Er is een fout opgelost waarbij er een fout zou zijn opgetreden in de app-gebeurtenissen voor het wisselen van de accountnaam/domeinnaam.
- Er is een fout opgelost waarbij Azure AD Connect niet kon worden geïnstalleerd op een dc, met de fout 'lid niet gevonden'.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Status van de release
07-05-2020: uitgebracht om te downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem opgelost waarbij niet-geselecteerde domeinen onjuist werden geselecteerd in de gebruikersinterface van de wizard als alleen kleinkindcontainers waren geselecteerd.


>[!NOTE]
>Deze versie bevat de nieuwe api Azure AD Connect V2-eindpunt synchroniseren.  Dit nieuwe V2-eindpunt is momenteel beschikbaar als openbare preview.  Deze versie of hoger is vereist voor het gebruik van de nieuwe V2-eindpunt-API.  Als u deze versie installeert, wordt het V2-eindpunt echter niet ingeschakeld. U blijft het V1-eindpunt gebruiken, tenzij u het V2-eindpunt inschakelen.  U moet de stappen onder [V2-eindpunt-API Azure AD Connect synchroniseren (openbare preview)](how-to-connect-sync-endpoint-api-v2.md) volgen om deze in te kunnenschakelen en u aan te kunnen bij de openbare preview.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Status van de release
23-04-2020: uitgebracht om te downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem opgelost dat is geïntroduceerd in build 1.5.20.0 waarbij een tenantbeheerder met MFA DSSO niet kon inschakelen.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Status van de release
20-04-2020: uitgebracht om te downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
Met deze hotfix-build wordt een probleem opgelost in build 1.5.20.0 als u de regel In hebt gekloond vanuit **AD -** groep lid worden en de regel In niet hebt gekloond vanuit AD **- Algemene** groep.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Status van de release
09-04-2020: uitgebracht om te downloaden

### <a name="fixed-issues"></a>Opgeloste problemen
- Met deze hotfix-build wordt een probleem opgelost met build 1.5.18.0 als de functie Groepsfiltering is ingeschakeld en mS-DS-ConsistencyGuid als bronanker wordt gebruikt.
- Er is een probleem opgelost in de PowerShell-module ADSyncConfig, waarbij het aanroepen van de DSACLS-opdracht die wordt gebruikt in alle Cmdlets voor Machtigingen set-ADSync* een van de volgende fouten zou veroorzaken:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Als u de synchronisatieregel In from **AD - Group Join** hebt gekloond en de synchronisatieregel In from AD - Group **Common** niet hebt gekloond en van plan bent een upgrade uit te voeren, moet u de volgende stappen uitvoeren als onderdeel van de upgrade:
> 1. Tijdens de upgrade moet u de optie **Start the synchronization process when configuration completes (Het synchronisatieproces** starten wanneer de configuratie is voltooid) uit.
> 2. Bewerk de synchronisatieregel voor gekloonde joins en voeg de volgende twee transformaties toe:
>     - Stel directe stroom `objectGUID` in op `sourceAnchorBinary` .
>     - Stel de expressiestroom `ConvertToBase64([objectGUID])` in op `sourceAnchor` .     
> 3. Schakel de scheduler in met `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Status van de release
02-04-2020: uitgebracht om te downloaden

### <a name="functional-changes-adsyncautoupgrade"></a>Functionele wijzigingen ADSyncAutoUpgrade 

- Ondersteuning toegevoegd voor de functie mS-DS-ConsistencyGuid voor groepsobjecten. Hiermee kunt u groepen verplaatsen tussen forests of groepen in AD opnieuw verbinden met Azure AD waar de OBJECTID van de AD-groep is gewijzigd, bijvoorbeeld wanneer een AD-server na een ramp opnieuw is opgebouwd. Zie Groepen verplaatsen [tussen forests voor meer informatie.](how-to-connect-migrate-groups.md)
- Het kenmerk mS-DS-ConsistencyGuid wordt automatisch ingesteld voor alle gesynchroniseerde groepen en u hoeft niets te doen om deze functie in teschakelen. 
- De Get-ADSyncRunProfile verwijderd omdat deze niet meer in gebruik is. 
- De waarschuwing die wordt weer gegeven bij het gebruik van een Enterprise Admin- of Domain Admin-account voor het AD DS connector-account is gewijzigd om meer context te bieden. 
- Er is een nieuwe cmdlet toegevoegd om objecten uit de connectorruimte te verwijderen. Het oude CSDelete.exe-hulpprogramma wordt verwijderd en vervangen door de nieuwe Remove-ADSyncCSObject-cmdlet. De Remove-ADSyncCSObject cmdlet gebruikt een CsObject als invoer. Dit object kan worden opgehaald met behulp van de Get-ADSyncCSObject cmdlet .

>[!NOTE]
>Het oude CSDelete.exe is verwijderd en vervangen door de nieuwe Remove-ADSyncCSObject-cmdlet 

### <a name="fixed-issues"></a>Opgeloste problemen

- Er is een fout opgelost in de groep terugschrijven forest/OE-selector bij het opnieuw uitvoeren van de wizard Azure AD Connect na het uitschakelen van de functie. 
- Er is een nieuwe foutpagina geïntroduceerd die wordt weergegeven als de vereiste DCOM-registerwaarden ontbreken met een nieuwe Help-koppeling. Er wordt ook informatie naar logboekbestanden geschreven. 
- Er is een probleem opgelost met het maken van het Azure Active Directory-synchronisatieaccount waarbij het inschakelen van directory-extensies of PHS kan mislukken omdat het account niet is doorgegeven aan alle servicereplica's voordat het gebruik is geprobeerd. 
- Er is een fout opgelost in het compressieprogramma voor synchronisatiefouten dat surrogaattekens niet correct afhandelde. 
- Er is een fout opgelost in de automatische upgrade waardoor de server in de status Scheduler suspended werd. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Status van de release
9-12-2019: release om te downloaden. Niet beschikbaar via automatische upgrade.
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- Wachtwoord-hashsynchronisatie voor Azure AD Domain Services is bijgewerkt om opvulling in Kerberos-hashes goed te kunnen verantwoorden.  Dit biedt een prestatieverbetering tijdens wachtwoordsynchronisatie van Azure AD naar Azure AD Domain Services.
- Er is ondersteuning toegevoegd voor betrouwbare sessies tussen de verificatieagent en Service Bus.
- In deze release wordt TLS 1.2 afgedwongen voor communicatie tussen verificatieagent en cloudservices.
- We hebben een DNS-cache toegevoegd voor websocket-verbindingen tussen verificatieagent en cloudservices.
- We hebben de mogelijkheid toegevoegd om een specifieke agent vanuit de cloud te targeten om te testen op agentconnectiviteit.

### <a name="fixed-issues"></a>Opgeloste problemen
- Release 1.4.18.0 had een fout waarbij de PowerShell-cmdlet voor DSSO de aanmeldingsreferenties van Windows gebruikte in plaats van de beheerdersreferenties die zijn opgegeven tijdens het uitvoeren van ps. Als gevolg hiervan is het niet mogelijk om DSSO in meerdere forest in te Azure AD Connect gebruikersinterface. 
- Er is een oplossing voor het gelijktijdig inschakelen van eenmalige aanmelding in het hele forest via Azure AD Connect gebruikersinterface

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Status van de release
08-11-2019: uitgebracht om te downloaden. Niet beschikbaar via automatische upgrade.

>[!IMPORTANT]
>Als gevolg van een interne schemawijziging in deze versie van Azure AD Connect, moet u uw MSOnline PowerShell-module bijwerken naar versie 1.1.183.57 of hoger als u configuratie-instellingen voor AD FS-vertrouwensrelatie beheert met MSOnline PowerShell

### <a name="fixed-issues"></a>Opgeloste problemen

Met deze versie wordt een probleem opgelost met bestaande hybride Azure AD-apparaten. Deze release bevat een nieuwe regel voor apparaatsynchronisatie die dit probleem corrigeert.
Houd er rekening mee dat deze regelwijziging kan leiden tot het verwijderen van verouderde apparaten uit Azure AD. Dit is geen reden tot zorg, omdat deze apparaatobjecten niet door Azure AD worden gebruikt tijdens de autorisatie van voorwaardelijke toegang. Voor sommige klanten kan het aantal apparaten dat via deze regelwijziging wordt verwijderd, de drempelwaarde voor verwijdering overschrijden. Als u ziet dat het verwijderen van apparaatobjecten in Azure AD de drempelwaarde voor het verwijderen van export overschrijdt, wordt u aangeraden de verwijderingen door te laten gaan. [Verwijderen toestaan wanneer ze de drempelwaarde voor verwijdering overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Status van de release
28-9-2019: uitgebracht voor automatische upgrade om tenants te selecteren. Niet beschikbaar om te downloaden.

Met deze versie wordt een fout opgelost waarbij sommige servers die automatisch zijn bijgewerkt van een eerdere versie naar 1.4.18.0 en problemen ondervonden met selfservice voor wachtwoord opnieuw instellen (SSPR) en wachtwoord terugschrijven.

### <a name="fixed-issues"></a>Opgeloste problemen

Onder bepaalde omstandigheden hebben servers die automatisch zijn bijgewerkt naar versie 1.4.18.0 de selfservice voor wachtwoord opnieuw instellen en wachtwoord terugschrijven niet ingeschakeld nadat de upgrade is voltooid. Deze release voor automatische upgrade lost dit probleem op en schakelt selfservice voor wachtwoord opnieuw instellen en Wachtwoord terugschrijven opnieuw in.

We hebben een fout opgelost in het compressieprogramma voor synchronisatiefouten dat surrogaattekens niet correct afhandelde.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>We onderzoeken een incident waarbij sommige klanten een probleem ondervinden met bestaande hybride Azure AD-apparaten na een upgrade naar deze versie van Azure AD Connect. Klanten die Hybrid Azure AD Join hebben geïmplementeerd, wordt geadviseerd om de upgrade naar deze versie uit te stellen totdat de hoofdoorzaak van deze problemen volledig is begrepen en opgelost. Meer informatie wordt zo snel mogelijk verstrekt.

>[!IMPORTANT]
>Met deze versie van Azure AD Connect sommige klanten mogelijk zien dat sommige of al hun Windows-apparaten uit Azure AD verdwijnen. Dit is geen reden tot zorg, omdat deze apparaat-id's niet door Azure AD worden gebruikt tijdens de autorisatie van voorwaardelijke toegang. Zie Understanding [Azure AD Connect 1.4.xx.x device disappears (Inzicht in het verdwijnen van apparaten met 1.4.xx.x) voor meer informatie](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Status van de release
25-9-2019: alleen uitgebracht voor automatische upgrade.

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- Nieuwe hulpprogramma's voor probleemoplossing helpen bij het oplossen van problemen met 'gebruiker niet synchroniseren', 'niet synchroniseren van groep' of 'groepslid synchroniseert niet'.
- Voeg ondersteuning toe voor nationale clouds in Azure AD Connect script voor probleemoplossing.
- Klanten moeten worden geïnformeerd dat de afgeschafte WMI-eindpunten voor MIIS_Service zijn verwijderd. Alle WMI-bewerkingen moeten nu worden uitgevoerd via PS-cmdlets.
- Beveiligingsverbetering door beperkte delegering opnieuw in te zetten voor het AZUREADSSOACC-object.
- Wanneer u een synchronisatieregel toevoegt of bewerkt, worden de kenmerken automatisch toegevoegd aan de connector als er kenmerken in de regel worden gebruikt die wel in het connectorschema staan maar niet aan de connector zijn toegevoegd. Hetzelfde geldt voor het objecttype dat de regel beïnvloedt. Als er iets aan de connector wordt toegevoegd, wordt de connector in de volgende synchronisatiecyclus gemarkeerd voor volledige import.
- Het gebruik van een ondernemings- of domeinbeheerder als connectoraccount wordt niet meer ondersteund in nieuwe Azure AD Connect implementaties. Huidige Azure AD Connect implementaties met een Ondernemings- of domeinbeheerder als het connectoraccount worden niet beïnvloed door deze release.
- In Synchronization Manager wordt een volledige synchronisatie uitgevoerd bij het maken/bewerken/verwijderen van regels. Er wordt een pop-up weergegeven bij een regelwijziging die de gebruiker op de hoogte stelt als volledige import of volledige synchronisatie wordt uitgevoerd.
- Er zijn oplossingsstappen voor wachtwoordfouten toegevoegd aan de pagina Connectors > eigenschappen > connectiviteit.
- Er is een afschaffingswaarschuwing toegevoegd voor synchronisatieservicebeheer op de eigenschappenpagina van de connector. Deze waarschuwing waarschuwt de gebruiker dat wijzigingen moeten worden aangebracht via de wizard Azure AD Connect maken.
- Er is een nieuwe fout toegevoegd voor problemen met het wachtwoordbeleid van een gebruiker.
- Voorkom onjuiste configuratie van groepsfilters op domein- en OE-filters. Bij groepsfiltering wordt een fout weergegeven wanneer het domein/de OE van de ingevoerde groep al is gefilterd en wordt voorkomen dat de gebruiker verder gaat totdat het probleem is opgelost.
- Gebruikers kunnen geen connector meer maken voor Active Directory Domain Services of Windows Azure Active Directory in de Synchronization Service Manager gebruikersinterface.
- Toegankelijkheid van aangepaste UI-besturingselementen in de Synchronization Service Manager.
- Zes federatiebeheertaken ingeschakeld voor alle aanmeldingsmethoden in Azure AD Connect.  (Voorheen was alleen de taak AD FS TLS/SSL-certificaat bijwerken beschikbaar voor alle aanmeldingen.)
- Er is een waarschuwing toegevoegd bij het wijzigen van de aanmeldingsmethode van federatie in PHS of PTA dat alle Azure AD-domeinen en -gebruikers worden geconverteerd naar beheerde verificatie.
- Certificaten voor token-ondertekening zijn verwijderd uit de taak 'Azure AD opnieuw instellen AD FS vertrouwensrelatie' en een afzonderlijke subtaak toegevoegd om deze certificaten bij te werken.
- Er is een nieuwe federatiebeheertaak met de naam Certificaten beheren toegevoegd, die subtaken heeft voor het bijwerken van de TLS- of token-ondertekeningscertificaten voor de AD FS farm.
- Er is een nieuwe subtaak voor federatiebeheer toegevoegd met de naam Primaire server opgeven, waarmee beheerders een nieuwe primaire server kunnen opgeven voor de AD FS farm.
- Er is een nieuwe federatiebeheertaak met de naam Servers beheren toegevoegd, die subtaken heeft voor het implementeren van een AD FS-server, het implementeren van een Web toepassingsproxy-server en het opgeven van de primaire server.
- Er is een nieuwe federatiebeheertaak toegevoegd met de naam 'Federatieconfiguratie weergeven' die de huidige instellingen AD FS weergeven.  (Vanwege deze toevoeging zijn AD FS verwijderd van de pagina Uw oplossing controleren.)

### <a name="fixed-issues"></a>Opgeloste problemen
- Probleem met synchronisatiefouten opgelost voor het scenario waarin een gebruikersobject dat het bijbehorende contactobject over neemt, een zelfverwijzing heeft (bijvoorbeeld gebruiker is zijn eigen manager).
- Help-pop-ups worden nu op toetsenbordfocus weer geven.
- Voor automatische upgrade moet u de app beëindigen en doorgaan met de upgrade als er een conflicterende app van 6 uur wordt uitgevoerd.
- Beperk het aantal kenmerken dat een klant kan selecteren tot 100 per object bij het selecteren van directory-extensies. Dit voorkomt dat de fout optreedt tijdens het exporteren, omdat Azure maximaal 100 extensiekenmerken per object heeft.
- Er is een fout opgelost om het AD-connectiviteitsscript robuuster te maken.
- Er is een fout opgelost om Azure AD Connect te installeren op een computer met behulp van een bestaande WCF-service met named pipes.
- Verbeterde diagnostische gegevens en probleemoplossing voor groepsbeleidsregels waardoor de ADSync-service niet kan worden starten wanneer deze voor het eerst wordt geïnstalleerd.
- Er is een fout opgelost waarbij de weergavenaam voor een Windows-computer onjuist werd geschreven.
- Er is een fout opgelost waarbij het type besturingssysteem voor een Windows-computer onjuist is geschreven.
- Er is een fout opgelost waarbij niet-Windows 10 computers onverwacht werden gesynchroniseerd. Houd er rekening mee dat het effect van deze wijziging is dat niet-Windows-10-computers die eerder zijn gesynchroniseerd, nu worden verwijderd. Dit heeft geen invloed op functies, omdat de synchronisatie van Windows-computers alleen wordt gebruikt voor hybride Azure AD-domein join, wat alleen werkt voor Windows-10-apparaten.
- Er zijn verschillende nieuwe (interne) cmdlets toegevoegd aan de ADSync PowerShell-module.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Er is een bekend probleem met het upgraden van Azure AD Connect van een eerdere versie naar 1.3.21.0 waarbij de Microsoft 365-portal niet de bijgewerkte versie weerspiegelt, ook al Azure AD Connect bijgewerkt.
>
> U kunt dit oplossen door de **AdSync-module** te importeren en vervolgens de `Set-ADSyncDirSyncConfiguration` PowerShell-cmdlet uit te voeren op Azure AD Connect server.  U kunt de volgende stappen gebruiken:
>
>1. Open PowerShell in de beheerdersmodus.
>2. Voer `Import-Module "ADSync"` uit.
>3. Voer `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` uit.
 
### <a name="release-status"></a>Status van de release 

14-05-2019: uitgebracht om te downloaden

### <a name="fixed-issues"></a>Opgeloste problemen 

- Er is een beveiligingsprobleem met verhoging van bevoegdheden opgelost dat Microsoft Azure Active Directory Connect build 1.3.20.0.  Met dit beveiligingsprobleem kan een aanvaller onder bepaalde voorwaarden twee PowerShell-cmdlets uitvoeren in de context van een bevoegde account en bevoorrechte acties uitvoeren.  Deze beveiligingsupdate lost het probleem op door deze cmdlets uit te uitschakelen. Zie beveiligingsupdate [voor meer informatie.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
