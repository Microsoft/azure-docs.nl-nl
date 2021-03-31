---
title: Beheerde apps publiceren via de portal
description: Laat zien hoe u de Azure Portal kunt gebruiken om een door Azure beheerde toepassing te maken die is bedoeld voor leden van uw organisatie.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651720"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Een service catalogus toepassing publiceren via Azure Portal

U kunt de Azure Portal gebruiken voor het publiceren van [beheerde toepassingen](overview.md) die bedoeld zijn voor leden van uw organisatie. Zo kan een IT-afdeling beheerde toepassingen publiceren die zorgen voor naleving van organisatiestandaarden. Deze beheerde toepassingen zijn beschikbaar via de servicecatalogus, niet Azure Marketplace.

## <a name="prerequisites"></a>Vereisten

Wanneer u een beheerde toepassing publiceert, geeft u een identiteit op om de resources te beheren. U wordt aangeraden een Azure Active Directory gebruikers groep op te geven. Als u een Azure Active Directory gebruikers groep wilt maken, raadpleegt u [een groep maken en leden toevoegen in azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Het zip-bestand dat de definitie van de beheerde toepassing bevat, moet beschikbaar zijn via een URI. Het is raadzaam om uw zip-bestand te uploaden naar een opslag-blob. 

## <a name="create-managed-application-with-portal"></a>Een beheerde toepassing maken met de portal

1. Selecteer in de linkerbovenhoek **+ Nieuw**.

   ![Nieuwe service](./media/publish-portal/new.png)

1. Zoek naar de **Service catalogus**.

1. Schuif in de resultaten naar de definitie van de **beheerde toepassing voor de Service catalogus**. Selecteer deze.

   ![Zoeken naar definities van beheerde toepassingen](./media/publish-portal/select-managed-apps-definition.png)

1. Selecteer **maken** om het proces voor het maken van de definitie van de beheerde toepassing te starten.

   ![Definitie van beheerde toepassing maken](./media/publish-portal/create-definition.png)

1. Geef waarden op voor naam, weergave naam, beschrijving, locatie, abonnement en resource groep. Geef voor de bestands-URI van het pakket het pad op naar het zip-bestand dat u hebt gemaakt.

   ![Waarden opgeven](./media/publish-portal/fill-application-values.png)

1. In de sectie Verificatie en vergrendelingsniveau selecteert u **Autorisatie toevoegen**.

   ![Autorisatie toevoegen](./media/publish-portal/add-authorization.png)

1. Selecteer een Azure Active Directory-groep om resources te beheren en selecteer **OK**.

   ![Autorisatiegroep toevoegen](./media/publish-portal/add-auth-group.png)

1. Wanneer u alle waarden hebt ingevoerd, selecteert u **Maken**.

   ![Een beheerde toepassing maken](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van beheerde toepassingen](overview.md) voor een inleiding tot beheerde toepassingen.
* Zie [voorbeeld projecten voor door Azure beheerde toepassingen](sample-projects.md)voor voor beelden van beheerde toepassingen.
* Zie [Aan de slag met CreateUiDefinition](create-uidefinition-overview.md) voor meer informatie over het maken van een UI-definitiebestand voor een beheerde toepassing.