---
title: Hoe kunt u instellingen beheren? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Het beheren van instellingen, het maken van de werk ruimte, het delen van de werk ruimte en het beheren van de abonnements sleutel in Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895778"
---
# <a name="how-to-manage-settings"></a>Instellingen beheren

U kunt op de pagina aangepaste Translator-instellingen uw werk ruimte delen, uw abonnements sleutel voor Translator wijzigen en de werk ruimte verwijderen.

Toegang krijgen tot de pagina instellingen:

1. Meld u aan bij de portal voor [aangepaste vertalers](https://portal.customtranslator.azure.ai/) .
2. Klik in de aangepaste Vertaal Portal op het tandwiel pictogram in de zijbalk.

    ![Koppeling instellen](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Conversie abonnement koppelen

U moet beschikken over een Translator-abonnements sleutel die is gekoppeld aan uw werk ruimte om modellen te trainen of implementeren.

Als u geen abonnement hebt, volgt u de onderstaande stappen:

1. Abonneer u om een Translator-resource te maken. Volg [hoe u zich kunt registreren voor Translator](../translator-how-to-signup.md) om u te abonneren en een Translator-sleutel te verkrijgen.
2. Let op de sleutel voor uw Vertaal abonnement. Een van de Key1 of Key2 is acceptabel.
3. Ga terug naar de portal voor aangepaste vertalers.

## <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

1. Klik op de knop + werk ruimte maken in de zijbalk aangepaste vertaler.

    ![Nieuwe werk ruimte maken](media/how-to/create-new-workspace.png)

2. Voer in het dialoog venster de naam van de nieuwe werk ruimte in.
3. Klik op volgende.
4. Kies het type abonnement.
5. Selecteer de regio abonnement. De regio moet overeenkomen met de geselecteerde regio wanneer de Translator-resource sleutel is gemaakt.
6. Voer de sleutel voor uw Vertaal abonnement in en klik vervolgens op de knop Opslaan.

    ![Dialoog venster nieuwe werk ruimte maken](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Aangepaste Translator biedt geen ondersteuning voor het maken van een werk ruimte voor Translator Text-API resource (ook Azure-abonnements sleutel) die is gemaakt in het [ingeschakelde VNET](../../../api-management/api-management-using-with-vnet.md).

### <a name="modify-existing-key"></a>Bestaande sleutel wijzigen

1. Ga naar de pagina instellingen voor uw werk ruimte.
2. Klik op sleutel wijzigen

    ![Abonnements sleutel toevoegen](media/how-to/how-to-add-subscription-key.png)

3. Voer in het dialoog venster de sleutel voor uw Vertaal abonnement in en klik vervolgens op de knop Opslaan.

    ![Het dialoog venster abonnements sleutel toevoegen](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Uw werkruimte beheren

Een werk ruimte is een werk ruimte voor het opstellen en bouwen van uw aangepaste Vertaal systeem. Een werk ruimte kan meerdere projecten, modellen en documenten bevatten.

Als een ander deel van uw werk moet worden gedeeld met andere personen, kan het handig zijn om meerdere werk ruimten te maken.

## <a name="share-your-workspace"></a>Uw werk ruimte delen

In aangepaste vertaler kunt u uw werk ruimte delen met anderen, als een ander deel van uw werk moet worden gedeeld met andere personen.

1. Navigeer naar de pagina instellingen van de werk ruimte.
2. Klik op de knop personen toevoegen in de sectie instellingen voor delen.

    ![Werk ruimte delen](media/how-to/share-workspace.png)

3. Voer in het dialoog venster een door komma's gescheiden lijst met e-mail adressen in die met deze werk ruimte moeten worden gedeeld. Zorg ervoor dat u deelt met het e-mail adres dat wordt gebruikt om u aan te melden bij een aangepaste vertaler met. Selecteer vervolgens het juiste niveau van de machtiging voor delen en klik op de knop Opslaan.

    ![Dialoog venster voor delen van werk ruimte](media/how-to/share-workspace-dialog.png)

4. Als uw werk ruimte nog steeds de standaard naam mijn werk ruimte heeft, moet u deze wijzigen voordat u uw werk ruimte deelt.
5. Klik op opslaan.

## <a name="sharing-permissions"></a>Machtigingen voor delen

1. **Lezer:** Met een lezer in de werk ruimte kunnen alle gegevens in de werk ruimte worden weer gegeven.

2. **Redacteur:** Met een editor in de werk ruimte kunnen documenten worden toegevoegd, modellen worden getraind en documenten en projecten worden verwijderd. Ze kunnen een abonnements sleutel toevoegen, maar kunnen niet wijzigen met wie de werk ruimte wordt gedeeld, de werk ruimte verwijderen of de naam van de werk ruimte wijzigen.

3. **Eigenaar:** Een eigenaar heeft volledige machtigingen voor de werk ruimte.

## <a name="change-sharing-permission"></a>Machtiging voor delen wijzigen

Wanneer een werk ruimte wordt gedeeld, worden in de sectie instellingen delen alle e-mail adressen weer gegeven waarop deze werk ruimte is gedeeld. U kunt de bestaande machtiging voor delen voor elk e-mail adres wijzigen als u de eigenaar toegang tot de werk ruimte hebt.

1. In de sectie instellingen voor delen voor elk e-mail bericht wordt het huidige machtigings niveau weer gegeven.

2. Klik op het vervolg keuzemenu en selecteer het nieuwe machtigings niveau dat u wilt toewijzen aan het e-mail adres.

    ![Instellingen voor delen van machtigingen](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Uw werk ruimte vastmaken

Uw eerste gemaakte werk ruimte is standaard vastgemaakt. Telkens wanneer u zich aanmeldt, wordt uw vastgemaakte werk ruimte weer gegeven op site belasting. Als u veel werk ruimten hebt gemaakt en u er een van wilt maken wanneer u zich aanmeldt, moet u deze vastmaken.

1. Klik in de zijbalk op de naam van de werk ruimte die u wilt vastmaken.
2. Ga naar de pagina instellingen voor uw werk ruimte.
3. Klik op het speld pictogram.

    ![Werk ruimte vastmaken](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het maken van uw werk ruimte en projecten](workspace-and-project.md)