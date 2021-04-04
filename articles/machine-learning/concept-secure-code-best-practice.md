---
title: Best practices voor veilige code
titleSuffix: Azure Machine Learning
description: Meer informatie over mogelijke beveiligings Risico's die kunnen optreden bij het ontwikkelen van Azure Machine Learning, oplossingen en aanbevolen procedures.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322967"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Aanbevolen procedures voor het beveiligen van code met Azure Machine Learning

In Azure Machine Learning kunt u bestanden en inhoud van elke bron uploaden naar Azure. Inhoud in Jupyter-notitie blokken of scripts die u laadt, kan mogelijk gegevens uit uw sessies lezen, toegang krijgen tot gegevens in uw organisatie in azure, of namens u schadelijke processen uitvoeren.

> [!IMPORTANT]
> Voer alleen notebooks of scripts uit van vertrouwde bronnen. Bijvoorbeeld, waar u of uw beveiligings team het notitie blok of script heeft gecontroleerd.

## <a name="potential-threats"></a>Mogelijke dreigingen

Ontwikkeling met Azure Machine Learning vaak betrekking hebben op webgebaseerde ontwikkel omgevingen (notebooks & Azure ML Studio). Wanneer u webgebaseerde ontwikkel omgevingen gebruikt, zijn de mogelijke dreigingen:

* [Cross-site scripting (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __DOM-injectie__: dit type aanval kan de gebruikers interface die wordt weer gegeven in de browser wijzigen. Bijvoorbeeld door te wijzigen hoe de knop uitvoeren zich gedraagt in een Jupyter Notebook.
    * __Toegangs token/cookies__: XSS-aanvallen hebben ook toegang tot lokale opslag-en browser cookies. Uw Azure Active Directory-verificatie token (AAD) wordt opgeslagen in de lokale opslag. Een XSS-aanval kan dit token gebruiken om namens u API-aanroepen te maken en de gegevens vervolgens naar een extern systeem of API te verzenden.

* [Aanvraag vervalsing op meerdere sites (CSRF)](https://owasp.org/www-community/attacks/csrf): deze aanval kan de URL van een afbeelding of koppeling met de URL van een schadelijk script of API vervangen. Wanneer de afbeelding is geladen of als de koppeling is geklikt, wordt een aanroep naar de URL verzonden.

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio-notebooks

Azure Machine Learning Studio biedt een gehoste laptop ervaring in uw browser. Met cellen in een notitie blok kunt u HTML-documenten of fragmenten die schadelijke code bevatten, uitvoeren.  Wanneer de uitvoer wordt weer gegeven, kan de code worden uitgevoerd.

__Mogelijke dreigingen__:
* Cross-site scripting (XSS)
* Aanvraag vervalsing op meerdere sites (CSRF)

De __beperkingen van Azure machine learning__:
* __Code-cel-uitvoer__ wordt sandbox in een IFRAME. Met iframe wordt voor komen dat het script toegang krijgt tot de bovenliggende DOM, cookies of sessie opslag.
* De inhoud van de __celinhoud__ wordt gereinigd met behulp van de dompurify-bibliotheek. Hiermee wordt voor komen dat schadelijke scripts worden uitgevoerd met een afkorting van cellen.
* De __afbeeldings-URL__ en __prijs verlaging koppelingen__ worden verzonden naar een eind punt van micro soft, waarmee wordt gecontroleerd op schadelijke waarden. Als er een schadelijke waarde wordt gedetecteerd, wordt de aanvraag door het eind punt afgewezen.

__Aanbevolen acties__:
* Controleer of u de inhoud van bestanden vertrouwt voordat u deze uploadt naar Studio. Wanneer u uploadt, moet u bevestigen dat u vertrouwde bestanden uploadt.
* Wanneer u een koppeling selecteert om een externe toepassing te openen, wordt u gevraagd om de toepassing te vertrouwen.

## <a name="azure-ml-compute-instance"></a>Reken instantie van Azure ML

Azure Machine Learning Reken exemplaar fungeert als host voor __Jupyter__ en __Jupyter Lab__. Wanneer u een van beide gebruikt, kunnen cellen in een notitie blok of code in HTML-documenten of fragmenten die schadelijke code bevatten, worden uitgevoerd. Wanneer de uitvoer wordt weer gegeven, kan de code worden uitgevoerd. Dezelfde bedreigingen zijn ook van toepassing wanneer u __RStudio__ gebruikt die wordt gehost op een reken instantie.

__Mogelijke dreigingen__:
* Cross-site scripting (XSS)
* Aanvraag vervalsing op meerdere sites (CSRF)

De __beperkingen van Azure machine learning__:
* Geen. Jupyter en Jupyter Lab zijn open-source toepassingen die worden gehost op het Azure Machine Learning Compute-exemplaar.

__Aanbevolen acties__:
* Controleer of u de inhoud van bestanden vertrouwt voordat u deze uploadt naar Studio. Wanneer u uploadt, moet u bevestigen dat u vertrouwde bestanden uploadt.

## <a name="report-security-issues-or-concerns"></a>Beveiligings problemen of problemen melden 

Azure Machine Learning komt in aanmerking onder het Microsoft Azure Bounty-programma. Ga voor meer informatie naar  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Volgende stappen

* [Enter prise Security voor Azure Machine Learning](concept-enterprise-security.md)