---
title: 'ML Studio (klassiek): Exporteer & uw gegevens te verwijderen-Azure'
description: In-product gegevens die zijn opgeslagen door Azure Machine Learning Studio (klassiek), kunnen worden geëxporteerd en verwijderd via de Azure Portal en ook via geverifieerde REST-Api's. Telemetriegegevens kunnen worden geopend via de Azure-privacybeleid. In dit artikel wordt beschreven hoe u dit kunt doen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: b2d797cc6cb45a76a61a235e7c4c66ac6f243580
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519861"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Gebruikers gegevens in het product exporteren en verwijderen uit Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)




U kunt gegevens uit het product die zijn opgeslagen door Azure Machine Learning Studio (klassiek) verwijderen of exporteren met behulp van de Azure Portal, de Studio-interface, de Power shell en de geverifieerde REST-Api's. In dit artikel leest u hoe. 

Telemetriegegevens kunnen worden geopend via de Azure-privacybeleid. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Welke soorten gebruikers gegevens worden in Studio (klassiek) verzameld?

Gebruikers gegevens voor deze service bestaan uit informatie over gebruikers die gemachtigd zijn voor toegang tot werk ruimten en telemetrie-records van gebruikers interacties met de service.

Er zijn twee soorten gebruikers gegevens in Machine Learning Studio (klassiek):
- **Persoonlijke account gegevens:** Account-Id's en e-mail adressen die zijn gekoppeld aan een account.
- **Klant gegevens:** Gegevens die u hebt geüpload om te analyseren.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio-account typen en hoe gegevens worden opgeslagen

Er zijn drie soorten accounts in Machine Learning Studio (klassiek). Het soort account dat u hebt, bepaalt hoe uw gegevens worden opgeslagen en hoe u deze kunt verwijderen of exporteren.

- Een **gast werkruimte** is een gratis, anoniem account. U meldt zich aan zonder referenties op te geven, zoals een e-mail adres of wacht woord.
    -  De gegevens worden verwijderd nadat de gast werkruimte is verlopen.
    - Gast gebruikers kunnen klant gegevens exporteren via de gebruikers interface, REST Api's of Power shell-pakket.
- Een **gratis werk ruimte** is een gratis account waarmee u zich aanmeldt met Microsoft-account referenties: een e-mail adres en wacht woord.
    - U kunt persoonlijke en klant gegevens exporteren en verwijderen, die onderworpen zijn aan de gegevens van het subject Rights (DSR)-aanvragen.
    - U kunt klant gegevens exporteren via de gebruikers interface, REST Api's of Power shell-pakket.
    - Voor gratis werk ruimten die geen Azure AD-accounts gebruiken, kunt u telemetrie exporteren met behulp van de privacy-Portal.
    - Wanneer u de werk ruimte verwijdert, verwijdert u alle persoonlijke klant gegevens.
- Een **standaard werkruimte** is een betaald account dat u hebt geopend met aanmeldings referenties.
    - U kunt persoonlijke en klant gegevens exporteren en verwijderen die zijn onderworpen aan DSR-aanvragen.
    - U kunt toegang krijgen tot gegevens via de Azure-privacybeleid
    - U kunt persoonlijke en klant gegevens exporteren via de gebruikers interface, REST Api's of Power shell-pakket
    - U kunt uw gegevens in de Azure Portal verwijderen.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Werkruimte gegevens verwijderen in Studio (klassiek) 

### <a name="delete-individual-assets"></a>Afzonderlijke assets verwijderen

Gebruikers kunnen assets in een werk ruimte verwijderen door ze te selecteren en vervolgens de knop verwijderen te selecteren.

![Assets in Machine Learning Studio verwijderen (klassiek)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Een hele werk ruimte verwijderen

Gebruikers kunnen ook hun hele werk ruimte verwijderen:
- Betaalde werk ruimte: verwijderen via de Azure Portal.
- Gratis werk ruimte: gebruik de knop verwijderen in het deel venster **instellingen** .

![Een gratis werk ruimte in Machine Learning Studio verwijderen (klassiek)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Studio-gegevens exporteren met Power shell
Met Power shell kunt u al uw gegevens exporteren naar een Verwissel bare indeling van Azure Machine Learning Studio (klassiek) met behulp van-opdrachten. Zie het artikel [Power shell-module voor Azure machine learning Studio (klassiek)](powershell-module.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor documentatie over webservices en de facturering van het toezeggings plan [Azure machine learning Studio (klassiek) rest API verwijzing](/rest/api/machinelearning/).