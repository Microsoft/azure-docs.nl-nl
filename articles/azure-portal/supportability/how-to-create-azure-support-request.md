---
title: Een ondersteuningsaanvraag maken voor Azure
description: Klanten die hulp nodig hebben, kunnen de Azure Portal gebruiken om self-service oplossingen te vinden en ondersteunings aanvragen te maken en te beheren.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 395fc0d5905389ffb8f68d8bbc4c6f8b3a871d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774865"
---
# <a name="create-an-azure-support-request"></a>Een Azure-ondersteuningsaanvraag maken

Met Azure kunt u ondersteunings aanvragen maken en beheren, ook wel ondersteunings tickets genoemd. U kunt aanvragen maken en beheren in de [Azure Portal](https://portal.azure.com), die in dit artikel worden besproken. U kunt ook programmatisch aanvragen maken en beheren met behulp van de [Azure-ondersteunings ticket rest API](/rest/api/support)of met behulp van [Azure cli](/cli/azure/azure-cli-support-request).

> [!NOTE]
> De Azure Portal-URL is specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd.
>
>* Azure Portal voor commercieel gebruik: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal voor Duitsland is: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal voor de Verenigde Staten overheid is: [https://portal.azure.us](https://portal.azure.us)

De ondersteunings verzoek ervaring is gericht op drie hoofd doelen:

* **Gestroomlijnd**: Maak ondersteuning en probleem oplossing eenvoudig om te ontdekken hoe u een ondersteunings aanvraag indient.
* **Geïntegreerd**: u kunt eenvoudig een ondersteunings aanvraag openen wanneer u een probleem met een Azure-resource oplost zonder de context te scha kelen.
* **Efficiënt**: Verzamel de belangrijkste informatie die uw ondersteunings technicus nodig heeft om uw probleem efficiënt op te lossen.

Azure biedt onbeperkte ondersteuning voor abonnements beheer, met inbegrip van facturering, quotum aanpassingen en account overdrachten. Voor technische ondersteuning hebt u een ondersteunings abonnement nodig. Zie [Ondersteuningsplannen vergelijken](https://azure.microsoft.com/support/plans) voor meer informatie.

## <a name="getting-started"></a>Aan de slag

U krijgt hulp en **ondersteuning** in de Azure Portal. Deze is beschikbaar via het Azure Portal menu, de globale koptekst of het resource menu voor een service. Voordat u een ondersteunings aanvraag kunt indienen, moet u de juiste machtigingen hebben.

### <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Als u een ondersteunings aanvraag wilt maken, moet u [eigenaar](../../role-based-access-control/built-in-roles.md#owner)of [bijdrager](../../role-based-access-control/built-in-roles.md#contributor) zijn of worden toegewezen aan de rol [Inzender voor ondersteunings aanvragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) op abonnements niveau. Als u een ondersteunings aanvraag wilt maken zonder abonnement, bijvoorbeeld een Azure Active Directory scenario, moet u een [beheerder](../../active-directory/roles/permissions-reference.md)zijn.

### <a name="go-to-help--support-from-the-global-header"></a>Ga naar Help en ondersteuning vanuit de Global header

Als u een ondersteunings aanvraag wilt starten vanaf elke locatie in de Azure Portal:

1. Selecteer de **?** Selecteer in de globale header Help en **ondersteuning**.

   ![Help en ondersteuning](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Selecteer **Nieuwe ondersteuningsaanvraag**. Volg de aanwijzingen om informatie over het probleem op te geven. We suggereren een aantal mogelijke oplossingen, verzamelen Details over het probleem en helpen u bij het indienen en bijhouden van de ondersteunings aanvraag.

   ![Nieuw ondersteuningsverzoek](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ga naar Help en ondersteuning vanuit een resource menu

Ga als volgt te werk om een ondersteunings aanvraag te starten in de context van de resource waarmee u momenteel werkt:

1. Klik in het menu resource, in de sectie **ondersteuning en probleem oplossing** , op **nieuwe ondersteunings aanvraag**.

   ![In de context](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Volg de aanwijzingen om ons informatie te geven over het probleem dat u hebt. Wanneer u het proces voor de ondersteunings aanvraag vanuit de resource start, zijn sommige opties vooraf voor u geselecteerd.

## <a name="create-a-support-request"></a>Een ondersteuningsaanvraag maken

U wordt begeleid bij een aantal stappen voor het verzamelen van informatie over uw probleem en het oplossen van problemen. Elke stap wordt beschreven in de volgende secties.

### <a name="basics"></a>Basisbeginselen

De eerste stap van het proces voor ondersteunings aanvragen verzamelt basis informatie over uw probleem en uw ondersteunings plan.

Op het tabblad **basis beginselen** van de **nieuwe ondersteunings aanvraag** gebruikt u de selecters om ons te laten weten wat het probleem is. Eerst identificeert u enkele algemene categorieën voor het probleem type en kiest u het gerelateerde abonnement. Selecteer de service, bijvoorbeeld **virtuele machine met Windows**. Selecteer de resource, zoals de naam van de virtuele machine. Beschrijf het probleem in uw eigen woorden en selecteer vervolgens **probleem type** en **probleem subtype** om specifiekere informatie te verkrijgen.

![Blade Grondbeginselen](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Oplossingen

Nadat u basis informatie hebt verzameld, worden oplossingen hierna weer gegeven die u zelf kunt proberen. In sommige gevallen kunnen we zelfs een snelle diagnose uitvoeren. Oplossingen worden geschreven door Azure-technici en zullen de meest voorkomende problemen oplossen.

### <a name="details"></a>Details

Vervolgens verzamelen we aanvullende informatie over het probleem. Door in deze stap uitgebreide en gedetailleerde informatie te verstrekken, kunnen we uw ondersteunings aanvraag naar de juiste Engineer routeren.

1. Laat ons, indien mogelijk, weten wanneer het probleem is gestart en alle stappen om het te reproduceren. U kunt een bestand, zoals een logboek bestand of uitvoer van diagnostische gegevens, uploaden. Zie [richt lijnen voor bestands upload](how-to-manage-azure-support-request.md#file-upload-guidelines)voor meer informatie over het uploaden van bestanden.

1. Nadat u alle informatie over het probleem hebt, kiest u hoe u ondersteuning kunt krijgen. Selecteer in de sectie **ondersteunings methode** de **ernst van de** impact. Het maximale Ernst niveau is afhankelijk van uw [ondersteunings plan](https://azure.microsoft.com/support/plans).

    De optie **Diagnostische gegevens delen** is standaard geselecteerd. Hierdoor kan ondersteuning voor Azure [Diagnostische gegevens](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) van uw Azure-resources verzamelen. In sommige gevallen is er een tweede vraag die niet standaard is geselecteerd, zoals het aanvragen van toegang tot het geheugen van een virtuele machine.

1. Geef uw voorkeurs methode voor contact personen, een goede tijd om contact met u op te nemen en uw ondersteunings taal.

1. Vul vervolgens de sectie **contact gegevens** in zodat we weten hoe ze contact met u kunnen opnemen.

### <a name="review--create"></a>Controleren en maken

Vul alle vereiste gegevens op elk tabblad in en selecteer vervolgens **controleren + maken**. Controleer de details die u gaat verzenden naar ondersteuning. Ga terug naar een tabblad om zo nodig een wijziging aan te brengen. Wanneer u hebt nagevraagd of het ondersteunings verzoek is voltooid, selecteert u **maken**.

Een ondersteunings technicus neemt contact met u op met de methode die u hebt opgegeven. Zie voor meer informatie over de eerste reactie tijden het [ondersteunings bereik en de reactie snelheid](https://azure.microsoft.com/support/plans/response/).


## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende video voor meer informatie over de ondersteunings opties voor Self-Help in Azure:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Volg deze koppelingen voor meer informatie:

* [Een ondersteunings aanvraag voor Azure beheren](how-to-manage-azure-support-request.md)
* [REST API voor Azure-ondersteuningstickets](/rest/api/support)
* [Stuur ons uw feedback en suggesties](https://feedback.azure.com/forums/266794-support-feedback)
* Neem contact met ons op [Twitter](https://twitter.com/azuresupport)
* Hulp krijgen van uw peers op de [pagina micro soft Q&een vraag](/answers/products/azure)
* Meer informatie in de [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq)