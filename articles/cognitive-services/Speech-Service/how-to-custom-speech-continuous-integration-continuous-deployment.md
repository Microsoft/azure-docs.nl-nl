---
title: CI/CD voor Custom Speech Speech-Service
titleSuffix: Azure Cognitive Services
description: Pas DevOps toe met Custom Speech-en CI/CD-werk stromen. Implementeer een bestaande DevOps-oplossing voor uw eigen project.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f82ea154d5949f4d229ac76e7a7ce2a89d15ac13
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025664"
---
# <a name="cicd-for-custom-speech"></a>CI/CD voor Custom Speech

Implementeer geautomatiseerd trainings-, test-en release beheer om continue verbetering van Custom Speech modellen mogelijk te maken bij het Toep assen van updates op trainings-en test gegevens. Via een efficiënte implementatie van CI/CD-werk stromen kunt u ervoor zorgen dat het eind punt voor het best presterende Custom Speech model altijd beschikbaar is.

[Continue integratie](/azure/devops/learn/what-is-continuous-integration) (CI) is de technische praktijk van het regel matig door voeren van updates in een gedeelde opslag plaats en het uitvoeren van een geautomatiseerde build. Met CI-werk stromen voor Custom Speech traint u een nieuw model van de gegevens bronnen en voert u automatische tests uit voor het nieuwe model om ervoor te zorgen dat het beter presteert dan het vorige model.

[Continue levering](/azure/devops/learn/what-is-continuous-delivery) (cd) haalt modellen uit het CI-proces en maakt een eind punt voor elk verbeterd Custom speech model. Met CD kunnen eind punten eenvoudig worden geïntegreerd in oplossingen.

Aangepaste CI/CD-oplossingen zijn mogelijk, maar voor een robuuste, vooraf gemaakte oplossing gebruikt u de [DevOps-opslag plaats](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)van de sjabloon, waarmee CI/cd-werk stromen worden uitgevoerd met github-acties.

## <a name="cicd-workflows-for-custom-speech"></a>CI/CD-werk stromen voor Custom Speech

Het doel van deze werk stromen is om ervoor te zorgen dat elk Custom Speech model een betere herkennings nauwkeurigheid heeft dan de vorige build. Als de updates voor testen en/of trainings gegevens nauw keuriger zijn, maken deze werk stromen een nieuw Custom Speech-eind punt.

Git-servers zoals GitHub en Azure DevOps kunnen geautomatiseerde werk stromen uitvoeren wanneer specifieke Git-gebeurtenissen plaatsvinden, zoals samen voegingen of pull-aanvragen. Een CI-werk stroom kan bijvoorbeeld worden geactiveerd wanneer updates voor het testen van gegevens naar de *hoofd* vertakking worden gepusht. Verschillende Git-servers hebben verschillende hulp middelen, maar kunnen ook scripting opdracht regel interface-opdrachten (CLI) gebruiken, zodat ze op een buildserver kunnen worden uitgevoerd.

De werk stromen moeten op de een of andere manier gegevens, testen, test bestanden, modellen en eind punten, zodat ze kunnen worden teruggeleid naar de door Voer of de versie die ze van hebben. Het is ook handig om deze activa een naam te geven, zodat u ze eenvoudig kunt zien die zijn gemaakt na het bijwerken van de test gegevens en de trainings gegevens.

### <a name="ci-workflow-for-testing-data-updates"></a>CI-werk stroom voor het testen van gegevens updates

