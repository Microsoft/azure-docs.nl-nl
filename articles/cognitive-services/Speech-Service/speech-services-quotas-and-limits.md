---
title: Quota en limieten voor Speech Services
titleSuffix: Azure Cognitive Services
description: Naslag informatie, gedetailleerde beschrijving en aanbevolen procedures voor de Quota's en limieten van Azure cognitieve speech Services
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: alexeyo
ms.openlocfilehash: 7fe2942250e165606175392d7749becda0cff23b
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645123"
---
# <a name="speech-services-quotas-and-limits"></a>Quota en limieten voor Speech Services

Dit artikel bevat een korte naslag informatie en een **gedetailleerde beschrijving** van de Quota's en limieten voor de Azure cognitieve speech-Services voor alle [prijs categorieën](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Het bevat ook enkele aanbevolen procedures om het beperken van aanvragen te voor komen. 

## <a name="quotas-and-limits-quick-reference"></a>Naslag informatie over quota's en beperkingen
Overschakelen naar de [quota en limieten voor tekst naar spraak](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Quota en limieten voor spraak-naar-tekst per spraakresource
In de onderstaande tabellen zijn para meters zonder ' instel bare ' rij **niet** aanpasbaar voor alle prijs categorieën.

#### <a name="online-transcription"></a>Online transcriptie
Voor gebruik met [spraak-SDK](speech-sdk.md) en/of [spraak-naar-tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Quota | Gratis (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Limiet voor gelijktijdige aanvragen-basis model** | 1 | 100 (standaard waarde) |
| Aanpasbaar | Geen<sup>2</sup> | Ja<sup>2</sup> |
| **Limiet voor gelijktijdige aanvragen-aangepast model** | 1 | 20 (standaard waarde) |
| Aanpasbaar | Geen<sup>2</sup> | Ja<sup>2</sup> |

#### <a name="batch-transcription"></a>Batch-transcriptie
| Quota | Gratis (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| De limiet [voor spraak naar tekst rest API v 2.0 en v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | Batch-transcriptie is niet beschikbaar voor F0 | 300 aanvragen per minuut |
| Maximale grootte van audio-invoer bestand | N.v.t. | 1 GB |
| Maximale grootte van invoer-BLOB (kan meer dan één bestand bevatten, bijvoorbeeld in een zip-archief; Controleer of de maximale bestands grootte hierboven) | N.v.t. | 2,5 GB |
| Maximale grootte van BLOB-container | N.v.t. | 5 GB |
| Maximum aantal blobs per container | N.v.t. | 10.000 |
| Maximum aantal bestanden per transcriptie-aanvraag (wanneer meerdere inhouds-Url's als invoer worden gebruikt) | N.v.t. | 1000  |
| Maximum aantal gelijktijdig uitgevoerde taken | N.v.t. | 2000  |

#### <a name="model-customization"></a>Model aanpassing
| Quota | Gratis (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| REST API limiet | 300 aanvragen per minuut | 300 aanvragen per minuut |
| Maximum aantal spraak gegevens sets | 2 | 500 |
| Maximale akoestische gegevensset-bestands grootte voor het importeren van gegevens | 2 GB | 2 GB |
| Maximale grootte van gegevenssetbestand voor gegevens import | 200 MB | 1,5 GB |
| Maximale grootte van de uitspraak van gegevensset voor gegevens import | 1 kB | 1 MB |
| Maximale teken grootte bij het gebruik `text` van para meter in [Create model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API-aanvraag | 200 kB | 500 kB |

de prijs categorie <sup>1</sup> voor **gratis (F0)** Zie ook maandelijkse vergoedingen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> Zie [aanvullende uitleg](#detailed-description-quota-adjustment-and-best-practices), [Aanbevolen procedures](#general-best-practices-to-mitigate-throttling-during-autoscaling)en [correctie-instructies](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Quota voor tekst naar spraak en limieten per spraak resource
In de onderstaande tabel zijn para meters zonder ' aanpas bare ' rij **niet** aanpasbaar voor alle prijs categorieën.

| Quota | Gratis (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| **Maximum aantal trans acties per seconde (TPS) voor standaard-en Neural stemmen** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limiet voor gelijktijdige aanvragen voor aangepaste spraak** |  |  |
| Standaardwaarde | 10 | 10 |
| Aanpasbaar | Geen<sup>5</sup> | Ja<sup>5</sup> |
| **HTTP-specifieke quota's** |  |
| Maximale audio duur die per aanvraag wordt geproduceerd | 10 minuten | 10 minuten |
| Maximum aantal afzonderlijke `<voice>` Tags in SSML | 50 | 50 |
| **WebSocket-specifieke quota's** |  |  |
|Maximale audio duur per beurt | 10 minuten | 10 minuten |
|Maximale SSML bericht grootte per draaiing |64 kB |64 kB |
| **REST API limiet** | 20 aanvragen per minuut | 300 aanvragen per minuut |


de prijs categorie <sup>3</sup> voor **gratis (F0)** Zie ook maandelijkse vergoedingen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> Zie [aanvullende uitleg](#detailed-description-quota-adjustment-and-best-practices) en [Aanbevolen procedures](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> Zie [aanvullende uitleg](#detailed-description-quota-adjustment-and-best-practices), [Aanbevolen procedures](#general-best-practices-to-mitigate-throttling-during-autoscaling)en [correctie-instructies](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Gedetailleerde beschrijving, quotum aanpassing en aanbevolen procedures
Voordat een quota verhoging (indien van toepassing) wordt aangevraagd, moet dit nodig zijn. De speech-service maakt gebruik van technologieën voor automatisch schalen om de vereiste reken bronnen in de modus on-demand te zetten en tegelijkertijd de klant kosten te verlagen door geen buitensporige hoeveelheid hardware-capaciteit te behouden. Telkens wanneer uw toepassing een antwoord code 429 ontvangt ("te veel aanvragen") terwijl uw werk belasting binnen de gedefinieerde limieten valt (Zie [quota en limieten](#quotas-and-limits-quick-reference)), is het waarschijnlijk dat de service omhoog wordt geschaald en dat de vereiste schaal nog niet is bereikt, dus niet onmiddellijk voldoende resources heeft om de aanvraag te verwerken. Deze status is doorgaans tijdelijk en mag niet langer zijn.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Algemene aanbevolen procedures voor het beperken van beperking bij automatisch schalen
Voor het minimaliseren van problemen met betrekking tot het beperken van beperkingen (respons code 429) wordt u aangeraden de volgende technieken te gebruiken:
- Logica voor opnieuw proberen implementeren in uw toepassing
- Vermijd scherpe wijzigingen in de werk belasting. De werk belasting geleidelijk verhogen <br/>
*Voorbeeld.* Uw toepassing gebruikt tekst-naar-spraak en uw huidige workload is 5 TPS (trans acties per seconde). De volgende seconde verhoogt u de belasting tot 20 TPS (dat is vier maal meer). De service begint direct omhoog te schalen om te voldoen aan de nieuwe belasting, maar dit zal er waarschijnlijk toe leiden dat deze niet binnen een tweede kan worden uitgevoerd, dus sommige aanvragen zullen antwoord code 429 ontvangen.   
- Andere toename patronen voor de belasting testen
  - Zie [voor beeld van spraak naar tekst](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Maak extra spraak bronnen in dezelfde of verschillende regio's en verdeel de werk belasting ertussen met behulp van de ' Round Robin ' techniek. Dit is vooral belang rijk voor **tekst-naar-spraak TPS (trans acties per seconde)** , die is ingesteld als 200 per spraak resource en niet kan worden aangepast  

In de volgende secties worden specifieke gevallen van het aanpassen van quota's beschreven.<br/>
Ga naar [tekst-naar-spraak. Limiet voor transcriptie gelijktijdige aanvragen voor aangepaste spraak verhogen](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Spraak naar tekst: verhogen van de limiet voor gelijktijdige aanvragen voor online transcriptie
Het aantal gelijktijdige aanvragen is standaard beperkt tot 20 per spraak bron (basis model) of per aangepast eind punt (aangepast model). Dit bedrag kan worden verhoogd voor de prijs categorie Standard. Zorg ervoor dat u bekend bent met het materiaal in [deze sectie](#detailed-description-quota-adjustment-and-best-practices) en op de hoogte bent van deze [Aanbevolen procedures](#general-best-practices-to-mitigate-throttling-during-autoscaling)voordat u de aanvraag indient.

Het verhogen van de limiet voor gelijktijdige aanvragen heeft **geen** invloed op uw kosten. Spraak Services maakt gebruik van het model ' alleen betalen voor wat u gebruikt '. De limiet bepaalt hoe hoog de service kan worden geschaald voordat uw aanvragen worden vertraagd.

Gelijktijdige aanvraag limieten voor **basis** -en **aangepaste** modellen moeten **afzonderlijk** worden aangepast.

De bestaande waarde van de para meter voor gelijktijdige aanvraag limiet is **niet** zichtbaar via Azure Portal, Command-Line-hulpprogram MA'S of API-aanvragen. Als u de bestaande waarde wilt verifiëren, maakt u een ondersteunings aanvraag voor Azure.

>[!NOTE]
>Voor [spraak containers](speech-container-howto.md) is geen verhoging van de limiet van gelijktijdige aanvragen vereist, omdat containers alleen worden beperkt door de cpu's van de hardware waarop ze worden gehost. Spraak containers hebben echter hun eigen capaciteits beperkingen die moeten worden opgenomen in het account. Zie de vraag *"kunt u helpen met capaciteits planning en kosten schatting van on-premises spraak-naar-tekst containers?"* uit de [Veelgestelde vragen over spraak containers](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>De vereiste gegevens zijn gereed:
- Voor **basis model**:
  - Spraak Resource-ID
  - Region
- Voor **aangepast model**: 
  - Region
  - Aangepaste eind punt-ID

- **Informatie over het ophalen van gegevens (basis model)**:  
  - Ga naar [Azure Portal](https://portal.azure.com/)
  - Selecteer de spraak bron waarvoor u de limiet voor gelijktijdige aanvragen wilt verhogen
  - *Eigenschappen* selecteren (*resource beheer* groep) 
  - Kopieer de waarden van de volgende velden en sla deze op:
    - **Resource-id**
    - **Locatie** (uw eindpunt regio)

- **Informatie over het ophalen van gegevens (aangepast model)**:
  - Ga naar de [Speech Studio](https://speech.microsoft.com/) -Portal
  - Aanmelden indien nodig
  - Ga naar Custom Speech
  - Selecteer uw project
  - Ga naar *implementatie*
  - Selecteer het vereiste eind punt
  - Kopieer de waarden van de volgende velden en sla deze op:
    - **Service regio** (uw eindpunt regio)
    - **Eind punt-ID**
  
#### <a name="create-and-submit-support-request"></a>Ondersteunings aanvraag maken en verzenden
Begin met het verhogen van de limiet voor gelijktijdige aanvragen voor uw resource of Controleer, indien nodig, de huidige limiet door de ondersteunings aanvraag in te dienen:

- Zorg ervoor dat u de [vereiste gegevens](#have-the-required-information-ready) hebt
- Ga naar [Azure Portal](https://portal.azure.com/)
- Selecteer de spraak bron waarvan u wilt verg Roten (of om te controleren) de limiet voor gelijktijdige aanvragen
- *Nieuwe ondersteunings aanvraag* selecteren (*ondersteuning en groep voor probleem oplossing* ) 
- Er wordt een nieuw venster weer gegeven met automatisch gevulde informatie over uw Azure-abonnement en Azure-resource
- Voer een *samen vatting* in (bijvoorbeeld ' verg Roten STT gelijktijdigheids aanvraag limiet ')
- Selecteer bij *probleem type* de optie quotum-of abonnements problemen
- Selecteer in verschenen *soort probleem* :
  - "Quotum of gelijktijdige aanvragen verhogen": voor een verhogings aanvraag
  - Validatie van quotum of gebruik om de bestaande limiet te controleren
- Klik op *volgende: oplossingen*
- Ga verder met het maken van de aanvraag
- Typ in het veld *Beschrijving* op het tabblad *Details* :
  - een opmerking: de aanvraag is over het quotum **voor spraak naar tekst**
  - **Basis** -of **aangepast** model
  - Azure-resource gegevens die u hebt [verzameld vóór](#have-the-required-information-ready) 
  - Voer de vereiste gegevens in en klik op de knop *maken* op het tabblad *controleren en maken*
  - Noteer het nummer van de ondersteunings aanvraag in Azure Portal meldingen. U neemt binnenkort contact met u op voor verdere verwerking

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Spraak naar tekst: voor beeld van een werkbelasting patroon best practice
In dit voor beeld wordt de volgende procedure aanbevolen om mogelijke aanvraag beperking te beperken omdat automatisch [schalen wordt uitgevoerd](#detailed-description-quota-adjustment-and-best-practices). Het is geen ' precies recepten ', maar er wordt alleen een sjabloon uitgenodigd om te volgen en zo nodig aan te passen.

Stel dat er voor een spraak bron de limiet voor gelijktijdige aanvragen is ingesteld op 300. Start de workload vanaf 20 gelijktijdige verbindingen en verg root de belasting met 20 gelijktijdige verbindingen om de 1,5-2 minuten. Beheer de antwoorden van de service en implementeer de logica die terugvalt (de belasting wordt verminderd) als u te veel antwoord codes 429. Probeer het opnieuw over een patroon van 1-2-4-4 minuten. (De belasting verhoging wordt in 1 minuut opnieuw uitgevoerd, als nog steeds niet werkt, daarna in 2 min, enzovoort)

Over het algemeen is het raadzaam om de werk belasting en de werkbelasting patronen te testen voordat u naar de productie gaat.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Tekst-naar-spraak: verhogen van de limiet voor transcriptie gelijktijdige aanvragen voor aangepaste spraak
Het aantal gelijktijdige aanvragen voor een aangepast spraak eind punt is standaard beperkt tot 10. Dit bedrag kan worden verhoogd voor de prijs categorie Standard. Zorg ervoor dat u bekend bent met het materiaal in [deze sectie](#detailed-description-quota-adjustment-and-best-practices) en op de hoogte bent van deze [Aanbevolen procedures](#general-best-practices-to-mitigate-throttling-during-autoscaling)voordat u de aanvraag indient.

Het verhogen van de limiet voor gelijktijdige aanvragen heeft **geen** invloed op uw kosten. Spraak Services maakt gebruik van het model ' alleen betalen voor wat u gebruikt '. De limiet bepaalt hoe hoog de service kan worden geschaald voordat uw aanvragen worden vertraagd.

De bestaande waarde van de para meter voor gelijktijdige aanvraag limiet is **niet** zichtbaar via Azure Portal, Command-Line-hulpprogram MA'S of API-aanvragen. Als u de bestaande waarde wilt verifiëren, maakt u een ondersteunings aanvraag voor Azure.

>[!NOTE]
>Voor [spraak containers](speech-container-howto.md) is geen verhoging van de limiet van gelijktijdige aanvragen vereist, omdat containers alleen worden beperkt door de cpu's van de hardware waarop ze worden gehost.

#### <a name="prepare-the-required-information"></a>De vereiste gegevens voorbereiden:
Als u een verhogings aanvraag wilt maken, moet u de implementatie regio en de aangepaste eind punt-ID opgeven. Voer de volgende acties uit om het bestand op te halen: 

- Ga naar de [Speech Studio](https://speech.microsoft.com/) -Portal
- Aanmelden indien nodig
- Naar *aangepaste spraak*
- Selecteer uw project
- Ga naar *implementatie*
- Selecteer het vereiste eind punt
- Kopieer de waarden van de volgende velden en sla deze op:
    - **Service regio** (uw eindpunt regio)
    - **Eind punt-ID**
  
#### <a name="create-and-submit-support-request"></a>Ondersteunings aanvraag maken en verzenden
Begin met het verhogen van de limiet voor gelijktijdige aanvragen voor uw resource of Controleer, indien nodig, de huidige limiet door de ondersteunings aanvraag in te dienen:

- Zorg ervoor dat u de [vereiste gegevens](#prepare-the-required-information) hebt
- Ga naar [Azure Portal](https://portal.azure.com/)
- Selecteer de spraak bron waarvan u wilt verg Roten (of om te controleren) de limiet voor gelijktijdige aanvragen
- *Nieuwe ondersteunings aanvraag* selecteren (*ondersteuning en groep voor probleem oplossing* ) 
- Er wordt een nieuw venster weer gegeven met automatisch gevulde informatie over uw Azure-abonnement en Azure-resource
- Geef een *samen vatting* op (bijvoorbeeld ' Verhoog het aantal aangepaste TTS-aanvragen voor het afrekenen van een aangepast eind punt)
- Selecteer bij *probleem type* de optie quotum-of abonnements problemen
- Selecteer in verschenen *soort probleem* :
  - "Quotum of gelijktijdige aanvragen verhogen": voor een verhogings aanvraag
  - Validatie van quotum of gebruik om de bestaande limiet te controleren
- Klik op *volgende: oplossingen*
- Ga verder met het maken van de aanvraag
- Typ in het veld *Beschrijving* op het tabblad *Details* :
  - een opmerking: de aanvraag is een quotum **voor tekst naar spraak**
  - Azure-resource gegevens die u hebt [verzameld vóór](#prepare-the-required-information) 
  - Voer de vereiste gegevens in en klik op de knop *maken* op het tabblad *controleren en maken*
  - Noteer het nummer van de ondersteunings aanvraag in Azure Portal meldingen. U neemt binnenkort contact met u op voor verdere verwerking