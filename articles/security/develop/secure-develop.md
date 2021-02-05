---
title: Veilige toepassingen ontwikkelen op Microsoft Azure
description: In dit artikel worden aanbevolen procedures besproken die u moet overwegen tijdens de implementatie-en verificatie fasen van uw webtoepassingen project.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8e69803f4740a58adb4230bd82cc723221762cb4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576613"
---
# <a name="develop-secure-applications-on-azure"></a>Beveiligde toepassingen ontwikkelen in Azure
In dit artikel bieden we beveiligings activiteiten en-controles waarmee u rekening moet houden bij het ontwikkelen van toepassingen voor de Cloud. Beveiligings vragen en-concepten waarmee u rekening moet houden tijdens de implementatie-en verificatie fasen van micro soft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) vallen onder de dekking. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-Services die u kunt gebruiken om een veiligere toepassing te ontwikkelen.

In dit artikel komen de volgende SDL-fasen aan bod:

- Implementatie
- Verificatie

## <a name="implementation"></a>Implementatie
De focus van de implementatie fase is het instellen van aanbevolen procedures voor vroegtijdige preventie en voor het detecteren en verwijderen van beveiligings problemen van de code.
Stel dat uw toepassing wordt gebruikt op een manier die u niet wilt gebruiken. Zo kunt u zich beschermen tegen onbedoelde of opzettelijke misbruik van uw toepassing.

### <a name="perform-code-reviews"></a>Code beoordelingen uitvoeren

Voordat u code incheckt, voert u [code beoordelingen](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) uit om de algehele code kwaliteit te verbeteren en het risico op het maken van fouten te verminderen. U kunt [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) gebruiken om het code controle proces te beheren.

### <a name="perform-static-code-analysis"></a>Statische codeanalyse uitvoeren

