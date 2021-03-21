---
title: Kosten ramen met behulp van de capaciteits planner voor Azure Cosmos DB
description: Met de Azure Cosmos DB capacity planner kunt u een schatting maken van de door Voer (RU/s) die vereist zijn en de kosten voor uw werk belasting. In dit artikel wordt beschreven hoe u de nieuwe versie van de capaciteits planner gebruikt om de door Voer en de vereiste kosten te ramen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434998"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Een raming van RU/s met behulp van de Azure Cosmos DB capaciteit planner
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Het configureren van uw Azure Cosmos-data bases en containers met de juiste hoeveelheid ingerichte door Voer of [aanvraag eenheden (ru/s)](request-units.md)voor uw werk belasting is essentieel voor het optimaliseren van de kosten en prestaties. In dit artikel wordt beschreven hoe u de Azure Cosmos DB [capacity planner](https://cosmos.azure.com/capacitycalculator/) kunt gebruiken om een schatting te krijgen van de vereiste ru/s en de kosten van uw werk belasting. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>De door Voer en kosten schatten met Azure Cosmos DB capacity planner

De capaciteits planner kan in twee modi worden gebruikt.

|**Modus**  |**Beschrijving**  |
|---------|---------|
|Basic|Geeft een snelle en geschatte kosten van RU/s. In deze modus wordt uitgegaan van de standaard Azure Cosmos DB instellingen voor het indexerings beleid, de consistentie en andere para meters. <br/><br/>Gebruik de basis modus voor een snelle schatting op hoog niveau wanneer u een mogelijke werk belasting evalueert om op Azure Cosmos DB uit te voeren.|
|Geavanceerd|Biedt een gedetailleerdere versie van RU/s en kosten, met de mogelijkheid om extra instellingen af te stemmen: indexerings beleid, consistentie niveau en andere para meters die van invloed zijn op de kosten en door voer. <br/><br/>Gebruik de geavanceerde modus wanneer u een schatting van RU/s voor een nieuw project hebt of een gedetailleerdere schatting wilt maken. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Ingerichte door Voer en kosten ramen met de basis modus
Als u een snelle schatting wilt maken voor uw werk belasting met behulp van de basis modus, gaat u naar de [capaciteits planner](https://cosmos.azure.com/capacitycalculator/). Voer de volgende para meters in op basis van uw workload: 

|**Invoer**  |**Beschrijving**  |
|---------|---------|
|Aantal regio's|Azure Cosmos DB is beschikbaar in alle Azure-regio's. Selecteer het aantal regio's dat vereist is voor uw werk belasting. U kunt elk gewenst aantal regio's koppelen aan uw Cosmos-account. Zie [globale distributie](distribute-data-globally.md) in azure Cosmos DB voor meer informatie.|
|Schrijfbewerkingen in meerdere regio's|Als u [schrijf bewerkingen met meerdere regio's](distribute-data-globally.md#key-benefits-of-global-distribution)inschakelt, kan uw toepassing lezen en schrijven naar elke Azure-regio. Als u het schrijven van meerdere regio's uitschakelt, kan uw toepassing gegevens naar één regio schrijven. <br/><br/> Schakel schrijf bewerkingen in meerdere regio's in als u verwacht dat u een actieve werk belasting hebt waarbij schrijf bewerkingen met een lage latentie in verschillende regio's vereist zijn. Bijvoorbeeld een IOT-workload waarmee gegevens naar de Data Base worden geschreven op grote volumes in verschillende regio's. <br/><br/> Met meerdere regio's schrijft u gegarandeerd 99,999% Beschik baarheid voor lezen en schrijven. Voor schrijf bewerkingen met meerdere regio's is meer door Voer vereist in vergelijking met de enkele schrijf regio's. Zie [How RUs verschilt voor een artikel met enkele en meerdere schrijf bewerkingen voor](optimize-cost-regions.md) meer informatie.|
|Totaal aantal opgeslagen gegevens (per regio)|Het totale aantal geschatte gegevens dat is opgeslagen in GB in één regio.|
|Itemgrootte|De geschatte grootte van het gegevens item (bijvoorbeeld document), variërend van 1 KB tot 2 MB. |
|Lees bewerkingen per regio|Aantal verwachte Lees bewerkingen per seconde. |
|Schrijf bewerkingen/sec per regio|Aantal schrijf bewerkingen dat wordt verwacht per seconde. |

Nadat u de vereiste gegevens hebt ingevuld, selecteert u **berekenen**. Op het tabblad **kosten raming** worden de totale kosten voor opslag en ingerichte door Voer weer gegeven. U kunt de koppeling **Details weer geven** op dit tabblad uitsplitsing van de door Voer die is vereist voor lees-en schrijf aanvragen. Telkens wanneer u de waarde van een veld wijzigt, selecteert u **berekenen** om de geschatte kosten opnieuw te berekenen. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Basis modus voor capaciteits planner":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Ingerichte door Voer en kosten ramen met geavanceerde modus

In de geavanceerde modus kunt u meer instellingen opgeven die van invloed zijn op de RU/s-schatting. Als u deze optie wilt gebruiken, navigeert u naar de [capaciteits planner](https://cosmos.azure.com/capacitycalculator/) en meldt u zich aan bij het hulp programma met een account dat u gebruikt voor Azure. De aanmeldings optie is beschikbaar in de rechter bovenhoek. 

Nadat u zich hebt aangemeld, ziet u aanvullende velden vergeleken met de velden in de standaard modus. Voer de aanvullende para meters in op basis van uw workload. 

|**Invoer**  |**Beschrijving**  |
|---------|---------|
|API|Azure Cosmos DB is een multi-model en een multi-API-service. Voor nieuwe workloads selecteert u de SQL-API (core). |
|Aantal regio's|Azure Cosmos DB is beschikbaar in alle Azure-regio's. Selecteer het aantal regio's dat vereist is voor uw werk belasting. U kunt elk gewenst aantal regio's koppelen aan uw Cosmos-account. Zie [globale distributie](distribute-data-globally.md) in azure Cosmos DB voor meer informatie.|
|Schrijfbewerkingen in meerdere regio's|Als u [schrijf bewerkingen met meerdere regio's](distribute-data-globally.md#key-benefits-of-global-distribution)inschakelt, kan uw toepassing lezen en schrijven naar elke Azure-regio. Als u het schrijven van meerdere regio's uitschakelt, kan uw toepassing gegevens naar één regio schrijven. <br/><br/> Schakel schrijf bewerkingen in meerdere regio's in als u verwacht dat u een actieve werk belasting hebt waarbij schrijf bewerkingen met een lage latentie in verschillende regio's vereist zijn. Bijvoorbeeld een IOT-workload waarmee gegevens naar de Data Base worden geschreven op grote volumes in verschillende regio's. <br/><br/> Met meerdere regio's schrijft u gegarandeerd 99,999% Beschik baarheid voor lezen en schrijven. Voor schrijf bewerkingen met meerdere regio's is meer door Voer vereist in vergelijking met de enkele schrijf regio's. Zie [How RUs verschilt voor een artikel met enkele en meerdere schrijf bewerkingen voor](optimize-cost-regions.md) meer informatie.|
|Standaard consistentie|Azure Cosmos DB ondersteunt vijf consistentie niveaus om ontwikkel aars in staat te stellen de verhouding tussen consistentie, Beschik baarheid en latentie-afwegingen te verdelen. Zie het artikel [consistentie niveaus](consistency-levels.md) voor meer informatie. <br/><br/> Azure Cosmos DB maakt standaard gebruik van sessie consistentie, waardoor u de mogelijkheid biedt om uw eigen schrijf bewerkingen in een sessie te lezen. <br/><br/> Als u een sterke of gebonden verouderd wilt kiezen, moeten dubbele de vereiste RU/s voor lees bewerkingen worden uitgevoerd, vergeleken met sessie, consistent voor voegsel en uiteindelijke consistentie. Sterke consistentie met meerdere regio's schrijven wordt niet ondersteund en wordt automatisch standaard ingesteld op enkelvoudige schrijf bewerkingen met sterke consistentie. |
|Indexeringsbeleid|Azure Cosmos DB [indexeert standaard alle eigenschappen](index-policy.md) in alle items voor flexibele en efficiënte query's (wijst toe aan het beleid voor **automatische** indexering). <br/><br/> Als u op **uit** klikt, worden geen van de eigenschappen geïndexeerd. Dit resulteert in de laagste RU-kosten voor schrijf bewerkingen. Selecteer beleid **uitschakelen** als u verwacht dat alleen [Lees bewerkingen](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (sleutel waarde opzoeken) en/of schrijf bewerkingen, en geen query's. <br/><br/> Met het aangepaste indexerings beleid kunt u specifieke eigenschappen uit de index opnemen of uitsluiten voor een lagere schrijf doorvoer en opslag. Zie het [Indexing-beleid](index-overview.md) en de voor [beelden van beleids regels](how-to-manage-indexing-policy.md#indexing-policy-examples) voor het indexeren van beleid voor meer informatie.|
|Totaal aantal opgeslagen gegevens (per regio)|Het totale aantal geschatte gegevens dat is opgeslagen in GB in één regio.|
|Werkbelasting modus|Selecteer **constant** als uw workload-volume een constante is. <br/><br/> Selecteer **variabele** als uw werkbelasting volume na verloop van tijd verandert.  Bijvoorbeeld gedurende een specifieke dag of maand. <br/><br/> De volgende instellingen zijn beschikbaar als u de optie variabele workload kiest:<ul><li>Percentage tijd bij piek: percentage van de tijd in een maand waarin de werk belasting piek (hoogste) door Voer vereist. <br/><br/> Als u bijvoorbeeld een werk belasting hebt met een hoge activiteit tijdens de 9:00-18:00 uur voor werk dagen, is het percentage van de tijd bij piek: 45 uur bij piek/730 uur/maand = ~ 6%.<br/><br/></li><li>Lees bewerkingen per seconde op basis van piek waarde-aantal lees bewerkingen verwacht per seconde bij piek.</li><li>Schrijf bewerkingen/sec per regio op piek: aantal schrijf bewerkingen verwacht per seconde bij piek.</li><li>Lees bewerkingen per seconde voor minder piek: aantal lees bewerkingen dat per seconde wordt verwacht tijdens de piek.</li><li>Schrijf bewerkingen per seconde op piek waarde-aantal schrijf bewerkingen die per seconde worden verwacht tijdens de piek.</li></ul>Met piek-en rustige intervallen kunt u uw kosten optimaliseren door [uw ingerichte door Voer op een programmatische manier](set-throughput.md#update-throughput-on-a-database-or-a-container) omhoog en omlaag te schalen.|
|Itemgrootte|De grootte van het gegevens item (bijvoorbeeld document), variërend van 1 KB tot 2 MB. <br/><br/>U kunt ook een voor beeld van een **JSON-document uploaden** voor een nauw keuriger schatting.<br/><br/>Als uw werk belasting meerdere typen items bevat (met verschillende JSON-inhoud) in dezelfde container, kunt u meerdere JSON-documenten uploaden en de schatting ophalen. Gebruik de knop **Nieuw item toevoegen** om meerdere voor beeld-JSON-documenten toe te voegen.|

U kunt ook de knop **schatting opslaan** gebruiken om een CSV-bestand met de huidige schatting te downloaden. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Geavanceerde modus voor capaciteits planner":::

De prijzen die in de Azure Cosmos DB capaciteits planner worden weer gegeven, zijn schattingen op basis van de open bare prijs tarieven voor door Voer en opslag. Alle prijzen worden weer gegeven in Amerikaanse dollars. Raadpleeg de [pagina met prijzen voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) om alle tarieven per regio weer te geven.  

## <a name="estimating-throughput-for-queries"></a>De door Voer voor query's schatten

De Azure Cosmos capacity-reken machine gaat ervan uitgaan dat punt Lees bewerkingen (een lees bewerking van één item, bijvoorbeeld document, ID en partitie sleutel waarde) en schrijft voor de werk belasting. Als u een schatting wilt maken van de door Voer die nodig is voor query's, voert u uw query uit op een representatieve gegevensset in een Cosmos-container en [haalt u de](find-request-unit-charge.md)koppelings kosten op Vermenigvuldig de RU-kosten op basis van het aantal query's dat per seconde moet worden uitgevoerd om het totale aantal RU/s te verkrijgen. 

Als voor uw werk belasting bijvoorbeeld een query is vereist ``SELECT * FROM c WHERE c.id = 'Alice'`` die 100 keer per seconde wordt uitgevoerd en de ru-kosten van de query is 10 RUs, hebt u 100 query per seconde * 10 ru/query = 1000 ru/s in totaal nodig om deze aanvragen te kunnen verwerken. Voeg deze RU/s toe aan de RU/s die zijn vereist voor lees-of schrijf bewerkingen in uw werk belasting.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het prijs model van Azure Cosmos DB](how-pricing-works.md).
* Maak een nieuw [Cosmos-account, een nieuwe data base en een container](create-cosmosdb-resources-portal.md).
* Meer informatie over het [optimaliseren van ingerichte doorvoer kosten](optimize-cost-throughput.md).
* Meer informatie over het [optimaliseren van kosten met gereserveerde capaciteit](cosmos-db-reserved-capacity.md).