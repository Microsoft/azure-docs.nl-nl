---
title: XML transformeren tussen indelingen
description: Trans formaties of kaarten maken die XML converteren tussen indelingen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654129"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kaarten maken die XML transformeren tussen indelingen in Azure Logic Apps met Enterprise Integration Pack

De Enterprise Integration Transform-connector converteert gegevens van de ene indeling naar een andere indeling. Mogelijk hebt u bijvoorbeeld een binnenkomend bericht met de huidige datum in de JaarMaandDag-indeling. U kunt een transformatie gebruiken om de datum opnieuw in te delen in de MaadDagJaar-indeling.

## <a name="what-does-a-transform-do"></a>Wat doet een trans formatie?
Een trans formatie, ook wel een toewijzing genoemd, bestaat uit een XML-bron schema (de invoer) en een XML-doel schema (de uitvoer). U kunt verschillende ingebouwde functies gebruiken om de gegevens te bewerken of te beheren, met inbegrip van teken reeksen, voorwaardelijke toewijzingen, wiskundige expressies, datum notaties en zelfs herhalings constructies.

## <a name="how-to-create-a-transform"></a>Hoe kan ik een trans formatie maken?
U kunt een trans formatie/toewijzing maken met behulp van de Visual Studio [BEDRIJFSINTEGRATIE SDK](https://aka.ms/vsmapsandschemas). Wanneer u klaar bent met het maken en testen van de trans formatie, uploadt u de trans formatie naar uw integratie account. 

## <a name="how-to-use-a-transform"></a>Een trans formatie gebruiken
Nadat u de trans formatie/toewijzing hebt geüpload in uw integratie account, kunt u deze gebruiken om een logische app te maken. De logische app voert uw trans formaties uit wanneer de logische app wordt geactiveerd (en er is invoer inhoud die moet worden getransformeerd).

**Hier volgen de stappen voor het gebruik van een trans formatie**:

### <a name="prerequisites"></a>Vereisten

* Een integratie account maken en hieraan een kaart toevoegen  

Nu u de vereiste onderdelen hebt gemaakt, is het tijd om uw logische app te maken:  

1. Maak een logische app en [koppel deze aan uw integratie account](./logic-apps-enterprise-integration-create-integration-account.md "Meer informatie over het koppelen van een integratie account aan een logische app") dat de kaart bevat.
2. Een **aanvraag** trigger toevoegen aan uw logische app  
   ![Scherm afbeelding van de vervolg keuzelijst ' door micro soft beheerde Api's weer geven ' terwijl de trigger voor de aanvraag is geselecteerd. De vervolg keuzelijst bevindt zich in een logische app die is gemaakt met behulp van de Visual Studio Bedrijfsintegratie SDK.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Voeg eerst **een actie toevoegen** toe om de **trans formatie XML-** actie te selecteren   
   ![Scherm afbeelding met de knop voor het toevoegen van een actie die is geselecteerd in het scherm voor het activeren van de aanvraag.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Voer de woord *transformatie* in het zoekvak in om alle acties die u wilt gebruiken, te filteren.  
   ![Scherm afbeelding die laat zien hoe u kunt zoeken naar de trans formatie-XML in de vervolg keuzelijst ' micro soft Managed Api's weer geven ' zodat deze kan worden toegevoegd aan de aanvraag trigger.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. De actie **XML-trans formatie** selecteren   
6. Voeg de XML- **inhoud** toe die u transformeert. U kunt alle XML-gegevens die u in de HTTP-aanvraag ontvangt als de **inhoud** gebruiken. Selecteer in dit voor beeld de hoofd tekst van de HTTP-aanvraag die de logische app heeft geactiveerd.

   > [!NOTE]
   > Zorg ervoor dat de inhoud voor de **trans formatie XML** XML is. Als de inhoud zich niet in XML of Base64-gecodeerd bevindt, moet u een expressie opgeven waarmee de inhoud wordt verwerkt. U kunt bijvoorbeeld [functies](logic-apps-workflow-definition-language.md#functions)gebruiken, zoals ```@base64ToBinary``` voor het decoderen van inhoud of ```@xml``` voor het verwerken van de inhoud als XML.
 

7. Selecteer de naam van de **kaart** die u wilt gebruiken om de trans formatie uit te voeren. De kaart moet al aanwezig zijn in uw integratie account. In een eerdere stap hebt u uw logische app al toegang gegeven tot uw integratie account dat de kaart bevat.      
   ![Scherm opname van de velden inhoud en kaart in het XML-venster trans formatie voor de trigger van de aanvraag.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uw werk opslaan  
    ![Scherm afbeelding met de knop Opslaan in de Logic Apps Designer.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

U bent nu klaar met het instellen van de kaart. In een echte wereld toepassing wilt u mogelijk de getransformeerde gegevens opslaan in een LOB-toepassing, zoals Sales Force. U kunt gemakkelijk als een actie de uitvoer van de trans formatie naar Sales Force verzenden. 

U kunt nu uw trans formatie testen door een aanvraag voor het HTTP-eind punt te maken.  


## <a name="features-and-use-cases"></a>Functies en use cases
* De trans formatie die is gemaakt in een kaart kan eenvoudig zijn, zoals het kopiëren van een naam en adres van het ene document naar het andere. U kunt ook complexere trans formaties maken met behulp van de out-of-the-box-toewijzings bewerkingen.  
* Meerdere toewijzings bewerkingen of functies zijn direct beschikbaar, waaronder teken reeksen, datum tijd functies, enzovoort.  
* U kunt een rechtstreekse gegevens kopie tussen de schema's uitvoeren. In de toewijzing die in de SDK is opgenomen, is dit net zo eenvoudig als het tekenen van een lijn die de elementen in het bron schema verbindt met hun equivalenten in het doel schema.  
* Wanneer u een kaart maakt, bekijkt u een grafische weer gave van de kaart, waarin alle relaties en koppelingen worden weer gegeven die u maakt.
* Gebruik de functie toewijzing testen om een XML-voorbeeld bericht toe te voegen. Met een eenvoudige klik kunt u de kaart die u hebt gemaakt, testen en de gegenereerde uitvoer bekijken.  
* Bestaande kaarten uploaden  
* Biedt ondersteuning voor de XML-indeling.

## <a name="advanced-features"></a>Geavanceerde functies

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referentie-assembly of aangepaste code van Maps 
De trans formatie-actie ondersteunt ook kaarten of trans formaties met verwijzing naar externe assembly. Met deze mogelijkheid kunnen aangepaste .NET-code rechtstreeks vanuit XSLT-kaarten worden aangeroepen. Hier volgen de vereisten voor het gebruik van assembly in Maps.

* De kaart en de assembly waarnaar wordt verwezen vanuit de kaart, moeten worden [geüpload naar het integratie account](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > De toewijzings-en assembly moeten in een specifieke volg orde worden geüpload. U moet de assembly uploaden voordat u de kaart uploadt die verwijst naar de assembly.

* De kaart moet ook deze kenmerken hebben en een CDATA-sectie die de aanroep van de assembly code bevat:

    * **name** de naam van de aangepaste assembly is.
    * **naam ruimte** is de naam ruimte in de assembly die de aangepaste code bevat.

  In dit voor beeld ziet u een kaart die verwijst naar een assembly met de naam ' XslUtilitiesLib ' en wordt de `circumreference` methode aangeroepen vanuit de assembly.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Byte order markering
Standaard begint de reactie van de trans formatie met de byte order Mark (BOM). U hebt alleen toegang tot deze functionaliteit terwijl u werkt in de code weergave-editor. Als u deze functionaliteit wilt uitschakelen, geeft u `disableByteOrderMark` voor de `transformOptions` eigenschap op:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Meer informatie
* [Meer informatie over de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack")  
* [Meer informatie over Maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Meer informatie over Enter prise Integration Maps")  