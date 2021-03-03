---
title: Gegevens delen en ontvangen van Azure Blob Storage en Azure Data Lake Storage
description: Meer informatie over het delen en ontvangen van gegevens van Azure Blob Storage en Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: dc309e85373193e4f5d431f543ff3e59ea5bebc7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739259"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Gegevens delen en ontvangen van Azure Blob Storage en Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure data share ondersteunt het delen op basis van moment opnamen vanuit een opslag account. In dit artikel wordt uitgelegd hoe u gegevens kunt delen en ontvangen van Azure Blob Storage, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2.

De Azure-gegevens share ondersteunt het delen van bestanden, mappen en bestands systemen van Azure Data Lake gen1 en Azure Data Lake Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers vanuit Azure Blob Storage. Alleen blok-blobs worden momenteel ondersteund. Gegevens die worden gedeeld vanuit deze bronnen kunnen worden ontvangen door Azure Data Lake Gen2 of Azure Blob Storage.

Wanneer bestands systemen, containers of mappen worden gedeeld in delen op basis van moment opnamen, kunnen gegevens gebruikers ervoor kiezen om een volledige kopie van de share gegevens te maken. Of ze kunnen de mogelijkheid van de incrementele moment opname gebruiken om alleen nieuwe of bijgewerkte bestanden te kopiëren. De functie incrementele moment opname is gebaseerd op de tijd van de laatste wijziging van de bestanden. 

Bestaande bestanden met dezelfde naam worden overschreven tijdens een moment opname. Een bestand dat wordt verwijderd uit de bron, wordt niet verwijderd op het doel. Lege submappen op de bron worden niet naar het doel gekopieerd. 

## <a name="share-data"></a>Gegevens delen

