---
title: Azure Stream Analytics Edge-taken in Visual Studio
description: In dit artikel wordt beschreven hoe u uw Stream Analytics op IoT Edge taken kunt schrijven, opsporen en maken met behulp van de Stream Analytics-hulpprogram ma's voor Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 09151ea0fe3d419401d576149f6655b8cdc09f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019955"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio Tools

In deze zelf studie leert u hoe u Stream Analytics-hulpprogram ma's voor Visual Studio kunt gebruiken. U leert hoe u uw Stream Analytics Edge-taken kunt schrijven, opsporen en maken. Nadat u de taak hebt gemaakt en getest, kunt u naar de Azure Portal gaan om deze te implementeren op uw apparaten. 

## <a name="prerequisites"></a>Vereisten

U hebt de volgende vereisten nodig om deze zelf studie te volt ooien:

* Installeer [Visual studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)of [Visual Studio 2013 update 4](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. De Express-editie wordt niet ondersteund.  

* Volg de [installatie-instructies](stream-analytics-tools-for-visual-studio-edge-jobs.md) om stream Analytics-hulpprogram Ma's voor Visual Studio te installeren.
 
## <a name="create-a-stream-analytics-edge-project"></a>Een Stream Analytics Edge-project maken 

Selecteer in Visual Studio **bestand**  >  **Nieuw**  >  **project**. Ga naar de lijst **sjablonen** aan de linkerkant > Vouw **Azure stream Analytics**  >  **Stream Analytics Edge**  >  **Azure stream Analytics Edge-toepassing** uit. Geef een naam, locatie en oplossings naam op voor uw project en selecteer **OK**.

![Nieuw Stream Analytics Edge-project in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Nadat het project is gemaakt, gaat u naar de **Solution Explorer** om de mappen hiërarchie weer te geven.

![De weer gave Solution Explorer van Stream Analytics Edge-taak](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Het juiste abonnement kiezen

1. Selecteer in het menu **beeld** van Visual Studio **Server Explorer**.  

2. Klik met de rechter muisknop op **Azure** > Selecteer **verbinding maken met Microsoft Azure abonnement** > en meld u vervolgens aan met uw Azure-account.

## <a name="define-inputs"></a>Invoer opgeven

1. Vouw in het **Solution Explorer** het knoop punt **invoer** uit om een invoer met de naam **EdgeInput.jsop** te geven. Dubbel klik om de instellingen weer te geven.  

2. Bron type instellen op **gegevens stroom**. Stel vervolgens bron-naar- **Edge-hub**, serialisatie-indeling voor gebeurtenissen in op **JSON** en code ring naar **utf8**. U kunt desgewenst de naam van de **invoer alias** wijzigen. laten we dit voor beeld laten staan. Als u de naam van de invoer alias wijzigt, gebruikt u de naam die u hebt opgegeven bij het definiëren van de query. Selecteer **Opslaan** om de instellingen op te slaan.  
   ![Configuratie van Stream Analytics taak invoer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Uitvoer definiëren

1. Vouw in het **Solution Explorer** het knoop punt **uitvoer** uit om een uitvoer met de naam **EdgeOutput.jsop** te geven. Dubbel klik om de instellingen weer te geven.  

2. Zorg ervoor dat sink is ingesteld op Select **Edge hub**, stel de indeling van de gebeurtenis serialisatie in op **JSON**, stel encoding in op **utf8** en stel format **array** in. U kunt desgewenst de naam van de **uitvoer alias** wijzigen. laten we dit voor beeld laten staan. Als u de naam van de uitvoer alias wijzigt, gebruikt u de naam die u hebt opgegeven bij het definiëren van de query. Selecteer **Opslaan** om de instellingen op te slaan. 
   ![Configuratie van Stream Analytics taak uitvoer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

Stream Analytics taken die in de Stream Analytics IoT Edge omgevingen zijn geïmplementeerd, ondersteunen de meeste [Stream Analytics query taal verwijzing](/stream-analytics-query/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). De volgende bewerkingen worden echter nog niet ondersteund voor Stream Analytics Edge-taken: 


|**Categorie**  | **Opdracht**  |
|---------|---------|
|Overige operatoren | <ul><li>PARTITIONEREN OP</li><li>TIME STAMP MET MEER DAN</li><li>JavaScript UDF</li><li>Door de gebruiker gedefinieerde aggregaties (UDA)</li><li>GetMetadataPropertyValue</li><li>Meer dan 14 statistische functies gebruiken in één stap</li></ul>   |

Wanneer u een Stream Analytics Edge-taak maakt in de portal, wordt u door de compiler automatisch gewaarschuwd als u geen ondersteunde operator gebruikt.

Definieer in uw Visual Studio de volgende transformatie query in de query-editor (**script. asaql-bestand**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>De taak lokaal testen

Als u de query lokaal wilt testen, moet u de voorbeeld gegevens uploaden. U kunt voorbeeld gegevens ophalen door registratie gegevens uit de [github-opslag plaats](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) te downloaden en op te slaan op uw lokale computer. 

1. Als u voorbeeld gegevens wilt uploaden, klikt u met de rechter muisknop op **EdgeInput.js** bestand en kiest u **lokale invoer toevoegen**  

2. In het pop-upvenster > **Bladeren door** de voorbeeld gegevens uit uw lokale pad > Selecteer **Opslaan**.
   ![Lokale invoer configuratie in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Een bestand met de naam **local_EdgeInput.jsop** wordt automatisch toegevoegd aan de map met invoer gegevens.  
4. U kunt de toepassing lokaal uitvoeren of naar Azure verzenden. Als u de query wilt testen, selecteert u **lokaal uitvoeren**.  
   ![Opties voor het uitvoeren van Stream Analytics taken in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. In het opdracht prompt venster wordt de status van de taak weer gegeven. Wanneer de taak wordt uitgevoerd, wordt er een map gemaakt met de naam ' 2018-02-23-11-31-42 ' in het pad naar de projectmap ' Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 '. Navigeer naar het mappad om de resultaten in de lokale map weer te geven:

   U kunt zich ook aanmelden bij de Azure Portal en controleren of de taak is gemaakt. 

   ![Map met Stream Analytics-taak resultaat](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>De taak naar Azure verzenden

1. Voordat u de taak naar Azure verzendt, moet u verbinding maken met uw Azure-abonnement. Open **Server Explorer** > Klik met de rechter muisknop op **Azure**  >  **Connect to Microsoft Azure Subscription** > Meld u aan bij uw Azure-abonnement.  

2. Als u de taak wilt verzenden naar Azure, gaat u naar de query-editor > selecteert **u verzenden naar Azure**.  

3. Er wordt een pop-upvenster geopend. Kies ervoor om een bestaande Stream Analytics Edge-taak bij te werken of een nieuwe te maken. Wanneer u een bestaande taak bijwerkt, wordt alle taak configuratie vervangen. in dit scenario publiceert u een nieuwe taak. Selecteer **een nieuwe Azure stream Analytics taak maken** > Voer een naam in voor uw taak, zoals **MyASAEdgeJob** > Kies het vereiste **abonnement**, de **resource groep** en de **locatie** > Selecteer **indienen**.

   ![Stream Analytics-taak verzenden naar Azure vanuit Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Uw Stream Analytics Edge-taak is nu gemaakt. U kunt de [zelf studie taken uitvoeren op IOT Edge](stream-analytics-edge.md) raadplegen voor informatie over hoe u deze kunt implementeren op uw apparaten. 

## <a name="manage-the-job"></a>De taak beheren 

U kunt de status van de taak en het taak diagram weer geven vanuit de Server Explorer. Vouw vanuit **Stream Analytics** in **Server Explorer** het abonnement en de resource groep uit waar u de taak stream Analytics Edge hebt geïmplementeerd. U kunt de MyASAEdgejob bekijken met de status **gemaakt**. Vouw uw taak knooppunt uit en dubbel klik erop om de taak weergave te openen.

![Opties voor taak beheer in Server Explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Het venster taak weergave bevat bewerkingen zoals het vernieuwen van de taak, het verwijderen van de taak en het openen van de taak vanuit Azure Portal.

![Taak diagram en andere opties in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Zelf studie voor IoT Edge van ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Feedback verzenden naar het team met behulp van deze enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)