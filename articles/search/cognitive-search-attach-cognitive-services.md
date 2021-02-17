---
title: Cognitive Services koppelen aan een vaardig heden
titleSuffix: Azure Cognitive Search
description: Meer informatie over het koppelen van een Cognitive Services alles-in-één-abonnement aan een AI-pijp lijn in azure Cognitive Search.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577924"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Een Cognitive Services resource koppelen aan een vaardig heden in azure Cognitive Search

Bij het configureren van een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) in azure Cognitive Search, kunt u een beperkt aantal gratis documenten verrijken. Voor grotere en frequentere werk belastingen moet u een factureer bare "alles-in-één"-Cognitive Services resource bijvoegen. Een ' alles-in-één ' abonnement verwijst naar ' Cognitive Services ' als aanbieding, in plaats van afzonderlijke services, met toegang via één API-sleutel.

Een ' alles-in-één ' Cognitive Services resource stations de [vooraf gedefinieerde vaardig heden](cognitive-search-predefined-skills.md) die u kunt toevoegen in een vakkennisset:

+ [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) voor afbeeldings analyse en optische teken herkenning (OCR)
+ [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voor taal detectie, entiteits herkenning, sentiment analyse en sleutel woordgroepen extractie
+ [Tekstomzetting](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Een ' alles-in-één ' Cognitive Services sleutel is optioneel in een definitie van een vakkennisset. Wanneer het aantal dagelijkse trans acties lager is dan 20 per dag, worden de kosten geabsorbeerd. Als trans acties echter het nummer overschrijden, is een geldige resource sleutel vereist om de verwerking voort te zetten.

De resource sleutel alles-in-één is geldig. Intern gebruikt een zoek service de resource die zich in dezelfde fysieke regio naast elkaar bevindt, zelfs als de ' alles-in-één '-sleutel voor een resource in een andere regio is. Op de pagina [product beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=search) wordt de regionale Beschik baarheid naast elkaar weer gegeven.

> [!NOTE]
> Als u vooraf gedefinieerde vaardig heden in een vaardighedenset weglaat, wordt Cognitive Services niet geopend en worden er geen kosten in rekening gebracht, zelfs niet als de vaardig heden een sleutel opgeeft.

## <a name="how-billing-works"></a>Werking van facturering

+ Azure Cognitive Search maakt gebruik van de Cognitive Services resource sleutel die u op een vaardighedenset hebt geboden om afbeeldings-en tekst verrijking te factureren. Het uitvoeren van factureer bare vaardig heden bevindt zich op de Cognitive Services prijs op basis van [betalen per gebruik](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ Afbeeldings extractie is een Azure Cognitive Search bewerking die optreedt wanneer documenten worden gekraakt voordat verrijking wordt uitgevoerd. De afbeeldings extractie is Factureerbaar. Zie de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)voor meer informatie over het ophalen van images.

+ Tekst extractie vindt ook plaats tijdens het kraken van documenten. Het is niet factureerbaar.

+ Vaardig heden die geen Cognitive Services aanroepen, zoals voorwaardelijke tekst, shaper, tekst samenvoeging en gesplitste vaardig heden, zijn niet factureerbaar.

## <a name="same-region-requirement"></a>Dezelfde regio vereiste

Zowel Cognitive Search als Cognitive Services moeten binnen dezelfde fysieke regio bestaan, zoals aangegeven op de pagina [product beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=search) . De meeste regio's die Cognitive Search bieden, bieden ook Cognitive Services.

Als u AI-verrijking wilt uitvoeren in een regio die niet beide services bevat, ziet u dit bericht: de ' de vermelde sleutel is geen geldige CognitiveServices-sleutel voor de regio van uw zoek service. '

> [!NOTE]
> Sommige ingebouwde vaardig heden zijn gebaseerd op niet-regionale Cognitive Services (bijvoorbeeld de [tekst Vertaal vaardigheid](cognitive-search-skill-text-translation.md)). Als u een niet-regionale vaardigheid gebruikt, kan het zijn dat uw aanvraag wordt verwerkt in een andere regio dan de Azure Cognitive Search-regio. Zie de pagina [Cognitive Services product per regio](https://aka.ms/allinoneregioninfo) voor meer informatie over niet-regionale Services.

## <a name="use-free-resources"></a>Gratis bronnen gebruiken

U kunt een beperkte, gratis verwerkings optie gebruiken voor het volt ooien van de zelf studie voor AI-verrijking en Snelstartgids.

Gratis (beperkte verrijkingen) resources zijn beperkt tot 20 documenten per dag, per Indexeer functie. U kunt [de Indexeer functie opnieuw instellen](search-howto-run-reset-indexers.md) om de teller opnieuw in te stellen.

Als u de wizard **gegevens importeren** gebruikt voor AI-verrijking, vindt u de opties ' koppelen Cognitive Services ' op de pagina **AI-verrijking toevoegen (optioneel)** .

![Sectie uitgebreide Cognitive Services toevoegen](./media/cognitive-search-attach-cognitive-services/attach1.png "Sectie uitgebreide Cognitive Services toevoegen")

## <a name="use-billable-resources"></a>Factureer bare resources gebruiken

Voor workloads die meer dan 20 verrijkingen per dag maken, moet u ervoor zorgen dat u een factureer bare Cognitive Services resource koppelt. U wordt aangeraden altijd een factureer bare Cognitive Services resource te koppelen, zelfs als u niet van plan bent om Cognitive Services-API's aan te roepen. Als u een resource koppelt, wordt de dagelijkse limiet overschreven.

Er worden alleen kosten in rekening gebracht voor de vaardig heden die de Cognitive Services-API's aanroepen. U wordt niet gefactureerd voor [aangepaste vaardig heden](cognitive-search-create-custom-skill-example.md)of vaardig heden zoals [tekst fusie](cognitive-search-skill-textmerger.md), [tekst splitsing](cognitive-search-skill-textsplit.md)en [shaper](cognitive-search-skill-shaper.md), die geen API-gebaseerd zijn.

Als u de wizard **gegevens importeren** gebruikt, kunt u een factureer bare resource configureren op de pagina **AI-verrijking toevoegen (optioneel)** .

1. Vouw **Cognitive Services koppelen** uit en selecteer vervolgens **nieuwe Cognitive Services resource maken**. Er wordt een nieuw tabblad geopend, zodat u de resource kunt maken:

   ![Een Cognitive Services-resource maken](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Een Cognitive Services-resource maken")

1. Selecteer in de lijst **locatie** dezelfde regio als de zoek service.

1. Selecteer in de lijst **prijs categorie** de optie **s0** om de alles-in-één-verzameling van Cognitive Services-functies op te halen, met inbegrip van de visie-en taal functies die de ingebouwde vaardig heden van Azure Cognitive Search.

   Voor de S0-laag kunt u tarieven voor specifieke werk belastingen vinden op de [pagina met Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Zorg ervoor dat **Cognitive Services** is geselecteerd in de lijst **aanbieding selecteren** .
   + Onder **taal** functies zijn de tarieven voor **Text Analytics standaard** van toepassing op AI-indexering.
   + Onder **Vision** -functies zijn de tarieven voor **Computer Vision S1** van toepassing.

1. Selecteer **maken** om de nieuwe Cognitive Services resource in te richten.

1. Ga terug naar het vorige tabblad. Selecteer **vernieuwen** om de Cognitive Services resource weer te geven en selecteer vervolgens de resource:

   ![De Cognitive Services resource selecteren](./media/cognitive-search-attach-cognitive-services/attach2.png "De Cognitive Services resource selecteren")

1. Vouw de sectie **cognitieve vaardig heden toevoegen** uit om de specifieke cognitieve vaardig heden te selecteren die u wilt uitvoeren op uw gegevens. Voltooi de rest van de wizard.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Een bestaande vaardig heden koppelen aan een Cognitive Services resource

Als u een bestaande vaardig heden hebt, kunt u deze koppelen aan een nieuwe of andere Cognitive Services resource.

1. Selecteer op de pagina overzicht van de zoek service **vaardig heden**:

   ![Tabblad vaardig heden](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Tabblad vaardig heden")

1. Selecteer de naam van de vaardig heden en selecteer vervolgens een bestaande resource of maak een nieuwe. Selecteer **OK** om uw wijzigingen te bevestigen.

   ![Lijst met vaardig heden-resources](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lijst met vaardig heden-resources")

   Houd er rekening mee dat de optie **gratis (beperkte verrijkingen)** u per dag beperkt tot 20 documenten en dat u **nieuwe Cognitive Services resource maken** kunt gebruiken om een nieuwe factureer bare resource in te richten. Als u een nieuwe resource maakt, selecteert u **vernieuwen** om de lijst met Cognitive services resources te vernieuwen en selecteert u vervolgens de resource.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services via een programma koppelen

Wanneer u de vaardig heden programmatisch definieert, voegt u een `cognitiveServices` sectie toe aan de vaardig heden. In dat gedeelte moet u de sleutel van de Cognitive Services resource die u wilt koppelen aan de vaardig heden, toevoegen. Houd er rekening mee dat de resource zich in dezelfde regio als uw Azure Cognitive Search-resource moet bevinden. Neem ook de `@odata.type` naam op en stel deze in op `#Microsoft.Azure.Search.CognitiveServicesByKey` .

In het volgende voor beeld ziet u dit patroon. U ziet de `cognitiveServices` sectie aan het einde van de definitie.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Voor beeld: kosten schatten

Als u een schatting wilt maken van de kosten die zijn gekoppeld aan het indexeren van cognitieve Zoek opdrachten, begint u met een idee van wat een gemiddeld document lijkt te zijn, zodat u enkele getallen kunt uitvoeren. U kunt bijvoorbeeld ongeveer het volgende doen:

+ 1.000 Pdf's.
+ Zes pagina's per.
+ Eén afbeelding per pagina (6.000 afbeeldingen).
+ 3.000 tekens per pagina.

Stel dat u een pijp lijn hebt die bestaat uit het kraken van een document van elke PDF, extractie van afbeeldingen en tekst, optische teken herkenning (OCR) van afbeeldingen en entiteits herkenning van organisaties.

De prijzen die in dit artikel worden weer gegeven, zijn hypothetisch. Ze worden gebruikt om het schattings proces te illustreren. De kosten zijn lager. Zie [Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services)voor de werkelijke prijzen van trans acties.

1. Voor het kraken van documenten met tekst-en afbeeldings inhoud is tekst extractie momenteel gratis. Voor 6.000 installatie kopieën wordt aangenomen dat $1 voor elke 1.000 installatie kopieën worden geëxtraheerd. Dat is een prijs van $6,00 voor deze stap.

2. Voor OCR van 6.000-afbeeldingen in het Engels gebruikt de OCR cognitieve vaardigheid het beste algoritme (DescribeText). Uitgaande van een kosten van $2,50 per 1.000 installatie kopieën, betaalt u $15,00 voor deze stap.

3. Voor het uitpakken van de entiteit hebt u per pagina in totaal drie tekst records. Elke record is 1.000 tekens. Drie tekst records per pagina vermenigvuldigd met 6.000 pagina's is gelijk aan 18.000 tekst records. Als er $2,00 per 1.000-tekst records worden berekend, wordt deze stap $36,00.

U betaalt ongeveer $57,00 tot opname van 1.000 PDF-documenten van dit type met de beschreven vaardig heden.

## <a name="next-steps"></a>Volgende stappen

+ [Pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)