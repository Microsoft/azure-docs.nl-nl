---
title: Documentatie overzicht van micro soft security code Analysis
description: Meer informatie over de uitbrei ding voor de analyse van beveiligings codes van micro soft. Met deze extensie kunt u analyse van beveiligings code toevoegen aan Azure DevOps CI/ID-pijp lijnen.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801193"
---
# <a name="about-microsoft-security-code-analysis"></a>Informatie over analyse van micro soft-beveiligings code

> [!Note]
> Met ingang van 1 maart 2022 wordt de uitbrei ding voor micro soft security code Analysis (MSCA) buiten gebruik gesteld. Bestaande MSCA-klanten behouden hun toegang tot MSCA tot en met 1 maart 2022. Raadpleeg de OWASP- [Hulpprogram ma's](https://owasp.org/www-community/Source_Code_Analysis_Tools) voor het analyseren van de bron code voor alternatieve opties in azure DevOps. Om klanten te migreren naar GitHub, kunt u de [geavanceerde beveiliging van github](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)bekijken.

Met de uitbrei ding voor de analyse van beveiligings code van micro soft kunnen teams beveiligings code analyses toevoegen aan hun DevOps voor continue integratie en levering (CI/CD) van Azure. Deze analyse wordt aanbevolen door de [sdl-experts (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) bij micro soft.

Een consistente UX vereenvoudigt de beveiliging door de complexiteit van actieve hulpprogram ma's te verbergen. Met NuGet levering van de hulpprogram ma's hoeven teams de installatie of het bijwerken van hulp middelen niet langer te beheren. Met zowel opdracht regel-als basis interfaces voor het bouwen van taken, kunnen alle gebruikers net zo veel controle over de hulpprogram ma's hebben als zij dat willen.

Teams kunnen ook krachtige postprocessing-mogelijkheden gebruiken, zoals:

- Publicatie logboeken voor retentie.
- Het genereren van rapporten die op ontwikkel aars gericht zijn.
- Het configureren van compilatie-einden bij regressie tests.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Waarom moet ik de analyse van de beveiligings code van micro soft gebruiken?

### <a name="security-simplified"></a>Beveiliging vereenvoudigd

Het toevoegen van hulpprogram ma's voor het analyseren van micro soft-beveiligings code aan uw Azure DevOps-pijp lijn is net zo eenvoudig als het toevoegen van nieuwe taken. De taken aanpassen of het standaard gedrag gebruiken. Taken worden uitgevoerd als onderdeel van uw Azure DevOps-pijp lijn en genereren logboeken die veel soorten resultaten beschrijven.

### <a name="clean-builds"></a>Schone builds

Nadat u de eerste problemen hebt opgelost die door de hulpprogram ma's worden gerapporteerd, kunt u de uitbrei ding configureren voor het verbreken van de builds van nieuwe problemen. Het instellen van continue integratie builds voor elke pull-aanvraag is eenvoudig.

### <a name="set-it-and-forget-it"></a>Instellen en het verg eten

De build-taken en-hulpprogram ma's blijven standaard up-to-date. Als er een bijgewerkte versie van een hulp programma is, hoeft u dit niet te downloaden en te installeren. De uitbrei ding zorgt voor de update voor u.

### <a name="under-the-hood"></a>Onderhuids

De build-taken van de extensie verbergen de complexiteit van:
  - De hulpprogram ma's voor statische analyse van beveiliging worden uitgevoerd.
  - De resultaten van de logboek bestanden verwerken om een samenvattings rapport te maken of de build te verbreken.

## <a name="microsoft-security-code-analysis-tool-set"></a>Hulp programma voor analyse van beveiligings code van micro soft

Met de uitbrei ding voor de analyse van micro soft-beveiligings code worden de nieuwste versies van belang rijke analyse tools voor u beschikbaar gemaakt. De uitbrei ding bevat zowel door micro soft beheerde hulpprogram ma's als open source-hulpprogram ma's.

Deze hulpprogram ma's worden automatisch naar de in de Cloud gehoste agent gedownload nadat u de bijbehorende opbouw taak hebt gebruikt om de pijp lijn te configureren en uit te voeren.

In deze sectie vindt u de set hulpprogram ma's die momenteel beschikbaar zijn in de uitbrei ding. Bekijk hoe meer hulp middelen worden toegevoegd. Stuur ons ook uw suggesties voor hulpprogram ma's die u door ons wilt laten toevoegen.

### <a name="anti-malware-scanner"></a>Scanner voor anti-malware

De anti-malware scanner bouwen is nu opgenomen in de uitbrei ding voor de analyse van beveiligings code van micro soft. Deze taak moet worden uitgevoerd op een build-agent waarop Windows Defender al is geïnstalleerd. Zie de [website van Windows Defender](https://aka.ms/defender)voor meer informatie.

### <a name="binskim"></a>BinSkim

BinSkim is een Portable uitvoer bare (PE) licht gewicht scanner die de instellingen van het Compileer programma, de koppelings instellingen en andere beveiligings kenmerken van binaire bestanden valideert. Deze build-taak biedt een opdracht regel wrapper rond de binskim.exe-console toepassing. BinSkim is een open source-hulp programma. Zie [BinSkim op github](https://github.com/Microsoft/binskim)voor meer informatie.

### <a name="credential-scanner"></a>Referentie scanner

Wacht woorden en andere geheimen die zijn opgeslagen in de bron code, vormen een aanzienlijk probleem. Referentie scanner is een eigen, statisch analyse programma waarmee dit probleem kan worden opgelost. Het hulp programma detecteert referenties, geheimen, certificaten en andere gevoelige inhoud in uw bron code en de uitvoer van uw build.

### <a name="roslyn-analyzers"></a>Roslyn Analyzers

Roslyn-analyse functies is het met compileren van micro soft geïntegreerde hulp programma voor het statisch analyseren van beheerde C#-en Visual Basic-code. Zie [Roslyn-analyse](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings)functies voor meer informatie.

### <a name="tslint"></a>TSLint

TSLint is een uitbreidbaar, statisch analyse programma waarmee type script code wordt gecontroleerd op lees-, onderhouds-en fouten in de functionaliteit. Het wordt veel ondersteund door moderne editors en build-systemen. U kunt dit aanpassen met uw eigen lint regels, configuraties en formatteringen. TSLint is een open source-hulp programma. Zie [TSLint op github](https://github.com/palantir/tslint)voor meer informatie.

## <a name="analysis-and-post-processing-of-results"></a>Analyse en naverwerking van resultaten

De uitbrei ding voor de analyse van beveiligings code van micro soft heeft ook drie postprocessing-taken. Deze taken helpen u bij het analyseren van de resultaten die worden gevonden door de taken van de beveiligings hulpprogramma's. Wanneer aan een pijp lijn wordt toegevoegd, volgen deze taken doorgaans alle andere werk taken.

### <a name="publish-security-analysis-logs"></a>Logboeken voor beveiligings analyse publiceren

De taak logboeken van beveiligings analyse publiceren maakt de logboek bestanden van de beveiligings hulpprogramma's die tijdens de build worden uitgevoerd. U kunt deze logboeken lezen voor onderzoek en follow-up.

U kunt de logboek bestanden publiceren naar Azure-artefacten als zip-bestand. U kunt ze ook kopiëren naar een toegankelijke bestands share vanuit uw Private build agent.

### <a name="security-report"></a>Beveiligings rapport

Met de taak beveiligings rapport maken worden de logboek bestanden geparseerd. Deze bestanden worden gemaakt door de beveiligings hulpprogramma's die tijdens de build worden uitgevoerd. Met de taak maken wordt vervolgens één samenvattings rapport bestand gemaakt. In dit bestand worden alle problemen weer gegeven die door de analyse hulpprogramma's zijn gevonden.

U kunt deze taak zo configureren dat de resultaten voor specifieke hulpprogram ma's of voor alle hulpprogram ma's worden gerapporteerd. U kunt ook kiezen welk probleem niveau u wilt melden, bijvoorbeeld alleen fouten of zowel fouten als waarschuwingen.

### <a name="post-analysis-build-break"></a>Na de analyse (build-einde)

Met de taak bouwen op basis van een build kunt u een build-einde injecteren dat ertoe leidt dat een build mislukt. U kunt een build-einde injecteren als er in een of meer analyse hulpprogramma's problemen in de code worden gerapporteerd.

U kunt deze taak configureren om de build te verbreken voor problemen die worden gevonden door specifieke hulpprogram ma's of alle hulpprogram ma's. U kunt deze ook configureren op basis van de ernst van problemen die zijn gevonden, zoals fouten of waarschuwingen.

>[!NOTE]
>Elke build-taak slaagt als de taak is voltooid. Dit is waar, ongeacht of er problemen worden gevonden met een hulp programma, zodat de build kan worden uitgevoerd op voltooid door alle hulpprogram ma's uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [hand leiding](security-code-analysis-onboard.md)voor de installatie van micro soft voor meer informatie over het uitvoeren van een onboarding en het installeren van de analyse van beveiligings codes.

Voor meer informatie over het configureren van de build-taken raadpleegt u onze [configuratie](security-code-analysis-customize.md) handleiding of de [yaml-configuratie handleiding](yaml-configuration.md).

Bekijk onze [pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding en de hulpprogram ma's die worden aangeboden.