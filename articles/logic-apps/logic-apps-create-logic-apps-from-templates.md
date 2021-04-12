---
title: Sneller werk stromen voor logische apps maken met behulp van vooraf ontwikkelde sjablonen
description: Snel werk stromen voor logische apps bouwen met behulp van vooraf gemaakte sjablonen van Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593096"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Werkstromen voor logische apps maken met vooraf gedefinieerde sjablonen

Logic Apps biedt sjablonen die preconstrueerde Logic apps hebben die de veelgebruikte patronen volgen, zodat u sneller aan de slag kunt gaan met het maken van werk stromen. Gebruik deze sjablonen zoals deze zijn verschaft of bewerk ze voor uw scenario.

Hier volgen enkele sjabloon categorieën:

| Sjabloon type | Description | 
| ------------- | ----------- | 
| Enter prise Cloud-sjablonen | Voor de integratie van Azure Blob, Dynamics CRM, Sales Force, box en ook andere connectors voor uw Enter prise Cloud-behoeften. U kunt deze sjablonen bijvoorbeeld gebruiken om zakelijke leads te organiseren of een back-up te maken van uw bedrijfs bestands gegevens. | 
| Persoonlijke productiviteits sjablonen | Verbeter de persoonlijke productiviteit door dagelijks herinneringen in te stellen, belang rijke werk items in te scha kelen in taken lijsten en langdurige taken uit te voeren naar één stap voor het goed keuren van een gebruiker. | 
| Cloud sjablonen voor consumenten | Voor de integratie van sociale media-services zoals Twitter, toegestane vertraging en e-mail. Handig voor het versterken van marketing initiatieven voor sociale media. Deze sjablonen omvatten ook taken als het kopiëren van de Cloud, waardoor de productiviteit wordt verhoogd door tijd te besparen op traditionele, terugkerende taken. | 
| Enter prise Integration Pack-sjablonen | Voor het configureren van VETER (valideren, uitpakken, transformeren, verrijken, route) pijp lijnen, het ontvangen van een X12 EDI-document via AS2 en transformeren naar XML, en het verwerken van X12, EDIFACT en AS2 berichten. | 
| Protocol patroon Sjablonen | Voor het implementeren van protocol patronen zoals aanvraag-Response via HTTP en integraties in FTP en SFTP. Gebruik deze sjablonen zoals u dat hebt gedaan, of bouw ze op voor complexe protocol patronen. | 
||| 

