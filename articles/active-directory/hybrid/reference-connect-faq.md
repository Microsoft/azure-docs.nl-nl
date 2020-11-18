---
title: Azure Active Directory Connect Veelgestelde vragen-| Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff2651d2fed9c307bbb90623572b4a74d750d3a4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836067"
---
# <a name="azure-active-directory-connect-faq"></a>Veelgestelde vragen over Azure Active Directory Connect

## <a name="general-installation"></a>Algemene installatie

**V: hoe kan ik mijn Azure AD Connect-server beveiligen om de kwets baarheid voor beveiliging te verlagen?**

Micro soft raadt aan uw Azure AD Connect-server te beveiligen om het beveiligings risico te verminderen voor dit kritieke onderdeel van uw IT-omgeving.  Als u de onderstaande aanbevelingen volgt, worden de beveiligings Risico's voor uw organisatie verminderd.

* Azure AD Connect implementeren op een server die lid is van een domein en de beheerders toegang tot domein beheerders of andere nauw keurig beheerde beveiligings groepen beperken

Voor meer informatie zie: 

* [Beheerders groepen beveiligen](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Ingebouwde Administrator-accounts beveiligen](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Beveiligings verbetering en-ondersteuning door kwets baarheid te verminderen](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [De Active Directory kwets baarheid verminderen](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**V: de installatie werkt als de Azure Active Directory (Azure AD) globale beheerder met twee ledige verificatie (twee ledige) is ingeschakeld?**  
Vanaf de builds van februari 2016 wordt dit scenario ondersteund.

**V: is er een manier om Azure AD Connect zonder toezicht te installeren?**  
Azure AD Connect-installatie wordt alleen ondersteund wanneer u de installatie wizard gebruikt. Een stille installatie zonder toezicht wordt niet ondersteund.

**V: Ik heb een forest waarbij er geen contact kan worden gemaakt met een domein. Azure AD Connect Hoe kan ik installeren?**  
Vanaf de builds van februari 2016 wordt dit scenario ondersteund.

**V: de Health Agent Azure Active Directory Domain Services (Azure AD DS) werkt op Server Core?**  
Ja. Nadat u de agent hebt geïnstalleerd, kunt u het registratie proces volt ooien met behulp van de volgende Power shell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**V: ondersteunt Azure AD Connect synchronisatie van twee domeinen naar een Azure AD?**  
Ja, dit scenario wordt ondersteund. Raadpleeg [meerdere domeinen](how-to-connect-install-multiple-domains.md).
 
**V: kunt u meerdere connectors voor hetzelfde Active Directory domein in Azure AD Connect hebben?**  
Nee, meerdere connectors voor hetzelfde AD-domein worden niet ondersteund. 

**V: kan ik de Azure AD Connect Data Base van de lokale data base verplaatsen naar een extern SQL Server exemplaar?**   
Ja, de volgende stappen bevatten algemene richt lijnen voor het uitvoeren van deze procedure. Er wordt momenteel gewerkt aan een gedetailleerder document.
1. Maak een back-up van de LocalDB ADSync-data base.
De eenvoudigste manier om dit te doen is door SQL Server Management Studio geïnstalleerd op dezelfde computer te gebruiken als Azure AD Connect. Maak verbinding met *(LocalDb) .\ADSync* en maak vervolgens een back-up van de ADSync-data base.

2. Herstel de ADSync-Data Base naar uw externe SQL Server-exemplaar.

3. Installeer Azure AD Connect op basis van de bestaande [externe SQL database](how-to-connect-install-existing-database.md).
   In dit artikel wordt beschreven hoe u migreert naar met behulp van een lokale SQL database. Als u migreert naar met behulp van een externe SQL database, moet u in stap 5 van het proces ook een bestaand service account invoeren waarmee de Windows Sync-Service wordt uitgevoerd. Dit service account voor de synchronisatie-engine wordt hier beschreven:
   
      **Een bestaand service account gebruiken**: standaard gebruikt Azure AD Connect een Virtual service-account voor de synchronisatie services die u wilt gebruiken. Als u een exemplaar van een externe SQL Server gebruikt of een proxy gebruikt die verificatie vereist, gebruikt u een beheerd service account of een service account in het domein en weet u het wacht woord. Voer in dat geval het te gebruiken account in. Zorg ervoor dat gebruikers die de installatie uitvoeren, systeem beheerders zijn in SQL, zodat aanmeldings referenties voor het service account kunnen worden gemaakt. Zie [Azure AD Connect accounts en-machtigingen](reference-connect-accounts-permissions.md#adsync-service-account)voor meer informatie. 
   
      In de laatste versie kan de inrichting van de database out-of-band worden uitgevoerd door de SQL-beheerder en vervolgens worden geïnstalleerd door de Azure AD Connect-beheerder met eigendomsrechten voor de database. Zie [Install Azure AD Connect met behulp van SQL delegated Administrator Permissions](how-to-connect-install-sql-delegation.md)voor meer informatie.

Om alles te blijven gebruiken, raden we aan dat gebruikers die Azure AD Connect systeem beheerders in SQL zijn geïnstalleerd. Met recente builds kunt u nu echter gebruikmaken van gedelegeerde SQL-beheerders, zoals beschreven in [Install Azure AD Connect met behulp van SQL delegated Administrator Permissions](how-to-connect-install-sql-delegation.md).

**V: wat zijn de aanbevolen procedures uit het veld?**  

Hieronder vindt u een informatief document waarin een aantal van de best practices wordt geboden die technisch, ondersteuning en onze consultants hebben ontwikkeld over de jaren.  Dit wordt weer gegeven in een lijst met opsommings tekens waarnaar snel kan worden verwezen.  Hoewel deze lijst moet worden uitgebreid, zijn er mogelijk nog meer aanbevolen procedures die dit mogelijk niet in de lijst hebben gemaakt.

- Als u volledige SQL gebruikt, moet dit lokaal versus extern blijven
    - Minder hops
    - Gemakkelijker problemen oplossen
    - Minder complexiteit
    - Resources moeten worden toegewezen aan SQL en overhead toestaan voor Azure AD Connect en besturings systeem
- Proxy altijd overs Laan als u de proxy mogelijk niet kunt omzeilen, moet u ervoor zorgen dat de time-outwaarde langer is dan vijf minuten.
- Als de proxy is vereist, moet u de proxy toevoegen aan het machine.config-bestand
- Zorg dat u zich bewust bent van lokale SQL-taken en-onderhoud en hoe ze van invloed zijn op Azure AD Connect, met name opnieuw indexeren
- Zorg ervoor dat DNS extern kan worden omgezet
- Zorg ervoor dat de [server specificaties](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) per aanbeveling zijn, ongeacht of u fysieke of virtuele servers gebruikt.
- Zorg ervoor dat als u een virtuele server gebruikt die vereist is voor de benodigde resources
- Zorg ervoor dat de schijf-en schijf configuratie voldoen aan aanbevolen procedures voor SQL Server
- Azure AD Connect Health voor bewaking installeren en configureren
- Gebruik de drempel waarde voor verwijderen die is ingebouwd in Azure AD Connect.
- Controleer zorgvuldig de release-updates die moeten worden voor bereid voor alle wijzigingen en nieuwe kenmerken die kunnen worden toegevoegd
- Back-up maken van alles
    - Back-upsleutels
    - Back-upsynchronisaties regels
    - Configuratie van de back-upserver
    - Back-upSQL Database
- Zorg ervoor dat er geen back-upagenten van derden zijn die back-ups maken van SQL zonder de SQL VSS-schrijver (gemeen schappelijk in virtuele servers met moment opnamen van derden)
- De hoeveelheid aangepaste synchronisatie regels beperken die worden gebruikt voor het toevoegen van complexiteit
- Azure AD Connect-servers behandelen als laag 0-servers
- Leery van het wijzigen van Cloud synchronisatie regels zonder goed beeld van de impact en de juiste zakelijke Stuur Programma's
- Zorg ervoor dat de juiste URL en Firewall poorten zijn geopend voor ondersteuning van Azure AD Connect en Azure AD Connect Health
- Gebruik het gefilterde kenmerk in de cloud om te voor komen dat fantoom objecten worden gebruikt
- Zorg ervoor dat u met de staging-server de Azure AD Connect-configuratie Documenter gebruikt voor consistentie tussen servers
- Faserings servers moeten zich in afzonderlijke Data Centers (fysieke locaties) bevinden
- Staging-servers zijn niet bedoeld als een oplossing met hoge Beschik baarheid, maar u kunt meerdere staging-servers hebben
- Introductie van een ' vertragings-' staging-server kunnen een mogelijke downtime in geval van een fout oplossen
- Test en valideer eerst alle upgrades op de staging-server
- Valideer altijd de export voordat u overschakelt naar de staging-server.  Maak gebruik van de staging-server voor volledige import bewerkingen en volledige synchronisaties om de impact van het bedrijf te verminderen
- Bewaar versie consistentie tussen Azure AD Connect servers zoveel mogelijk 

**V: kan ik toestaan dat Azure AD Connect het Azure AD Connector-account op de werkgroepcomputer maakt?**
Nee.  De computer moet lid zijn van een domein om Azure AD Connect in staat te stellen het Azure AD Connector-account automatisch te maken.  

## <a name="network"></a>Netwerk
**V: Ik heb een firewall, netwerk apparaat of iets anders dat de tijd beperkt die de verbindingen in mijn netwerk kunnen blijven staan. Wat moet de drempel waarde voor de time-out aan de client zijde zijn wanneer ik Azure AD Connect gebruik?**  
Alle netwerk software, fysieke apparaten of andere onderdelen die de maximale tijds duur voor het openen van verbindingen beperken, moeten een drempel van ten minste vijf minuten (300 seconden) gebruiken voor de verbinding tussen de server waarop de Azure AD Connect-client is geïnstalleerd en Azure Active Directory. Deze aanbeveling is ook van toepassing op alle eerder gepubliceerde micro soft Identiteitssynchronisatie-hulpprogram ma's.

**V: zijn domeinen met één label (SLDs) ondersteund?**  
We raden u ten zeerste aan deze netwerk configuratie te gebruiken ([Zie artikel](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), waarbij Azure AD Connect synchronisatie met een domein met één label wordt ondersteund, mits de netwerk configuratie voor het domein met één niveau goed werkt.

**V: zijn forests met niet-aaneengesloten AD-domeinen ondersteund?**  
Nee, Azure AD Connect biedt geen ondersteuning voor on-premises forests die niet-aaneengesloten naam ruimten bevatten.

**V: ' gestippelde ' NetBIOS-namen worden ondersteund?**  
Nee, Azure AD Connect biedt geen ondersteuning voor on-premises forests of domeinen met de NetBIOS-naam een punt (.).

**V: wordt een pure IPv6-omgeving ondersteund?**  
Nee, Azure AD Connect biedt geen ondersteuning voor een zuivere IPv6-omgeving.

**Q:I een omgeving met meerdere forests en het netwerk tussen de twee forests maakt gebruik van NAT (netwerkadresomzetting). Wordt Azure AD Connect tussen deze twee forests gebruikt?**</br>
Nee, het gebruik van Azure AD Connect via NAT wordt niet ondersteund. 

## <a name="federation"></a>Federatie
**V: wat moet ik doen als ik een e-mail bericht ontvang waarin wordt gevraagd mijn Microsoft 365-certificaat te vernieuwen?**  
Zie [certificaten vernieuwen](how-to-connect-fed-o365-certs.md)voor meer informatie over het vernieuwen van het certificaat.

**V: Ik heb Relying Party automatisch bijwerken ingesteld voor de Microsoft 365 Relying Party. Moet ik actie ondernemen wanneer mijn token handtekening certificaat automatisch wordt doorgevoerd?**  
Gebruik de richt lijnen die worden beschreven in het artikel [certificaten vernieuwen](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Omgeving
**V: wordt het ondersteund om de naam van de server te wijzigen nadat Azure AD Connect is geïnstalleerd?**  
Nee. Als u de server naam wijzigt, kan de synchronisatie-engine geen verbinding maken met het SQL database-exemplaar en kan de service niet worden gestart.

**V: worden NGC-synchronisatie regels (Next generatie Cryptographic) ondersteund op een computer met FIPS-functionaliteit?**  
Nee.  Dit wordt niet ondersteund.

**Nils. Als ik een gesynchroniseerd apparaat heb uitgeschakeld (bijvoorbeeld: HAADJ) in het Azure Portal, waarom wordt het opnieuw ingeschakeld?**<br>
Gesynchroniseerde apparaten kunnen worden gemaakt of gemasterd op locatie. Als een gesynchroniseerd apparaat op locatie is ingeschakeld, wordt het mogelijk opnieuw ingeschakeld in het Azure Portal, zelfs als dit eerder is uitgeschakeld door een beheerder. Als u een gesynchroniseerd apparaat wilt uitschakelen, gebruikt u de on-premises Active Directory om het computer account uit te scha kelen.

**Nils. Als ik de aanmelding van gebruikers op de Microsoft 365 of de Azure AD-Portal blok keren voor gesynchroniseerde gebruikers, waarom wordt de blok kering opgeheven bij het aanmelden.**<br>
Gesynchroniseerde gebruikers kunnen worden gemaakt of gemasterd op locatie. Als het account is ingeschakeld op locatie, kan het de blok kering opheffen van het aanmeldings blok dat door de beheerder is geplaatst.

## <a name="identity-data"></a>Identiteits gegevens
**V: Waarom komt het kenmerk userPrincipalName (UPN) in azure AD niet overeen met de on-premises UPN?**  
Zie de volgende artikelen voor meer informatie:

* [Gebruikers namen in Microsoft 365, Azure of intune komen niet overeen met de on-premises UPN of alternatieve aanmeldings-ID](https://support.microsoft.com/kb/2523192)
* [Wijzigingen worden niet gesynchroniseerd met het Azure Active Directory Sync-hulp programma nadat u de UPN van een gebruikers account hebt gewijzigd om een ander federatief domein te gebruiken](https://support.microsoft.com/kb/2669550)

U kunt Azure AD ook zo configureren dat de synchronisatie-engine de UPN kan bijwerken, zoals wordt beschreven in [Azure AD Connect-synchronisatie service-functies](how-to-connect-syncservice-features.md).

**V: is het mogelijk om te voldoen aan een on-premises Azure AD-groep of een contact object met een bestaand Azure AD-groeps-of-contact object?**  
Ja, deze zachte overeenkomst is gebaseerd op de proxyAddress attribuut. Zacht zoeken wordt niet ondersteund voor groepen waarvoor geen e-mail functionaliteit is ingeschakeld.

**V: is it ondersteund om hand matig het kenmerk ImmutableId in te stellen voor een bestaande Azure AD-groep of-contact object om het vast te maken met een on-premises Azure AD-groep of-contact object?**  
Nee, u kunt het kenmerk ImmutableId niet hand matig instellen voor een bestaande Azure AD-groep of-contact object naar een harde overeenkomst. dit wordt momenteel niet ondersteund.

## <a name="custom-configuration"></a>Aangepaste configuratie
**V: waar zijn de Power shell-cmdlets voor Azure AD Connect gedocumenteerd?**  
Met uitzonde ring van de cmdlets die zijn gedocumenteerd op deze site, worden andere Power shell-cmdlets die in Azure AD Connect worden gevonden, niet ondersteund voor gebruik door klanten.

**V: kan ik de optie server exporteren/server importeren in Synchronization Service Manager gebruiken om de configuratie tussen servers te verplaatsen?**  
Nee. Met deze optie worden niet alle configuratie-instellingen opgehaald en mag niet worden gebruikt. In plaats daarvan gebruikt u de wizard om de basis configuratie op de tweede server te maken en gebruikt u de editor voor synchronisatie regels om Power shell-scripts te genereren om een aangepaste regel tussen servers te verplaatsen. Zie voor meer informatie [Swing migratie](how-to-upgrade-previous-version.md#swing-migration).

**V: kunnen wacht woorden worden opgeslagen in de cache voor de aanmeldings pagina van Azure, en kan deze caching worden voor komen omdat deze een invoer element voor een wacht woord bevat met het kenmerk *' onwaar '* in de tabel ' onwaar '?**  
Op dit moment wordt het wijzigen van de HTML-kenmerken van het **wachtwoord** veld, met inbegrip van de tag AutoAanvullen, niet ondersteund. Er wordt momenteel gewerkt aan een functie die aangepaste Java script toestaat, waarmee u een wille keurig kenmerk aan het **wachtwoord** veld kunt toevoegen.

**V: de aanmeldings pagina van Azure bevat de gebruikers namen van gebruikers die eerder zijn aangemeld. Kan dit gedrag worden uitgeschakeld?**  
Op dit moment wordt het wijzigen van de HTML-kenmerken van het veld **wachtwoord** invoer, met inbegrip van de tag AutoAanvullen, niet ondersteund. Er wordt momenteel gewerkt aan een functie die aangepaste Java script toestaat, waarmee u een wille keurig kenmerk aan het **wachtwoord** veld kunt toevoegen.

**V: is er een manier om gelijktijdige sessies te voor komen?**  
Nee.

## <a name="auto-upgrade"></a>Automatische upgrade

**V: wat zijn de voor delen en gevolgen van het gebruik van automatische upgrade?**  
We adviseren alle klanten om automatische upgrade voor hun Azure AD Connect-installatie in te scha kelen. Het voor deel is dat u altijd de meest recente patches ontvangt, inclusief beveiligings updates voor beveiligings problemen die in Azure AD Connect zijn gevonden. Het upgrade proces is eenvoudige methode en gebeurt automatisch zodra er een nieuwe versie beschikbaar is. Veel duizenden Azure AD Connect klanten gebruiken automatische upgrades bij elke nieuwe release.

Het proces voor automatische upgrades bepaalt altijd eerst of een installatie in aanmerking komt voor automatische upgrade. Als deze in aanmerking komt, wordt de upgrade uitgevoerd en getest. Het proces omvat ook het zoeken naar aangepaste wijzigingen aan regels en specifieke omgevings factoren. Als de tests aantonen dat een upgrade is mislukt, wordt de vorige versie automatisch hersteld.

Afhankelijk van de grootte van de omgeving kan het proces enkele uren duren. Terwijl de upgrade wordt uitgevoerd, gebeurt er geen synchronisatie tussen Windows Server Active Directory en Azure AD.

**V: Ik heb een e-mail bericht ontvangen waarin staat dat mijn automatische upgrade niet meer werkt en dat ik een nieuwe versie moet installeren. Waarom moet ik dit doen?**  
Vorig jaar hebben we een versie van Azure AD Connect uitgebracht die in bepaalde omstandigheden de functie voor automatisch bijwerken op uw server heeft uitgeschakeld. We hebben het probleem opgelost in Azure AD Connect versie 1.1.750.0. Als het probleem zich heeft voorgedaan, kunt u dit verhelpen door een Power shell-script uit te voeren om het te herstellen of door hand matig een upgrade naar de nieuwste versie van Azure AD Connect. 

Als u het Power shell-script wilt uitvoeren, [downloadt u het script](https://aka.ms/repairaadconnect) en voert u dit uit op uw Azure AD Connect server in een beheer-Power shell-venster. [Bekijk deze korte video](https://aka.ms/repairaadcau)voor meer informatie over het uitvoeren van het script.

Als u hand matig wilt bijwerken, moet u de nieuwste versie van het AADConnect.msi-bestand downloaden en uitvoeren.
 
-  Als uw huidige versie ouder is dan 1.1.750.0, [down load en upgrade dan naar de nieuwste versie](https://www.microsoft.com/download/details.aspx?id=47594).
- Als uw Azure AD Connect-versie 1.1.750.0 of hoger is, is er geen verdere actie vereist. U gebruikt al de versie die de oplossing voor automatische upgrades bevat. 

**V: Ik heb een e-mail adres ontvangen waarmee ik kan upgraden naar de nieuwste versie om de automatische upgrade opnieuw in te scha kelen. Ik gebruik versie 1.1.654.0. Moet ik een upgrade uitvoeren?**  
Ja, u moet een upgrade uitvoeren naar versie 1.1.750.0 of hoger om de automatische upgrade opnieuw in te scha kelen. [Down load en installeer de nieuwste versie](https://www.microsoft.com/download/details.aspx?id=47594).

**V: Ik heb een e-mail adres ontvangen waarmee ik kan upgraden naar de nieuwste versie om de automatische upgrade opnieuw in te scha kelen. Als ik Power shell heb gebruikt om automatische upgrade in te scha kelen, moet ik dan nog steeds de nieuwste versie installeren?**  
Ja, u moet nog steeds een upgrade uitvoeren naar versie 1.1.750.0 of hoger. Het inschakelen van de service voor automatisch bijwerken met Power shell verkleint het probleem voor automatische upgrades dat is gevonden in versies vóór 1.1.750.0.

**V: Ik wil een upgrade uitvoeren naar een nieuwere versie, maar ik weet niet met wie Azure AD Connect is geïnstalleerd en de gebruikers naam en het wacht woord zijn niet aanwezig. Hebben we dit nodig?**
U hoeft niet de gebruikers naam en het wacht woord te weten die voor het eerst zijn gebruikt voor het bijwerken van Azure AD Connect. Gebruik een Azure AD-account met de rol globale beheerder.

**V: hoe kan ik zien welke versie van Azure AD Connect Ik gebruik?**  
Als u wilt controleren welke versie van Azure AD Connect op uw server is geïnstalleerd, gaat u naar configuratie scherm en zoekt u de geïnstalleerde versie van Microsoft Azure AD verbinding maken door **Program** ma's en onderdelen van het selectie vakje te selecteren  >  **Programs and Features**, zoals hier wordt weer gegeven:

![Azure AD Connect-versie in het configuratie scherm](./media/reference-connect-faq/faq1.png)

**V: Hoe kan ik upgrade naar de nieuwste versie van Azure AD Connect?**  
Zie [Azure AD Connect: upgrade van een eerdere versie naar de nieuwste](how-to-upgrade-previous-version.md)versie voor meer informatie over het uitvoeren van een upgrade naar de meest recente. 

**V: er is al een upgrade uitgevoerd naar de nieuwste versie van Azure AD Connect vorig jaar. Moeten we de upgrade opnieuw uitvoeren?**  
Het Azure AD Connect team maakt regel matig updates van de service. Het is belang rijk om uw server up-to-date te houden met de meest recente versie om te profiteren van problemen met oplossingen en beveiligings updates en over nieuwe functies. Als u automatisch bijwerken inschakelt, wordt uw software versie automatisch bijgewerkt. Zie [Azure AD Connect: release geschiedenis](reference-connect-version-history.md)van de versie om de release geschiedenis van Azure AD Connect te vinden.

**V: hoe lang duurt het om de upgrade uit te voeren en wat is de invloed op mijn gebruikers?**  
De tijd die nodig is om te upgraden, is afhankelijk van de grootte van uw Tenant. Voor grotere organisaties is het wellicht het beste om de upgrade in de avond of het weekend uit te voeren. Tijdens de upgrade vindt er geen synchronisatie activiteiten plaats.

**V: Ik ben ervan overtuigd dat ik een upgrade heb uitgevoerd naar Azure AD Connect, maar de Office-Portal vermeldt wel DirSync. Waarom is dit?**  
Het Office-team werkt met de updates van de Office-Portal om de huidige product naam weer te geven. Het bevat niet de synchronisatie tool die u gebruikt.

**V: mijn automatische upgrade status zegt ' Suspended '. Waarom is het opgeschort? Moet ik deze inschakelen?**  
Er is een bug geïntroduceerd in een eerdere versie die onder bepaalde omstandigheden de status voor automatische upgrades is ingesteld op onderbroken. Het hand matig inschakelen van het programma is technisch mogelijk, maar er zijn verschillende complexe stappen vereist. U kunt het beste de nieuwste versie van Azure AD Connect installeren.

**V: mijn bedrijf heeft strenge vereisten voor wijzigings beheer en ik wil bepalen wanneer het is gepusht. Kan ik controleren wanneer de automatische upgrade wordt gestart?**  
Nee, er is vandaag geen enkele functie. De functie wordt geëvalueerd voor een toekomstige release.

**V: krijg ik een e-mail als de automatische upgrade is mislukt? Hoe weet ik dat de toepassing is voltooid?**  
U wordt niet op de hoogte gebracht van het resultaat van de upgrade. De functie wordt geëvalueerd voor een toekomstige release.

**V: publiceert u een tijd lijn voor wanneer u automatische upgrades wilt pushen?**  
Automatische upgrade is de eerste stap in het release proces van een nieuwere versie. Wanneer er een nieuwe release is, worden de upgrades automatisch gepusht. Nieuwere versies van Azure AD Connect worden vooraf aangekondigd in het [Azure AD-schema](../fundamentals/whats-new.md).

**V: werkt de automatische upgrade ook Azure AD Connect Health?**  
Ja, de automatische upgrade werkt ook Azure AD Connect Health.

**V: wilt u ook Azure AD Connect servers in de faserings modus automatisch bijwerken?**  
Ja, u kunt een Azure AD Connect-server die zich in de faserings modus bevindt, automatisch bijwerken.

**V: als de automatische upgrade mislukt en mijn Azure AD Connect-server niet start, wat moet ik doen?**  
In zeldzame gevallen wordt de Azure AD Connect-service niet gestart nadat u de upgrade hebt uitgevoerd. In dergelijke gevallen wordt het probleem doorgaans opgelost door de server opnieuw op te starten. Als de Azure AD Connect-service nog steeds niet wordt gestart, opent u een ondersteunings ticket. Zie [een service aanvraag maken om contact op te nemen met Microsoft 365 ondersteuning](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support)voor meer informatie. 

**V: Ik weet niet wat de Risico's zijn wanneer ik een upgrade naar een nieuwere versie van Azure AD Connect. Kunt u mij bellen om me te helpen met de upgrade?**  
Als u hulp nodig hebt bij het upgraden naar een nieuwere versie van Azure AD Connect, opent u een ondersteunings ticket bij [een service aanvraag maken om contact op te nemen met Microsoft 365 ondersteuning](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Operationele best practice    
Hieronder vindt u enkele aanbevolen procedures voor het synchroniseren van de Windows Server-Active Directory en Azure Active Directory.

**Multi-factor Authentication Toep assen voor alle gesynchroniseerde accounts** Met Azure AD Multi-Factor Authentication kunt u de toegang tot gegevens en toepassingen beschermen terwijl u eenvoudiger bent voor gebruikers. Het biedt extra beveiliging door een tweede vorm van verificatie te vereisen en sterke verificatie te bieden met behulp van een bereik aan gebruiks vriendelijke verificatie methoden. Gebruikers kunnen of kunnen niet worden aangevraagd voor MFA op basis van configuratie beslissingen die een beheerder doet. U kunt hier meer lezen over MFA: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Volg de Azure AD Connect server-beveiligings richtlijnen** De Azure AD Connect-server bevat essentiële identiteits gegevens en moet worden behandeld als een onderdeel van laag 0 zoals beschreven in het model van de [Active Directory-administratieve laag](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material). Raadpleeg ook onze [richt lijnen voor het beveiligen van uw AADConnect-server](./how-to-connect-install-prerequisites.md#azure-ad-connect-server).

**PHS inschakelen voor de detectie van gelekte referenties** Met wachtwoord hash-synchronisatie wordt ook [gelekte referentie detectie](../identity-protection/concept-identity-protection-risks.md) voor uw hybride accounts ingeschakeld. Microsoft werkt samen met wetshandhavers en onderzoekers die het 'dark web' bestuderen om openbaar beschikbare gebruikersnaam/wachtwoord-paren te vinden. Als een van deze paren overeenkomt met die van uw gebruikers, wordt het gekoppelde account verplaatst naar een hoog risico. 


## <a name="troubleshooting"></a>Problemen oplossen
**V: hoe kan ik hulp krijgen bij Azure AD Connect?**

[Zoek in de micro soft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Zoek in de KB naar technische oplossingen voor veelvoorkomende problemen met de ondersteuning voor Azure AD Connect.

[Micro soft Q&een vraag pagina voor Azure Active Directory](/answers/topics/azure-active-directory.html)

* Zoek technische vragen en antwoorden of vraag uw eigen vragen door naar [de Azure AD-Community](/answers/topics/azure-active-directory.html)te gaan.

[Ondersteuning voor Azure AD vragen](../fundamentals/active-directory-troubleshooting-support-howto.md)

**V: Waarom worden gebeurtenissen 6311 en 6401 weer gegeven na fouten in de synchronisatie stap?**

De gebeurtenissen 6311- **de server heeft een onverwachte fout aangetroffen tijdens het uitvoeren van een call back** en 6401- **de beheer agent controller heeft een onverwachte fout aangetroffen. deze** worden altijd geregistreerd na een fout in de synchronisatie stap. Om deze fouten op te lossen, moet u de fouten in de synchronisatie stap opschonen.  Zie [problemen oplossen tijdens synchronisatie](tshoot-connect-sync-errors.md) en [problemen met object synchronisatie oplossen met Azure AD Connect Sync](tshoot-connect-objectsync.md) voor meer informatie.