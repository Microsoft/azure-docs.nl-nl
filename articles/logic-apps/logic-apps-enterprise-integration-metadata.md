---
title: Meta gegevens van integratie account artefacten beheren
description: Meta gegevens voor artefacten toevoegen of ophalen uit integratie accounts in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792467"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Metagegevens van artefacten in integratieaccounts beheren met Azure Logic Apps en Enterprise Integration Pack

U kunt aangepaste metagegevens voor artefacten definiëren in integratieaccounts en die metagegevens ophalen tijdens runtime voor de logische app die u wilt gebruiken. U kunt bijvoorbeeld meta gegevens opgeven voor artefacten, zoals partners, overeenkomsten, schema's en kaarten. alle meta gegevens worden opgeslagen met sleutel-waardeparen. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meld u dan aan voor een gratis Azure-account</a>.

* Een basis [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met de artefacten waaraan u meta gegevens wilt toevoegen, bijvoorbeeld: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Overeenkomst](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Kaart](logic-apps-enterprise-integration-maps.md)

* Een logische app die is gekoppeld aan het integratie account en artefact meta gegevens die u wilt gebruiken. Als uw logische app nog niet is gekoppeld, leert u [hoe u logische apps kunt koppelen aan integratie accounts](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Als u nog geen logische app hebt, leert u [hoe u logische apps kunt maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Voeg de trigger en acties toe die u wilt gebruiken voor het beheren van meta gegevens van artefacten. Als u alleen maar wilt proberen, voegt u een trigger zoals een **aanvraag** of **http** toe aan uw logische app.

## <a name="add-metadata-to-artifacts"></a>Meta gegevens toevoegen aan artefacten

1. Gebruik de referenties van uw Azure-account om u aan melden bij het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Zoek en open uw integratie account.

1. Selecteer het artefact waaraan u meta gegevens wilt toevoegen en kies **bewerken**. Voer de meta gegevens gegevens voor dat artefact in, bijvoorbeeld:

   ![Meta gegevens invoeren](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Als u klaar bent, kiest u **Done**.

1. Als u deze meta gegevens wilt weer geven in de definitie van de JavaScript Object Notation (JSON) voor het integratie account, kiest u **bewerken als JSON** zodat de JSON-editor wordt geopend: 

   ![JSON voor partner-meta gegevens](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Meta gegevens van artefact ophalen

1. Open in de Azure Portal de logische app die is gekoppeld aan het gewenste integratie account. 

1. Als u in de ontwerp functie voor logische apps de stap voor het ophalen van meta gegevens onder de trigger of de laatste actie in de werk stroom toevoegt, kiest u **nieuwe stap**  >  **een actie toevoegen**. 

1. Voer in het zoekvak ' integratie account ' in. Kies **Alle** onder het zoekvak. Selecteer in de lijst acties deze actie: **integratie account artefact opzoeken-integratie account**

   ![Selecteer integratie account artefact opzoeken](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Geef deze informatie op voor het artefact dat u wilt zoeken:

   | Eigenschap | Vereist | Waarde | Beschrijving | 
   |----------|---------|-------|-------------| 
   | **Type artefact** | Ja | **Schema**, **kaart**, **partner**, **overeenkomst** of aangepast type | Het type voor het gewenste artefact | 
   | **Artefact naam** | Ja | <*artefact naam*> | De naam voor het gewenste artefact | 
   ||| 

   Stel dat u de meta gegevens wilt ophalen voor het artefact van een handels partner:

   ![Type artefact selecteren en artefact naam invoeren](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Voeg de actie toe die u wilt voor het verwerken van de meta gegevens, bijvoorbeeld:

   1. Kies onder de **Zoek actie integratie account artefacten** **volgende stap** en selecteer **een actie toevoegen**. 

   1. Voer in het zoekvak ' http ' in. Kies in het zoekvak **ingebouwde modules** en selecteer deze actie: **http-http**

      ![HTTP-actie toevoegen](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Geef informatie op over de meta gegevens voor artefacten die u wilt beheren. 

      Stel bijvoorbeeld dat u de meta gegevens wilt ophalen `routingUrl` die eerder in dit onderwerp worden toegevoegd. Dit zijn de waarden van de eigenschap die u kunt opgeven: 

      | Eigenschap | Vereist | Waarde | Beschrijving | 
      |----------|----------|-------|-------------| 
      | **Methode** | Ja | <*bewerking-uitvoeren*> | De HTTP-bewerking die op het artefact moet worden uitgevoerd. Deze HTTP-actie maakt bijvoorbeeld gebruik van de **Get** -methode. | 
      | **URI** | Ja | <*meta gegevens-locatie*> | Voor toegang tot de `routingUrl` meta gegevens waarde uit het artefact dat u hebt opgehaald, kunt u een expressie gebruiken, bijvoorbeeld: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Kopteksten** | Nee | <*header-waarden*> | Eventuele header-uitvoer van de trigger die u wilt door geven aan de HTTP-actie. Als u bijvoorbeeld de eigenschaps waarde van de trigger wilt door geven `headers` : u kunt een expressie gebruiken, bijvoorbeeld: <p>`@triggeroutputs()['headers']` | 
      | **Hoofdtekst** | Nee | <*hoofd tekst: inhoud*> | Alle andere inhoud die u wilt door geven via de eigenschap van de HTTP-actie `body` . In dit voor beeld worden de waarden van het artefact door gegeven `properties` aan de HTTP-actie: <p>1. Klik in de eigenschap **Body** zodat de lijst met dynamische inhoud wordt weer gegeven. Als er geen eigenschappen worden weer gegeven, kiest u **meer weer geven**. <br>2. Selecteer in de lijst met dynamische inhoud onder **integratie account artefact opzoeken** de optie **Eigenschappen**. | 
      |||| 

      Bijvoorbeeld:

      ![Waarden en expressies opgeven voor HTTP-actie](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Als u de informatie die u hebt ingevoerd voor de HTTP-actie wilt controleren, bekijkt u de JSON-definitie van uw logische app. Kies op de werk balk van de Logic app-ontwerp functie de **code weergave** zodat de JSON-definitie van de app wordt weer gegeven, bijvoorbeeld:

      ![JSON-definitie van logische app](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Nadat u bent teruggeschakeld naar de ontwerp functie voor logische apps, worden alle expressies die u nu hebt gebruikt, omgezet zoals:

      ![Opgeloste expressies in Logic app Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over overeenkomsten](logic-apps-enterprise-integration-agreements.md)
