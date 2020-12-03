---
title: Azure Data Explorer scannen
description: Hier vindt u informatie over het scannen van Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 01a1ded570d20d175b5e8eadb3e6cc8556155a85
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553158"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure-Data Explorer registreren en controleren

In dit artikel wordt beschreven hoe u een Azure Data Explorer-account registreert in azure controle sfeer liggen en hoe u een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Data Explorer ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie instellen voor een scan

Er is slechts één manier om verificatie in te stellen voor Azure Data Explorer:

- Service-principal

### <a name="service-principal"></a>Service-principal

Als u Service-Principal-verificatie voor scans wilt gebruiken, kunt u een bestaande gebruiken of een nieuw account maken. 

> [!Note]
> Als u een nieuwe Service-Principal moet maken, volgt u deze stappen:
> 1. Navigeer naar [Azure Portal](https://portal.azure.com).
> 1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
> 1. Selecteer **App-registraties**.
> 1. Selecteer **+ nieuwe toepassing registreren**.
> 1. Voer een naam in voor de **toepassing** (de Service Principal Name).
> 1. Selecteer **alleen accounts in deze organisatie Directory**.
> 1. Voor omleidings-URI selecteert u **Web** en voert u de gewenste URL in. het hoeft niet echt of werk te zijn.
> 1. Selecteer vervolgens **Registreren**.

Het is vereist om de toepassings-ID en het geheim van de Service-Principal op te halen:

1. Navigeer naar uw Service-Principal in de [Azure Portal](https://portal.azure.com)
1. Kopieer de waarden van de **toepassings-id van de toepassing (client)** van het **overzicht** en het **client geheim** van **certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. **Instellingen > geheimen** selecteren
1. Selecteer **+ genereren/importeren** en voer de **naam** van uw keuze en **waarde** in als het **client geheim** van de Service-Principal
1. Selecteer **maken** om te volt ooien
1. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal voor het instellen van de scan

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>De Service-Principal toegang verlenen tot uw Azure Data Explorer-exemplaar

1. Navigeer naar de Azure Portal. Ga vervolgens naar uw Azure Data Explorer-exemplaar.

1. Voeg de Service-Principal toe aan de rol **AllDatabasesViewer** op het tabblad **machtigingen** , zoals wordt weer gegeven in de volgende scherm afbeelding.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Scherm opname voor het toevoegen van de Service-Principal in machtigingen" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Een Azure Data Explorer-account registreren

Ga als volgt te werk om een nieuw Azure Data Explorer-account (Kusto) in uw Data Catalog te registreren:

1. Navigeer naar uw controle sfeer liggen-account
1. **Bronnen** selecteren in de linkernavigatiebalk
1. Selecteer **Registreren**
1. Selecteer **Azure Data Explorer** bij **bronnen registreren**
1. Selecteer **Doorgaan**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="nieuwe gegevens bron registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure Data Explorer (Kusto))** :

1. Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslag account:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en het juiste cluster in de vervolg keuzelijst **cluster** .
   1. U kunt **ook hand matig invoeren** selecteren en een service-eind punt (URL) invoeren.
1. **Volt ooien** om de gegevens bron te registreren.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="bronnen opties registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure controle sfeer liggen Data Catalog](how-to-browse-catalog.md)
- [Zoek in de Azure controle sfeer liggen-Data Catalog](how-to-search-catalog.md)
