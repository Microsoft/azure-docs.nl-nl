---
title: Microsoft Azure StorSimple Data Manager gebruikers interface
description: Meer informatie over hoe u de StorSimple Data Manager-gebruikers interface kunt gebruiken om de gegevens op de StorSimple 8000 Series-apparaten te transformeren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957925"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>De StorSimple Data Manager-service beheren in Azure Portal

In dit artikel wordt uitgelegd hoe u de StorSimple Data Manager-gebruikers interface kunt gebruiken om de gegevens op de StorSimple 8000 Series-apparaten te transformeren. De getransformeerde gegevens kunnen vervolgens worden gebruikt door andere Azure-Services, zoals Azure Media Services, Azure HDInsight, Azure Machine Learning en Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>StorSimple-gegevens transformatie gebruiken

De StorSimple Data Manager is de resource waarbinnen gegevens transformatie wordt geïnstantieerd. Met de service voor gegevens transformatie kunt u gegevens van de StorSimple-indeling omzetten in een systeem eigen indeling in blobs of Azure Files. Als u de StorSimple systeem eigen indelings gegevens wilt transformeren, moet u de details van uw StorSimple 8000-serie apparaat opgeven en de gegevens van belang die u wilt transformeren.

### <a name="create-a-storsimple-data-manager-service"></a>Een StorSimple Data Manager-service maken

Voer de volgende stappen uit om een StorSimple Data Manager-service te maken.

