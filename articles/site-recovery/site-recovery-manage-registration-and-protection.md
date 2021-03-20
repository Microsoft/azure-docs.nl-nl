---
title: Servers verwijderen en beveiliging uitschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u servers van een Site Recovery kluis kunt verwijderen en de beveiliging van virtuele machines en fysieke servers kunt uitschakelen.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: 1356deabd13db1dd2f29ac1b1f088db2120353fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738851"
---
# <a name="remove-servers-and-disable-protection"></a>Servers verwijderen en beveiliging uitschakelen

In dit artikel wordt beschreven hoe u servers van een Recovery Services kluis kunt verwijderen en hoe u de beveiliging kunt uitschakelen voor computers die worden beveiligd door Site Recovery.


## <a name="unregister-a--configuration-server"></a>Registratie van een configuratie server ongedaan maken

Als u virtuele VMware-machines of fysieke Windows/Linux-servers naar Azure repliceert, kunt u als volgt de registratie van een niet-verbonden configuratie server bij een kluis opheffen:

1. [Schakel de beveiliging van virtuele machines uit](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. De koppeling van het replicatie beleid wordt [verwijderd](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. [De configuratie server verwijderen](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Registratie van een VMM-server ongedaan maken

1. Stop met het repliceren van virtuele machines in Clouds op de VMM-server die u wilt verwijderen.
2. Verwijder netwerk toewijzingen die worden gebruikt door Clouds op de VMM-server die u wilt verwijderen. Klik in **site Recovery infra structuur**  >  **voor System Center VMM**  >  -**netwerk toewijzing** met de rechter muisknop op de netwerk toewijzing > **verwijderen**.
3. Noteer de ID van de VMM-server.
4. Koppel het replicatie beleid van Clouds op de VMM-server die u wilt verwijderen.  Dubbel klik in **site Recovery-infra structuur**  >  **voor System Center VMM**  >   -**replicatie beleid** op het bijbehorende beleid. Klik met de rechter muisknop op de Cloud > de **koppeling te verbreken**.
5. De VMM-server of het actieve knoop punt verwijderen. Klik in **site Recovery infra structuur**  >  **voor System Center VMM**  >  **VMM-servers** met de rechter muisknop op de server > **verwijderen**.
6. Als uw VMM-server de status verbroken heeft, downloadt en voert u het [opschoon script](/samples/browse/?redirectedfrom=TechNet-Gallery) uit op de VMM-server. Open Power shell met de optie **als administrator uitvoeren** om het uitvoerings beleid voor het standaard bereik (LocalMachine) te wijzigen. Geef in het script de ID op van de VMM-server die u wilt verwijderen. Met het script worden registratie-en Cloud koppelings gegevens van de server verwijderd.
5. Voer het script voor opschonen op een secundaire VMM-server uit.
6. Voer het opschoon script uit op alle andere passieve VMM-cluster knooppunten waarop de provider is geïnstalleerd.
7. Verwijder de provider hand matig op de VMM-server. Als u een cluster hebt, verwijdert u van alle knoop punten.
8. Als uw virtuele machines zijn gerepliceerd naar Azure, moet u de micro soft Recovery Services-agent verwijderen van Hyper-V-hosts in de verwijderde Clouds.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>De registratie van een Hyper-V-host op een Hyper-V-site ongedaan maken

Hyper-V-hosts die niet door VMM worden beheerd, worden in een Hyper-V-site verzameld. Verwijder een host op een Hyper-V-site als volgt:

1. Schakel replicatie uit voor Hyper-V-Vm's die zich op de host bevinden.
2. Koppel het beleid voor de Hyper-V-site. Dubbel klik in **site Recovery-infra structuur**  >  voor het replicatie beleid **van Hyper-V-sites** op  >   het bijbehorende beleid. Klik met de rechter muisknop op de site > de **koppeling te verbreken**.
3. Hyper-V-hosts verwijderen. Klik in **site Recovery-infra structuur**  >  **voor hyper-v-sites**  >  **hyper-v-hosts** met de rechter muisknop op de server > **verwijderen**.
4. Verwijder de Hyper-V-site nadat alle hosts hiervan zijn verwijderd. Klik in **site Recovery-infra structuur**  >  **voor hyper-v-** sites  >  **hyper-v-sites** met de rechter muisknop op de site > **verwijderen**.
5. Als de Hyper-V-host de status **verbroken** heeft, voert u het volgende script uit op elke hyper-v-host die u hebt verwijderd. Met het script worden instellingen op de server opgeschoond en wordt de registratie van de kluis ongedaan gemaakt.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Beveiliging uitschakelen voor een virtuele VMware-machine of fysieke server (VMware naar Azure)

1. Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
2. Selecteer een van de volgende opties op de pagina **replicatie uitschakelen** :
    - **Replicatie uitschakelen en verwijderen (aanbevolen)** : met deze optie wordt het gerepliceerde item verwijderd uit Azure site Recovery en wordt de replicatie voor de machine gestopt. De replicatie Configuratie op de configuratie server wordt opgeruimd en Site Recovery facturering voor deze beveiligde server is gestopt. Houd er rekening mee dat deze optie alleen kan worden gebruikt wanneer de configuratie server zich in de status verbonden bevindt.
    - **Verwijderen** : deze optie moet alleen worden gebruikt als de bron omgeving is verwijderd of niet is geopend (niet verbonden). Hiermee wordt het gerepliceerde item verwijderd uit de Azure Site Recovery (facturering wordt gestopt). De replicatie Configuratie op de configuratie server **wordt niet** opgeschoond. 

> [!NOTE]
> In beide opties wordt de Mobility-service niet verwijderd van de beveiligde servers, moet u deze hand matig verwijderen. Als u van plan bent om de server opnieuw te beveiligen met behulp van dezelfde configuratie server, kunt u het verwijderen van de Mobility-service overs Laan.

> [!NOTE]
> Als u al een failover hebt uitgevoerd voor een virtuele machine en deze actief is in azure, moet u er rekening mee houden dat het uitschakelen van de beveiliging geen invloed heeft op de failover van de virtuele machine.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Beveiliging voor een Azure-VM uitschakelen (Azure naar Azure)

-  Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
> [!NOTE]
> de Mobility-service wordt niet van de beveiligde servers verwijderd. u moet deze hand matig verwijderen. Als u van plan bent om de server opnieuw te beveiligen, kunt u het verwijderen van de mobiliteitsservice overslaan.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Beveiliging uitschakelen voor een Hyper-V-virtuele machine (Hyper-V naar Azure)

> [!NOTE]
> Gebruik deze procedure als u virtuele Hyper-V-machines naar Azure repliceert zonder een VMM-server. Als u uw virtuele machines repliceert met het scenario van **System Center VMM naar Azure** , volgt u de instructies voor het uitschakelen van de beveiliging van een Hyper-V virtuele machine die wordt gerepliceerd met behulp van het System Center VMM naar Azure-scenario

1. Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
2. In **replicatie uitschakelen** kunt u de volgende opties selecteren:
   - **Replicatie uitschakelen en verwijderen (aanbevolen)** : met deze optie wordt het gerepliceerde item verwijderd uit Azure site Recovery en wordt de replicatie voor de machine gestopt. De replicatie Configuratie op de on-premises virtuele machine wordt opgeruimd en Site Recovery facturering voor deze beveiligde server wordt gestopt.
   - **Verwijderen** : deze optie moet alleen worden gebruikt als de bron omgeving is verwijderd of niet is geopend (niet verbonden). Hiermee wordt het gerepliceerde item verwijderd uit de Azure Site Recovery (facturering wordt gestopt). De replicatie Configuratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

    > [!NOTE]
    > Als u de optie **verwijderen** hebt gekozen, voert u de volgende reeks scripts uit om de replicatie-instellingen op de lokale Hyper-V-Server op te schonen.

    > [!NOTE]
    > Als u al een failover hebt uitgevoerd voor een virtuele machine en deze actief is in azure, moet u er rekening mee houden dat het uitschakelen van de beveiliging geen invloed heeft op de failover van de virtuele machine.

1. Op de bron-Hyper-V-hostserver om de replicatie voor de virtuele machine te verwijderen. Vervang SQLVM1 door de naam van uw virtuele machine en voer het script uit vanuit een beheer-Power shell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Beveiliging uitschakelen voor een Hyper-V virtuele machine die naar Azure repliceert met het System Center VMM naar Azure-scenario

1. Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
2. In **replicatie uitschakelen** selecteert u een van de volgende opties:

   - **Replicatie uitschakelen en verwijderen (aanbevolen)** : met deze optie wordt het gerepliceerde item verwijderd uit Azure site Recovery en wordt de replicatie voor de machine gestopt. De replicatie Configuratie op de on-premises virtuele machine wordt opgeruimd en Site Recovery facturering voor deze beveiligde server is gestopt.
   - **Verwijderen** : deze optie moet alleen worden gebruikt als de bron omgeving is verwijderd of niet is geopend (niet verbonden). Hiermee wordt het gerepliceerde item verwijderd uit de Azure Site Recovery (facturering wordt gestopt). De replicatie Configuratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

     > [!NOTE]
     > Als u de optie **verwijderen** hebt gekozen, voert u de volgende scripts uit om de on-premises VMM-server van de replicatie-instellingen op te schonen.
3. Voer dit script uit op de Bron-VMM-server met behulp van Power shell (beheerders bevoegdheden vereist) van de VMM-console. Vervang de tijdelijke aanduiding **SQLVM1** door de naam van uw virtuele machine.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Met de bovenstaande stappen worden de replicatie-instellingen op de VMM-Server gewist. Als u de replicatie voor de virtuele machine die wordt uitgevoerd op de Hyper-V-hostserver wilt stoppen, voert u dit script uit. Vervang SQLVM1 door de naam van uw virtuele machine en host01.contoso.com met de naam van de Hyper-V-hostserver.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Beveiliging uitschakelen voor een Hyper-V virtuele machine die wordt gerepliceerd naar een secundaire VMM-server met behulp van het System Center VMM naar VMM-scenario

1. Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
2. In **replicatie uitschakelen** selecteert u een van de volgende opties:

   - **Replicatie uitschakelen en verwijderen (aanbevolen)** : met deze optie wordt het gerepliceerde item verwijderd uit Azure site Recovery en wordt de replicatie voor de machine gestopt. De replicatie Configuratie op de on-premises virtuele machine wordt opgeruimd en Site Recovery facturering voor deze beveiligde server is gestopt.
   - **Verwijderen** : deze optie moet alleen worden gebruikt als de bron omgeving is verwijderd of niet is geopend (niet verbonden). Hiermee wordt het gerepliceerde item verwijderd uit de Azure Site Recovery (facturering wordt gestopt). De replicatie Configuratie op de on-premises virtuele machine **wordt niet** opgeschoond. Voer de volgende reeks scripts uit om de on-premises virtuele machines van de replicatie-instellingen op te schonen.
     > [!NOTE]
     > Als u de optie **verwijderen** hebt gekozen, voert u de volgende scripts uit om de on-premises VMM-server van de replicatie-instellingen op te schonen.

3. Voer dit script uit op de Bron-VMM-server met behulp van Power shell (beheerders bevoegdheden vereist) van de VMM-console. Vervang de tijdelijke aanduiding **SQLVM1** door de naam van uw virtuele machine.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Voer dit script uit op de secundaire VMM-server om de instellingen voor de secundaire virtuele machine op te schonen:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. Vernieuw de virtuele machines op de Hyper-V-hostserver op de secundaire VMM-server, zodat de secundaire virtuele machine weer wordt gedetecteerd in de VMM-console.
6. In de bovenstaande stappen worden de replicatie-instellingen op de VMM-Server gewist. Als u de replicatie voor de virtuele machine wilt stoppen, voert u het volgende script uit om de primaire en secundaire Vm's uit te voeren. Vervang SQLVM1 door de naam van uw virtuele machine.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```