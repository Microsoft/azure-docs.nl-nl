---
title: .NET-rollen in azure Cloud Services (klassieke) installeren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de .NET Framework hand matig installeert op de web-en werk rollen van uw Cloud service
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99061848"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>.NET Cloud Services-rollen (klassiek) installeren in azure

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel wordt beschreven hoe u versies van .NET Framework installeert die niet worden meegeleverd met het Azure-gast besturingssysteem. U kunt .NET gebruiken op het gast besturingssysteem om uw web-en werk rollen van de Cloud service te configureren.

U kunt bijvoorbeeld .NET Framework 4.6.2 installeren op de gast besturingssysteem familie 4, die niet wordt geleverd met een versie van .NET Framework 4,6. (De gast besturingssysteem familie 5 wordt geleverd met .NET Framework 4,6.) Zie het [Azure Guest OS release News](cloud-services-guestos-update-matrix.md)(Engelstalig) voor de meest recente informatie over de versies van het Azure-gast besturingssysteem. 

>[!IMPORTANT]
>De Azure SDK 2,9 bevat een beperking voor het implementeren van .NET Framework 4,6 op de gast besturingssysteem familie 4 of eerder. Er is een oplossing voor de beperking beschikbaar op de [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) -site.

Als u .NET wilt installeren op uw web-en werk rollen, moet u de .NET Web Installer opnemen als onderdeel van uw Cloud service-project. Start het installatie programma als onderdeel van de opstart taken van de rol. 

## <a name="add-the-net-installer-to-your-project"></a>Het .NET-installatie programma toevoegen aan uw project
Als u het web-installatie programma voor de .NET Framework wilt downloaden, kiest u de versie die u wilt installeren:

* [.NET Framework 4,8 web installer](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [.NET Framework 4.7.2 web installer](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 web installer](https://www.microsoft.com/download/details.aspx?id=53345)

Het installatie programma voor een *webrol toevoegen* :
  1. Klik in **Solution Explorer** onder **rollen** *in uw Cloud* service project met de rechter muisknop op uw webfunctie en Selecteer  >  **nieuwe map** toevoegen. Maak een map met de naam **bin**.
  2. Klik met de rechter muisknop op de map bin en selecteer  >  **bestaand item** toevoegen. Selecteer .NET Installer en voeg deze toe aan de bin-map.
  
Het installatie programma voor een *werk* rollen toevoegen:
* Klik *met de rechter* muisknop op uw werknemersrol en selecteer  >  **bestaand item** toevoegen. Selecteer .NET Installer en voeg deze toe aan de rol. 

Wanneer bestanden op deze manier aan de map rol inhoud worden toegevoegd, worden ze automatisch toegevoegd aan uw Cloud service pakket. De bestanden worden vervolgens geïmplementeerd op een consistente locatie op de virtuele machine. Herhaal dit proces voor elke web-en werk functie in uw Cloud service, zodat alle rollen een kopie van het installatie programma hebben.

> [!NOTE]
> U moet .NET Framework 4.6.2 in de Cloud service functie installeren, zelfs als uw toepassings doelen .NET Framework 4,6. Het gast besturingssysteem bevat de Knowledge Base [update 3098779](https://support.microsoft.com/kb/3098779) en [Update 3097997](https://support.microsoft.com/kb/3097997). Er kunnen problemen optreden wanneer u uw .NET-toepassingen uitvoert als .NET Framework 4,6 boven op de Knowledge Base-updates wordt geïnstalleerd. Als u deze problemen wilt voor komen, installeert u .NET Framework 4.6.2 in plaats van versie 4,6. Zie het [Knowledge Base-artikel 3118750](https://support.microsoft.com/kb/3118750) en [4340191](https://support.microsoft.com/kb/4340191)voor meer informatie.
> 
> 

![Inhoud van rollen met Installer-bestanden][1]

## <a name="define-startup-tasks-for-your-roles"></a>Opstart taken voor uw rollen definiëren
U kunt opstart taken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Als u de .NET Framework als onderdeel van de opstart taak installeert, zorgt u ervoor dat het Framework wordt geïnstalleerd voordat een toepassings code wordt uitgevoerd. Zie [opstart taken uitvoeren in azure](cloud-services-startup-tasks.md)voor meer informatie over opstart taken. 

1. Voeg de volgende inhoud toe aan het bestand ServiceDefinition. csdef onder het knoop punt **webrole** of **WorkerRole** voor alle rollen:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    De voor gaande configuratie voert de console opdracht `install.cmd` uit met beheerders bevoegdheden om de .NET Framework te installeren. De configuratie maakt ook een **LocalStorage** -element met de naam **NETFXInstall**. Met het opstart script wordt de map Temp ingesteld voor het gebruik van deze lokale opslag resource. 
    
    > [!IMPORTANT]
    > Om ervoor te zorgen dat het Framework correct wordt geïnstalleerd, stelt u de grootte van deze resource in op ten minste 1.024 MB.
    
    Zie [algemene Azure Cloud Services startup tasks](cloud-services-startup-tasks-common.md)(Engelstalig) voor meer informatie over opstart taken.

2. Maak een bestand met de naam **install. cmd** en voeg het volgende installatie script toe aan het bestand.

   Met het script wordt gecontroleerd of de opgegeven versie van de .NET Framework al is geïnstalleerd op de machine door het REGI ster te doorzoeken. Als de .NET Framework-versie niet is geïnstalleerd, wordt de .NET Framework web installer geopend. Om problemen op te lossen, worden alle activiteiten door het script geregistreerd in het bestand startuptasklog-(huidige datum en tijd). txt dat is opgeslagen in **InstallLogs** lokale opslag.
   
   > [!IMPORTANT]
   > Gebruik een eenvoudige tekst editor, zoals Windows Klad blok, om het bestand install. cmd te maken. Als u Visual Studio gebruikt om een tekst bestand te maken en de extensie te wijzigen in. cmd, bevat het bestand mogelijk nog een UTF-8-byte order mark. Dit merk kan een fout veroorzaken wanneer de eerste regel van het script wordt uitgevoerd. U kunt deze fout voor komen door de eerste regel van het script een REM-instructie te geven die kan worden overgeslagen door de verwerking van de byte-order. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Voeg het bestand install. cmd aan elke rol toe met behulp van  >  **bestaand item** toevoegen in **Solution Explorer** zoals eerder in dit onderwerp wordt beschreven. 

    Nadat deze stap is voltooid, moeten alle rollen het .NET-installatie bestand en het bestand install. cmd hebben.

   ![Inhoud van rollen met alle bestanden][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Diagnostische gegevens configureren voor het overdragen van opstart logboeken naar Blob Storage
Als u problemen met de installatie wilt vereenvoudigen, kunt u Azure Diagnostics configureren voor het overdragen van logboek bestanden die door het opstart script of .NET Installer worden gegenereerd naar Azure Blob-opslag. Als u deze methode gebruikt, kunt u de logboeken bekijken door de logboek bestanden te downloaden uit de Blob-opslag in plaats van extern bureau blad te gebruiken voor de rol.


Als u Diagnostische gegevens wilt configureren, opent u het bestand Diagnostics. wadcfgx en voegt u de volgende inhoud toe onder het knoop punt **directory's** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Met deze XML worden diagnostische gegevens geconfigureerd voor het overdragen van de bestanden in de logboekmap in de **NETFXInstall** -bron naar het diagnostische-opslag account in de **netfx-installatie** van de BLOB-container.

## <a name="deploy-your-cloud-service"></a>Uw Cloud service implementeren
Wanneer u uw Cloud service implementeert, installeren de opstart taken de .NET Framework als deze nog niet is geïnstalleerd. De Cloud service rollen zijn in de status *bezet* terwijl het Framework wordt geïnstalleerd. Als de Framework-installatie opnieuw moet worden gestart, kunnen de service rollen ook opnieuw worden gestart. 

## <a name="additional-resources"></a>Aanvullende bronnen
* [De .NET Framework installeren][Installing the .NET Framework]
* [Bepalen welke .NET Framework versies worden geïnstalleerd][How to: Determine Which .NET Framework Versions Are Installed]
* [Problemen met .NET Framework-installaties oplossen][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



