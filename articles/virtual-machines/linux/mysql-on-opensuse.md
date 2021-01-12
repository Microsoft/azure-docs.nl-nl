---
title: MySQL installeren op een OpenSUSE-VM in azure
description: Meer informatie over het installeren van MySQL op een virtuele OpenSUSE Linux-machine in Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 608cfca3343b02101ac5e08acf0bec62900d1443
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108650"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>MySQL installeren op een virtuele machine met OpenSUSE Linux in azure

[MySQL](https://www.mysql.com) is een populaire open-source SQL database. In deze zelf studie wordt uitgelegd hoe u een virtuele machine met OpenSUSE Linux maakt en vervolgens MySQL installeert.


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Een virtuele machine maken waarop OpenSUSE Linux wordt uitgevoerd

Maak eerst een resource groep. In dit voor beeld heeft de resource groep de naam *mySQSUSEResourceGroup* en wordt deze gemaakt in de regio *VS-Oost* .

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Maak de VM. In dit voor beeld heeft de virtuele machine de naam *myVM* en is de VM-grootte *Standard_D2s_v3*, maar u moet de [VM-grootte](../sizes.md) kiezen die het meest geschikt is voor uw werk belasting.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image SUSE:openSUSE-Leap:15-2:latest \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

U moet ook een regel toevoegen aan de netwerk beveiligings groep om verkeer toe te staan via poort 3306 voor MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

U gebruikt SSH om verbinding te maken met de virtuele machine. In dit voor beeld is het open bare IP-adres van de virtuele machine *10.111.112.113*. U kunt het IP-adres in de uitvoer zien wanneer u de virtuele machine hebt gemaakt.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>De virtuele machine bijwerken
 
Nadat u verbinding hebt gemaakt met de virtuele machine, installeert u systeem updates en-patches. 
   
```bash
sudo zypper update
```

Volg de aanwijzingen om uw virtuele machine bij te werken.

## <a name="install-mysql"></a>MySQL installeren 


Installeer de MySQL in de virtuele machine via SSH. Beantwoord de vragen indien van toepassing.

```bash
sudo zypper install mysql
```
 
Stel MySQL in om te starten wanneer het systeem wordt opgestart. 

```bash
sudo systemctl enable mysql
```
Controleer of MySQL is ingeschakeld.

```bash
systemctl is-enabled mysql
```

Dit moet als resultaat worden geretourneerd: ingeschakeld.

Start de server opnieuw.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-wacht woord

Na de installatie is het MySQL-hoofd wachtwoord standaard leeg. Voer het script voor **\_ beveiligde \_ installatie van MySQL** uit om MySQL te beveiligen. Het script vraagt u om het MySQL-hoofd wachtwoord te wijzigen, anonieme gebruikers accounts te verwijderen, aanmelden bij externe hoofdmap uit te scha kelen, test databases te verwijderen en de tabel met bevoegdheden opnieuw te laden. 

Nadat de server opnieuw is opgestart, moet u opnieuw SSH naar de VM.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Aanmelden bij MySQL

U kunt zich nu aanmelden en de MySQL-prompt invoeren.

```bash  
sudo mysql -u root -p
```
Hiermee schakelt u over naar de MySQL-prompt waarin u SQL-instructies kunt verzenden om te communiceren met de data base.

Maak nu een nieuwe MySQL-gebruiker.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
De punt komma (;) aan het einde van de regel is van cruciaal belang voor het beëindigen van de opdracht.


## <a name="create-a-database"></a>Een database maken


Maak een Data Base en verleen de `mysqluser` gebruikers machtigingen.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Database gebruikers namen en wacht woorden worden alleen gebruikt door scripts die verbinding maken met de data base.  Namen van database gebruikers accounts vertegenwoordigen niet noodzakelijkerwijs werkelijke gebruikers accounts op het systeem.

Aanmelden vanaf een andere computer inschakelen. In dit voor beeld is het IP-adres van de computer waarvoor aanmelden is toegestaan *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Als u het hulp programma voor MySQL-database beheer wilt afsluiten, typt u:

```    
quit
```


## <a name="next-steps"></a>Volgende stappen
Zie de [MySQL-documentatie](https://dev.mysql.com/doc)voor meer informatie over MySQL.
