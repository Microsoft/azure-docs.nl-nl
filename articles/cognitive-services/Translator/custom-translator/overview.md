---
title: Wat is Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator biedt vergelijkbare mogelijkheden als Microsoft Translator Hub voor statistische machinevertalingen (SMT), maar dan uitsluitend voor systemen voor neurale machinevertalingen (NMT).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 37d31d753812924607c4d31df6d4f4d5e7b32d69
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890710"
---
# <a name="what-is-custom-translator"></a>Wat is Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) is een functie van de service Microsoft Translator. Hiermee kunnen vertaalondernemingen, app-ontwikkelaars en aanbieders van taaldiensten aangepaste systemen voor neurale machinevertalingen bouwen. De aangepaste vertaalsystemen integreren naadloos met bestaande toepassingen, werkstromen en websites.

Vertaalsystemen die zijn gebouwd met [Custom Translator](https://portal.customtranslator.azure.ai) zijn beschikbaar via dezelfde [beveiligde](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), krachtige en uiterst schaalbare Microsoft Translator [Text-API V3](../reference/v3-0-translate.md?tabs=curl) in de cloud, die elke dag wordt gebruikt voor miljarden vertalingen.

Custom Translator biedt ondersteuning voor meer dan 36 talen en is rechtstreeks gekoppeld aan de beschikbare talen voor NMT. Zie de [talen van Microsoft Translator](../language-support.md#customization) voor een volledige lijst.

## <a name="features"></a>Functies

Custom Translator biedt verschillende mogelijkheden voor het bouwen van een aangepast vertaalsysteem en het verkrijgen van toegang tot het aangepaste vertaalsysteem.

|Functie  |Beschrijving  |
|---------|---------|
|[Maak gebruik van neurale technologie voor automatische vertalingen](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Verbeter de vertalingen door gebruik te maken van neurale machinevertalingen (NMT) van Custom Translator.       |
|[Bouw systemen met uw eigen vaktermen](what-are-parallel-documents.md)     |  Bouw vertaalsystemen en pas vertaalsystemen aan met behulp van parallelle documenten in de terminologie van uw eigen bedrijf en bedrijfstak.       |
|[Een woordenlijst gebruiken om uw modellen te bouwen](what-is-dictionary.md)     |   Als u geen set met trainingsgegevens hebt, kunt u een model trainen met alleen de gegevens van een woordenlijst.       |
|[Werk samen met anderen](how-to-manage-settings.md#share-your-workspace)     |   Werk samen met uw team door uw werk te delen met andere personen.     |
|[Toegang tot uw aangepaste vertaalmodel](../reference/v3-0-translate.md?tabs=curl)     |  Uw aangepaste vertaalmodel kan op elk gewenst moment worden geopend door uw bestaande toepassingen/programma's via Microsoft Translator Text-API V3.       |

## <a name="get-better-translations"></a>Betere vertalingen verkrijgen

[Neurale machinevertalingen (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) van Microsoft Translator is uitgebracht in 2016. NMT biedt belangrijke voordelen in de vertaalkwaliteit ten opzichte van de industriestandaardtechnologie van [statistische machinevertalingen (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation). Omdat NMT beter in staat is de context van volledige zinnen te begrijpen alvorens ze te vertalen, levert NMT vertalingen van hogere kwaliteit, die menselijker klinken en vloeiender lopen. [Custom Translator](https://portal.customtranslator.azure.ai) maakt gebruik van NMT voor uw aangepaste modellen, hetgeen resulteert in vertalingen van betere kwaliteit.

U kunt een vertaalsysteem opbouwen met behulp van eerder vertaalde documenten. Dergelijke documenten bevatten specifieke terminologie en hebben ook een specifieke stijl, wat beter is dan een vertaling van een standaard vertaalsysteem. Gebruikers kunnen ALIGN-, PDF-, LCL-, HTML-, HTM-, XLF-, TMX-, XLIFF-, TXT-, DOCX- en XLSX-documenten uploaden.

Custom Translator accepteert ook parallelle gegevens op documentniveau om gegevens effectiever te kunnen verzamelen en voorbereiden. Als gebruikers toegang hebben tot versies van dezelfde inhoud in meerdere talen, maar wel in afzonderlijke documenten, is Custom Translator in staat om zinnen uit verschillende documenten automatisch te matchen.

Met het juiste type en de juiste hoeveelheid trainingsgegevens, zijn stijgingen van 5 tot 10 punten in de [BLEU-score](what-is-bleu-score.md) niet ongebruikelijk als u Custom Translator gebruikt.

## <a name="be-productive-and-cost-effective"></a>Werk productief en rendabel

Met [Custom Translator](https://portal.customtranslator.azure.ai) zijn er geen programmeervaardigheden nodig voor het trainen en implementeren van een aangepast systeem.

Met behulp van de beveiligde [Custom Translator](https://portal.customtranslator.azure.ai)-portal, kunnen gebruikers trainingsgegevens uploaden, systemen trainen, testen en in een productie-omgeving implementeren via een intuïtieve gebruikersinterface. Het systeem kan vervolgens op grote schaal worden gebruikt binnen enkele uren (de werkelijke tijd is afhankelijk van de hoeveelheid trainingsgegevens).

[Custom Translator](https://portal.customtranslator.azure.ai) is ook programmatisch toegankelijk via een [toewezen API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (momenteel als Preview-versie). Met de API kunnen gebruikers regelmatig trainingsgegevens maken, bijwerken en beheren via hun eigen app of webservice.

De kosten voor het gebruik van een aangepast model voor het vertalen van inhoud zijn afhankelijk van de prijscategorie voor de Translator Text-API van de gebruiker. Zie de [webpagina met prijzen voor Translator Text-API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) voor Cognitive Services voor meer informatie over de prijscategorieën.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Altijd en overal veilig vertalen in al uw apps en services

Aangepaste systemen kunnen naadloos worden geopend en geïntegreerd in een product- of bedrijfswerkstroom, en op elk gewenst apparaat, via de Microsoft Translator Text-API met behulp van standaard REST-technologie.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Leer een vertaalmodel in Custom Translator te bouwen met deze [snelstart](quickstart-build-deploy-custom-model.md).
