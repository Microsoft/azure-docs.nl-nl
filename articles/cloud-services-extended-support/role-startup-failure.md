---
title: Opstartfout van rol exemplaar in Azure Cloud Services (uitgebreide ondersteuning)
description: Problemen met het opstarten van rol-exemplaren in Azure Cloud Services oplossen (uitgebreide ondersteuning).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748929"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Problemen met Azure Cloud Services (uitgebreide ondersteuning) oplossen die niet kunnen worden starten

Hier zijn enkele veelvoorkomende problemen en oplossingen met betrekking tot Azure Cloud Services (uitgebreide ondersteuning) rollen die niet starten.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Cloudservicebewerking mislukt met RoleInstanceStartupTimeoutError

Een of meer van uw rol-exemplaren in Azure Cloud Services (uitgebreide ondersteuning) starten mogelijk traag of ze worden mogelijk gerecycled en het rolproces mislukt. De fout van de `RoleInstanceStartupTimeoutError` roltoepassing wordt weergegeven.

De roltoepassing bevat twee onderdelen die kunnen leiden tot het recyclen van rollen: *opstarttaken* en *rolcode (implementatie van RoleEntryPoint).* 

Als de rol stopt, wordt platform as a service de rol opnieuw gestart door de PaaS-agent.

U kunt de huidige status en details voor een rol exemplaar voor het diagnosticeren van fouten met behulp van PowerShell of de volgende Azure Portal:

* **PowerShell:** gebruik de cmdlet [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) om informatie op te halen over de runtime-status van het rolin exemplaar:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal:** ga in de portal naar het cloudservice-exemplaar. Als u de statusdetails wilt weergeven, **selecteert u** Rollen en exemplaren en selecteert u vervolgens het rol-exemplaar.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Schermopname van een fout bij het opstarten van een rol in de Azure Portal.":::

## <a name="missing-dlls-or-dependencies"></a>Ontbrekende DLL's of afhankelijkheden

Niet-reagerende rollen en rollen die tussen de staten worden gecyclusd, worden mogelijk veroorzaakt door ontbrekende DLL's of assemblies.

Hier zijn enkele symptomen van ontbrekende DLL's of assemblies:

* Uw rol-exemplaar doorloop de staten **Initialiseren,** **Bezet** en **Stoppen.**
* Uw rol-exemplaar is verplaatst **naar** de status Gereed, maar als u naar uw webtoepassing gaat, is de pagina niet zichtbaar.


Op een website die is geïmplementeerd in een webrol en die een DLL ontbreekt, kan deze runtimefout van de server worden weergegeven:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Schermopname van een runtimefout na een fout bij het opstarten van een rol.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Ontbrekende DLL's en assemblies oplossen

Fouten van ontbrekende DLL's en assemblies oplossen:

1. Open Visual Studio de oplossing.
2. Open Solution Explorer map *Verwijzingen.*
3. Selecteer de assembly die in de fout is geïdentificeerd.
4. Stel **in Eigenschappen** de eigenschap Copy **Local** in op **True.**
5. De cloudservice opnieuw te gebruiken.

Nadat u hebt gecontroleerd of er geen fouten meer worden weergegeven, moet u de service opnieuw gebruiken. Schakel bij het instellen van de implementatie het selectievakje **IntelliTrace inschakelen voor .NET 4-rollen** niet in.

## <a name="diagnose-role-instance-errors"></a>Fouten met rol-exemplaren diagnosticeren

Kies uit de volgende opties om problemen met rol instances vast te stellen.

### <a name="turn-off-custom-errors"></a>Aangepaste fouten uitschakelen

Als u volledige foutgegevens wilt weergeven, stelt u in *het* web.configvoor de webrol de aangepaste foutmodus in op en de `off` service opnieuw teployeren:

1. Open Visual Studio de oplossing.
2. Open Solution Explorer het *web.config* bestand.
3. Voeg in `system.web` de sectie de volgende code toe:

   ```xml
   <customErrors mode="Off" />
   ```

4. Sla het bestand op.
5. Verpak de service opnieuw enployer deze opnieuw.

