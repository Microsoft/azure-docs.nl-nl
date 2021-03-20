---
title: XML transformeren met XSLT-kaarten
description: XSLT-kaarten toevoegen aan het transformeren van XML in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570783"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML transformeren met kaarten in Azure Logic Apps met Enterprise Integration Pack

Als u XML-gegevens wilt overdragen tussen indelingen voor scenario's voor bedrijfsintegratie in Azure Logic Apps, kunt u met uw logische app gebruikmaken van toewijzingen of, meer specifiek, XSLT-toewijzingen (Extensible Style Sheet Language Transformations). Een toewijzing is een XML-document dat beschrijft hoe gegevens van een XML-document naar een andere indeling moeten worden geconverteerd. 

Stel bijvoorbeeld dat u regel matig B2B-orders of facturen ontvangt van een klant die de datum notatie YYYMMDD gebruikt. Uw organisatie gebruikt echter de datum notatie MMDDYYY. U kunt een kaart definiëren en gebruiken die de YYYMMDD datum notatie naar de MMDDYYY-indeling transformeert voordat u de order-of factuur details opslaat in uw data base met klant activiteiten.

Zie [limieten en configuratie-informatie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor limieten met betrekking tot integratie accounts en-artefacten, zoals Maps.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waar u uw kaarten en andere artefacten opslaat voor Enter prise Integration en Business-to-Business (B2B)-oplossingen.

* Als uw kaart verwijst naar een externe assembly, moet u *zowel de assembly als de kaart* uploaden naar uw integratie account. Zorg ervoor dat u [*eerst uw assembly uploadt*](#add-assembly)en upload vervolgens de kaart die verwijst naar de assembly.

  Als uw assembly 2 MB of kleiner is, kunt u de assembly *rechtstreeks* vanuit de Azure portal toevoegen aan uw integratie account. Als uw assembly of toewijzing echter groter is dan 2 MB maar niet groter is dan de [maximale grootte voor verzamelingen of kaarten](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), hebt u de volgende opties:

  * Voor assembly's hebt u een Azure Blob-container nodig waar u de assembly en de locatie van die container kunt uploaden. Op die manier kunt u deze locatie later opgeven wanneer u de assembly toevoegt aan uw integratie account. 
  Voor deze taak hebt u de volgende items nodig:

    | Item | Beschrijving |
    |------|-------------|
    | [Azure Storage-account](../storage/common/storage-account-overview.md) | Maak in dit account een Azure Blob-container voor de assembly. Meer informatie [over het maken van een opslag account](../storage/common/storage-account-create.md). |
    | Blobcontainer | In deze container kunt u uw assembly uploaden. U hebt ook de locatie van deze container nodig wanneer u de assembly toevoegt aan uw integratie account. Meer informatie over het [maken van een BLOB-container](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure-opslagverkenner](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Met dit hulp programma kunt u opslag accounts en BLOB-containers eenvoudiger beheren. Als u Storage Explorer wilt gebruiken, moet u [Azure Storage Explorer downloaden en installeren](https://www.storageexplorer.com/). Vervolgens verbindt u Storage Explorer met uw opslag account door de stappen in aan de [slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)te volgen. Zie [Quick Start: een BLOB maken in object opslag met Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)voor meer informatie. <p>U kunt ook uw opslag account zoeken en selecteren in het Azure Portal. Selecteer in het menu van uw opslag account **Storage Explorer**. |
    |||

  * Voor Maps kunt u op dit moment grotere kaarten toevoegen met behulp van de [Azure Logic apps-rest API-kaarten](/rest/api/logic/maps/createorupdate).

U hebt geen logische app nodig bij het maken en toevoegen van Maps. Voor het gebruik van een kaart moet uw logische app echter een koppeling hebben met een integratie account waar u die kaart opslaat. Meer informatie [over het koppelen van Logic apps aan integratie accounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Als u nog geen logische app hebt, leert u [hoe u logische apps kunt maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Assembly's waarnaar wordt verwezen, toevoegen

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Als u uw integratie account wilt zoeken en openen, selecteert u in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' integratie account ' in. 
   Selecteer **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecteer het integratie account waaraan u de assembly wilt toevoegen, bijvoorbeeld:

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **assembly's** .

   ![Selecteer Assembly's](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Nadat de pagina **assembly's** is geopend, kiest u **toevoegen**.

   ![Scherm afbeelding die de knop toevoegen op de pagina Assembly's markeert.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Op basis van de grootte van uw assembly-bestand, volgt u de stappen voor het uploaden van een assembly van [Maxi maal 2 MB](#smaller-assembly) of [meer dan 2 MB, maar Maxi maal 8 MB](#larger-assembly).
Zie [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)voor limieten voor assembly aantallen in integratie accounts.

> [!NOTE]
> Als u de assembly wijzigt, moet u ook uw kaart bijwerken, ongeacht of de kaart wijzigingen bevat.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Assembly's Maxi maal 2 MB toevoegen

1. Voer onder **Assembly toevoegen** een naam in voor de assembly. **Klein bestand** selecteren. Klik naast het vak **Assembly** op het mappictogram. Zoek en selecteer de assembly die u wilt uploaden, bijvoorbeeld:

   ![Kleinere assembly uploaden](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   In de eigenschap **assembly naam** wordt de bestands naam van de assembly automatisch weer gegeven nadat u de assembly hebt geselecteerd.

1. Wanneer u klaar bent, kiest u **OK**.

   Nadat het assembly-bestand is geüpload, wordt de assembly weer gegeven in de lijst met **assembly's** .

   ![Lijst met geüploade assembly's](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, bevat de tegel **assembly's** nu het aantal geüploade assembly's, bijvoorbeeld:

   ![Geüploade assembly's](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Assembly's meer dan 2 MB toevoegen

Als u grotere assembly's wilt toevoegen, kunt u uw assembly uploaden naar een Azure-Blob-container in uw Azure Storage-account. De stappen voor het toevoegen van assembly's variëren op basis van het feit of de BLOB-container open bare Lees toegang heeft. Controleer eerst of de BLOB-container open bare Lees toegang heeft door de volgende stappen uit te voeren: [openbaar toegangs niveau voor BLOB-container instellen](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Toegangs niveau voor container controleren

1. Open Azure Storage Explorer. In het venster Explorer breidt u uw Azure-abonnement uit als dat nog niet is gebeurd.

1. Vouw **opslag accounts** uit > {*your-storage-account*} > **BLOB-containers**. Selecteer uw BLOB-container.

1. Selecteer in het snelmenu van de BLOB-container het optie **openbaar toegangs niveau instellen**.

   * Als uw BLOB-container ten minste open bare toegang heeft, kiest u **Annuleren** en volgt u deze stappen verderop op deze pagina: [uploaden naar containers met open bare toegang](#public-access-assemblies)

     ![Openbare toegang](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Als uw BLOB-container geen open bare toegang heeft, kiest u **Annuleren** en volgt u deze stappen verderop op deze pagina: [uploaden naar containers zonder open bare toegang](#no-public-access-assemblies)

     ![Geen open bare toegang](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Uploaden naar containers met open bare toegang

1. Upload de assembly naar uw opslag account. 
   Kies **uploaden** in het rechterdeel venster.

1. Nadat u klaar bent met uploaden, selecteert u de geüploade assembly. Kies op de werk balk **URL kopiëren** zodat u de URL van de assembly kopieert.

1. Ga terug naar de Azure Portal waar het deel venster **Assembly toevoegen** is geopend. 
   Voer een naam in voor de assembly. 
   Kies een **groot bestand (groter dan 2 MB)**.

   Het vak **URI van inhoud** wordt nu weer gegeven in plaats van het vak voor de **Assembly** .

1. Plak de URL van de assembly in het vak **URI van inhoud** . 
   Toevoegen van de assembly volt ooien.

Nadat de assembly is geüpload, wordt het schema weer gegeven in de lijst met **assembly's** .
Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, bevat de tegel **assembly's** nu het aantal geüploade assembly's.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Uploaden naar containers zonder open bare toegang

1. Upload de assembly naar uw opslag account. 
   Kies **uploaden** in het rechterdeel venster.

1. Genereer een Shared Access Signature (SAS) voor uw assembly nadat u klaar bent met het uploaden. 
   Selecteer in het snelmenu van de assembly de optie **Shared Access Signature ophalen**.

1. Selecteer in het deel venster **Shared Access Signature** de optie URI maken voor **Shared Access-hand tekening op container niveau genereren**  >  . 
   Wanneer de SAS-URL wordt gegenereerd, klikt u naast het vak **URL** op **kopiëren**.

1. Ga terug naar de Azure Portal waar het deel venster **Assembly toevoegen** is geopend. 
   Voer een naam in voor de assembly. 
   Kies een **groot bestand (groter dan 2 MB)**.

   Het vak **URI van inhoud** wordt nu weer gegeven in plaats van het vak voor de **Assembly** .

1. Plak in het vak **URI van inhoud** de SAS-URI die u eerder hebt gegenereerd. Toevoegen van de assembly volt ooien.

Nadat de assembly is geüpload, wordt de assembly weer gegeven in de lijst **schemas** . Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, bevat de tegel **assembly's** nu het aantal geüploade assembly's.

## <a name="create-maps"></a>Kaarten maken

Als u een XSLT-document wilt maken, kunt u gebruiken als kaart, kunt u Visual Studio 2015 gebruiken voor het maken van een BizTalk-integratie project met behulp van de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). In dit project kunt u een bestand met een integratie toewijzing maken, waarmee u items visueel kunt toewijzen tussen twee XML-schema bestanden. Nadat u dit project hebt gemaakt, krijgt u een XSLT-document.
Zie [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)voor limieten voor het toewijzen van aantallen in integratie accounts. 

## <a name="add-maps"></a>Kaarten toevoegen

Nadat u alle assembly's hebt geüpload die door uw kaart worden verwezen, kunt u nu uw kaart uploaden.

1. Als u zich nog niet hebt aangemeld, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) met de referenties van uw Azure-account. 

1. Als uw integratie account nog niet is geopend, selecteert u in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' integratie account ' in. 
   Selecteer **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecteer het integratie account waaraan u de kaart wilt toevoegen, bijvoorbeeld:

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **kaarten** .

   ![Selecteer kaarten](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Nadat de pagina **kaarten** is geopend, kiest u **toevoegen**.

   ![Kies toevoegen](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Kaarten toevoegen tot 2 MB

1. Voer onder **kaart toevoegen** een naam in voor de kaart. 

1. Onder **kaart type** selecteert u het type, bijvoorbeeld: **liquide**, **XSLT**, **XSLT 2,0** of **XSLT 3,0**.

1. **Klein bestand** selecteren. Kies het mappictogram naast het vak **kaart** . Zoek en selecteer de kaart die u wilt uploaden, bijvoorbeeld:

   ![Kaart uploaden](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Als u de eigenschap **name** leeg hebt gelaten, wordt de bestands naam van de kaart automatisch in die eigenschap automatisch weer gegeven nadat u het kaart bestand hebt geselecteerd. 
   U kunt echter een unieke naam gebruiken.

1. Wanneer u klaar bent, kiest u **OK**. 
   Nadat het kaart bestand is geüpload, wordt de kaart weer gegeven in de lijst **kaarten** .

   ![Lijst met geüploade kaarten](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Op de **overzichts** pagina van uw integratie account, onder **onderdelen**, bevat de tegel **kaarten** nu het aantal geüploade kaarten, bijvoorbeeld:

   ![Geüploade kaarten](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Maps toevoegen meer dan 2 MB

Als u op dit moment grotere kaarten wilt toevoegen, gebruikt u de [Azure Logic apps-rest API-kaarten](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Kaarten bewerken

Als u een bestaande kaart wilt bijwerken, moet u een nieuw kaart bestand uploaden dat de gewenste wijzigingen bevat. U kunt echter eerst de bestaande kaart voor bewerken downloaden.

1. Zoek en open uw integratie account in de [Azure Portal](https://portal.azure.com)als dit nog niet is geopend.

1. Selecteer in het hoofd menu van Azure **alle services**. Voer in het zoekvak ' integratie account ' in. Selecteer **integratie accounts**.

1. Selecteer het integratie account waar u de kaart wilt bijwerken.

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **kaarten** .

1. Nadat de pagina **Maps** is geopend, selecteert u de kaart. 
   Als u eerst de kaart wilt downloaden en bewerken, kiest u **downloaden** en slaat u de kaart op.

1. Wanneer u klaar bent om de bijgewerkte kaart te uploaden, selecteert u op de pagina **kaarten** de kaart die u wilt bijwerken en kiest u **bijwerken**.

1. Zoek en selecteer de bijgewerkte kaart die u wilt uploaden. 
   Nadat het kaart bestand is geüpload, wordt de bijgewerkte kaart weer gegeven in de lijst **kaarten** .

## <a name="delete-maps"></a>Kaarten verwijderen

1. Zoek en open uw integratie account in de [Azure Portal](https://portal.azure.com)als dit nog niet is geopend.

1. Selecteer in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' integratie account ' in. 
   Selecteer **integratie accounts**.

1. Selecteer het integratie account waarnaar u de kaart wilt verwijderen.

1. Selecteer op de pagina **overzicht** van uw integratie account onder **onderdelen** de tegel **kaarten** .

1. Nadat de pagina **kaarten** is geopend, selecteert u uw kaart en kiest u **verwijderen**.

1. Klik op **Ja** om te bevestigen dat u de kaart wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Meer informatie over schema's](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Meer informatie over trans formaties](../logic-apps/logic-apps-enterprise-integration-transform.md)
