---
title: De Azure Portal gebruiken om shares te beheren in Azure Data Box Gateway | Microsoft Docs
description: Beschrijft hoe u de Azure Portal gebruikt om shares te beheren in uw Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 8a6bfb1c6aa268914e6dd1157115067b15bfa404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786907"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-gateway"></a>De Azure Portal gebruiken om shares te beheren in uw Azure Data Box Gateway 

Dit artikel beschrijft hoe u shares beheert in uw Azure Data Box Gateway. U kunt de Azure Data Box Gateway beheren via Azure Portal of via de lokale webinterface. Gebruik de Azure Portal om shares toe te voegen, te verwijderen of te vernieuwen of de opslagsleutel te synchroniseren voor het opslagaccount dat is gekoppeld aan de shares.

## <a name="about-shares"></a>Info over shares

Als u wilt gegevens wilt overdragen naar Azure, moet u shares maken in uw Azure Data Box Gateway. De shares die u toevoegt aan het Data Box Gateway-apparaat, zijn cloudshares. De gegevens uit deze shares worden automatisch geüpload naar de cloud. Alle cloudfuncties zoals het vernieuwen en synchroniseren van opslagsleutels worden toegepast op deze shares. Gebruik de cloudshares wanneer u wilt dat de apparaatgegevens automatisch naar uw opslagaccount in de cloud worden gepusht.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een share toevoegen
> * Een share verwijderen
> * Shares vernieuwen
> * Opslagsleutel synchroniseren


## <a name="add-a-share"></a>Een share toevoegen

Voer in de Azure Portal de volgende stappen uit om een share te maken.

1. Ga in het Azure Portal naar uw Data Box Gateway resource en navigeer vervolgens naar **overzicht**. Klik op de opdrachtbalk op **+ Share toevoegen**.
2. Geef in **Share toevoegen** de instellingen voor de share op. Geef een unieke naam voor de share op. 

    ![Op Share toevoegen klikken](media/data-box-gateway-manage-shares/add-share-1.png)

    Namen van shares mogen alleen cijfers, kleine letters en afbreekstreepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een cijfer of letter beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.

3. Selecteer een **Type** voor de share. Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

4. Geef het **opslagaccount** op waar de share zich bevindt. In het opslagaccount met de naam van de share wordt een container gemaakt, als deze nog niet bestaat. Als de container al bestaat, wordt de bestaande container gebruikt.  

5. Kies de **Opslagservice** vanuit blok-blob, pagina-blob of bestanden. Het type service dat u kiest, is afhankelijk van de indeling waarin u de gegevens in Azure wilt opslaan. In dit geval willen we bijvoorbeeld dat de gegevens zich in azure als blob-blokken bevinden. Daarom selecteren we **blok-BLOB**. Als u **Pagina-blob** kiest, moet u ervoor zorgen dat uw gegevens op 512 bytes zijn uitgelijnd. VHDX is bijvoorbeeld altijd op 512 bytes uitgelijnd.

   > [!IMPORTANT]
   > Controleer of voor het Azure-opslagaccount dat u gebruikt geen onveranderbaarheidsbeleid is ingesteld als u het account gebruikt in combinatie met een Data Box Gateway-apparaat. Zie [Beleid voor onveranderbaarheid instellen en beheren voor blobopslag](../storage/blobs/storage-blob-immutability-policies-manage.md) voor meer informatie.

6. Deze stap hangt af van of u een SMB- of een NFS-share gaat maken.
    - **Als u een SMB-share maakt**: maak in het veld **All privilege local user** (Lokale gebruiker met alle bevoegdheden) een keuze uit **Nieuwe maken** of **Bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, geeft u de **gebruikersnaam** en het **wachtwoord** op en bevestigt u het wachtwoord. Hiermee worden de bevoegdheden aan de lokale gebruiker toegewezen. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.

        ![SMB-share toevoegen](media/data-box-gateway-manage-shares/add-share-2.png)

        Als u ervoor kiest om alleen alleen-lezen bewerkingen toe te staan voor deze share-gegevens, kunt u gebruikers opgeven met alleen-lezen machtigingen.
    - **Als u een NFS-share maakt**: geef de **IP-adressen op van de clients** die toegang hebben tot de share.

        ![NFS-share toevoegen](media/data-box-gateway-manage-shares/add-share-3.png)

7. Klik op **maken** om de share te maken. U ontvangt een melding als wordt begonnen met het maken van de share. Nadat de share is gemaakt met de opgegeven instellingen, wordt de Blade **shares** bijgewerkt zodat deze overeenkomen met de nieuwe share.
 
