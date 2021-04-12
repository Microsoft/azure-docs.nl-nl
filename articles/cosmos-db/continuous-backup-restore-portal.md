---
title: Configureer continue back-ups en herstel tijdstippen met behulp van Azure Portal in Azure Cosmos DB.
description: Meer informatie over het identificeren van het herstel punt en het configureren van doorlopende back-ups met Azure Portal. U ziet hoe u een live en verwijderd account kunt herstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 707ef9f60891c1da7c13638e233ee74e78fc20dd
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283934"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Continue back-ups en herstel naar een tijdstip configureren en beheren (preview)-Azure Portal gebruiken
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie voor het terugzetten van het tijdstip van een onbedoelde wijziging in een container kunt u een verwijderde account, data base of container herstellen of herstellen in een regio (waarbij back-ups bestaan). Azure Cosmos DB Met de continue back-upmodus kunt u een herstel bewerking uitvoeren naar een wille keurig tijdstip in de afgelopen 30 dagen.

In dit artikel wordt beschreven hoe u het herstel punt identificeert en doorlopende back-up configureert met Azure Portal.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Een account inrichten met doorlopende back-up

Bij het maken van een nieuw Azure Cosmos DB-account, voor de optie **back-upbeleid** , kiest u **doorlopende** modus om de functionaliteit voor het herstel punt in tijd in te scha kelen voor het nieuwe account. Wanneer deze functie is ingeschakeld voor het account, zijn alle data bases en containers beschikbaar voor continue back-ups. Wanneer u het tijdstip herstelt, worden de gegevens altijd teruggezet naar een nieuw account. u kunt momenteel niet herstellen naar een bestaand account.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Richt een Azure Cosmos DB-account in met continue back-upconfiguratie." border="true":::

## <a name="backup-storage-redundancy"></a>Opslag redundantie van back-ups

Standaard slaat Azure Cosmos DB permanente back-upgegevens op in lokaal redundante opslag-blobs. Voor de regio's waarvoor zone redundantie is geconfigureerd, wordt de back-up opgeslagen in zone-redundante opslag-blobs. In deze modus kunt u de redundantie opslag voor back-ups niet bijwerken.

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Een Live-account herstellen tegen onbedoelde wijzigingen

U kunt Azure Portal gebruiken om een Live-account of geselecteerde data bases en containers daarin te herstellen. Gebruik de volgende stappen om uw gegevens te herstellen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)
1. Navigeer naar uw Azure Cosmos DB-account en open het deel venster **herstel punt in tijd** .

   > [!NOTE]
   > Het deel venster herstellen in Azure Portal wordt alleen ingevuld als u de `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` machtiging hebt. Zie het artikel [back-ups maken en herstellen](continuous-backup-restore-permissions.md) voor meer informatie over het instellen van deze machtiging.