Het hoofd doel van de CI/CD-werk stromen is het bouwen van een nieuw model met behulp van de trainings gegevens en om dat model te testen met behulp van de test gegevens om te bepalen of de [fout frequentie van Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (wer) is verbeterd ten opzichte van het eerder best presterende model (het ' bench Mark-model '). Als het nieuwe model beter presteert, wordt het het nieuwe benchmark model voor het vergelijken van toekomstige modellen vergeleken.

De CI-werk stroom voor het testen van gegevens updates moet het huidige Bench Mark-model opnieuw testen met de bijgewerkte test gegevens om de herziene WER te berekenen. Op deze manier zorgt u ervoor dat wanneer de WER van een nieuw model wordt vergeleken met de WER van de Bench Mark, beide modellen zijn getest op dezelfde test gegevens en u vergelijken zoals in een soort.

Deze werk stroom moet activeren bij updates om gegevens te testen en:

- Het Bench Mark-model testen op basis van de bijgewerkte test gegevens.
- Sla de test uitvoer op, die de WER van het Bench Mark-model bevat, met behulp van de bijgewerkte gegevens.
- De WER van deze tests wordt de nieuwe Bench Mark-WER die voor toekomstige modellen moet worden uitgevoerd.
- De CD-werk stroom wordt niet uitgevoerd voor updates om gegevens te testen.

### <a name="ci-workflow-for-training-data-updates"></a>CI-werk stroom voor het bijwerken van trainings gegevens

Updates voor trainings gegevens worden bijgewerkt naar het aangepaste model.

Deze werk stroom moet worden geactiveerd voor updates op trainings gegevens en:

- Train een nieuw model met de bijgewerkte trainings gegevens.
- Test het nieuwe model op basis van de test gegevens.
- Sla de test uitvoer op die de WER bevat.
- Vergelijk de WER van het nieuwe model met de WER van het Bench Mark-model.
- Als de WER niet wordt verbeterd, stopt u de werk stroom.
- Als de WER wordt verbeterd, voert u de werk stroom van de CD uit om een Custom Speech-eind punt te maken.

### <a name="cd-workflow"></a>CD-werk stroom

Nadat een update van de trainings gegevens de herkenning van een model verbetert, moet de CD-werk stroom automatisch worden uitgevoerd om een nieuw eind punt voor dat model te maken en dat eind punt beschikbaar maken zodat het kan worden gebruikt in een oplossing.

#### <a name="release-management"></a>Releasebeheer

De meeste teams vereisen een hand matig controle-en goedkeurings proces voor implementatie naar een productie omgeving. Voor een productie-implementatie wilt u er wellicht zeker van zijn dat deze zich voordoet wanneer er belang rijke personen voor het ontwikkelings team beschikbaar zijn voor ondersteuning of tijdens een periode van minder verkeer.

### <a name="tools-for-custom-speech-workflows"></a>Hulp middelen voor het Custom Speech van werk stromen

Gebruik de volgende hulpprogram ma's voor CI/CD Automation-werk stromen voor Custom Speech:

- [Azure cli](/cli/azure/?view=azure-cli-latest) voor het maken van een Azure-Service-Principal-verificatie, het opvragen van Azure-abonnementen en het opslaan van test resultaten in Azure Blob.
- [Azure speech cli](spx-overview.md) om te communiceren met de spraak service vanaf de opdracht regel of een geautomatiseerde werk stroom.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps-oplossing voor Custom Speech met GitHub-acties

Voor een al geïmplementeerde DevOps-oplossing voor Custom Speech gaat u naar de [Speech DevOps-sjabloon opslag plaats](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Maak een kopie van de sjabloon en begin met het ontwikkelen van aangepaste modellen met een robuust DevOps systeem dat tests, training en versie beheer met GitHub-acties omvat. De opslag plaats biedt voorbeeld test-en trainings gegevens om te helpen bij het instellen en om de werk stroom te verklaren. Als u de eerste keer hebt ingesteld, moet u de voorbeeld gegevens vervangen door uw project gegevens.

De [opslag plaats van de speech DevOps-sjabloon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) biedt de infra structuur en gedetailleerde richt lijnen voor het volgende:

- Kopieer de sjabloon opslagplaats naar uw GitHub-account en maak vervolgens Azure-resources en een [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) voor de GITHUB acties CI/cd-werk stromen.
- Door loop de '[dev Inner-lus](https://mitchdenny.com/the-inner-loop/)'. Werk de training bij en test gegevens van een functie vertakking, test de wijzigingen met een tijdelijk ontwikkel model en verhoog een pull-aanvraag om de wijzigingen voor te stellen en te controleren.
- Wanneer trainings gegevens worden bijgewerkt in een pull-aanvraag naar de *Master*, traint u modellen met de GitHub-acties CI-werk stroom.
- Voer automatische nauwkeurigheids tests uit om de [Word-fout frequentie](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (wer) van een model vast te leggen. Sla de test resultaten op in Azure Blob.
- Voer de CD-werk stroom uit om een eind punt te maken wanneer de WER wordt verbeterd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over DevOps met spraak:

- Gebruik de [Speech DevOps-sjabloon opslag plaats](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) om DevOps te implementeren voor Custom speech met github-acties.