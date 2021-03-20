---
title: Een interne ondersteunings verklaring toevoegen aan een lab in Azure DevTest Labs
description: Meer informatie over het plaatsen van een interne ondersteunings verklaring voor een lab in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480470"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Een interne ondersteunings verklaring toevoegen aan een lab in Azure DevTest Labs

Met Azure DevTest Labs kunt u uw Lab aanpassen met een interne ondersteunings verklaring die gebruikers ondersteuning biedt voor informatie over het lab. U kunt bijvoorbeeld contact gegevens opgeven, zodat een gebruiker weet hoe interne ondersteuning kan worden bereikt wanneer deze hulp nodig heeft bij het oplossen van problemen of het openen van resources in het lab. U kunt ook koppelingen naar interne websites of veelgestelde vragen over uw team opgeven voordat u contact opneemt met de ondersteuning.

Een interne ondersteunings verklaring is bedoeld voor het plaatsen van Lab-informatie die doorgaans niet te vaak verandert. Om gebruikers op de hoogte te stellen van Lab-informatie met meer tijdelijke aard, zoals recente updates voor het lab-beleid, Zie [aankondiging publiceren in een Lab](devtest-lab-announcements.md).

U kunt een ondersteunings verklaring eenvoudig uitschakelen of bewerken nadat deze niet meer van toepassing is.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Stappen om een ondersteunings instructie toe te voegen aan een bestaand Lab

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **alle services** en selecteer vervolgens **DevTest Labs** in de lijst. (Uw Lab wordt mogelijk al weer gegeven op het dash board onder **alle resources**).
1. Selecteer in de lijst met Labs het lab waarin u een ondersteunings verklaring wilt toevoegen.  
1. Selecteer in het gedeelte **overzicht** van het lab de optie **configuratie en beleid**.  

    ![Knop configuratie en beleid](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Selecteer aan de linkerkant onder **instellingen** de optie **interne ondersteuning**.

    ![Knop interne ondersteuning](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Stel ingeschakeld in op **Ja** als u een intern ondersteunings bericht wilt maken voor de gebruikers in dit lab.

1. Voer in het veld **ondersteunings bericht** de interne ondersteunings verklaring in die u wilt presen teren aan uw test gebruikers. Het ondersteunings bericht accepteert de prijs verlaging. Wanneer u de bericht tekst invoert, kunt u het **voorbeeld** gebied onder aan het scherm weer geven om te zien hoe het bericht voor gebruikers wordt weer gegeven.

    ![Interne ondersteuning scherm voor het maken van het bericht.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecteer **Opslaan** zodra uw ondersteunings verklaring gereed is om te worden geplaatst.

Als u dit ondersteunings bericht niet meer wilt weer geven voor gebruikers met een lab, keert u terug naar de pagina **interne ondersteuning** en **stelt u in op** **Nee**.

## <a name="steps-for-users-to-view-the-support-message"></a>Stappen voor het weer geven van het ondersteunings bericht in gebruikers

1. Selecteer een lab in het [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **interne ondersteuning** in het **overzichts** gebied van het lab.  

    ![Knop interne ondersteuning](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Als er een ondersteunings bericht wordt geplaatst, kan de gebruiker het weer geven in het deel venster interne ondersteuning.

    ![Het deel venster interne ondersteuning met het ondersteunings bericht dat wordt gepost](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* Interne ondersteunings verklaringen worden meestal gebruikt om ondersteunings informatie te bieden die u niet regel matig wijzigt. U kunt ook leren hoe u [een aankondiging kunt plaatsen in een Lab](devtest-lab-announcements.md) om gebruikers te informeren over tijdelijke wijzigingen of updates voor het lab.
* [Beleid en Schema's instellen](devtest-lab-set-lab-policy.md) biedt informatie over hoe u andere beperkingen en conventies voor uw abonnement kunt Toep assen met behulp van aangepast beleid.