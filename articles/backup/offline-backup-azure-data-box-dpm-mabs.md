---
title: Offline back-up met Azure Data Box voor DPM en MABS
description: U kunt Azure Data Box gebruiken om de eerste back-upgegevens offline van DPM en MABS te maken.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752546"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Offline seeding met behulp van Azure Data Box voor DPM en MABS (preview)

> [!NOTE]
> Deze functie is van toepassing op Data Protection Manager (DPM) 2019 UR2 en hoger.<br><br>
> Deze functie is momenteel beschikbaar als preview-versie voor Microsoft Azure Backup-Server (MABS). Als u geïnteresseerd bent in het gebruik van Azure Data Box voor offline seeding met MABS, kunt u contact opnemen met ons op [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

In dit artikel wordt uitgelegd hoe u Azure Data Box kunt gebruiken om de eerste back-upgegevens offline te brengen vanuit DPM en MABS naar een Azure Recovery Services-kluis.

U kunt [Azure data Box](../databox/data-box-overview.md) gebruiken om uw grote initiële DPM/MABS-back-ups offline te brengen (zonder het netwerk te gebruiken) naar een Recovery Services kluis. Dit proces bespaart tijd en netwerk bandbreedte, waardoor een grote hoeveelheid back-upgegevens online kan worden verplaatst via een netwerk met een hoge latentie. Deze functie is momenteel beschikbaar als preview-product.

Offline back-up op basis van Azure Data Box biedt twee verschillende voor delen ten opzichte van [offline back-ups op basis van de Azure import/export-service](backup-azure-backup-server-import-export.md):

- U hoeft uw eigen Azure-compatibele schijven en connectors niet aan te schaffen. Azure Data Box stuurt de schijven die zijn gekoppeld aan de geselecteerde [Data Box SKU](https://azure.microsoft.com/services/databox/data/).

- Azure Backup (MARS-agent) kunnen back-upgegevens rechtstreeks naar de ondersteunde Sku's van Azure Data Box schrijven. Op deze manier hoeft u geen tijdelijke locatie in te richten voor de eerste back-upgegevens. U hebt ook geen hulpprogram ma's nodig om die gegevens op de schijven te Format teren en te kopiëren.

## <a name="supported-platforms"></a>Ondersteunde platforms

De volgende platforms worden ondersteund:

- Windows Server 2019 64 bits (Standard, Data Center, Essentials)
- Windows Server 2016 64 bits (Standard, Data Center, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Grootte van back-upgegevens en ondersteunde Data Box Sku's

De volgende Data Box Sku's worden ondersteund:

| Grootte van back-upgegevens (na compressie per MARS) \* per server | Ondersteund Azure Data Box SKU |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB en <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*Typische compressie tarieven variëren tussen 10-20% <br>
\*\*Neem contact op met [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) Als u verwacht dat u meer dan 80 TB aan initiële back-upgegevens voor één gegevens bron hebt.

> [!IMPORTANT]
> De eerste back-upgegevens van één gegevens bron moeten zich in één Azure Data Box of Azure Data Box schijf bevinden en kunnen niet worden gedeeld tussen meerdere apparaten van dezelfde of verschillende Sku's. Een Azure Data Box kan echter eerste back-ups van meerdere gegevens bronnen bevatten.

## <a name="before-you-begin"></a>Voordat u begint

De MARS-agent die wordt uitgevoerd op DPM-MABS moet worden bijgewerkt naar de [nieuwste versie](https://aka.ms/azurebackup_agent) (2.0.9171.0 of hoger).

Zorg ervoor dat:

### <a name="azure-subscription-and-required-permissions"></a>Azure-abonnement en de vereiste machtigingen

- Een geldig Azure-abonnement.
- De gebruiker die is bedoeld om het offline back-upbeleid uit te voeren, moet een eigenaar van het Azure-abonnement zijn.
- De Data Box-taak en de Recovery Services kluis waarnaar de gegevens moeten worden geseedd, moeten beschikbaar zijn in dezelfde abonnementen.
    > [!NOTE]
    > We raden aan dat het doel-opslag account en de Recovery Services kluis zich in dezelfde regio bevinden. Dit is echter niet verplicht.

### <a name="order-and-receive-the-data-box-device"></a>Bestel en ontvang het Data Box apparaat

Zorg ervoor dat de vereiste Data Box-apparaten de status *bezorgd* hebben voordat u offline back-ups gaat activeren. Bekijk de [grootte van back-upgegevens en ondersteunde data Box sku's](#backup-data-size-and-supported-data-box-skus) om de meest geschikte SKU voor uw vereiste te best Ellen. Volg de stappen in [dit artikel](../databox/data-box-disk-deploy-ordered.md) om uw data Box-apparaten te best Ellen en te ontvangen.

> [!IMPORTANT]
> Selecteer *BlobStorage* voor het **soort account** niet. Voor de DPM-MABS-server is een account vereist dat pagina-blobs ondersteunt. dit wordt niet ondersteund wanneer *BlobStorage* is geselecteerd. Selecteer  **opslag v2 (algemeen gebruik v2)** als het **soort account** bij het maken van het doel opslag account voor uw Azure data Box-taak.

![Azure databox instellen](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Azure Data Box-apparaten instellen

Wanneer u het Azure Data Box apparaat hebt ontvangen, moet u, afhankelijk van de Azure Data Box SKU die u hebt besteld, de volgende stappen uitvoeren om de Data Box apparaten in te stellen en voor te bereiden voor de DPM-MABS-server om de eerste back-upgegevens te identificeren en over te dragen.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box schijf instellen

Als u een of meer Azure Data Box schijven hebt besteld (Maxi maal 8 TB elk), volgt u de stappen die [hier](../databox/data-box-disk-deploy-set-up.md) worden beschreven om uw data Box schijf uit te pakken, te verbinden en te ontgrendelen.

> [!NOTE]
> Het is mogelijk dat de DPM-MABS-server geen USB-poort heeft. In een dergelijk scenario kunt u uw Azure Data Box-schijf aansluiten op een andere server/client en de hoofdmap van het apparaat beschikbaar maken als een netwerk share.

## <a name="setup-azure-data-box"></a>Azure Data Box instellen

Als u een Azure Data Box (tot 100 TB) hebt besteld, volgt u de stappen die [hier](../databox/data-box-deploy-set-up.md) worden beschreven om uw data box in te stellen.

### <a name="mount-your-azure-data-box-as-local-system"></a>Uw Azure Data Box als lokaal systeem koppelen

De DPM-MABS-server werkt in de systeem context en vereist daarom dat hetzelfde bevoegdheids niveau wordt gegeven aan het koppelingspad waarmee de Azure Data Box is verbonden. Volg de onderstaande stappen om ervoor te zorgen dat u uw Data Box-apparaat als lokaal systeem kunt koppelen met behulp van het NFS-protocol.

1. Schakel de functie client voor NFS op de DPM-MABS-server in.
Geef een alternatieve bron op: *Wim: D: \Sources\Install.Wim: 4*
2. Down load **PSExec** van [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) naar de DPM/MABS-server.
3. Open een opdracht prompt met verhoogde bevoegdheid en voer de volgende opdracht uit met de map met *PSExec.exe* als de huidige map.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. Het opdracht venster dat wordt geopend als gevolg van de bovenstaande opdracht bevindt zich in de context van het lokale systeem. Gebruik dit opdracht venster voor het uitvoeren van stappen voor het koppelen van de Azure page BLOB-share als een netwerk station op uw Windows-Server.
5. Volg de onderstaande [stappen om](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) uw DPM/MABS-server te verbinden met het data Box-apparaat via NFS en voer de volgende opdracht uit op de opdracht prompt van het lokale systeem om de share Azure-pagina-blobs te koppelen:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Nadat de verbinding is gekoppeld, controleert u of u toegang hebt tot X: vanaf uw server. Als dat mogelijk is, gaat u verder met de volgende sectie van dit artikel.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Initiële back-upgegevens overdragen naar Azure Data Box-apparaten

1. Volg op uw DPM-MABS-server de stappen voor het [maken van een nieuwe beveiligings groep](/system-center/dpm/create-dpm-protection-groups). Als u een online beveiliging toevoegt aan de bestaande beveiligings groep, klikt u met de rechter muisknop op de bestaande beveiligings groep en selecteert u **online beveiliging toevoegen** en beginnen bij **stap 8**.
2. Geef op de pagina **groeps leden selecteren** de computers en bronnen op waarvan u een back-up wilt maken.
3. Geef op de pagina **methode voor gegevens beveiliging selecteren** op hoe u back-ups voor de korte en de lange termijn wilt afhandelen. Zorg ervoor dat ik kies voor **online beveiliging.**

   ![Nieuwe beveiligings groep maken](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Op de pagina doelen voor de **korte termijn selecteren** geeft u op hoe u een back-up wilt maken naar kortetermijnbeveiliging op schijf.
5. Controleer op de pagina **schijf toewijzing controleren** de schijf ruimte voor de opslag groep die is toegewezen aan de beveiligings groep.
6. Selecteer op de pagina **methode voor maken van replica** selecteren **automatisch via het netwerk.**
7. Selecteer op de pagina **Opties voor consistentie controle kiezen** hoe u consistentie controles wilt automatiseren.
8. Selecteer op de pagina **online beveiligings gegevens opgeven** het lid waarvoor u online beveiliging wilt inschakelen.

    ![Gegevens voor online beveiliging opgeven](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Op de pagina **online back-upschema opgeven** geeft u op hoe vaak incrementele back-ups naar Azure moeten worden uitgevoerd.
10. Geef op de pagina **online retentie beleid opgeven** op hoe de herstel punten die zijn gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups, worden bewaard in Azure.
11. Kies in het scherm **online replicatie kiezen** van de wizard de optie **overdracht met behulp van micro soft-schijven** en selecteer **volgende**.

    ![Kies eerste online replicatie](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > De optie om overdracht te selecteren **met behulp van micro soft-schijven** is niet beschikbaar voor MABS v3, aangezien de functie in preview is. Neem contact met ons op [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) Als u deze functie voor MABS v3 wilt gebruiken.

12. Meld u aan bij Azure, wanneer u hierom wordt gevraagd, met behulp van de gebruikers referenties met de eigenaar van het Azure-abonnement. Nadat het aanmelden is geslaagd, wordt het volgende scherm weer gegeven:

    ![Na geslaagde aanmelding](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     De DPM-MABS-server haalt vervolgens de Data Box-taken op in het abonnement die de status *bezorgd* hebben.

     > [!NOTE]
     > De eerste keer dat het aanmelden langer duurt dan normaal. De module Azure PowerShell wordt op de achtergrond geïnstalleerd en ook de Azure AD-toepassing is geregistreerd.
     >
     >  - De volgende Power shell-modules zijn geïnstalleerd:<br>
          -AzureRM. profile     *5.8.3*<br>
          -AzureRM. resources   *6.7.3*<br>
          -AzureRM. Storage     *5.2.0*<br>
          -Azure. Storage       *4.6.1*<br>
     >  - De Azure AD-toepassing is geregistreerd *als \<object GUID of the user> AzureOfflineBackup_*.

13. Selecteer de juiste volg orde voor data vakken waarvoor u de Data Box schijf hebt uitgepakt, verbonden en ontgrendeld. Selecteer **Next**.

    ![Selecteer de databox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Voer in het scherm **de DataBox detecteren** het pad van uw data Box apparaat in en selecteer **apparaat detecteren**.

    ![Netwerkpad invoeren](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Geef het netwerkpad naar de hoofdmap van de Azure Data Box schijf op. Deze map moet een map met de naam *PageBlob* bevatten, zoals hieronder wordt weer gegeven:
    >
    > ![USB-station](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Als het pad van de schijf `\\mydomain\myserver\disk1\` en *Disk1* bijvoorbeeld een map bevat met de naam *PageBlob*, is het pad dat moet worden gegeven in de wizard DPM/MABS server `\\mydomain\myserver\disk1\` .
    > Als u [een Azure Data Box 100 TB-apparaat hebt ingesteld](./offline-backup-azure-data-box.md#set-up-azure-data-box), geeft u het volgende op als het netwerkpad naar het apparaat `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Selecteer **Next**. Controleer uw instellingen op de pagina **samen vatting** en selecteer **groep maken**.

    ![Databox detecteren](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    In het volgende scherm wordt bevestigd dat de beveiligings groep is gemaakt.

    ![Beveiligings groep gemaakt](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Selecteer **sluiten** op het bovenstaande scherm.

    Daarom vindt de initiële replicatie van de gegevens plaats op de DPM-MABS-schijf. Wanneer de beveiliging is voltooid, wordt in de groeps status de beveiligings status op de pagina **beveiliging** weer gegeven als **OK** .

17. Klik met de rechter muisknop op de **beveiligings groep** en kies vervolgens de optie **herstel punt maken** om de back-up van het offline maken naar uw Azure data Box-apparaat te initiëren. Vervolgens kiest u de optie voor **online beveiliging** .

    ![Herstel punt maken](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Herstel punt](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   De DPM-MABS-server begint met het maken van een back-up van de gegevens die u hebt geselecteerd voor het Azure Data Box apparaat. Dit kan enkele uren tot enkele dagen duren, afhankelijk van de grootte van de gegevens en de verbindings snelheid tussen de DPM/MABS-server en de Azure Data Box Disk.

   U kunt de status van de taak controleren in het deel venster **bewaking** . Wanneer de back-up van de gegevens is voltooid, ziet u een scherm dat er ongeveer als volgt uitziet:

   ![Administrator-console](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Stappen na de back-up

Volg deze stappen wanneer de back-up van de gegevens naar de Azure Data Box Disk is voltooid.

- Volg de stappen in [dit artikel](../databox/data-box-disk-deploy-picked-up.md) om de Azure data Box schijf naar Azure te verzenden. Als u een apparaat met Azure Data Box 100-TB hebt gebruikt, volgt u [deze stappen](../databox/data-box-deploy-picked-up.md) om de Azure data box naar Azure te verzenden.
- [Controleer de data Box taak](../databox/data-box-disk-deploy-upload-verify.md) in de Azure Portal. Zodra de Azure Data Box taak is *voltooid*, verplaatst de DPM-MABS-server de gegevens automatisch van het opslag account naar de Recovery Services kluis op het moment van de volgende geplande back-up. Als een herstel punt is gemaakt, wordt de back-uptaak als *voltooid* gemarkeerd.

  > [!NOTE]
  > De DPM-MABS-server activeert de back-ups op de tijden die zijn gepland tijdens het maken van de beveiligings groep. Met deze taken wordt echter *gewacht tot de Azure data Box taak is voltooid* tot het moment waarop de taak is voltooid.

- Nadat de DPM-MABS-server een herstel punt heeft gemaakt dat overeenkomt met de eerste back-up, kunt u het opslag account (of de specifieke inhoud) verwijderen die aan de Azure Data Box-taak is gekoppeld.

## <a name="troubleshooting"></a>Problemen oplossen

De Microsoft Azure Backup-Agent (MAB) op de DPM-server maakt een Azure AD-toepassing voor u, in uw Tenant. Voor deze toepassing is een certificaat vereist voor verificatie dat is gemaakt en geüpload bij het configureren van het offline seeding-beleid.

We gebruiken Azure PowerShell om het certificaat te maken en te uploaden naar de Azure AD-toepassing.

### <a name="issue"></a>Probleem

Op het moment van het configureren van offline back-ups, vanwege een bekend fout code in de cmdlet Azure PowerShell, kunt u niet meerdere certificaten toevoegen aan dezelfde Azure AD-toepassing die door de MAB-agent is gemaakt. Dit heeft gevolgen voor u als u een offline seeding-beleid voor dezelfde of een andere server hebt geconfigureerd.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Controleren of het probleem wordt veroorzaakt door deze specifieke hoofd oorzaak

Voer een van de volgende stappen uit om ervoor te zorgen dat de fout wordt veroorzaakt door het bovenstaande [probleem](#issue) :

#### <a name="step-1"></a>Stap 1

Controleer of het volgende fout bericht wordt weer gegeven in de DPM-MABS-console op het moment dat u offline back-up configureert:

![Azure Recovery Services-agent](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Stap 2

1. Open de map **temp** in het installatiepad (standaardpad voor tijdelijke mappen is *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Zoek naar het *CBUICurr* -bestand en open het bestand.
2. Ga in het *CBUICurr* -bestand naar de laatste regel en controleer of de fout is veroorzaakt door ' kan geen Azure AD-toepassings referenties maken in het account van de klant. Uitzonde ring: update naar een bestaande referentie met KeyId \<some guid> is niet toegestaan.

### <a name="workaround"></a>Tijdelijke oplossing

U kunt dit probleem oplossen door de volgende stappen uit te voeren en de beleids configuratie opnieuw uit te voeren.

1. Meld u aan bij de Azure-aanmeldings pagina die wordt weer gegeven in de gebruikers interface van de DPM-MABS-server met een ander account met beheerders toegang voor het abonnement waarvoor de Data Box-taak is gemaakt.
2. Als voor geen enkele andere server offline seeding is geconfigureerd en er geen andere server afhankelijk is van de `AzureOfflineBackup_<Azure User Id>` toepassing, verwijdert u deze toepassing uit **Azure Portal > Azure Active Directory > app-registraties**.

   > [!NOTE]
   > Controleer of er geen `AzureOfflineBackup_<Azure User Id>` andere offline seeding is geconfigureerd voor de toepassing en of er geen andere server afhankelijk is van deze toepassing. Ga naar **instellingen > sleutels** onder de sectie open bare sleutels. Er mogen geen andere **open bare sleutels** worden toegevoegd. Raadpleeg de volgende scherm afbeelding voor referentie:
   >
   > ![Open bare sleutels](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Stap 3

Voer de volgende acties uit vanaf de DPM-MABS-server die u wilt configureren voor offline back-ups:

1. Open het tabblad **computer certificaat toepassing beheren**  >   en zoek naar het certificaat met de naam `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Selecteer het certificaat hierboven, klik met de rechter muisknop op **alle taken** en **Exporteer** zonder persoonlijke sleutel, in de. CER-indeling.
3. Ga naar de Azure offline back-uptoepassing die wordt vermeld in **punt 2**. Upload het certificaat dat u in de bovenstaande stap hebt geëxporteerd in de sleutel **instellingen** voor het uploaden van de  >    >  **open bare sleutel** .

   ![Open bare sleutels uploaden](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Open het REGI ster in de-server door **regedit** te typen in het venster **uitvoeren** .
5. Ga naar het REGI ster *Computer\HKEY \_ Local \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Klik met de rechter muisknop op **CloudBackupProvider** en voeg een nieuwe teken reeks waarde toe met de naam `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Voer een van de volgende acties uit om de gebruikers-ID van Azure op te halen:
    >
    >- Voer de opdracht uit vanuit de met Azure verbonden Power shell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` .
    > - Navigeer naar het registerpad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` met de naam *CurrentUserId*.

6. Klik met de rechter muisknop op de teken reeks die u in de bovenstaande stap hebt toegevoegd en selecteer **wijzigen**. Geef in de waarde de vinger afdruk op van het certificaat dat u in **punt 2** hebt geëxporteerd en selecteer **OK**.
7. Als u de waarde van de vinger afdruk wilt ophalen, dubbelklikt u op het certificaat en selecteert u vervolgens **Details**  en schuift u totdat het veld vinger afdruk wordt weer geven. Selecteer **vinger afdruk** en kopieer de waarde.

   ![Vingerafdruk waarde](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Volgende stappen

- [Offline seeding met behulp van een eigen schijf (met Azure import/export-service)](backup-azure-backup-server-import-export.md)