1. Vul de volgende gegevens in om terug te zetten:

   * **Herstel punt (UTC)** : een tijds tempel in de afgelopen 30 dagen. Het account moet bestaan in die tijds tempel. U kunt het herstel punt in UTC opgeven. Het kan even zijn als de tweede wanneer u deze wilt herstellen. Selecteer de koppeling **Klik hier** om hulp te krijgen bij [het identificeren van het herstel punt](#event-feed).

   * **Locatie** : de doel regio waar het account wordt teruggezet. Het account moet voor komen in deze regio met de opgegeven tijds tempel (bijvoorbeeld VS-West of VS-Oost). Een account kan alleen worden teruggezet naar de regio's waarin het bron account bestond.

   * **Resource herstellen** : u kunt een **volledig account** of een **geselecteerde data base/container** kiezen om te herstellen. De data bases en containers moeten aanwezig zijn op de opgegeven tijds tempel. Op basis van het herstel punt en de geselecteerde locatie worden de herstel resources gevuld, waardoor de gebruiker specifieke data bases of containers kan selecteren die moeten worden hersteld.

   * **Resource groep** : de resource groep waaronder het doel account wordt gemaakt en teruggezet. De resource groep moet al bestaan.

   * **Doel account herstellen** : de naam van het doel account. De naam van het doel account moet dezelfde richt lijnen volgen als wanneer u een nieuw account maakt. Dit account wordt gemaakt door het herstel proces in de regio waarin uw bron account zich bevindt.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Een Live-account herstellen op basis van onbedoelde wijzigings Azure Portal." border="true":::

1. Nadat u de bovenstaande para meters hebt geselecteerd, selecteert u de knop **verzenden** om een herstel bewerking te starten. De herstel kosten zijn eenmalige kosten, die zijn gebaseerd op de hoeveelheid gegevens en kosten voor de opslag in bepaalde regio's. Zie de sectie met [prijzen](continuous-backup-restore-introduction.md#continuous-backup-pricing) voor meer informatie.

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Gebeurtenis invoer gebruiken om de herstel tijd te identificeren

Als u hulp nodig hebt bij het identificeren van het herstel punt in de Azure Portal, selecteert u de koppeling **Klik hier** om naar de Blade gebeurtenis doorvoer te gaan. De gebeurtenis feed biedt een volledige lijst met een overzicht van het maken, vervangen en verwijderen van gebeurtenissen in data bases en containers van het bron account. 

Als u bijvoorbeeld wilt herstellen naar het punt voordat een bepaalde container is verwijderd of bijgewerkt, controleert u deze gebeurtenis feed. Gebeurtenissen worden in chronologische volg orde weer gegeven wanneer ze zijn opgetreden, met recente gebeurtenissen bovenaan. U kunt door de resultaten bladeren en de tijd voor of na de gebeurtenis selecteren om uw tijd verder te verfijnen.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Gebruik gebeurtenis invoer om de herstel punt tijd te identificeren." border="true":::

> [!NOTE]
> In de gebeurtenis feed worden de wijzigingen in de item resources niet weer gegeven. U kunt altijd hand matig een wille keurige tijds tempel opgeven in de afgelopen 30 dagen (op voor waarde dat het account op dat moment bestaat) voor herstellen.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Een verwijderd account herstellen

U kunt Azure Portal gebruiken om een verwijderde account binnen 30 dagen na verwijdering volledig te herstellen. Gebruik de volgende stappen om een verwijderd account te herstellen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)
1. Zoek naar *Azure Cosmos DB* resources op de algemene zoek balk. Alle bestaande accounts worden weer gegeven.
1. Selecteer vervolgens de knop **herstellen** . In het deel venster herstellen wordt een lijst weer gegeven met verwijderde accounts die binnen de retentie periode kunnen worden hersteld. Dit is 30 dagen vanaf de verwijderings tijd.
1. Kies het account dat u wilt herstellen.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Een verwijderd account herstellen uit Azure Portal." border="true":::

   > [!NOTE]
   > Opmerking: het deel venster herstellen in Azure Portal wordt alleen ingevuld als u de `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` machtiging hebt. Zie het artikel [back-ups maken en herstellen](continuous-backup-restore-permissions.md) voor meer informatie over het instellen van deze machtiging.

1. Selecteer een account om te herstellen en voer de volgende gegevens in om een verwijderd account te herstellen:

   * **Herstel punt (UTC)** : een tijds tempel in de afgelopen 30 dagen. Het account moet zich in die tijds Tempel bevonden. Geef het herstel punt op in UTC. Het kan even zijn als de tweede wanneer u deze wilt herstellen.

   * **Locatie** : de doel regio waar het account moet worden hersteld. Het bron account moet voor komen in deze regio met de opgegeven tijds tempel. Voor beeld vs-West of VS-Oost.  

   * **Resource groep** : de resource groep waaronder het doel account wordt gemaakt en teruggezet. De resource groep moet al bestaan.

   * **Doel account herstellen** : de naam van het doel account moet dezelfde richt lijnen volgen als wanneer u een nieuw account maakt. Dit account wordt gemaakt door het herstel proces in de regio waarin uw bron account zich bevindt.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>De status van de herstel bewerking bijhouden

Nadat u een herstel bewerking hebt gestart, selecteert u in de rechter bovenhoek van de portal het pictogram met de **meldings** klok. Er wordt een koppeling weer gegeven met de status van het account dat wordt hersteld. Wanneer het terugzetten wordt uitgevoerd, wordt de status van het account *gemaakt* nadat de herstel bewerking is voltooid, wordt de account status gewijzigd in *online*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="De status van de herstelde account verandert van maken in online wanneer de bewerking is voltooid." border="true":::

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure cli](continuous-backup-restore-command-line.md), [power shell](continuous-backup-restore-powershell.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
