---
title: Open Shift container platform 3,11 in azure-vereisten
description: Vereisten voor het implementeren van open Shift container platform 3,11 in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 645402d6c6b1fe744938ef5597098f46bc80c78e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672366"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Algemene vereisten voor het implementeren van open Shift container platform 3,11 in azure

In dit artikel worden algemene vereisten beschreven voor het implementeren van open Shift container platform of OKD in Azure.

De installatie van open SHIFT maakt gebruik van Ansible playbooks. Ansible maakt gebruik van Secure Shell (SSH) om verbinding te maken met alle clusterhosts om de installatie stappen te volt ooien.

Wanneer ansible de SSH-verbinding met de externe hosts maakt, kan er geen wacht woord worden ingevoerd. Daarom kan aan de persoonlijke sleutel geen wacht woord (wachtwoordzin) worden gekoppeld of kan de implementatie niet worden uitgevoerd.

Omdat de virtuele machines (Vm's) worden geïmplementeerd via Azure Resource Manager sjablonen, wordt dezelfde open bare sleutel gebruikt voor toegang tot alle Vm's. De bijbehorende persoonlijke sleutel moet zich op de VM behoren die ook alle playbooks uitvoert. Als u deze actie veilig wilt uitvoeren, wordt een Azure-sleutel kluis gebruikt om de persoonlijke sleutel door te geven aan de virtuele machine.

Als er voor containers een permanente opslag ruimte nodig is, zijn er permanente volumes vereist. Open Shift biedt ondersteuning voor virtuele harde schijven (Vhd's) van Azure voor permanente volumes, maar Azure moet eerst worden geconfigureerd als de Cloud provider.

In dit model, open Shift:

- Hiermee maakt u een VHD-object in een Azure-opslag account of een beheerde schijf.
- Koppelt de VHD aan een VM en formatteert het volume.
- Koppelt het volume aan de pod.

Deze configuratie werkt alleen als open Shift machtigingen heeft om deze taken uit te voeren in Azure. Een service-principal wordt gebruikt voor dit doel einde. De Service-Principal is een beveiligings account in Azure Active Directory dat machtigingen voor bronnen heeft.

De Service-Principal moet toegang hebben tot de opslag accounts en Vm's die het cluster vormen. Als alle open Shift-cluster bronnen op één resource groep worden geïmplementeerd, kan aan de Service-Principal machtigingen worden verleend voor die resource groep.

In deze hand leiding wordt beschreven hoe u de artefacten maakt die aan de vereisten zijn gekoppeld.

> [!div class="checklist"]
> * Een sleutel kluis maken voor het beheren van SSH-sleutels voor het open Shift-cluster.
> * Een service-principal maken voor gebruik door de Azure-Cloud provider.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 
Meld u aan bij uw Azure-abonnement met de opdracht [AZ login](/cli/azure/reference-index) en volg de instructies op het scherm, of klik op **proberen het** Cloud shell te gebruiken.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. U moet een speciale resource groep gebruiken om de sleutel kluis te hosten. Deze groep is gescheiden van de resource groep waarin de open Shift-cluster resources worden geïmplementeerd.

In het volgende voor beeld wordt een resource groep met de naam *keyvaultrg* gemaakt op de locatie *eastus* :

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken
Maak een sleutel kluis om de SSH-sleutels voor het cluster op te slaan met de opdracht [AZ sleutel kluis Create](/cli/azure/keyvault) . De naam van de sleutel kluis moet globaal uniek zijn en moet zijn ingeschakeld voor de implementatie van een sjabloon, anders mislukt de implementatie met de fout ' KeyVaultParameterReferenceSecretRetrieveFailed '.

In het volgende voor beeld wordt een sleutel *kluis gemaakt* met de naam in de resource groep *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Een SSH-sleutel maken 
Er is een SSH-sleutel nodig om de toegang tot het open Shift-cluster te beveiligen. Een SSH-sleutel paar maken met behulp van de `ssh-keygen` opdracht (op Linux of macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Uw SSH-sleutel paar kan geen wacht woord/wachtwoordzin hebben.

Zie [SSH-sleutels maken in Windows](./ssh-from-windows.md)voor meer informatie over SSH-sleutels in Windows. Zorg ervoor dat u de persoonlijke sleutel in de OpenSSH-indeling exporteert.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Sla de persoonlijke SSH-sleutel op in Azure Key Vault
De openshift-implementatie maakt gebruik van de SSH-sleutel die u hebt gemaakt om de toegang tot het open Shift-model te beveiligen. Als u de implementatie wilt inschakelen om de SSH-sleutel veilig op te halen, slaat u de sleutel op in Key Vault met behulp van de volgende opdracht:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Een service-principal maken 
Open Shift communiceert met Azure met behulp van een gebruikers naam en wacht woord of een service-principal. Een Azure-Service-Principal is een beveiligings identiteit die u kunt gebruiken met apps, services en hulpprogram ma's voor automatisering zoals open SHIFT. U beheert en definieert de machtigingen voor de bewerkingen die de Service-Principal in azure kan uitvoeren. Het is het beste om de machtigingen van de service-principal te beperken tot specifieke resource groepen in plaats van met het hele abonnement.

Maak een service-principal met [AZ AD SP create-for-RBAC](/cli/azure/ad/sp) en uitvoer de referenties die openshift nodig heeft.

In het volgende voor beeld wordt een service-principal gemaakt en worden de Inzender machtigingen toegewezen aan een resource groep met de naam *openshiftrg*.

Maak eerst de resource groep met de naam *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Service-Principal maken:

```azurecli
az group show --name openshiftrg --query id
```

Sla de uitvoer van de opdracht op en gebruik in plaats van $scope in de volgende opdracht

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Noteer de eigenschap appId en het wacht woord die worden geretourneerd door de opdracht:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Zorg ervoor dat u het beveiligde wacht woord noteert omdat het niet mogelijk is om dit wacht woord opnieuw op te halen.

Zie [een Azure-service-principal maken met Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie over service-principals.

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Vereisten die alleen van toepassing zijn op de Resource Manager-sjabloon

Er moeten geheimen worden gemaakt voor de persoonlijke SSH-sleutel (**sshPrivateKey**), Azure AD client Secret (**aadClientSecret**), open Shift admin password (**OpenshiftPassword**) en Red Hat Subscription manager password of Activation Key (**rhsmPasswordOrActivationKey**).  Als er aangepaste TLS/SSL-certificaten worden gebruikt, moeten er bovendien zes extra geheimen worden gemaakt: **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** en **masterkeyfile**.  Deze para meters worden uitgebreid beschreven.

De sjabloon verwijst naar specifieke geheime namen, dus u **moet** de vetgedrukte namen gebruiken die hierboven worden vermeld (hoofdletter gevoelig).

### <a name="custom-certificates"></a>Aangepaste certificaten

De sjabloon implementeert standaard een open Shift-cluster met zelfondertekende certificaten voor de open Shift-webconsole en het routerings domein. Als u aangepaste TLS/SSL-certificaten wilt gebruiken, stelt u ' routingCertType ' in op ' Custom ' en ' masterCertType ' in ' Custom '.  U hebt de CA-, CERT-en sleutel bestanden nodig in. pem-indeling voor de certificaten.  Het is mogelijk om aangepaste certificaten te gebruiken voor één, maar niet voor de andere.

U moet deze bestanden opslaan in Key Vault geheimen.  Gebruik hetzelfde Key Vault als het account dat is gebruikt voor de persoonlijke sleutel.  In plaats van dat er 6 extra invoer vereist is voor de geheime namen, wordt de sjabloon vastgelegd om specifieke geheime namen te gebruiken voor elk van de TLS/SSL-certificaat bestanden.  Sla de certificaat gegevens op met behulp van de informatie in de volgende tabel.

| Geheime naam      | Certificaatbestand   |
|------------------|--------------------|
| mastercafile     | hoofd-CA-bestand     |
| mastercertfile   | hoofd certificaat bestand   |
| masterkeyfile    | hoofd sleutel bestand    |
| routingcafile    | CA-bestand voor route ring    |
| routingcertfile  | routerings certificaat bestand  |
| routingkeyfile   | routerings sleutel bestand   |

Maak de geheimen met behulp van de Azure CLI. Hieronder ziet u een voor beeld.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel komen de volgende onderwerpen aan bod:
> [!div class="checklist"]
> * Een sleutel kluis maken voor het beheren van SSH-sleutels voor het open Shift-cluster.
> * Een service-principal maken voor gebruik door de Azure Cloud Solution Provider.

Implementeer vervolgens een open Shift-cluster:

- [Open Shift container platform implementeren](./openshift-container-platform-3x.md)
- [Self-Managed Marketplace-aanbieding voor open Shift container platform implementeren](./openshift-container-platform-3x-marketplace-self-managed.md)
