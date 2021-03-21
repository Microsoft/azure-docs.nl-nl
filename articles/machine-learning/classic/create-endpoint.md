---
title: 'ML Studio (klassiek): webservice-eind punten maken-Azure'
description: Webservice-eind punten maken in Azure Machine Learning Studio (klassiek). Elk eind punt in de webservice wordt onafhankelijk aangepakt, beperkt en beheerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 36eb6a67aa2a8da80894c27937994afd4f263ecd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517107"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Eind punten maken voor geïmplementeerde Azure Machine Learning Studio (klassieke) webservices

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> In dit onderwerp worden technieken beschreven die van toepassing zijn op een **klassieke** machine learning-webservice.

Na de implementatie van een webservice wordt een standaardeindpunt voor de service gemaakt. Het standaardeindpunt kan worden aangeroepen met de API-sleutel. U kunt meer eind punten toevoegen met hun eigen sleutels vanuit de portal voor webservices.
Elk eind punt in de webservice wordt onafhankelijk aangepakt, beperkt en beheerd. Elk eind punt is een unieke URL met een autorisatie sleutel die u aan uw klanten kunt distribueren.

## <a name="add-endpoints-to-a-web-service"></a>Eind punten toevoegen aan een webservice

U kunt een eind punt toevoegen aan een webservice met behulp van de Azure Machine Learning Web Services-portal. Zodra het eind punt is gemaakt, kunt u het gebruiken via synchrone Api's, batch-Api's en Excel-werk bladen.

> [!NOTE]
> Als u extra eind punten aan de webservice hebt toegevoegd, kunt u het standaard eindpunt niet verwijderen.

1. Klik in Machine Learning Studio (klassiek) in de linker navigatie kolom op Web Services.
2. Klik onder aan het dash board van de webservice op **eind punten beheren**. De Azure Machine Learning Web Services-portal wordt geopend op de pagina eind punten voor de webservice.
3. Klik op **Nieuw**.
4. Typ een naam en beschrijving voor het nieuwe eind punt. Namen van eind punten moeten uit 24 tekens of minder bestaan en moeten bestaan uit kleine letters of cijfers. Selecteer het logboek registratie niveau en of voorbeeld gegevens zijn ingeschakeld. Zie [logboek registratie inschakelen voor machine learning-webservices](web-services-logging.md)voor meer informatie over logboek registratie.

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> Een webservice schalen door extra eind punten toe te voegen

Elke gepubliceerde webservice is standaard geconfigureerd voor de ondersteuning van 20 gelijktijdige aanvragen en kan Maxi maal 200 gelijktijdige aanvragen zijn. Azure Machine Learning Studio (klassiek) optimaliseert automatisch de instelling om de beste prestaties voor uw webservice te bieden en de waarde van de portal wordt genegeerd.

Als u van plan bent de API aan te roepen met een hogere belasting dan een maximum waarde voor gelijktijdige aanroepen van 200, moet u meerdere eind punten maken op dezelfde webservice. U kunt de belasting vervolgens wille keurig verdelen over al deze taken.

Het schalen van een webservice is een veelvoorkomende taak. Enkele redenen om te schalen zijn het ondersteunen van meer dan 200 gelijktijdige aanvragen, het verhogen van de beschik baarheid via meerdere eind punten of het bieden van afzonderlijke eind punten voor de webservice. U kunt de schaal verg Roten door extra eind punten voor dezelfde webservice toe te voegen via de [Azure machine learning-webservice](https://services.azureml.net/) Portal.

Houd er rekening mee dat het gebruik van een hoog gelijktijdigheids aantal nadelig kan zijn als u de API niet aanroept met een overeenkomend hoog aantal. U kunt sporadische time-outs en/of pieken in de latentie zien als u een relatief lage belasting voor een API hebt geconfigureerd voor hoge belasting.

De synchrone Api's worden doorgaans gebruikt in situaties waarin een lage latentie gewenst is. De latentie is hier de tijd die de API nodig heeft om één aanvraag te volt ooien en geen netwerk vertragingen. Stel dat u een API hebt met een latentie van 50 MS. Als u de beschik bare capaciteit met het hoge en maximum aantal gelijktijdige aanroepen = 20 volledig wilt gebruiken, moet u deze API 20 * 1000/50 = 400 keer per seconde aanroepen. Door dit verder uit te breiden, kunt u met een maximum aantal gelijktijdige aanroepen van 200 de API 4000 keer per seconde aanroepen, uitgaande van een latentie van 50 MS.

## <a name="next-steps"></a>Volgende stappen

[Een Azure machine learning-webservice gebruiken](consume-web-services.md).