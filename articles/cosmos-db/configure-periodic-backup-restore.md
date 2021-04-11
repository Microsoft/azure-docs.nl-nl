---
title: Azure Cosmos DB account configureren met periodieke back-up
description: In dit artikel wordt beschreven hoe u Azure Cosmos DB accounts configureert met periodieke back-ups met een back-upinterval. en bewaren. Neem ook contact op met de ondersteuning om uw gegevens te herstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565827"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Azure Cosmos DB account configureren met periodieke back-up
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB maakt op regelmatige tijdstippen automatisch back-ups van uw gegevens. De automatische back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of beschikbaarheid van de databasebewerkingen. Alle back-ups worden afzonderlijk in een opslag service opgeslagen en deze back-ups worden wereld wijd gerepliceerd voor flexibiliteit tegen regionale rampen. Met Azure Cosmos DB, niet alleen uw gegevens, maar ook de back-ups van uw gegevens zijn zeer redundant en robuust voor regionale rampen. De volgende stappen laten zien hoe Azure Cosmos DB gegevens back-up uitvoert:

* Azure Cosmos DB maakt automatisch een volledige back-up van uw data base in elke 4 uur en op elk gewenst moment worden alleen de meest recente twee back-ups standaard opgeslagen. Als de standaard intervallen niet voldoende zijn voor uw workloads, kunt u het back-upinterval en de retentie periode van de Azure Portal wijzigen. U kunt de back-upconfiguratie wijzigen tijdens of nadat het Azure Cosmos-account is gemaakt. Als de container of Data Base wordt verwijderd, Azure Cosmos DB de bestaande moment opnamen van een bepaalde container of data base gedurende 30 dagen behouden.

* Azure Cosmos DB slaat deze back-ups op in Azure Blob-opslag terwijl de daad werkelijke gegevens lokaal zijn opgeslagen in Azure Cosmos DB.

* Om een lage latentie te garanderen, wordt de moment opname van uw back-up opgeslagen in Azure Blob Storage in dezelfde regio als de huidige schrijf regio (of **een** van de schrijf regio's, voor het geval u een multi-regio schrijf configuratie hebt). Voor tolerantie tegen regionale noodsituaties wordt elke momentopname van de back-upgegevens in Azure Blob-opslag opnieuw gerepliceerd naar een andere regio via geografisch redundante opslag (GRS). De regio waarnaar de back-up wordt gerepliceerd, is gebaseerd op de bronregio en het regionale paar dat aan de bronregio is gekoppeld. Zie de [lijst met geo-redundante paren van Azure-regio's](../best-practices-availability-paired-regions.md) voor meer informatie. U hebt geen rechtstreekse toegang tot deze back-up. Azure Cosmos DB team herstelt uw back-up wanneer u daartoe een verzoek via een ondersteuningsaanvraag doet.

  In de volgende afbeelding ziet u hoe een Azure Cosmos-container met alle drie primaire fysieke partities in West VS een back-up maakt in een extern Azure Blob Storage-account in VS-West en vervolgens wordt gerepliceerd naar VS-Oost:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Periodieke volledige back-ups van alle Cosmos DB entiteiten in GRS Azure Storage." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* De back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of Beschik baarheid van uw toepassing. Azure Cosmos DB gegevens back-up op de achtergrond uitvoeren zonder gebruik te maken van extra ingerichte door Voer (RUs) of de prestaties en beschik baarheid van uw data base beïnvloeden.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Het back-upinterval en de Bewaar periode wijzigen

Azure Cosmos DB maakt automatisch een volledige back-up van uw gegevens voor elke 4 uur en op elk gewenst moment worden de meest recente twee back-ups opgeslagen. Deze configuratie is de standaard optie en wordt aangeboden zonder extra kosten. U kunt het standaardwaarden voor het back-upinterval en de bewaartermijn wijzigen terwijl u het Azure Cosmos-account maakt of daarna. De back-upconfiguratie wordt ingesteld op het niveau van het Azure Cosmos-account en u moet deze voor elk account configureren. Nadat u de back-upopties voor een account hebt geconfigureerd, wordt deze toegepast op alle containers in dat account. U kunt de back-upopties momenteel alleen wijzigen in Azure Portal.