Als u nog geen abonnement op Azure hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint. Zie [een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het bouwen van een logische app.

## <a name="create-logic-apps-from-templates"></a>Logische apps maken met sjablonen

1. Als u dat nog niet hebt gedaan, meldt u zich aan bij de [Azure Portal](https://portal.azure.com "Azure Portal").

2. Kies in het hoofd menu van Azure **een resource maken**  >  **bedrijfsintegratie**  >  **logische app**.

   ![Azure Portal, Nieuw, Enterprise Integration, Logische app](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Maak de logische app aan de hand van de instellingen die in de tabel onder deze afbeelding staan opgegeven:

   ![Gegevens van logische app opgeven](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Instelling | Waarde | Beschrijving | 
   | ------- | ----- | ----------- | 
   | **Naam** | *naam-van-uw-logische-app* | Geef een unieke naam voor de logische app op. | 
   | **Abonnement** | *your-Azure-subscription-name* | Selecteer het Azure-abonnement dat u wilt gebruiken. | 
   | **Resourcegroep** | *naam-van-uw-Azure-resourcegroep* | Maak of selecteer een [Azure-resource groep](../azure-resource-manager/management/overview.md) voor deze logische app en beheer alle resources die zijn gekoppeld aan deze app. | 
   | **Locatie** | *uw-Azure-datacenter-regio* | Selecteer de regio van het datacenter voor het implementeren van uw logische app, bijvoorbeeld VS - west. | 
   | **Log Analytics** | **Uit** (standaard) of **op** | Stel [Diagnostische logboek registratie](../logic-apps/monitor-logic-apps-log-analytics.md) in voor uw logische app met behulp van [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md). Hiervoor hebt u al een Log Analytics-werk ruimte nodig. | 
   |||| 

4. Wanneer u klaar bent, selecteert u **Vastmaken aan dashboard**. Op die manier wordt uw logische app automatisch weergegeven op uw Azure-dashboard en wordt geopend na de implementatie. Kies **Maken**.

   > [!NOTE]
   > Als u de logische app niet wilt vastmaken, moet u deze handmatig zoeken en openen na de implementatie, zodat u kunt doorgaan.

   Nadat Azure uw logische app heeft geïmplementeerd, wordt de ontwerper voor logische apps geopend en ziet u een pagina met een inleidende video. 
   Onder de video ziet u sjablonen voor veelvoorkomende patronen voor logische apps. 

5. Schuif voorbij de inleidings video en veelgebruikte triggers naar **sjablonen**. Kies een vooraf gemaakte sjabloon. Bijvoorbeeld:

   ![Een sjabloon voor een logische app kiezen](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Kies **lege logische app** om uw logische app helemaal zelf te maken.

   Wanneer u een vooraf gemaakte sjabloon selecteert, kunt u meer informatie over die sjabloon weer geven. 
   Bijvoorbeeld:

   ![Een vooraf gemaakte sjabloon kiezen](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Kies **deze sjabloon gebruiken** om door te gaan met de geselecteerde sjabloon. 

7. Op basis van de connectors in de sjabloon wordt u gevraagd om een van de volgende stappen uit te voeren:

   * Meld u aan met uw referenties voor systemen of services waarnaar wordt verwezen door de sjabloon.

   * Maak verbindingen voor services of systemen waarnaar wordt verwezen door de sjabloon. Als u een verbinding wilt maken, geeft u een naam op voor de verbinding en selecteert u, indien nodig, de resource die u wilt gebruiken. 

   * Als u deze verbindingen al hebt ingesteld, kiest u **door gaan**.

   Bijvoorbeeld:

   ![Verbindingen maken](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Wanneer u klaar bent, wordt uw logische App geopend en weer gegeven in de ontwerp functie voor Logic Apps.

   > [!TIP]
   > Als u wilt terugkeren naar de sjabloon viewer, kiest u **sjablonen** op de werk balk van de ontwerp functie. Met deze actie worden niet-opgeslagen wijzigingen verwijderd. er wordt dus een waarschuwings bericht weer gegeven om uw aanvraag te bevestigen.

8. Ga door met het bouwen van uw logische app.

   > [!NOTE] 
   > Veel sjablonen bevatten connectors die mogelijk al vooraf ingevuld vereiste eigenschappen hebben. Voor sommige sjablonen is het echter mogelijk dat u nog steeds waarden moet opgeven voordat u de logische app correct kunt implementeren. Als u probeert te implementeren zonder de ontbrekende eigenschaps velden te volt ooien, wordt er een fout bericht weer gegeven. 

## <a name="update-logic-apps-with-templates&quot;></a>Logische apps bijwerken met sjablonen

1. Zoek en open in de [Azure Portal](https://portal.azure.com &quot;Azure Portal")uw logische app in de ontwerp functie voor logische apps.

2. Kies **sjablonen** op de werk balk van de ontwerp functie. Met deze actie worden niet-opgeslagen wijzigingen verwijderd. er wordt dus een waarschuwing weer gegeven, zodat u kunt bevestigen dat u wilt door gaan. Klik op **OK** om te bevestigen. Bijvoorbeeld:

   ![Kies sjablonen](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Schuif voorbij de inleidings video en veelgebruikte triggers naar **sjablonen**. Kies een vooraf gemaakte sjabloon. Bijvoorbeeld:

   ![Een sjabloon voor een logische app kiezen](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Wanneer u een vooraf gemaakte sjabloon selecteert, kunt u meer informatie over die sjabloon weer geven. 
   Bijvoorbeeld:

   ![Een vooraf gemaakte sjabloon kiezen](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Kies **deze sjabloon gebruiken** om door te gaan met de geselecteerde sjabloon. 

5. Op basis van de connectors in de sjabloon wordt u gevraagd om een van de volgende stappen uit te voeren:

   * Meld u aan met uw referenties voor systemen of services waarnaar wordt verwezen door de sjabloon.

   * Maak verbindingen voor services of systemen waarnaar wordt verwezen door de sjabloon. Als u een verbinding wilt maken, geeft u een naam op voor de verbinding en selecteert u, indien nodig, de resource die u wilt gebruiken. 

   * Als u deze verbindingen al hebt ingesteld, kiest u **door gaan**.

   ![Verbindingen maken](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Uw logische app wordt nu geopend en wordt weer gegeven in de ontwerp functie voor Logic Apps.

8. Ga door met het bouwen van uw logische app. 

   > [!TIP]
   > Als u uw wijzigingen nog niet hebt opgeslagen, kunt u uw werk verwijderen en terugkeren naar uw vorige logische app. Kies **verwijderen** op de werk balk van de ontwerp functie.

> [!NOTE] 
> Veel sjablonen bevatten connectors waarvoor mogelijk al vooraf de vereiste eigenschappen zijn ingevuld. Voor sommige sjablonen is het echter mogelijk dat u nog steeds waarden moet opgeven voordat u de logische app correct kunt implementeren. Als u probeert te implementeren zonder de ontbrekende eigenschaps velden te volt ooien, wordt er een fout bericht weer gegeven.

## <a name="deploy-logic-apps-built-from-templates"></a>Logic Apps implementeren die zijn gemaakt op basis van sjablonen

Nadat u de wijzigingen in de sjabloon hebt aangebracht, kunt u de wijzigingen opslaan. Deze actie publiceert ook automatisch uw logische app.

Kies **Opslaan** op de werkbalk van de ontwerper.

![Uw logische app opslaan en publiceren](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van logische apps met voor beelden, scenario's, ervaringen van klanten en instructies.

> [!div class="nextstepaction"]
> [Voor beelden, scenario's en instructies voor logische apps bekijken](../logic-apps/logic-apps-examples-and-scenarios.md)
