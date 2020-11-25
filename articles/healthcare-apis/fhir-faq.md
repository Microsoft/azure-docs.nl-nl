---
title: Veelgestelde vragen over FHIR Services in azure-Azure API voor FHIR
description: Krijg antwoorden op veelgestelde vragen over de Azure API voor FHIR, zoals de opslag locatie van gegevens achter FHIR Api's en versie ondersteuning.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 744bcfe196629f76c5505f36ef9c8c224fefe601
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909064"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Veelgestelde vragen over de Azure API voor FHIR

## <a name="azure-api-for-fhir-the-basics"></a>Azure API voor FHIR: de basis beginselen

### <a name="what-is-fhir"></a>Wat is FHIR?
De snelle interoperabiliteits bronnen van de gezondheids zorg (FHIR-uitgesp roken "vuur") is een interoperabiliteits norm die is bedoeld om de uitwisseling van gezondheids zorg tussen verschillende Health-systemen mogelijk te maken. Deze standaard is ontwikkeld door de HL7-organisatie en wordt aangenomen door organisaties in de gezondheids zorg over de hele wereld. De meest recente versie van FHIR beschikbaar is R4 (Release 4). De Azure API voor FHIR ondersteunt R4 en biedt ook ondersteuning voor de vorige versie van STU3 (standaard voor proef versie 3). Ga naar [HL7.org](http://hl7.org/fhir/summary.html)voor meer informatie over FHIR.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Bevinden de gegevens zich achter de FHIR-Api's die zijn opgeslagen in azure?

Ja, de gegevens worden opgeslagen in beheerde data bases in Azure. De Azure API voor FHIR biedt geen directe toegang tot het onderliggende gegevens archief.

### <a name="what-identity-provider-do-you-support"></a>Welke id-provider ondersteunt u?

We ondersteunen momenteel Microsoft Azure Active Directory als id-provider.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Wat is het beoogde herstel punt (RPO) voor de Azure API voor FHIR?
De Azure-API voor FHIR wordt ondersteund door Cosmos DB als onze persistentie provider. Als gevolg hiervan is de RPO voor de service gelijk aan [Cosmos DB (één regio)](https://docs.microsoft.com/azure/cosmos-db/consistency-levels) en is < 240 minuten.

### <a name="what-fhir-version-do-you-support"></a>Welke FHIR-versie wordt ondersteund?

We ondersteunen versies 4.0.0 en 3.0.1 op zowel de Azure API voor FHIR (PaaS) als de FHIR-server voor Azure (open source).

Zie [ondersteunde functies](fhir-features-supported.md)voor meer informatie. Meer informatie over wat er is gewijzigd tussen FHIR-versies (STU3 naar R4) in de [versie geschiedenis van HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Azure IoT connector voor FHIR (preview) ondersteunt momenteel alleen FHIR versie R4 en is alleen zichtbaar op R4-instanties van Azure API voor FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Wat is het verschil tussen ' micro soft FHIR server voor Azure ' en ' Azure API for FHIR '?

De Azure API voor FHIR is een gehoste en beheerde versie van de open-source micro soft FHIR-server voor Azure. In de beheerde service biedt micro soft alle onderhoud en updates. 

Wanneer u de FHIR-server voor Azure uitvoert, hebt u rechtstreeks toegang tot de onderliggende services, maar bent u verantwoordelijk voor het onderhouden en bijwerken van de server en alle vereiste nalevings taken als u PHI-gegevens opslaat.

Voor een ontwikkelings standpunt wordt elke functie die niet alleen van toepassing is op de beheerde service, eerst geïmplementeerd op de open-source micro soft FHIR server voor Azure. Zodra de app is gevalideerd in open source, wordt deze vrijgegeven aan de PaaS Azure API voor de FHIR-oplossing. De tijd tussen de release in open source en PaaS is afhankelijk van de complexiteit van de functie en andere prioriteiten voor het schema. Dit is hetzelfde proces voor al onze services, zoals Azure IoT connector voor FHIR (preview).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Waar kan ik zien wat er wordt vrijgegeven in de Azure API voor FHIR?

Raadpleeg de [release](https://github.com/microsoft/fhir-server/releases) van de open-source FHIR-server om te zien wat er in de Azure API voor FHIR wordt uitgebracht. Vanaf november 2020 hebben we items gelabeld met Azure-API-for-FHIR als het open-source item wordt vrijgegeven aan de beheerde service. Deze functies zijn doorgaans twee weken beschikbaar nadat ze zich op de release pagina in open-source bevinden. We hebben ook instructies opgenomen voor het testen van de build [hier] ( https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) Als u wilt testen in uw eigen omgeving. We evalueren hoe u de beste aanvullende beheerde service-updates kunt delen.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>In welke regio's is Azure API voor FHIR beschikbaar?

Momenteel hebben we algemene Beschik baarheid voor zowel open bare als overheid in [meerdere geografische regio's](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Bekijk [Azure-Services door FedRAMP](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)voor meer informatie over Government Cloud Services bij micro soft.

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Waar kan ik zien wat er wordt vrijgegeven in de Azure API voor FHIR?

Raadpleeg de [release](https://github.com/microsoft/fhir-server/releases) van de open-source FHIR-server om te zien wat er in de Azure API voor FHIR wordt uitgebracht. We hebben met Azure-API-for-FHIR items gelabeld als ze worden vrijgegeven aan de beheerde service en die meestal twee weken na de release pagina in open source zijn. We hebben ook instructies opgenomen over het testen van de build [hier](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) als u wilt testen in uw eigen omgeving. We evalueren hoe u de beste aanvullende beheerde service-updates kunt delen.

### <a name="what-is-smart-on-fhir"></a>Wat is slim op FHIR?

SLIMME (Vervang bare medische toepassingen en herbruikbare technologie) op FHIR is een set open specificaties voor het integreren van partner toepassingen met FHIR-servers en andere Health IT-systemen, zoals elektronische status records en informatie uitwisselingen van de status. Door een slimme on FHIR-toepassing te maken, kunt u ervoor zorgen dat uw toepassing toegankelijk is en kan worden gebruikt door een verdwaald van verschillende systemen.
Verificatie en Azure API voor FHIR. Ga voor meer informatie naar Smart [Health](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Waar kan ik vinden welke versie van FHIR op mijn Data Base wordt uitgevoerd. 

U kunt de exacte FHIR-versie die wordt weer gegeven in de instructie Capability vinden onder de eigenschap ' fhirVersion '.

## <a name="fhir-implementations-and-specifications"></a>FHIR-implementaties en-specificaties

### <a name="can-i-create-a-custom-fhir-resource"></a>Kan ik een aangepaste FHIR-resource maken?

Er zijn geen aangepaste FHIR-resources toegestaan. Als u een aangepaste FHIR-resource nodig hebt, kunt u een aangepaste resource maken boven op de [basis bron](http://www.hl7.org/fhir/basic.html) met uitbrei dingen. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Worden [extensies](https://www.hl7.org/fhir/extensibility.html) ondersteund op de Azure API voor FHIR?

U kunt alle geldige FHIR JSON-gegevens naar de server laden. Als u de structuur definitie wilt opslaan die de uitbrei ding definieert, kunt u deze opslaan als een structuur definitie resource. Op dit moment kunt u niet zoeken op uitbrei dingen.

### <a name="what-is-the-limit-on-_count"></a>Wat is de limiet voor _count?

De huidige limiet voor _count is 100. Als u _count instelt op meer dan 100, ontvangt u een waarschuwing in de bundel dat er alleen 100 records worden weer gegeven.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Zijn er beperkingen voor de export functionaliteit van de groep?

Voor het exporteren van de groep exporteert u alleen de opgenomen verwijzingen van de groep, niet alle kenmerken van de [groeps resource](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Kan ik een bundel voor FHIR in de Azure-API plaatsen?

We ondersteunen momenteel het boeken van [batch bundels](https://www.hl7.org/fhir/valueset-bundle-type.html) , maar bieden geen ondersteuning voor het boeken van transactie bundels in de Azure API voor FHIR. U kunt de open-source FHIR-server die wordt ondersteund door SQL gebruiken voor het boeken van transactie bundels.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Hoe krijg ik alle resources voor één patiënt in de Azure API voor FHIR?

Het [zoeken naar compartimenten](https://www.hl7.org/fhir/compartmentdefinition.html) wordt ondersteund in de Azure API voor FHIR. Zo kunt u alle resources met betrekking tot een specifieke patiënt ophalen. Houd er rekening mee dat de afdeling nu alle resources bevat die aan de patiënt zijn gerelateerd, maar niet de patiënt zelf, dus u moet ook zoeken naar de patiënt als u de patiënt resource in uw resultaten nodig hebt.

Enkele voor beelden hiervan zijn:

* Ontvang een patiënt/<id>/*
* Patiënt/ <id> /OBSERVATION ophalen
* Bent u de <id> /Observation? code = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Wat is de standaard sortering bij het zoeken naar resources in azure API for FHIR?

Sorteren op de datum die het laatst is bijgewerkt, wordt ondersteund: _sort = _lastUpdated. Bekijk onze [pagina met ondersteunde functies](./fhir-features-supported.md#search)voor meer informatie over andere ondersteunde zoek parameters.

### <a name="how-does-export-work"></a>Hoe werkt $export?

$export maakt deel uit van de FHIR-specificatie: https://hl7.org/fhir/uv/bulkdata/export/index.html . Als de FHIR-service is geconfigureerd met een beheerde identiteit en een opslag account, en als de beheerde identiteit toegang heeft tot dat opslag account, kunt u gewoon $export aanroepen in de FHIR-API en alle FHIR-resources worden geëxporteerd naar het opslag account. Raadpleeg het [artikel over $export](./export-data.md)voor meer informatie.

## <a name="using-azure-api-for-fhir"></a>Azure API voor FHIR gebruiken

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Hoe kan ik log Analytics voor Azure API voor FHIR in te scha kelen?

Diagnostische logboek registratie inschakelen en voorbeeld query's voor deze logboeken toestaan. Raadpleeg [deze sectie](./enable-diagnostic-logging.md)voor meer informatie over het inschakelen van controle logboeken en voorbeeld query's. Als u aanvullende informatie wilt toevoegen aan de logboeken, kunt u het beste eens [gebruikmaken van aangepaste HTTP-headers](./use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Waar kan ik enkele voor beelden zien van het gebruik van de Azure API voor FHIR binnen een werk stroom?

We hebben een verzameling referentie architecturen die beschikbaar zijn op de GitHub-pagina van de [status architectuur](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Waar kan ik een voor beeld zien van het verbinden van een webtoepassing met Azure API voor FHIR?

We hebben een [github-pagina met een status architectuur](https://github.com/microsoft/health-architectures) die voorbeeld toepassingen en-scenario's bevat. In dit voor beeld ziet u hoe u een webtoepassing verbindt met Azure API voor FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API voor FHIR-functies en-services 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Is er een manier om mijn gegevens te versleutelen met mijn persoonlijke sleutel geen standaard sleutel?

Ja, met Azure API voor FHIR kunnen door de klant beheerde sleutels worden geconfigureerd, met ondersteuning van Cosmos DB. Bekijk [deze sectie](./customer-managed-key.md)voor meer informatie over het versleutelen van uw gegevens met een persoonlijke sleutel.

## <a name="azure-api-for-fhir-preview-features"></a>Azure API voor FHIR: Preview-functies

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Kan ik schaal capaciteit configureren voor Azure IoT connector voor FHIR (preview)?

Omdat Azure IoT connector voor FHIR gratis is tijdens de open bare preview, is de capaciteit van de schaal vast en beperkt. De Azure IoT-connector voor FHIR-configuratie die beschikbaar is in open bare Preview wordt verwacht een door Voer van ongeveer 200 berichten per seconde te bieden. Een vorm van resource capaciteits configuratie wordt beschikbaar gesteld in algemene Beschik baarheid (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Waarom kan ik de Azure IoT-connector voor FHIR (preview) niet installeren als persoonlijke koppeling is ingeschakeld op de Azure API voor FHIR?

Azure IoT connector voor FHIR biedt op dit moment geen ondersteuning voor persoonlijke koppelings mogelijkheden. Als u een persoonlijke koppeling hebt ingeschakeld in azure API voor FHIR, kunt u de Azure IoT-connector niet installeren voor FHIR en vice versa. Deze beperking wordt verwacht als de Azure IoT-connector voor FHIR beschikbaar is voor algemene Beschik baarheid (GA).