Wanneer de service opnieuw wordt geïmplementeerd, bevat een foutbericht de naam van ontbrekende assemblies of DLL's.

### <a name="use-remote-desktop"></a>Extern bureaublad gebruiken

Gebruik Extern bureaublad om toegang te krijgen tot de rol en volledige foutinformatie weer te geven:

1. [Voeg de Extern bureaublad-extensie voor Azure Cloud Services (uitgebreide ondersteuning) toe.](enable-rdp.md)
2. Wanneer in Azure Portal cloudservice-exemplaar de status  Gereed wordt weergeven, gebruikt u Extern bureaublad om u aan te melden bij de cloudservice. Zie Verbinding maken met rol-exemplaren met behulp van Extern bureaublad [voor meer Extern bureaublad.](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)
3. Meld u aan bij de virtuele machine met behulp van de referenties die u hebt gebruikt voor het instellen van Extern bureaublad.
4. Open een opdrachtpromptvenster.
5. Voer bij de opdrachtprompt **ipconfig in.** Noteer de geretourneerde waarde voor het IPv4-adres.
6. Open Microsoft Internet Explorer.
7. Voer in de adresbalk het IPv4-adres in, gevolgd door een slash en de naam van het standaardbestand van de webtoepassing. Bijvoorbeeld `http://<IPv4 address>/default.aspx`.

Als u nu naar de website gaat, ziet u foutberichten die meer informatie bevatten. Hier volgt een voorbeeld:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Schermopname van een voorbeeld van een foutbericht.":::
  
### <a name="use-the-compute-emulator"></a>De rekenemulator gebruiken

U kunt de Azure-rekenemulator gebruiken om problemen met ontbrekende afhankelijkheden en problemen metweb.config *oplossen.* Wanneer u deze methode gebruikt om problemen vast te stellen, gebruikt u voor de beste resultaten een computer of virtuele machine met een schone installatie van Windows.

Problemen vaststellen met behulp van de Azure-rekenemulator:

1. Installeer de [Azure SDK.](https://azure.microsoft.com/downloads/)
2. Bouw het cloudserviceproject op de ontwikkelcomputer.
3. Ga in Verkenner in het cloudserviceproject naar de *map bin\debug.*
4. Kopieer de *map .csx* en het *.cscfg-bestand* naar de computer die u gebruikt om problemen op te lossen.
5. Open op de schone machine een Azure SDK-opdrachtpromptvenster.
6. Voer bij de opdrachtprompt in **csrun.exe /devstore:start.**
7. Voer vervolgens **csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser uit.**
8. Wanneer de rol wordt gestart, Internet Explorer gedetailleerde foutinformatie weergegeven.

Als u meer diagnoses nodig hebt, kunt u standaard windows-hulpprogramma's voor probleemoplossing gebruiken.

### <a name="use-intellitrace"></a>IntelliTrace gebruiken

Voor werkrollen en webrollen die gebruikmaken van .NET Framework 4, kunt u [IntelliTrace gebruiken.](/visualstudio/debugger/intellitrace) IntelliTrace is beschikbaar in Visual Studio Enterprise.

Uw cloudservice implementeren met IntelliTrace ingeschakeld:

1. Controleer of Azure SDK 1.3 of hoger is geïnstalleerd.
2. In Visual Studio implementeert u de oplossing. Wanneer u de implementatie in stelt, selecteert u het selectievakje **IntelliTrace inschakelen voor .NET 4-rollen.**
3. Nadat het rol-exemplaar is gestart, opent u Server Explorer.
4. Vouw het **knooppunt Azure\Cloud Services** uit.
5. Vouw de implementatie uit om rol instances weer te geven. Klik met de rechtermuisknop op een rolin exemplaar.
6. Selecteer **IntelliTrace-logboeken weergeven.**
7. Ga **in Samenvatting van IntelliTrace** naar  **Uitzonderingsgegevens**.
8. Vouw **Uitzonderingsgegevens** uit en zoek naar een `System.IO.FileNotFoundException` fout:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Schermopname van uitzonderingsgegevens voor een fout bij het opstarten van een rol." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met cloudservicerol met behulp van diagnostische gegevens van Azure PaaS-computers.](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)
