---
title: Data Lake Storage Gen1 integreren met Azure Data Catalog
description: Meer informatie over het registreren van gegevens van Azure Data Lake Storage Gen1 in Azure Data Catalog om gegevens detecteerbaar te maken in uw organisatie.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106617"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Gegevens van Azure Data Lake Storage Gen1 in Azure Data Catalog registreren
In dit artikel leert u hoe u Azure Data Lake Storage Gen1 integreert met Azure Data Catalog, zodat uw gegevens binnen een organisatie kunnen worden gedetecteerd door deze te integreren met Data Catalog. Zie [Azure Data Catalog](../data-catalog/overview.md)voor meer informatie over het catalogiseren van gegevens. Zie [Azure Data Catalog algemene scenario's](../data-catalog/data-catalog-common-scenarios.md)voor meer informatie over scenario's waarin u Data Catalog kunt gebruiken.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Schakel uw Azure-abonnement in** voor data Lake Storage gen1. Zie de [instructies](data-lake-store-get-started-portal.md).
* **Een Data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md). Maak voor deze zelf studie een Data Lake Storage Gen1 account met de naam **datacatalogstore**.

    Nadat u het account hebt gemaakt, uploadt u een set met voorbeeld gegevens. Voor deze zelf studie kunt u alle CSV-bestanden in de map **AmbulanceData** in de Git- [opslag plaats van Azure data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/)uploaden. U kunt verschillende clients, zoals [Azure Storage Explorer](https://storageexplorer.com/), gebruiken om gegevens te uploaden naar een BLOB-container.
* **Azure Data Catalog**. Uw organisatie moet al een Azure Data Catalog hebben gemaakt voor uw organisatie. Er is slechts één catalogus toegestaan voor elke organisatie.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Data Lake Storage Gen1 registreren als bron voor Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Ga naar `https://azure.microsoft.com/services/data-catalog` en klik op **aan de slag**.
1. Meld u aan bij de portal van Azure Data Catalog en klik op **Gegevens publiceren**.

    ![Een gegevensbron registreren](./media/data-lake-store-with-data-catalog/register-data-source.png "Een gegevensbron registreren")
1. Klik op de volgende pagina op **toepassing starten**. Hiermee wordt het manifest bestand van de toepassing op uw computer gedownload. Dubbel klik op het manifest bestand om de toepassing te starten.
1. Klik op de pagina Welkom op **Aanmelden** en voer uw referenties in.

    ![Welkomstscherm](./media/data-lake-store-with-data-catalog/welcome.screen.png "Welkomstscherm")
1. Selecteer **Azure data Lake Store** op de pagina een gegevens bron selecteren en klik vervolgens op **volgende**.

    ![Gegevens bron selecteren](./media/data-lake-store-with-data-catalog/select-source.png "Gegevensbron selecteren")
1. Geef op de volgende pagina de Data Lake Storage Gen1-account naam op die u wilt registreren in Data Catalog. Wijzig de andere opties als standaard en klik vervolgens op **verbinding maken**.

    ![Verbinding maken met gegevensbron](./media/data-lake-store-with-data-catalog/connect-to-source.png "Verbinding maken met gegevensbron")
1. De volgende pagina kan worden onderverdeeld in de volgende segmenten.

    a. Het vak **Server hiërarchie** vertegenwoordigt de mapstructuur van het data Lake Storage gen1-account. **$Root** vertegenwoordigt de hoofdmap van het data Lake Storage gen1 account en **AmbulanceData** vertegenwoordigt de map die is gemaakt in de hoofdmap van het data Lake Storage gen1-account.

    b. In het vak **beschik bare objecten** worden de bestanden en mappen in de map **AmbulanceData** weer gegeven.

    c. In het vak objecten die moeten **worden geregistreerd** worden de bestanden en mappen vermeld die u wilt registreren in azure Data Catalog.

    ![Scherm afbeelding van het dialoog venster Microsoft Azure Data Catalog-opslag account.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Gegevens structuur weer geven")
1. Voor deze zelf studie moet u alle bestanden in de map registreren. Klik hiervoor op de knop (![objecten verplaatsen](./media/data-lake-store-with-data-catalog/move-objects.png "Objecten verplaatsen")) om alle bestanden te verplaatsen naar objecten die moeten **worden geregistreerd** .

    Omdat de gegevens worden geregistreerd in een organisatie-brede Data Catalog, is het een aanbevolen benadering om enkele meta gegevens toe te voegen die u later kunt gebruiken om snel de gegevens te vinden. U kunt bijvoorbeeld een e-mail adres toevoegen voor de gegevens eigenaar (bijvoorbeeld een persoon die de gegevens uploadt) of een tag toevoegen om de gegevens te identificeren. In de onderstaande scherm afbeelding ziet u een label die u aan de gegevens toevoegt.

    ![Scherm afbeelding van het dialoog venster Microsoft Azure Data Catalog opslag account met het label dat is toegevoegd aan de gegevens.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Gegevens structuur weer geven")

    Klik op **Registreren**.
1. In de volgende scherm opname wordt aangegeven dat de gegevens zijn geregistreerd in de Data Catalog.

    ![Registratie is voltooid](./media/data-lake-store-with-data-catalog/registration-complete.png "Gegevens structuur weer geven")
1. Klik op **Portal weer geven** om terug te gaan naar de Data Catalog Portal en controleer of u nu toegang hebt tot de geregistreerde gegevens vanuit de portal. Als u de gegevens wilt doorzoeken, kunt u de tag gebruiken die u hebt gebruikt bij het registreren van de gegevens.

     ![Gegevens zoeken in catalogus](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Gegevens zoeken in catalogus")
1. U kunt nu bewerkingen uitvoeren zoals aantekeningen en documentatie toevoegen aan de gegevens. Zie de volgende koppelingen voor meer informatie.

    * [Aantekeningen toevoegen aan gegevens bronnen in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Gegevens bronnen documenteren in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Zie ook
* [Aantekeningen toevoegen aan gegevens bronnen in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Gegevens bronnen documenteren in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Storage Gen1 integreren met andere Azure-Services](data-lake-store-integrate-with-other-services.md)