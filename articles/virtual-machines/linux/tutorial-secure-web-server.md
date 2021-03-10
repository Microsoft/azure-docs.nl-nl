---
title: 'Zelfstudie: Een Linux-webserver beveiligen met TLS/SSL-certificaten in Azure'
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt om een virtuele Linux-machine waarop de NGINX-webserver wordt uitgevoerd, te beveiligen met SSL-certificaten die zijn opgeslagen in Azure Key Vault.
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3f762597ad81dfaba907115cbcf6074d81ec2fa4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549580"
---
# <a name="tutorial-secure-a-web-server-on-a-linux-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Zelfstudie: Een webserver op een virtuele Linux-machine in Azure beveiligen met TLS/SSL-certificaten die zijn opgeslagen in Key Vault
Om webservers te beveiligen, kan een Transport Layer Security-certificaat (TLS), voorheen bekend als Secure Sockets Layer (SSL), worden gebruikt voor het versleutelen van internetverkeer. Deze TLS/SSL-certificaten kunnen worden opgeslagen in Azure Key Vault en beveiligde implementaties van certificaten aan virtuele Linux-machines (VM's) in Azure toestaan. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de NGINX-webserver installeren
> * Het certificaat invoeren in de virtuele machine en NGINX configureren met een TLS-binding

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.


## <a name="overview"></a>Overzicht
Azure Key Vault beschermt cryptografische sleutels en geheimen, zoals certificaten of wachtwoorden. Key Vault helpt het beheerproces voor certificaten te stroomlijnen en zorgt dat u de controle houdt over de sleutels waarmee deze certificaten toegankelijk zijn. U kunt een zelfondertekend certificaat in Key Vault maken of een bestaand, vertrouwd certificaat dat u al bezit uploaden.

In plaats van met een aangepaste VM-installatiekopie die standaard certificaten bevat, kunt u certificaten invoeren in een actieve virtuele machine. Dit proces zorgt ervoor dat de meest recente certificaten tijdens de implementatie op een webserver zijn geïnstalleerd. Als u een certificaat wilt vernieuwen of vervangen, hoeft u niet ook een nieuwe aangepaste VM-installatiekopie te maken. De meest recente certificaten worden automatisch toegevoegd als u extra virtuele machines maakt. Tijdens het hele proces verlaten de certificaten nooit het Azure-platform of zijn ze blootgesteld in een script, opdrachtregelgeschiedenis of sjabloon.


## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken
Voordat u een Key Vault en certificaten kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupSecureWeb* gemaakt op de locatie *VS Oost*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Maak vervolgens een Key Vault met [Az keyvault maken](/cli/azure/keyvault) en schakel deze in voor gebruik wanneer u een virtuele machine implementeert. Elke Key Vault moet een unieke naam hebben van alleen kleine letters. Vervang *\<mykeyvault>* in het volgende voorbeeld met de naam van uw eigen unieke Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Een certificaat genereren en opslaan in Key Vault
Voor gebruik in de productie, moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider met [az keyvault certificate import](/cli/azure/keyvault/certificate). Voor deze zelfstudie toont het volgende voorbeeld hoe kunt u een zelfondertekend certificaat kunt genereren met [az keyvault certificate create](/cli/azure/keyvault/certificate) dat gebruikmaakt van het standaardbeleid voor certificaten:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Een certificaat voor gebruik met een virtuele machine voorbereiden
Om het certificaat te gebruiken tijdens het creatieproces van de virtuele machine, verkrijgen de ID van het certificaat met [az keyvault secret list-versions](/cli/azure/keyvault/secret). Converteer het certificaat met [az vm secret format](/cli/azure/vm/secret#az-vm-secret-format). Het volgende voorbeeld wijst de uitvoer van deze opdrachten toe aan variabelen voor eenvoudig gebruik in de volgende stappen:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Maak een cloud-init-config om NGINX te beveiligen
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Wanneer u een virtuele machine maakt, worden certificaten en sleutels opgeslagen in de beveiligde */var/lib/waagent/* directory. Gebruik cloud-init voor het automatiseren van het toevoegen van het certificaat aan de virtuele machine en het configureren van de webserver. In dit voorbeeld installeert en configureert u de NGINX-webserver. U kunt hetzelfde proces gebruiken voor het installeren en configureren van Apache. 

Maak een bestand met de naam *cloud-init-web-server.txt* en plak de volgende configuratie:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Een beveiligde virtuele machine maken
Maak een virtuele machine met [az vm create](/cli/azure/vm). De gegevens van het certificaat worden geïnjecteerd uit de Sleutelkluis met de parameter `--secrets`. U kunt de cloud init-configuratie doorgeven met de parameter `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. Dit adres wordt gebruikt voor toegang tot uw site in een webbrowser.

Zodat beveiligde webverkeer uw virtuele machine bereiken kan, open poort 443 vanaf het Internet met [az vm open-port](/cli/azure/vm):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testen van de beveiligde web-app
Nu kunt u een webbrowser openen en *https:\/\/\<publicIpAddress>* in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. Als u een zelfondertekend certificaat gebruikt, aanvaardt u de beveiligingswaarschuwing:

![Beveiligingswaarschuwing voor web browser accepteren](./media/tutorial-secure-web-server/browser-warning.png)

Uw beveiligde NGINX-site wordt vervolgens weergegeven zoals in het volgende voorbeeld:

![Beveiligde actieve NGINX-site weergeven](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u een NGINX-webserver beveiligd met een TLS/SSL-certificaat dat is opgeslagen in Azure Key Vault. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de NGINX-webserver installeren
> * Het certificaat invoeren in de virtuele machine en NGINX configureren met een TLS-binding

Volg deze link om voorbeelden te zien van vooraf gemaakte virtuele machinescripts.

> [!div class="nextstepaction"]
> [Voorbeelden van virtuele Linux-machines](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)