---
title: Azure Data Box/Azure Data Box Heavy beheren met de lokale webgebruikersinterface
description: Hierin wordt beschreven hoe u de lokale web-UI gebruikt voor het beheren van uw Data Box en Data Box Heavy apparaten
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/03/2020
ms.author: alkohli
ms.openlocfilehash: 90869af032a381cecd3e65f5d5b367156dd047c5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576838"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Gebruik de lokale web-UI voor het beheren van uw Data Box en Data Box Heavy

In dit artikel worden enkele van de configuratie-en beheer taken beschreven die op Data Box en Data Box Heavy apparaten worden uitgevoerd. U kunt de Data Box en Data Box Heavy apparaten beheren via de Azure Portal gebruikers interface en de lokale webgebruikersinterface voor het apparaat. Dit artikel is gericht op taken die worden uitgevoerd met behulp van de lokale webgebruikersinterface.

De lokale web-UI voor de Data Box en voor Data Box Heavy wordt gebruikt voor de initiële configuratie van het apparaat. U kunt ook de lokale web-UI gebruiken om het apparaat af te sluiten of opnieuw op te starten, diagnostische tests uit te voeren, software bij te werken, de kopie logboeken te bekijken, lokale gegevens van het apparaat te wissen en een ondersteunings pakket voor Microsoft Ondersteuning te genereren. Op een Data Box Heavy apparaat met twee onafhankelijke knoop punten, hebt u toegang tot twee afzonderlijke lokale web-UIs die overeenkomen met elk knoop punt van het apparaat.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>Ondersteuningspakket genereren

Als u problemen met het apparaat ondervindt, kunt u een ondersteuningspakket maken vanuit het systeemlogboek. Microsoft Ondersteuning gebruikt dit pakket om het probleem op te lossen.

Als u een ondersteunings pakket wilt genereren, voert u de volgende stappen uit:

1. Ga in de lokale web-UI naar **contact opnemen met ondersteuning**. Selecteer eventueel **exclusief geheugen dump**. Selecteer vervolgens **ondersteunings pakket maken**.

    Een geheugen dump is de inhoud van het geheugen van uw apparaat, opgeslagen na een systeem fout.

    U moet de optie voor het **toevoegen van geheugen dump** niet selecteren, tenzij ondersteuning om een te vragen. Het duurt lang om een ondersteunings pakket te verzamelen dat een geheugen dump bevat, en gevoelige gegevens worden opgenomen.

    ![Ondersteuningspakket maken 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Er wordt een ondersteuningspakket verzameld. Deze bewerking duurt enkele minuten als u alleen systeem logboeken opneemt. Als u een geheugen dump opneemt, duurt het veel langer.

    ![Ondersteuningspakket maken 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Zodra het pakket is gemaakt, selecteert u het **Download ondersteunings pakket**.

    ![Ondersteunings pakket 3 maken](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Blader naar en kies de downloadlocatie. Open de map om de inhoud weer te geven.

    ![Ondersteuningspakket maken 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Lokale gegevens van uw apparaat wissen

U kunt de lokale web-UI gebruiken om lokale gegevens van uw apparaat te wissen voordat u deze terugstuurt naar het Azure-Data Center.

> [!IMPORTANT]
> Het wissen van gegevens kan niet ongedaan worden gemaakt. Maak een back-up van de bestanden voordat u lokale gegevens van het apparaat wist.

Voer de volgende stappen uit om lokale gegevens van uw apparaat te wissen:

1. Ga in de lokale web-UI naar **gegevens wissen**.
2. Voer het wacht woord van het apparaat in en selecteer **gegevens wissen**.

    ![Optie gegevens wissen voor een apparaat](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. Selecteer **Ja** in het bevestigings venster om door te gaan. Het wissen van gegevens kan tot 50 minuten duren.

   Zorg ervoor dat u een back-up maakt van uw lokale gegevens voordat u deze wist van het apparaat. Het wissen van gegevens kan niet ongedaan worden gemaakt.

    ![Prompt voor het bevestigen van gegevens wissen](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Apparaat uitschakelen of opnieuw opstarten

U kunt het apparaat afsluiten of opnieuw opstarten met behulp van de lokale webgebruikersinterface. U wordt aangeraden de shares offline te zetten op de host en vervolgens op het apparaat. Zo minimaliseert u de kans op beschadiging van gegevens. Zorg ervoor dat het kopiëren van de gegevens niet wordt uitgevoerd wanneer u het apparaat uitschakelt.

Voer de volgende stappen uit om uw apparaat af te sluiten.

1. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.

2. Selecteer **Afsluiten**.

    ![Data Box afsluiten 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Wanneer u om bevestiging wordt gevraagd, selecteert u **OK** om door te gaan.

    ![Data Box afsluiten 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Nadat het apparaat is uitgeschakeld, gebruikt u de aan-uitknop op het voorpaneel om het apparaat in te schakelen.

Voer de volgende stappen uit om uw Data Box opnieuw op te starten.

1. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.
2. Selecteer **opnieuw opstarten**.

    ![Data Box opnieuw opstarten 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Wanneer u om bevestiging wordt gevraagd, selecteert u **OK** om door te gaan.

   Het apparaat wordt afgesloten en opnieuw opgestart.

## <a name="download-bom-or-manifest-files"></a>Stuklijst- of manifestbestanden downloaden

De stuk lijst of de manifest bestanden bevatten de lijst met bestanden die zijn gekopieerd naar de Data Box of Data Box Heavy. Deze bestanden worden gegenereerd voor een import volgorde wanneer u het apparaat voorbereidt op verzen ding.

Voordat u begint, voert u de volgende stappen uit om stuk lijst-of manifest bestanden voor uw import volgorde te downloaden:

1. Ga naar de lokale web-UI voor uw apparaat. Controleer of het apparaat de **voorbereiding voor verzending** stap heeft voltooid. Nadat de voorbereiding van het apparaat is voltooid, wordt de status van het apparaat bijgewerkt naar **Gereed voor verzending**.

    ![Apparaat gereed voor verzending](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Selecteer **lijst met bestanden downloaden** om de lijst met bestanden te downloaden die op uw data box zijn gekopieerd.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. In Verkenner worden afzonderlijke lijsten met bestanden gegenereerd, afhankelijk van het protocol dat wordt gebruikt om verbinding te maken met het apparaat en het Azure Storage type dat wordt gebruikt.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Bestanden voor opslagtype en verbindingsprotocol](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   In de volgende tabel ziet u welke bestandsnamen horen bij welk type Azure Storage en gebruikt verbindingsprotocol.

    |Bestandsnaam  |Azure Storage-type  |Gebruikt verbindingsprotocol |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blok-blobs         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Pagina-blobs         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Pagina-blobs         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blok-blobs         |REST         |

U kunt deze lijst gebruiken om de bestanden die zijn geüpload naar het Azure Storage-account te controleren nadat de Data Box is geretourneerd naar het Azure-datacenter. Hieronder ziet u een voorbeeld van een manifestbestand.

> [!NOTE]
> Op een Data Box Heavy zijn twee sets lijst met bestanden (stuk lijst bestanden) aanwezig die overeenkomen met de twee knoop punten op het apparaat.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Dit bestand bevat de lijst met alle bestanden die zijn gekopieerd op de Data Box of Data Box Heavy. In dit bestand is de *crc64*-waarde gerelateerd aan de controlesom die is gegenereerd voor het bijbehorende bestand.

## <a name="view-available-capacity-of-the-device"></a>Beschikbare capaciteit van het apparaat weergeven

U kunt het apparaatdashboard gebruiken om de beschikbare en gebruikte capaciteit van het apparaat weer te geven.

1. Ga in de lokale webinterface naar **Dashboard weergeven**.
2. Onder **Verbinding maken en kopiëren** wordt de beschikbare en gebruikte ruimte op het apparaat weergegeven.

    ![Beschikbare capaciteit weergeven](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>De controlesomvalidatie overslaan

Er worden standaard controlesommen gegenereerd voor uw gegevens tijdens de voorbereiding voor verzending. In sommige zeldzame gevallen, afhankelijk van het gegevenstype (kleine bestandsgrootten), kan dit lang duren. In dergelijke gevallen kunt u de controlesom overslaan.

De berekening van de controlesom tijdens de voor bereiding op verzen ding wordt alleen uitgevoerd voor import orders en niet voor export orders.

We raden u ten zeerste aan de controlesom niet uit te scha kelen, tenzij de prestaties zwaar worden beïnvloed.

1. Ga in de rechter bovenhoek van de lokale web-UI van uw apparaat naar **instellingen**.

    ![Controlesom uitschakelen](media/data-box-local-web-ui-admin/disable-checksum.png)

2. Controlesomvalidatie **Uitschakelen**
3. Selecteer **Toepassen**.

> [!NOTE]
> De optie voor het berekenen van de controlesom overs laan is alleen beschikbaar wanneer de Azure Data Box is ontgrendeld. Deze optie wordt niet weer geven wanneer het apparaat is vergrendeld.

## <a name="enable-smb-signing"></a>SMB-ondertekening inschakelen

SMB-ondertekening (Server Message Block) is een functie waarmee communicatie met SMB digitaal kan worden ondertekend op pakket niveau. Deze ondertekening voor komt aanvallen waarbij SMB-pakketten tijdens de overdracht worden gewijzigd.

Zie [overzicht van Server Message Block Signing](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing)(Engelstalig) voor meer informatie over SMB-ondertekening.

SMB-ondertekening inschakelen in uw Azure-apparaat:

1. Selecteer in de rechter bovenhoek van de lokale web-UI van uw apparaat de optie **instellingen**.

    ![Instellingen openen](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Inschakelen** Ondertekenen van SMB.

    ![SMB-ondertekening inschakelen](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Selecteer **Toepassen**.
4. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.
5. Selecteer **opnieuw opstarten**.

## <a name="enable-backup-operator-privileges"></a>Bevoegdheden voor back-upoperator inschakelen

Uw webinterface gebruikers hebben standaard back-upoperator bevoegdheden op SMB-shares. Als u dit niet wilt, gebruikt u de bevoegdheid **back operator inschakelen** om de bevoegdheden uit te scha kelen of in te scha kelen.

Zie Back-upoperators in [Active Directory-beveiligings groepen](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators)voor meer informatie.

De bevoegdheden van een back-upoperator inschakelen op uw Azure-apparaat:

1. Selecteer in de rechter bovenhoek van de lokale web-UI van uw apparaat de optie **instellingen**.

   ![Open Data Box-instellingen-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Inschakelen** Bevoegdheden voor back-upoperators.

   ![Bevoegdheden voor back-upoperator inschakelen](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Selecteer Toep assen**.
4. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.
5. Selecteer **opnieuw opstarten**.

## <a name="enable-acls-for-azure-files"></a>Acl's inschakelen voor Azure Files

Meta gegevens voor bestanden worden standaard overgedragen wanneer gebruikers gegevens uploaden via SMB naar uw Data Box. De meta gegevens bevatten de toegangs beheer lijsten (Acl's), bestands kenmerken en tijds tempels. Als u dit niet wilt, gebruikt u **acl's voor Azure files** om deze functie uit te scha kelen of in te scha kelen.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Als u meta gegevens wilt overdragen met bestanden, moet u een back-upoperator zijn. Wanneer u deze functie gebruikt, moet u ervoor zorgen dat lokale gebruikers van de Web-UI back-upoperators zijn. Zie [bevoegdheden voor back-upoperator inschakelen](#enable-backup-operator-privileges).

De overdracht van Acl's voor Azure files inschakelen:

1. Selecteer in de rechter bovenhoek van de lokale web-UI van uw apparaat de optie **instellingen**.

    ![Open Data Box-instellingen-2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Inschakelen** Acl's voor Azure files.

     ![Acl's inschakelen voor Azure files](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Selecteer **Toepassen**.
4. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.
5. Selecteer **opnieuw opstarten**.

## <a name="enable-tls-11"></a>TLS 1,1 inschakelen

Azure Data Box maakt standaard gebruik van Transport Layer Security (TLS) 1,2 voor versleuteling omdat het veiliger is dan TSL 1,1. Als u of uw clients echter een browser gebruiken om toegang te krijgen tot gegevens die geen ondersteuning bieden voor TLS 1,2, kunt u TLS 1,1 inschakelen.

Zie [Azure data Box gateway Security](../databox-gateway/data-box-gateway-security.md)(Engelstalig) voor meer informatie over TLS.

TLS 1,1 inschakelen in uw Azure-apparaat:

1. Selecteer in de rechter bovenhoek van de lokale web-UI van uw apparaat de optie **instellingen**.

    ![Data Box instellingen openen-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Inschakelen** TLS 1,1.

    ![TLS 1,1 inschakelen](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Selecteer **Toepassen**.
4. Ga in de lokale webinterface naar **Afsluiten of opnieuw opstarten**.
5. Selecteer **opnieuw opstarten**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van de data box en het data Box Heavy via de Azure Portal](data-box-portal-admin.md).
