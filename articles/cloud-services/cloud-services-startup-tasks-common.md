---
title: Veelvoorkomende opstart taken voor Cloud Services (klassiek) | Microsoft Docs
description: Hier vindt u enkele voor beelden van veelvoorkomende opstart taken die u mogelijk wilt uitvoeren in de webfunctie van uw Cloud Services of de werk rollen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f55b225e615a3e7a5fbcf56b405054883d3b5413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741193"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Veelvoorkomende opstart taken voor Cloud service (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Dit artikel bevat enkele voor beelden van veelvoorkomende opstart taken die u in uw Cloud service wilt uitvoeren. U kunt opstart taken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Bewerkingen die u mogelijk wilt uitvoeren, zijn onder andere het installeren van een onderdeel, het registreren van COM-onderdelen, het instellen van register sleutels of het starten van een langlopend proces. 

Raadpleeg [dit artikel](cloud-services-startup-tasks.md) voor meer informatie over de werking van opstart taken en het maken van de vermeldingen waarmee een opstart taak wordt gedefinieerd.

> [!NOTE]
> Opstart taken zijn niet van toepassing op Virtual Machines, alleen voor web-en werk rollen in de Cloud service.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Omgevings variabelen definiëren voordat een rol wordt gestart
Als u omgevings variabelen voor een specifieke taak nodig hebt, gebruikt u het element [omgeving] binnen het element [taak] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Variabelen kunnen ook een [geldige Azure XPath-waarde](cloud-services-role-config-xpath.md) gebruiken om te verwijzen naar iets over de implementatie. In plaats van het `value` kenmerk te gebruiken, definieert u een onderliggend [RoleInstanceValue] -element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Opstarten met IIS configureren met AppCmd.exe
Het opdracht regel programma [AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) kan worden gebruikt om IIS-instellingen te beheren bij het opstarten van Azure. *AppCmd.exe* biedt handige, opdracht regel toegang tot configuratie-instellingen voor gebruik in opstart taken in Azure. U kunt met behulp van *AppCmd.exe* website-instellingen toevoegen, wijzigen of verwijderen voor toepassingen en sites.

Er zijn echter enkele zaken om te bekijken in het gebruik van *AppCmd.exe* als een opstart taak:

* Opstart taken kunnen meer dan één keer worden uitgevoerd tussen het opnieuw opstarten. Bijvoorbeeld wanneer een rol wordt gerecycled.
* Als een *AppCmd.exe* actie meer dan één keer wordt uitgevoerd, kan er een fout optreden. Als u bijvoorbeeld een sectie probeert toe te voegen aan *Web.config* twee keer een fout gegenereerd.
* Opstart taken mislukken als er een afsluit code of **Error level** wordt geretourneerd die niet gelijk is aan nul. Als *AppCmd.exe* bijvoorbeeld een fout genereert.

Het is een goed idee om **Error level** te controleren na het aanroepen van *AppCmd.exe*. Dit is heel eenvoudig als u de aanroep naar *AppCmd.exe* verpakken met een *. cmd* -bestand. Als u een bekend **Error level** -antwoord detecteert, kunt u dit negeren of door sturen.

