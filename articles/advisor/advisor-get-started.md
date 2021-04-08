---
title: Aan de slag met Azure Advisor
description: Aan de slag met Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85117859"
---
# <a name="get-started-with-azure-advisor"></a>Aan de slag met Azure Advisor

Meer informatie over het openen van Advisor via de Azure Portal, het ophalen van aanbevelingen en het implementeren van aanbevelingen.

> [!NOTE]
> Azure Advisor wordt automatisch op de achtergrond uitgevoerd om nieuwe resources te vinden. Het kan tot 24 uur duren om aanbevelingen te doen voor deze resources.

## <a name="get-recommendations"></a>Aanbevelingen ophalen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik in het linkerdeel venster op **Advisor**.  Als u Advisor niet in het linkerdeel venster ziet, klikt u op **alle services**.  Klik in het menu venster Service, onder **bewaking en beheer**, op **Advisor**. Het Advisor-dash board wordt weer gegeven.

   ![Toegang tot Azure Advisor via de Azure Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. In het Advisor-dashboard geeft een samenvatting weer van uw aanbevelingen voor alle geselecteerde abonnementen.  U kunt de abonnementen kiezen waarvoor u aanbevelingen wilt weergeven voor het gebruik van de vervolgkeuzelijst met het abonnementsfilter.

1. Als u aanbevelingen voor een specifieke categorie wilt ontvangen, klikt u op een van de tabbladen: **betrouw baarheid**, **beveiliging**, **prestaties** of **kosten**. 

   ![Azure Advisor dash board](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Details van aanbevelingen ophalen en een oplossing implementeren

U kunt een aanbeveling in Advisor selecteren om meer details weer te geven, zoals de aanbevolen acties en betrokken resources, en de oplossing implementeren voor de aanbeveling.  

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Selecteer een aanbevelings categorie om de lijst met aanbevelingen in die categorie weer te geven of selecteer het tabblad **alle** om al uw aanbevelingen weer te geven.

1. Klik op een aanbeveling die u gedetailleerd wilt bekijken.

1. Lees de informatie over de aanbeveling en de resources waarop de aanbeveling van toepassing is.

1. Klik op de **Aanbevolen actie** om de aanbeveling te implementeren.

## <a name="filter-recommendations"></a>Filter aanbevelingen

U kunt de aanbevelingen filteren om in te zoomen op wat het belangrijkst is voor u.  U kunt filteren op abonnement, resource type of advies status.  

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Gebruik de vervolg keuzelijsten in het Advisor-dash board om te filteren op het abonnement, het resource type of de status van de aanbeveling.

    ![Advisor search-filter criteria](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Aanbevelingen uitstellen of negeren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Navigeer naar de aanbeveling die u wilt uitstellen of verwijderen.

1. Klik op de aanbeveling.

1. Klik op **uitstellen**. 

1. Geef een uitstel periode op of selecteer **nooit** om de aanbeveling te negeren.

## <a name="exclude-subscriptions-or-resource-groups"></a>Abonnementen of resource groepen uitsluiten

Mogelijk hebt u resource groepen of-abonnementen waarvoor u geen aanbevelingen voor Advisor wilt ontvangen, zoals ' test ' resources.  U kunt Advisor configureren zodat alleen aanbevelingen voor specifieke abonnementen en resource groepen worden gegenereerd.

> [!NOTE]
> Als u een abonnement of resource groep van Advisor wilt opnemen of uitsluiten, moet u eigenaar van het abonnement zijn.  Als u niet de vereiste machtigingen voor een abonnement of resource groep hebt, kunt u de optie voor het opnemen of uitsluiten van de toepassing uitschakelen in de gebruikers interface.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Klik op **configureren** in de actie balk.

1. Schakel de selectie vakjes uit voor alle abonnementen of resource groepen waarvoor u geen aanbevelingen voor Advisor wilt ontvangen.

    ![Voor beeld van Advisor-bronnen configureren](./media/advisor-get-started/advisor-configure-resources.png)

1. Klik op de knop **Apply** (Toepassen).

## <a name="configure-low-usage-vm-recommendation"></a>Aanbeveling voor VM van laag gebruik configureren

Met deze procedure configureert u de gemiddelde CPU-gebruiks regel voor de aanbeveling van de virtuele machine met weinig gebruik.

Advisor bewaakt het gebruik van uw virtuele machines gedurende 7 dagen en identificeert vervolgens virtuele machines met weinig gebruik. Virtuele machines worden beschouwd als laag gebruik als het CPU-gebruik 5% of minder is en het netwerk gebruik lager is dan 2% of als de huidige werk belasting kan worden aangepast aan de grootte van een kleinere virtuele machine.

Als u meer wilt weten over het identificeren van virtuele machines met een laag gebruik, kunt u het gemiddelde CPU-gebruiks regel niveau op basis van een abonnement aanpassen.  De regel voor het CPU-gebruik kan worden ingesteld op 5%, 10%, 15% of 20%.

> [!NOTE]
> Als u de regel gemiddeld CPU-gebruik voor het identificeren van virtuele machines met een laag gebruik wilt aanpassen, moet u *eigenaar* van het abonnement zijn.  Als u niet beschikt over de vereiste machtigingen voor een abonnement of resource groep, wordt de optie om deze op te nemen of uit te sluiten, uitgeschakeld in de gebruikers interface. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Klik op **configureren** in de actie balk.

1. Klik op het tabblad **regels** .

1. Selecteer de abonnementen waarvoor u de gemiddelde regel voor CPU-gebruik wilt aanpassen en klik vervolgens op **bewerken**.

1. Selecteer de waarde voor het gewenste gemiddelde CPU-gebruik en klik op **Toep assen**.

1. Klik op **aanbevelingen vernieuwen** om uw bestaande aanbevelingen bij te werken voor het gebruik van de nieuwe regel voor gemiddeld CPU-gebruik. 

   ![Voor beeld van aanbevelings regels voor Advisor configureren](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Aanbevelingen downloaden

Met Advisor kunt u een samen vatting van uw aanbevelingen downloaden.  U kunt uw aanbevelingen downloaden als een PDF-bestand of een CSV-bestand.  Door uw aanbevelingen te downloaden, kunt u eenvoudig delen met uw collega's of uw eigen analyse uitvoeren boven op de gegevens van de aanbeveling.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

1. Klik op **down load als CSV** of **down load als PDF** op de actie balk.

De download optie respecteert alle filters die u hebt toegepast op het Advisor-dash board.  Als u de optie downloaden selecteert terwijl u een specifieke aanbevelings categorie of aanbeveling bekijkt, bevat het gedownloade overzicht alleen informatie over deze categorie of aanbeveling. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor:

- [Inleiding tot Azure Advisor](advisor-overview.md)
- [Aanbevelingen voor de Advisor-betrouw baarheid](advisor-high-availability-recommendations.md)
- [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
- [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
- [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
- [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
