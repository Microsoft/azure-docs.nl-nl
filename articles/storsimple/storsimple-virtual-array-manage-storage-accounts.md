---
title: Referenties voor StorSimple virtuele-matrix opslag account beheren | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u de pagina StorSimple Apparaatbeheer configureren kunt gebruiken om de beveiligings sleutels voor de referenties van het opslag account die zijn gekoppeld aan de virtuele StorSimple-matrix toe te voegen, te bewerken, te verwijderen of te draaien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967105"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple Apparaatbeheer gebruiken voor het beheren van de referenties van het opslag account voor de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht
De **configuratie** sectie van de StorSimple Apparaatbeheer service-Blade van de virtuele StorSimple-matrix bevat de algemene service parameters die kunnen worden gemaakt in de StorSimple Manager-service. Deze para meters kunnen worden toegepast op alle apparaten die zijn verbonden met de service, en omvatten:

* Opslagaccountreferenties
* Access Control records
  
  ![Dash board van Apparaatbeheer-service](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

In deze zelf studie wordt uitgelegd hoe u referenties voor opslag accounts kunt toevoegen, bewerken of verwijderen voor uw virtuele StorSimple-matrix. De informatie in deze zelf studie is alleen van toepassing op de virtuele StorSimple-matrix. Zie [de StorSimple Manager-service gebruiken voor het beheren van uw opslag account](./storsimple-8000-manage-storage-accounts.md)voor meer informatie over het beheren van opslag accounts in de 8000-serie.

De referenties van het opslag account bevatten de referenties die het apparaat gebruikt voor toegang tot uw opslag account met uw Cloud serviceprovider. Voor Microsoft Azure opslag accounts zijn dit referenties zoals de account naam en de primaire toegangs sleutel.

Op de Blade referenties voor het **opslag account** worden alle referenties van het opslag account die voor het facturerings abonnement zijn gemaakt, weer gegeven in een tabel indeling die de volgende informatie bevat:

* **Naam** : de unieke naam die is toegewezen aan het account wanneer deze is gemaakt.
* **SSL ingeschakeld** : Hiermee wordt aangegeven of TLS is ingeschakeld en apparaat-naar-Cloud-communicatie via het beveiligde kanaal.
  
  ![Configuratie sectie](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De meest voorkomende taken met betrekking tot de referenties van het opslag account die kunnen worden uitgevoerd op de Blade referenties van het **opslag account** zijn:

* Een opslagaccountreferentie toevoegen
* Een opslag account referentie bewerken
* Een opslag account referentie verwijderen

## <a name="types-of-storage-account-credentials"></a>Typen referenties voor het opslag account
Er zijn drie typen referenties voor het opslag account die kunnen worden gebruikt met uw StorSimple-apparaat.

* **Automatisch gegenereerde referenties voor het opslag account** : zoals de naam wordt voorgesteld, wordt dit type opslag account automatisch gegenereerd wanneer de service voor het eerst wordt gemaakt. Zie [een nieuwe service maken](storsimple-virtual-array-manage-service.md#create-a-service)voor meer informatie over hoe deze referentie voor het opslag account wordt gemaakt.
* **referenties voor het opslag account in het service abonnement** : Dit zijn de referenties van het Azure Storage-account die zijn gekoppeld aan hetzelfde abonnement als die van de service. Zie [over Azure Storage-accounts](../storage/common/storage-account-create.md)voor meer informatie over de wijze waarop deze referenties voor het opslag account worden gemaakt.
* **referenties voor het opslag account buiten het service abonnement** : Dit zijn de referenties van het Azure Storage-account die niet zijn gekoppeld aan uw service en die waarschijnlijk bestonden voordat de service werd gemaakt.

## <a name="add-a-storage-account-credential"></a>Een opslagaccountreferentie toevoegen
U kunt een opslag account referentie toevoegen aan uw StorSimple Apparaatbeheer service configuratie door een unieke beschrijvende naam op te geven en toegang te krijgen tot de referenties die zijn gekoppeld aan het opslag account. U kunt ook de modus Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL) inschakelen om een beveiligd kanaal te maken voor netwerk communicatie tussen uw apparaat en de Cloud.

U kunt meerdere accounts maken voor een bepaalde Cloud serviceprovider. Terwijl de referenties van het opslag account worden opgeslagen, probeert de service te communiceren met uw Cloud serviceprovider. De referenties en het toegangs materiaal dat u hebt opgegeven, worden op dit moment geverifieerd. Er wordt alleen een opslag account referentie gemaakt als de verificatie slaagt. Als de verificatie mislukt, wordt een geschikt fout bericht weer gegeven.

Gebruik de volgende procedures om referenties voor het Azure Storage-account toe te voegen:

* Een opslag account referentie toevoegen die hetzelfde Azure-abonnement als de Apparaatbeheer-service heeft
* Een Azure Storage-account referentie toevoegen die zich buiten het Apparaatbeheer-service abonnement bevindt

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Een opslag account referentie toevoegen die hetzelfde Azure-abonnement als de Apparaatbeheer-service heeft

1. Navigeer naar uw Apparaatbeheer-service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie.
3. Klik op **Add**.
4. Ga als volgt te werk op de Blade **een opslag account toevoegen** :
   
    1. Selecteer bij **abonnement** de optie **actueel**.
    2. Geef de naam van uw Azure Storage-account op.
    3. Selecteer **inschakelen** om een beveiligd kanaal te maken voor de netwerk communicatie tussen uw StorSimple-apparaat en de Cloud. Selecteer alleen **uitschakelen** als u binnen een privécloud werkt.
    4. Klik op **Add**. U wordt gewaarschuwd nadat het opslag account is gemaakt.<br></br>
   
        ![Een bestaande opslag account referentie toevoegen](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Een Azure Storage-account referentie toevoegen die zich buiten het Apparaatbeheer-service abonnement bevindt

1. Navigeer naar uw Apparaatbeheer-service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie. Hiermee wordt een lijst weer gegeven met alle bestaande referenties voor opslag accounts die zijn gekoppeld aan de StorSimple-Apparaatbeheer service.
3. Klik op **Add**.
4. Ga als volgt te werk op de Blade **een opslag account toevoegen** :
   
    1. Selecteer voor **abonnement** **andere**.
   
    2. Geef de naam op van de referenties van uw Azure Storage-account.
   
    3. Geef in het tekstvak **toegangs sleutel voor opslag account** de primaire toegangs sleutel voor uw Azure Storage-account referentie op. Als u deze sleutel wilt ophalen, gaat u naar de Azure Storage-service, selecteert u de referenties van uw opslag account en klikt u op **account sleutels beheren**. U kunt nu de primaire toegangs sleutel kopiëren.
   
    4. Als u TLS wilt inschakelen, klikt u op de knop **inschakelen** om een beveiligd kanaal te maken voor netwerk communicatie tussen uw StorSimple-Apparaatbeheer service en de Cloud. Klik op de knop **uitschakelen** alleen als u binnen een privécloud werkt.
   
    5. Klik op **Add**. U krijgt een melding wanneer de referenties van het opslag account zijn gemaakt.

5. De nieuwe opslag account referentie wordt weer gegeven op de Blade StorSimple configureren Apparaatbeheer service onder referenties voor het **opslag account**.
   
    ![Een opslag account referentie toevoegen buiten het Apparaatbeheer service-abonnement](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Een opslag account referentie bewerken
U kunt een opslag account bewerken die wordt gebruikt door uw apparaat. Als u een opslag account bewerkt die momenteel in gebruik is, zijn de beschik bare velden voor het wijzigen van de toegangs sleutel en de TLS-modus voor de referenties van het opslag account. U kunt de nieuwe toegangs sleutel voor opslag opgeven of de selectie **SSL-modus inschakelen** en de bijgewerkte instellingen opslaan.

#### <a name="to-edit-a-storage-account-credential"></a>Een opslag account referentie bewerken
1. Navigeer naar uw Apparaatbeheer-service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie. Hiermee wordt een lijst weer gegeven met alle bestaande referenties voor opslag accounts die zijn gekoppeld aan de StorSimple-Apparaatbeheer service.
3. Selecteer in de lijst in tabel vorm met referenties voor het opslag account en dubbel klik op het account dat u wilt wijzigen.
4. Ga als volgt te werk op de Blade **Eigenschappen** van referenties voor opslag account:
   
   1. Indien nodig kunt u de selectie SSL-modus **inschakelen** wijzigen.
   2. U kunt ervoor kiezen om de toegangs sleutels voor de referenties van uw opslag account opnieuw te genereren. Zie [Toegangssleutels voor een opslagaccount beheren](../storage/common/storage-account-keys-manage.md) voor meer informatie. Geef de referentie sleutel voor het nieuwe opslag account op. Dit is de primaire toegangs sleutel voor een Azure Storage-account.
   3. Klik boven aan de Blade **Eigenschappen** op **Opslaan** om de instellingen op te slaan. De instellingen worden bijgewerkt op de Blade referenties van het **opslag account** .
      
      ![Een opslag account referentie bewerken](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Een opslag account referentie verwijderen
> [!IMPORTANT]
> U kunt de referenties van een opslag account alleen verwijderen als deze niet in gebruik zijn. Als een opslag account referentie wordt gebruikt, wordt u hiervan op de hoogte gebracht.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Een opslag account referentie verwijderen
1. Navigeer naar uw Apparaatbeheer-service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie. Hiermee wordt een lijst weer gegeven met alle bestaande referenties voor opslag accounts die zijn gekoppeld aan de StorSimple-Apparaatbeheer service.
3. Selecteer in de lijst in tabel vorm met referenties voor het opslag account en dubbel klik op het account dat u wilt verwijderen.
4. Ga als volgt te werk op de Blade **Eigenschappen** van referenties voor opslag account:
   
   1. Klik op **verwijderen** om de referenties te verwijderen.
   2. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan met het verwijderen. De lijst in tabel vorm wordt bijgewerkt met de wijzigingen.
      
      ![Een opslag account referentie verwijderen](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Referentie sleutels voor opslag accounts synchroniseren
Uit veiligheids overwegingen is het vaak een vereiste voor het draaien van sleutels in data centers. Een Microsoft Azure beheerder kan de primaire of secundaire sleutel opnieuw genereren of wijzigen door rechtstreeks toegang te krijgen tot de referenties van het opslag account (via de Microsoft Azure Storage-Service). Deze wijziging wordt niet automatisch weer geven door de StorSimple-Apparaatbeheer service.

Als u de StorSimple Apparaatbeheer service van de wijziging wilt inlichten, moet u toegang hebben tot de StorSimple Apparaatbeheer-service, toegang krijgen tot de referenties van het opslag account en vervolgens de primaire of secundaire sleutel synchroniseren (afhankelijk van wat er is gewijzigd). De service haalt vervolgens de meest recente sleutel, versleutelt de sleutels en stuurt de versleutelde sleutel naar het apparaat.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Sleutels voor de referenties van het opslag account in hetzelfde abonnement als de service synchroniseren (alleen Azure)
1. Selecteer uw service op de Blade service overloop, dubbel klik op de naam van de service en klik vervolgens in de sectie **configuratie** op referenties voor het **opslag account**.
2. Selecteer op de Blade referenties voor het **opslag account** in de lijst met referenties voor het opslag account de referenties van het opslag account waarvan u de sleutels wilt synchroniseren.
3. Ga als volgt te werk op de Blade **Eigenschappen** voor de referenties van het geselecteerde opslag account:
   
    1. Klik op **meer** en klik vervolgens op **toegangs sleutel synchroniseren**.
   
    2. Klik op **synchronisatie sleutel** om de synchronisatie te volt ooien als u om bevestiging wordt gevraagd.
    
4. In de StorSimple-Apparaatbeheer service moet u de sleutel bijwerken die eerder in de Microsoft Azure Storage-service is gewijzigd. Als de primaire toegangs sleutel is gewijzigd (opnieuw gegenereerd), klikt u op de Blade sleutel van de **opslag account synchroniseren** op primair en klikt u vervolgens op **sleutel synchroniseren**. Als de secundaire sleutel is gewijzigd, klikt u op **secundair** en klikt u vervolgens op **sleutel synchroniseren**.
   
    ![Toegangs sleutel synchroniseren](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).