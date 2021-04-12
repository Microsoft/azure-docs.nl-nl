---
title: Aanbevelings samenvatting voor Azure Advisor
description: Periodieke samen vatting van uw actieve aanbevelingen ophalen
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579813"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Periodieke samen vatting voor aanbevelingen configureren

Advisor **Recommendation digesties** bieden een gemakkelijke en proactieve manier om op de hoogte te blijven van uw actieve aanbevelingen, in verschillende categorieën. De functie biedt de mogelijkheid om periodieke meldingen te configureren voor het overzicht van al uw actieve aanbevelingen, in verschillende categorieën. U kunt uw gewenste kanaal kiezen voor meldingen zoals e-mail, SMS of anderen, met behulp van actie groepen. Dit artikel laat u zien hoe u een **aanbevelings samenvatting** kunt instellen voor uw aanbevelingen voor Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Uw aanbevelings samenvatting instellen 

Met de functie voor het maken van **aanbevelings samenvatting** kunt u de samen vatting configureren. U kunt de onderstaande para meters voor configuraties selecteren:
1. Categorie: we hebben aanbevelings categorieën zoals kosten, hoge Beschik baarheid, prestaties en bedrijfs kwaliteit. De mogelijkheid is nog niet beschikbaar voor beveiligings aanbevelingen.
2. Frequentie van Digest: frequentie voor de samenvattings meldingen kan wekelijks, twee weken en maandelijks zijn.
3. Actie groep: u kunt een bestaande actie groep selecteren of een nieuwe actie groep maken. Zie [actie groepen maken en beheren](../azure-monitor/alerts/action-groups.md)voor meer informatie over actie groepen.
4. Taal voor de samen vatting
5. Naam van aanbevelings Samenvatting: u kunt een gebruiks vriendelijke teken reeks gebruiken om de samen vattingen beter bij te houden en te controleren.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Stappen voor het maken van aanbevelings samenvatting in Azure Portal

Hier volgen de stappen voor het maken van **aanbevelings Samenvatting:**
* **Stap 1:** Ga in het Azure Portal naar **Advisor** en selecteer onder **bewaking** het gedeelte **aanbeveling samen vatting** 

   ![Samenvattings vermelding van aanbeveling: punt](./media/digest-0.png)

* **Stap 2:** Selecteer **nieuwe aanbevelings samenvatting** in de bovenste balk, zoals hieronder wordt beschreven:

   ![Aanbevelings samenvatting maken](./media/digest-5.png)

* **Stap 3:** Selecteer in de sectie **bereik** het **abonnement** voor uw Digest

   ![Invoer van aanbevelings samenvatting opgeven](./media/digest-1.png)

* **Stap 4:** Selecteer in de sectie **voor waarde** de configuraties, zoals **categorie**, **frequentie** en **taal**

   ![Invoer voorwaarden voor aanbevelings samenvatting opgeven](./media/digest-2.png)

* **Stap 5:** Selecteer in de sectie **actie groep** de **actie groep** voor de samen vatting. Hier vindt u meer informatie- [actie groepen maken en beheren](../azure-monitor/alerts/action-groups.md)

   ![Invoer actie groep aanbevelings samenvatting opgeven](./media/digest-3.png)

* **Stap 6:** In dit laatste gedeelte voor **samenvattings Details** kunt u de naam en de status toewijzen aan uw aanbevelings samenvatting. Druk op **aanbevelings samenvatting maken** om de configuratie te volt ooien.
   ![Volt ooien aanbevelings samenvatting maken](./media/digest-4.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
