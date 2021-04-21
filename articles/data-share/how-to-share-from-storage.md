---
title: Gegevens delen en ontvangen van Azure Blob Storage en Azure Data Lake Storage
description: Meer informatie over het delen en ontvangen van gegevens van Azure Blob Storage en Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819264"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Gegevens delen en ontvangen van Azure Blob Storage en Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share biedt ondersteuning voor delen op basis van momentopnamen vanuit een opslagaccount. In dit artikel wordt uitgelegd hoe u gegevens kunt delen en ontvangen van Azure Blob Storage, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2.

Azure Data Share ondersteunt het delen van bestanden, mappen en bestandssystemen van Azure Data Lake Gen1 en Azure Data Lake Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers vanuit Azure Blob Storage. U kunt blok-, toevoegen- of pagina-blobs delen en ze worden ontvangen als blok-blobs. Gegevens die vanuit deze bronnen worden gedeeld, kunnen worden ontvangen door Azure Data Lake Gen2 of Azure Blob Storage.

Wanneer bestandssystemen, containers of mappen worden gedeeld in delen op basis van momentopnamen, kunnen gegevensverbruikers ervoor kiezen om een volledige kopie van de sharegegevens te maken. Of ze kunnen de mogelijkheid voor incrementele momentopnamen gebruiken om alleen nieuwe of bijgewerkte bestanden te kopiëren. De mogelijkheid voor incrementele momentopnamen is gebaseerd op het tijdstip waarop de bestanden het laatst zijn gewijzigd. 

Bestaande bestanden met dezelfde naam worden overschreven tijdens een momentopname. Een bestand dat uit de bron wordt verwijderd, wordt niet verwijderd op het doel. Lege submappen bij de bron worden niet gekopieerd naar het doel. 

## <a name="share-data"></a>Gegevens delen

Gebruik de informatie in de volgende secties om gegevens te delen met behulp van Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Vereisten voor het delen van gegevens

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Zoek het e-mailadres voor aanmelden bij Azure van de ontvanger. De e-mailalias van de ontvanger werkt niet voor uw doeleinden.
* Als het Azure-brongegevensopslag zich in een ander Azure-abonnement bevindt dan het abonnement waarin u de Data Share-resource maakt, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="prerequisites-for-the-source-storage-account"></a>Vereisten voor het bronopslagaccount

* Een Azure Storage-account. Als u nog geen account hebt, maakt [u er een.](../storage/common/storage-account-create.md)
* Machtiging om naar het opslagaccount te schrijven. Schrijfmachtiging is *in Microsoft.Storage/storageAccounts/write.* Het maakt deel uit van de rol Inzender.
* Machtiging voor het toevoegen van roltoewijzing aan het opslagaccount. Deze machtiging is in *Microsoft.Authorization/role assignments/write.* Deze maakt deel uit van de rol Eigenaar. 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Een Data Share maken

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Open het menu in de linkerbovenhoek van de portal en selecteer **vervolgens Een resource maken** (+).

1. Zoek naar *Data Share*.

1. Selecteer **Data Share** en **Maken.**

1. Geef de basisgegevens van uw Azure Data Share op: 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer een Azure-abonnement voor uw data share-account.|
    | Resourcegroep | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een resourcegroep. |
    | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
    | Naam | *datashareaccount* | Noem uw data share-account. |
    | | |

1. Selecteer **Beoordelen en maken om**  >  **uw** data share-account in terichten. Het inrichten van een nieuw data share-account duurt doorgaans ongeveer twee minuten. 

1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

### <a name="create-a-share"></a>Een share maken

1. Ga naar de overzichtspagina van **uw gegevens** delen.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Schermopname van het overzicht van de gegevens delen.":::

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Geef de details voor uw share op. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![Schermopname met details van de gegevens delen.](./media/enter-share-details.png "Voer de details van de gegevens delen in.") 

1. Selecteer **Doorgaan**.

1. Als u gegevenssets wilt toevoegen aan uw share, **selecteert u Gegevenssets toevoegen.** 

    ![Schermopname die laat zien hoe u gegevenssets toevoegt aan uw share.](./media/datasets.png "Datasets.")

1. Selecteer een gegevenssettype dat u wilt toevoegen. De lijst met typen gegevenssets is afhankelijk van of u in de vorige stap delen op basis van momentopnamen of in-place delen hebt geselecteerd. 

    ![Schermopname die laat zien waar u een gegevenssettype selecteert.](./media/add-datasets.png "Gegevenssets toevoegen.")    

