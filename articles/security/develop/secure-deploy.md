---
title: Veilige toepassingen implementeren op Microsoft Azure
description: In dit artikel worden aanbevolen procedures besproken die u moet overwegen tijdens de release-en respons fasen van uw webtoepassingen project.
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
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517186"
---
# <a name="deploy-secure-applications-on-azure"></a>Beveiligde toepassingen implementeren in azure
In dit artikel bieden we beveiligings activiteiten en-controles waarmee u rekening moet houden wanneer u toepassingen voor de Cloud implementeert. Beveiligings vragen en-concepten waarmee u rekening moet houden tijdens de release-en respons fasen van micro soft [Security Development Lifecycle (SDL),](/previous-versions/windows/desktop/cc307891(v=msdn.10)) worden gedekt. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-Services die u kunt gebruiken om een veiligere toepassing te implementeren.

In dit artikel komen de volgende SDL-fasen aan bod:

- Release
- Antwoord

## <a name="release"></a>Release
De focus van de release fase is het voorbereiden van een project voor de open bare versie.
Dit omvat plannings manieren om taken die na de release worden uitgevoerd effectief uit te voeren en beveiligings problemen op te lossen die later kunnen optreden.

### <a name="check-your-applications-performance-before-you-launch"></a>Controleer de prestaties van uw toepassing voordat u start

Controleer de prestaties van uw toepassing voordat u deze start of installeer updates voor de productie. Voer in de cloud gebaseerde [belasting tests](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) uit met behulp van Visual Studio om prestatie problemen in uw toepassing op te sporen, de implementatie kwaliteit te verbeteren, ervoor te zorgen dat uw toepassing altijd up-to-available is en dat uw toepassing verkeer voor uw Lance ring kan verwerken.

### <a name="install-a-web-application-firewall"></a>Een Web Application Firewall installeren

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Deze cracks zijn gebruikelijk voor SQL-injectie aanvallen en scripts voor cross-site scripting. Het voor komen van deze aanvallen in toepassings code kan lastig zijn. Mogelijk zijn er in veel lagen van de toepassings topologie strikte onderhoud, patching en controle vereist. Een gecentraliseerde WAF helpt beveiligings beheer eenvoudiger te maken. Een WAF-oplossing kan ook reageren op een beveiligings risico door een bekend beveiligings probleem op een centrale locatie op te lossen en elke afzonderlijke webtoepassing te beveiligen.

De [Azure-toepassing gateway WAF](../../web-application-firewall/ag/ag-overview.md) biedt gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. De WAF is gebaseerd op regels van de [OWASP core Rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 of 2.2.9.

### <a name="create-an-incident-response-plan"></a>Een reactieplan voor incidenten maken

Het voorbereiden van een reactie plan voor incidenten is van cruciaal belang om nieuwe bedreigingen te verhelpen die na verloop van tijd kunnen optreden. Het voorbereiden van een reactie plan voor incidenten omvat het identificeren van de juiste contacten voor beveiligings nood gevallen en het opstellen van beveiligings onderhouds plannen voor code die wordt overgenomen van andere groepen in de organisatie en voor code van een derde partij.

### <a name="conduct-a-final-security-review"></a>Een definitieve beveiligings beoordeling uitvoeren

Het controleren van alle beveiligings activiteiten die zijn uitgevoerd, zorgt voor een geslaagde voor bereiding op uw software versie of toepassing. De laatste beveiligings beoordeling (FSR) omvat doorgaans het onderzoeken van de bedreigings modellen, hulpprogram ma's en prestaties tegen de kwaliteits poorten en fout balken die zijn gedefinieerd in de fase vereisten.

### <a name="certify-release-and-archive"></a>Release en archief certificeren

Het certificeren van software voordat een release helpt ervoor te zorgen dat aan de vereisten voor beveiliging en privacy wordt voldaan. Het archiveren van alle relevante gegevens is essentieel voor het uitvoeren van onderhouds taken na de release. Archivering helpt ook de langlopende kosten te verlagen die zijn gekoppeld aan de aanhoudende software techniek.

## <a name="response"></a>Antwoord
De responsief post-release-fase van het ontwikkelings team en kan op de juiste wijze reageren op alle rapporten van opkomende software dreigingen en beveiligings problemen.

### <a name="execute-the-incident-response-plan"></a>Het antwoord plan voor incidenten uitvoeren

Het is essentieel dat u het antwoord plan voor incidenten in de release fase kunt implementeren om klanten te beschermen tegen beveiligings problemen van software of privacy die zich voordoen.

### <a name="monitor-application-performance"></a>Prestaties van toepassingen controleren

Voortdurende bewaking van uw toepassing na de implementatie ervan helpt u bij het detecteren van prestatie problemen en beveiligings problemen.
Azure-Services die u helpen bij het bewaken van toepassingen zijn:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Hiermee kunt u uw livewebtoepassing controleren. Application Insights detecteert automatisch prestatie afwijkingen. Het bevat krachtige analyse hulpprogramma's die u helpen bij het vaststellen van problemen en inzicht te krijgen in wat gebruikers daad werkelijk doen met uw app. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) helpt u bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid van de beveiliging van uw Azure-resources, inclusief webtoepassingen. Azure Security Center helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt. Het werkt met verschillende beveiligings oplossingen.

De gratis laag van Security Center biedt beperkte beveiliging alleen voor uw Azure-resources. De [laag Security Center Standard](../../security-center/security-center-get-started.md) breidt deze mogelijkheden uit naar on-premises resources en andere Clouds.
Security Center Standard helpt u bij het volgende:

  - Beveiligings problemen detecteren en oplossen.
  - Pas toegangs-en toepassings besturings elementen toe om schadelijke activiteiten te blok keren.
  - Detectie van bedreigingen met behulp van Analytics en intelligence.
  - Reageer snel op wanneer u een aanval onderneemt.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden wij aan beveiligings maatregelen en activiteiten te gebruiken die u kunnen helpen bij het ontwerpen en ontwikkelen van beveiligde toepassingen.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen ontwikkelen](secure-develop.md)