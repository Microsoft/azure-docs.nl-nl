---
title: Probleem oplossing-persoonlijker
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.openlocfilehash: b5f7ed1f2ded8f6ec0320d417b59bab016d75028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777272"
---
# <a name="personalizer-troubleshooting"></a>Problemen met persoonlijker oplossen

In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.

## <a name="configuration-issues"></a>Configuratie problemen

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>Ik heb een configuratie-instelling gewijzigd en nu wordt mijn lus niet op hetzelfde leer niveau uitgevoerd. Wat is er gebeurd?

Sommige configuratie-instellingen [stellen uw model opnieuw](how-to-settings.md#settings-that-include-resetting-the-model)in. Wijzigingen in de configuratie moeten zorgvuldig worden gepland.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>Ik heb een fout ontvangen bij het configureren van Personaler met de API. Wat is er gebeurd?

Als u een enkele API-aanvraag gebruikt om uw service te configureren en uw leer gedrag te wijzigen, treedt er een fout op. U moet twee afzonderlijke API-aanroepen uitvoeren: eerst, om uw service te configureren en vervolgens het leer gedrag te scha kelen.

## <a name="transaction-errors"></a>Transactie fouten

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Er wordt een reactie van HTTP 429 (te veel aanvragen) ontvangen van de service. Wat kan ik doen?

Als u een gratis prijs categorie hebt gekozen tijdens het maken van het persoonlijk exemplaar, is er een quotum limiet voor het aantal toegestane rang aanvragen. Controleer de API-aanroep frequentie voor de positie-API (in het deel venster metrische gegevens in de Azure Portal voor uw persoonlijke resource) en pas de prijs categorie aan (in het deel venster prijs categorie) als uw oproep volume naar verwachting groter is dan de drempel waarde voor gekozen prijs categorie.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Er wordt een 5xx-fout weer op de positie-of belonings-Api's. Wat moet ik doen?

Deze problemen moeten transparant zijn. Als ze door gaan, neemt u contact op met de ondersteuning door **nieuwe ondersteunings aanvraag** te selecteren in het gedeelte **ondersteuning en probleem oplossing** in de Azure portal voor uw persoonlijke resource.

## <a name="learning-loop"></a>Learning-lus

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>De learning-lus bereikt geen 100% in overeenstemming met het systeem zonder persoonlijker. Hoe kan ik dit probleem oplossen?

De reden waarom u uw doel niet bereikt met de learning-lus:
* Er zijn onvoldoende functies verzonden met de positie-API-aanroep
* Fouten in de functies die zijn verzonden, zoals het verzenden van niet-geaggregeerde functie gegevens zoals tijds tempels voor het classificeren van de API
* Fouten met loop-verwerking, zoals het verzenden van belonings gegevens naar belonings-API voor gebeurtenissen

Als u dit wilt oplossen, moet u de verwerking wijzigen door de functies te wijzigen die naar de lus worden verzonden, of om ervoor te zorgen dat de beloning een correcte beoordeling is van de kwaliteit van de reactie van de rang schikking.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>De learning-lus lijkt niet te leren. Hoe kan ik dit probleem oplossen?

Voor de learning-lus moeten enkele duizenden belonings aanroepen worden uitgevoerd voordat rang gegeven worden.

Als u niet zeker weet hoe uw leer proces momenteel zich gedraagt, voert u een [offline-evaluatie](concepts-offline-evaluation.md)uit en past u het gecorrigeerde leer beleid toe.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Ik blijf bij het ophalen van rang resultaten met dezelfde kansen voor alle items. Hoe kan ik weet je dat Personaler leren?

Personaler retourneert dezelfde waarschijnlijkheid in een rangorde API-resultaat wanneer het zojuist is gestart en een _leeg_ model heeft, of wanneer u de aangepaste lus opnieuw instelt en uw model nog steeds binnen de periode van uw **model update** valt.

Wanneer de nieuwe update periode begint, wordt het bijgewerkte model gebruikt en ziet u de waarschijnlijke veranderingen.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Het leer proces is leren, maar lijkt niet meer te weten en de kwaliteit van de rang resultaten is niet goed. Wat moet ik doen?

* Zorg ervoor dat u een evaluatie hebt voltooid en toegepast op de Azure Portal voor die persoonlijker resource (Learning-lus).
* Zorg ervoor dat alle beloningen worden verzonden via de belonings-API en verwerkt.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Hoe kan ik weet u dat de learning-lus regel matig wordt bijgewerkt en wordt gebruikt om mijn gegevens te scoren?

U kunt het tijdstip waarop het model voor het laatst is bijgewerkt, vinden op de pagina **model-en leer instellingen** van de Azure Portal. Als u een oud tijds tempel ziet, is dit waarschijnlijk omdat u niet de Rangings-en belonings aanroepen verzendt. Als de service geen inkomende gegevens heeft, wordt het leer proces niet bijgewerkt. Als u ziet dat de learning-lus niet vaak genoeg wordt bijgewerkt, kunt u de **model update frequentie** van de lus bewerken.

## <a name="offline-evaluations"></a>Offline evaluaties

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>De belang rijke functie van een evaluatie versie retourneert een lange lijst met honderden of duizenden items. Wat is er gebeurd?

Dit wordt meestal veroorzaakt door tijds tempels, gebruikers-Id's of andere verfijnde functies die worden verzonden in.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Ik heb een offline-evaluatie gemaakt en deze is bijna onmiddellijk voltooid. De reden hiervoor? Ik zie geen resultaten?

De offline-evaluatie maakt gebruik van de getrainde model gegevens van de gebeurtenissen in die tijds periode. Als u geen gegevens hebt verzonden binnen de tijds periode tussen de begin-en eind tijd van de evaluatie, wordt deze zonder resultaat voltooid. Verzend een nieuwe offline-evaluatie door een tijds bereik te selecteren met de gebeurtenissen die u naar persoonlijke voor keuren hebt verzonden.

## <a name="learning-policy"></a>Leerbeleid

### <a name="how-do-i-import-a-learning-policy"></a>Hoe kan ik een leer beleid importeren?

Meer informatie over [concepten van het trainings beleid](concept-active-learning.md#understand-learning-policy-settings) en [het Toep assen](how-to-manage-model.md) van een nieuw leer beleid. Als u geen leer beleid wilt selecteren, kunt u de [offline-evaluatie](how-to-offline-evaluation.md) gebruiken om een leer beleid te suggereren op basis van uw huidige gebeurtenissen.


## <a name="security"></a>Beveiliging

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>De API-sleutel voor mijn lus is aangetast. Wat kan ik doen?

U kunt één sleutel opnieuw genereren nadat u uw clients hebt gewisseld om de andere sleutel te gebruiken. Als u twee sleutels hebt, kunt u de sleutel op een luie manier door geven zonder uitval tijd. U wordt aangeraden dit te doen op een regel matige cyclus als beveiligings maatregel.


## <a name="next-steps"></a>Volgende stappen

[De update frequentie van het model configureren](how-to-settings.md#model-update-frequency)