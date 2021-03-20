---
title: 'ML Studio (klassiek): een werk ruimte maken-Azure'
description: Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een Machine Learning Studio (klassieke) werk ruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520541"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Een Machine Learning Studio-werk ruimte (klassieke) maken en delen

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Als u Azure Machine Learning Studio (klassiek) wilt gebruiken, moet u een Machine Learning Studio (klassieke) werk ruimte hebben. Deze werkruimte bevat de hulpprogramma's die u nodig hebt om experimenten te maken, beheren en publiceren.

## <a name="create-a-studio-classic-workspace"></a>Een studio-werk ruimte maken

Als u een werk ruimte in Machine Learning Studio (klassiek) wilt openen, moet u zijn aangemeld bij het micro soft-account dat u hebt gebruikt voor het maken van de werk ruimte, of moet u een uitnodiging van de eigenaar ontvangen om deel te nemen aan de werk ruimte. Vanuit de Azure Portal kunt u de werk ruimte beheren, waaronder de mogelijkheid om toegang te configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)

    > [!NOTE]
    > Als u zich wilt aanmelden en een studio-werk ruimte wilt maken, moet u een beheerder van het Azure-abonnement zijn. 
    >
    > 

2. Klik op **+ Nieuw**

3. Typ in het zoekvak **machine learning Studio (klassieke) werk ruimte** en selecteer het overeenkomende item. Selecteer vervolgens onder aan de pagina op **maken** .

4. Voer uw werkruimte gegevens in:

   - De *naam van de werk ruimte* mag maxi maal 260 tekens bevatten en mag niet eindigen met een spatie. De naam mag niet de volgende tekens bevatten: `< > * % & : \ ? + /`
   - Het *webservice-abonnement* dat u kiest (of maakt), samen met de bijbehorende *prijs categorie* die u selecteert, wordt gebruikt als u webservices uit deze werk ruimte implementeert.

     ![Een nieuwe studio-werk ruimte maken](./media/create-workspace/create-new-workspace.png)

5. Klik op **Create**.

   Machine Learning is momenteel beschikbaar in een beperkt aantal regio's. Als uw abonnement niet een van deze regio's bevat, wordt het fout bericht ' u hebt geen abonnementen in de toegestane regio's ' weer gegeven.  Als u een regio wilt toevoegen aan uw abonnement, maakt u een nieuwe micro soft-ondersteunings aanvraag van de Azure Portal, kiest u **facturering** als het probleem type en volgt u de aanwijzingen om uw aanvraag in te dienen.


> [!NOTE]
> Machine Learning Studio (klassiek) is afhankelijk van een Azure Storage-account dat u opgeeft om tussenliggende gegevens op te slaan wanneer de werk stroom wordt uitgevoerd. Nadat de werk ruimte is gemaakt, wordt het opslag account verwijderd, of als de toegangs sleutels zijn gewijzigd, werkt de werk ruimte niet meer en mislukken alle experimenten in deze werk ruimte.
Als u het opslag account per ongeluk verwijdert, maakt u het opslag account opnieuw met dezelfde naam in dezelfde regio als het verwijderde opslag account en synchroniseert u de toegangs sleutel opnieuw. Als u de toegangssleutel voor een opslagaccount hebt gewijzigd, moet u de toegangssleutel in Azure Portal opnieuw synchroniseren in de werkruimte.

Nadat de werk ruimte is geïmplementeerd, kunt u deze openen in Machine Learning Studio (klassiek).