Gebruik de informatie in de volgende secties om gegevens te delen met behulp van de Azure-gegevens share. 
### <a name="prerequisites-to-share-data"></a>Vereisten voor het delen van gegevens

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Zoek het e-mail adres van Azure Sign-in van de ontvanger. De e-mail alias van de ontvanger werkt niet voor uw doel einden.
* Als de bron-Azure-gegevens opslag zich in een ander Azure-abonnement bevindt dan de locatie waar u de gegevens share bron maakt, registreert u de [resource provider micro soft. DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waar het Azure-gegevens archief zich bevindt. 

### <a name="prerequisites-for-the-source-storage-account"></a>Vereisten voor het bron-opslag account

* Een Azure Storage-account. Als u nog geen account hebt, maakt u er [een](../storage/common/storage-account-create.md).
* Machtiging voor het schrijven naar het opslag account. Schrijf machtiging bevindt zich in *micro soft. Storage/Storage accounts/write*. Het maakt deel uit van de rol Inzender.
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account. Deze machtiging bevindt zich in *micro soft. autorisatie/roltoewijzingen/schrijven*. Het maakt deel uit van de rol van eigenaar. 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Een gegevens share-account maken

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Open het menu in de linkerbovenhoek van de portal en selecteer vervolgens **een resource maken** (+).

1. Zoek naar *Data Share*.

1. Selecteer **gegevens share** en **Maak** deze.

1. Geef de basis gegevens van uw Azure-gegevens share bron op: 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer een Azure-abonnement voor uw gegevens share-account.|
    | Resourcegroep | *test-resource-group* | Gebruik een bestaande resource groep of maak een resource groep. |
    | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
    | Naam | *datashareaccount* | Geef uw gegevens share-account een naam. |
    | | |

1. Selecteer **controleren +** maken  >  **maken** om uw gegevens share-account in te richten. Het inrichten van een nieuw gegevens share account duurt doorgaans ongeveer twee minuten. 

1. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

### <a name="create-a-share"></a>Een share maken

1. Ga naar de pagina **overzicht** van gegevens delen.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Scherm opname van het overzicht van gegevens delen.":::

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Geef de details op voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![Scherm opname van Details van gegevens delen.](./media/enter-share-details.png "Voer de details van de gegevens share in.") 

1. Selecteer **Doorgaan**.

1. Als u gegevens sets wilt toevoegen aan uw share, selecteert u **gegevens sets toevoegen**. 

    ![Scherm afbeelding die laat zien hoe gegevens sets aan uw share worden toegevoegd.](./media/datasets.png "Sets.")

1. Selecteer een type gegevensset om toe te voegen. De lijst met typen gegevensset is afhankelijk van of u in de vorige stap hebt gekozen voor delen op basis van moment opnamen of in-place delen. 

    ![Scherm afbeelding die laat zien waar u een type gegevensset kunt selecteren.](./media/add-datasets.png "Voeg gegevens sets toe.")    

1. Ga naar het object dat u wilt delen. Selecteer vervolgens **gegevens sets toevoegen**. 

    ![Scherm afbeelding die laat zien hoe u een object selecteert dat u wilt delen.](./media/select-datasets.png "Selecteer gegevens sets.")    

1. Voeg op het tabblad **ontvangers** het e-mail adres van uw gegevens consument toe door **ontvanger toevoegen** te selecteren. 

    ![Scherm afbeelding die laat zien hoe e-mail adressen van ontvangers worden toegevoegd.](./media/add-recipient.png "Ontvangers toevoegen.") 

1. Selecteer **Doorgaan**.

1. Als u een type snap shot share hebt geselecteerd, kunt u het momentopname schema instellen om uw gegevens bij te werken voor de gegevens verbruiker. 

    ![Scherm afbeelding met de instellingen van het schema voor moment opnamen.](./media/enable-snapshots.png "Moment opnamen inschakelen.") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad **controleren en maken** de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer vervolgens **Maken**.

U hebt nu uw Azure-gegevens share gemaakt. De ontvanger van uw gegevens share kan uw uitnodiging accepteren. 

## <a name="receive-data"></a>Gegevens ontvangen

In de volgende secties wordt beschreven hoe u gedeelde gegevens kunt ontvangen.
### <a name="prerequisites-to-receive-data"></a>Vereisten voor het ontvangen van gegevens
Zorg ervoor dat u beschikt over de volgende vereisten voordat u een uitnodiging voor gegevens delen accepteert: 

* Een Azure-abonnement. Als u geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/).
* Een uitnodiging van Azure. Het onderwerp van de e-mail moet ' uitnodiging voor Azure-gegevens delen van *\<yourdataprovider\@domain.com>* ' zijn.
* Een geregistreerde [resource provider voor micro soft. DataShare](concepts-roles-permissions.md#resource-provider-registration) in:
    * Het Azure-abonnement waarin u een gegevens share bron maakt.
    * Het Azure-abonnement waarin uw doel-Azure-gegevens archieven zich bevinden.

### <a name="prerequisites-for-a-target-storage-account"></a>Vereisten voor een doel opslag account

* Een Azure Storage-account. Als u er nog geen hebt, moet u [een account maken](../storage/common/storage-account-create.md). 
* Machtiging voor het schrijven naar het opslag account. Deze machtiging bevindt zich in *micro soft. Storage/Storage accounts/write*. Het maakt deel uit van de rol Inzender. 
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account. Deze toewijzing bevindt zich in *micro soft. autorisatie/roltoewijzingen/schrijven*. Het maakt deel uit van de rol van eigenaar.  

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Een uitnodiging openen

U kunt een uitnodiging openen vanuit e-mail of rechtstreeks vanuit het Azure Portal.

1. Als u een uitnodiging vanuit e-mail wilt openen, controleert u uw postvak in op een uitnodiging van uw gegevens provider. De uitnodiging van Microsoft Azure heeft de titel ' uitnodiging voor Azure-gegevens delen van *\<yourdataprovider\@domain.com>* '. Selecteer **uitnodiging weer geven** om uw uitnodiging in azure te bekijken. 

   Zoek *uitnodigingen voor gegevens delen* om een uitnodiging te openen vanuit het Azure Portal. U ziet een lijst met uitnodigingen voor gegevens delen.

   ![Scherm opname van de lijst met uitnodigingen in de Azure Portal.](./media/invitations.png "Lijst met uitnodigingen.") 

1. Selecteer de share die u wilt weer geven. 

### <a name="accept-an-invitation"></a>Een uitnodiging accepteren
1. Bekijk alle velden, met inbegrip van de **Gebruiksvoorwaarden**. Als u akkoord gaat met de voor waarden, schakelt u het selectie vakje in. 

   ![Scherm opname met het Gebruiksvoorwaarden gebied.](./media/terms-of-use.png "Gebruiksvoorwaarden.") 

1. Onder **doel gegevens share account** selecteert u het abonnement en de resource groep waar u uw gegevens share gaat implementeren. Vul vervolgens de volgende velden in:

   * Selecteer in het veld **gegevens share account** de optie **nieuwe maken** als u geen gegevens share-account hebt. Als dat niet het geval is, selecteert u een bestaand gegevens share-account waarmee uw gegevens share wordt geaccepteerd. 

   * In het veld **ontvangen share naam** , behoud de standaard waarde die de gegevens provider heeft opgegeven of geef een nieuwe naam op voor de ontvangen share. 

1. Selecteer **accepteren en configureren**. Er wordt een share abonnement gemaakt. 

   ![Scherm opname waarin wordt weer gegeven waar de configuratie opties moeten worden geaccepteerd.](./media/accept-options.png "Opties voor accepteren") 

    De ontvangen share wordt weer gegeven in uw gegevens share-account. 

    Als u de uitnodiging niet wilt accepteren, selecteert u **afwijzen**. 

### <a name="configure-a-received-share"></a>Een ontvangen share configureren
Volg de stappen in deze sectie om een locatie te configureren voor het ontvangen van gegevens.

1. Schakel op het tabblad **gegevens sets** het selectie vakje in naast de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **toewijzen aan doel** om een doel gegevens archief te kiezen. 

   ![Scherm afbeelding die laat zien hoe u kunt toewijzen aan een doel.](./media/dataset-map-target.png "Toewijzen aan doel.") 

1. Selecteer een doel gegevens Archief voor de gegevens. Bestanden in de doel gegevens opslag die hetzelfde pad en dezelfde naam hebben als de bestanden in de ontvangen gegevens, worden overschreven. 

   ![Scherm afbeelding die laat zien waar u een doel opslag account selecteert.](./media/map-target.png "Doel opslag.") 

1. Voor het delen op basis van moment opnamen, als de gegevens provider een momentopname schema gebruikt om de gegevens regel matig bij te werken, kunt u de planning inschakelen op het tabblad **schema voor moment opnamen** . Schakel het selectie vakje in naast het schema voor de moment opname. Selecteer vervolgens **inschakelen**.

   ![Scherm afbeelding die laat zien hoe u een momentopname schema kunt inschakelen.](./media/enable-snapshot-schedule.png "Momentopname schema inschakelen.")

### <a name="trigger-a-snapshot"></a>Een momentopname activeren
De stappen in deze sectie zijn alleen van toepassing op delen op basis van moment opnamen.

1. U kunt een moment opname activeren op het tabblad **Details** . Op het tabblad, selecteert u **moment opname activeren**. U kunt ervoor kiezen om een volledige moment opname of incrementele moment opname van uw gegevens te activeren. Als u voor de eerste keer gegevens van uw gegevens provider ontvangt, selecteert u **volledig kopiëren**. Wanneer een moment opname wordt uitgevoerd, worden volgende moment opnamen niet gestart totdat de vorige is voltooid.

   ![Scherm afbeelding van de selectie van de moment opname van de trigger.](./media/trigger-snapshot.png "Moment opname activeren.") 

1. Wanneer de status van de laatste uitvoering is *geslaagd*, gaat u naar het doel gegevens archief om de ontvangen gegevens weer te geven. Selecteer **gegevens sets** en selecteer vervolgens de koppeling doelpad. 

   ![Scherm opname van de toewijzing van een consument gegevensset.](./media/consumer-datasets.png "Toewijzing van de consument DataSet.") 

### <a name="view-history"></a>Geschiedenis weergeven
U kunt de geschiedenis van uw moment opnamen alleen bekijken in delen op basis van moment opnamen. Open het tabblad **geschiedenis** om de geschiedenis weer te geven. Hier ziet u de geschiedenis van alle moment opnamen die in de afgelopen 30 dagen zijn gegenereerd. 

## <a name="storage-snapshot-performance"></a>Prestaties van opslag momentopname
De prestaties van de opslag momentopname worden beïnvloed door een aantal factoren naast het aantal bestanden en de grootte van de gedeelde gegevens. Het wordt altijd aanbevolen uw eigen prestatie tests uit te voeren. Hieronder ziet u enkele voor beelden van factoren die invloed hebben op de prestaties.

* Gelijktijdige toegang tot de bron-en doel gegevens archieven.  
* Locatie van bron-en doel gegevens archieven. 
* Voor incrementele moment opnamen kan het aantal bestanden in de gedeelde gegevensset van invloed zijn op de tijd om de lijst met bestanden te vinden waarvan het tijdstip voor het laatst is gewijzigd na de laatste geslaagde moment opname. 


## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u gegevens kunt delen en ontvangen van een opslag account met behulp van de Azure data share-service. Zie [ondersteunde gegevens archieven](supported-data-stores.md)voor meer informatie over het delen van andere gegevens bronnen.