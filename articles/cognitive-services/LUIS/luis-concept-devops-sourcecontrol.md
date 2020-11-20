---
title: Bron beheer en ontwikkelings filialen-LUIS
description: Uw Language Understanding-app (LUIS) onderhouden onder broncode beheer. Updates Toep assen op een LUIS-app terwijl u aan het werk bent in een ontwikkelings vertakking.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: e49b713aca23c0373fa71d772ef7567372abe456
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990564"
---
# <a name="devops-practices-for-luis"></a>DevOps-procedures voor LUIS

Software-engineers die een Language Understanding-app (LUIS) ontwikkelen, kunnen DevOps-procedures rond [broncode beheer](luis-concept-devops-sourcecontrol.md)en [geautomatiseerde builds](luis-concept-devops-automation.md), [testen](luis-concept-devops-testing.md)en [release beheer](luis-concept-devops-automation.md#release-management) Toep assen door deze richt lijnen te volgen.

## <a name="source-control-and-branch-strategies-for-luis"></a>Broncodebeheer en vertakkingsstrategieën voor LUIS

Een van de belangrijkste factoren die het succes van DevOps is, is afhankelijk van het [bron beheer](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops). Met een broncode beheer systeem kunnen ontwikkel aars samen werken aan code en wijzigingen bijhouden. Door het gebruik van branches kunnen ontwikkel aars scha kelen tussen verschillende versies van de code basis en onafhankelijk van andere leden van het team. Wanneer ontwikkel aars een [pull-aanvraag](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) opleggen om updates van de ene vertakking naar een andere te stellen, of wanneer wijzigingen worden samengevoegd, kan dit de trigger zijn voor het maken van [automatische builds](luis-concept-devops-automation.md) en het continu testen van code.

Door gebruik te maken van de concepten en richt lijnen die in dit document worden beschreven, kunt u een LUIS-App ontwikkelen tijdens het bijhouden van wijzigingen in een broncode beheer systeem. Volg deze aanbevolen procedures voor software techniek:

- **Broncodebeheer**
  - De bron code voor uw LUIS-app bevindt zich in een Human Lees bare indeling.
  - Het model kan op een Herhaal bare manier worden gebouwd op basis van de bron.
  - De bron code kan worden beheerd door een bron code opslagplaats.
  - Referenties en geheimen, zoals ontwerpen en abonnements sleutels, worden nooit opgeslagen in de bron code.

- **Vertakkingen en samen voegen**
  - Ontwikkel aars kunnen werken met onafhankelijke vertakkingen.
  - Ontwikkel aars kunnen gelijktijdig in meerdere vertakkingen werken.
  - Het is mogelijk om wijzigingen in een LUIS-app te integreren in een andere vertakking door deze te herbaseren of samen te voegen.
  - Ontwikkel aars kunnen een PR samen voegen met de bovenliggende vertakking.

- **Versiebeheer**
  - Elk onderdeel in een grote toepassing moet onafhankelijk van elkaar worden geversied, zodat ontwikkel aars alleen de wijzigingen of updates kunnen detecteren door te kijken naar het versie nummer.

- **Code beoordelingen**
  - De wijzigingen in de PR worden gepresenteerd als een door de mens lees bare bron code die kan worden gecontroleerd voordat de PR wordt geaccepteerd.

## <a name="source-control"></a>Broncodebeheer

Als u de [definitie](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition) van het app-schema van een Luis-app in een broncode beheersysteem wilt behouden, gebruikt u de [LUDown-indeling ( `.lu` )](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  voor de weer gave van de app. `.lu` de indeling heeft de voor keur om te `.json` Format teren omdat deze door iedereen kan worden gelezen, waardoor het eenvoudiger is om wijzigingen aan te brengen en te controleren in pull.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Een LUIS-app opslaan met de LUDown-indeling

Als u een LUIS-app in een indeling wilt opslaan `.lu` en deze wilt plaatsen onder broncode beheer:

- OFWEL: [Exporteer de app](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) -versie `.lu` vanuit de [Luis-Portal](https://www.luis.ai/) en voeg deze toe aan uw opslag plaats voor broncode beheer

- OF: gebruik een tekst editor om een `.lu` bestand voor een Luis-app te maken en toe te voegen aan uw opslag plaats voor broncode beheer

> [!TIP]
> Als u werkt met de JSON-export van een LUIS-app, kunt u [deze converteren naar LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) met behulp van de [BotBuilder-HULPPROGRAM ma's Luis cli](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS). Gebruik de `--sort` optie om ervoor te zorgen dat de intenties en uitingen alfabetisch worden gesorteerd.  
> Houd er rekening mee dat **.** Met de Lu-export functie die in de Luis-Portal is ingebouwd, wordt de uitvoer al gesorteerd.

### <a name="build-the-luis-app-from-source"></a>De LUIS-app bouwen vanuit de bron

Als u een LUIS-app wilt *bouwen op basis van bron* , betekent dit dat u [een nieuwe Luis-App-versie kunt maken door de `.lu` bron te importeren](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) , [de versie te trainen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) en te [publiceren](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app). U kunt dit doen in de LUIS-portal of op de opdracht regel:

- Gebruik de LUIS-Portal om [de `.lu` versie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) van de app te importeren vanuit broncode beheer en de app te [trainen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) en te [publiceren](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) .

- Gebruik de [bot Framework opdracht regel interface voor Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) op de opdracht regel of in een CI/cd-werk [import](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) stroom om de `.lu` versie van de app te importeren vanuit broncode beheer in een Luis-toepassing, en de app te [trainen](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) en te [publiceren](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) .

### <a name="files-to-maintain-under-source-control"></a>Te onderhouden bestanden onder broncode beheer

De volgende typen bestanden voor uw LUIS-toepassing moeten worden onderhouden onder broncode beheer:

- `.lu` bestand voor de LUIS-toepassing

- [Definitie bestanden voor de eenheids test](luis-concept-devops-testing.md#writing-tests) (uitingen en verwachte resultaten)

- [Batch test bestanden](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format) (uitingen en verwachte resultaten) die worden gebruikt voor het testen van de prestaties

### <a name="credentials-and-keys-are-not-checked-in"></a>Referenties en sleutels zijn niet ingecheckt

Neem geen abonnements sleutels of soort gelijke vertrouwelijke waarden op in bestanden die u incheckt bij uw opslag plaats, waar ze mogelijk zichtbaar zijn voor onbevoegden. De sleutels en andere waarden die u moet gebruiken voor het inchecken, zijn onder andere:

- LUIS-ontwerpen en Voorspellings sleutels
- LUIS-ontwerpen en Voorspellings eindpunten
- Sleutels van Azure-abonnement
- Toegangs tokens, zoals het token voor een Azure- [Service-Principal](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) die wordt gebruikt voor Automation-verificatie

#### <a name="strategies-for-securely-managing-secrets"></a>Strategieën voor het veilig beheren van geheimen

Strategieën voor het veilig beheren van geheimen zijn onder andere:

- Als u gebruik wilt maken van Git-versie beheer, kunt u runtime geheimen opslaan in een lokaal bestand en voor komen dat het bestand wordt ingecheckt door een patroon toe te voegen dat overeenkomt met de bestands naam van een [. gitignore](https://git-scm.com/docs/gitignore) -bestand
- In een Automation-werk stroom kunt u geheimen veilig opslaan in de configuratie van de para meters die worden aangeboden door de automatiserings technologie. Als u bijvoorbeeld [github-acties](https://github.com/features/actions)gebruikt, kunt u geheimen veilig opslaan in [github geheimen](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Vertakkingen en samen voegen

Gedistribueerde versie beheersystemen zoals git bieden flexibiliteit bij het publiceren, delen, bekijken en herhalen van code wijzigingen via ontwikkelings filialen die met anderen worden gedeeld. Pas een [Git-vertakkings strategie](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance) toe die geschikt is voor uw team.

Welke vertakkings strategie u ook neemt, een belang rijk principe van de IT-afdeling is dat team leden, onafhankelijk van het werk dat in andere branches gaat, met de oplossing in een *functie vertakking* kunnen werken.

Voor het ondersteunen van onafhankelijke werk in filialen met een LUIS-project:

- **De hoofd vertakking heeft een eigen LUIS-app.** Deze app vertegenwoordigt de huidige status van uw oplossing voor uw project en de huidige actieve versie moet altijd worden toegewezen aan de `.lu` bron die zich in de hoofd vertakking bevindt. Alle updates voor de `.lu` bron voor deze app moeten worden gecontroleerd en getest, zodat deze app kan worden geïmplementeerd om omgevingen zoals productie op elk gewenst moment te bouwen. Wanneer updates van de `.lu` worden samengevoegd in de hoofd sectie van een functie vertakking, moet u een nieuwe versie maken in de Luis-app en [het versie nummer van de](#versioning)rand laten dalen.

- **Elke functie vertakking moet een eigen exemplaar van een Luis-app gebruiken**. Ontwikkel aars werken met deze app in een functie vertakking zonder dat dit van invloed is op ontwikkel aars die in andere branches werken. Deze app dev Branch is een werk kopie die moet worden verwijderd wanneer de functie vertakking wordt verwijderd.

![Git-functie vertakking](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Ontwikkel aars kunnen werken met onafhankelijke vertakkingen

Ontwikkel aars kunnen voor een LUIS-app onafhankelijk van andere vertakkingen werken door:

1. Het maken van een functie vertakking van de hoofd vertakking (afhankelijk van uw branche strategie, meestal Master of ontwikkeling).

1. [Maak een nieuwe Luis-app in de Luis-Portal](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app) (de '*dev Branch-app*'), uitsluitend ter ondersteuning van het werk in de functie vertakking.

   * Als de `.lu` bron voor uw oplossing al in uw vertakking bestaat, omdat deze is opgeslagen nadat het werk in een andere vertakking eerder in het project is uitgevoerd, maakt u de Luis-app voor ontwikkel aars door het bestand te importeren `.lu` .

   * Als u aan het werk bent van een nieuw project, hebt u de `.lu` bron voor uw hoofd Luis-app nog niet in de opslag plaats. U maakt het `.lu` bestand door uw dev Branch-app vanuit de portal te exporteren wanneer u uw functie vertakking hebt voltooid en deze als onderdeel van uw PR te verzenden.

1. Werk aan de actieve versie van uw dev Branch-app om de vereiste wijzigingen te implementeren. Het is raadzaam dat u alleen werkt in één versie van uw dev Branch-app voor al het functie vertakkings werk. Als u meer dan één versie in de app dev Branch maakt, moet u controleren welke versie de wijzigingen bevat die u wilt inchecken wanneer u uw PR verhoogt.

1. De updates testen: Zie [testen voor Luis DevOps](luis-concept-devops-testing.md) voor meer informatie over het testen van uw app dev Branch.

1. Exporteer de actieve versie van uw dev Branch-app `.lu` uit de [lijst met versies](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions).

1. Controleer uw updates en bestudeer de peer beoordeling van uw updates. Als u GitHub gebruikt, verhoogt u een pull- [aanvraag](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).

1. Wanneer de wijzigingen zijn goedgekeurd, voegt u de updates samen in de hoofd vertakking. Op dit moment maakt u een nieuwe [versie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) van de *hoofd* -Luis-app met behulp van de bijgewerkte `.lu` in-Master. Zie [versie beheer](#versioning) voor overwegingen bij het instellen van de versie naam.

1. Wanneer de functie vertakking wordt verwijderd, is het een goed idee om de LUIS-app voor ontwikkel aars te verwijderen die u hebt gemaakt voor het functie-Branch-werk.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Ontwikkel aars kunnen gelijktijdig in meerdere vertakkingen werken

Als u het hierboven beschreven patroon in [ontwikkel aars kunt gebruiken om te werken met onafhankelijke vertakkingen](#developers-can-work-from-independent-branches), gebruikt u een unieke Luis-toepassing in elke functie vertakking. Eén ontwikkelaar kan gelijktijdig op meerdere vertakkingen werken, zolang ze overschakelen naar de juiste dev Branch LUIS-app voor de vertakking waaraan ze momenteel werken.

Het is raadzaam dat u dezelfde naam gebruikt voor zowel de functie vertakking als voor de dev Branch LUIS-app die u voor de functie vertakking maakt, om ervoor te zorgen dat u per ongeluk op de verkeerde app werkt.

Zoals hierboven vermeld, raden we u aan om eenvoudig te werken in één versie in elke dev Branch-app. Als u meerdere versies gebruikt, moet u ervoor zorgen dat u de juiste versie activeert terwijl u tussen dev Branch-apps schakelt.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Meerdere ontwikkel aars kunnen gelijktijdig op dezelfde vertakking werken

U kunt op hetzelfde moment meerdere ontwikkel aars ondersteunen die aan dezelfde functie vertakking werken:

- Ontwikkel aars bekijken dezelfde functie vertakking en push-en pull-wijzigingen die door zichzelf en andere ontwikkel aars zijn verzonden terwijl de werkzaamheden worden uitgevoerd.

- Als u het hierboven beschreven patroon in [ontwikkel aars kunt gebruiken om te werken met onafhankelijke vertakkingen](#developers-can-work-from-independent-branches), zal deze vertakking een unieke Luis-toepassing gebruiken ter ondersteuning van de ontwikkeling. De LUIS-app ' dev Branch ' wordt gemaakt door het eerste lid van het ontwikkel team dat in de functie vertakking begint te werken.

- [Voeg team leden als mede](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate) werkers toe aan de Luis-app voor ontwikkel aars.

- Wanneer het functie vertakkings werk is voltooid, exporteert u de actieve versie van de app dev Branch LUIS `.lu` uit de [lijst met versies](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions), slaat u het bijgewerkte `.lu` bestand op in de opslag plaats en inchecken en brengt u de wijzigingen aan.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Wijzigingen van de ene vertakking in de andere opnemen met opnieuw baseren of samen voegen

Sommige andere ontwikkel aars van uw team die in een andere vertakking werken, hebben mogelijk updates aangebracht in de `.lu` bron en deze samengevoegd met de hoofd vertakking nadat u uw functie vertakking hebt gemaakt. Mogelijk wilt u de wijzigingen in uw werk versie opnemen voordat u wijzigingen aanbrengt in uw functie vertakking. U kunt dit doen door het project op dezelfde manier als andere code activa [te baseren of samen te voegen](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) . Omdat de LUIS-app in de LUDown-indeling van humaners leesbaar is, ondersteunt het samen voegen met behulp van standaard-merge tools.

Volg deze tips als u uw LUIS-app opnieuw wilt baseren in een functie vertakking:

- Zorg ervoor dat uw lokale kopie van de `.lu` bron voor uw app beschikt over alle meest recente wijzigingen die u hebt toegepast met behulp van de Luis-Portal voordat u opnieuw baseert of samenvoegt. u moet uw app eerst opnieuw exporteren uit de portal. Op die manier kunt u ervoor zorgen dat alle wijzigingen die u hebt aangebracht in de portal en nog niet zijn geëxporteerd verloren gaan.

- Gebruik tijdens het samen voegen standaard Programma's om eventuele samenvoeg conflicten op te lossen.

- Vergeet niet na het opnieuw indelen of samen voegen is voltooid om de app opnieuw te importeren in de portal, zodat u met de bijgewerkte app werkt wanneer u uw eigen wijzigingen wilt Toep assen.

### <a name="merge-prs"></a>Pull samen voegen

Nadat uw PR is goedgekeurd, kunt u uw wijzigingen in uw hoofd vertakking samen voegen. Er zijn geen speciale overwegingen van toepassing op de LUDown-bron voor een LUIS-app: deze is leesbaar en ondersteunt samen voeging met behulp van standaard-merge-hulpprogram ma's. Eventuele samenvoeg conflicten kunnen op dezelfde manier worden opgelost als andere bron bestanden.

Nadat uw PR is samengevoegd, is het raadzaam om het volgende op te schonen:

- De vertakking in uw opslag plaats verwijderen

- Verwijder de LUIS-app ' dev Branch ' die u hebt gemaakt voor het functie-Branch-werk.

Op dezelfde manier als bij de activa van de toepassings code moet u eenheids tests schrijven voor LUIS-app-updates. U moet continue integratie werk stromen gebruiken om te testen:

- Updates in een PR voordat de PR wordt samengevoegd
- De LUIS-app van de hoofd vertakking nadat een PR is goedgekeurd en de wijzigingen zijn samengevoegd in de Master.

Zie voor meer informatie over testen voor LUIS DevOps [testen op DevOps voor Luis](luis-concept-devops-testing.md). Zie [Automation-werk stromen voor Luis DevOps](luis-concept-devops-automation.md)voor meer informatie over het implementeren van werk stromen.

## <a name="code-reviews"></a>Codebeoordelingen

Een LUIS-app in de LUDown-indeling is leesbaar voor mensen, die ondersteuning biedt voor de communicatie van wijzigingen in een PR die geschikt is voor beoordeling. Test bestanden voor eenheden worden ook geschreven in de LUDown-indeling en kunnen ook eenvoudig worden weer gegeven in een PR.

## <a name="versioning"></a>Versiebeheer

Een toepassing bestaat uit meerdere onderdelen, zoals een bot die wordt uitgevoerd in [Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA Maker](https://www.qnamaker.ai/), [Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), enzovoort. Als u het doel van het lossen van toepassingen wilt benutten, gebruikt u [versie beheer](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control) , zodat elk onderdeel van een toepassing afzonderlijk wordt bijgewerkt, waardoor ontwikkel aars alleen op het versie nummer kijken of updates kunnen detecteren. Het is eenvoudiger om uw LUIS-app onafhankelijk van andere onderdelen te maken als u deze in een eigen opslag plaats behoudt.

Voor de LUIS-app voor de hoofd vertakking moet een versie beheer schema worden toegepast. Wanneer u updates samenvoegt naar de `.lu` Luis-app in de Master, importeert u die bijgewerkte bron vervolgens in een nieuwe versie in de Luis-app voor de hoofd vertakking.

Het is raadzaam een numeriek versie schema te gebruiken voor de hoofd versie van de LUIS-app, bijvoorbeeld:

`major.minor[.build[.revision]]`

Het versie nummer van elke update wordt verhoogd met het laatste cijfer.

De primaire/secundaire versie kan worden gebruikt om het bereik van de wijzigingen aan de functionaliteit van de LUIS-app aan te geven:

* Primaire versie: een belang rijke wijziging, zoals ondersteuning voor een nieuwe [intentie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent) of [entiteit](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)
* Secundaire versie: een kleine wijziging die compatibel is met eerdere versies, zoals na aanzienlijke nieuwe training
* Bouwen: er is geen functionaliteits wijziging, alleen een andere build.

Zodra u het versie nummer voor de laatste revisie van uw LUIS-app hebt vastgesteld, moet u de nieuwe app-versie bouwen en testen en deze publiceren naar een eind punt waar deze kan worden gebruikt in verschillende build-omgevingen, zoals kwaliteits garantie of productie. Het wordt ten zeerste aanbevolen om al deze stappen in een CI-werk stroom (continue Integration) te automatiseren.

Zie:
- [Automatiserings werk stromen](luis-concept-devops-automation.md) voor meer informatie over het implementeren van een CI-werk stroom voor het testen en vrijgeven van een Luis-app.
- [Release beheer](luis-concept-devops-automation.md#release-management) voor informatie over het implementeren van uw Luis-app.

### <a name="versioning-the-feature-branch-luis-app"></a>Versie beheer van de LUIS-app feature Branch

Wanneer u werkt met een ' dev Branch ' LUIS-app die u hebt gemaakt om het werk in een functie vertakking te ondersteunen, exporteert u uw app wanneer uw werk is voltooid en neemt u de bijgewerkte versie `'lu` op in uw PR. De vertakking in uw opslag plaats en de LUIS-app ' dev Branch ' moet worden verwijderd nadat de pull-in-Master is samengevoegd. Omdat deze app alleen bestaat om het werk in de functie vertakking te ondersteunen, is er geen specifiek versie beheer schema dat u in deze app moet Toep assen.

Wanneer uw wijzigingen in uw PR worden samengevoegd in de hoofd server, dat wil zeggen wanneer de versie beheer moet worden toegepast, zodat alle updates voor de hoofd server onafhankelijk van een versie worden gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [testen voor Luis DevOps](luis-concept-devops-testing.md)
* Meer informatie over het [implementeren van DevOps voor Luis met github](luis-how-to-devops-with-github.md)
