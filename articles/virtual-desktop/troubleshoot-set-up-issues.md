---
title: Het maken van een hostgroep in Windows Virtual Desktop-omgeving-Azure
description: Problemen met Tenant-en hostgroepen oplossen tijdens de installatie van een virtuele Windows-desktop omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c31a32b32a685087c53198ec52af1188d0071cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652442"
---
# <a name="host-pool-creation"></a>Hostgroepen maken

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

In dit artikel komen problemen aan bod tijdens de eerste installatie van de virtuele bureau blad-Tenant van Windows en de gerelateerde infra structuur van de sessie-hostgroep.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>De afbeelding voor meerdere sessies van Windows 10 Enter prise ophalen

Als u de Windows 10 Enter prise-installatie kopie voor meerdere sessies wilt gebruiken, gaat u naar Azure Marketplace en selecteert u aan de **slag** met  >  **micro soft Windows 10** > en [Windows 10 Enter prise multi-session versie 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemen met het gebruik van de Azure Portal om hostgroepen te maken

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Fout: ' een gratis account maken ' wordt weer gegeven bij het openen van de service

> [!div class="mx-imgBorder"]
> ![Een afbeelding met de Azure Portal het bericht ' een gratis account maken ' weer gegeven](media/create-new-account.png)

**Oorzaak**: er zijn geen actieve abonnementen in het account waarmee u zich hebt aangemeld bij Azure, of het account heeft geen machtigingen om de abonnementen weer te geven.

**Fix**: Meld u aan bij het abonnement waarin u de virtual machines van de host (vm's) wilt implementeren met een account met ten minste toegang op Inzender niveau.

### <a name="error-exceeding-quota-limit"></a>Fout: "overschrijding quotum limiet"

Als uw bewerking de quotum limiet overschrijdt, kunt u een van de volgende dingen doen:

- Maak een nieuwe hostgroep met dezelfde para meters, maar met minder Vm's en VM-kernen.

- Open de koppeling die u ziet in het veld statusMessage in een browser om een aanvraag in te dienen voor het verhogen van het quotum voor uw Azure-abonnement voor de opgegeven VM-SKU.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Fout: kan geen gebruikers toewijzingen in app-groepen zien.

**Oorzaak**: deze fout treedt meestal op nadat u het abonnement van 1 Azure Active Directory-TENANT (AD) naar een andere hebt verplaatst. Als uw oude toewijzingen nog steeds zijn gekoppeld aan de oude Azure AD-Tenant, worden deze niet meer in de Azure Portal bijgehouden.

**Oplossen**: u moet gebruikers opnieuw toewijzen aan app-groepen.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>Ik zie alleen ons bij het instellen van de locatie voor mijn service objecten

**Oorzaak**: Azure biedt momenteel geen ondersteuning voor deze regio voor de virtueel-bureaublad service van Windows. Bekijk de [gegevens locaties](data-locations.md)voor meer informatie over welke geografische gebieden worden ondersteund. Als Windows virtueel bureau blad de locatie ondersteunt, maar deze nog niet wordt weer gegeven wanneer u een locatie wilt selecteren, betekent dit dat uw resource provider nog niet is bijgewerkt.

**Oplossen**: als u de meest recente lijst met regio's wilt ophalen, moet u de resource provider opnieuw registreren:

1. Ga naar **abonnementen** en selecteer het relevante abonnement.
2. **Resource provider** selecteren.
3. Selecteer **micro soft. DesktopVirtualization** en selecteer **opnieuw registreren** in het menu Actie.

Wanneer u de resource provider opnieuw registreert, ziet u geen specifieke feedback over de gebruikers interface of update status. Het proces voor opnieuw registreren heeft ook geen invloed op uw bestaande omgevingen.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager sjabloon fouten

Volg deze instructies voor het oplossen van problemen met mislukte implementaties van Azure Resource Manager sjablonen en Power shell DSC.

1. Bekijk fouten in de implementatie met behulp [van de weer gave-implementatie bewerkingen met Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md).
2. Als er geen fouten in de implementatie zijn, controleert u de fouten in het activiteiten logboek met behulp van [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md).
3. Wanneer de fout is geïdentificeerd, gebruikt u het fout bericht en de resources in [problemen met veelvoorkomende Azure-implementatie fouten oplossen met Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) om het probleem op te lossen.
4. Verwijder alle resources die tijdens de vorige implementatie zijn gemaakt en probeer de sjabloon opnieuw te implementeren.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fout: de implementatie is mislukt.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![De scherm afbeelding van de implementatie is mislukt.](media/failure-joindomain.png)

Voor beeld van onbewerkte fout:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Oorzaak 1:** De referenties die zijn ingevoerd voor het samen voegen van Vm's aan het domein, zijn onjuist.

**Oplossing 1:** Zie de fout ' onjuiste referenties ' voor virtuele machines die geen deel uitmaken van het domein in de [host-VM-configuratie](troubleshoot-vm-configuration.md).

**Oorzaak 2:** De domein naam wordt niet omgezet.

**Oplossing 2:** Zie [fout: domein naam wordt niet omgezet](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) in de [host-VM-configuratie](troubleshoot-vm-configuration.md).

**Oorzaak 3:** De DNS-configuratie van uw virtuele netwerk (VNET) is ingesteld op **standaard**.

Ga als volgt te werk om dit probleem op te lossen:

1. Open de Azure Portal en ga naar het tabblad **virtuele netwerken** .
2. Zoek uw VNET en selecteer vervolgens **DNS-servers**.
3. Het menu DNS-servers moet aan de rechter kant van het scherm worden weer gegeven. Selecteer **aangepast** in dat menu.
4. Zorg ervoor dat de DNS-servers die worden vermeld onder aangepast overeenkomen met uw domein controller of Active Directory domein. Als uw DNS-server niet wordt weer geven, kunt u deze toevoegen door de waarde ervan in te voeren in het veld **DNS-server toevoegen** .

### <a name="error-your-deployment-failedunauthorized"></a>Fout: Uw implementatie is mislukt...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Oorzaak:** Het abonnement dat u gebruikt, is een type dat geen toegang heeft tot de vereiste onderdelen in de regio waarin de klant probeert te implementeren. Bijvoorbeeld: MSDN-, Free-of Education-abonnementen kunnen deze fout weer geven.

**Oplossen:** Wijzig het abonnements type of de regio in een die toegang heeft tot de vereiste onderdelen.

### <a name="error-vmextensionprovisioningerror"></a>Fout: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Scherm opname van de implementatie is mislukt met de status van de Terminal-inrichting is mislukt.](media/failure-vmextensionprovisioning.png)

**Oorzaak 1:** Tijdelijke fout met de virtueel-bureaublad omgeving van Windows.

**Oorzaak 2:** Tijdelijke fout met de verbinding.

**Oplossen:** Bevestig dat Windows Virtual Desktop Environment in orde is door u aan te melden met Power shell. Voltooi de VM-registratie hand matig in [een hostgroep maken met Power shell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fout: De opgegeven gebruikersnaam voor een beheerder is niet toegestaan

> [!div class="mx-imgBorder"]
> ![Scherm opname van de implementatie is mislukt, omdat een opgegeven beheerder niet is toegestaan.](media/failure-username.png)

Voor beeld van onbewerkte fout:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Oorzaak:** Het opgegeven wacht woord bevat verboden subtekenreeksen (admin, Administrator, root).

**Oplossen:** Werk de gebruikers naam bij of gebruik andere gebruikers.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fout: De VM heeft een fout gerapporteerd bij het verwerken van extensie

> [!div class="mx-imgBorder"]
> ![Scherm opname van de bron bewerking die is voltooid met een Terminal-inrichtings status in uw implementatie is mislukt.](media/failure-processing.png)

Voor beeld van onbewerkte fout:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Oorzaak:** De Power shell DSC-uitbrei ding kan geen beheerders toegang krijgen op de virtuele machine.

**Oplossen:** Bevestig dat de gebruikers naam en het wacht woord beheerders toegang hebben op de virtuele machine en voer de Azure Resource Manager sjabloon opnieuw uit.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fout: heeft-Power shell DSC-configuratie FirstSessionHost is voltooid met fout (en)

> [!div class="mx-imgBorder"]
> ![Scherm opname van implementatie mislukt met Power shell DSC-configuratie FirstSessionHost voltooid met fout (en).](media/failure-dsc.png)

Voor beeld van onbewerkte fout:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Oorzaak:** De Power shell DSC-uitbrei ding kan geen beheerders toegang krijgen op de virtuele machine.

**Oplossen:** Bevestig dat de opgegeven gebruikers naam en het wacht woord beheerders toegang hebben op de virtuele machine en voer de Azure Resource Manager sjabloon opnieuw uit.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: heeft – InvalidResourceReference

Voor beeld van onbewerkte fout:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Oorzaak:** Een deel van de naam van de resource groep wordt gebruikt voor bepaalde resources die worden gemaakt door de sjabloon. Omdat de naam overeenkomt met bestaande resources, kan de sjabloon een bestaande resource uit een andere groep selecteren.

**Oplossen:** Wanneer u de Azure Resource Manager sjabloon uitvoert om Vm's voor de host te implementeren, moet u de eerste twee tekens uniek maken voor de naam van de resource groep voor uw abonnement.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fout: heeft – InvalidResourceReference

Voor beeld van onbewerkte fout:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Oorzaak:** Deze fout is omdat de NIC die is gemaakt met de Azure Resource Manager sjabloon dezelfde naam heeft als een andere NIC die al in het VNET is opgenomen.

**Oplossen:** Gebruik een ander host-voor voegsel.

### <a name="error-deploymentfailed--error-downloading"></a>Fout: heeft: fout bij het downloaden

Voor beeld van onbewerkte fout:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Oorzaak:** Deze fout wordt veroorzaakt door een statische route, een firewall regel of een NSG die het downloaden van het zip-bestand dat is gekoppeld aan de Azure Resource Manager-sjabloon blokkeert.

**Oplossen:** Verwijder de blokkerende statische route, firewall regel of NSG. U kunt eventueel het JSON-bestand van de Azure Resource Manager sjabloon openen in een tekst editor, de koppeling naar een zip-bestand maken en de resource downloaden naar een toegestane locatie.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Fout: kan geen sessiehost verwijderen uit de hostgroep nadat de virtuele machine is verwijderd

**Oorzaak:** U moet de sessiehost verwijderen voordat u de virtuele machine verwijdert.

**Oplossen:** Plaats de sessiehost in de verwerkings modus, Meld alle gebruikers af bij de sessiehost en verwijder vervolgens de host.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie problemen [met veelvoorkomende problemen met Windows Virtual Desktop agent oplossen](troubleshoot-agent.md)voor informatie over het oplossen van problemen met de Windows Virtual Desktop agent of sessie verbinding.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.