1. Gebruik de referenties van uw Microsoft-account om u aan te melden bij [Azure Portal](https://portal.azure.com/).

2. Klik op **+ een resource maken** en zoek naar StorSimple Data Manager.

    ![Een StorSimple Data Manager-service maken 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klik op StorSimple Data Manager en klik vervolgens op **maken**.
    
    ![Een StorSimple Data Manager-service maken 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Voor de nieuwe service, geeft u het volgende op:

   1. Geef een unieke **service naam** op voor uw StorSimple Data Manager. Dit is een beschrijvende naam die kan worden gebruikt om de service te identificeren. De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). De naam moet beginnen en eindigen met een letter of cijfer.

   2. Kies een **abonnement** in de vervolg keuzelijst. Het abonnement is gekoppeld aan uw factureringsrekening. Dit veld wordt automatisch ingevuld (en kan niet worden geselecteerd) als u slechts één abonnement hebt.

   3. Selecteer een bestaande resource groep of maak een nieuwe groep. Zie [Azure-resourcegroepen](../azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie.

   4. Geef de **locatie** voor uw service op die uw opslag accounts en uw StorSimple Data Manager-service inneemt. Uw StorSimple-Apparaatbeheer service, Data Manager-service en het bijbehorende opslag account moeten allemaal in de ondersteunde regio's staan.
    
   5. Als u een koppeling naar deze service op uw dash board wilt ontvangen, selecteert **u vastmaken aan dash board**.
    
   6. Klik op **Create**.

      ![Een StorSimple Data Manager-service maken 3](./media/storsimple-data-manager-ui/create-service-4.png)

Het maken van de service duurt enkele minuten. U ziet een melding nadat de service is gemaakt en de nieuwe service wordt weer gegeven.

### <a name="create-a-data-transformation-job-definition"></a>Een definitie van een taak voor gegevenstransformatie maken

Binnen een StorSimple Data Manager-service moet u een definitie van een gegevens transformatie taak maken. Een taak definitie bevat details van de StorSimple-gegevens die u wilt verplaatsen naar een opslag account in de oorspronkelijke indeling. Als u een taak definitie hebt gemaakt, kunt u deze taak opnieuw uitvoeren met verschillende runtime-instellingen.

Voer de volgende stappen uit om een taak definitie te maken.

1. Ga naar de service die u hebt gemaakt. Ga naar **beheer > taak definities**.

2. Klik op **+ taak definitie**.

    ![Klik op + taak definitie](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Geef een naam op voor de taak definitie. De naam kan tussen de 3 en 63 tekens lang zijn. De naam kan hoofd letters en kleine letters, cijfers en afbreek streepjes bevatten.

4. Geef een locatie op waar de taak wordt uitgevoerd. Deze locatie kan afwijken van de locatie waar de service wordt geïmplementeerd.

5. Klik op **bron** om de opslag plaats voor de bron gegevens op te geven.

    ![Bron gegevens opslag plaats configureren](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Omdat dit een nieuwe Data Manager-service is, zijn er geen opslag plaatsen voor gegevens geconfigureerd. In **gegevens bron configureren** geeft u de details op van uw StorSimple 8000 Series-apparaat en de gegevens van belang.

   Als u uw StorSimple Apparaatbeheer wilt toevoegen als gegevens opslagplaats, klikt u op **Nieuw toevoegen** in de vervolg keuzelijst gegevens opslagplaats en klikt u vervolgens op **Gegevens opslagplaats toevoegen**.

    ![Nieuwe gegevens opslag plaats toevoegen](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Kies **StorSimple 8000 Series Manager** als het type gegevens opslagplaats.
    
   2. Voer een beschrijvende naam in voor de opslag van de bron gegevens.
    
   3. Kies in de vervolg keuzelijst een abonnement dat is gekoppeld aan uw StorSimple-Apparaatbeheer service.
    
   4. Geef de naam op van de StorSimple-Apparaatbeheer voor de **resource**.

   5. Voer de **versleutelings sleutel voor service gegevens** voor de StorSimple-Apparaatbeheer service in. 

      ![Bron gegevens configureren opslag plaats 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klik op **OK** wanneer u klaar bent. Hiermee wordt uw gegevensopslag plaats opgeslagen. Gebruik deze StorSimple Apparaatbeheer in andere taak definities zonder deze para meters opnieuw in te voeren. Het duurt enkele seconden nadat u op **OK** hebt geklikt voor de zojuist gemaakte opslag plaats voor de bron gegevens die u in de vervolg keuzelijst wilt weer geven.

7. Selecteer in de vervolg keuzelijst voor **Gegevens opslagplaats** de gegevensopslag plaats die u hebt gemaakt. 

   1. Voer de naam in van het StorSimple 8000-serie apparaat dat de gegevens van belang bevat.

   2. Geef de naam op van het volume op het StorSimple-apparaat waarop uw gegevens van belang zijn.

   3. Voer in de Subsectie **filter** de hoofdmap in die de gegevens bevat die van belang zijn in de _\MyRootDirectory\Data_ -indeling. Stationsletters zoals _\c: \Data_ worden niet ondersteund. U kunt ook alle bestands filters toevoegen.

   4. De service voor gegevens transformatie werkt alleen met de laatste moment opname van de gegevens die naar Azure worden gepusht.

   5. Klik op **OK**.

      ![Bron gegevens configureren opslag plaats 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Vervolgens moet de doel gegevens opslagplaats worden geconfigureerd. Kies opslag accounts om bestanden in blobs in dat account in te voegen. Selecteer in de vervolg keuzelijst **nieuwe toevoegen** en **Configureer vervolgens instellingen**.

9. Selecteer het type doel opslagplaats dat u wilt toevoegen en de andere para meters die zijn gekoppeld aan de opslag plaats.

    Als u een type doel voor het opslag account selecteert, kunt u een beschrijvende naam, een abonnement (Kies hetzelfde als die van de service of andere) en een opslag account opgeven.
        ![Doel gegevens opslag plaats 1 configureren](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Er wordt een opslag wachtrij gemaakt wanneer de taak wordt uitgevoerd. Deze wachtrij wordt gevuld met berichten over getransformeerde blobs wanneer deze gereed zijn. De naam van deze wachtrij is hetzelfde als de naam van de taakdefinitie.
    
10. Nadat u de gegevens opslagplaats hebt toegevoegd, wacht u enkele minuten.
    
    1. Selecteer de opslag plaats die u als doel hebt gemaakt in de vervolg keuzelijst in de naam van het **doel account**.

    2. Kies het opslag type als blobs of bestanden. Geef de naam op van de opslag container waarin de getransformeerde gegevens zich bevinden. Klik op **OK**.

        ![Opslag plaats-opslag account voor doel gegevens configureren](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. U kunt ook de optie controleren om een schatting van de taak duur te presen teren voordat u de taak uitvoert. Klik op **OK** om de taak definitie te maken. De taak definitie is nu voltooid. U kunt deze taak definitie meerdere keren gebruiken via de gebruikers interface met verschillende runtime-instellingen.

    ![Taak definitie volt ooien](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    De zojuist gemaakte taak definitie wordt toegevoegd aan de lijst met taak definities voor deze service.

### <a name="run-the-job-definition"></a>De taakdefinitie uitvoeren

Wanneer u gegevens moet verplaatsen van StorSimple naar het opslag account dat u in de taak definitie hebt opgegeven, moet u het uitvoeren. Tijdens runtime kunnen sommige para meters anders worden opgegeven. De stappen zijn als volgt:

1. Selecteer uw StorSimple Data Manager-service en ga naar **beheer > taak definities**. Selecteer en klik op de taak definitie die u wilt uitvoeren.
     
     ![Taak uitvoeren 1 starten](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klik op **nu uitvoeren**.
     
     ![Taak 2 starten](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klik op **instellingen uitvoeren** om de instellingen te wijzigen die u mogelijk wilt wijzigen voor deze taak uitvoering. Klik op **OK** en klik vervolgens op **uitvoeren** om de taak te starten.

    ![Taak uitvoeren 3 starten](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Als u deze taak wilt bewaken, gaat u naar **taken** in uw StorSimple Data Manager. Naast bewaking op de Blade **taken** kunt u ook Luis teren naar de opslag wachtrij waar een bericht wordt toegevoegd telkens wanneer een bestand wordt verplaatst van StorSimple naar het opslag account.

    ![Taak uitvoeren 4 starten](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Logboeken weer geven na voltooiing van de taak

Nadat een taak is voltooid, kunt u de status van de taak bekijken. De taak status kan worden **geslaagd**, **gedeeltelijk geslaagd** en **mislukt**. U kunt de lijst weer geven met bestanden die zijn gekopieerd en bestanden die niet konden worden gekopieerd. Deze lijsten zijn beschikbaar in een container met de naam **' storsimple-Data-Manager-joblogs '** binnen uw doel-opslag account. In deze container kunt u zoeken naar een map met dezelfde naam als uw taak definitie. Binnen deze, wordt er een map gemaakt voor elke uitvoering van de taak die uw lijsten bevat. De naam van deze map is de GUID van de taak, die u kunt verkrijgen op de pagina met taak Details. In de meeste gevallen ziet u een koppeling voor de kopie logboeken op de pagina taken zelf.
Er zijn twee sets CSV-bestanden die in deze map worden weer geven. Alle bestanden die beginnen met **copiedfilelist...** , bevatten de lijst met correct gekopieerde bestanden. Alle bestanden die beginnen met **failedfilelist...** bevatten bestanden die niet konden worden gekopieerd, samen met een fout bericht.


## <a name="next-steps"></a>Volgende stappen

[Gebruik .NET SDK om StorSimple Data Manager taken te starten](storsimple-data-manager-dotnet-jobs.md).