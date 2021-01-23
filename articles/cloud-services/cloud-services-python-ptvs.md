---
title: Aan de slag met python en Azure Cloud Services (klassiek) | Microsoft Docs
description: Overzicht van het gebruik van Python-tools voor Visual Studio voor het maken van Azure Cloud Services, met inbegrip van webrollen en werkrollen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 16aa6918c0f4b0df5ebf23f28268f8cbe5223fce
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743284"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Python-web- en -werkrollen met Python-tools voor Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Dit artikel biedt een overzicht van het gebruik van Python-web- en -werkrollen met [Python Tools for Visual Studio][Python Tools for Visual Studio]. U kunt hier lezen hoe u met Visual Studio een eenvoudige cloudservice maakt en implementeert die gebruikmaakt van Python.

## <a name="prerequisites"></a>Vereisten
* [Visual Studio 2013, 2015 of 2017](https://www.visualstudio.com/)
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK-hulpprogramma’s voor VS 2013][Azure SDK Tools for VS 2013] of  
[Azure SDK-hulpprogramma’s voor VS 2015][Azure SDK Tools for VS 2015] of  
[Azure SDK-tools voor VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 32-bits][Python 2.7 32-bit] of [Python 3.5 32-bits][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Wat zijn Python-web- en -werkrollen?
Azure biedt drie rekenmodellen voor het uitvoeren van toepassingen: [web-appsfunctie in Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] en [Azure Cloud Services][execution model-cloud services]. Alle drie modellen ondersteunen Python. Cloud Services, die web- en werkrollen bevatten, bieden *Platform as a Service (PaaS)*. Binnen een cloudservice biedt een webrol een speciale IIS-webserver (Internet Information Services) voor het hosten van front-end webtoepassingen. Een werkrol kan asynchrone langlopende of permanente taken uitvoeren onafhankelijk van de interactie of invoer van de gebruiker.

Zie [Wat is een cloudservice?] voor meer informatie.

> [!NOTE]
> *Wilt u een eenvoudige website bouwen?*
> Als uw scenario slechts een ongecompliceerde website-front-end omvat, kunt u overwegen de eenvoudige functie Web Apps in Azure App Service te gebruiken. U kunt vervolgens gemakkelijk upgraden naar een cloudservice naarmate uw website groeit en de vereisten veranderen. Zie het [Python Developer Center](https://azure.microsoft.com/develop/python/) voor artikelen over ontwikkeling met de functie Web Apps in Azure App Service.
> <br />
> 
> 

## <a name="project-creation"></a>Een project maken
In Visual Studio kunt u **Azure Cloud Service** selecteren in het dialoogvenster **Nieuw project** onder **Python**.

![Het dialoogvenster Nieuw project](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

U kunt nieuwe web- en werkrollen maken in de Azure Cloud Service-wizard.

![Het dialoogvenster Azure Cloud Service](./media/cloud-services-python-ptvs/new-service-wizard.png)

De werkrolsjabloon wordt geleverd met standaardcode om verbinding maken met een Azure-opslagaccount of Azure Service Bus.

![Cloudserviceoplossing](./media/cloud-services-python-ptvs/worker.png)

U kunt op elk gewenst moment web- of werkrollen toevoegen aan een bestaande cloudservice.  U kunt bestaande projecten aan uw oplossing toevoegen of nieuwe maken.

![De opdracht Rol toevoegen](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Uw cloudservice kan rollen bevatten die zijn geïmplementeerd in verschillende talen.  U kunt bijvoorbeeld een Python-webrol hebben die is geïmplementeerd met Django, met Python of met C#-werkrollen.  U kunt eenvoudig communiceren tussen uw rollen met behulp van de Service Bus-wachtrijen of opslagwachtrijen.

## <a name="install-python-on-the-cloud-service"></a>Python installeren op de cloudservice
> [!WARNING]
> De installatiescripts die met Visual Studio zijn geïnstalleerd, werken niet (op het moment dat dit artikel voor het laatst werd bijgewerkt). Deze sectie beschrijft een tijdelijke oplossing.
> 
> 

Het belangrijkste probleem met de installatiescripts is dat Python hiermee niet wordt geïnstalleerd. Eerst definieert u twee [opstarttaken](cloud-services-startup-tasks.md) in het bestand [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). De eerste taak (**PrepPython.ps1**) downloadt en installeert de Python-runtime. De tweede taak (**PipInstaller.ps1**) voert pip uit om alle afhankelijkheden die u mogelijk hebt, te installeren.

De volgende scripts zijn geschreven voor gebruik met Python 3.5. Als u versie 2.x van Python wilt gebruiken, stelt u de variabele **PYTHON2** in het bestand in op **on** (aan) voor de twee opstarttaken en de runtimetaak: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

De variabelen **PYTHON2** en **PYPATH** moeten aan de opstarttaak van de werkrol worden toegevoegd. De variabele **PYPATH** wordt alleen gebruikt als de variabele **PYTHON2** is ingesteld op **on** (aan).

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Voorbeeld van ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Maak vervolgens de bestanden **PrepPython.ps1** en **PipInstaller.ps1** in de map **./bin** van uw rol.

#### <a name="preppythonps1"></a>PrepPython.ps1
Dit script installeert Python. Als de omgevingsvariabele **PYTHON2** is ingesteld op **on**, wordt Python 2.7 geïnstalleerd; anders wordt Python 3.5 geïnstalleerd.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Met dit script wordt pip aangeroepen en worden alle afhankelijkheden in het bestand **requirements.txt** geïnstalleerd. Als de omgevingsvariabele **PYTHON2** is ingesteld op **on**, wordt Python 2.7 gebruikt; anders wordt Python 3.5 gebruikt.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>LaunchWorker.ps1 wijzigen
> [!NOTE]
> In het geval van een project met een **werkrol** is het bestand **LauncherWorker.ps1** vereist om het opstartbestand uit te voeren. Bij een project met een **webrol** wordt het opstartbestand in plaats daarvan gedefinieerd in de projecteigenschappen.
> 
> 

Het bestand **bin\LaunchWorker.ps1** is oorspronkelijk gemaakt om heel wat voorbereidende taken te verrichten, maar werkt niet echt. Vervang de inhoud van dat bestand door het volgende script.

Met dit script wordt het bestand **worker.py** van uw Python-project aangeroepen. Als de omgevingsvariabele **PYTHON2** is ingesteld op **on**, wordt Python 2.7 gebruikt; anders wordt Python 3.5 gebruikt.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
De Visual Studio-sjablonen zouden in de map **./bin** een bestand **ps.cmd** moeten hebben gemaakt. Dit shellscript roept de bovenstaande PowerShell-wrapperscripts aan en biedt logboekregistratie op basis van de naam van de aangeroepen PowerShell-wrapper. Als dit bestand niet is gemaakt, ziet u hieronder wat het moet bevatten. 

```cmd
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Lokaal uitvoeren
Als u uw cloudserviceproject instelt als opstartproject en op F5 drukt, wordt de cloudservice in de lokale Azure-emulator uitgevoerd.

Hoewel PTVS opstarten in de emulator ondersteunt, werkt foutopsporing (bijvoorbeeld onderbrekingspunten) niet.

Voor foutopsporing in uw web- en werkrollen kunt u het rolproject instellen als opstartproject en daarop foutopsporing uitvoeren.  U kunt ook meerdere opstartprojecten instellen.  Klik met de rechtermuisknop op de oplossing en selecteer vervolgens **Opstartprojecten instellen**.

![Eigenschappen van opstartprojecten in de oplossing](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publiceren naar Azure
Om te publiceren, klikt u met de rechtermuisknop op het cloudserviceproject in de oplossing en selecteert u vervolgens **Publiceren**.

![Aanmelden voor publiceren in Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Volg de wizard. Schakel Extern bureaublad in, als dat nodig is. Extern bureaublad is handig wanneer u ergens fouten in moet opsporen.

Klik op **Publiceren** wanneer u klaar bent met het configureren van instellingen.

De voortgang wordt gedeeltelijk weergegeven in het uitvoervenster. Vervolgens ziet u het venster Activiteitenlogboek van Microsoft Azure.

![Het venster Activiteitenlogboek van Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

De implementatie duurt enkele minuten. Daarna worden uw web- en/of werkrollen uitgevoerd in Azure.

### <a name="investigate-logs"></a>Logboeken onderzoeken
Nadat de virtuele machine van de cloudservice wordt gestart en Python installeert, kunt u de logboeken controleren op foutberichten. Deze logboeken bevinden zich in de map **map c:\resources\directory \\ {Role} \LogFiles** . **PrepPython.err.txt** bevat ten minste één fout doordat het script probeert te detecteren of Python is geïnstalleerd. Mogelijk geeft **PipInstaller.err.txt** ook aan dat de versie van pip is verouderd.

## <a name="next-steps"></a>Volgende stappen
Zie de documentatie bij PTVS voor meer informatie over het werken met web- en werkrollen in Python-tools voor Visual Studio:

* [Cloudserviceprojecten][Cloud Service Projects]

Zie de volgende artikelen voor meer informatie over het gebruik van Azure-services via uw web- en werkrollen, zoals het gebruik van Azure Storage of Service Bus:

* [BLOB-service][Blob Service]
* [Tabelservice][Table Service]
* [Queue-service][Queue Service]
* [Service Bus wachtrijen][Service Bus Queues]
* [Service Bus onderwerpen][Service Bus Topics]

<!--Link references-->

[Wat is een cloudservice?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: /visualstudio/python/
[Cloud Service Projects]: /visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/