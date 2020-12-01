---
title: Aan de slag en een Azure Lab Services Lab maken in teams
description: Meer informatie over hoe u aan de slag kunt gaan en een Azure Lab Services Lab maakt in teams.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433968"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Aan de slag en een Lab-service Lab maken in teams

In dit artikel wordt beschreven hoe u de **Azure Lab Services** -app toevoegt aan een team en hoe u een Lab maakt in een omgeving met MS-teams.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie stelt u een lab in met virtuele machines voor uw team. Als u een lab in een Lab-account wilt instellen, moet u lid zijn van een van deze rollen in het lab-account: eigenaar, Lab-maker of Inzender. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan de eigenaarsrol. U kunt dus het gebruikers account dat u hebt gebruikt om een Lab-account te maken, gebruiken om een lab te maken.

Dit is de gebruikelijke werk stroom voor het gebruik van Azure Lab Services in teams

1. Gebruiker [maakt een Lab-account](tutorial-setup-lab-account.md#create-a-lab-account) op het Azure Portal.
1. Een [Lab-account Maker voegt andere gebruikers](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) toe aan de rol **Lab Creator** . De labaccountmaker/beheerder voegt bijvoorbeeld onderwijzers toe aan de rol **Labmaker**, zodat ze labels voor hun klassen kunnen maken.
1. De docenten maken vervolgens Labs, configureren de VM van de sjabloon vooraf en publiceert het lab om de virtuele machine in te richten op iedereen in het team.
1. Zodra het lab is gepubliceerd, wordt er een virtuele machine aan iedereen in de team lidmaatschaps lijst van de eerste aanmelding bij Azure Lab Services, door te klikken op het tabblad met **Azure Lab Services** app binnen teams (SSO) of door toegang tot de [website van Labs](https://labs.azure.com). Gebruikers kunnen de virtuele machine vervolgens gebruiken voor het werk en het huis voer van de klasse.

> [!IMPORTANT]
> Azure Lab Services kunnen alleen worden gebruikt in teams als de Lab-accounts in dezelfde Tenant zijn gemaakt als teams.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Azure Lab Services-app toevoegen als een tabblad aan een team

Als eigenaar van een team kunt u **Azure Lab Services** app rechtstreeks toevoegen in uw teams-kanalen, waarna de app beschikbaar is voor iedereen in het team. Volg de onderstaande drie stappen:

1. Navigeer naar het teams kanaal waar u de app wilt toevoegen en selecteer **+** om een tabblad toe te voegen. 
1. Zoek **Azure Lab Services** via de opties op het tabblad en voeg deze app toe. 

    > [!NOTE]
    > Alleen team **eigenaren** kunnen Labs maken voor het team.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Een tabblad toevoegen":::
1. Selecteer een Lab Services-account, dat u wilt gebruiken voor het maken van Labs in dit team. 

    Azure Lab Services maakt gebruik van eenmalige aanmelding in de [Azure Lab Services-website](https://labs.azure.com) en haalt alle Lab-accounts op waartoe u toegang hebt. 

    De accounts die zich in dezelfde Tenant bevinden als teams en waarvoor u de toegang **eigenaar**, **bijdrager** of **Maker** hebt, worden weer gegeven. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Welkom bij als":::
1. Druk op **Opslaan** en het tabblad wordt toegevoegd aan het kanaal.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Het tabblad als is gemaakt":::

    Nu kunt u het tabblad **Azure Lab Services** selecteren vanuit uw kanaal en de laboratoria beheren, zoals beschreven in de volgende artikelen.

Nadat het lab-account is geselecteerd, kunnen team eigenaren Labs maken voor het team. Het hele Lab wordt gemaakt en alle taken op het niveau van de test omgeving kunnen worden uitgevoerd binnen teams. Gebruikers hebben de mogelijkheid om meerdere Labs te maken binnen hetzelfde team en de team eigenaar, met de juiste toegang op het niveau van het account Lab, maar alleen de laboratoria die aan het specifieke team zijn gekoppeld.

## <a name="next-steps"></a>Volgende stappen

Wanneer een lab in teams wordt gemaakt, wordt de gebruikers lijst in de test omgeving automatisch ingevuld en gesynchroniseerd met het team lidmaatschap. Iedereen in het team, met inbegrip van eigen aren, leden en gasten, wordt automatisch toegevoegd aan de gebruikers lijst van de test omgeving. Azure Lab-Services behouden een synchronisatie met het team lidmaatschap en een automatische synchronisatie wordt elke 24 uur geactiveerd. Zie deze artikelen voor meer informatie:

[Gebruikers lijsten van Lab-Services beheren in teams](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Zie ook

Zie ook de volgende artikelen:

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [De VM-groep van het lab in teams beheren](how-to-manage-vm-pool-within-teams.md)
- [Lab-schema's maken en beheren in teams](how-to-create-schedules-within-teams.md)
- [Toegang tot een virtuele machine in teams – student View](how-to-access-vm-for-students-within-teams.md)
- [Laboratoria verwijderen in teams](how-to-delete-lab-within-teams.md)
