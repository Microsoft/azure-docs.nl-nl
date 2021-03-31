---
title: Azure files scannen
description: Hier vindt u informatie over het scannen van Azure-bestanden.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553121"
---
# <a name="register-and-scan-azure-files"></a>Azure Files registreren en scannen

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Files ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het Toep assen van classificaties op de meta gegevens op basis van systeem-en klant classificaties.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een gegevens bron beheerder zijn om scans in te stellen en te plannen. Raadpleeg de [catalogus machtigingen](catalog-permissions.md) voor meer informatie.

## <a name="register-an-azure-files-storage-account"></a>Een Azure Files Storage-account registreren

Ga als volgt te werk om een nieuw Azure Files-account in uw Data Catalog te registreren:

1. Navigeer naar uw controle sfeer liggen-Data Catalog.
1. Selecteer **beheer centrum** in het navigatie venster aan de linkerkant.
1. Selecteer **gegevens bronnen** onder **bronnen en scannen**.
1. Selecteer **+ Nieuw**.
1. Selecteer **Azure files** bij **bron registreren**. Selecteer **Doorgaan**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="nieuwe gegevensbron registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure files)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslagaccount:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en het betreffende opslag account in de vervolg keuzelijst **opslag account naam** .
   1. U kunt **ook hand matig invoeren** selecteren en een service-eind punt (URL) invoeren.
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Ga als volgt te werk om verificatie in te stellen voor Azure Files opslag met behulp van een account sleutel:

1. Selecteer de verificatie methode als **account sleutel**.
2. Selecteer een optie **in het Azure-abonnement** .
3. Kies uw Azure-abonnement waar het Azure Files-account bestaat.
4. Kies de naam van uw opslag account in de lijst.
5. Klik op **Voltooien**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)