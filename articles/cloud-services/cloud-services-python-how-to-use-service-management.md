---
title: De functie handleiding voor Service Management-API (python) gebruiken
description: Informatie over het programmatisch uitvoeren van algemene Service beheer taken vanuit Python.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741397"
---
# <a name="use-service-management-from-python"></a>Service beheer van python gebruiken

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In deze hand leiding wordt beschreven hoe u via programma code veelvoorkomende Service beheer taken vanuit python kunt uitvoeren. De **maken** -klasse in de [Azure SDK voor python](https://github.com/Azure/azure-sdk-for-python) ondersteunt programmatische toegang tot veel van de functies voor Service beheer die beschikbaar zijn in de [Azure Portal][management-portal]. U kunt deze functie gebruiken om Cloud Services, implementaties, gegevens beheer Services en virtuele machines te maken, bij te werken en te verwijderen. Deze functionaliteit kan nuttig zijn bij het bouwen van toepassingen die programmatische toegang tot Service beheer nodig hebben.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Wat is Service beheer?
De Azure Service Management-API biedt programmatische toegang tot veel van de Service beheer functionaliteit die beschikbaar is via de [Azure Portal][management-portal]. U kunt de Azure SDK voor python gebruiken om uw Cloud Services en opslag accounts te beheren.

Als u de Service Management-API wilt gebruiken, moet u [een Azure-account maken](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Concepten
De Azure SDK voor python verloopt de [Service Management-API][svc-mgmt-rest-api]. Dit is een rest API. Alle API-bewerkingen worden uitgevoerd via TLS en wederzijds geverifieerd met behulp van X. 509 v3-certificaten. De beheer service kan worden geopend vanuit een service die in azure wordt uitgevoerd. Het kan ook rechtstreeks via internet worden geopend vanuit elke toepassing die een HTTPS-aanvraag kan verzenden en een HTTPS-antwoord kan ontvangen.

## <a name="installation"></a><a name="Installation"> </a>Installatie
Alle functies die in dit artikel worden beschreven, zijn beschikbaar in het `azure-servicemanagement-legacy` pakket, dat u kunt installeren met behulp van PIP. Zie [python en de Azure SDK installeren](/azure/developer/python/azure-sdk-install)voor meer informatie over de installatie (bijvoorbeeld als u geen ervaring hebt met python).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Verbinding maken met Service Management
U hebt uw Azure-abonnements-ID en een geldig beheer certificaat nodig om verbinding te maken met het Service Management-eind punt. U kunt uw abonnements-ID verkrijgen via de [Azure Portal][management-portal].

> [!NOTE]
> U kunt nu certificaten gebruiken die zijn gemaakt met OpenSSL wanneer u op Windows uitvoert. Python 2.7.4 of hoger is vereist. We raden u aan OpenSSL te gebruiken in plaats van. pfx, omdat ondersteuning voor pfx-certificaten waarschijnlijk in de toekomst wordt verwijderd.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Beheer certificaten op Windows/Mac/Linux (OpenSSL)
U kunt [openssl](https://www.openssl.org/) gebruiken om uw beheer certificaat te maken. U moet twee certificaten maken, één voor de server (een `.cer` bestand) en één voor de client (een `.pem` bestand). Voer het volgende uit om het bestand te maken `.pem` :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Voer het volgende uit om het certificaat te maken `.cer` :

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Zie [overzicht van certificaten voor azure Cloud Services](cloud-services-certs-create.md)voor meer informatie over Azure-certificaten. Zie de documentatie op voor een volledige beschrijving van OpenSSL-para meters [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Nadat u deze bestanden hebt gemaakt, uploadt u het `.cer` bestand naar Azure. Selecteer in het [Azure Portal][management-portal]op het tabblad **instellingen** de optie **uploaden**. Houd er rekening mee dat u het bestand hebt opgeslagen `.pem` .

Nadat u uw abonnements-ID hebt verkregen, maakt u een certificaat en uploadt u het `.cer` bestand naar Azure, maakt u verbinding met het Azure-beheer eindpunt. Maak verbinding door de abonnements-ID en het pad naar het `.pem` bestand door te geven aan **maken**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

In het voor gaande voor beeld `sms` is een **maken** -object. De klasse **maken** is de primaire klasse die wordt gebruikt voor het beheren van Azure-Services.

### <a name="management-certificates-on-windows-makecert"></a>Beheer certificaten op Windows (MakeCert)
U kunt een zelfondertekend beheer certificaat maken op uw computer met behulp van `makecert.exe` . Open een **Visual Studio-opdracht prompt** als **beheerder** en gebruik de volgende opdracht, waarbij u *AzureCertificate* vervangt door de naam van het certificaat dat u wilt gebruiken:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

Met de opdracht maakt `.cer` u het bestand en installeert u het in het **persoonlijke** certificaat archief. Zie het [overzicht van certificaten voor Azure Cloud Services](cloud-services-certs-create.md)voor meer informatie.

Nadat u het certificaat hebt gemaakt, uploadt u het `.cer` bestand naar Azure. Selecteer in het [Azure Portal][management-portal]op het tabblad **instellingen** de optie **uploaden**.

Nadat u uw abonnements-ID hebt verkregen, maakt u een certificaat en uploadt u het `.cer` bestand naar Azure, maakt u verbinding met het Azure-beheer eindpunt. Maak verbinding door de abonnements-ID en de locatie van het certificaat in uw **persoonlijke** certificaat Archief door te geven aan **maken** (opnieuw, vervang *AzureCertificate* door de naam van uw certificaat).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

In het voor gaande voor beeld `sms` is een **maken** -object. De klasse **maken** is de primaire klasse die wordt gebruikt voor het beheren van Azure-Services.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Beschik bare locaties weer geven
Als u de locaties wilt weer geven die beschikbaar zijn voor hosting services, gebruikt u de **lijst \_ locatie** methode.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Wanneer u een Cloud service of opslag service maakt, moet u een geldige locatie opgeven. De methode **lijst \_ locaties** retourneert altijd een actuele lijst van de beschik bare locaties. Vanaf deze locatie zijn de beschik bare locaties:

* Europa -west
* Europa - noord
* Azië - zuidoost
* Azië - oost
* VS - centraal
* VS - noord-centraal
* VS - zuid-centraal
* VS - west
* VS - oost
* Japan - oost
* Japan - west
* Brazilië - zuid
* Australië - oost
* Australië - zuidoost

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Een Cloud service maken
Wanneer u een toepassing maakt en deze in azure uitvoert, worden de code en configuratie samen een Azure- [Cloud service][cloud service]genoemd. (Het heette een *gehoste service* in eerdere versies van Azure.) U kunt de methode **\_ gehoste \_ service maken** gebruiken om een nieuwe gehoste service te maken. Maak de service door een gehoste service naam op te geven (deze moet uniek zijn in Azure), een label (automatisch gecodeerd naar base64), een beschrijving en een locatie.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

U kunt alle gehoste services voor uw abonnement weer geven met de **\_ gehoste methode \_ Services** .

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Als u informatie wilt ophalen over een bepaalde gehoste service, geeft u de naam van de gehoste service door aan de methode **Get \_ hosted \_ service \_ Properties** .

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Nadat u een Cloud service hebt gemaakt, implementeert u de code in de service met de methode **\_ implementatie maken** .

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Een Cloud service verwijderen
U kunt een Cloud service verwijderen door de service naam door te geven aan de methode **\_ gehoste \_ service verwijderen** .

```python
sms.delete_hosted_service('myhostedservice')
```

Voordat u een service kunt verwijderen, moeten alle implementaties voor de service eerst worden verwijderd. Zie [een implementatie verwijderen](#DeleteDeployment)voor meer informatie.

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Een implementatie verwijderen
Als u een implementatie wilt verwijderen, gebruikt u de methode **\_ implementatie verwijderen** . In het volgende voor beeld ziet u hoe u een implementatie verwijdert met de naam `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Een opslag service maken
Een [opslag service](../storage/common/storage-account-create.md) geeft u toegang tot Azure- [blobs](../storage/blobs/storage-quickstart-blobs-python.md),- [tabellen](../cosmos-db/table-storage-how-to-use-python.md)en- [wacht rijen](../storage/queues/storage-python-how-to-use-queue-storage.md). Als u een opslag service wilt maken, moet u een naam voor de service (tussen 3 en 24 kleine letters en uniek in Azure) hebben. U hebt ook een beschrijving nodig, een label (Maxi maal 100 tekens, automatisch gecodeerd naar base64) en een locatie. In het volgende voor beeld ziet u hoe u een opslag service maakt door een locatie op te geven:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

In het vorige voor beeld kan de status van de bewerking **\_ opslag \_ account maken** worden opgehaald door het resultaat dat wordt geretourneerd door het maken van een **\_ opslag \_ account** aan de methode **Get \_ Operation- \_ status** door te geven. 

U kunt uw opslag accounts en de bijbehorende eigenschappen weer geven met de methode **\_ opslag \_ accounts weer geven** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Een opslag service verwijderen
Als u een opslag service wilt verwijderen, geeft u de naam van de opslag service door aan de methode voor het verwijderen van een **\_ opslag \_ account** . Als u een opslag service verwijdert, worden alle gegevens die zijn opgeslagen in de service (blobs, tabellen en wacht rijen) verwijderd.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Beschik bare besturings systemen weer geven
Gebruik de methode **\_ Operating \_ Systems** om de besturings systemen weer te geven die beschikbaar zijn voor hosting services.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

U kunt ook de methode voor het **\_ besturings \_ systeem \_ listen** gebruiken, waarin de besturings systemen worden gegroepeerd op familie.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Een installatie kopie van een besturings systeem maken
Als u een installatie kopie van een besturings systeem wilt toevoegen aan de opslag plaats voor installatie kopieën, gebruikt u de methode **\_ OS- \_ installatie kopie toevoegen** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Als u wilt weer geven welke installatie kopieën van het besturings systeem beschikbaar zijn, gebruikt u de methode **\_ \_ installatie kopieën lijst besturingssysteem** . Het bevat alle platform installatie kopieën en gebruikers installatie kopieën.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Een installatie kopie van een besturings systeem verwijderen
Als u een installatie kopie van een gebruiker wilt verwijderen, gebruikt u de methode **\_ \_ installatie kopie van het besturings systeem verwijderen** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Een virtuele machine maken
Als u een virtuele machine wilt maken, moet u eerst een [Cloud service](#CreateCloudService)maken. Maak vervolgens de implementatie van de virtuele machine met behulp van de **\_ implementatie methode virtuele \_ machine \_ maken** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Een virtuele machine verwijderen
Als u een virtuele machine wilt verwijderen, verwijdert u eerst de implementatie met behulp van de **\_ implementatie methode verwijderen** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

De Cloud service kan vervolgens worden verwijderd met behulp van de methode **\_ gehoste \_ service verwijderen** .

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Een virtuele machine maken op basis van een vastgelegde installatie kopie van een virtuele machine
Als u een VM-installatie kopie wilt vastleggen, roept u eerst de methode voor het **vastleggen van \_ VM- \_ installatie kopie** op.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Om ervoor te zorgen dat u de installatie kopie hebt vastgelegd, gebruikt u de **lijst \_ VM- \_ installatie kopieën weer geven** . Zorg ervoor dat de afbeelding wordt weer gegeven in de resultaten.

```python
images = sms.list_vm_images()
```

Als u de virtuele machine tot slot wilt maken met behulp van de vastgelegde installatie kopie, gebruikt u de methode voor het maken van een **\_ virtuele \_ machine \_** als voorheen, maar deze tijd geeft u in plaats daarvan op in de vm_image_name.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Zie [een virtuele Linux-machine vastleggen](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic)voor meer informatie over het vastleggen van een virtuele Linux-machine in het klassieke implementatie model.

Zie [een virtuele Windows-machine vastleggen](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic)voor meer informatie over het vastleggen van een virtuele Windows-machine in het klassieke implementatie model.

## <a name="next-steps"></a><a name="What's Next"> </a>Volgende stappen
Nu u de basis principes van Service beheer hebt geleerd, kunt u toegang krijgen tot de [volledige API-referentie documentatie voor de Azure python-SDK](https://azure-sdk-for-python.readthedocs.org/) en eenvoudig complexe taken uitvoeren om uw python-toepassing te beheren.

Raadpleeg het [Python Developer Center](https://azure.microsoft.com/develop/python/) voor meer informatie.

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/