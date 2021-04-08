---
title: Apps migreren van BizTalk Services naar Azure Logic Apps
description: Uw apps en oplossingen van Microsoft Azure BizTalk Services (MABS) verplaatsen naar Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023649"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migreer uw apps en oplossingen van BizTalk Services naar Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) wordt buiten gebruik gesteld. Volg de richt lijnen in dit artikel om uw integratie oplossingen voor MABS naar [Azure Logic apps](../logic-apps/logic-apps-overview.md)te verplaatsen. 

## <a name="introduction"></a>Introductie

BizTalk Services bestaat uit twee subservices:

* Micro soft BizTalk Services Hybride verbindingen
* Integratie van EAI en EDI-brug

[Azure App Service hybride verbindingen](../app-service/app-service-hybrid-connections.md) vervangt BizTalk Services hybride verbindingen. Azure Hybride verbindingen is beschikbaar met Azure App Service via de Azure Portal. Deze service biedt een Hybrid Connection Manager, zodat u bestaande BizTalk Services hybride verbindingen en ook de nieuwe hybride verbindingen die u in de portal maakt kunt beheren. 

[Logic apps](../logic-apps/logic-apps-overview.md) vervangt EAI-en EDI-integratie op basis van een brug met dezelfde mogelijkheden in BizTalk Services en meer. Deze service biedt werk stroom-en indelings functies op basis van de Cloud schaal voor u om snel en eenvoudig complexe integratie oplossingen te bouwen via een browser of met Visual Studio.

Deze tabel wijst BizTalk Services mogelijkheden toe aan Logic Apps.

| BizTalk Services   | Logic Apps            | Doel                      |
| ------------------ | --------------------- | ---------------------------- |
| Connector          | Connector             | Gegevens verzenden en ontvangen   |
| Bridge             | Logische apps             | Pijplijn processor           |
| Fase valideren     | XML-validatie actie | Een XML-document valideren op basis van een schema | 
| Verrijk fase       | Gegevens tokens           | Eigenschappen promo veren naar berichten of beslissingen voor route ring |
| Transformatie fase    | Trans formatie actie      | XML-berichten van de ene indeling naar de andere converteren |
| Decoderings fase       | Decoderings actie voor plat bestand | Converteren van plat bestand naar XML |
| Coderings fase       | Actie voor platte bestands codering | Converteren van XML naar plat bestand |
| Berichten controle  | Azure Functions of API Apps | Aangepaste code uitvoeren in uw integraties |
| Route actie       | Voor waarde of switch | Berichten routeren naar een van de opgegeven connectors |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefacten

BizTalk Services heeft verschillende soorten artefacten. 

## <a name="connectors"></a>Connectors

Met BizTalk Services Connectors kunnen gegevens worden verzonden en ontvangen, waaronder twee richtings bruggen die op HTTP gebaseerde aanvraag/antwoord interacties mogelijk maken. Logic Apps gebruikt dezelfde terminologie en heeft honderden connectors die hetzelfde doel hebben door verbinding te maken met een breed scala aan technologieën en services. Connectors zijn bijvoorbeeld beschikbaar voor Cloud SaaS-en PaaS-Services, zoals OneDrive, Office365, Dynamics CRM en meer, plus on-premises systemen via de on-premises gegevens gateway, waarmee de BizTalk adapter service voor BizTalk Services wordt vervangen. Bronnen in BizTalk Services zijn beperkt tot de FTP-, SFTP-en Service Bus-wachtrij of het onderwerp-abonnement.

![Diagram waarin de BizTalk Services stroom wordt weer gegeven.](media/logic-apps-move-from-mabs/sources.png)

Elke Bridge heeft standaard een HTTP-eind punt dat is geconfigureerd met het runtime-adres en de relatieve adres eigenschappen voor de brug. Gebruik de acties voor [aanvragen en antwoorden](../connectors/connectors-native-reqres.md) om dezelfde resultaten te krijgen met Logic apps.

## <a name="xml-processing-and-bridges"></a>XML-verwerking en bruggen

In BizTalk Services is een brug gelijk aan een verwerkings pijplijn. Een Bridge kan gegevens ontvangen van een connector, sommige werk met de gegevens uitvoeren en de resultaten naar een ander systeem verzenden. Logic Apps doet hetzelfde door het ondersteunen van dezelfde op pijplijn gebaseerde interactie patronen als BizTalk Services en ook andere integratie patronen bieden. De [XML-Request-Reply Bridge](/previous-versions/azure/hh689781(v=azure.100)) in BizTalk Services wordt een VETER-pijp lijn genoemd, die bestaat uit fasen die deze taken uitvoeren:

