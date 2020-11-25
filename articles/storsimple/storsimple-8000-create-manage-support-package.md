---
title: Een ondersteunings pakket voor de StorSimple 8000-serie maken
description: Meer informatie over het maken, ontsleutelen en bewerken van een ondersteunings pakket voor het StorSimple 8000-serie apparaat.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017165"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Een ondersteunings pakket voor de StorSimple 8000-serie maken en beheren

## <a name="overview"></a>Overzicht

Een StorSimple-ondersteunings pakket is een eenvoudig te gebruiken mechanisme dat alle relevante logboeken verzamelt om Microsoft Ondersteuning te helpen bij het oplossen van problemen met StorSimple-apparaten. De verzamelde logboeken worden versleuteld en gecomprimeerd.

Deze zelf studie bevat stapsgewijze instructies voor het maken en beheren van het ondersteunings pakket voor het StorSimple-apparaat van de 8000-serie. Als u werkt met een virtuele StorSimple-matrix, gaat u naar [een logboek pakket genereren](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Een ondersteunings pakket maken

In sommige gevallen moet u het ondersteunings pakket hand matig maken via Windows PowerShell voor StorSimple. Bijvoorbeeld:

* Als u gevoelige informatie uit uw logboek bestanden moet verwijderen voordat u met Microsoft Ondersteuning kunt delen.
* Als u problemen ondervindt bij het uploaden van het pakket vanwege verbindings problemen.

U kunt uw hand matig gegenereerde ondersteunings pakket delen met Microsoft Ondersteuning over e-mail. Voer de volgende stappen uit om een ondersteunings pakket te maken in Windows PowerShell voor StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Een ondersteunings pakket maken in Windows PowerShell voor StorSimple

1. Als u een Windows Power shell-sessie wilt starten als beheerder op de externe computer die wordt gebruikt om verbinding te maken met uw StorSimple-apparaat, voert u de volgende opdracht in:
   
    `Start PowerShell`
2. Maak in de Windows Power shell-sessie verbinding met de SSAdmin-console van uw apparaat:
   
   1. Voer in de opdrachtregel in:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Voer in het dialoog venster dat wordt geopend het beheerders wachtwoord voor uw apparaat in. Het standaardapparaatwachtwoord is _Wachtwoord1_.
     
      ![Het dialoog venster Power shell-referenties](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecteer **OK**.
   4. Voer in de opdrachtregel in:
     
      `Enter-PSSession $MS`
3. Voer in de sessie die wordt geopend de juiste opdracht in.
   
   * Voor netwerk shares die met een wacht woord zijn beveiligd, voert u het volgende in:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       U wordt gevraagd om een wacht woord en een wachtwoordzin voor versleuteling (omdat het ondersteunings pakket is versleuteld). Vervolgens wordt er een ondersteunings pakket gemaakt in de standaardmap (apparaatnaam toegevoegd met de huidige datum en tijd).
   * Voor shares die niet met een wacht woord zijn beveiligd, hebt u de `-Credential` para meter niet nodig. Voer het volgende in:
     
       `Export-HcsSupportPackage`
     
       Het ondersteunings pakket wordt gemaakt voor beide controllers in de standaardmap. Het pakket is een versleuteld, gecomprimeerd bestand dat naar Microsoft Ondersteuning kan worden verzonden voor het oplossen van problemen. Zie [contact opnemen met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md)voor meer informatie.

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>De cmdlet-para meters voor Export-HcsSupportPackage

U kunt de volgende para meters gebruiken met de cmdlet Export-HcsSupportPackage.

| Parameter | Vereist/optioneel | Beschrijving |
| --- | --- | --- |
| `-Path` |Vereist |Gebruik om de locatie op te geven van de gedeelde netwerkmap waarin het ondersteunings pakket wordt geplaatst. |
| `-EncryptionPassphrase` |Vereist |Gebruiken om een wachtwoordzin op te geven voor het versleutelen van het ondersteunings pakket. |
| `-Credential` |Optioneel |Gebruiken om toegangs referenties op te geven voor de gedeelde netwerkmap. |
| `-Force` |Optioneel |Gebruik om de stap voor het bevestigen van de wachtwoord code voor versleuteling over te slaan. |
| `-PackageTag` |Optioneel |Gebruik om een map op te geven onder het *pad* waarin het ondersteunings pakket wordt geplaatst. De standaard waarde is [apparaatnaam]-[huidige datum en tijd: JJJJ-MM-DD-uu-mm-SS]. |
| `-Scope` |Optioneel |Geef als **cluster** (standaard) op om een ondersteunings pakket voor beide controllers te maken. Als u een pakket alleen voor de huidige controller wilt maken, geeft u **controller** op. |

## <a name="edit-a-support-package"></a>Een ondersteunings pakket bewerken

Nadat u een ondersteunings pakket hebt gegenereerd, moet u het pakket mogelijk bewerken om gevoelige informatie te verwijderen. Dit kunnen volume namen, IP-adressen van apparaten en back-upnamen van de logboek bestanden zijn.

> [!IMPORTANT]
> U kunt alleen een ondersteunings pakket bewerken dat is gegenereerd via Windows PowerShell voor StorSimple. U kunt geen pakket bewerken dat is gemaakt in de Azure Portal met de StorSimple Apparaatbeheer-service.

Als u een ondersteunings pakket wilt bewerken voordat u het uploadt op de Microsoft Ondersteuning-site, moet u eerst het ondersteunings pakket ontsleutelen, de bestanden bewerken en het vervolgens opnieuw versleutelen. Voer de volgende stappen uit.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Een ondersteunings pakket bewerken in Windows PowerShell voor StorSimple

1. Genereer een ondersteunings pakket zoals eerder beschreven, in [om een ondersteunings pakket te maken in Windows PowerShell voor StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Down load het script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokaal op uw client.
3. Importeer de Windows Power shell-module. Geef het pad op naar de lokale map waarin u het script hebt gedownload. Als u de module wilt importeren, voert u het volgende in:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alle bestanden zijn *. AES* -bestanden die zijn gecomprimeerd en versleuteld. Voer het volgende in om bestanden te decomprimeren en te ontsleutelen:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Houd er rekening mee dat de daad werkelijke bestands extensies nu worden weer gegeven voor alle bestanden.
   
    ![Ondersteunings pakket bewerken](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Wanneer u wordt gevraagd om de wachtwoordzin voor versleuteling, voert u de wachtwoordzin in die u hebt gebruikt bij het maken van het ondersteunings pakket.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Blader naar de map met de logboek bestanden. Omdat de logboek bestanden nu worden gedecomprimeerd en ontsleuteld, hebben deze de oorspronkelijke bestands extensies. Wijzig deze bestanden om klantspecifieke informatie, zoals volume namen en IP-adressen van apparaten, te verwijderen en de bestanden op te slaan.
7. Sluit de bestanden om ze te comprimeren met gzip en versleutel ze met AES-256. Dit is voor snelheid en beveiliging bij het overdragen van het ondersteunings pakket via een netwerk. Als u bestanden wilt comprimeren en versleutelen, voert u het volgende in:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Ondersteunings pakket 2 bewerken](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Wanneer u hierom wordt gevraagd, geeft u een wachtwoordzin voor versleuteling op voor het gewijzigde ondersteunings pakket.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Schrijf de nieuwe wachtwoordzin op, zodat u deze kunt delen met Microsoft Ondersteuning wanneer dat wordt gevraagd.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Voor beeld: bestanden bewerken in een ondersteunings pakket op een share die is beveiligd met een wacht woord

In het volgende voor beeld ziet u hoe u een ondersteunings pakket kunt ontsleutelen, bewerken en opnieuw versleutelen.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de informatie die wordt [verzameld in het ondersteunings pakket](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Informatie over het [gebruik van ondersteunings pakketten en logboeken voor apparaten voor het oplossen van problemen met de implementatie van uw apparaten](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

