---
title: Zelfstudie - Een Linux-VM aanpassen in Azure met cloud-init
description: In deze zelfstudie leert u hoe u cloud-init en Key Vault gebruikt voor het aanpassen van virtuele Linux-machines als ze de eerste keer worden opgestart in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8a8d5d5d76dc762734921dc2cb7ae575d15b626c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789631"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Zelfstudie - Cloud-init gebruiken voor het aanpassen van een virtuele Linux-machine in Azure bij de eerste keer dat die wordt opgestart

In een vorige zelfstudie hebt u geleerd hoe u een virtuele machine (VM) via SSH kunt verbinden en NGINX handmatig kunt installeren. Als u virtuele machines op een snelle en consistente manier wilt maken, is een vorm van automatisering doorgaans gewenst. Een veelgebruikte manier voor het aanpassen van een virtuele machine bij de eerste keer opstarten is het gebruik van [cloud-init](https://cloudinit.readthedocs.io). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een cloud-init-configuratiebestand maken
> * Een virtuele machine maken die gebruikmaakt van een cloud-init-bestand
> * Een actieve Node.js-app weergeven nadat de virtuele machine is gemaakt
> * Key Vault gebruiken voor het veilig opslaan van certificaten
> * Beveiligde implementaties van NGINX automatiseren met cloud-init

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="cloud-init-overview"></a>Overzicht van cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

Samen met onze partners willen we cloud-init opnemen en werkend krijgen in de installatiekopieën die zij aan Azure leveren. Raadpleeg [Cloud-init-ondersteuning voor virtuele machines in Azure](using-cloud-init.md) voor gedetailleerde informatie over Cloud-init-ondersteuning voor elke distributie.


## <a name="create-cloud-init-config-file"></a>Een cloud-init-configuratiebestand maken
Als u cloud-init in actie wilt zien, maakt u een VM waarop NGINX is geïnstalleerd en een eenvoudige 'Hallo wereld' Node.js-app wordt uitgevoerd. Met de volgende cloud init-configuratie installeert u de vereiste pakketten, maakt u een Node.js-app en initialiseert en start u vervolgens de app.

Maak bij de Bash-prompt of in Cloud Shell een bestand met de naam *cloud-init.txt* en plak de volgende configuratie in het bestand. Typ bijvoorbeeld `sensible-editor cloud-init.txt` om het bestand te maken en om een overzicht van beschikbare editors te zien. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Zie [Voorbeelden van cloud-init-configuraties](https://cloudinit.readthedocs.io/en/latest/topics/examples.html) voor meer informatie over configuratieopties voor cloud-init.

## <a name="create-virtual-machine"></a>Virtuele machine maken
Voordat u een virtuele machine kunt maken, moet u eerst een resourcegroep maken met [az-groep maken](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupAutomate* gemaakt op de locatie *VS Oost*:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Maak een virtuele machine met [az vm create](/cli/azure/vm#az_vm_create). Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. Het duurt mogelijk nog een paar minuten voordat u toegang hebt tot de app. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. Dit adres wordt gebruikt voor toegang tot de Node.js-app in een webbrowser.

Open poort 80 via internet met [az vm open-port](/cli/azure/vm#az_vm_open_port) zodat beveiligd webverkeer uw virtuele machine kan bereiken:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Web-app testen
U kunt nu een webbrowser openen en *http:\/\/\<publicIpAddress>* in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. De Node.js-app wordt weergegeven zoals in het volgende voorbeeld:

![Actieve NGINX-site weergeven](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Certificaten van Key Vault invoeren
In deze optionele sectie wordt beschreven hoe u certificaten veilig kunt opslaan in Azure Key Vault en ze tijdens de implementatie van de virtuele machine kunt invoeren. In plaats van een aangepaste installatiekopie te gebruiken die de certificaten standaard bevat, zorgt dit proces ervoor dat de meest recente certificaten zijn ingevoerd op een virtuele machine bij de eerste keer opstarten. Tijdens het proces verlaten de certificaten nooit het Azure-platform, en ook worden ze niet blootgesteld aan een script, opdrachtregelgeschiedenis of sjabloon.

Azure Key Vault beschermt cryptografische sleutels en geheimen, zoals certificaten of wachtwoorden. Key Vault stroomlijnt het beheerproces voor sleutels en zorgt dat u de controle houdt over de sleutels waarmee uw gegevens toegankelijk zijn en worden versleuteld. Dit scenario bevat enkele Key Vault-concepten voor het maken en gebruiken van een certificaat, maar biedt geen volledig overzicht van het gebruik van Key Vault.

De volgende stappen laten zien hoe u het volgende kunt doen:

- Een Azure Key Vault maken
- Een certificaat genereren of uploaden naar de Key Vault
- Een geheim maken van het certificaat dat moet worden ingevoerd in een virtuele machine
- Een virtuele machine maken en het certificaat invoeren

### <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken
Maak eerst een Key Vault met [az keyvault create](/cli/azure/keyvault#az_keyvault_create) en schakel deze in voor gebruik wanneer u een virtuele machine implementeert. Elke Key Vault moet een unieke naam hebben van alleen kleine letters. Vervang `mykeyvault` in het volgende voorbeeld door de naam van uw eigen unieke Key Vault:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Een certificaat genereren en opslaan in Key Vault
Voor gebruik in de productie, moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider met [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Voor deze zelfstudie toont het volgende voorbeeld hoe kunt u een zelfondertekend certificaat kunt genereren met [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) dat gebruikmaakt van het standaardbeleid voor certificaten:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Een certificaat voor gebruik met een virtuele machine voorbereiden
Om het certificaat te gebruiken tijdens het creatieproces van de virtuele machine, verkrijgen de ID van het certificaat met [az keyvault secret list-versions](/cli/azure/keyvault/secret#az_keyvault_secret_list_versions). Voor de VM is vereist dat het certificaat een bepaalde indeling heeft om te kunnen worden ingevoerd bij het opstarten. Converteer het certificaat daarom met [az vm format-secret](/cli/azure/vm). Het volgende voorbeeld wijst de uitvoer van deze opdrachten toe aan variabelen voor eenvoudig gebruik in de volgende stappen:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Cloud-init-config maken om NGINX te beveiligen
Wanneer u een virtuele machine maakt, worden certificaten en sleutels opgeslagen in de beveiligde */var/lib/waagent/* directory. U kunt de bijgewerkte cloud-init-configuratie uit het vorige voorbeeld gebruiken om het certificaat toe te voegen en NGINX te configureren.

Maak een bestand met de naam *cloud-init-secured.txt* en plak de volgende configuratie. Als u Cloud Shell gebruikt, maakt u het cloud-init-configuratiebestand daar en niet op uw lokale computer. Typ bijvoorbeeld `sensible-editor cloud-init-secured.txt` om het bestand te maken en om een overzicht van beschikbare editors te zien. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Een beveiligde virtuele machine maken
Maak een virtuele machine met [az vm create](/cli/azure/vm#az_vm_create). De gegevens van het certificaat worden geïnjecteerd uit de Sleutelkluis met de parameter `--secrets`. Net als in het vorige voorbeeld, geeft u ook de cloud-init-configuratie door met de parameter `--custom-data`:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. Het duurt mogelijk nog een paar minuten voordat u toegang hebt tot de app. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. Dit adres wordt gebruikt voor toegang tot de Node.js-app in een webbrowser.

Zodat beveiligde webverkeer uw virtuele machine bereiken kan, open poort 443 vanaf het Internet met [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>De beveiligde web-app testen
Nu kunt u een webbrowser openen en *https:\/\/\<publicIpAddress>* in de adresbalk invoeren. Geef uw eigen openbare IP-adres op zoals weergegeven in de uitvoer tijdens het maken van de vorige VM. Als u een zelfondertekend certificaat gebruikt, aanvaardt u de beveiligingswaarschuwing:

![Beveiligingswaarschuwing voor web browser accepteren](./media/tutorial-automate-vm-deployment/browser-warning.png)

Uw beveiligde NGINX-site en Node.js app worden vervolgens weergegeven zoals in het volgende voorbeeld:

![Beveiligde actieve NGINX-site weergeven](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u virtuele machines bij de eerste keer opstarten geconfigureerd met cloud-init. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een cloud-init-configuratiebestand maken
> * Een virtuele machine maken die gebruikmaakt van een cloud-init-bestand
> * Een actieve Node.js-app weergeven nadat de virtuele machine is gemaakt
> * Key Vault gebruiken voor het veilig opslaan van certificaten
> * Beveiligde implementaties van NGINX automatiseren met cloud-init

Ga door naar de volgende zelfstudie voor informatie over het maken van aangepaste VM-installatiekopieën.

> [!div class="nextstepaction"]
> [Aangepaste VM-installatiekopieën maken](./tutorial-custom-images.md)