## <a name="delete-a-share"></a>Een share verwijderen

Voer in de Azure Portal de volgende stappen uit om een share te verwijderen.

1. Klik in de lijst met shares op de share die u wilt verwijderen.

    ![Share selecteren](media/data-box-gateway-manage-shares/delete-1.png)

2. Klik op **Verwijderen**. 

    ![Op Verwijderen klikken](media/data-box-gateway-manage-shares/delete-2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![De verwijdering bevestigen](media/data-box-gateway-manage-shares/delete-3.png)

De lijst met shares wordt bijgewerkt en de verwijderde share wordt niet meer weergegeven.


## <a name="refresh-shares"></a>Shares vernieuwen

Met de functie Vernieuwen kunt u de inhoud van een on-premises share vernieuwen. Wanneer u een share vernieuwt, wordt er een zoekopdracht gestart om de Azure-objecten te zoeken, inclusief blobs en bestanden die aan de cloud zijn toegevoegd na de laatste vernieuwing. Deze aanvullende bestanden worden vervolgens gebruikt om de inhoud te vernieuwen van de on-premises share op het apparaat. 

> [!NOTE]
> Machtigingen en toegangs beheer lijsten (Acl's) blijven niet behouden in een vernieuwings bewerking. 

Voer in de Azure Portal de volgende stappen uit om een share te vernieuwen.

1. Ga in de Azure Portal naar **Shares**. Klik op de share die u wilt vernieuwen.

   ![Share 2 selecteren](media/data-box-gateway-manage-shares/refresh-1.png)

2. Klik op **Vernieuwen**. 

   ![Op Vernieuwen klikken](media/data-box-gateway-manage-shares/refresh-2.png)
 
3. Klik op **Ja** als u om bevestiging wordt gevraagd. Er wordt een taak gestart om de inhoud van de on-premises share te vernieuwen. 

   ![Vernieuwen bevestigen](media/data-box-gateway-manage-shares/refresh-3.png)
 
4.   Terwijl het vernieuwen wordt uitgevoerd, is de optie Vernieuwen niet beschikbaar in het contextmenu. Klik op de taakmelding om de status van de vernieuwingstaak weer te geven.

5. Hoe lang de vernieuwing duurt is afhankelijk van het aantal bestanden in de Azure-container en van het aantal bestanden op het apparaat. Zodra het vernieuwen is voltooid, wordt het tijdstempel van de share bijgewerkt. Zelfs als de vernieuwing niet geheel zonder fouten is uitgevoerd, wordt de bewerking als voltooid beschouwd en wordt het tijdstempel bijgewerkt. 

   ![Bijgewerkt tijdstempel](media/data-box-gateway-manage-shares/refresh-4.png)
 
Als er een fout optreedt, wordt er een waarschuwing gegenereerd. De waarschuwing bevat meer informatie over de oorzaak en een aanbeveling om het probleem te verhelpen. De waarschuwing bevat ook een koppeling naar een bestand dat een volledig overzicht bevat van de fouten en van de bestanden die niet zijn bijgewerkt of verwijderd.

>[!IMPORTANT]
> In deze versie kunt u niet meer dan één share tegelijk vernieuwen.

## <a name="sync-storage-keys"></a>Opslagsleutels synchroniseren

Als de sleutels van uw opslagaccount zijn geroteerd, moet u de toegangssleutels voor opslag synchroniseren. Door een synchronisatie kan het apparaat de meest recente sleutels voor uw opslagaccount verkrijgen.

Voer in de Azure Portal de volgende stappen uit om uw toegangssleutel voor opslag te synchroniseren.

1. Ga in uw resource naar **Overzicht**. 
2. Klik in de lijst met shares op een share die is gekoppeld aan het opslag account dat u wilt synchroniseren. Klik op **opslag sleutel synchroniseren**. 

     ![Opslagsleutel synchroniseren](media/data-box-gateway-manage-shares/sync-storage-key-1.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd. Sluit het dialoogvenster wanneer de synchronisatie is voltooid.

     ![Opslag sleutel 2 synchroniseren](media/data-box-gateway-manage-shares/sync-storage-key-2.png)

>[!NOTE]
> U hoeft dit maar eenmaal te doen voor een gegeven opslagaccount. U hoeft deze actie niet te herhalen voor alle shares die zijn gekoppeld aan hetzelfde opslagaccount.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](data-box-gateway-manage-users.md).