1. Ga naar het object dat u wilt delen. Selecteer vervolgens **Gegevenssets toevoegen.** 

    ![Schermopname die laat zien hoe u een object selecteert dat u wilt delen.](./media/select-datasets.png "Selecteer gegevenssets.")    

1. Voeg op **het tabblad Ontvangers** het e-mailadres van uw gegevens consumer toe door Ontvanger toevoegen te **selecteren.** 

    ![Schermopname die laat zien hoe u e-mailadressen van ontvangers toevoegt.](./media/add-recipient.png "Voeg ontvangers toe.") 

1. Selecteer **Doorgaan**.

1. Als u een momentopname-sharetype hebt geselecteerd, kunt u het schema voor momentopnamen instellen om uw gegevens voor de gegevens consumer bij te werken. 

    ![Schermopname met de schema-instellingen voor momentopnamen.](./media/enable-snapshots.png "Momentopnamen inschakelen.") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op **het tabblad Beoordelen** en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van het pakket. Selecteer vervolgens **Maken**.

U hebt nu uw Azure-gegevens share gemaakt. De ontvanger van uw gegevens share kan uw uitnodiging accepteren. 

## <a name="receive-data"></a>Gegevens ontvangen

In de volgende secties wordt beschreven hoe u gedeelde gegevens ontvangt.
### <a name="prerequisites-to-receive-data"></a>Vereisten voor het ontvangen van gegevens
Voordat u een data share-uitnodiging accepteert, moet u ervoor zorgen dat u aan de volgende vereisten hebt: 

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account.](https://azure.microsoft.com/free/)
* Een uitnodiging van Azure. Het e-mailonderwerp moet 'Azure Data Share uitnodiging van *\<yourdataprovider\@domain.com>* ' zijn.
* Een geregistreerde [Microsoft.DataShare-resourceprovider](concepts-roles-permissions.md#resource-provider-registration) in:
    * Het Azure-abonnement waarin u een Data Share maken.
    * Het Azure-abonnement waarin uw Azure-doelgegevensopslag zich bevindt.

### <a name="prerequisites-for-a-target-storage-account"></a>Vereisten voor een doelopslagaccount

* Een Azure Storage-account. Als u er nog geen hebt, maakt [u een account](../storage/common/storage-account-create.md). 
* Machtiging om naar het opslagaccount te schrijven. Deze machtiging is in *Microsoft.Storage/storageAccounts/write.* Het maakt deel uit van de rol Inzender. 
* Machtiging voor het toevoegen van roltoewijzing aan het opslagaccount. Deze toewijzing staat in *Microsoft.Authorization/role assignments/write.* Deze maakt deel uit van de rol Eigenaar.  

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Een uitnodiging openen

U kunt een uitnodiging openen via e-mail of rechtstreeks vanuit Azure Portal.

1. Als u een uitnodiging wilt openen via e-mail, controleert u uw postvak IN op een uitnodiging van uw gegevensprovider. De uitnodiging van Microsoft Azure heeft de titel 'Azure Data Share uitnodiging van *\<yourdataprovider\@domain.com>* '. Selecteer **Uitnodiging weergeven om** uw uitnodiging in Azure te bekijken. 

   Als u een uitnodiging wilt openen vanuit Azure Portal, zoekt u naar *Data Share uitnodigingen*. U ziet een lijst met Data Share uitnodigingen.

   ![Schermopname van de lijst met uitnodigingen in de Azure Portal.](./media/invitations.png "Lijst met uitnodigingen.") 

1. Selecteer de share die u wilt weergeven. 

### <a name="accept-an-invitation"></a>Een uitnodiging accepteren
1. Bekijk alle velden, inclusief de **Gebruiksrechtovereenkomst**. Als u akkoord gaat met de voorwaarden, selecteert u het selectievakje. 

   ![Schermopname van het Gebruiksrechtovereenkomst gebied.](./media/terms-of-use.png "Gebruiksrechtovereenkomst.") 

1. Selecteer **onder Data Share doelaccount** het abonnement en de resourcegroep waar u uw Data Share. Vul vervolgens de volgende velden in:

   * Selecteer in **het veld Data share-account** de optie **Nieuwe** maken als u geen Data Share hebt. Anders selecteert u een bestaand Data Share account dat uw gegevens share accepteert. 

   * Laat in **het veld Ontvangen sharenaam** de standaardwaarde staan die de gegevensprovider heeft opgegeven of geef een nieuwe naam op voor de ontvangen share. 

1. Selecteer **Accepteren en configureren.** Er wordt een shareabonnement gemaakt. 

   ![Schermopname die laat zien waar de configuratieopties moeten worden geaccepteerd.](./media/accept-options.png "Opties voor accepteren") 

    De ontvangen share wordt weergegeven in uw Data Share account. 

    Als u de uitnodiging niet wilt accepteren, selecteert u **Weigeren.** 

### <a name="configure-a-received-share"></a>Een ontvangen share configureren
Volg de stappen in deze sectie om een locatie te configureren voor het ontvangen van gegevens.

1. Schakel op **het tabblad Gegevenssets** het selectievakje in naast de gegevensset waar u een bestemming wilt toewijzen. Selecteer **Kaart aan doel om** een doelgegevensopslag te kiezen. 

   ![Schermopname die laat zien hoe u aan een doel kunt toevoegen.](./media/dataset-map-target.png "Wijs toe aan het doel.") 

1. Selecteer een doelgegevensopslag voor de gegevens. Bestanden in het doelgegevensopslag met hetzelfde pad en dezelfde naam als bestanden in de ontvangen gegevens worden overschreven. 

   ![Schermopname die laat zien waar u een doelopslagaccount kunt selecteren.](./media/map-target.png "Doelopslag.") 

1. Als de gegevensprovider voor delen op basis van momentopnamen een schema voor momentopnamen gebruikt om de gegevens regelmatig bij te werken, kunt u de planning inschakelen op het tabblad Schema voor **momentopnamen.** Selecteer het vakje naast het schema voor momentopnamen. Selecteer vervolgens **Inschakelen.** Houd er rekening mee dat de eerste geplande momentopname binnen één minuut na de geplande tijd start en dat volgende momentopnamen binnen enkele seconden na de geplande tijd starten.

   ![Schermopname die laat zien hoe u een schema voor momentopnamen kunt inschakelen.](./media/enable-snapshot-schedule.png "Schema voor momentopnamen inschakelen.")

### <a name="trigger-a-snapshot"></a>Een momentopname activeren
De stappen in deze sectie zijn alleen van toepassing op delen op basis van momentopnamen.

1. U kunt een momentopname activeren op het **tabblad Details.** Selecteer op het tabblad **Momentopname activeren.** U kunt ervoor kiezen om een volledige momentopname of incrementele momentopname van uw gegevens te activeren. Als u voor het eerst gegevens van uw gegevensprovider ontvangt, selecteert u **Volledig kopiëren.** Wanneer een momentopname wordt uitgevoerd, worden volgende momentopnamen pas uitgevoerd wanneer de vorige is voltooid.

   ![Schermopname van de selectie Momentopname activeren.](./media/trigger-snapshot.png "Momentopname activeren.") 

1. Wanneer de status van de laatste run is *geslaagd,* gaat u naar het doelgegevensopslag om de ontvangen gegevens weer te geven. Selecteer **Gegevenssets** en selecteer vervolgens de koppeling naar het doelpad. 

   ![Schermopname van de toewijzing van een gegevensset voor consumenten.](./media/consumer-datasets.png "Toewijzing van gegevenssets van consumenten.") 

### <a name="view-history"></a>Geschiedenis weergeven
U kunt de geschiedenis van uw momentopnamen alleen bekijken in delen op basis van momentopnamen. Als u de geschiedenis wilt weergeven, opent u **het tabblad** Geschiedenis. Hier ziet u de geschiedenis van alle momentopnamen die in de afgelopen 30 dagen zijn gegenereerd. 

## <a name="storage-snapshot-performance"></a>Prestaties van opslagmomentopnamen
De prestaties van opslagmomentopnamen worden beïnvloed door een aantal factoren naast het aantal bestanden en de grootte van de gedeelde gegevens. Het wordt altijd aanbevolen om uw eigen prestatietests uit te voeren. Hieronder vindt u enkele voorbeeldfactoren die van invloed zijn op de prestaties.

* Gelijktijdige toegang tot de bron- en doelgegevensopslag.  
* Locatie van bron- en doelgegevensopslag. 
* Voor incrementele momentopnamen kan het aantal bestanden in de gedeelde gegevensset invloed hebben op de tijd die nodig is om de lijst met bestanden te vinden met de laatste wijzigingstijd na de laatste geslaagde momentopname. 


## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u gegevens van een opslagaccount kunt delen en ontvangen met behulp van de Azure Data Share service. Zie Ondersteunde gegevensopslag voor meer informatie over het delen [van gegevens uit andere gegevensbronnen.](supported-data-stores.md)
