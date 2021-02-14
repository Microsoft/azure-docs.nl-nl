---
title: Permanente back-up met de functie voor herstel naar een bepaald moment in Azure Cosmos DB
description: Met de functie voor herstel naar een bepaald tijdstip van Azure Cosmos DB kunt u gegevens herstellen van een onbedoeld schrijven, een Verwijder bewerking of gegevens herstellen in een regio. Meer informatie over prijzen en huidige beperkingen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393136"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Doorlopende back-up met een functie voor herstel naar een bepaald tijdstip in Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De functie voor het terugzetten van een bepaald tijdstip van Azure Cosmos DB (preview) helpt in meerdere scenario's zoals de volgende:

* Herstellen van een onbedoelde schrijf-of verwijder bewerking binnen een container.
* Een verwijderd account, een Data Base of een container herstellen.
* Herstellen in een wille keurige regio (waarbij er back-ups bestonden) op het herstel punt in de tijd.

Azure Cosmos DB gegevens back-up op de achtergrond uitvoeren zonder gebruik te maken van extra ingerichte door Voer (RUs) of de prestaties en beschik baarheid van uw data base beïnvloeden. Doorlopende back-ups worden gemaakt in elke regio waarin het account zich bevindt. In de volgende afbeelding ziet u hoe een container met een schrijf regio in West VS, regio's lezen in Oost en VS Oost 2 een back-up maakt naar een extern Azure Blob Storage-account in de desbetreffende regio's. Standaard wordt de back-up in elke regio opgeslagen in lokaal redundante opslag accounts. Als de regio [beschikbaarheids zones](high-availability.md#availability-zone-support) heeft ingeschakeld, wordt de back-up opgeslagen in Zone-Redundant opslag accounts.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB gegevens back-up naar de Azure-Blob Storage." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Het beschik bare tijd venster voor herstel (ook wel de Bewaar periode genoemd) is de lagere waarde van de volgende twee: *30 dagen terug vanaf nu* of *tot de aanmaak tijd van de resource*. Het tijdstip voor het terugzetten kan een tijds tempel zijn binnen de Bewaar periode.

In de open bare preview kunt u het Azure Cosmos DB-account voor de SQL-API of het MongoDB-inhouds punt in de tijd herstellen naar een ander account met behulp van [Azure Portal](continuous-backup-restore-portal.md), de [Azure-opdracht regel interface](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)of de [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Wat wordt hersteld?

Bij een constante status worden alle mutaties die zijn uitgevoerd op het bron account (inclusief data bases, containers en items) asynchroon van een back-up binnen 100 seconden gemaakt. Als de back-upmedia (die Azure Storage is) niet actief of niet beschikbaar is, worden de mutaties lokaal bewaard totdat de media weer beschikbaar zijn. vervolgens worden ze verwijderd om verlies van de betrouw baarheid van bewerkingen te voor komen dat kan worden hersteld.

U kunt ervoor kiezen om een combi natie van ingerichte doorvoer containers, een gedeelde doorvoer database of het hele account te herstellen. Met de actie VorigFormaat worden alle gegevens en de index eigenschappen van een nieuw account hersteld. Het herstel proces zorgt ervoor dat alle gegevens die in een account, data base of container zijn teruggezet, gegarandeerd consistent zijn met de opgegeven herstel tijd. De duur van het terugzetten is afhankelijk van de hoeveelheid gegevens die moet worden hersteld.

> [!NOTE]
> Met de continue back-upmodus worden de back-ups gemaakt in elke regio waar uw Azure Cosmos DB-account beschikbaar is. Back-ups die zijn gemaakt voor elk regio account zijn lokaal redundant, standaard en zone redundant als uw account de functie [beschikbaarheids zone](high-availability.md#availability-zone-support) heeft ingeschakeld voor die regio. Met de herstel actie worden gegevens altijd teruggezet naar een nieuw account.

## <a name="what-is-not-restored"></a>Wat wordt niet hersteld?

De volgende configuraties worden niet hersteld na het herstel naar een bepaald tijdstip:

* Instellingen voor de firewall, het VNET, het persoonlijke eind punt.
* Consistentie-instellingen. Het account wordt standaard teruggezet met sessie consistentie.  
* Secties.
* Opgeslagen procedures, triggers, Udf's.

U kunt deze configuraties toevoegen aan het herstelde account nadat het herstellen is voltooid.

## <a name="restore-scenarios"></a>Herstelscenario's

Hier volgen enkele van de belangrijkste scenario's die worden verholpen door de functie voor het herstellen van een bepaald tijdstip. Scenario's [a] tot en met [c] laten zien hoe u een herstel bewerking kunt activeren als de tijds tempel van de terugzet bewerking vooraf bekend is.
Er kunnen echter scenario's zijn waarin u niet precies weet wat er per ongeluk is verwijderd of beschadigd. Scenario's [d] en [e] laten zien hoe u de Restore-tijds tempel kunt _detecteren_ met behulp van de nieuwe event feed-api's voor de herstelbare data base of containers.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Levenscyclus gebeurtenissen met tijds tempels voor een restorable-account." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Verwijderd account herstellen** : alle verwijderde accounts die u kunt herstellen, worden weer gegeven in het deel venster **herstellen** . Als *account A* bijvoorbeeld wordt verwijderd bij tijds tempel T3. In dit geval is de tijds tempel net vóór T3, locatie, doel accountnaam, resource groep en doel account naam voldoende om te herstellen vanaf [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [Power shell](continuous-backup-restore-powershell.md#trigger-restore)of [cli](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Levenscyclus gebeurtenissen met tijds tempels voor een onstorbare data base en container." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Gegevens herstellen van een account in een bepaalde regio** , bijvoorbeeld als *account a* bestaat in twee regio's VS- *Oost* en *VS-West* op tijds tempel T3. Als u een kopie van account A in *West VS* nodig hebt, kunt u een herstel punt in de tijd herstellen vanaf [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md#trigger-restore)of [cli](continuous-backup-restore-command-line.md#trigger-restore) met VS-West als doel locatie.

c. **Herstellen van een onbedoelde schrijf-of verwijder bewerking binnen een container met een bekende Restore-tijds tempel** , bijvoorbeeld als u **weet** dat de inhoud van *container 1* in *Data Base 1* per ongeluk is gewijzigd bij tijds tempel T3. U kunt een herstel punt van [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [Power shell](continuous-backup-restore-powershell.md#trigger-restore)of [cli](continuous-backup-restore-command-line.md#trigger-restore) in een ander account in time stamp T3 uitvoeren om de gewenste status van de container te herstellen.

d. **Een account herstellen naar een eerder tijdstip voordat de data base per ongeluk is verwijderd** -in de [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), kunt u het deel venster gebeurtenis invoer gebruiken om te bepalen wanneer een Data Base is verwijderd en de herstel tijd te vinden. Op dezelfde manier kunt u met [Azure cli](continuous-backup-restore-command-line.md#trigger-restore) en [Power shell](continuous-backup-restore-powershell.md#trigger-restore)de gebeurtenis voor het verwijderen van de data base detecteren door de invoer van database gebeurtenissen te inventariseren en vervolgens de opdracht Restore te activeren met de vereiste para meters.

e. **Een account herstellen naar een eerder tijdstip voordat de container eigenschappen per ongeluk zijn verwijderd of gewijzigd.** -In [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)kunt u het deel venster gebeurtenis invoer gebruiken om te bepalen wanneer een container is gemaakt, gewijzigd of verwijderd om de herstel tijd te vinden. Op dezelfde manier kunt u met [Azure cli](continuous-backup-restore-command-line.md#trigger-restore) en [Power shell](continuous-backup-restore-powershell.md#trigger-restore)alle container gebeurtenissen detecteren door de feed voor container gebeurtenissen op te sommen en vervolgens de opdracht Restore te activeren met de vereiste para meters.

## <a name="permissions"></a>Machtigingen

Met Azure Cosmos DB kunt u de herstel machtigingen voor een continu back-upaccount isoleren en beperken tot een specifieke rol of een principal. De eigenaar van het account kan een Restore activeren en een rol toewijzen aan andere principals om de herstel bewerking uit te voeren. Zie het artikel over [machtigingen](continuous-backup-restore-permissions.md) voor meer informatie.

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Prijzen

Azure Cosmos DB accounts waarvoor continue back-up is ingeschakeld, worden maandelijks extra kosten in rekening gebracht voor *het opslaan van de back-up* en voor het *herstellen van uw gegevens*. De herstel kosten worden telkens toegevoegd wanneer de herstel bewerking wordt gestart. Als u een account met doorlopende back-up configureert, maar de gegevens niet herstelt, worden alleen back-upopslagkosten opgenomen in uw factuur.

Het volgende voor beeld is gebaseerd op de prijs van een Azure Cosmos-account dat is geïmplementeerd in een niet-overheids regio in de Verenigde Staten. De prijzen en berekening kunnen variëren, afhankelijk van de regio die u gebruikt. Zie de [pagina met prijzen voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente prijs informatie.

* Voor alle accounts waarvoor continu back-upbeleid is ingeschakeld, zijn er extra maandelijkse kosten in rekening gebracht voor back-upopslag die als volgt wordt berekend:

  $0,20/GB * gegevens grootte in GB in het account * aantal regio's

* Elke reaanroep van Restore API wordt één keer in rekening gebracht. De kosten zijn een functie van de hoeveelheid gegevens herstel en worden als volgt berekend:

  $0.15/GB * gegevens grootte in GB.

Als u bijvoorbeeld 1 TB aan gegevens in twee regio's hebt, gaat u als volgt te werkt:

* De kosten voor back-upopslag worden berekend als (1000 * 0,20 * 2) = $400 per maand

* De herstel kosten worden berekend als (1000 * 0,15) = $150 per herstel

## <a name="current-limitations-public-preview"></a>Huidige beperkingen (open bare preview)

De functionaliteit van het herstel punt in tijd is momenteel beschikbaar in de open bare preview en heeft de volgende beperkingen:

* Alleen Azure Cosmos DB-Api's voor SQL en MongoDB worden ondersteund voor continue back-ups. Cassandra-, Table-en Gremlin-Api's worden nog niet ondersteund.

* Een bestaand account met het standaard beleid voor periodieke back-ups kan niet worden geconverteerd om de continue back-upmodus te gebruiken.

* Azure soevereine en Azure Government Cloud regio's worden nog niet ondersteund.

* Accounts met door de klant beheerde sleutels worden niet ondersteund voor het gebruik van continue back-ups.

* Multi-regio schrijf accounts worden niet ondersteund.

* Accounts waarvoor Synapse-koppeling is ingeschakeld, worden niet ondersteund.

* Het herstelde account wordt gemaakt in de regio waarin uw bron account zich bevindt. U kunt een account niet herstellen in een regio waar het bron account niet bestaat.

* Het venster herstellen is slechts 30 dagen en kan niet worden gewijzigd.

* De back-ups zijn niet automatisch geografisch nood bestendig. U moet expliciet een andere regio toevoegen voor een tolerantie voor het account en de back-up.

* Wanneer een herstel bewerking wordt uitgevoerd, wijzigt of verwijdert u niet de beleids regels voor identiteits-en toegangs beheer (IAM) die de machtigingen verlenen voor het account of het wijzigen van een VNET, firewall configuratie.

* Azure Cosmos DB-API voor SQL-of MongoDB-accounts die een unieke index maken nadat de container is gemaakt, worden niet ondersteund voor continue back-ups. Alleen containers die een unieke index maken als onderdeel van het maken van de eerste container worden ondersteund. Voor MongoDB-accounts maakt u een unieke index met behulp van [extensie opdrachten](mongodb-custom-commands.md).

* De functionaliteit voor herstel naar een bepaald tijdstip wordt altijd teruggezet naar een nieuw Azure Cosmos-account. Het herstellen naar een bestaand account wordt momenteel niet ondersteund. Als u geïnteresseerd bent in het geven van feedback over in-place herstel, neemt u contact op met het Azure Cosmos DB team via uw account vertegenwoordiger of [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Alle nieuwe api's die worden weer gegeven voor de vermelding `RestorableDatabaseAccount` ,,,, `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` zijn onderhevig aan wijzigingen wanneer de functie in preview is.

* Na het herstellen is het mogelijk dat voor bepaalde verzamelingen de consistente index opnieuw kan worden samengesteld. U kunt de status van de bewerking voor opnieuw opbouwen controleren via de eigenschap [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* Het herstel proces herstelt alle eigenschappen van een container met inbegrip van de TTL-configuratie. Als gevolg hiervan is het mogelijk dat de herstelde gegevens onmiddellijk worden verwijderd als u die manier hebt geconfigureerd. Om deze situatie te voor komen, moet het tijds tempel van de herstel bewerking zijn voordat de TTL-eigenschappen aan de container zijn toegevoegd.

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
