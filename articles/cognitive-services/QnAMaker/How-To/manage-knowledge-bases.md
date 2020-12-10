---
title: Knowledge bases beheren-QnA Maker
description: Met QnA Maker kunt u uw kennis slagen beheren door toegang te bieden tot de instellingen en inhoud van de kennis basis.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938253"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Knowledge Base maken en instellingen beheren

Met QnA Maker kunt u uw kennis grondslagen beheren door toegang te bieden tot de kennis basis instellingen en gegevens bronnen.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/cognitive-services/) voordat u begint.
> * Een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) die in de Azure Portal is gemaakt. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

1. Meld u bij de [QnAMaker.ai](https://QnAMaker.ai)-portal met uw Azure-referenties.

1. Selecteer **Een knowledge base maken** in de QnA Maker-portal.

1. Sla op de pagina **Maken** **Stap 1** over als u uw QnA Maker-resource al hebt.

    Selecteer **Een QnA-service maken** als u de resource nog niet hebt gemaakt. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

    Ga terug naar de QnA Maker-portal, vernieuw de browserpagina en ga door naar **Stap 2** als u klaar bent met het maken van de resource in Azure Portal.

1. Selecteer in **Stap 3** uw Active Directory, abonnement en service (resource), en de taal voor alle knowledge bases die in de service worden gemaakt.

   ![Schermopname van het selecteren van een knowledge base in de QnA Maker-service](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. In **stap 3** moet u de naam van uw kennis database noemen `My Sample QnA KB` .

1. Configureer in **Stap 4** de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel uitpakken van meerdere paden in vanuit URL's, .pdf- of .docx-bestanden.**|Ingeschakeld|
    |**Standaardantwoordtekst**| `Quickstart - default answer not found.`|
    |**+ URL toevoegen**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Chit-chat**|**Professional** selecteren|

1. Selecteer in **stap 5**, **Uw KB maken**.

    Tijdens het extractieproces, dat enige tijd duurt, wordt het document gelezen en worden de vragen en antwoorden geïdentificeerd.

    Nadat QnA Maker de knowledge base heeft gemaakt, wordt de pagina **Knowledge Base** geopend. U kunt de inhoud van de knowledge base op deze pagina bewerken.

## <a name="edit-knowledge-base"></a>Knowledge base bewerken

1.  Selecteer **mijn Knowledge bases** in de bovenste navigatie balk.

       U kunt alle services zien die u hebt gemaakt of die met u zijn gedeeld, gesorteerd in aflopende volg orde van de **Laatst gewijzigd** op.

       ![Mijn Knowledge bases](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecteer een specifieke Knowledge Base om er wijzigingen aan te brengen.

1.  Selecteer **Instellingen**. De volgende lijst bevat velden die u kunt wijzigen.

       |Doel|Actie|
       |--|--|
       |URL toevoegen|U kunt nieuwe Url's toevoegen om nieuwe veelgestelde vragen over inhoud toe te voegen aan de Knowledge Base door te klikken op **Knowledge Base beheren-> koppeling + URL toevoegen** .|
       |URL verwijderen|U kunt bestaande Url's verwijderen door het pictogram verwijderen te selecteren, de prullenbak kan.|
       |Inhoud vernieuwen|Als u de meest recente inhoud van bestaande Url's door de Knowledge Base wilt laten verkennen, selecteert u het selectie vakje **vernieuwen** . Hiermee wordt de Knowledge Base één keer bijgewerkt met de nieuwste URL-inhoud. Dit is niet een regel matig schema van updates.|
       |Bestand toevoegen|U kunt een ondersteund bestands document toevoegen om deel uit te maken van een Knowledge Base door **Knowledge Base beheren** te selecteren en vervolgens **+ bestand toevoegen** te selecteren|
    |Importeren|U kunt ook een bestaande Knowledge Base importeren door de knop **kennis basis importeren** te selecteren. |
    |Bijwerken|Het bijwerken van de kennis basis is afhankelijk van de **prijs categorie voor beheer** die wordt gebruikt bij het maken van QnA Maker-service die aan uw Knowledge Base is gekoppeld. U kunt de beheer laag ook bijwerken vanuit Azure Portal, indien nodig.

  1. Wanneer u klaar bent met het aanbrengen van wijzigingen in de Knowledge Base, selecteert u **opslaan en trainen** in de rechter bovenhoek van de pagina om de wijzigingen te behouden.

       ![Opslaan en trainen](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Als u de pagina verlaat voordat u **opslaan en trainen** selecteert, gaan alle wijzigingen verloren.



## <a name="manage-large-knowledge-bases"></a>Grote kennis grondslagen beheren

* **Gegevens bron groepen**: de QnAs worden gegroepeerd op de gegevens bron van waaruit ze zijn geëxtraheerd. U kunt de gegevens bron uitvouwen of samen vouwen.

    ![Gebruik de QnA Maker gegevens bron balk om vragen en antwoorden over de gegevens bron samen te vouwen en uit te breiden](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Zoek Knowledge Base**: u kunt de Knowledge Base doorzoeken door in het tekstvak boven aan de Knowledge Base-tabel te typen. Klik op ENTER om te zoeken naar de vraag, het antwoord of de inhoud van meta gegevens. Klik op het pictogram X om het zoek filter te verwijderen.

    ![Gebruik het zoekvak van QnA Maker boven de vragen en antwoorden om de weer gave te verkleinen zodat alleen items kunnen worden gefilterd](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginering**: snel door gegevens bronnen bladeren om grote kennis slagen te beheren

    ![Gebruik de QnA Maker paginerings functies boven de vragen en antwoorden om door pagina's van vragen en antwoorden te bladeren](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Kennis grondslagen verwijderen

Het verwijderen van een Knowledge Base (KB) is een permanente bewerking. Deze kan niet ongedaan worden gemaakt. Voordat u een Knowledge Base verwijdert, moet u de Knowledge Base exporteren via de pagina **instellingen** van de QnA Maker Portal.

Als u uw Knowledge Base deelt met mede werkers,] (samen werken-kennis-base.md) vervolgens het item verwijdert, verliest iedereen de toegang tot de KB.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van de taal](../index.yml) van alle kennis grondslagen in een resource.

* QnA-paren bewerken
* Azure-resources beheren die worden gebruikt door QnA Maker