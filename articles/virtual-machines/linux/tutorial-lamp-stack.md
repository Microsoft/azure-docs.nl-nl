---
title: 'Zelfstudie: LAMP en WordPress implementeren op een VM'
description: In deze zelfstudie leert u hoe u de LAMP-stack en WordPress installeert op een virtuele Linux-machine in Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816339"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Zelfstudie: Een LAMP-stack installeren op een Azure Linux-VM

Dit artikel begeleidt u bij de implementatie van een Apache-webserver, MySQL en PHP (de LAMP-stack) op een Ubuntu-VM in Azure. Als u de LAMP-server in actie wilt zien, kunt u eventueel een WordPress-site installeren en configureren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maken van een Ubuntu-VM 
> * Poort 80 openen voor webverkeer
> * Apache, MySQL en PHP installeren
> * Installatie en configuratie verifiëren
> * WordPress installeren 

Deze installatie is voor snelle tests en het testen van het concept. Zie de [documentatie van Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP) (Engelstalig) voor meer informatie over de LAMP-stack, waaronder aanbevelingen voor een productieomgeving.

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* en worden er SSH-sleutels gemaakt, als deze nog niet bestaan op een standaardsleutellocatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. Met de opdracht wordt *azureuser* ook ingesteld als gebruikersnaam voor de beheerder. Later gebruikt u deze naam om verbinding te maken met de VM. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Wanneer de virtuele machine is gemaakt, toont de Azure CLI informatie die lijkt op de informatie in het volgende voorbeeld. Noteer het `publicIpAddress`. Dit adres wordt in latere stappen gebruikt voor toegang tot de VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Poort 80 openen voor webverkeer 

Standaard worden alleen SSH-verbindingen toegestaan naar Linux-VM’s die zijn geïmplementeerd in Azure. Omdat deze VM wordt gebruikt als een webserver, moet u poort 80 openen voor verkeer vanaf internet. Gebruik de opdracht [az vm open-port](/cli/azure/vm) om de gewenste poort te openen.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Zie Poorten openen voor meer informatie over het openen van poorten [voor uw](nsg-quickstart.md)VM.

## <a name="ssh-into-your-vm"></a>SSH in uw virtuele machine

Als u het openbare IP-adres van de VM niet weet, voert u de opdracht [az network public-ip list](/cli/azure/network/public-ip) uit. U hebt dit IP-adres nodig voor verschillende latere stappen.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Gebruik de volgende opdracht om een SSH-sessie te starten voor de virtuele machine. Gebruik hierbij het juiste openbare IP-adres van uw virtuele machine. In dit voorbeeld is het IP-adres *40.68.254.142*. *azureuser* is de gebruikersnaam voor de beheerder die u hebt ingesteld bij het maken van de VM.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Apache, MySQL en PHP installeren

Voer de volgende opdracht uit om Ubuntu-pakketbronnen bij te werken en Apache, MySQL en PHP te installeren. Let op het caret-teken (^) aan het eind van de opdracht. Dit maakt deel uit van de pakketnaam van `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

U wordt gevraagd de pakketten en andere afhankelijkheden te installeren. In dit proces worden de minimaal vereiste PHP-extensies geïnstalleerd die voor het gebruik van PHP met MySQL nodig zijn.  

## <a name="verify-apache"></a>Apache Verifiëren

Controleer de versie van Apache met de volgende opdracht:
```bash
apache2 -v
```

Als Apache is geïnstalleerd en poort 80 is geopend voor de VM, is de webserver nu toegankelijk vanaf internet. Als u de standaardpagina van Apache2 Ubuntu wilt weergeven, opent u een webbrowser en voert u het openbare IP-adres van de VM in. Gebruik het openbare IP-adres dat u hebt gebruikt om een SSH-verbinding met de VM te maken:

![Standaardpagina van Apache][3]


## <a name="verify-and-secure-mysql"></a>MySQL verifiëren en beveiligen

Controleer de versie van MySQL met de volgende opdracht (let op de hoofdletter `V` van de parameter):

```bash
mysql -V
```

Als u de installatie van MySQL wilt beveiligen, inclusief het instellen van een hoofdwachtwoord, voert u het script `mysql_secure_installation` uit. 

```bash
sudo mysql_secure_installation
```

U kunt desgewenst de invoegtoepassing voor wachtwoordvalidatie instellen (aanbevolen). Vervolgens stelt u een wachtwoord in voor de MySQL-hoofdgebruiker en configureert u de resterende instellingen voor uw omgeving. We raden u aan alle vragen met 'J' (Ja) te beantwoorden.

Als u MySQL-functies wilt uitproberen (MySQL-database maken, gebruikers toevoegen of configuratie-instellingen wijzigen), meldt u zich aan bij MySQL. Deze stap is niet noodzakelijk voor het voltooien van deze zelfstudie.

```bash
sudo mysql -u root -p
```

Als u klaar bent, sluit u de mysql-prompt door `\q` te typen.

## <a name="verify-php"></a>PHP verifiëren

Controleer de versie van PHP met de volgende opdracht:

```bash
php -v
```

Als u meer tests wilt uitvoeren, maakt u snel een PHP-infopagina om in een browser weer te geven. Met de volgende opdracht wordt de PHP-infopagina gemaakt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nu kunt u de zojuist gemaakte PHP-infopagina controleren. Open een browser en ga naar `http://yourPublicIPAddress/info.php`. Vervang het openbare IP-adres van uw VM. Het moet er ongeveer uitzien als in deze afbeelding.

![PHP-infopagina][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een LAMP-server in Azure geïmplementeerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Maken van een Ubuntu-VM
> * Poort 80 openen voor webverkeer
> * Apache, MySQL en PHP installeren
> * Installatie en configuratie verifiëren
> * WordPress op de LAMP-server installeren

Ga door naar de volgende zelfstudie om te leren hoe u webservers kunt beveiligen met behulp van TSL-/SSL-certificaten.

> [!div class="nextstepaction"]
> [Webserver beveiligen met TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
