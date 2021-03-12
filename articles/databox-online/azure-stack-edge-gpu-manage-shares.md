---
title: Share beheer van Azure Stack Edge Pro GPU | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt om shares te beheren op uw Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b79cb1b195d35c1e25dd750476c0dacb296f8010
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637974"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Azure Portal gebruiken om shares te beheren op uw Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u shares kunt beheren op uw Azure Stack Edge Pro. U kunt de Azure Stack Edge Pro beheren via de Azure Portal of via de lokale web-UI. Gebruik de Azure Portal om shares toe te voegen, te verwijderen, te vernieuwen of de opslag sleutel te synchroniseren voor opslag accounts die zijn gekoppeld aan de shares. Dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.

## <a name="about-shares"></a>Info over shares

Als u gegevens wilt overdragen naar Azure, moet u shares maken op uw Azure Stack Edge Pro. De shares die u toevoegt op het Azure Stack Edge Pro-apparaat kunnen lokale shares zijn of shares die gegevens pushen naar de Cloud.

 - **Lokale shares**: gebruik deze shares wanneer u wilt dat de gegevens lokaal worden verwerkt op het apparaat.
 - **Shares**: gebruik deze shares als u wilt dat de apparaatgegevens automatisch naar uw opslag account in de cloud worden gepusht. Alle Cloud functies, zoals **vernieuwen** en **synchronisatie van opslag sleutels** , zijn van toepassing op de shares.


## <a name="add-a-share"></a>Een share toevoegen

