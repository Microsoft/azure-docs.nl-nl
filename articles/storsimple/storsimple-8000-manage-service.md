---
title: De StorSimple Apparaatbeheer-service in azure implementeren | Microsoft Docs
description: Meer informatie over de stappen die nodig zijn voor het maken, verwijderen, migreren van de service en het beheer van de service registratie sleutel.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360673"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>De StorSimple Apparaatbeheer-service voor StorSimple 8000 Series-apparaten implementeren

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere StorSimple-apparaten. Nadat u de service hebt gemaakt, kunt u deze gebruiken voor het beheren van alle apparaten die zijn verbonden met de StorSimple-Apparaatbeheer service vanaf één centrale locatie, waardoor de administratieve belasting wordt geminimaliseerd.

In deze zelf studie worden de stappen beschreven die nodig zijn voor het maken, verwijderen, migreren van de service en het beheer van de service registratie sleutel. De informatie in dit artikel is alleen van toepassing op apparaten uit de StorSimple 8000-serie. Ga voor meer informatie over virtuele StorSimple-matrices naar [Deploy a StorSimple Apparaatbeheer service voor uw StorSimple Virtual array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Het Azure Portal ondersteunt apparaten met Update 5,0 of hoger. Als uw apparaat niet up-to-date is, installeert u update 5 onmiddellijk. Ga voor meer informatie naar [Update 5 installeren](storsimple-8000-install-update-5.md). 
> - Als u een StorSimple Cloud Appliance (8010/8020) gebruikt, kunt u een Cloud apparaat niet bijwerken. Gebruik de nieuwste versie van de software om een nieuw Cloud apparaat met Update 5,0 te maken en ga vervolgens naar het nieuwe Cloud apparaat dat is gemaakt. 
> - Op alle apparaten waarop update 4,0 of eerder wordt uitgevoerd, wordt de beheer functionaliteit gereduceerd. 

## <a name="create-a-service"></a>Een service maken
Als u een StorSimple Apparaatbeheer-service wilt maken, moet u het volgende hebben:

* Een abonnement met een Enterprise Overeenkomst
* Een actief Microsoft Azure Storage-account
* De facturerings gegevens die worden gebruikt voor toegangs beheer

Alleen de abonnementen met een Enterprise Overeenkomst zijn toegestaan. U kunt er ook voor kiezen om een standaard-opslag account te genereren wanneer u de service maakt.

Eén service kan meerdere apparaten beheren. Een apparaat kan echter niet meerdere services omvatten. Een grote onderneming kan meerdere service-exemplaren gebruiken om te werken met verschillende abonnementen, organisaties of zelfs implementatie locaties. 

> [!NOTE]
> U hebt afzonderlijke instanties van StorSimple Apparaatbeheer service nodig voor het beheren van StorSimple 8000 Series-apparaten en StorSimple virtuele matrices.

Voer de volgende stappen uit om een service te maken.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Voor elke StorSimple-Apparaatbeheer service bestaan de volgende kenmerken:

* **Naam** : de naam die is toegewezen aan uw StorSimple-Apparaatbeheer service toen deze werd gemaakt. **De service naam kan niet worden gewijzigd nadat de service is gemaakt. Dit geldt ook voor andere entiteiten, zoals apparaten, volumes, volume containers en back-upbeleiden waarvan de naam niet kan worden gewijzigd in de Azure Portal.**
* **Status** : de status van de service, die **actief**, **maken** of **online** kan zijn.
* **Locatie** : de geografische locatie waar het StorSimple-apparaat wordt geïmplementeerd.
* **Abonnement** : het facturerings abonnement dat is gekoppeld aan uw service.

## <a name="delete-a-service"></a>Een service verwijderen

Voordat u een service verwijdert, moet u ervoor zorgen dat er geen aangesloten apparaten worden gebruikt. Als de service wordt gebruikt, deactiveert u de verbonden apparaten. Met de bewerking deactiveren wordt de verbinding tussen het apparaat en de service verbroken, maar blijven de apparaatgegevens in de Cloud behouden.

> [!IMPORTANT]
> De bewerking kan niet ongedaan worden gemaakt nadat een service is verwijderd. Elk apparaat dat de service gebruikt, moet opnieuw worden ingesteld op de fabrieks instellingen voordat het kan worden gebruikt met een andere service. In dit scenario gaan de lokale gegevens op het apparaat, evenals de configuratie, verloren.

Voer de volgende stappen uit om een service te verwijderen.

### <a name="to-delete-a-service"></a>Een service verwijderen

1. Zoek de service die u wilt verwijderen. Klik op het pictogram **resources** en voer vervolgens de juiste voor waarden in om te zoeken. Klik in de zoek resultaten op de service die u wilt verwijderen.

    ![Zoek service die u wilt verwijderen](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Hiermee gaat u naar de Blade StorSimple Apparaatbeheer service. Klik op **Verwijderen**.

    ![Service verwijderen](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klik op **Ja** in het bevestigings bericht. Het kan enkele minuten duren voordat de service is verwijderd.

    ![Verwijdering bevestigen](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>De serviceregistratiesleutel ophalen

Nadat u een service hebt gemaakt, moet u uw StorSimple-apparaat registreren bij de service. Als u uw eerste StorSimple-apparaat wilt registreren, hebt u de service registratie sleutel nodig. Als u extra apparaten wilt registreren met een bestaande StorSimple-service, hebt u zowel de registratie sleutel als de versleutelings sleutel voor service gegevens nodig (die tijdens de registratie wordt gegenereerd op het eerste apparaat). Zie [StorSimple Security](storsimple-8000-security.md)(Engelstalig) voor meer informatie over de versleutelings sleutel voor service gegevens. U kunt de registratie sleutel ophalen met behulp van **sleutels** op uw StorSimple Apparaatbeheer-Blade.

Voer de volgende stappen uit om de service registratie sleutel op te halen.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Behoud de service registratie sleutel op een veilige locatie. U hebt deze sleutel nodig, evenals de versleutelings sleutel voor service gegevens, om extra apparaten te registreren bij deze service. Nadat u de service registratie sleutel hebt verkregen, moet u uw apparaat configureren via de Windows PowerShell voor StorSimple-interface.

Zie voor meer informatie over het gebruik van deze registratie sleutel [stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>De service registratie sleutel opnieuw genereren
Als u een sleutel rotatie moet uitvoeren of als de lijst met service beheerders is gewijzigd, moet u een service registratie sleutel opnieuw genereren. Wanneer u de sleutel opnieuw genereert, wordt de nieuwe sleutel alleen gebruikt voor het registreren van volgende apparaten. De apparaten die al zijn geregistreerd, worden niet beïnvloed door dit proces.

Voer de volgende stappen uit om een service registratie sleutel opnieuw te genereren.

### <a name="to-regenerate-the-service-registration-key"></a>De service registratie sleutel opnieuw genereren
1. Ga op de Blade **StorSimple Apparaatbeheer** naar **beheer &gt;** **sleutels**.
    
    ![Ga naar de Blade sleutels](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Klik op de Blade **sleutels** op **opnieuw genereren**.

    ![Klik op opnieuw genereren](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Controleer op de Blade **service registratie sleutel opnieuw genereren** de actie die vereist is wanneer de sleutels opnieuw worden gegenereerd. Op alle volgende apparaten die zijn geregistreerd bij deze service wordt de nieuwe registratie sleutel gebruikt. Klik op **opnieuw genereren** om te bevestigen. U wordt gewaarschuwd nadat het opnieuw genereren is voltooid.

    ![Opnieuw genereren bevestigen](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Er wordt een nieuwe service registratie sleutel weer gegeven.

5. Kopieer deze sleutel en sla deze op voor het registreren van nieuwe apparaten bij deze service.



## <a name="change-the-service-data-encryption-key"></a>De versleutelings sleutel voor service gegevens wijzigen
De versleutelings sleutels voor service gegevens worden gebruikt voor het versleutelen van vertrouwelijke klant gegevens, zoals referenties voor het opslag account, die worden verzonden vanuit uw StorSimple Manager-service naar het StorSimple-apparaat. U moet deze sleutels regel matig wijzigen als uw IT-organisatie een beleid voor sleutel rotatie heeft op de opslag apparaten. Het sleutel wijzigings proces kan enigszins verschillen, afhankelijk van het feit of er één apparaat of meerdere apparaten worden beheerd door de StorSimple Manager-service. Ga voor meer informatie naar [StorSimple beveiliging en gegevens bescherming](storsimple-8000-security.md).

Het wijzigen van de versleutelings sleutel voor service gegevens is een proces met drie stappen:

1. Een apparaat machtigen om de versleutelings sleutel van de service gegevens te wijzigen met behulp van Windows Power shell-scripts voor Azure Resource Manager.
2. Met Windows PowerShell voor StorSimple initieert u de wijziging van de versleutelings sleutel voor de service gegevens.
3. Als u meer dan één StorSimple-apparaat hebt, werkt u de versleutelings sleutel voor service gegevens op de andere apparaten bij.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Stap 1: Windows Power shell-script gebruiken om een apparaat te autoriseren om de versleutelings sleutel van de service gegevens te wijzigen
Normaal gesp roken vraagt de beheerder aan dat de service beheerder een apparaat moet machtigen om de versleutelings sleutels voor de service gegevens te wijzigen. De service beheerder geeft het apparaat vervolgens toestemming om de sleutel te wijzigen.

Deze stap wordt uitgevoerd met behulp van het script op basis van Azure Resource Manager. De service beheerder kan een apparaat selecteren dat in aanmerking komt voor autorisatie. Het apparaat wordt vervolgens geautoriseerd om het wijzigings proces voor de versleutelings sleutel van de service gegevens te starten. 

Ga voor meer informatie over het gebruik van het script naar [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Welke apparaten kunnen worden geautoriseerd om de versleutelings sleutels voor service gegevens te wijzigen?
Een apparaat moet voldoen aan de volgende criteria voordat het kan worden geautoriseerd om de wijzigingen in de versleutelings sleutel van de service gegevens te initiëren:

* Het apparaat moet online zijn om in aanmerking te komen voor wijzigings autorisatie van de versleutelings sleutel voor service gegevens.
* U kunt hetzelfde apparaat na 30 minuten opnieuw autoriseren als de sleutel wijziging niet is geïnitieerd.
* U kunt een ander apparaat machtigen, mits de sleutel wijziging niet door het eerder geautoriseerde apparaat is geïnitieerd. Nadat het nieuwe apparaat is geautoriseerd, kan het oude apparaat de wijziging niet initiëren.
* U kunt een apparaat niet machtigen terwijl de overschakeling van de versleutelings sleutel voor de service gegevens wordt uitgevoerd.
* U kunt een apparaat machtigen wanneer een aantal van de apparaten die zijn geregistreerd bij de service, zijn doorgevoerd in de versleuteling, terwijl anderen dat niet doen. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Stap 2: gebruik Windows PowerShell voor StorSimple om de wijziging van de versleutelings sleutel voor de service gegevens te initiëren
Deze stap wordt uitgevoerd in de Windows PowerShell voor StorSimple-interface op het geautoriseerde StorSimple-apparaat.

> [!NOTE]
> Er kunnen geen bewerkingen worden uitgevoerd in de Azure Portal van uw StorSimple Manager-service totdat de sleutel rollover is voltooid.


Als u de seriële console van het apparaat gebruikt om verbinding te maken met de Windows Power shell-interface, voert u de volgende stappen uit.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>De wijziging van de versleutelings sleutel van de service gegevens initiëren
1. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ in de opdrachtprompt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Nadat de cmdlet is voltooid, krijgt u een nieuwe versleutelings sleutel voor service gegevens. Kopieer deze sleutel en sla deze op voor gebruik in stap 3 van dit proces. Deze sleutel wordt gebruikt om alle resterende apparaten bij te werken die zijn geregistreerd bij de StorSimple Manager-service.
   
   > [!NOTE]
   > Dit proces moet worden gestart binnen vier uur na het autoriseren van een StorSimple-apparaat.
   > 
   > 
   
   Deze nieuwe sleutel wordt vervolgens naar de service verzonden die moet worden gepusht naar alle apparaten die zijn geregistreerd bij de service. Er wordt dan een waarschuwing weer gegeven in het service dashboard. De service schakelt alle bewerkingen op de geregistreerde apparaten uit en de apparaat-beheerder moet de versleutelings sleutel voor de service gegevens vervolgens op de andere apparaten bijwerken. Het I/O's (hosts die gegevens verzenden naar de Cloud) wordt echter niet onderbroken.
   
   Als er één apparaat is geregistreerd bij uw service, is het rollover proces nu voltooid en kunt u de volgende stap overs Laan. Als er meerdere apparaten zijn geregistreerd bij uw service, gaat u verder met stap 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Stap 3: de versleutelings sleutel voor service gegevens op andere StorSimple-apparaten bijwerken
Deze stappen moeten worden uitgevoerd in de Windows Power shell-interface van uw StorSimple-apparaat als er meerdere apparaten zijn geregistreerd bij uw StorSimple Manager-service. De sleutel die u hebt verkregen in stap 2 moet worden gebruikt voor het bijwerken van alle resterende StorSimple-apparaten die zijn geregistreerd bij de StorSimple Manager-service.

Voer de volgende stappen uit om de service gegevens versleuteling op uw apparaat bij te werken.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>De versleutelings sleutel voor service gegevens op fysieke apparaten bijwerken
1. Gebruik Windows PowerShell voor StorSimple om verbinding te maken met de console. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ het volgende achter de opdracht prompt:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Geef de versleutelings sleutel voor service gegevens op die u hebt verkregen in [stap 2: gebruik Windows PowerShell voor StorSimple om de wijziging van de versleutelings sleutel van de service gegevens te initiëren](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>De versleutelings sleutel voor service gegevens op alle 8010/8020-Cloud apparaten bijwerken
1. Down load en Setup [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) Power shell-script. 
2. Open Power shell en typ bij de opdracht prompt:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Met dit script wordt ervoor gezorgd dat de versleutelings sleutel voor service gegevens is ingesteld op alle 8010/8020-Cloud apparaten onder Apparaatbeheer.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Ondersteunde bewerkingen op apparaten met versies voorafgaand aan update 5,0
In de Azure Portal worden alleen de StorSimple-apparaten met Update 5,0 en hoger ondersteund. De apparaten waarop oudere versies worden uitgevoerd, hebben beperkte ondersteuning. Nadat u naar de Azure Portal hebt gemigreerd, gebruikt u de volgende tabel om te begrijpen welke bewerkingen worden ondersteund op apparaten met versies voorafgaand aan update 5,0.

| Bewerking                                                                                                                       | Ondersteund      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Een apparaat registreren                                                                                                               | Yes            |
| Apparaatinstellingen configureren, zoals algemeen, netwerk en beveiliging                                                                | Yes            |
| Updates scannen, downloaden en installeren                                                                                             | Yes            |
| Apparaat deactiveren                                                                                                               | Yes            |
| Apparaat verwijderen                                                                                                                   | Yes            |
| Een volume container maken, wijzigen en verwijderen                                                                                   | No             |
| Een volume maken, wijzigen en verwijderen                                                                                             | No             |
| Een back-upbeleid maken, wijzigen en verwijderen                                                                                      | No             |
| Een hand matige back-up maken                                                                                                            | No             |
| Een geplande back-up maken                                                                                                         | Niet van toepassing |
| Herstellen vanuit een back-upset                                                                                                        | No             |
| Klonen naar een apparaat met update 3,0 en hoger <br> Op het bron apparaat wordt versie uitgevoerd vóór het bijwerken van 3,0.                                | Yes            |
| Klonen naar een apparaat met versies voorafgaand aan update 3,0                                                                          | No             |
| Failover als bron apparaat <br> (vanaf een apparaat met versie vóór het bijwerken van 3,0 naar een apparaat met update 3,0 of hoger)                                                               | Yes            |
| Failover als doel apparaat <br> (op een apparaat met software versie vóór update 3,0)                                                                                   | No             |
| Een waarschuwing wissen                                                                                                                  | Yes            |
| Back-upbeleid, back-upcatalogus, volumes, volume containers, bewakings grafieken, taken en waarschuwingen die zijn gemaakt in de klassieke portal weer geven | Yes            |
| Apparaat-controllers in-of uitschakelen                                                                                              | Ja            |


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [implementatie proces van StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Meer informatie over [het beheren van uw StorSimple-opslag account](storsimple-8000-manage-storage-accounts.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).