1. Blader naar Machine Learning Studio (klassiek) op [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Selecteer uw werkruimte in de rechterbovenhoek.

    ![Werkruimte selecteren](./media/create-workspace/open-workspace.png)

3. Klik op **mijn experimenten**.

    ![Experimenten openen](./media/create-workspace/my-experiments.png)

Voor informatie over het beheren van uw studio-werk ruimte (klassiek), Zie [een Azure machine learning Studio (klassieke) werk ruimte beheren](manage-workspace.md).
Als er een probleem optreedt bij het maken van uw werk ruimte, raadpleegt u de [hand leiding voor probleem oplossing: maken en verbinding maken met een machine learning Studio (klassieke) werk ruimte](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio-werk ruimte (klassieke) delen
Zodra een Machine Learning Studio (klassieke) werk ruimte is gemaakt, kunt u gebruikers uitnodigen voor uw werk ruimte om de toegang tot uw werk ruimte en alle experimenten, gegevens sets, enzovoort te delen. U kunt gebruikers toevoegen in een van de twee rollen:

* **Gebruiker** : een werkruimte gebruiker kan experimenten, gegevens sets, enzovoort, maken, openen, wijzigen en verwijderen in de werk ruimte.
* **Eigenaar** : een eigenaar kan gebruikers uitnodigen en verwijderen in de werk ruimte, naast wat een gebruiker kan doen.

> [!NOTE]
> Het beheerders account waarmee de werk ruimte wordt gemaakt, wordt automatisch toegevoegd aan de werk ruimte als eigenaar van de werk ruimte. Andere beheerders of gebruikers in dat abonnement hebben echter niet automatisch toegang tot de werk ruimte, maar u moet ze expliciet uitnodigen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Een studio-werk ruimte delen (klassiek)

1. Meld u aan bij Machine Learning Studio (klassiek) op [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Klik in het linkerdeel venster op **instellingen**

3. Klik op het tabblad **gebruikers**

4. Klik op **meer gebruikers uitnodigen** onder aan de pagina

    ![Studio-instellingen](./media/create-workspace/settings.png)

5. Voer een of meer e-mail adressen in. De gebruikers hebben een geldig Microsoft-account of een organisatie account (van Azure Active Directory) nodig.

6. Geef aan of u de gebruikers als eigenaar of gebruiker wilt toevoegen.

7. Klik op de knop voor het selectie vakje **OK** .

Elke gebruiker die u toevoegt, ontvangt een e-mail met instructies over hoe u zich aanmeldt bij de gedeelde werk ruimte.

> [!NOTE]
> Gebruikers kunnen alleen webservices in deze werk ruimte implementeren of beheren als ze een Inzender of beheerder zijn in het Azure-abonnement. 

## <a name="troubleshoot-storage-accounts"></a>Problemen met opslag accounts oplossen


De Machine Learning-service heeft een opslag account nodig om gegevens op te slaan. U kunt een bestaand opslag account gebruiken of u kunt een nieuw opslag account maken wanneer u de nieuwe Machine Learning Studio (klassieke) werk ruimte maakt (als u een quotum hebt om een nieuw opslag account te maken).

Nadat de nieuwe Machine Learning Studio (klassieke) werk ruimte is gemaakt, kunt u zich aanmelden bij Machine Learning Studio (klassiek) met behulp van de Microsoft-account die u hebt gebruikt om de werk ruimte te maken. Als het fout bericht ' werk ruimte niet gevonden ' (vergelijkbaar met de volgende scherm afbeelding) wordt weer gegeven, moet u de volgende stappen gebruiken om uw browser cookies te verwijderen.

![De werk ruimte is niet gevonden](media/troubleshooting-creating-ml-workspace/screen3.png)

**Browser cookies verwijderen**

1. Als u Internet Explorer gebruikt, klikt u op de knop **extra** in de rechter bovenhoek en selecteert u **Internet opties**.  

   ![Internetopties](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klik op het tabblad **Algemeen** op **verwijderen...**

   ![Tabblad Algemeen](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Controleer in het dialoog venster **Browse geschiedenis verwijderen** of **cookies en website gegevens** zijn geselecteerd en klik op **verwijderen**.

   ![Cookies verwijderen](media/troubleshooting-creating-ml-workspace/screen6.png)

Nadat de cookies zijn verwijderd, start u de browser opnieuw en gaat u naar de pagina [Microsoft Azure machine learning Studio (klassiek)](https://studio.azureml.net) . Wanneer u wordt gevraagd om een gebruikers naam en wacht woord, voert u de Microsoft-account die u hebt gebruikt om de werk ruimte te maken.


## <a name="next-steps"></a>Volgende stappen

Zie [een Azure machine learning Studio (klassieke) werk ruimte beheren](manage-workspace.md)voor meer informatie over het beheren van een werk ruimte.