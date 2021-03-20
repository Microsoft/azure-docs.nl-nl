---
title: Azure Advisor aanbevelingen bekijken die voor u belang rijk zijn
description: Azure Advisor aanbevelingen weer geven en filteren om ruis te verminderen.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90986867"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Azure Advisor aanbevelingen bekijken die voor u belang rijk zijn

Azure Advisor biedt aanbevelingen om u te helpen uw Azure-implementaties te optimaliseren. Binnen Advisor hebt u toegang tot een aantal functies waarmee u uw aanbevelingen kunt beperken tot alleen die zaken die voor u van belang zijn.

## <a name="configure-subscriptions-and-resource-groups"></a>Abonnementen en resource groepen configureren

Advisor biedt u de mogelijkheid om abonnementen en resource groepen te selecteren die voor u en uw organisatie van belang zijn. U ziet alleen aanbevelingen voor de abonnementen en resource groepen die u selecteert. Standaard zijn alle ingeschakeld. Configuratie-instellingen zijn van toepassing op het abonnement of de resource groep, zodat dezelfde instellingen van toepassing zijn op iedereen die toegang heeft tot dat abonnement of deze resource groep. Configuratie-instellingen kunnen worden gewijzigd in de Azure Portal of via een programma.

Wijzigingen aanbrengen in de Azure Portal:

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in het Azure Portal.

1. Selecteer **configuratie** in het menu.

   ![Menu configuratie van Advisor](./media/view-recommendations/configuration.png)

1. Schakel het selectie vakje in de kolom **insluiting** voor abonnementen of resource groepen in om Advisor-aanbevelingen te ontvangen. Als het selectie vakje is uitgeschakeld, bent u mogelijk niet gemachtigd om een configuratie wijziging aan te brengen voor dat abonnement of deze resource groep. Meer informatie over [machtigingen in azure Advisor](permissions.md).

1. Klik op **Toep assen** aan de onderkant nadat u een wijziging hebt aangebracht.

## <a name="filtering-your-view-in-the-azure-portal"></a>Uw weer gave filteren in de Azure Portal

Configuratie-instellingen blijven actief totdat ze worden gewijzigd. Als u de weer gave van aanbevelingen wilt beperken voor één weergave, kunt u de vervolg keuzelijsten boven aan het deel venster Advisor gebruiken. Vanuit de deel Vensters overzicht, hoge Beschik baarheid, beveiliging, prestaties, kosten en alle aanbevelingen kunt u de abonnementen, resource typen en aanbevelings status selecteren die u wilt zien.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="Scherm opname van Azure Advisor weer gave van filter opties.":::

## <a name="dismissing-and-postponing-recommendations"></a>De aanbevelingen voor negeren en uitstellen

Met Azure Advisor kunt u aanbevelingen op één resource negeren of uitstellen. Als u een aanbeveling negeert, wordt deze niet meer weer geven, tenzij u deze hand matig activeert. Door een aanbeveling uit te stellen, kunt u echter een duur opgeven waarna de aanbeveling automatisch opnieuw wordt geactiveerd. Uitstellen kan worden uitgevoerd in de Azure Portal of via een programma.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Een enkele aanbeveling uitstellen in de Azure Portal 

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in het Azure Portal.
1. Selecteer een aanbevelings categorie om uw aanbevelingen weer te geven
1. Selecteer een aanbeveling in de lijst met aanbevelingen
1. Selecteer uitstellen of verwijderen voor de aanbeveling die u wilt uitstellen of verwijderen

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="Scherm afbeelding van het venster voor het gebruiken van Managed Disks waarin de kolom selecteren wordt weer gegeven en de acties uitstellen en verwijderen voor één aanbeveling gemarkeerd.":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Een aantal aanbevelingen uitstellen of verwijderen in de Azure Portal

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in het Azure Portal.
1. Selecteer een aanbevelings categorie om uw aanbevelingen weer te geven.
1. Selecteer een aanbeveling in de lijst met aanbevelingen.
1. Schakel het selectie vakje links van de rij in voor alle resources die u wilt uitstellen of de aanbeveling wilt negeren.
1. Selecteer **uitstellen** of **verwijderen** in de linkerbovenhoek van de tabel.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="Scherm afbeelding van het venster voor het gebruiken van Managed Disks waarin de kolom selecteren wordt weer gegeven en de acties uitstellen en verwijderen in de linkerbovenhoek van de tabel gemarkeerd.":::

> [!NOTE]
> U hebt de machtiging Inzender of eigenaar nodig om een aanbeveling te negeren of uit te stellen. Meer informatie over machtigingen in Azure Advisor.

> [!NOTE]
> Als de selectie vakjes zijn uitgeschakeld, kunnen er nog steeds aanbevelingen worden geladen. Wacht tot alle aanbevelingen zijn geladen voordat u probeert af te stellen of te verwijderen.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Een uitgestelde of genegeerde aanbeveling opnieuw activeren

U kunt een aanbeveling activeren die is uitgesteld of genegeerd. Deze actie kan worden uitgevoerd in de Azure Portal of via een programma. In Azure Portal:

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in het Azure Portal.

1. Wijzig het filter in het deel venster Overzicht in **uitgesteld**. In Advisor worden vervolgens uitgestelde of genegeerde aanbevelingen weer gegeven.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="Scherm opname van het Azure Advisor venster met de uitgestelde vervolg keuzelijst geselecteerd.":::

1. Selecteer een categorie om **uitgestelde** en **Genegeerde** aanbevelingen weer te geven.

1. Selecteer een aanbeveling in de lijst met aanbevelingen. Hiermee worden aanbevelingen voor het weer geven van de **Uitgestelde &** uitgeschakeld tabblad al geselecteerd om de resources te tonen waarvoor deze aanbeveling is uitgesteld of genegeerd.

1. Klik op **Activate** aan het einde van de rij. Wanneer u eenmaal hebt geklikt, is de aanbeveling actief voor die resource en dus uit deze tabel verwijderd. De aanbeveling is nu zichtbaar op het **actieve** tabblad.
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="Scherm opname van het venster voorlopig verwijderen inschakelen met het tabblad uitgestelde & genegeerd met de actie activeren gemarkeerd.":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u in Azure Advisor aanbevelingen kunt bekijken. Zie voor meer informatie over Advisor: 

- [Wat is Azure Advisor?](advisor-overview.md)
- [Aan de slag met Advisor](advisor-get-started.md)
- [Machtigingen in Azure Advisor](permissions.md)