* (V) valideren
* (E) verrijking
* (T) trans formatie
* (E) verrijking
* (R) route

In deze afbeelding ziet u hoe de verwerking wordt gesplitst tussen de aanvraag en het antwoord, waarmee u de aanvraag en de antwoord paden afzonderlijk kunt controleren, bijvoorbeeld door gebruik te maken van verschillende toewijzingen voor elk pad:

![Scherm afbeelding die laat zien hoe de verwerking van de aanvraag en het antwoord wordt gesplitst.](media/logic-apps-move-from-mabs/xml-request-reply.png)

Daarnaast voegt een XML-One-Way Bridge decodeer-en versleutelings fasen toe aan het begin en het einde van de verwerking. De Pass-Through Bridge bevat een enkele verrijkte fase.

### <a name="message-processing-decoding-and-encoding"></a>Bericht verwerking, decoderen en coderen

In BizTalk Services kunt u verschillende typen XML-berichten ontvangen en het overeenkomende schema voor het ontvangen bericht bepalen. Dit werk wordt uitgevoerd in de fase *bericht typen* van de pipeline voor de verwerking van ontvangen. In de dedecodeer fase wordt vervolgens het gedetecteerde bericht type gebruikt om het bericht te decoderen met behulp van het meegeleverde schema. Als het schema een plat bestands schema is, converteert deze fase het binnenkomende platte bestand naar XML. 

Logic Apps biedt vergelijk bare mogelijkheden. U ontvangt een plat bestand via verschillende protocollen met behulp van de verschillende connector triggers (bestands systeem, FTP, HTTP, enzovoort) en u gebruikt de actie voor het [decoderen van platte bestanden](../logic-apps/logic-apps-enterprise-integration-flatfile.md) om de binnenkomende gegevens te converteren naar XML. U kunt uw bestaande platte bestands schema's rechtstreeks verplaatsen naar Logic Apps zonder wijzigingen, en vervolgens schema's uploaden naar uw integratie account.

### <a name="validation"></a>Validatie

Nadat de binnenkomende gegevens zijn geconverteerd naar XML (of als XML de bericht indeling is ontvangen), wordt validatie uitgevoerd om te bepalen of het bericht voldoet aan uw XSD-schema. Als u deze taak in Logic Apps wilt uitvoeren, gebruikt u de [validatie actie XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) . U kunt dezelfde schema's van BizTalk Services gebruiken zonder dat u wijzigingen hoeft aan te brengen.

### <a name="transform-messages"></a>Berichten transformeren

In BizTalk Services converteert het transformatie stadium één op XML gebaseerde bericht indeling naar een andere. Dit werk wordt uitgevoerd door een kaart toe te passen met behulp van de toewijzing op basis van TRFM. In Logic Apps is het proces vergelijkbaar. Met de actie trans formatie wordt een kaart uitgevoerd vanuit uw integratie account. Het belangrijkste verschil is dat Maps in Logic Apps een XSLT-indeling hebben. XSLT bevat de mogelijkheid om bestaande XSLT te gebruiken die u al hebt, inclusief kaarten die zijn gemaakt voor BizTalk Server die functoids bevatten. 

### <a name="routing-rules"></a>Regels voor doorsturen

BizTalk Services maakt een routerings besluit waarop een eind punt of connector inkomende berichten of gegevens verzendt. De mogelijkheid om te kiezen uit vooraf geconfigureerde eind punten is mogelijk met behulp van de routerings filter optie:

![Scherm afbeelding met de optie routerings filter.](media/logic-apps-move-from-mabs/route-filter.png)

Als er in BizTalk Services slechts twee opties zijn, is het gebruik van een *voor waarde* de beste manier om routerings filters in BizTalk services te converteren. Als er meer dan twee zijn, gebruikt u vervolgens een **Switch**.

