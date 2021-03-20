---
title: 'Zelfstudie: Gegevenskopieerservice gebruiken om naar uw apparaat te kopiëren'
titleSuffix: Azure Data Box
description: In deze zelfstudie leert u hoe u de gegevenskopieerservice gebruikt om gegevens naar uw Azure Data Box-apparaat te kopiëren
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: e664055893bbdef0f7090811b8a160a1b8a4a1fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92124045"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Zelfstudie: De gegevenskopieerservice gebruiken om gegevens direct op te nemen in Azure Data Box (preview)

In deze zelfstudie wordt beschreven hoe u gegevens opneemt met behulp van de gegevenskopieerservice, zonder gebruik te maken van een tussenliggende host. De gegevenskopieerservice wordt lokaal uitgevoerd op Microsoft Azure Data Box, maakt via SMB verbinding met uw NAS-apparaat (network-attached storage) en kopieert gegevens naar Data Box.

De gegevenskopieerservice gebruiken:

- In NAS-omgevingen waar tussenliggende hosts mogelijk niet beschikbaar zijn.
- Met kleine bestanden waarvoor soms weken nodig zijn om gegevens op te nemen en te uploaden. Met de gegevenskopieerservice wordt de opname- en uploadtijd voor kleine bestanden aanzienlijk verbeterd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Gegevens kopiëren naar Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt deze zelfstudie voltooid: [Azure Data Box instellen](data-box-deploy-set-up.md).
2. U hebt uw Data Box-apparaat ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt de referenties van het bron-NAS-apparaat waarmee u verbinding wilt maken om gegevens te kopiëren.
4. U bent verbonden met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minimaal één verbinding van 10-GbE. Als er geen 10 GbE-verbinding beschikbaar is, kunt u een 1 GbE-gegevenskoppeling gebruiken, maar dit heeft wel gevolgen voor de kopieersnelheid.

## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Wanneer u bent verbonden met het NAS-apparaat, kunt u uw gegevens gaan kopiëren. Neem de volgende punten door voordat u gegevens gaat kopiëren:

* Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die worden vermeld in het artikel [Limieten voor Azure-opslag en Data Box](data-box-limits.md).

* Als de gegevens die door Data Box worden geüpload gelijktijdig door andere toepassingen buiten Data Box worden geüpload, kan dit tot fouten voor de uploadtaak en beschadigde gegevens leiden.

* Als de gegevens worden gewijzigd terwijl deze door de kopieerservice worden gelezen, is het mogelijk dat er fouten optreden of gegevens beschadigd raken.

> [!IMPORTANT]
> Zorg dat u een kopie van de brongegevens in uw on-premises omgeving hebt totdat u kunt bevestigen dat de gegevens met Data Box naar Azure Storage zijn overgebracht.

U moet een taak maken als u gegevens wilt kopiëren met behulp van de gegevenskopieerservice:

