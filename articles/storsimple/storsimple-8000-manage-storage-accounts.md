---
title: Referenties voor het opslag account beheren, StorSimple 8000 Series-apparaat
description: In dit artikel wordt uitgelegd hoe u de StorSimple Apparaatbeheer pagina configureren kunt gebruiken om de beveiligings sleutels voor een opslag account toe te voegen, te bewerken, te verwijderen of te roteren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951731"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van uw referenties voor het opslag account

## <a name="overview"></a>Overzicht

In de **configuratie** sectie van de Blade StorSimple Apparaatbeheer service worden alle globale service parameters weer gegeven die kunnen worden gemaakt in de StorSimple Apparaatbeheer-service. Deze para meters kunnen worden toegepast op alle apparaten die zijn verbonden met de service, en omvatten:

* Opslagaccountreferenties
* Bandbreedte sjablonen 
* Access Control records 

In deze zelf studie wordt uitgelegd hoe u referenties voor het opslag account kunt toevoegen, bewerken of verwijderen, of hoe u de beveiligings sleutels voor een opslag account kunt draaien.

 ![Lijst met referenties voor het opslag account 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Opslag accounts bevatten de referenties die het StorSimple-apparaat gebruikt voor toegang tot uw opslag account met uw Cloud serviceprovider. Voor Microsoft Azure opslag accounts zijn dit referenties zoals de account naam en de primaire toegangs sleutel. 

Op de Blade referenties voor het **opslag account** worden alle opslag accounts die zijn gemaakt voor het abonnement, weer gegeven in een tabel indeling die de volgende informatie bevat:

* **Naam** : de unieke naam die is toegewezen aan het account wanneer deze is gemaakt.
* **SSL ingeschakeld** : Hiermee wordt aangegeven of TLS is ingeschakeld en apparaat-naar-Cloud-communicatie via het beveiligde kanaal.
* **Gebruikt door** : het aantal volumes dat gebruikmaakt van het opslag account.

De meest voorkomende taken met betrekking tot opslag accounts die kunnen worden uitgevoerd, zijn:

* Een opslagaccount toevoegen 
* Een opslag account bewerken 
* Een opslagaccount verwijderen 
* Sleutel rotatie van opslag accounts 

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Er zijn drie typen opslag accounts die kunnen worden gebruikt met uw StorSimple-apparaat.

* **Automatisch gegenereerde opslag accounts** : als de naam wordt voorgesteld, wordt dit type opslag account automatisch gegenereerd wanneer de service voor het eerst wordt gemaakt. Zie [stap 1: een nieuwe service maken](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) in [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie over hoe dit opslag account wordt gemaakt. 
* **Opslag accounts in het service abonnement** : Dit zijn de Azure Storage-accounts die zijn gekoppeld aan hetzelfde abonnement als die van de service. Zie [over Azure Storage-accounts](../storage/common/storage-account-create.md)voor meer informatie over de manier waarop deze opslag accounts worden gemaakt. 
* **Opslag accounts buiten het service abonnement** : Dit zijn de Azure Storage-accounts die niet zijn gekoppeld aan uw service en die waarschijnlijk bestonden voordat de service werd gemaakt.

## <a name="add-a-storage-account"></a>Een opslagaccount toevoegen

U kunt een opslag account toevoegen door een unieke beschrijvende naam op te geven en toegang te krijgen tot de referenties die zijn gekoppeld aan het opslag account (met de opgegeven Cloud serviceprovider). U kunt ook de modus Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL) inschakelen om een beveiligd kanaal te maken voor netwerk communicatie tussen uw apparaat en de Cloud.

U kunt meerdere accounts maken voor een bepaalde Cloud serviceprovider. Houd er echter rekening mee dat u na het maken van een opslag account de Cloud serviceprovider niet kunt wijzigen.

Terwijl het opslag account wordt opgeslagen, probeert de service te communiceren met uw Cloud serviceprovider. De referenties en het toegangs materiaal dat u hebt opgegeven, worden op dit moment geverifieerd. Een opslag account wordt alleen gemaakt als de verificatie slaagt. Als de verificatie mislukt, wordt een geschikt fout bericht weer gegeven.

Gebruik de volgende procedures om referenties voor het Azure Storage-account toe te voegen:

* Een opslag account referentie toevoegen die hetzelfde Azure-abonnement als de Apparaatbeheer-service heeft
* Een Azure Storage-account referentie toevoegen die zich buiten het Apparaatbeheer-service abonnement bevindt

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Een Azure Storage-account referentie toevoegen buiten het StorSimple Apparaatbeheer service-abonnement

1. Ga naar de StorSimple-Apparaatbeheer service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie. Hiermee wordt een lijst weer gegeven met alle bestaande referenties voor opslag accounts die zijn gekoppeld aan de StorSimple-Apparaatbeheer service.
3. Klik op **Add**.
4. Ga als volgt te werk op de Blade **een opslag account referentie toevoegen** :
   
    1. Selecteer voor **abonnement** **andere**.
   
    2. Geef de naam op van de referenties van uw Azure Storage-account.
   
    3. Geef in het tekstvak **toegangs sleutel voor opslag account** de primaire toegangs sleutel voor uw Azure Storage-account referentie op. Als u deze sleutel wilt ophalen, gaat u naar de Azure Storage-service, selecteert u de referenties van uw opslag account en klikt u op **account sleutels beheren**. U kunt nu de primaire toegangs sleutel kopiëren.
   
    4. Als u TLS wilt inschakelen, klikt u op de knop **inschakelen** om een beveiligd kanaal te maken voor netwerk communicatie tussen uw StorSimple-Apparaatbeheer service en de Cloud. Klik op de knop **uitschakelen** alleen als u binnen een privécloud werkt.
   
    5. Klik op **Add**. U krijgt een melding wanneer de referenties van het opslag account zijn gemaakt.

5. De nieuwe opslag account referentie wordt weer gegeven op de Blade StorSimple configureren Apparaatbeheer service onder referenties voor het **opslag account**.
   


## <a name="edit-a-storage-account"></a>Een opslag account bewerken

U kunt een opslag account bewerken dat wordt gebruikt door een volume container. Als u een opslag account bewerkt dat momenteel in gebruik is, is het enige veld dat kan worden gewijzigd de toegangs sleutel voor het opslag account. U kunt de nieuwe toegangs sleutel voor opslag opgeven en de bijgewerkte instellingen opslaan.

#### <a name="to-edit-a-storage-account"></a>Een opslag account bewerken

1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.

    ![Referenties voor het opslag account 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Selecteer op de Blade referenties voor het **opslag account** in de lijst met referenties voor het opslag account de optie die u wilt bewerken. 

3. U kunt de selectie **SSL inschakelen** wijzigen. U kunt ook op **meer klikken...** en vervolgens **synchronisatie toegangs sleutel selecteren om** de toegangs sleutels van uw opslag account te roteren. Ga naar [sleutel rotatie van opslag accounts](#key-rotation-of-storage-accounts) voor meer informatie over het uitvoeren van een sleutel rotatie. Nadat u de instellingen hebt gewijzigd, klikt u op **Opslaan**. 

    ![De referenties voor het bewerkte opslag account opslaan](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klik op **Ja** als u om bevestiging wordt gevraagd. 

    ![Wijzigingen bevestigen](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

De instellingen worden bijgewerkt en opgeslagen voor uw opslag account. 

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

> [!IMPORTANT]
> U kunt een opslag account alleen verwijderen als dit niet wordt gebruikt door een volume container. Als een opslag account wordt gebruikt door een volume container, moet u eerst de volume container verwijderen en vervolgens het bijbehorende opslag account verwijderen.

#### <a name="to-delete-a-storage-account"></a>Een opslag account verwijderen

1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.

2. Beweeg de muis aanwijzer over het account dat u wilt verwijderen in de lijst in tabel vorm van opslag accounts. Klik met de rechter muisknop om het context menu aan te roepen en klik op **verwijderen**.

    ![Referenties voor het opslag account verwijderen](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan met het verwijderen. De lijst in tabel vorm wordt bijgewerkt met de wijzigingen.

    ![De verwijdering bevestigen](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Sleutel rotatie van opslag accounts

Uit veiligheids overwegingen is het vaak een vereiste voor het draaien van sleutels in data centers. Elk Microsoft Azure-abonnement kan een of meer gekoppelde opslag accounts hebben. De toegang tot deze accounts wordt bepaald door het abonnement en de toegangs sleutels voor elk opslag account. 

Wanneer u een opslag account maakt, genereert Microsoft Azure 2 512-bits opslag toegangs sleutels die worden gebruikt voor verificatie wanneer het opslag account wordt geopend. Als u twee opslag toegangs sleutels hebt, kunt u de sleutels opnieuw genereren zonder dat uw opslag service of toegang tot die service wordt onderbroken. De sleutel die momenteel wordt gebruikt, is de *primaire* sleutel en de back-upsleutel wordt aangeduid als de *secundaire* sleutel. Een van deze twee sleutels moet worden opgegeven wanneer uw Microsoft Azure StorSimple-apparaat toegang heeft tot uw Cloud Storage-Provider.

## <a name="what-is-key-rotation"></a>Wat is het draaien van sleutels?

Normaal gesp roken gebruiken toepassingen slechts een van de sleutels om toegang te krijgen tot uw gegevens. Na een bepaalde periode kunt u uw toepassingen overschakelen op het gebruik van de tweede sleutel. Nadat u uw toepassingen hebt overgeschakeld naar de secundaire sleutel, kunt u de eerste sleutel buiten gebruik stellen en vervolgens een nieuwe sleutel genereren. Met behulp van de twee toetsen op deze manier kunnen uw toepassingen toegang krijgen tot de gegevens zonder uitval tijd.

De sleutels voor het opslag account worden in een versleutelde vorm altijd opgeslagen in de service. Deze kunnen echter opnieuw worden ingesteld via de StorSimple-Apparaatbeheer service. De service kan de primaire sleutel en secundaire sleutel ophalen voor alle opslag accounts in hetzelfde abonnement, met inbegrip van accounts die in de opslag service zijn gemaakt, evenals de standaard opslag accounts die worden gegenereerd wanneer de StorSimple-Apparaatbeheer service voor het eerst werd gemaakt. Met de StorSimple-Apparaatbeheer service worden deze sleutels altijd opgehaald uit de klassieke Azure-Portal en vervolgens op een versleutelde manier opgeslagen.

## <a name="rotation-workflow"></a>Werk stroom voor rotatie

Een Microsoft Azure beheerder kan de primaire of secundaire sleutel opnieuw genereren of wijzigen door rechtstreeks toegang te krijgen tot het opslag account (via de Microsoft Azure Storage-Service). Deze wijziging wordt niet automatisch weer geven door de StorSimple-Apparaatbeheer service.

Als u de StorSimple-Apparaatbeheer service van de wijziging wilt inlichten, moet u toegang hebben tot de StorSimple Apparaatbeheer-service, toegang krijgen tot het opslag account en vervolgens de primaire of secundaire sleutel synchroniseren (afhankelijk van wat er is gewijzigd). De service haalt vervolgens de meest recente sleutel, versleutelt de sleutels en stuurt de versleutelde sleutel naar het apparaat.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Sleutels synchroniseren voor opslag accounts in hetzelfde abonnement als de service 
1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.
2. Klik in de lijst in tabel vorm van opslag accounts op het item dat u wilt wijzigen. 

    ![sleutels synchroniseren](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klik op **... Meer** en selecteer vervolgens **synchronisatie toegangs sleutel om te roteren**.   

    ![sleutels synchroniseren 2](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. In de StorSimple-Apparaatbeheer service moet u de sleutel bijwerken die eerder in de Microsoft Azure Storage-service is gewijzigd. Als de primaire toegangs sleutel is gewijzigd (opnieuw gegenereerd), selecteert u **primaire** sleutel. Selecteer **secundaire** sleutel als de secundaire sleutel is gewijzigd. Klik op **synchronisatie sleutel**.
      
      ![sleutels synchroniseren 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

U wordt gewaarschuwd nadat de sleutel is gesynchroniseerd.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Sleutels voor opslag accounts buiten het service abonnement synchroniseren
1. Klik op de pagina **Services** op het tabblad **configureren** .
2. Klik op **opslag accounts toevoegen/bewerken**.
3. Ga als volgt te werk in het dialoog venster:
   
   1. Selecteer het opslag account met de toegangs sleutel die u wilt bijwerken.
   2. U moet de toegangs sleutel voor opslag bijwerken in de StorSimple-Apparaatbeheer service. In dit geval ziet u de toegangs sleutel voor opslag. Voer de nieuwe sleutel in het vak **toegangs sleutel voor het opslag account** in. 
   3. Sla uw wijzigingen op. De toegangs sleutel voor uw opslag account moet nu worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [StorSimple-beveiliging](storsimple-8000-security.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).