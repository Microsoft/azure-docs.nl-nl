---
title: Opstart fout van rolinstantie voor Azure Cloud Services (uitgebreide ondersteuning)
description: Opstart fouten van Rolinstantie voor Azure Cloud Services oplossen (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382352"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Problemen met rollen van Azure Cloud service (uitgebreide ondersteuning) oplossen die niet worden gestart
Hier volgen enkele veelvoorkomende problemen en oplossingen met betrekking tot de rollen van Cloud Services (uitgebreide ondersteuning) die niet worden gestart.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Cloud service bewerking is mislukt met RoleInstanceStartupTimeoutError
Een of meer rolinstanties van uw service (uitgebreide ondersteuning) kunnen niet worden gestart in de voorgeschreven tijd. Het kan enige tijd duren voordat deze rolinstanties worden gestart of gerecycled en de rolinstantie kan mislukken met een RoleInstanceStartupTimeoutError. Dit is een functie toepassings fout. De functie toepassing bevat twee belang rijke onderdelen: ' startup tasks ' en ' Role code (implementatie van RoleEntryPoint) ', beide kunnen ertoe leiden dat de rol wordt gerecycled. Als de rol is vastgelopen, wordt de PaaS-agent altijd opnieuw gestart. 

Als u de huidige status en Details van de rolinstanties in geval van fouten wilt ophalen, gebruikt u:

* Power shell: gebruik de cmdlet [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) om informatie op te halen over de runtime status van een rolinstantie in een Cloud service.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: Ga naar de Cloud service en selecteer het tabblad rollen en exemplaren. Klik op de rolinstantie om de status gegevens op te halen 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Afbeelding toont fout bij het starten van een rol op de portal.":::
   
Hier volgen enkele veelvoorkomende problemen en oplossingen met betrekking tot rollen van Azure Cloud Services (uitgebreide ondersteuning) die niet worden gestart of die worden uitgevoerd tussen de status initialisatie, bezet en gestopt.

## <a name="missing-dlls-or-dependencies"></a>Ontbrekende Dll's of afhankelijkheden
Niet-reagerende rollen en rollen die worden uitgevoerd tussen de initialisatie, bezet en het stoppen van de status, kunnen worden veroorzaakt door ontbrekende Dll's of assembly's.
Symptomen van ontbrekende Dll's of assembly's kunnen zijn:

* Uw rolinstantie wordt uitgevoerd door de **initialisatie**, **bezet** en de status van het **stoppen** .
* De rolinstantie is verplaatst naar **gereed** , maar als u naar uw webtoepassing navigeert, wordt de pagina niet weer gegeven.

Er zijn verschillende aanbevolen methoden om deze problemen te onderzoeken.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Ontbrekende DLL-problemen in een webfunctie diagnosticeren
Wanneer u navigeert naar een website die is geïmplementeerd in een webrole, en de browser een server fout weergeeft die lijkt op het volgende, kan dit erop duiden dat er een DLL-bestand ontbreekt.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Afbeelding toont runtime fout bij opstart fout van rol":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problemen vaststellen door aangepaste fouten uit te scha kelen
Meer informatie over de fout kan worden weer gegeven door de web.config voor de webfunctie te configureren om de aangepaste fout modus in te stellen op uit en de service opnieuw te implementeren.
Meer volledige fouten weer geven zonder Extern bureaublad te gebruiken:
1.  Open de oplossing in micro soft Visual Studio.
2.  Zoek in het Solution Explorer het web.config bestand en open het.
3.  Zoek in het web.config-bestand de sectie System. Web en voeg de volgende regel toe:
 ```xml
<customErrors mode="Off" />
```
4.  Sla het bestand op.
5.  De service opnieuw te verpakken en opnieuw te implementeren.
Zodra de service opnieuw is geïmplementeerd, wordt er een fout bericht weer gegeven met de naam van de ontbrekende assembly of DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problemen vaststellen door de fout op afstand te bekijken
U kunt Extern bureaublad gebruiken om toegang te krijgen tot de rol en meer informatie over de fout extern te bekijken. Gebruik de volgende stappen om de fouten weer te geven met behulp van Extern bureaublad:
1.  Extern bureau blad-extensie voor Cloud service inschakelen (uitgebreide ondersteuning). Zie [extern bureaublad extensie Toep assen op Cloud Services (uitgebreide ondersteuning) met Azure Portal](enable-rdp.md) voor meer informatie.
2.  Op de Azure Portal, zodra het exemplaar de status gereed, extern in het exemplaar toont. Zie [verbinding maken met rolinstanties met extern bureaublad](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled) voor meer informatie over het gebruik van extern bureau blad met Cloud Services (uitgebreide ondersteuning).
3.  Meld u aan bij de virtuele machine met behulp van de referenties die zijn opgegeven tijdens de Extern bureaublad configuratie.
4.  Open een opdrachtvenster.
5.  Typ IPconfig.
6.  Noteer de waarde van het IPv4-adres.
7.  Open Internet Explorer.
8.  Typ het adres en de naam van de webtoepassing. Bijvoorbeeld, http:// <IPV4 Address> /default.aspx.
Als u naar de website navigeert, worden er nu meer expliciete fout berichten weer gegeven:
* Server fout in/-toepassing.
* Beschrijving: er is een onverwerkte uitzonde ring opgetreden tijdens het uitvoeren van de huidige webaanvraag. Raadpleeg de stack tracering voor meer informatie over de fout en de herkomst van de-code.
* Details van uitzonde ring: System. IO. FIleNotFoundException: kan bestand of Assembly ' micro soft. WindowsAzure. StorageClient, version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 ' of een van de bijbehorende afhankelijkheden niet laden. Het systeem kan het opgegeven bestand niet vinden.
Bijvoorbeeld:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Afbeelding toont uitzonde ring bij opstart fout van rol":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problemen vaststellen met behulp van de compute-emulator
U kunt de Azure Compute-emulator gebruiken om problemen met ontbrekende afhankelijkheden en web.config fouten op te sporen en op te lossen.
Voor de beste resultaten bij het gebruik van deze diagnose methode moet u een computer of virtuele machine gebruiken die een schone installatie van Windows heeft. 
1.  De [Azure SDK](https://azure.microsoft.com/downloads/) installeren 
2.  Bouw het Cloud service project op de ontwikkel machine.
3.  Navigeer in Windows Verkenner naar de map bin\debug van het Cloud service-project.
4.  Kopieer de CSX-map en het cscfg-bestand naar de computer die u gebruikt om de problemen op te lossen.
5.  Open op de schone computer een Azure SDK-opdracht prompt venster en typ csrun.exe/devstore: start.
6.  Typ op de opdracht prompt csrun uitvoeren <pad naar. CSX-map> <pad naar. cscfg-bestand>/launchBrowser.
7.  Wanneer de rol wordt gestart, worden gedetailleerde fout gegevens weer geven in Internet Explorer. U kunt ook de standaard hulpprogram ma's voor probleem oplossing van Windows gebruiken om het probleem verder te onderzoeken.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problemen vaststellen met behulp van IntelliTrace
Voor werk nemers en webrollen die gebruikmaken van .NET Framework 4, kunt u [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace)gebruiken, dat beschikbaar is in micro soft Visual Studio Enter prise.
Voer de volgende stappen uit om de service te implementeren met IntelliTrace ingeschakeld:
1.  Controleer of Azure SDK 1,3 of hoger is geïnstalleerd.
2.  Implementeer de oplossing met behulp van Visual Studio. Schakel tijdens de implementatie het selectie vakje IntelliTrace inschakelen voor .NET 4-rollen in.
3.  Zodra het exemplaar is gestart, opent u het Server Explorer.
4.  Vouw het knoop punt Azure\Cloud services uit en zoek de implementatie.
5.  Vouw de implementatie uit totdat u de rolinstanties ziet. Klik met de rechter muisknop op een van de exemplaren.
6.  Kies IntelliTrace-logboeken weer geven. De IntelliTrace-samen vatting wordt geopend.
7.  Zoek de sectie uitzonde ringen van de samen vatting. Als er uitzonde ringen zijn, wordt de sectie uitzonderings gegevens genoemd.
8.  Vouw de uitzonderings gegevens uit en zoek naar System. IO. FileNotFoundException-fouten die vergelijkbaar zijn met het volgende:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Afbeelding toont uitzonderings gegevens bij opstart fout van rol" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Ontbrekende Dll's en assembly's in adres
Ga als volgt te werk om ontbrekende DLL-en assembly fouten op te lossen:
1.  Open de oplossing in Visual Studio.
2.  Open de map references in Solution Explorer.
3.  Klik op de assembly die in de fout is geïdentificeerd.
4.  Zoek in het deel venster Eigenschappen naar lokale eigenschap kopiëren en stel de waarde in op waar.
5.  Implementeer de Cloud service opnieuw.
Wanneer u hebt gecontroleerd of alle fouten zijn gecorrigeerd, kunt u de service implementeren zonder het selectie vakje IntelliTrace inschakelen voor .NET 4-rollen in te scha kelen.

## <a name="next-steps"></a>Volgende stappen 
- Voor informatie over het oplossen van problemen met Cloud service rollen met behulp van Azure PaaS computer diagnostische gegevens raadpleegt u [de blog serie van Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
