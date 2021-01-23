---
title: Een Windows-VM maken en beheren in azure met behulp van python
description: Meer informatie over het gebruik van python voor het maken en beheren van een Windows-VM in Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: d99f9326b3113742fdc0c14a4ae39d552df47edd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730153"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Windows-Vm's maken en beheren in azure met behulp van python

Een [virtuele machine](overview.md) (VM) van Azure heeft verschillende ondersteunende Azure-resources nodig. In dit artikel wordt beschreven hoe u VM-resources maakt, beheert en verwijdert met behulp van python. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Visual Studio-project maken
> * Pakketten installeren
> * Referenties maken
> * Resources maken
> * Beheer taken uitvoeren
> * Resources verwijderen
> * De toepassing uitvoeren

Het duurt ongeveer 20 minuten om deze stappen uit te voeren.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

1. Als u dat nog niet hebt gedaan, installeert u [Visual Studio](/visualstudio/install/install-visual-studio). Selecteer **python-ontwikkeling** op de pagina workloads en klik vervolgens op **installeren**. In de samen vatting ziet u dat **Python 3 64-bits (3.6.0)** automatisch voor u wordt geselecteerd. Als u Visual Studio al hebt geïnstalleerd, kunt u de python-workload toevoegen met behulp van de Visual Studio Launcher.
2. Klik na het installeren en starten van Visual Studio op **bestand**  >  **Nieuw**  >  **project**.
3. Klik op **sjablonen**  >  **python**  >  **python-toepassing**, Voer *myPythonProject* in als de naam van het project, selecteer de locatie van het project en klik vervolgens op **OK**.

## <a name="install-packages"></a>Pakketten installeren

1. Klik in Solution Explorer onder *myPythonProject* met de rechter muisknop op **python-omgevingen** en selecteer vervolgens **virtuele omgeving toevoegen**.
2. Accepteer op het scherm virtuele omgeving toevoegen de standaard naam van de *env*, Controleer of *Python 3,6 (64-bits)* is geselecteerd voor de basis-interpreter en klik vervolgens op **maken**.
3. Klik met de rechter muisknop op de *env* -omgeving die u hebt gemaakt, klik op **python-pakket installeren**, Voer *Azure* in het zoekvak in en druk op ENTER.

U ziet in de uitvoer Vensters dat de Azure-pakketten zijn geïnstalleerd. 

## <a name="create-credentials"></a>Referenties maken

Voordat u met deze stap begint, moet u ervoor zorgen dat u een [Active Directory Service-Principal](../../active-directory/develop/howto-create-service-principal-portal.md)hebt. Noteer ook de toepassings-ID, de verificatie sleutel en de Tenant-ID die u in een latere stap nodig hebt.

1. Open het *myPythonProject.py* -bestand dat is gemaakt en voeg deze code toe om de uitvoering van uw toepassing mogelijk te maken:

    ```python
    if __name__ == "__main__":
    ```

2. Als u de code die nodig is wilt importeren, voegt u deze instructies toe aan het begin van het. py-bestand:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Voeg vervolgens in het. py-bestand variabelen toe na de instructies importeren om algemene waarden op te geven die worden gebruikt in de code:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Vervang de **abonnements-id** door de id van uw abonnement.

4. Als u de Active Directory referenties wilt maken die u nodig hebt om aanvragen uit te voeren, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Vervang de **toepassings-id**, **verificatie sleutel** en **Tenant-id** door de waarden die u eerder hebt verzameld tijdens het maken van uw Azure Active Directory Service-Principal.

5. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Resources maken
 
### <a name="initialize-management-clients"></a>Managementclients initialiseren

Er zijn management-clients nodig om resources te maken en te beheren met behulp van de python-SDK in Azure. Als u de Management-clients wilt maken, voegt u deze code toe onder de instructie **if** op het einde van het. py-bestand:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>De virtuele machine en ondersteunende resources maken

Alle resources moeten deel uitmaken van een [resource groep](../../azure-resource-manager/management/overview.md).

1. Als u een resource groep wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

Met [beschikbaarheids sets](tutorial-availability-sets.md) kunt u gemakkelijker de virtuele machines onderhouden die door uw toepassing worden gebruikt.

1. Als u een beschikbaarheidsset wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Een [openbaar IP-adres](../../virtual-network/public-ip-addresses.md) is nodig om te communiceren met de virtuele machine.

