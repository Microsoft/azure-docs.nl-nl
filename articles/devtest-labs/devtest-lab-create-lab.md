---
title: Een lab maken in Azure DevTest Labs | Microsoft Docs
description: In dit artikel wordt stapsgewijs uitgelegd hoe u een Lab maakt met behulp van de Azure Portal en Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92058330"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Een lab maken in Azure DevTest Labs

Een lab in Azure DevTest Labs is de infrastructuur die een groep resources omvat, zoals VM's (virtuele machines), waarmee u deze resources beter kunt beheren door limieten en quota op te geven. In dit artikel wordt uitgelegd hoe u een lab maakt met behulp van Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u een lab wilt maken, hebt u het volgende nodig:

* Een Azure-abonnement. Zie [Azure aanschaffen](https://azure.microsoft.com/pricing/purchase-options/) of [Gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie over Azure-aankoopopties. U moet de eigenaar van het abonnement zijn om het lab te maken.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>In enkele minuten aan de slag met Azure DevTest Labs

Als u op de volgende koppeling klikt, wordt u overgezet naar de pagina Azure Portal waarmee u een nieuw lab in Azure DevTest Labs kunt gaan maken.

[In enkele minuten aan de slag met Azure DevTest Labs](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Instellingen voor het nieuwe account invullen

Vul op de pagina **een DevTest Labs maken** de volgende instellingen in.

> [!TIP]
> Onder aan elke pagina vindt u een koppeling waarmee u **een sjabloon voor automatisering kunt downloaden**.

### <a name="basic-settings"></a>Basisinstellingen

Standaard wordt het tabblad **basis instellingen** weer geven. 

![basis instellingen](./media/devtest-lab-create-lab/basic-settings.png)

Vul deze waarden in:

|Naam|Beschrijving|
|---|---|
|**Abonnement** | Vereist. Selecteer het **abonnement** dat u wilt koppelen aan het lab.|
|**Resourcegroep**| Vereist. Voer een **naam in voor de resource groep** voor het lab. Een nieuw item maken als er geen bestaat.|
|**Naam van Lab**| Vereist. Voer een **naam** in voor het lab.|
|**Locatie**|Vereist. Selecteer een locatie waar u het Lab wilt opslaan.|
|**Openbare omgevingen**| Zie [open bare omgevingen configureren en gebruiken](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Instellingen voor automatisch afsluiten

Schakel over naar de pagina **automatisch afsluiten** om de instellingen weer te geven. Met automatisch afsluiten kunt u automatisch alle machines in een Lab afsluiten op een gepland tijdstip per dag.

![Tabblad automatisch afsluiten](./media/devtest-lab-create-lab/auto-shutdown.png)

Op de pagina kunt u **automatisch afsluiten** inschakelen en de para meters definiëren voor het automatisch afsluiten van alle virtuele machines van het lab. De functie Auto-shutdown is voornamelijk een kostenbesparende functie waar u kunt opgeven wanneer de VM automatisch moet worden afgesloten. U kunt de instellingen voor automatisch afsluiten tijdens het maken van het lab wijzigen door de stappen te volgen die worden beschreven in het artikel [alle beleids regels beheren voor een lab in azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Netwerken

Wanneer u een Lab maakt, wordt er een standaard netwerk voor u gemaakt. Ga naar het tabblad **netwerken** en wijzig de instelling naar wens. Selecteer bijvoorbeeld een bestaand virtueel netwerk.

![Tabblad Netwerken ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Tags

Voer informatie over **Naam** en **Waarde** in voor **Tags** als u aangepaste tags wilt maken die worden toegevoegd aan elke resource die u in het lab maakt. Tags zijn handig om labresources te beheren en te organiseren per categorie. Zie [Tags toevoegen aan een lab](devtest-lab-add-tag.md) voor meer informatie over tags, inclusief het toevoegen van tags na het maken van het lab.

![Tabblad Labels ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Controleren en maken

Wanneer u klaar bent, selecteert u **maken**. U kunt de status van het proces voor het maken van het lab bewaken door het gebied **meldingen** rechtsboven op de portal-pagina te bekijken. 

![Tabblad maken](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Het maken is voltooid

Zodra dit is voltooid, wordt de knop **Ga naar resource** weer gegeven onder aan de pagina en in het meldings venster. U kunt ook de pagina **DevTest Labs** vernieuwen om de zojuist gemaakte Lab weer te geven in de lijst met Labs.  

![De service maken](./media/devtest-lab-create-lab/create-2.png)

Druk op de knop **Go to resource** en u gaat naar de start pagina van uw nieuwe DevTest Labs-account.

![Resource](./media/devtest-lab-create-lab/go-to-resource.png)

U kunt ook zoeken naar **DevTest Labs** in het Azure Portal. Selecteer uw nieuwe account in de lijst en ga naar de start pagina. 

![De service is gemaakt](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw lab hebt gemaakt, kunt u onder andere de volgende stappen uitvoeren:

* [Toegang tot een lab beveiligen](devtest-lab-add-devtest-user.md)
* [Lab-beleid instellen](devtest-lab-set-lab-policy.md)
* [Een Lab-sjabloon maken](devtest-lab-create-template.md)
* [Aangepaste artefacten maken voor uw virtuele machines](devtest-lab-artifact-author.md)
* [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)

