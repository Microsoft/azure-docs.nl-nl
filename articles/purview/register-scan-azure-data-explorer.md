---
title: Azure Data Explorer scannen
description: Hier vindt u informatie over het scannen van Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896104"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure-Data Explorer registreren en controleren

In dit artikel wordt beschreven hoe u een Azure Data Explorer-account registreert in azure controle sfeer liggen en hoe u een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Data Explorer ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Er is slechts één manier om verificatie in te stellen voor Azure Data Explorer:

- Service-principal

### <a name="service-principal"></a>Service-principal

Als u Service-Principal-verificatie voor scans wilt gebruiken, kunt u een bestaande gebruiken of een nieuw account maken. 

> [!Note]
> Als u een nieuwe service-principal moet maken, volgt u deze stappen:
> 1. Navigeer naar [Azure Portal](https://portal.azure.com).
> 1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
> 1. Selecteer **App-registraties**.
> 1. Selecteer **+ Nieuwe toepassing registreren**.
> 1. Voer een naam in voor de **toepassing** (de service-principal-naam).
> 1. Selecteer **Alleen accounts in deze organisatiemap**.
> 1. Bij Omleidings-URI selecteert u **Web** en voert u de gewenste URL in. Dit hoeft geen echte of werkende URL te zijn.
> 1. Selecteer vervolgens **Registreren**.

Het is vereist om de toepassings-ID en het geheim van de Service-Principal op te halen:

1. Navigeer naar uw service-principal in [Azure Portal](https://portal.azure.com)
1. Kopieer de waarde van de **Toepassings-id (client)** uit **Overzicht** en van **Clientgeheim** uit **Certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **Naam** van uw keuze in en de **Waarde** als het **Clientgeheim** van uw service-principal
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal om uw scan in te stellen

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>De Service-Principal toegang verlenen tot uw Azure Data Explorer-exemplaar

1. Navigeer naar de Azure Portal. Ga vervolgens naar uw Azure Data Explorer-exemplaar.

1. Voeg de Service-Principal toe aan de rol **AllDatabasesViewer** op het tabblad **machtigingen** , zoals wordt weer gegeven in de volgende scherm afbeelding.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Scherm opname voor het toevoegen van de Service-Principal in machtigingen" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Een Azure Data Explorer-account registreren

Ga als volgt te werk om een nieuw Azure Data Explorer-account (Kusto) in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Selecteer **Azure Data Explorer** bij **bronnen registreren**
1. Selecteer **Doorgaan**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="nieuwe gegevensbron registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure Data Explorer (Kusto))** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslagaccount:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en het juiste cluster in de vervolg keuzelijst **cluster** .
   1. U kunt **ook hand matig invoeren** selecteren en een service-eind punt (URL) invoeren.
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
