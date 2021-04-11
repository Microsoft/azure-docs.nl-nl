---
title: Firewall regels beheren-Azure CLI-Azure Database for MySQL-flexibele server
description: Maak en beheer firewall regels voor Azure Database for MySQL-flexibele server met behulp van de Azure CLI-opdracht regel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c699b00ebbecdbee985babe47c036cce6e0cb4b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109961"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Azure Database for MySQL-flexibele server firewall regels maken en beheren met de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Azure Database for MySQL Flexibele server ondersteunt twee typen elkaar wederzijds uitsluitende netwerkverbindingsmethoden om verbinding te maken met uw flexibele server. De twee opties zijn:

- Openbare toegang (toegestane IP-adressen)
- Privétoegang (VNet-integratie)

In dit artikel wordt de nadruk gelegd op het maken van een MySQL **-server met open bare toegang (toegestane IP-adressen)** met behulp van Azure CLI en biedt een overzicht van de Azure cli-opdrachten die u kunt gebruiken om firewall regels te maken, bij te werken, te verwijderen, te vermelden en weer te geven nadat u de server hebt gemaakt. Met *open bare toegang (toegestane IP-adressen)* worden de verbindingen met de mysql-server beperkt tot toegestane IP-adressen. De IP-adressen van de client moeten worden toegestaan in firewall regels. Raadpleeg [open bare toegang (toegestane IP-adressen)](./concepts-networking.md#public-access-allowed-ip-addresses)voor meer informatie hierover. De firewall regels kunnen worden gedefinieerd op het moment dat de server wordt gemaakt (aanbevolen), maar kunnen ook later worden toegevoegd.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

[Azure Cloud Shell](../../cloud-shell/overview.md) is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. Als u naar [https://shell.azure.com/bash](https://shell.azure.com/bash) gaat, kunt u Cloud Shell ook openen in een afzonderlijk browsertabblad. Selecteer **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en selecteer vervolgens **Enter** om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az-login). Let op de eigenschap **id** , die verwijst naar de **abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account#az-account-set). Noteer de **id-** waarde van de AZ- **aanmeld** uitvoer die moet worden gebruikt als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Een firewall regel maken tijdens een flexibele server maken met behulp van Azure CLI

U kunt de `az mysql flexible-server --public access` opdracht gebruiken om de flexibele server met *open bare toegang (toegestane IP-adressen)* te maken en de firewall regels te configureren tijdens het maken van een flexibele server. U kunt de schakel optie **--Public-Access** gebruiken om de toegestane IP-adressen op te geven waarmee verbinding kan worden gemaakt met de server. U kunt één of meerdere IP-adressen opgeven die moeten worden opgenomen in de lijst met toegestane Ip's. Het IP-adres bereik moet een streepje gescheiden en geen spaties bevatten. Er zijn verschillende opties voor het maken van een flexibele server met behulp van CLI, zoals wordt weer gegeven in het onderstaande voor beeld.

Raadpleeg de [naslag documentatie](/cli/azure/mysql/flexible-server) van Azure CLI voor de volledige lijst met CONFIGUREER bare cli-para meters. In de onderstaande opdrachten kunt u optioneel de resource groep opgeven.

- Een flexibele server met open bare toegang maken en IP-adres van client toevoegen om toegang te krijgen tot de server
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Een flexibele server met open bare toegang maken en het IP-adres bereik toevoegen om toegang te krijgen tot deze server

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Een flexibele server met open bare toegang maken en toepassingen van Azure IP-adressen toestaan verbinding te maken met uw flexibele server
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Met deze optie wordt de firewall zodanig geconfigureerd dat open bare toegang van Azure-Services en-resources in azure naar deze server wordt toegestaan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
    >
- Een flexibele server met open bare toegang maken en alle IP-adressen toestaan
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > Met de bovenstaande opdracht maakt u een firewall regel met het IP-begin adres = 0.0.0.0, het laatste IP-adres = 255.255.255.255 en worden er geen IP-adressen geblokkeerd. Alle hosts op internet hebben toegang tot deze server. U wordt aangeraden deze regel alleen tijdelijk en alleen te gebruiken op test servers die geen gevoelige gegevens bevatten.

- Een flexibele server met open bare toegang en zonder IP-adres maken
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > het is niet raadzaam een server te maken zonder firewall regels. Als u geen firewall regels toevoegt, kan geen enkele client verbinding maken met de server.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Een firewall regel maken en beheren nadat de server is gemaakt
De opdracht **AZ mysql Flexible-Server firewall-Rule** wordt gebruikt vanuit de Azure CLI om firewall regels te maken, verwijderen, weer geven en bijwerken.

Opdrachten:
- **maken**: een flexibele server firewall regel maken.
- **lijst**: de flexibele server firewall regels weer geven.
- **Update**: een flexibele server firewall regel bijwerken.
- **weer geven**: de details van een flexibele server firewall regel weer geven.
- **verwijderen**: een flexibele server firewall regel verwijderen.

Raadpleeg de [naslag documentatie](/cli/azure/mysql/flexible-server) van Azure CLI voor de volledige lijst met CONFIGUREER bare cli-para meters. In de onderstaande opdrachten kunt u optioneel de resource groep opgeven.

### <a name="create-a-firewall-rule"></a>Een firewallregel maken
Gebruik de `az mysql flexible-server firewall-rule create` opdracht om een nieuwe firewall regel te maken op de server.
Als u toegang tot een bereik van IP-adressen wilt toestaan, geeft u het IP-adres op als IP-begin adres en IP-eind adres, zoals in dit voor beeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt verlenen voor één IP-adres, geeft u slechts één IP-adres op, zoals in dit voor beeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Als u wilt toestaan dat toepassingen van IP-adressen van Azure verbinding maken met uw flexibele server, geeft u het IP-adres 0.0.0.0 op als IP-begin, zoals in dit voor beeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie wordt de firewall zodanig geconfigureerd dat open bare toegang van Azure-Services en-resources in azure naar deze server wordt toegestaan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Bij voltooiing worden met elke opdracht uitvoer een lijst gemaakt met de details van de firewall regel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

### <a name="list-firewall-rules"></a>Firewall regels weer geven 
Gebruik de `az mysql flexible-server firewall-rule list` opdracht om de bestaande server firewall regels op de-server weer te geven. U ziet dat het kenmerk server naam is opgegeven in de schakel optie **-naam** .
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
In de uitvoer worden de regels, indien aanwezig, in JSON-indeling weer gegeven (standaard). U kunt de schakel optie--Output Table * * gebruiken om de resultaten in een meer Lees bare tabel indeling uit te voeren.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Een firewall regel bijwerken
Gebruik de `az mysql flexible-server firewall-rule update` opdracht om een bestaande firewall regel op de server bij te werken. Geef de naam van de bestaande firewall regel op als invoer, evenals het begin-IP-adres en de IP-eind adres kenmerken die moeten worden bijgewerkt.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Wanneer de opdracht is voltooid, worden de details weer gegeven van de firewall regel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

> [!NOTE]
> Als de firewall regel niet bestaat, wordt de regel gemaakt met de opdracht bijwerken.

### <a name="show-firewall-rule-details"></a>Details van firewall regel weer geven
Gebruik de `az mysql flexible-server firewall-rule show` opdracht om de details van de bestaande firewall regel van de server weer te geven. Geef de naam van de bestaande firewall regel op als invoer.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Wanneer de opdracht is voltooid, worden de details van de firewall regel die u hebt opgegeven, weer gegeven in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

### <a name="delete-a-firewall-rule"></a>Een firewallregel verwijderen
Gebruik de `az mysql flexible-server firewall-rule delete` opdracht om een bestaande firewall regel te verwijderen van de-server. Geef de naam op van de bestaande firewall regel.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Als de bewerking is voltooid, is er geen uitvoer. Wanneer de fout is opgetreden, wordt tekst weer gegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in azure database for MySQL flexibele server](./concepts-networking.md)
- Meer informatie over [Azure database for MySQL flexibele server firewall regels](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Firewallregels voor Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-firewall-portal.md).