Logic Apps biedt geavanceerde logica mogelijkheden plus geavanceerde controle stroom en route ring met [voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md) en [Switch instructies](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Verrijken

Bij BizTalk Services verwerking voegt het verrijkte stadium eigenschappen toe aan de bericht context die is gekoppeld aan de ontvangen gegevens. U kunt bijvoorbeeld een eigenschap promoten die moet worden gebruikt voor route ring vanuit een zoek opdracht in de data base of door een waarde te extra heren met behulp van een XPath-expressie. Logic Apps biedt toegang tot alle contextuele gegevens uitvoer van de voor gaande acties, waardoor het eenvoudig is om hetzelfde gedrag te repliceren. `Get Row`Als u bijvoorbeeld de actie SQL-verbinding gebruikt, haalt u gegevens op uit een SQL Server-Data Base en gebruikt u de gegevens in een beslissings actie voor route ring. Daarnaast zijn eigenschappen van binnenkomende Service Bus berichten in de wachtrij door een trigger adresseerbaar, evenals XPath met behulp van de XPath-expressie voor de definitie taal van de werk stroom.

### <a name="run-custom-code"></a>Aangepaste code uitvoeren

Met BizTalk Services kunt u [aangepaste code uitvoeren](/previous-versions/azure/dn232389(v=azure.100)) die is geüpload in uw eigen assembly's. Deze functionaliteit wordt geïmplementeerd door de [IMessageInspector]() -interface. Elke fase in de Bridge bevat twee eigenschappen (op ENTER en op de Exitcontrole functie) die het .NET-type opgeven dat u hebt gemaakt en die deze interface implementeert. Met aangepaste code kunt u complexere verwerking uitvoeren op gegevens en kunt u bestaande code opnieuw gebruiken in assembly's die algemene bedrijfs logica uitvoeren. 

Logic Apps biedt twee primaire manieren om aangepaste code uit te voeren: Azure Functions en API Apps. Azure Functions kunnen worden gemaakt en worden aangeroepen vanuit Logic apps. Zie [aangepaste code toevoegen en uitvoeren voor logische apps via Azure functions](../logic-apps/logic-apps-azure-functions.md). Gebruik API Apps, onderdeel van Azure App Service, om uw eigen triggers en acties te maken. Meer informatie over het [maken van een aangepaste API voor het gebruik van Logic apps](../logic-apps/logic-apps-create-api-app.md). 

Als u aangepaste code hebt in de assembly's die u aanroept vanuit BizTalk Services, kunt u deze code naar Azure Functions verplaatsen of aangepaste Api's met API Apps maken, afhankelijk van wat u implementeert. Als u bijvoorbeeld code hebt die een andere service verpakt waarvoor Logic Apps geen connector heeft, maakt u vervolgens een API-app en gebruikt u de acties die uw API-app in uw logische app biedt. Als u hulp functies of bibliotheken hebt, is Azure Functions waarschijnlijk het beste.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-verwerking en beheer van handels partners

BizTalk Services en Logic Apps zijn voorzien van EDI en B2B-verwerking met ondersteuning voor AS2 (Applicability Statement 2), X12 en EDIFACT. In BizTalk Services maakt u EDI-bruggen en maakt of beheert u handels partners en-overeenkomsten in de speciale tracking-en beheer Portal.
In Logic Apps krijgt u deze functionaliteit via de [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). De EIP biedt [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en B2B-acties voor EDI en B2B-verwerking. U kunt ook een integratie account gebruiken om [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) en- [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md)te maken en te beheren. Nadat u een integratie account hebt gemaakt, kunt u een of meer Logic apps aan het account koppelen. U kunt vervolgens B2B-acties gebruiken om toegang te krijgen tot de gegevens van handels partners vanuit uw logische app. De volgende acties zijn aanwezig:

* AS2-code ring
* AS2 decoderen
* X12-code ring
* X12 decoderen
* EDIFACT-code ring
* EDIFACT decoderen

In tegens telling tot BizTalk Services worden deze acties losgekoppeld van de transport protocollen. Wanneer u logische apps maakt, hebt u dus meer flexibiliteit in welke connectors u gebruikt om gegevens te verzenden en te ontvangen. U kunt bijvoorbeeld X12-bestanden ontvangen als bijlagen vanuit e-mail en vervolgens deze bestanden verwerken in een logische app. 

## <a name="manage-and-monitor"></a>Beheren en bewaken

In BizTalk Services biedt een speciale portal Traceer mogelijkheden om problemen te bewaken en op te lossen. Logic Apps biedt uitgebreide tracking-en bewakings mogelijkheden voor [het bewaken van logische apps in de Azure Portal](../logic-apps/monitor-logic-apps.md)en bevat een mobiele app waarmee u op dingen kunt blijven wanneer u onderweg bent.

## <a name="high-availability"></a>Hoge beschikbaarheid

Voor hoge Beschik baarheid (HA) in BizTalk Services kunt u de verwerkings belasting delen door meer dan één instantie in een specifieke regio te gebruiken. Logic Apps biedt een regionale HA zonder extra kosten. 

In BizTalk Services is een back-up-en herstel proces vereist voor het herstel na nood gevallen voor B2B-verwerking. Voor bedrijfs continuïteit biedt Logic Apps multiregionale actieve/passieve [Dr-functionaliteit](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), waarmee u B2B-gegevens kunt synchroniseren tussen integratie accounts in verschillende regio's.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md), of snel aan de slag met behulp van een [vooraf gemaakte sjabloon](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Bekijk alle beschik bare connectors](../connectors/apis-list.md) die u kunt gebruiken in Logic apps