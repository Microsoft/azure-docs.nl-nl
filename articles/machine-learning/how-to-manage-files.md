---
title: Bestanden in uw werk ruimte maken en beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en beheren van bestanden in uw werk ruimte in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101344"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Bestanden in uw werk ruimte maken en beheren

Meer informatie over het maken en beheren van de bestanden in uw Azure Machine Learning-werk ruimte.  Deze bestanden worden opgeslagen in de standaard werkruimte opslag. Bestanden en mappen kunnen worden gedeeld met iemand anders met lees toegang tot de werk ruimte en kunnen worden gebruikt vanuit alle reken instanties in de werk ruimte.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Bestanden maken

Een nieuw bestand maken in de standaardmap ( `Users > yourname` ):

1. Open uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).
1. Selecteer aan de linkerkant **notitie blokken**.
1. Selecteer de **+** installatie kopie.
1. Selecteer de installatie kopie voor het  **nieuwe bestand maken** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Nieuw bestand maken":::

1. Geef het bestand een naam.
1. Selecteer een bestands type.
1. Selecteer **Maken**.

De notitie blokken en de meeste tekst bestands typen worden weer gegeven in de sectie voor beeld.  De meeste andere bestands typen hebben geen preview-versie.

Een nieuw bestand maken in een andere map:
1. Selecteer de '... ' aan het einde van de map
1. Selecteer **nieuw bestand maken**.

> [!IMPORTANT]
> Inhoud in notitie blokken en scripts kan mogelijk gegevens van uw sessies lezen en toegang krijgen tot gegevens zonder uw organisatie in Azure.  Laad alleen bestanden van vertrouwde bronnen. Zie [Aanbevolen procedures voor veilige code](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)voor meer informatie.

## <a name="manage-files-with-git"></a>Bestanden beheren met git

[Gebruik een compute instance-Terminal](how-to-access-terminal.md#git) om Git-opslag plaatsen te klonen en te beheren.

## <a name="clone-samples"></a>Voor beelden klonen

Uw werk ruimte bevat een map met notitie **blokken** die is ontworpen om u te helpen de SDK te verkennen en als voor beeld voor uw eigen machine learning-projecten te gebruiken.   enige deze notitie blokken in uw eigen map om ze uit te voeren en te bewerken.  

Zie [zelf studie: uw eerste ml-experiment maken](tutorial-1st-experiment-sdk-setup.md#azure)voor een voor beeld.

## <a name="share-files"></a>Bestanden delen

Kopieer en plak de URL om een bestand te delen.  Alleen andere gebruikers van de werk ruimte hebben toegang tot deze URL.  Meer informatie over [het verlenen van toegang tot uw werk ruimte](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Een bestand verwijderen

U *kunt* de voor **beelden van notitieblok** bestanden niet verwijderen.  Deze bestanden maken deel uit van de studio en worden bijgewerkt telkens wanneer een nieuwe SDK wordt gepubliceerd.  

U *kunt* op een van de volgende manieren bestanden verwijderen die in de sectie **bestanden** zijn gevonden:

* Selecteer in de Studio de **..** . aan het einde van een map of bestand.  Zorg ervoor dat u een ondersteunde browser (micro soft Edge, Chrome of Firefox) gebruikt.
* [Gebruik een Terminal](how-to-access-terminal.md) van een reken instantie in uw werk ruimte. De map **~/cloudfiles** is toegewezen aan opslag in uw opslag account voor werk ruimten.
* In Jupyter of Jjupyterlab met hun hulp middelen.