1. Ga in de lokale webgebruikersinterface van uw Data Box-apparaat naar **Beheren** > **Gegevens kopiëren**.
2. Selecteer op de pagina **Gegevens kopiëren** de optie **Maken**.

    ![Maken selecteren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Voer in het dialoogvenster **Taak configureren en starten** de volgende velden in:
    
    |Veld                          |Waarde    |
    |-------------------------------|---------|
    |**Taaknaam**                       |Een unieke naam van maximaal 230 tekens voor de taak. De volgende tekens zijn niet toegestaan in de naam van de taak: \<, \>, \|, \?, \*, \\, \:, \/ en \\\.         |
    |**Bronlocatie**                |Geef het SMB-pad naar de gegevensbron op in de indeling: `\\<ServerIPAddress>\<ShareName>` of `\\<ServerName>\<ShareName>`.        |
    |**Gebruikersnaam**                       |Gebruikersnaam in de notatie `\\<DomainName><UserName>` voor toegang tot de gegevensbron. Wanneer een lokale beheerder verbinding maakt, moet hij expliciete beveiligingsmachtigingen hebben. Klik met de rechtermuisknop op de map, selecteer **Eigenschappen** en vervolgens **Beveiliging**. Hiermee wordt de lokale beheerder toegevoegd in het tabblad **Beveiliging**.       |
    |**Wachtwoord**                       |Wachtwoord voor toegang tot de gegevensbron.           |
    |**Doelopslagaccount**    |Selecteer in de lijst het doelopslagaccount waarnaar u de gegevens wilt uploaden.         |
    |**Doeltype**       |Selecteer het doelopslagtype in de lijst: **Blok-blob**, **pagina-blob** of **Azure Files**.        |
    |**Doelcontainer/-share**    |Voer de naam in van de container of share waarnaar u gegevens wilt uploaden in uw doelopslagaccount. De naam kan een sharenaam of een containernaam zijn. Gebruik bijvoorbeeld `myshare` of `mycontainer`. U kunt de naam ook invoeren in de indeling `sharename\directory_name` of `containername\virtual_directory_name`.        |
    |**Bestanden kopiëren die overeenkomen met het patroon**    | U kunt het bestandsnaampatroon invoeren op de volgende twee manieren:<ul><li>**Met jokertekenexpressies:** Alleen `*` en `?` worden ondersteund in expressies met jokertekens. De expressie `*.vhd` bijvoorbeeld komt overeen met alle bestanden die de extensie `.vhd` hebben. En zo komt `*.dl?` overeen met alle bestanden die de extensie `.dl` hebben of die beginnen met `.dl`, zoals `.dll`. En zo komt `*foo` overeen met alle bestanden waarvan de bestandsnaam eindigt op `foo`.<br>U kunt de expressie met jokertekens rechtstreeks invoeren in het veld. Standaard wordt de waarde die u in het veld invoert, beschouwd als een expressie met jokertekens.</li><li>**Met reguliere expressies:** Op POSIX gebaseerde reguliere expressies worden ondersteund. De reguliere expressie `.*\.vhd` bijvoorbeeld komt overeen met alle bestanden die de extensie `.vhd` hebben. Voor reguliere expressies geeft u het `<pattern>` rechtstreeks op als `regex(<pattern>)`. Voor meer informatie over reguliere expressies gaat u naar [Regular expression language - a quick reference](/dotnet/standard/base-types/regular-expression-language-quick-reference) (Reguliere expressies - een snelzoekgids).</li><ul>|
    |**Bestandsoptimalisatie**              |Als deze functie is ingeschakeld, worden bestanden die kleiner zijn dan 1 MB tijdens de opname verpakt. Dit verpakken versnelt het kopiëren van gegevens voor kleine bestanden. Het bespaart ook aanzienlijk veel tijd wanneer het aantal bestanden veel groter is dan het aantal directory’s.        |
 
4. Selecteer **Starten**. De invoer wordt gevalideerd en als de validatie is geslaagd, wordt de taak gestart. Het kan enkele minuten duren voordat de taak start.

    ![Een taak starten vanuit het dialoogvenster De taak configureren en starten](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Er wordt een taak gemaakt met de opgegeven instellingen. U kunt een taak onderbreken, hervatten, annuleren of opnieuw starten. Schakel het selectievakje in naast de taaknaam en selecteer de gewenste knop.

    ![Een taak beheren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - U kunt een taak onderbreken als deze tijdens piekuren invloed heeft op de resources van het NAS-apparaat:

        ![Een taak onderbreken op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        U kunt de taak later, na de piekuren, hervatten:

        ![Een taak hervatten op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - U kunt een taak op elk gewenst moment annuleren:

        ![Een taak annuleren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Wanneer u een taak annuleert, moet u dit bevestigen:

        ![Annulering van taak bevestigen](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Als u besluit om een taak te annuleren, worden de gegevens die al zijn gekopieerd, niet verwijderd. Als u gegevens wilt verwijderen die u naar uw Data Box-apparaat hebt gekopieerd, moet u het apparaat opnieuw instellen.

        ![Een apparaat opnieuw instellen](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Als u een taak annuleert of onderbreekt, is het mogelijk dat grote bestanden slechts gedeeltelijk zijn gekopieerd. Deze gedeeltelijk gekopieerde bestanden worden in dezelfde staat geüpload naar Azure. Wanneer u een taak annuleert of onderbreekt, moet u controleren of uw bestanden correct zijn gekopieerd. U kunt de bestanden valideren door de SMB-shares te bekijken of het BOM-bestand te downloaden.

    - U kunt een taak opnieuw starten als deze is mislukt vanwege een tijdelijke fout, zoals een netwerkfout. U kunt een taak echter niet opnieuw starten als deze een definitieve status heeft bereikt, zoals **Voltooid** of **Voltooid met fouten**. Taakfouten kunnen worden veroorzaakt door problemen met bestandsnamen of -grootten. Deze fouten worden vastgelegd in een logboek, maar de taak kan pas opnieuw worden gestart nadat deze is voltooid.

        ![Een mislukte taak opnieuw starten](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Als er een fout optreedt en u de taak niet opnieuw kunt starten, download dan de foutenlogboeken en zoek de fout op in de logboekbestanden. Wanneer u het probleem hebt opgelost, maakt u een nieuwe taak om de bestanden te kopiëren. U kunt ook [de bestanden kopiëren via SMB](data-box-deploy-copy-data.md).
    
    - In deze release kunt u een taak niet verwijderen.
    
    - U kunt een onbeperkt aantal taken maken, maar u kunt maximaal 10 taken tegelijkertijd uitvoeren.
    - Als **Bestandsoptimalisatie** is ingeschakeld, worden kleine bestanden ingepakt bij opname om kopieerprestaties te verbeteren. In dergelijke gevallen wordt er een ingepakte bestand weergegeven (met een GUID als bestandsnaam). Verwijder dit bestand niet. Dit wordt uitgepakt tijdens het uploaden.

6. Terwijl de taak wordt uitgevoerd, ziet u het volgende op de pagina **Gegevens kopiëren**:

    - In de kolom **Status** ziet u de status van de kopieertaak. De status kan zijn:
        - **Wordt uitgevoerd**
        - **Mislukt**
        - **Geslaagd**
        - **Wordt onderbroken**
        - **Onderbroken**
        - **Wordt geannuleerd**
        - **Geannuleerd**
        - **Voltooid met fouten**
    - In de kolom **Bestanden** ziet u het aantal en de totale grootte van de bestanden die worden gekopieerd.
    - In de kolom **Verwerkt** ziet u het aantal en de totale grootte van de bestanden die worden verwerkt.
    - Selecteer in de kolom **Taakdetails** de optie **Weergeven** als u de taakdetails wilt bekijken.
    - Als er fouten optreden tijdens het kopieerproces, wat wordt weergegeven in de kolom **# fouten**, ga dan naar de kolom **Foutenlogboek** en download de foutenlogboeken om het probleem op te lossen.

Wacht tot de kopieertaak is voltooid. Omdat een aantal fouten alleen wordt vastgelegd op de pagina **Verbinding maken en kopiëren**, moet u wachten tot de kopieertaak zonder fouten is voltooid voordat u verder gaat met de volgende stap.

![Geen fouten op de pagina Verbinding maken en kopiëren](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Om de gegevensintegriteit te garanderen wordt een controlesom inline berekend terwijl de gegevens worden gekopieerd. Selecteer na het kopiëren de optie **Dashboard weergeven** om de gebruikte ruimte en vrije ruimte op uw apparaat te controleren.
    
![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Wanneer de kopieertaak is voltooid, kunt u **Voorbereiding voor verzending** selecteren.

>[!NOTE]
> **Voorbereiding voor verzending** kan niet worden uitgevoerd terwijl de kopieertaken worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box-apparaat terugstuurt naar Microsoft.

> [!div class="nextstepaction"]
> [Uw Azure Data Box terugsturen naar Microsoft](./data-box-deploy-picked-up.md)