1. Als u een openbaar IP-adres voor de virtuele machine wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Een virtuele machine moet zich in een subnet van een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md)bevinden.

1. Als u een virtueel netwerk wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Als u een subnet wilt toevoegen aan het virtuele netwerk, voegt u deze functie toe na de variabelen in het. py-bestand:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Een virtuele machine heeft een netwerk interface nodig om te communiceren met het virtuele netwerk.

1. Als u een netwerk interface wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Nu u alle ondersteunende resources hebt gemaakt, kunt u een virtuele machine maken.

1. Als u de virtuele machine wilt maken, voegt u deze functie toe na de variabelen in het. py-bestand:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > In deze zelf studie maakt u een virtuele machine waarop een versie van het Windows Server-besturings systeem wordt uitgevoerd. Zie voor meer informatie over het selecteren van andere installatie kopieën [navigeren en installatie kopieën van virtuele Azure-machines selecteren met Windows Power shell en de Azure cli](../linux/cli-ps-findimage.md).
    > 
    > 

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Beheer taken uitvoeren

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. U kunt ook code maken om herhaalde of complexe taken te automatiseren.

### <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

1. Als u informatie over de virtuele machine wilt ophalen, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>De virtuele machine stoppen

U kunt een virtuele machine stoppen en alle instellingen behouden, maar er nog steeds kosten in rekening worden gebracht, of u kunt een virtuele machine stoppen en de toewijzing ervan ongedaan maken. Wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt, worden alle resources die eraan zijn gekoppeld, ook ongedaan gemaakt en wordt de facturering voor het apparaat beëindigd.

1. Als u de virtuele machine wilt stoppen zonder de toewijzing ervan te detoewijzen, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Als u de toewijzing van de virtuele machine ongedaan wilt maken, wijzigt u de power_off aanroep van deze code:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>De virtuele machine starten

1. Als u de virtuele machine wilt starten, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Grootte van de virtuele machine wijzigen

Veel aspecten van de implementatie moeten worden overwogen bij het bepalen van de grootte van de virtuele machine. Zie [VM-grootten](../sizes.md)voor meer informatie.

1. Als u de grootte van de virtuele machine wilt wijzigen, voegt u deze functie toe na de variabelen in het. py-bestand:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Een gegevensschijf toevoegen aan de virtuele machine

Virtuele machines kunnen een of meer [gegevens schijven](../managed-disks-overview.md) hebben die zijn opgeslagen als vhd's.

1. Als u een gegevens schijf aan de virtuele machine wilt toevoegen, voegt u deze functie toe na de variabelen in het. py-bestand: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Resources verwijderen

Omdat er in rekening worden gebracht voor resources die worden gebruikt in azure, is het altijd een goed idee om resources te verwijderen die niet meer nodig zijn. Als u de virtuele machines en alle ondersteunende bronnen wilt verwijderen, moet u de resource groep verwijderen.

1. Als u de resource groep en alle resources wilt verwijderen, voegt u deze functie toe na de variabelen in het. py-bestand:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Als u de functie die u eerder hebt toegevoegd wilt aanroepen, voegt u deze code toe onder de **if** -instructie aan het einde van het. py-bestand:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Sla *myPythonProject.py* op.

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Klik op **starten** in Visual Studio om de console toepassing uit te voeren.

2. Druk op **Enter** nadat de status van elke resource is geretourneerd. In de status informatie ziet u een **geslaagde** inrichtings status. Nadat de virtuele machine is gemaakt, hebt u de mogelijkheid om alle resources die u maakt, te verwijderen. Voordat u op **Enter** drukt om resources te verwijderen, kunt u een paar minuten duren om te controleren of de Azure Portal is gemaakt. Als u de Azure Portal hebt geopend, moet u de Blade wellicht vernieuwen om nieuwe resources weer te geven.  

    Het duurt ongeveer vijf minuten voordat deze console toepassing volledig van begin tot eind kan worden uitgevoerd. Het kan enkele minuten duren voordat de toepassing is voltooid voordat alle resources en de resource groep zijn verwijderd.


## <a name="next-steps"></a>Volgende stappen

- Als er problemen met de implementatie zijn, raadpleegt u als volgende stap [Problemen met resourcegroepimplementaties in Azure Portal oplossen](../../azure-resource-manager/templates/deployment-history.md).
- Meer informatie over de [Azure python-bibliotheek](/python/api/overview/azure/)
