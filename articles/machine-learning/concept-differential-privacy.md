---
title: Differentiële privacy in machine learning (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over de differentiële privacy en hoe u differentiatie bare systemen kunt implementeren die de privacy van gegevens behouden.
author: luisquintanilla
ms.author: luquinta
ms.date: 01/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: 39f4b1a7b9eb1ad7a87097240dd772e4f2dadf17
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683533"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Wat is differentiële privacy in machine learning (preview-versie)

Meer informatie over de differentiële privacy in machine learning en hoe deze werkt.

Als de hoeveelheid gegevens die een organisatie voor het verzamelen en gebruiken van analyses toeneemt, is dit van belang voor privacy en beveiliging. Voor analyses zijn gegevens vereist. Normaal gesp roken worden de gegevens die worden gebruikt om modellen te trainen, nauw keuriger. Wanneer persoonlijke gegevens voor deze analyses worden gebruikt, is het met name belang rijk dat de gegevens gedurende het hele gebruik persoonlijk blijven.

## <a name="how-differential-privacy-works"></a>Hoe differentiële privacy werkt

Differentiële privacy is een verzameling systemen en procedures die u helpen de gegevens van personen veilig en privé te houden.

> [!div class="mx-imgBorder"]
> ![machine learning proces voor differentiële privacy](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

In traditionele scenario's worden onbewerkte gegevens opgeslagen in bestanden en data bases. Wanneer gebruikers gegevens analyseren, gebruiken ze doorgaans de onbewerkte gegevens. Dit is een probleem omdat dit kan leiden tot inbreuk op de privacy van een persoon. Differentiële privacy probeert dit probleem te verhelpen door "Noise" of wille keurigheid toe te voegen aan de gegevens, zodat gebruikers geen afzonderlijke gegevens punten kunnen identificeren. Een dergelijk systeem biedt ten minste plausible-weigerings mogelijkheden. Daarom wordt de privacy van personen bewaard met beperkte impact op de nauw keurigheid van de gegevens.

In differentiatie bare systemen worden gegevens gedeeld via aanvragen die **query's** worden genoemd. Wanneer een gebruiker een query voor gegevens indient, worden de aangevraagde gegevens door middel van **Privacy-mechanismen** ruis toegevoegd. Privacy-mechanismen retour neren een *benadering van de gegevens* in plaats van de onbewerkte gegevens. Dit privacy-behoud resultaat wordt weer gegeven in een **rapport**. Rapporten bestaan uit twee delen, de werkelijke gegevens die worden berekend en een beschrijving van de manier waarop de gegevens zijn gemaakt.

## <a name="differential-privacy-metrics"></a>Differentiële metrische gegevens over privacy

Differentiële privacy probeert te beveiligen tegen de mogelijkheid dat een gebruiker een onbeperkt aantal rapporten kan maken om uiteindelijk gevoelige gegevens te onthullen. Een waarde die wordt herkend als **Epsilon** , wordt gemeten met een rapport. Epsilon heeft een inverse relatie met ruis of privacy. Hoe lager de letter epsilon, des te meer ruis (en persoonlijke gegevens).

Letter epsilon-waarden zijn niet negatief. De waarden onder 1 bieden volledige plausible-weigering. Alles boven 1 wordt geleverd met een hoger risico op de bloot stelling van de werkelijke gegevens. Bij het implementeren van differentiatie bare systemen, wilt u rapporten maken met de letter epsilon-waarden tussen 0 en 1.

Een andere waarde die rechtstreeks aan Epsilon is gerelateerd, is **Delta**. Delta is een meting van de kans dat een rapport niet volledig privé is. Hoe hoger de Delta, hoe hoger het lettertje. Omdat deze waarden worden gecorreleerd, wordt het gebruik van letter epsilon vaker gebruikt.

## <a name="limit-queries-with-a-privacy-budget"></a>Query's met een privacy-budget beperken

Ter bescherming van de privacy in systemen waar meerdere query's zijn toegestaan, definieert differentiële privacy een frequentie limiet. Deze limiet wordt ook wel een **Privacy-budget** genoemd. Met privacy-budgetten wordt voor komen dat gegevens opnieuw worden gemaakt via meerdere query's. Projectbudgetten worden een letter epsilon toegewezen, meestal tussen 1 en 3 om het risico van het heridentificatieen te beperken. Naarmate er rapporten worden gegenereerd, houden de projectbudgetten de letterlijke waarde van afzonderlijke rapporten en de statistische functie voor alle rapporten bij. Nadat een privacy-budget is uitgegeven of uitgeput, hebben gebruikers geen toegang meer tot gegevens. 

## <a name="reliability-of-data"></a>Betrouw baarheid van gegevens

Hoewel het behoud van privacy het doel is, is er sprake van een afweging wanneer het gaat om de bruikbaarheid en betrouw baarheid van de gegevens. In gegevens analyse kan nauw keurigheid worden beschouwd als een meting van de onzekerheid die door steekproef fouten wordt geïntroduceerd. Deze onzekerheid moet binnen bepaalde grenzen vallen. **Nauw keurigheid** van een differentieel privacy-perspectief meet in plaats daarvan de betrouw baarheid van de gegevens, die worden beïnvloed door de onzekerheid die door de privacyopties wordt geïntroduceerd. Kortom, een hoger niveau van ruis of privacy wordt omgezet in gegevens met een lager lettertje, nauw keurigheid en betrouw baarheid. 

## <a name="open-source-differential-privacy-libraries"></a>Open-source differentiële privacybeleid

SmartNoise is een open-source project dat verschillende onderdelen bevat voor het bouwen van wereld wijde differentiatie systemen. SmartNoise bestaat uit de volgende onderdelen op het hoogste niveau:

- SmartNoise-kern bibliotheek
- SDK-bibliotheek voor SmartNoise

### <a name="smartnoise-core"></a>SmartNoise-kern

De kern bibliotheek bevat de volgende privacy-mechanismen voor het implementeren van een differentiatie systeem:

|Onderdeel  |Beschrijving  |
|---------|---------|
|Analyse     | Een grafiek beschrijving van wille keurige berekeningen. |
|Validator     | Een roest-bibliotheek met een set hulpprogram ma's voor het controleren en afleiden van de benodigde voor waarden voor een analyse die differentiatie privé is.          |
|Runtime     | Het medium om de analyse uit te voeren. De referentie runtime is geschreven in roest, maar runtime kan worden geschreven met behulp van een reken raamwerk zoals SQL en Spark, afhankelijk van de behoeften van uw gegevens.        |
|Bindingen     | Taal bindingen en helper-bibliotheken om analyses te maken. Momenteel SmartNoise biedt python-bindingen. |

### <a name="smartnoise-sdk"></a>SmartNoise-SDK

De systeem bibliotheek bevat de volgende hulpprogram ma's en services voor het werken met tabellaire en relationele gegevens:

|Onderdeel  |Beschrijving  |
|---------|---------|
|Data Access     | Bibliotheek die SQL-query's onderschept en verwerkt en rapporten produceert. Deze bibliotheek is geïmplementeerd in Python en ondersteunt de volgende ODBC-en DBAPI-gegevens bronnen:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Service     | Uitvoerings service die een REST-eind punt biedt om aanvragen of query's te leveren aan gedeelde gegevens bronnen. De service is ontworpen om samen stelling van differentiële privacyfuncties toe te staan die wordt toegepast op aanvragen met verschillende Delta-en Epsilon-waarden, ook wel heterogene aanvragen genoemd. Deze referentie-implementatie accounts voor extra gevolgen van query's op gecorreleerde gegevens. |
|Evaluator     | Stochastische evaluator die controleert op privacy-schendingen, nauw keurigheid en afwijking. De evaluator ondersteunt de volgende tests: <ul><li>Privacy testen: bepaalt of een rapport voldoet aan de voor waarden van differentiële privacy.</li><li>Nauw keurigheid testen: Hiermee wordt bepaald of de betrouw baarheid van rapporten binnen de boven-en ondergrenzen ligt op het betrouwbaarheids niveau 95%.</li><li>Hulp programma testen: Hiermee wordt bepaald of de vertrouwens grenzen van een rapport dicht bij de gegevens worden gesloten terwijl de privacy nog steeds wordt gemaximaliseerd.</li><li>Bias test: meet de distributie van rapporten voor herhaalde query's om ervoor te zorgen dat ze niet worden gesaldeerd</li></ul> |

## <a name="next-steps"></a>Volgende stappen

[Een differentieeel privé systeem bouwen](how-to-differential-privacy.md) in azure machine learning.

Voor meer informatie over de onderdelen van SmartNoise raadpleegt u de GitHub-opslag plaatsen voor [SmartNoise core](https://github.com/opendifferentialprivacy/smartnoise-core), [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)en [SmartNoise samples](https://github.com/opendifferentialprivacy/smartnoise-samples).