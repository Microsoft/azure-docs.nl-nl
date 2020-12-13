---
title: Aanmelden bij de LUIS-portal
description: Als u een nieuwe gebruiker bent die zich aanmeldt bij de LUIS-Portal, wijkt de aanmeldings ervaring enigszins af op basis van uw huidige gebruikers account.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: b8382b76496976054ebb452e39866765d986ccbb
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368172"
---
# <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Gebruik dit artikel om aan de slag te gaan met de LUIS-Portal en een ontwerp bron te maken. Nadat u de stappen in dit artikel hebt voltooid, kunt u LUIS-apps maken en publiceren.

## <a name="access-the-portal"></a>Toegang tot de portal


1. Ga naar de [Luis-Portal](https://www.luis.ai)om aan de slag te gaan met Luis. Als u nog geen abonnement hebt, wordt u gevraagd om een [gratis account](https://azure.microsoft.com//free/cognitive-services/) te maken en terug te keren naar de portal.
2. Vernieuw de pagina om deze bij te werken met het zojuist gemaakte abonnement
3. Selecteer uw abonnement in de vervolg keuzelijst

    > [!div class="mx-imgBorder"]
    > ![abonnementen selecteren](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Als uw abonnement zich onder een andere Tenant bevindt, kunt u geen tenants van het bestaande venster meer wijzigen. U kunt de tenants wijzigen door dit venster te sluiten en de meest rechtse avatar te selecteren die uw initialen bevat in de bovenste balk. Klik op **een andere bron** voor het maken van een andere resource om het venster opnieuw te openen.

    > [!div class="mx-imgBorder"]
    > ![scha kelen tussen mappen](./media/migrate-authoring-key/switch-directories.png)

5. Als u een bestaande LUIS-ontwerp bron hebt die aan uw abonnement is gekoppeld, kiest u deze in de vervolg keuzelijst. U kunt alle toepassingen weer geven die zijn gemaakt onder deze ontwerp bron.
6. Als dat niet het geval is, klikt u op **een nieuwe ontwerp bron maken** onder aan deze modale.
7.  Geef bij het maken van een nieuwe ontwerpresource de volgende informatie op:

    > [!div class="mx-imgBorder"]
    > ![Nieuwe resource maken](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Tenant naam** : de Tenant waaraan uw Azure-abonnement is gekoppeld. U kunt geen tenants van het bestaande venster wijzigen. U kunt de tenants wijzigen door dit venster te sluiten en de avatar te selecteren in de rechter bovenhoek van het scherm met uw initialen. Selecteer **een andere bron** voor het ontwerpen van de eerste om het venster opnieuw te openen.
    * **Naam van de Azure-resource groep** : een aangepaste naam voor de resource groep die u in uw abonnement hebt gekozen. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer. Als u momenteel geen resource groep in uw abonnement hebt, mag u er geen in de LUIS-portal maken. Ga naar [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) om er nog een te maken, gaat u naar Luis om door te gaan met het aanmeldings proces.
    * **Azure-resource naam** : een aangepaste naam die u kiest, die wordt gebruikt als onderdeel van de URL voor uw ontwerp transacties. De resource naam mag alleen alfanumerieke tekens bevatten `-` en mag niet beginnen of eindigen met `-` . Als er andere symbolen in de naam zijn opgenomen, mislukt het maken van een resource.
    * **Locatie** : Kies voor het schrijven van uw toepassingen op een van de [drie ontwerp locaties](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) die momenteel worden ondersteund door Luis, waaronder: West-VS, Europa-West en Oost-Australië
    * **Prijs categorie** : standaard wordt de prijs categorie F0 authoring geselecteerd, omdat dit de aanbevolen instelling is. Maak een door de [klant beheerde sleutel](https://docs.microsoft.com/azure/cognitive-services/luis/luis-encryption-of-data-at-rest#customer-managed-keys-for-language-understanding) van de Azure portal als u op zoek bent naar een extra beveiligingslaag.
8. U bent nu aangemeld bij LUIS. U kunt nu beginnen met het maken van toepassingen.

## <a name="troubleshooting"></a>Problemen oplossen

* Wanneer u een nieuwe resource maakt, moet u ervoor zorgen dat de resource naam alleen alfanumerieke tekens,-bevat en niet kan beginnen of eindigen met '-'. Als dat niet het geval is, mislukt de fout.
* Zorg ervoor dat u over de [juiste machtigingen voor uw abonnement beschikt om een Azure-resource te maken](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Als u niet over de juiste machtigingen beschikt, neemt u contact op met de beheerder van uw abonnement om u voldoende machtigingen te geven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [starten van een nieuwe app](luis-how-to-start-new-app.md)