Voer in de Azure Portal de volgende stappen uit om een share te maken.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar de **Cloud Storage gateway > shares**. Selecteer **+ share toevoegen** op de opdracht balk.

    ![Selecteer share toevoegen](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. Geef in **Share toevoegen** de instellingen voor de share op. Geef een unieke naam voor de share op.
    
    Namen van shares mogen alleen cijfers, kleine letters en afbreekstreepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een cijfer of letter beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.

3. Selecteer een **Type** voor de share. Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

4. Geef het **opslagaccount** op waar de share zich bevindt. In het opslagaccount met de naam van de share wordt een container gemaakt, als deze nog niet bestaat. Als de container al bestaat, wordt de bestaande container gebruikt.

5. Kies in de vervolg keuzelijst de **opslag service** van blok-blob, pagina-BLOB of bestanden. Het type service dat u kiest, is afhankelijk van de indeling waarin u de gegevens in Azure wilt opslaan. In dit geval willen we bijvoorbeeld dat de gegevens zich bevinden als blok-blobs in Azure. Daarom selecteren we **blok-BLOB**. Als u **pagina-BLOB** kiest, moet u ervoor zorgen dat uw gegevens 512 bytes zijn uitgelijnd. Gebruik de **pagina-BLOB** voor VHD'S of VHDX die altijd 512 bytes zijn uitgelijnd.

6. Deze stap hangt af van of u een SMB- of een NFS-share gaat maken.
    - **Als u een SMB-share maakt**: maak in het veld **All privilege local user** (Lokale gebruiker met alle bevoegdheden) een keuze uit **Nieuwe maken** of **Bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, geeft u de **gebruikersnaam** en het **wachtwoord** op en bevestigt u het wachtwoord. Hiermee worden de bevoegdheden aan de lokale gebruiker toegewezen. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.

        ![SMB-share toevoegen](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Als u ervoor kiest om alleen alleen-lezen bewerkingen toe te staan voor deze share-gegevens, kunt u gebruikers opgeven met alleen-lezen machtigingen.
    - **Als u een NFS-share maakt**: geef de **IP-adressen op van de clients** die toegang hebben tot de share.

        ![NFS-share toevoegen](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Gebruik het lokale koppel punt om eenvoudig toegang te krijgen tot de shares van Edge Compute-modules. Selecteer **de berekening delen met Edge gebruiken** zodat de share automatisch wordt gekoppeld nadat deze is gemaakt. Als deze optie is geselecteerd, kan de module Edge ook de compute met het lokale koppel punt gebruiken.

8. Klik op **maken** om de share te maken. U ontvangt een melding als wordt begonnen met het maken van de share. Nadat de share is gemaakt met de opgegeven instellingen, wordt de Blade **shares** bijgewerkt zodat deze overeenkomen met de nieuwe share.

## <a name="add-a-local-share"></a>Een lokale share toevoegen

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar de **Cloud Storage gateway > shares**. Selecteer **+ share toevoegen** op de opdracht balk.

    ![Selecteer share 2 toevoegen](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. Geef in **Share toevoegen** de instellingen voor de share op. Geef een unieke naam voor de share op.
    
    Share namen mogen alleen cijfers, kleine letters en cijfers en afbreek streepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een cijfer of letter beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.

3. Selecteer een **Type** voor de share. Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

   > [!IMPORTANT]
   > Controleer of voor het Azure-opslagaccount dat u gebruikt geen onveranderbaarheidsbeleid is ingesteld als u het account gebruikt in combinatie met een Azure Stack Edge Pro- of Data Box Gateway-apparaat. Zie [Beleid voor onveranderbaarheid instellen en beheren voor blobopslag](../storage/blobs/storage-blob-immutability-policies-manage.md) voor meer informatie.

4. Gebruik het lokale koppel punt om eenvoudig toegang te krijgen tot de shares van Edge Compute-modules. Selecteer **de berekening delen met Edge gebruiken** zodat de rand module de compute kan gebruiken met het lokale koppel punt.

5. Selecteer **configureren als Edge-lokale shares**. De gegevens in lokale shares blijven lokaal op het apparaat. U kunt deze gegevens lokaal verwerken.

6. Kies in het veld **alle bevoegdheden lokale gebruiker** de optie **nieuwe maken** of **bestaande gebruiken**.

7. Selecteer **Maken**. 

    ![Lokale share maken](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    U ziet een melding dat de share wordt gemaakt. Nadat de share is gemaakt met de opgegeven instellingen, wordt de Blade **shares** bijgewerkt zodat deze overeenkomen met de nieuwe share.

    ![Blade met updates shares weer geven](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Selecteer de share om de lokale koppel punt voor de Edge Compute-modules voor deze share weer te geven.

    ![Details van lokale share weer geven](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Een share koppelen

Als u een share hebt gemaakt voordat u Compute hebt geconfigureerd op uw Azure Stack Edge Pro-apparaat, moet u de share koppelen. Voer de volgende stappen uit om een share te koppelen.


1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar de **Cloud Storage gateway > shares**. Selecteer in de lijst met shares de share die u wilt koppelen. In de kolom **gebruikt voor berekening** wordt de status **uitgeschakeld** weer gegeven voor de geselecteerde share.

    ![Share selecteren](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. Selecteer **koppelen**.

    ![Koppeling selecteren](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Selecteer **Ja** als u om bevestiging wordt gevraagd. Hiermee wordt de share gekoppeld.

    ![Bevestig bevestiging](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. Nadat de share is gekoppeld, gaat u naar de lijst met shares. U ziet dat de kolom **gebruikt voor Compute** de status van de share als **ingeschakeld** weergeeft.

    ![Share gekoppeld](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. Selecteer de share opnieuw om de lokale koppel punt voor de share weer te geven. De Edge Compute-module maakt gebruik van deze lokale koppel punt voor de share.

    ![Lokale koppel punt voor de share](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Een share ontkoppelen

Voer de volgende stappen uit in de Azure Portal om een share te ontkoppelen.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar de **Cloud Storage gateway > shares**. Selecteer in de lijst met shares de share die u wilt ontkoppelen. U wilt er zeker van zijn dat de share die u ontkoppelt, niet wordt gebruikt door modules. Als de share wordt gebruikt door een module, worden er problemen met de bijbehorende module weer geven.

    ![Share 2 selecteren](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Selecteer **ontkoppelen**.

    ![Ontkoppelen selecteren](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Selecteer **Ja** als u om bevestiging wordt gevraagd. Hiermee wordt de share ontkoppeld.

    ![Ontkoppelen bevestigen](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. Wanneer de share is ontkoppeld, gaat u naar de lijst met shares. U ziet dat in de kolom **Compute** wordt weer gegeven dat de status van de share is **uitgeschakeld**.

    ![Delen niet gekoppeld](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Een share verwijderen

Voer in de Azure Portal de volgende stappen uit om een share te verwijderen.

1. Klik in de lijst met shares op de share die u wilt verwijderen.

    ![Share 3 selecteren](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. Klik op **Verwijderen**.

    ![Op Verwijderen klikken](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![De verwijdering bevestigen](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

De lijst met shares-updates om de verwijdering weer te geven.

## <a name="refresh-shares"></a>Shares vernieuwen

Met de functie voor vernieuwen kunt u de inhoud van een share vernieuwen. Wanneer u een share vernieuwt, wordt er een zoekopdracht gestart om de Azure-objecten te zoeken, inclusief blobs en bestanden die aan de cloud zijn toegevoegd na de laatste vernieuwing. Deze extra bestanden worden vervolgens gedownload om de inhoud van de share op het apparaat te vernieuwen.

> [!IMPORTANT]
> - U kunt geen lokale shares vernieuwen.
> - Machtigingen en toegangs beheer lijsten (Acl's) blijven niet behouden in een vernieuwings bewerking. 

Voer in de Azure Portal de volgende stappen uit om een share te vernieuwen.

1.  Ga in de Azure Portal naar **Shares**. Klik op de share die u wilt vernieuwen.

    ![Share 4 selecteren](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  Klik op **Vernieuwen**. 

    ![Op Vernieuwen klikken](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Klik op **Ja** als u om bevestiging wordt gevraagd. Er wordt een taak gestart om de inhoud van de on-premises share te vernieuwen.

    ![Vernieuwen bevestigen](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   Terwijl het vernieuwen wordt uitgevoerd, is de optie Vernieuwen niet beschikbaar in het contextmenu. Klik op de taakmelding om de status van de vernieuwingstaak weer te geven.

5.   Hoe lang de vernieuwing duurt is afhankelijk van het aantal bestanden in de Azure-container en van het aantal bestanden op het apparaat. Zodra het vernieuwen is voltooid, wordt het tijdstempel van de share bijgewerkt. Zelfs als de vernieuwing niet geheel zonder fouten is uitgevoerd, wordt de bewerking als voltooid beschouwd en wordt het tijdstempel bijgewerkt. De logboeken voor het vernieuwen van fouten worden ook bijgewerkt.

![Bijgewerkt tijdstempel](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
Als er een fout optreedt, wordt er een waarschuwing gegenereerd. De waarschuwing bevat meer informatie over de oorzaak en een aanbeveling om het probleem te verhelpen. De waarschuwing bevat ook een koppeling naar een bestand dat een volledig overzicht bevat van de fouten en van de bestanden die niet zijn bijgewerkt of verwijderd.

## <a name="sync-pinned-files"></a>Vastgemaakte bestanden synchroniseren 

Als u vastgemaakte bestanden automatisch wilt synchroniseren, voert u de volgende stappen uit in de Azure Portal:
 
1. Selecteer een bestaand Azure Storage-account. 

2. Ga naar **containers** en selecteer **+ container** om een container te maken. Geef deze container de naam *newcontainer*. Stel het **niveau van open bare toegang** in op container.

    ![Automatische synchronisatie voor vastgemaakte bestanden 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Selecteer de naam van de container en stel de volgende meta gegevens in:  

    - Name = "vastgemaakt" 
    - Waarde = "True" 

    ![Automatische synchronisatie voor vastgemaakte bestanden 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Maak een nieuwe share op het apparaat. Wijs deze toe aan de vastgemaakte container door de optie bestaande container te kiezen. Markeer de share als alleen-lezen. Maak een nieuwe gebruiker en geef de gebruikers naam en het bijbehorende wacht woord op voor deze share.  

    ![Automatische synchronisatie voor vastgemaakte bestanden 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. Blader vanuit het Azure Portal naar de container die u hebt gemaakt. Upload het bestand dat u wilt vastmaken aan de newcontainer waarvoor de meta gegevens zijn ingesteld op vastgemaakt. 

6. Selecteer **gegevens vernieuwen** in azure portal voor het apparaat om het vastmaken van het beleid voor de desbetreffende Azure storage container te downloaden.  

    ![Automatische synchronisatie voor vastgemaakte bestanden 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Open de nieuwe share die op het apparaat is gemaakt. Het bestand dat naar het opslag account is geüpload, wordt nu gedownload naar de lokale share. 

    Telkens wanneer het apparaat is losgekoppeld of opnieuw verbinding maakt, wordt het vernieuwen geactiveerd. Met vernieuwen worden alleen de bestanden bijgewerkt die zijn gewijzigd. 


## <a name="sync-storage-keys"></a>Opslagsleutels synchroniseren

Als de sleutels van uw opslagaccount zijn geroteerd, moet u de toegangssleutels voor opslag synchroniseren. Door een synchronisatie kan het apparaat de meest recente sleutels voor uw opslagaccount verkrijgen.

Voer in de Azure Portal de volgende stappen uit om uw toegangssleutel voor opslag te synchroniseren.

1. Ga in uw resource naar **Overzicht**. Klik in de lijst met shares op een share die is gekoppeld aan het opslagaccount dat u wilt synchroniseren.

    ![Selecteer delen met relevant opslag account](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. Klik op **Opslagsleutel synchroniseren**. Klik op **Ja** als u om bevestiging wordt gevraagd.

     ![Opslag sleutel voor synchronisatie selecteren](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. Sluit het dialoogvenster wanneer de synchronisatie is voltooid.

>[!NOTE]
> U hoeft dit maar eenmaal te doen voor een gegeven opslagaccount. U hoeft deze actie niet te herhalen voor alle shares die zijn gekoppeld aan hetzelfde opslagaccount.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](azure-stack-edge-gpu-manage-users.md).