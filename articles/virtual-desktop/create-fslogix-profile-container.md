---
title: FSLogix-profiel containers NetApp Windows virtueel bureau blad-Azure
description: Een FSLogix-profiel container maken met behulp van Azure NetApp Files in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016825"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Een profiel container maken met Azure NetApp Files en AD DS

We raden u aan om FSLogix-profiel containers als een gebruikers profiel oplossing te gebruiken voor de [virtueel-bureaublad service van Windows](overview.md). FSLogix-profiel containers slaan een volledig gebruikers profiel op in één container en zijn ontworpen om profielen te zwerven in niet-permanente, externe computer omgevingen zoals Windows virtueel bureau blad. Wanneer u zich aanmeldt, wordt de container dynamisch aan de computer omgeving gekoppeld met behulp van een lokaal ondersteunde virtuele harde schijf (VHD) en Hyper-V virtuele harde schijf (VHDX). Met deze geavanceerde filter technologieën kan het gebruikers profiel onmiddellijk beschikbaar zijn en in het systeem worden weer gegeven op dezelfde manier als een lokaal gebruikers profiel. Zie [FSLogix-profiel containers en Azure files](fslogix-containers-azure-files.md)voor meer informatie over FSLogix-profiel containers.

U kunt FSLogix-profiel containers maken met behulp van [Azure NetApp files](https://azure.microsoft.com/services/netapp/), een eenvoudig te gebruiken Azure systeem eigen platform service waarmee klanten snel en betrouwbaar SMB-volumes op bedrijfs niveau kunnen inrichten voor hun virtuele Windows-desktop omgevingen. Zie [Wat is Azure NetApp files?](../azure-netapp-files/azure-netapp-files-introduction.md) voor meer informatie over Azure NetApp files.

In deze hand leiding wordt uitgelegd hoe u een Azure NetApp Files account instelt en FSLogix profiel containers maakt in Windows virtueel bureau blad.

In dit artikel wordt ervan uitgegaan dat u al [hostgroepen](create-host-pools-azure-marketplace.md) hebt ingesteld en gegroepeerd in een of meer tenants in uw Windows Virtual Desktop-omgeving. Zie voor meer informatie over het instellen van tenants [een Tenant maken in het virtuele bureau blad van Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) en [onze technische community-blog post](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

De instructies in deze hand leiding zijn specifiek voor Windows-gebruikers met een virtueel bureau blad. Als u meer algemene richt lijnen wilt voor het instellen van Azure NetApp Files en het maken van FSLogix-profiel containers buiten Windows virtueel bureau blad, raadpleegt u de Snelstartgids voor het [instellen van Azure NetApp files en het maken van een NFS-volume](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>In dit artikel worden geen aanbevolen procedures beschreven voor het beveiligen van de toegang tot de Azure NetApp Files share.

>[!NOTE]
>Zie [opslag opties voor FSLogix-profiel containers](store-fslogix-profile.md)als u op zoek bent naar het vergelijkings materiaal over de verschillende FSLogix-profiel container opslag opties in Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u een FSLogix-profiel container voor een hostgroep kunt maken, moet u het volgende doen:

- Virtuele Windows-bureau blad instellen en configureren
- Een Windows-hostgroep voor virtueel bureau blad inrichten
- [Uw Azure NetApp Files-abonnement inschakelen](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Uw Azure NetApp Files-account instellen

Als u aan de slag wilt gaan, moet u een Azure NetApp Files-account instellen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zorg ervoor dat uw account Inzender-of beheerders machtigingen heeft.

2. Selecteer het **Azure Cloud shell pictogram** rechts van de zoek balk om Azure Cloud shell te openen.

3. Als Azure Cloud Shell is geopend, selecteert u **Power shell**.

4. Als dit de eerste keer is dat u Azure Cloud Shell gebruikt, maakt u een opslag account in hetzelfde abonnement dat u de Azure NetApp Files en het virtuele bureau blad van Windows bewaart.

   > [!div class="mx-imgBorder"]
   > ![Het venster opslag account met de knop opslag maken onder aan het venster is rood gemarkeerd.](media/create-storage-button.png)

5. Als Azure Cloud Shell is geladen, voert u de volgende twee cmdlets uit.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Selecteer in de linkerkant van het venster **alle services**. Voer **Azure NetApp files** in het zoekvak dat boven aan het menu wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname van een gebruiker die Azure NetApp Files invoert in het zoekvak alle services. In de zoek resultaten wordt de Azure NetApp Files resource weer gegeven.](media/azure-netapp-files-search-box.png)


7. Selecteer **Azure NetApp files** in de zoek resultaten en selecteer vervolgens **maken**.

8. Selecteer de knop **Add**.
9. Wanneer het tabblad **Nieuw NetApp-account** wordt geopend, voert u de volgende waarden in:

    - Voer bij **naam** de naam van uw NetApp-account in.
    - Selecteer bij **abonnement** het abonnement voor het opslag account dat u in stap 4 hebt ingesteld in de vervolg keuzelijst.
    - Voor **resource groep** selecteert u een bestaande resource groep in de vervolg keuzelijst of maakt u een nieuwe, door **Nieuw maken** te selecteren.
    - Selecteer bij **locatie** de regio voor uw NetApp-account in de vervolg keuzelijst. Deze regio moet dezelfde regio zijn als de Vm's van de host van uw sessie.

   >[!NOTE]
   >Azure NetApp Files biedt momenteel geen ondersteuning voor het koppelen van een volume in verschillende regio's.

10. Wanneer u klaar bent, selecteert u **maken** om uw NetApp-account te maken.

## <a name="create-a-capacity-pool"></a>Een capaciteits pool maken

Maak vervolgens een nieuwe capaciteits groep:

1. Ga naar het menu Azure NetApp Files en selecteer uw nieuwe account.
2. Selecteer in uw account menu de optie **capaciteits Pools** onder Storage service.
3. Selecteer **groep toevoegen**.
4. Wanneer het tabblad **nieuwe capaciteits groep** wordt geopend, voert u de volgende waarden in:

    - Voer bij **naam** een naam in voor de nieuwe capaciteits groep.
    - Selecteer voor **service niveau** de gewenste waarde in de vervolg keuzelijst. We raden **Premium** aan voor de meeste omgevingen.
       >[!NOTE]
       >De Premium-instelling biedt de minimale door Voer die beschikbaar is voor een Premium-service niveau, 256 MBps. Mogelijk moet u deze door Voer aanpassen voor een productie omgeving. De laatste door Voer is gebaseerd op de relatie die wordt beschreven in de [doorvoer limieten](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Voer voor **grootte (Tib)** de capaciteits pool grootte in die het beste bij uw behoeften past. De minimale grootte is 4 TiB.

5. Klik op **OK** wanneer u klaar bent.

## <a name="join-an-active-directory-connection"></a>Een Active Directory verbinding maken

Daarna moet u lid worden van een Active Directory verbinding.

1. Selecteer **Active Directory verbindingen** in het menu aan de linkerkant van de pagina en selecteer vervolgens de knop **samen voegen** om de pagina **lid worden** van de Active Directory te openen.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname van het menu verbinding maken Active Directory.](media/active-directory-connections-menu.png)

2. Voer de volgende waarden in op de pagina **lid worden Active Directory** om lid te worden van een verbinding:

    - Voor **primaire DNS** voert u het IP-adres van de DNS-server in uw omgeving in die de domein naam kan omzetten.
    - Voer uw Fully Qualified Domain Name (FQDN) in voor het **domein**.
    - Voer voor het voor voegsel van de **SMB-server (computer-account)** de teken reeks in die u wilt toevoegen aan de naam van het computer account.
    - Voer bij **gebruikers naam** de naam in van het account met machtigingen voor het uitvoeren van een domein deelname.
    - Voer bij **wacht woord** het wacht woord van het account in.

## <a name="create-a-new-volume"></a>Een nieuw volume maken

Vervolgens moet u een nieuw volume maken.

1. Selecteer **volumes** en selecteer vervolgens **volume toevoegen**.

2. Wanneer het tabblad **een volume maken** wordt geopend, voert u de volgende waarden in:

    - Voer voor **volume naam** een naam in voor het nieuwe volume.
    - Voor de **capaciteits pool** selecteert u de capaciteits groep die u zojuist hebt gemaakt in de vervolg keuzelijst.
    - Voer bij **quotum (GIB)** de grootte van het benodigde volume in voor uw omgeving.
    - Voor **virtueel netwerk** selecteert u een bestaand virtueel netwerk dat is verbonden met de domein controller in de vervolg keuzelijst.
    - Selecteer onder **subnet** de optie **nieuwe maken**. Houd er rekening mee dat dit subnet wordt gedelegeerd aan Azure NetApp Files.

3.  Selecteer **volgende: protocol \> \>** om het tabblad Protocol te openen en uw volume toegangs parameters in te stellen.

## <a name="configure-volume-access-parameters"></a>Volume toegangs parameters configureren

Nadat u het volume hebt gemaakt, configureert u de volume toegangs parameters.

1.  Selecteer **SMB** als protocol type.
2.  Selecteer onder configuratie in de vervolg keuzelijst **Active Directory** de map waarin u oorspronkelijk verbinding hebt gemaakt in [lid worden van een Active Directory-verbinding](create-fslogix-profile-container.md#join-an-active-directory-connection). Houd er wel voor dat er een limiet van één Active Directory per abonnement is.
3.  Voer in het tekstvak **share naam** de naam in van de share die wordt gebruikt door de sessiehost en de gebruikers.

4.  Selecteer **Controleren en maken** onderaan de pagina. Hiermee opent u de pagina validatie. Nadat het volume is gevalideerd, selecteert u **maken**.

5.  Op dit moment zal het nieuwe volume worden geïmplementeerd. Zodra de implementatie is voltooid, kunt u de Azure NetApp Files share gebruiken.

6.  Als u het koppelingspad wilt zien, selecteert u **naar resource gaan** en zoekt u deze op het tabblad Overzicht.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van het overzichts scherm met een rode pijl die verwijst naar het koppelingspad.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>FSLogix configureren op virtuele machines in de sessiehost (Vm's)

Deze sectie is gebaseerd op het [maken van een profiel container voor een hostgroep met een bestands share](create-host-pools-user-profile.md).

1. [Down load het bestand FSLogix agent. zip](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) terwijl u nog steeds extern bent in de host-VM van de sessie.

2. Pak het gedownloade bestand uit.

3. Ga in het bestand naar **x64**-  >  **releases** en voer **FSLogixAppsSetup.exe** uit. Het installatie menu wordt geopend.

4.  Als u een product code hebt, voert u deze in het tekstvak product code in.

5. Schakel het selectie vakje in bij **Ik ga akkoord met de licentie voorwaarden**.

6. Selecteer **Installeren**.

7. Navigeer naar **C: \\ Program Files \\ FSLogix \\ apps** om te bevestigen dat de agent is geïnstalleerd.

8. Voer in het menu Start **regedit** als Administrator uit.

9. Navigeer naar **Computer \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.

10. Maak een sleutel met de naam **profielen**.

11.  Maak een waarde met de naam **ingeschakeld** met een **REG_DWORD** type ingesteld op een gegevens waarde van **1**.

12. Maak een waarde met de naam **VHDLocations** met een type met **meerdere teken reeksen** en stel de gegevens waarde in op de URI voor de Azure NetApp Files share.

13. Maak een waarde met de naam **DeleteLocalProfileWhenVHDShouldApply** met de DWORD-waarde 1 om problemen met bestaande lokale profielen te voor komen voordat u zich aanmeldt.

     >[!WARNING]
     >Wees voorzichtig bij het maken van de waarde DeleteLocalProfileWhenVHDShouldApply. Wanneer het FSLogix profielen systeem bepaalt dat een gebruiker een FSLogix-profiel moet hebben, maar er al een lokaal profiel bestaat, zal de profiel container het lokale profiel permanent verwijderen. De gebruiker wordt vervolgens aangemeld met het nieuwe FSLogix-profiel.

## <a name="assign-users-to-session-host"></a>Gebruikers toewijzen aan de sessiehost

1. Open **Power shell ISE** als beheerder en meld u aan bij Windows virtueel bureau blad.

2. Voer de volgende cmdlets uit:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Wanneer u om referenties wordt gevraagd, voert u de referenties in voor de gebruiker met de rol Tenant maker of RDS-eigenaar/RDS-Inzender op de Windows Virtual Desktop-Tenant.

4. Voer de volgende cmdlets uit om een gebruiker toe te wijzen aan een Extern bureaublad groep:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Zorg ervoor dat gebruikers toegang hebben tot de bestands share van Azure NetApp

1. Open uw Internet browser en ga naar <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Meld u aan met de referenties van een gebruiker die is toegewezen aan de Extern bureaublad groep.

3. Nadat u de gebruikers sessie tot stand hebt gebracht, meldt u zich aan bij de Azure Portal met een Administrator-account.

4. Open **Azure NetApp files**, selecteer uw Azure NetApp files account en selecteer vervolgens **volumes**. Zodra het menu volumes wordt geopend, selecteert u het bijbehorende volume.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname van het NetApp-account dat u eerder hebt ingesteld in de Azure Portal met de knop volumes geselecteerd.](media/netapp-account.png)

5. Ga naar het tabblad **overzicht** en controleer of in de FSLogix-profiel container ruimte wordt gebruikt.

6. Maak rechtstreeks verbinding met een VM-onderdeel van de hostgroep met Extern bureaublad en open de **bestanden Verkenner.** Ga vervolgens naar het **koppelingspad** (in het volgende voor beeld is het koppelingspad \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ ANF vol).

   In deze map moet er een VHD (of VHDX) van het profiel staan, zoals in het volgende voor beeld.

   > [!div class="mx-imgBorder"]
   > ![Een scherm afbeelding van de inhoud van de map in het koppelingspad. In is één VHD-bestand met de naam ' Profile_ssbb '.](media/mount-path-folder.png)

## <a name="next-steps"></a>Volgende stappen

U kunt FSLogix-profiel containers gebruiken om een gebruikers profiel share in te stellen. Zie [een profiel container maken voor een hostgroep met een bestands share voor](create-host-pools-user-profile.md)meer informatie over het maken van gebruikers profiel shares met uw nieuwe containers.

U kunt ook een Azure Files bestands share maken om uw FSLogix-profiel op te slaan in. Zie [een Azure files bestands share maken met een domein controller](create-file-share.md)voor meer informatie.