[Statische code analyse](https://owasp.org/www-community/controls/Static_Code_Analysis) (ook wel bekend als *broncode analyse*) wordt gewoonlijk uitgevoerd als onderdeel van een code controle. Statische code analyse verwijst doorgaans naar het uitvoeren van hulpprogram ma's voor statische code analyse om mogelijke beveiligings problemen in niet-actieve code te vinden met behulp van technieken als [Taint-controle](https://en.wikipedia.org/wiki/Taint_checking) en [gegevens stroom analyse](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace biedt [ontwikkel hulpprogramma's](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) voor het uitvoeren van een statische code analyse en hulp bij het evalueren van code.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Elke invoer voor uw toepassing valideren en opschonen

Behandel alle invoer als niet-vertrouwd om uw toepassing te beschermen tegen de meest voorkomende beveiligings problemen van webtoepassingen. Niet-vertrouwde gegevens zijn een medium voor injectie aanvallen. De invoer voor uw toepassing bevat para meters in de URL, de invoer van de gebruiker, de gegevens uit de data base of een API, en alles wat wordt door gegeven in een gebruiker die mogelijk kan worden bewerkt. Een toepassing moet [valideren](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) dat gegevens syntactisch en semantisch geldig zijn voordat de toepassing de gegevens op een wille keurige manier gebruikt (met inbegrip van het weer geven van de gebruiker).

Valideer invoer vroegtijdig in de gegevens stroom om ervoor te zorgen dat alleen goed gevormde gegevens de werk stroom binnenkomen. U wilt niet dat er misvormde gegevens in uw data base worden bewaard of een storing in een downstream-onderdeel wordt geactiveerd.

Blocklisting en allowlisting zijn twee algemene benaderingen voor het uitvoeren van validatie van invoer syntaxis:

  - Blocklisting probeert te controleren of een bepaalde gebruikers invoer niet de inhoud ' bekend als schadelijk is ' bevat.

  - Allowlisting probeert te controleren of een bepaalde gebruikers invoer overeenkomt met een aantal "bekende goede" invoer. Op tekens gebaseerd allowlisting is een vorm van allowlisting waarbij een toepassing controleert of gebruikers invoer alleen ' bekende goede ' tekens bevat of dat de invoer overeenkomt met een bekende indeling.
    Dit kan bijvoorbeeld nodig zijn om te controleren of een gebruikers naam alleen alfanumerieke tekens bevat of dat deze precies twee getallen bevat.

Allowlisting is de aanbevolen benadering voor het bouwen van beveiligde software.
Blocklisting is gevoelig voor fouten, omdat het onmogelijk is om een volledige lijst met mogelijk onjuiste invoer te bedenken.

Doe dit op de-server, niet aan de client zijde (of op de-server en aan de client zijde).

### <a name="verify-your-applications-outputs"></a>De uitvoer van uw toepassing controleren

Een uitvoer die u visueel of in een document bevindt, moet altijd worden gecodeerd en ontsnapeerd. [Escapes](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), ook wel bekend als *uitvoer codering*, wordt gebruikt om ervoor te zorgen dat niet-vertrouwde gegevens geen voer tuig zijn voor een injectie aanval. Escapes, gecombineerd met gegevens validatie, bieden gelaagde beveiligingen voor een betere beveiliging van het systeem als geheel.

Bij het maken van een Escape wordt gecontroleerd of alles wordt weer gegeven als *uitvoer.* Bij het maken van een Escape kan de interpreter ook weten dat de gegevens niet zijn bedoeld om te worden uitgevoerd. dit voor komt dat aanvallen werken. Dit is een andere veelvoorkomende aanvals techniek die *cross-site scripting* (XSS) wordt genoemd.

Als u een webframework van een derde partij gebruikt, kunt u de opties voor uitvoer codering op websites controleren met behulp van het [Cheat-blad OWASP XSS-preventie](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Query's met para meters gebruiken wanneer u contact opneemt met de data base

Maak nooit een inline-database query in uw code en verzend deze rechtstreeks naar de data base. Door schadelijke code die in uw toepassing is ingevoegd, kan uw data base mogelijk worden gestolen, gewist of gewijzigd. Uw toepassing kan ook worden gebruikt om schadelijke besturingssysteem opdrachten uit te voeren op het besturings systeem dat als host fungeert voor uw data base.

Gebruik in plaats daarvan query's met para meters of opgeslagen procedures. Wanneer u query's met para meters gebruikt, kunt u de procedure veilig aanroepen vanuit uw code en deze door geven aan een teken reeks zonder dat deze wordt beschouwd als onderdeel van de query-instructie.

### <a name="remove-standard-server-headers"></a>Standaard server headers verwijderen

Headers zoals server, X-by en X-AspNet-version tonen informatie over de-server en onderliggende technologieën. We raden u aan deze headers te onderdrukken om te voor komen dat de toepassing wordt gevingerafdrukd.
Zie [standaard server headers verwijderen op Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Uw productie gegevens scheiden

Uw productie gegevens, of echte gegevens, mogen niet worden gebruikt voor ontwikkeling, tests of andere doel einden dan het bedrijf dat bedoeld is. Een gemaskeerde ([geanonimiseerd](https://en.wikipedia.org/wiki/Data_anonymization)) gegevensset moet worden gebruikt voor alle ontwikkel-en test doeleinden.

Dit betekent dat minder mensen toegang hebben tot uw echte gegevens, waardoor uw kwets baarheid wordt beperkt. Het betekent ook dat minder werk nemers persoonlijke gegevens zien, waardoor een mogelijke schending van vertrouwelijkheid wordt voor komen.

### <a name="implement-a-strong-password-policy"></a>Een sterk wachtwoord beleid implementeren

U moet een sterk wachtwoord beleid implementeren om ervoor te zorgen dat gebruikers een complex wacht woord maken (bijvoorbeeld: 12 tekens minimum lengte en alfanumerieke en speciale tekens vereisen) om te voor komen dat de computer wordt geraden.

U kunt een identiteits raamwerk gebruiken om wachtwoord beleid te maken en af te dwingen. Azure AD B2C helpt u bij het beheer van wacht woorden door [ingebouwde beleids regels](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [selfservice voor wachtwoord herstel](../../active-directory-b2c/user-flow-self-service-password-reset.md)en meer te bieden.

Om te beschermen tegen aanvallen op standaard accounts, moet u controleren of alle sleutels en wacht woorden worden vervangen en dat ze worden gegenereerd of vervangen nadat u resources hebt geïnstalleerd.

Als de toepassing automatisch wacht woorden moet genereren, zorgt u ervoor dat de gegenereerde wacht woorden wille keurig zijn en dat ze een hoge entropie hebben.

### <a name="validate-file-uploads"></a>Uploads van bestanden valideren

Als uw toepassing [uploads van bestanden](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)toestaat, moet u rekening houden met voorzorgsmaatregelen die u voor deze Risk ante activiteit kunt nemen. De eerste stap in veel aanvallen is het verkrijgen van schadelijke code in een systeem dat wordt aangevallen. Als u een bestand uploadt, helpt de aanvaller dit te doen. OWASP biedt oplossingen voor het valideren van een bestand om ervoor te zorgen dat het bestand dat u uploadt veilig is.

Met antimalware Protection kunt u virussen, spyware en andere schadelijke software identificeren en verwijderen. U kunt [micro soft antimalware](../fundamentals/antimalware.md) of een Endpoint Protection-oplossing van micro soft partner ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)en [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)) installeren.

[Micro soft antimalware](../fundamentals/antimalware.md) bevat functies als realtime-beveiliging, geplande scans, malware-herstel, handtekening updates, engine-updates, voor beelden van rapporten en uitsluitings gebeurtenissen verzamelen. U kunt micro soft antimalware en partner oplossingen integreren met [Azure Security Center](../../security-center/security-center-partner-integration.md) voor een gemakkelijke implementatie en ingebouwde detecties (waarschuwingen en incidenten).

### <a name="dont-cache-sensitive-content"></a>Geen gevoelige inhoud opslaan in cache

Sla gevoelige inhoud niet op in de cache van de browser. Browsers kunnen gegevens opslaan voor caching en geschiedenis. Bestanden in de cache worden opgeslagen in een map, zoals de map Tijdelijke Internet bestanden, in het geval van Internet Explorer. Wanneer deze pagina's weer worden genoemd, worden de pagina's uit de cache van de browser weer gegeven. Als gevoelige informatie (adres, creditcard gegevens, sofi-nummer, gebruikers naam) wordt weer gegeven aan de gebruiker, kan de informatie worden opgeslagen in de cache van de browser en kan worden opgehaald door de cache van de browser te controleren of door simpelweg op de knop **terug** van de browser te drukken.

## <a name="verification"></a>Verificatie
De verificatie fase omvat een uitgebreide inspanning om ervoor te zorgen dat de code voldoet aan de beveiligings-en privacyfuncties die in de voor gaande fasen zijn vastgelegd.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Problemen in uw toepassings afhankelijkheden detecteren en oplossen

U scant uw toepassing en de bijbehorende afhankelijke bibliotheken om bekende kwets bare onderdelen te identificeren. Producten die beschikbaar zijn voor het uitvoeren van deze scan, zijn [OWASP dependency check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)en [Black eenden](https://www.blackducksoftware.com/).

Het scannen van beveiligings problemen met [Tinfoil-beveiliging](https://www.tinfoilsecurity.com/) is beschikbaar voor Azure app service Web apps. [Tinfoil Security scanning via app service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) biedt ontwikkel aars en beheerders een snelle, geïntegreerde en betaal bare manier om beveiligings problemen op te sporen en op te lossen voordat een kwaadwillende actor hiervan gebruik kan maken.

> [!NOTE]
> U kunt ook [Tinfoil-beveiliging integreren met Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Het integreren van Tinfoil-beveiliging met Azure AD biedt de volgende voor delen:
>  - In azure AD kunt u bepalen wie toegang heeft tot Tinfoil-beveiliging.
>  - Uw gebruikers kunnen automatisch worden aangemeld bij Tinfoil-beveiliging (eenmalige aanmelding) door gebruik te maken van hun Azure AD-accounts.
>  - U kunt uw accounts beheren op één centrale locatie, het Azure Portal.

### <a name="test-your-application-in-an-operating-state"></a>Uw toepassing testen in een bedrijfs toestand

Dynamische test op toepassings beveiliging (DAST) is een proces van het testen van een toepassing in een bedrijfs status om beveiligings problemen op te sporen. Met DAST-hulpprogram ma's worden Program ma's geanalyseerd terwijl ze worden uitgevoerd voor het vinden van beveiligings problemen, zoals geheugen beschadiging, onveilige server configuratie, cross-site scripting, problemen met gebruikers bevoegdheden, SQL-injectie en andere belang rijke beveiligings problemen.

DAST wijkt af van de statische test van de toepassings beveiliging (SAST). SAST-hulpprogram ma's analyseren bron code of gecompileerde versies van code wanneer de code niet wordt uitgevoerd om beveiligings fouten te vinden.

Voer DAST uit, bij voor keur met de hulp van een beveiligings Professional (een [indringings tester](../fundamentals/pen-testing.md) of beveiligings ervaring). Als een beveiligings Professional niet beschikbaar is, kunt u DAST zelf uitvoeren met een webproxy scanner en een aantal trainingen. Sluit in een vroeg stadium een DAST-scanner aan om ervoor te zorgen dat u geen duidelijke beveiligings problemen in uw code kunt introduceren. Zie de [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) -site voor een lijst met scanners voor beveiligings problemen met webtoepassingen.

### <a name="perform-fuzz-testing"></a>Fuzzy tests uitvoeren

Bij [fuzz testen](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)wordt programma fouten veroorzaakt door een opzettelijk geformuleerde of wille keurige gegevens naar een toepassing te introduceren. Als er programma fouten optreden, kunnen er beveiligings problemen worden onthuld voordat de toepassing wordt uitgebracht.

[Detectie van beveiligings Risico's](https://www.microsoft.com/en-us/security-risk-detection/) is de micro soft Unique-test service voor het vinden van essentiële beveiligings problemen in software.

### <a name="conduct-attack-surface-review"></a>Aanvals beoordeling uitvoeren

Door de kwets baarheid te controleren nadat de code is voltooid, zorgt u ervoor dat alle ontwerp-of implementatie wijzigingen in een toepassing of systeem worden overwogen. Het helpt ervoor te zorgen dat nieuwe aanvals vectoren die zijn gemaakt als gevolg van de wijzigingen, met inbegrip van bedreigings modellen, worden beoordeeld en verholpen.

U kunt een afbeelding van de kwets baarheid bouwen door de toepassing te scannen. Micro soft biedt een hulp programma voor het uitvoeren van een kwets baarheid voor aanvallen met de naam [aanval Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=58105) U kunt kiezen uit veel commerciële, dynamische tests en hulpprogram ma's voor het scannen van problemen of services, waaronder [OWASP schrijving Attack proxy project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)en [w3af](http://w3af.sourceforge.net/). Deze scan tools verkennen uw app en wijst de onderdelen van de toepassing toe die toegankelijk zijn via het web. U kunt ook de Azure Marketplace doorzoeken op vergelijk bare [ontwikkel hulpprogramma's](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Beveiligings indringings tests uitvoeren

Om ervoor te zorgen dat uw toepassing veilig is, is het belang rijk dat u een andere functionaliteit test. Zorg voor [indringings test](../fundamentals/pen-testing.md) een standaard onderdeel van het build-en implementatie proces. Plan regel matige beveiligings tests en beveiligings problemen met het scannen van geïmplementeerde toepassingen en controleer op open poorten, eind punten en aanvallen.

### <a name="run-security-verification-tests"></a>Tests voor beveiligings verificatie uitvoeren

[Secure DevOps Kit voor Azure](https://azsk.azurewebsites.net/index.html) (AzSK) bevat SVTs voor meerdere services van het Azure-platform. U voert deze SVTs regel matig uit om ervoor te zorgen dat uw Azure-abonnement en de verschillende resources waaruit uw toepassing bestaat een veilige status hebben. U kunt deze tests ook automatiseren door gebruik te maken van de functie continue integratie/doorlopende implementatie (CI/CD) Extensions van AzSK, die SVTs beschikbaar maakt als een Visual Studio-uitbrei ding.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden wij aan beveiligings maatregelen en activiteiten te gebruiken die u kunnen helpen bij het ontwerpen en implementeren van veilige toepassingen.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen implementeren](secure-deploy.md)