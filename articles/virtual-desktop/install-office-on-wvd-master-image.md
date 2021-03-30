---
title: Office installeren op een hoofd-VHD-installatie kopie-Azure
description: Office installeren en aanpassen op basis van een installatie kopie van een virtueel bureau blad van Windows in Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326045"
---
# <a name="install-office-on-a-master-vhd-image"></a>Office installeren op een master-VHD-installatiekopie

In dit artikel leest u hoe u Microsoft 365-apps voor Enter prise, OneDrive en andere algemene toepassingen op een master-image van een virtuele harde schijf (VHD) installeert voor het uploaden naar Azure. Als uw gebruikers toegang moeten hebben tot bepaalde LOB-toepassingen (line-of-Business), raden wij u aan deze te installeren nadat u de instructies in dit artikel hebt voltooid.

In dit artikel wordt ervan uitgegaan dat u al een virtuele machine (VM) hebt gemaakt. Als dat niet het geval is, raadpleegt u [een VHD-master installatie kopie voorbereiden en aanpassen](set-up-customize-master-image.md#create-a-vm)

In dit artikel wordt ervan uitgegaan dat u toegang hebt tot verhoogde bevoegdheden op de virtuele machine, ongeacht of deze is ingericht in azure of Hyper-V-beheer. Als dat niet het geval is, raadpleegt [u toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Deze instructies gelden voor een Windows-specifieke configuratie voor virtueel bureau blad die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="install-office-in-shared-computer-activation-mode"></a>Office installeren in de activerings modus gedeelde computer

Met de activering van gedeelde computers kunt u Microsoft 365-apps voor ondernemingen implementeren op een computer in uw organisatie die wordt geopend door meerdere gebruikers. Zie [overzicht van gedeelde computer activering voor Microsoft 365-apps](/deployoffice/overview-shared-computer-activation)voor meer informatie over het activeren van gedeelde computers.

Gebruik het [hulp programma voor implementatie van Office](https://www.microsoft.com/download/details.aspx?id=49117) om Office te installeren. Windows 10 Enter prise multi-session ondersteunt alleen de volgende versies van Office:

   - Microsoft 365-apps voor ondernemingen
   - Microsoft 365-apps voor bedrijven die worden geleverd met een Microsoft 365 Business Premium-abonnement

Voor het Office Deployment Tool is een XML-configuratie bestand vereist. Als u het volgende voor beeld wilt aanpassen, raadpleegt u de [configuratie opties voor het hulp programma Office-implementatie](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Deze XML-voorbeeld configuratie heeft de volgende kenmerken:

   - Installeer Office vanuit het maandelijkse bedrijfs kanaal en lever updates via het maandelijkse bedrijfs kanaal.
   - Gebruik de x64-architectuur.
   - Automatische updates uitschakelen.
   - Verwijder alle bestaande installaties van Office en migreer de instellingen.
   - Activering van gedeelde computer inschakelen.

>[!NOTE]
>De functie voor het zoeken naar het stencil van Visio werkt mogelijk niet zoals verwacht in Windows virtueel bureau blad.

Dit is de XML-voorbeeld configuratie:

   - Skype voor bedrijven installeren
   - Installeer OneDrive in de modus per gebruiker. Zie voor meer informatie [OneDrive installeren in de modus per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Activering van gedeelde computers kan worden ingesteld via groepsbeleid objecten (Gpo's) of register instellingen. Het groeps beleidsobject bevindt zich in **computer configuratie \\ beleid \\ Beheersjablonen de licentie- \\ \\ instellingen voor Microsoft Office 2016 (computer)**

Het Office Deployment Tool bevat setup.exe. Als u Office wilt installeren, voert u de volgende opdracht uit op de opdracht regel:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Voorbeeld configuration.xml

Met het volgende XML-voor beeld wordt de maandelijkse versie van het Enter prise Channel geïnstalleerd.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Het Office-team raadt aan om de 64-bits installatie te gebruiken voor de para meter **OfficeClientEdition** .

Na de installatie van Office kunt u het standaard gedrag van Office bijwerken. Voer de volgende opdrachten afzonderlijk of in een batch-bestand uit om het gedrag bij te werken.

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>OneDrive installeren in de modus per computer

OneDrive wordt normaal gesp roken per gebruiker geïnstalleerd. In deze omgeving moet deze per computer worden geïnstalleerd.

U kunt als volgt OneDrive installeren in de modus per computer:

1. Maak eerst een locatie voor het faseren van het OneDrive-installatie programma. Een lokale schijf map of [ \\ \\ UNC]-locatie (File://UNC) is nauw keurig.

2. Down load OneDriveSetup.exe naar uw gefaseerde locatie met deze koppeling: <https://aka.ms/OneDriveWVD-Installer>

3. Als u Office hebt geïnstalleerd met OneDrive door niets **\<ExcludeApp ID="OneDrive" /\>** te verwijderen, verwijdert u alle bestaande installaties per gebruiker van onedrive van een opdracht prompt met verhoogde bevoegdheid door de volgende opdracht uit te voeren:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Voer deze opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om de register waarde **AllUsersInstall** in te stellen:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Voer deze opdracht uit om OneDrive te installeren in de modus per computer:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Voer deze opdracht uit om OneDrive te configureren om te beginnen bij het aanmelden voor alle gebruikers:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Schakel **gebruikers account op de achtergrond configureren** uit door de volgende opdracht uit te voeren.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omleiden en verplaatsen van Windows bekende mappen naar OneDrive door de volgende opdracht uit te voeren.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Micro soft teams en Skype voor bedrijven

Het virtuele bureau blad van Windows biedt geen ondersteuning voor Skype voor bedrijven.

Voor hulp bij het installeren van micro soft teams raadpleegt [u micro soft teams gebruiken op virtuele Windows-bureau blad](teams-on-wvd.md). Media optimalisatie voor micro soft teams op het virtuele bureau blad van Windows is beschikbaar als preview-versie.

## <a name="next-steps"></a>Volgende stappen

Nu u Office aan de installatie kopie hebt toegevoegd, kunt u de installatie kopie van uw hoofd-VHD blijven aanpassen. Zie [een VHD-master installatie kopie voorbereiden en aanpassen](set-up-customize-master-image.md).