Het error level dat door *AppCmd.exe* wordt geretourneerd, wordt weer gegeven in het bestand winerror. h en kan ook worden weer gegeven op [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Voor beeld van het beheren van het fout niveau
In dit voor beeld wordt een sectie compressie en een compressie vermelding voor JSON toegevoegd aan het *Web.config* bestand, met fout afhandeling en logboek registratie.

De relevante secties van het bestand [ServiceDefinition. csdef] worden hier weer gegeven, zoals het instellen van het kenmerk [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) om `elevated` *AppCmd.exe* voldoende machtigingen te geven om de instellingen in het *Web.config* bestand te wijzigen:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Het batch bestand *Startup. cmd* maakt gebruik van *AppCmd.exe* om een compressie sectie en een compressie vermelding voor json toe te voegen aan het *Web.config* bestand. Het verwachte **Error level** van 183 wordt ingesteld op nul met behulp van het opdracht regel programma VERIFY.EXE. Onverwachte error levels worden geregistreerd in StartupErrorLog.txt.

```cmd
REM   **_ Add a compression section to the Web.config file. _*_
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   _*_ Add compression for json. _*_
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   _*_ Exit batch file. _*_
EXIT /b 0

REM   _*_ Log error and exit _*_
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Firewall regels toevoegen
In azure zijn er effectief twee firewalls. De eerste firewall beheert verbindingen tussen de virtuele machine en de buiten wereld. Deze firewall wordt bepaald door het element [endpoints] in het bestand [ServiceDefinition. csdef] .

De tweede firewall regelt de verbindingen tussen de virtuele machine en de processen in die virtuele machine. Deze firewall kan worden beheerd met `netsh advfirewall firewall` behulp van het opdracht regel programma.

Azure maakt firewall regels voor de processen die zijn gestart binnen uw rollen. Wanneer u bijvoorbeeld een service of programma start, worden automatisch de benodigde firewall regels gemaakt om ervoor te zorgen dat de service kan communiceren met internet. Als u echter een service maakt die is gestart door een proces buiten uw rol (zoals een COM+-service of een geplande taak van Windows), moet u hand matig een firewall regel maken om toegang tot die service toe te staan. Deze firewall regels kunnen worden gemaakt met behulp van een opstart taak.

Een opstart taak waarmee een firewall regel wordt gemaakt, moet een [executionContext]-[taak] hebben van _ * verhoogde * *. Voeg de volgende opstart taak toe aan het bestand [ServiceDefinition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Als u de firewall regel wilt toevoegen, moet u de juiste `netsh advfirewall firewall` opdrachten in het opstart batch bestand gebruiken. In dit voor beeld is voor de opstart taak beveiliging en versleuteling vereist voor TCP-poort 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Een specifiek IP-adres blok keren
U kunt de toegang van een Azure-webrole beperken tot een set opgegeven IP-adressen door uw IIS **web.config** -bestand te wijzigen. U moet ook een opdracht bestand gebruiken om de sectie **ipSecurity** van het **ApplicationHost.config** -bestand op te heffen.

Als u de sectie **ipSecurity** van het **ApplicationHost.config** bestand wilt ontgrendelen, maakt u een opdracht bestand dat wordt uitgevoerd bij het starten van de rol. Maak een map op het hoofd niveau van de webfunctie met de naam **Start** en maak in deze map een batch bestand met de naam **Startup. cmd**. Voeg dit bestand toe aan uw Visual Studio-project en stel de eigenschappen in op **kopiëren altijd** om ervoor te zorgen dat deze in uw pakket zijn opgenomen.

Voeg de volgende opstart taak toe aan het bestand [ServiceDefinition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Voeg deze opdracht toe aan het bestand **Startup. cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Met deze taak wordt het batch bestand **Startup. cmd** uitgevoerd telkens wanneer de webrole wordt geïnitialiseerd, zodat de vereiste sectie **ipSecurity** wordt ontgrendeld.

Wijzig ten slotte de [sectie System. webserver](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) van het **web.config** -bestand van uw webfunctie om een lijst met IP-adressen toe te voegen die toegang krijgen, zoals wordt weer gegeven in het volgende voor beeld:

Met deze voorbeeld configuratie **kunnen** alle IP-adressen toegang krijgen tot de server, met uitzonde ring van de twee gedefinieerde

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Met deze voorbeeld configuratie worden alle IP-adressen van toegang tot de server **geweigerd** , behalve de twee gedefinieerde.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Een Power shell-opstart taak maken
Windows Power shell-scripts kunnen niet rechtstreeks vanuit het bestand [ServiceDefinition. csdef] worden aangeroepen, maar ze kunnen wel vanuit een batch bestand voor opstarten worden aangeroepen.

Power Shell voert geen niet-ondertekende scripts uit (standaard). Tenzij u uw script ondertekent, moet u Power shell configureren voor het uitvoeren van niet-ondertekende scripts. Voor het uitvoeren van niet-ondertekende scripts moet **ExecutionPolicy** zijn ingesteld op **onbeperkt**. De **ExecutionPolicy** -instelling die u gebruikt, is gebaseerd op de versie van Windows Power shell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Als u een gast besturingssysteem gebruikt waarop Power Shell 2,0 of 1,0 wordt uitgevoerd, kunt u ervoor zorgen dat versie 2 wordt uitgevoerd. Als u dit niet wilt, gebruikt u versie 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Bestanden in lokale opslag maken van een opstart taak
U kunt een lokale opslag Resource gebruiken om bestanden op te slaan die zijn gemaakt door de opstart taak en later door uw toepassing worden geopend.

Als u de lokale opslag resource wilt maken, voegt u een sectie [LocalResources] toe aan het bestand [ServiceDefinition. csdef] en voegt u vervolgens het onderliggende element [LocalStorage] toe. Geef de lokale opslag resource een unieke naam en een geschikte grootte voor de opstart taak.

Als u een lokale opslag resource in uw opstart taak wilt gebruiken, moet u een omgevings variabele maken om te verwijzen naar de locatie van de lokale opslag resource. De opstart taak en de toepassing kunnen vervolgens bestanden lezen en schrijven naar de lokale opslag resource.

De relevante secties van het bestand **ServiceDefinition. csdef** worden hier weer gegeven:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Als voor beeld gebruikt dit **Startup. cmd** batch-bestand de omgevings variabele **PathToStartupStorage** om het bestand te maken **MyTest.txt** op de lokale opslag locatie.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

U kunt toegang krijgen tot de lokale opslagmap vanuit de Azure SDK met behulp van de methode [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uitvoeren in de emulator of in de Cloud
U kunt de opstart taak gebruiken om verschillende stappen uit te voeren wanneer deze in de cloud worden uitgevoerd, vergeleken met wanneer deze zich in de compute-emulator bevinden. Het is bijvoorbeeld mogelijk dat u een nieuwe kopie van uw SQL-gegevens wilt gebruiken wanneer deze wordt uitgevoerd in de emulator. Het is ook mogelijk dat u een aantal prestatie optimalisaties wilt uitvoeren voor de cloud die u niet hoeft te doen wanneer u in de emulator wordt uitgevoerd.

Deze mogelijkheid om verschillende acties uit te voeren op de compute-emulator en de cloud kan worden gerealiseerd door een omgevings variabele te maken in het bestand [ServiceDefinition. csdef] . Vervolgens test u de omgevings variabele voor een waarde in de opstart taak.

Als u de omgevings variabele wilt maken [], voegt u het / [RoleInstanceValue] -element variabele toe en maakt u een XPath-waarde van `/RoleEnvironment/Deployment/@emulated` . De waarde van de omgevings variabele **% ComputeEmulatorRunning%** is `true` wanneer deze wordt uitgevoerd op de compute-emulator en `false` wanneer deze wordt uitgevoerd in de Cloud.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

De taak kan nu de **% ComputeEmulatorRunning%** -omgevings variabele controleren om verschillende acties uit te voeren op basis van het feit of de functie wordt uitgevoerd in de Cloud of de emulator. Hier volgt een cmd shell-script dat controleert op die omgevings variabele.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detecteren dat de taak al is uitgevoerd
De rol kan worden herhaald zonder opnieuw op te starten, waardoor de opstart taken opnieuw worden uitgevoerd. Er is geen vlag waarmee wordt aangegeven dat een taak al is uitgevoerd op de hosting-VM. Mogelijk hebt u een aantal taken die niet van belang zijn om meerdere keren uit te voeren. U kunt echter een situatie uitvoeren waarin wordt voor komen dat een taak meer dan één keer wordt uitgevoerd.

De eenvoudigste manier om te detecteren dat een taak al is uitgevoerd, is door een bestand te maken in de map **% temp%** wanneer de taak is voltooid en te zoeken naar het begin van de taak. Hier volgt een voor beeld van een cmd shell-script dat voor u is.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Best practices voor taken
Hier volgen enkele aanbevolen procedures voor het configureren van de taak voor uw web-of worker-rol.

### <a name="always-log-startup-activities"></a>Opstart activiteiten altijd vastleggen in logboek
Visual Studio biedt geen fout opsporing voor het uitvoeren van batch-bestanden, zodat u zoveel mogelijk gegevens kunt ophalen over de werking van batch-bestanden. Het registreren van de uitvoer van batch bestanden, **stdout** en **stderr**, kan u belang rijke informatie geven bij het opsporen van fouten en het herstellen van batch bestanden. Als u **stdout** en **stderr** wilt vastleggen in het StartupLog.txt bestand in de map waarnaar wordt geverwijst door de **% temp%** -omgevings variabele, voegt u de tekst toe `>>  "%TEMP%\\StartupLog.txt" 2>&1` aan het einde van specifieke regels die u wilt registreren. Als u bijvoorbeeld setup.exe wilt uitvoeren in de map **% PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Als u uw XML wilt vereenvoudigen, kunt u een wrapper- *cmd* -bestand maken dat alle opstart taken samen met logboek registratie aanroept en zorgt ervoor dat alle onderliggende taken dezelfde omgevings variabelen delen.

Het kan voor komen dat u aan `>> "%TEMP%\StartupLog.txt" 2>&1` het einde van elke opstart taak kunt gebruiken. U kunt de taak logboek registratie afdwingen door een wrapper te maken waarmee logboek registratie voor u wordt afgehandeld. Deze wrapper roept het daad werkelijke batch-bestand op dat u wilt uitvoeren. Alle uitvoer van het doel batch bestand wordt omgeleid naar het *Startuplog.txt* -bestand.

In het volgende voor beeld ziet u hoe u alle uitvoer van een batch-opstart bestand omleidt. In dit voor beeld maakt het bestand ServerDefinition. csdef een opstart taak die *logwrap. cmd* aanroept. *logwrap. cmd* roept *Startup2. cmd* aan, waarbij alle uitvoer wordt omgeleid naar **% temp% \\StartupLog.txt**.

ServiceDefinition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2. cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Voorbeeld uitvoer in het **StartupLog.txt** -bestand:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Het **StartupLog.txt** -bestand bevindt zich in de map *C:\Resources\temp \\ {Role Identifier} \RoleTemp* .
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>ExecutionContext op de juiste wijze instellen voor opstart taken
Stel de machtigingen op de juiste wijze in voor de opstart taak. Soms moeten opstart taken worden uitgevoerd met verhoogde bevoegdheden, zelfs als de rol wordt uitgevoerd met normale bevoegdheden.

Met het[taak] kenmerk [executionContext]wordt het bevoegdheden niveau van de opstart taak ingesteld. Als u gebruikt `executionContext="limited"` , heeft de opstart taak hetzelfde bevoegdheids niveau als de rol. Gebruiken `executionContext="elevated"` houdt in dat de opstart taak beheerders bevoegdheden heeft, waarmee de opstart taak beheerders taken kan uitvoeren zonder beheerders bevoegdheden voor uw rol te geven.

Een voor beeld van een opstart taak waarvoor verhoogde bevoegdheden zijn vereist, is een opstart taak die gebruikmaakt van **AppCmd.exe** om IIS te configureren. **AppCmd.exe** vereist `executionContext="elevated"` .

### <a name="use-the-appropriate-tasktype"></a>De juiste taskType gebruiken
Het [TaskType]-[taak] kenmerk bepaalt hoe de opstart taak wordt uitgevoerd. Er zijn drie waarden: **eenvoudig**, op de **achtergrond** en op de voor **grond**. De achtergrond-en voorgrond taken worden asynchroon gestart, waarna de eenvoudige taken synchroon worden uitgevoerd.

Met **eenvoudige** opstart taken kunt u de volg orde instellen waarin de taken worden uitgevoerd in de volg orde waarin de taken worden weer gegeven in het bestand ServiceDefinition. csdef. Als een **eenvoudige** taak eindigt met een afsluit code die niet gelijk is aan nul, wordt de opstart procedure gestopt en wordt de rol niet gestart.

Het verschil tussen opstart taken op de **achtergrond** en opstart taken op de voor **grond** **is dat de** functie wordt uitgevoerd totdat de **voorgrond** taak wordt beëindigd. Dit betekent ook dat als de **voorgrond** taak vastloopt of vastloopt, de rol niet opnieuw wordt gerecycled totdat de **voorgrond** taak geforceerd is gesloten. Daarom worden **achtergrond** taken aanbevolen voor asynchrone opstart taken, tenzij u die functie van de **voorgrond** taak nodig hebt.

### <a name="end-batch-files-with-exit-b-0"></a>Batch-bestanden beëindigen met EXIT/B 0
De rol wordt alleen gestart als de **Error level** van elk van uw eenvoudige opstart taken nul is. Niet alle Program ma's hebben de status van **Error level** (afsluit code) correct ingesteld, dus het batch bestand moet eindigen met een `EXIT /B 0` als alles goed is uitgevoerd.

Een ontbrekend `EXIT /B 0` aan het einde van een batch bestand voor opstarten is een veelvoorkomende oorzaak van de functies die niet worden gestart.

> [!NOTE]
> Ik heb opgemerkt dat geneste batch bestanden soms niet meer reageren wanneer de `/B` para meter wordt gebruikt. U kunt er ook voor zorgen dat dit probleem niet optreedt als een ander batch bestand het huidige batch bestand aanroept, bijvoorbeeld als u de [logboek-wrapper](#always-log-startup-activities)gebruikt. U kunt de `/B` para meter in dit geval weglaten.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Verwacht dat opstart taken meer dan één keer worden uitgevoerd
Niet alle rollen recyclen bevatten een herstart, maar alle functie herhalingen zijn het uitvoeren van alle opstart taken. Dit betekent dat opstart taken meerdere keren moeten kunnen worden uitgevoerd tussen het opnieuw opstarten zonder dat er problemen zijn. Dit wordt beschreven in de [vorige sectie](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Lokale opslag gebruiken om bestanden op te slaan die toegankelijk moeten zijn in de rol
Als u tijdens de opstart taak een bestand wilt kopiëren of maken dat vervolgens toegankelijk is voor uw rol, dan moet dat bestand in lokale opslag worden geplaatst. Zie de [voor gaande sectie](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Volgende stappen
Bekijk het Cloud [service model en het pakket](cloud-services-model-and-package.md)

Meer informatie over hoe [taken](cloud-services-startup-tasks.md) werken.

[Maak en implementeer](cloud-services-how-to-create-deploy-portal.md) uw Cloud service pakket.

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Taak]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Omgeving]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variabele]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Eindpunten]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue