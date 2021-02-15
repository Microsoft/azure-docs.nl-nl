---
title: Nieuwe en gewijzigde bestanden kopiëren via LastModifiedDate
description: Meer informatie over het gebruik van een oplossings sjabloon om nieuwe en gewijzigde bestanden te kopiëren door LastModifiedDate met Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 94d09dc96d017dba3b16cc5fe113272a9393741d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362077"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Nieuwe en gewijzigde bestanden kopiëren met behulp van LastModifiedDate met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een oplossings sjabloon beschreven die u kunt gebruiken om nieuwe en gewijzigde bestanden alleen te kopiëren door LastModifiedDate vanuit een archief op basis van een bestand naar een doel archief. 

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden eerst de nieuwe en gewijzigde bestanden geselecteerd op basis van de kenmerken **LastModifiedDate** en worden vervolgens die geselecteerde bestanden gekopieerd van de gegevens bron opslag naar het doel archief van de gegevens.

De sjabloon bevat één activiteit:
- **Kopiëren** om nieuwe en gewijzigde bestanden alleen door LastModifiedDate te kopiëren van een bestands archief naar een doel archief.

De sjabloon definieert zes para meters:
-  *FolderPath_Source* is het mappad waar u de bestanden uit het bron archief kunt lezen. U moet de standaard waarde vervangen door uw eigen mappad.
-  *Directory_Source* is het pad naar de submap waar u de bestanden uit het bron archief kunt lezen. U moet de standaard waarde vervangen door uw eigen pad naar de submap.
-  *FolderPath_Destination* is het mappad waarnaar u bestanden wilt kopiëren naar het doel archief. U moet de standaard waarde vervangen door uw eigen mappad.
-  *Directory_Destination* is het pad naar de submap waar u bestanden naar het doel archief wilt kopiëren. U moet de standaard waarde vervangen door uw eigen pad naar de submap.
-  *LastModified_From* wordt gebruikt om de bestanden te selecteren waarvan het kenmerk LastModifiedDate na of gelijk is aan deze datum/tijd waarde.  Als u alleen de nieuwe bestanden wilt selecteren, die de laatste keer niet zijn gekopieerd, kan deze datum/tijd-waarde de tijd zijn waarop de pijp lijn de laatste keer is geactiveerd. U kunt de standaard waarde ' 2019-02-01T00:00:00Z ' vervangen door de verwachte LastModifiedDate in UTC-tijd zone. 
-  *LastModified_To* wordt gebruikt om de bestanden te selecteren waarvan het kenmerk LastModifiedDate vóór deze datum/tijd-waarde valt. Als u alleen de nieuwe bestanden wilt selecteren, die de laatste keer niet zijn gekopieerd, kan deze datum/tijd-waarde de huidige keer zijn.  U kunt de standaard waarde ' 2019-02-01T00:00:00Z ' vervangen door de verwachte LastModifiedDate in UTC-tijd zone. 

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar sjabloon **alleen nieuwe bestanden kopiëren door LastModifiedDate**. Een **nieuwe** verbinding maken met uw bron opslag archief. In het bron opslag archief kunt u bestanden kopiëren.

    ![Een nieuwe verbinding maken met de bron](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Maak een **nieuwe** verbinding naar uw doel archief. U wilt bestanden kopiëren naar het doel archief. 

    ![Een nieuwe verbinding maken met de bestemming](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. U ziet de pijp lijn die beschikbaar is in het deel venster, zoals in het volgende voor beeld wordt weer gegeven:

    ![De pijp lijn weer geven](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecteer **debug**, schrijf de waarde voor de **para meters** en selecteer **volt ooien**.  In de onderstaande afbeelding worden de para meters als volgt ingesteld.
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = submap
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = submap
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Het voor beeld geeft aan dat de bestanden, die voor het laatst zijn gewijzigd binnen de time span (**2019-02-01T00:00:00Z** naar **2019-03-01T00:00:00Z**), worden gekopieerd van het bronpad **sourcefolder/submap** naar het doelpad **destinationfolder/submap**.  U kunt deze vervangen door uw eigen para meters.

    ![De pijplijn uitvoeren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Bekijk het resultaat. U ziet alleen de bestanden die voor het laatst zijn gewijzigd binnen de geconfigureerde tijds periode, zijn gekopieerd naar het doel archief.

    ![Bekijk het resultaat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. U kunt nu een tumblingvenstertriggers-Windows-trigger toevoegen om deze pijp lijn te automatiseren, zodat de pijp lijn altijd nieuwe en gewijzigde bestanden alleen door LastModifiedDate kan kopiëren.  Selecteer **trigger toevoegen** en selecteer **Nieuw/bewerken**.

    ![Scherm afbeelding met de optie Nieuw/bewerken menu die wordt weer gegeven wanneer u trigger toevoegen selecteert.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Selecteer **+ Nieuw** in het venster **triggers toevoegen** .

9. Selecteer **tumblingvenstertriggers-venster** voor het trigger type, stel **elke 15 minuten** in als het terugkeer patroon (u kunt wijzigen in een wille keurige interval periode). Selecteer **Ja** voor geactiveerd vak en selecteer vervolgens **OK**.

    ![Trigger maken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Stel de waarde voor de **trigger run-para meters** als volgt in en selecteer **volt ooien**.
    - **FolderPath_Source**  =  **sourcefolder**.  U kunt vervangen door de map in de gegevens opslag van de bron.
    - **Directory_Source**  =  **submap**.  U kunt vervangen door de submap in de gegevens opslag van de bron.
    - **FolderPath_Destination**  =  **destinationfolder**.  U kunt vervangen door de map in het doel gegevens archief.
    - **Directory_Destination**  =  **submap**.  U kunt vervangen door de submap in het doel gegevens archief.
    - **LastModified_From**  =   **\@ trigger (). outputs. windowStartTime**.  Het is een systeem variabele van de trigger voor het bepalen van de tijd waarop de pijp lijn de laatste keer is geactiveerd.
    - **LastModified_To**  =  **\@ trigger (). outputs. windowEndTime**.  Het is een systeem variabele van de trigger die de tijd bepaalt wanneer de pijp lijn deze keer wordt geactiveerd.
    
    ![Invoer parameters](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecteer **Alles publiceren**.
    
    ![Alles publiceren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Nieuwe bestanden maken in de bronmap van de gegevens bron opslag.  U wacht nu totdat de pijp lijn automatisch wordt geactiveerd en alleen de nieuwe bestanden worden gekopieerd naar het doel archief.

13. Selecteer het tabblad **monitor** in het navigatie paneel aan de linkerkant en wacht ongeveer 15 minuten als het terugkeer patroon van de trigger is ingesteld op elke 15 minuten. 

14. Bekijk het resultaat. U ziet dat de pijp lijn automatisch om de 15 minuten wordt geactiveerd en alleen de nieuwe of gewijzigde bestanden uit het bron archief worden gekopieerd naar het doel archief in elke pijplijn uitvoering.

    ![Scherm opname van de resultaten die worden geretourneerd wanneer de pijp lijn wordt geactiveerd.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