Als u uw gegevens per ongeluk hebt verwijderd of beschadigd **voordat u een ondersteunings aanvraag voor het herstellen van de gegevens maakt, moet u de retentie van de back-up voor uw account verg Roten tot ten minste zeven dagen. Het is het beste om uw Bewaar periode binnen 8 uur na deze gebeurtenis te verg Roten.** Op deze manier heeft het Azure Cosmos DB-team voldoende tijd om uw account te herstellen.

Gebruik de volgende stappen om de standaard back-upopties voor een bestaand Azure Cosmos-account te wijzigen:

1. Meld u aan bij de [Azure Portal.](https://portal.azure.com/)
1. Navigeer naar uw Azure Cosmos-account en open het deel venster **back-up & herstellen** . Werk het back-upinterval en de Bewaar periode voor back-ups zo nodig bij.

   * **Back-upinterval** : dit is het interval waarmee Azure Cosmos DB probeert een back-up van uw gegevens te maken. Het maken van een back-up kost een periode van minder dan nul en in sommige gevallen kan dit mislukken vanwege downstream-afhankelijkheden. Azure Cosmos DB probeert echter het beste een back-up te maken met het geconfigureerde interval, maar niet dat de back-up binnen dat tijds interval is voltooid. U kunt deze waarde configureren in uren of minuten. Het back-upinterval mag niet minder dan 1 uur en langer dan 24 uur zijn. Wanneer u dit interval wijzigt, wordt het nieuwe interval van kracht vanaf het moment waarop de laatste back-up werd gemaakt.

   * **Bewaren van back-ups** : deze vertegenwoordigt de periode waarin elke back-up wordt bewaard. U kunt deze configureren in uren of dagen. De minimale Bewaar periode mag niet korter zijn dan twee keer het back-upinterval (in uren) en mag niet groter zijn dan 720 uur.

   * **Kopieën van opgeslagen gegevens** -standaard worden twee back-ups van uw gegevens gratis aangeboden. Als u meer dan twee exemplaren nodig hebt, worden er extra kosten in rekening gebracht. Zie de sectie verbruikte opslag in de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie over de exacte prijs voor extra exemplaren.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Het back-upinterval en de retentie voor een bestaand Azure Cosmos-account configureren." border="true":::

Als u de opties voor back-up configureert tijdens het maken van het account, kunt u het **back-upbeleid** configureren, ofwel **periodiek** of **doorlopend**. Met het periodieke beleid kunt u het back-upinterval en de retentie van back-ups configureren. Het doorlopende beleid is momenteel alleen beschikbaar als u zich aanmeldt. Het Azure Cosmos DB-team zal uw werk belasting beoordelen en uw aanvraag goed keuren.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Periodiek of continu back-upbeleid configureren voor nieuwe Azure Cosmos-accounts." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Gegevens terugzetten vanuit een back-up opvragen

Als u per ongeluk uw data base of container verwijdert, kunt u [een ondersteunings ticket indienen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) of [de ondersteuning van Azure aanroepen](https://azure.microsoft.com/support/options/) om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals **Standard**, **Developer** en abonnementen hoger dan die. Ondersteuning voor Azure is niet beschikbaar voor het **Basic** -abonnement. Zie de pagina [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/) voor meer informatie over verschillende ondersteunings abonnementen.

Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar zijn tijdens de back-upcyclus voor die moment opname.
U moet de volgende gegevens hebben voordat u een herstel bewerking kunt aanvragen:

* Uw abonnement-ID gereed is.

* Op basis van de manier waarop uw gegevens per ongeluk zijn verwijderd of gewijzigd, moet u voor bereidingen treffen voor aanvullende informatie. Het is raadzaam dat u de informatie die u voor het eerst beschikbaar hebt, hebt om de back-and-Case zo klein mogelijk te houden.

* Als het volledige Azure Cosmos DB account wordt verwijderd, moet u de naam van het verwijderde account opgeven. Als u een ander account met dezelfde naam als het verwijderde account maakt, deelt u dit met het ondersteunings team, omdat het u helpt om het juiste account te bepalen. Het is raadzaam om verschillende ondersteunings tickets voor elk verwijderd account te bestand, omdat het de Verwar ring voor de status van herstellen minimaliseert.

* Als een of meer data bases worden verwijderd, moet u het Azure Cosmos-account en de namen van de Azure Cosmos-data base opgeven en aangeven of er een nieuwe Data Base met dezelfde naam bestaat.

* Als een of meer containers worden verwijderd, moet u de naam van het Azure Cosmos-account, de database namen en de container namen opgeven. En geef op of er een container met dezelfde naam bestaat.

* Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, neemt u binnen 8 uur contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/options/) , zodat het Azure Cosmos DB team u kan helpen bij het herstellen van de gegevens van de back-ups. **Voordat u een ondersteunings aanvraag voor het herstellen van de gegevens maakt, moet u [de retentie van de back-up voor uw account verg Roten](#configure-backup-interval-retention) tot ten minste zeven dagen. Het is het beste om uw Bewaar periode binnen 8 uur na deze gebeurtenis te verg Roten.** Op deze manier kan het ondersteunings team van Azure Cosmos DB voldoende tijd hebben om uw account te herstellen.

Naast de naam van het Azure Cosmos-account, database namen, container namen, moet u het tijdstip opgeven waarop de gegevens kunnen worden teruggezet. Het is belang rijk om zo nauw keurig mogelijk te zijn om ons te helpen bij het bepalen van de beste beschik bare back-ups op dat moment. **Het is ook belang rijk om de tijd op te geven in UTC.**

In de volgende scherm afbeelding ziet u hoe u een ondersteunings aanvraag voor een container (verzameling/grafiek/tabel) kunt maken om gegevens te herstellen met behulp van Azure Portal. Geef andere details op, zoals het type gegevens, het doel van de herstel tijd, het tijdstip waarop de gegevens zijn verwijderd om ons te helpen bij het bepalen van de prioriteit van de aanvraag.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Maak een aanvraag voor back-upondersteuning via Azure Portal." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Overwegingen voor het herstellen van de gegevens van een back-up

U kunt uw gegevens per ongeluk verwijderen of wijzigen in een van de volgende scenario's:  

* Verwijder het hele Azure Cosmos-account.

* Een of meer Azure Cosmos-data bases verwijderen.

* Verwijder een of meer Azure Cosmos-containers.

* De Azure Cosmos-items (bijvoorbeeld documenten) in een container verwijderen of wijzigen. Dit specifieke geval wordt meestal gegevens beschadiging genoemd.

* Een Data Base of containers van een gedeeld aanbod binnen een Data Base van het gedeelde aanbod worden verwijderd of beschadigd.

Azure Cosmos DB kunt de gegevens in alle bovenstaande scenario's herstellen. Bij het herstellen wordt een nieuw Azure Cosmos-account gemaakt om de herstelde gegevens te bewaren. De naam van het nieuwe account, als dit niet is opgegeven, heeft de indeling `<Azure_Cosmos_account_original_name>-restored1` . Het laatste cijfer wordt verhoogd wanneer er meerdere herstel pogingen worden gedaan. U kunt geen gegevens herstellen naar een vooraf gemaakt Azure Cosmos-account.

Wanneer u per ongeluk een Azure Cosmos-account verwijdert, kunnen we de gegevens herstellen naar een nieuw account met dezelfde naam als de account naam niet in gebruik is. U wordt aangeraden het account niet opnieuw te maken nadat u het hebt verwijderd. Omdat het niet alleen voor komt dat de herstelde gegevens dezelfde naam gebruiken, maar ook de juiste account wordt gedetecteerd om van moeilijk te herstellen.

Wanneer u per ongeluk een Azure Cosmos-data base verwijdert, kunnen we de gehele data base of een subset van de containers in die data base herstellen. Het is ook mogelijk om specifieke containers in data bases te selecteren en deze terug te zetten naar een nieuw Azure Cosmos-account.

Wanneer u per ongeluk een of meer items in een container verwijdert of wijzigt (het geval van gegevens beschadiging), moet u de tijd opgeven waarop u wilt herstellen. Tijd is belang rijk als er gegevens beschadigd zijn. Omdat de container Live is, wordt de back-up nog steeds uitgevoerd, dus als u na de Bewaar periode blijft (de standaard instelling is acht uur), worden de back-ups overschreven. Als u wilt voor komen dat de back-up wordt overschreven, verhoogt u de retentie van de back-up voor uw account tot ten minste zeven dagen. Het is het beste om uw Bewaar periode binnen 8 uur na de beschadiging van de gegevens te verg Roten.

Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, neemt u binnen 8 uur contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/options/) , zodat het Azure Cosmos DB team u kan helpen bij het herstellen van de gegevens van de back-ups. Op deze manier kan het ondersteunings team van Azure Cosmos DB voldoende tijd hebben om uw account te herstellen.

> [!NOTE]
> Nadat u de gegevens hebt hersteld, worden niet alle bron functies of-instellingen overgedragen naar het herstelde account. De volgende instellingen worden niet overgedragen naar het nieuwe account:
> * VNET-toegangs beheer lijsten
> * Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies
> * Instellingen voor meerdere regio's  

Als u de door Voer inricht op database niveau, gebeurt het back-up-en herstel proces in dit geval op het hele database niveau en niet op het niveau van de afzonderlijke containers. In dergelijke gevallen kunt u geen subset van containers selecteren die u wilt herstellen.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Vereiste machtigingen voor het wijzigen van de retentie of het herstellen van de portal
Principals die deel uitmaken van de rol [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), eigenaar of Inzender, kunnen een herstel aanvraag aanvragen of de Bewaar periode wijzigen.

## <a name="understanding-costs-of-extra-backups"></a>Meer informatie over de kosten van extra back-ups
Twee back-ups zijn gratis en extra back-ups worden in rekening gebracht op basis van de op regio's gebaseerde prijs voor back-upopslag die wordt beschreven in de [prijzen voor back-upopslag](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Als back-upbewaaring bijvoorbeeld is geconfigureerd op 240 uur, 10 dagen en een back-upinterval van 24 uur. Dit betekent 10 kopieën van de back-upgegevens. Uitgaande van 1 TB aan gegevens in VS-West 2, zijn de kosten 0,12 * 1000 * 8 voor back-upopslag in de opgegeven maand. 


## <a name="options-to-manage-your-own-backups"></a>Opties voor het beheren van uw eigen back-ups

Met Azure Cosmos DB SQL-API-accounts kunt u ook uw eigen back-ups onderhouden met behulp van een van de volgende benaderingen:

* Gebruik [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens regel matig te verplaatsen naar een opslag van uw keuze.

* Gebruik Azure Cosmos DB [Change feed](change-feed.md) om gegevens periodiek te lezen voor volledige back-ups of voor incrementele wijzigingen en sla deze op in uw eigen opslag.

## <a name="post-restore-actions"></a>Acties na herstel

Het belangrijkste doel van het terugzetten van gegevens is het herstellen van de gegevens die u per ongeluk hebt verwijderd of gewijzigd. Daarom raden we u aan eerst de inhoud van de herstelde gegevens te controleren om er zeker van te zijn dat deze bevat wat u verwacht. Als alles er goed uitziet, kunt u de gegevens opnieuw naar het primaire account migreren. Hoewel het mogelijk is om het herstelde account te gebruiken als uw nieuwe actieve account, is dit niet de aanbevolen optie als u werk belastingen voor productie hebt. 

Nadat u de gegevens hebt hersteld, ontvangt u een melding over de naam van het nieuwe account (meestal in de indeling `<original-name>-restored1` ) en het tijdstip waarop het account is hersteld. Het herstelde account heeft dezelfde ingerichte door Voer, het indexerings beleid en het bevindt zich in dezelfde regio als het oorspronkelijke account. Een gebruiker die de abonnements beheerder of een cobeheerder is, kan het herstelde account zien.

### <a name="migrate-data-to-the-original-account"></a>Gegevens migreren naar het oorspronkelijke account

Hier volgen verschillende manieren om gegevens terug te migreren naar het oorspronkelijke account:

* Gebruik het [Azure Cosmos DB hulp programma voor gegevens migratie](import-data.md).
* Gebruik de [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Gebruik de [wijzigings feed](change-feed.md) in azure Cosmos db.
* U kunt uw eigen aangepaste code schrijven.

U wordt aangeraden de container of data base onmiddellijk na het migreren van de gegevens te verwijderen. Als u de herstelde data bases of containers niet verwijdert, worden er kosten in rekening gebracht voor aanvraag eenheden, opslag en uitgaand verkeer.

## <a name="next-steps"></a>Volgende stappen

* Neem contact op met de ondersteuning van Azure om een aanvraag voor terugzetten te maken, maar [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
