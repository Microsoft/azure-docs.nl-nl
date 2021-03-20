---
title: XML valideren met schema's
description: Schema's toevoegen voor het valideren van XML-documenten in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75979382"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML valideren met schema's in Azure Logic Apps met Enterprise Integration Pack

Uw logische app kan schema's gebruiken om te controleren of documenten geldige XML gebruiken en de verwachte gegevens hebben in de vooraf gedefinieerde indeling voor scenario's voor bedrijfs integratie in Azure Logic Apps. Een schema kan ook berichten valideren die van toepassing zijn op Logic apps Exchange in Business-to-Business (B2B)-scenario's.

Zie [limieten en configuratie-informatie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor limieten met betrekking tot integratie accounts en-artefacten, zoals schema's.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meld u dan aan voor een gratis Azure-account</a>.

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waar u uw schema's en andere artefacten opslaat voor bedrijfs integratie en B2B-oplossingen (Business-to-Business). 

  Als uw schema [2 MB of kleiner](#smaller-schema)is, kunt u het schema rechtstreeks vanuit de Azure portal toevoegen aan uw integratie account. Als uw schema echter groter is dan 2 MB maar niet groter is dan de [maximale schema grootte](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), kunt u uw schema uploaden naar een Azure-opslag account. 
  Om dat schema toe te voegen aan uw integratie account, kunt u een koppeling maken naar uw opslag account vanuit uw integratie account. 
  Dit zijn de items die u nodig hebt voor deze taak: 

  * [Azure Storage-account](../storage/common/storage-account-overview.md) waar u een BLOB-container voor uw schema maakt. Meer informatie over het [maken van een opslag account](../storage/common/storage-account-create.md). 

  * BLOB-container voor het opslaan van uw schema. Meer informatie over het [maken van een BLOB-container](../storage/blobs/storage-quickstart-blobs-portal.md). 
  U hebt de inhouds-URI van de container later nodig wanneer u het schema toevoegt aan uw integratie account.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), die u kunt gebruiken voor het beheren van opslag accounts en BLOB-containers. 
  Als u Storage Explorer wilt gebruiken, kiest u een van de volgende opties:
  
    * Zoek en selecteer uw opslag account in de Azure Portal. 
    Selecteer in het menu van uw opslag account **Storage Explorer**.

    * [Down load en installeer Azure Storage Explorer](https://www.storageexplorer.com/)voor de desktop versie. 
    Vervolgens verbindt u Storage Explorer met uw opslag account door de stappen in aan de [slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)te volgen. 
    Zie [Quick Start: een BLOB maken in object opslag met Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)voor meer informatie.

U hebt geen logische app nodig bij het maken en toevoegen van schema's. Voor het gebruik van een schema moet uw logische app echter een koppeling hebben met een integratie account waar u dat schema opslaat. Meer informatie [over het koppelen van Logic apps aan integratie accounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Als u nog geen logische app hebt, leert u [hoe u logische apps kunt maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Schema's toevoegen

1. Gebruik de referenties van uw Azure-account om u aan melden bij het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. Als u uw integratie account wilt zoeken en openen, selecteert u in het hoofd menu van Azure **alle services**. Voer in het zoekvak ' integratie account ' in. Selecteer **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecteer het integratie account waaraan u het schema wilt toevoegen, bijvoorbeeld:

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **schema's** .

   ![Selecteer ' Schema's '](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Nadat de pagina **schema's** is geopend, kiest u **toevoegen**.

   ![Kies toevoegen](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Volg de stappen voor het uploaden van een schema dat [Maxi maal 2 MB](#smaller-schema) of [meer dan 2 MB bedraagt,](#larger-schema)op basis van de grootte van uw schema bestand (. XSD), Maxi maal 8 MB.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Schema's toevoegen tot 2 MB

1. Voer onder **schema toevoegen** een naam in voor het schema. 
   **Klein bestand** selecteren. Klik naast het vak **schema** op het mappictogram. Zoek en selecteer het schema dat u wilt uploaden, bijvoorbeeld:

   ![Kleiner schema uploaden](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Wanneer u klaar bent, kiest u **OK**.

   Nadat het schema is geüpload, wordt het schema weer gegeven in de lijst **schema** .

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Schema's meer dan 2 MB toevoegen

Als u grotere schema's wilt toevoegen, kunt u uw schema uploaden naar een Azure-Blob-container in uw Azure Storage-account. De stappen voor het toevoegen van schema's variëren op basis van het feit of de BLOB-container open bare Lees toegang heeft. Controleer eerst of de BLOB-container open bare Lees toegang heeft door de volgende stappen uit te voeren: [openbaar toegangs niveau voor BLOB-container instellen](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Toegangs niveau voor container controleren

1. Open Azure Storage Explorer. In het venster Explorer breidt u uw Azure-abonnement uit als dat nog niet is gebeurd.

1. Vouw **opslag accounts** uit > {*your-storage-account*} > **BLOB-containers**. Selecteer uw BLOB-container.

1. Selecteer in het snelmenu van de BLOB-container het optie **openbaar toegangs niveau instellen**.

   * Als uw BLOB-container ten minste open bare toegang heeft, kiest u **Annuleren** en volgt u deze stappen verderop op deze pagina: [uploaden naar containers met open bare toegang](#public-access)

     ![Openbare toegang](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Als uw BLOB-container geen open bare toegang heeft, kiest u **Annuleren** en volgt u deze stappen verderop op deze pagina: [uploaden naar containers zonder open bare toegang](#public-access)

     ![Geen open bare toegang](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Uploaden naar containers met open bare toegang

1. Upload het schema naar uw opslag account. 
   Kies **uploaden** in het rechterdeel venster.

1. Nadat u klaar bent met uploaden, selecteert u het geüploade schema. Kies op de werk balk **URL kopiëren** zodat u de URL van het schema kopieert.

1. Ga terug naar de Azure Portal waar het deel venster **schema toevoegen** is geopend. 
   Voer een naam in voor de assembly. 
   Kies een **groot bestand (groter dan 2 MB)**. 

   Het vak **URI van inhoud** wordt nu weer gegeven in plaats van het vak **schema** .

1. Plak de URL van uw schema in het vak **URI van inhoud** . 
   Het toevoegen van het schema volt ooien.

Nadat het schema is geüpload, wordt het schema weer gegeven in de lijst **schema** . Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, toont de tegel **schema's** nu het aantal geüploade schema's.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Uploaden naar containers zonder open bare toegang

1. Upload het schema naar uw opslag account. 
   Kies **uploaden** in het rechterdeel venster.

1. Genereer een Shared Access Signature (SAS) voor uw schema nadat u klaar bent met het uploaden. 
   Selecteer in het snelmenu van het schema de optie **Shared Access Signature ophalen**.

1. Selecteer in het deel venster **Shared Access Signature** de optie URI maken voor **Shared Access-hand tekening op container niveau genereren**  >  . 
   Wanneer de SAS-URL wordt gegenereerd, klikt u naast het vak **URL** op **kopiëren**.

1. Ga terug naar de Azure Portal waar het deel venster **schema toevoegen** is geopend. Kies een **groot bestand**.

   Het vak **URI van inhoud** wordt nu weer gegeven in plaats van het vak **schema** .

1. Plak in het vak **URI van inhoud** de SAS-URI die u eerder hebt gegenereerd. Het toevoegen van het schema volt ooien.

Nadat het schema is geüpload, wordt het schema weer gegeven in de lijst **schema** . Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, toont de tegel **schema's** nu het aantal geüploade schema's.

## <a name="edit-schemas"></a>Schema's bewerken

Als u een bestaand schema wilt bijwerken, moet u een nieuw schema bestand uploaden dat de gewenste wijzigingen bevat. U kunt echter eerst het bestaande schema downloaden om het te bewerken.

1. Zoek en open uw integratie account in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>als dit nog niet is geopend.

1. Selecteer in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' integratie account ' in. 
   Selecteer **integratie accounts**.

1. Selecteer het integratie account waar u het schema wilt bijwerken.

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **schema's** .

1. Nadat de pagina **schema's** is geopend, selecteert u uw schema. 
   Als u het schema eerst wilt downloaden en bewerken, kiest u **downloaden** en slaat u het schema op.

1. Wanneer u klaar bent om het bijgewerkte schema te uploaden, selecteert u op de pagina **schema's** het schema dat u wilt bijwerken en kiest u **bijwerken**.

1. Zoek en selecteer het bijgewerkte schema dat u wilt uploaden. 
   Nadat het schema bestand is geüpload, wordt het bijgewerkte schema weer gegeven in de lijst **schemas** .

## <a name="delete-schemas"></a>Schema's verwijderen

1. Zoek en open uw integratie account in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>als dit nog niet is geopend.

1. Selecteer in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' integratie account ' in. 
   Selecteer **integratie accounts**.

1. Selecteer het integratie account waar u het schema wilt verwijderen.

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **schema's** .

1. Nadat de pagina **schema's** is geopend, selecteert u uw schema en kiest u **verwijderen**.

1. Kies **Ja** om te bevestigen dat u het schema wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Meer informatie over Maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Meer informatie over trans formaties](../logic-apps/logic-apps-enterprise-integration-transform.md)
