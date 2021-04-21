---
title: Firewallregels beheren - Azure CLI - Azure Database for MySQL - Flexible Server
description: Maak en beheer firewallregels voor Azure Database for MySQL - Flexible Server met behulp van de Azure CLI-opdrachtregel.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0692e7e7452fef9577414e9aa5340d933f50b30e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776947"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Firewallregels voor flexibele Azure Database for MySQL maken en beheren met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Azure Database for MySQL Flexibele server ondersteunt twee typen elkaar wederzijds uitsluitende netwerkverbindingsmethoden om verbinding te maken met uw flexibele server. De twee opties zijn:

- Openbare toegang (toegestane IP-adressen)
- Privétoegang (VNet-integratie)

In dit artikel richten we ons op het maken van Een MySQL-server met openbare toegang **(toegestane IP-adressen)** met behulp van Azure CLI en krijgt u een overzicht van Azure CLI-opdrachten die u kunt gebruiken om firewallregels te maken, bij te werken, te verwijderen, weer te geven en weer te geven nadat de server is gemaakt. Bij *openbare toegang (toegestane IP-adressen)* zijn de verbindingen met de MySQL-server beperkt tot alleen toegestane IP-adressen. De IP-adressen van de client moeten worden toegestaan in firewallregels. Raadpleeg Openbare toegang [(toegestane IP-adressen)](./concepts-networking.md#public-access-allowed-ip-addresses)voor meer informatie. De firewallregels kunnen worden gedefinieerd op het moment dat de server wordt gemaakt (aanbevolen), maar kunnen ook later worden toegevoegd.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

[Azure Cloud Shell](../../cloud-shell/overview.md) is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. Als u naar [https://shell.azure.com/bash](https://shell.azure.com/bash) gaat, kunt u Cloud Shell ook openen in een afzonderlijk browsertabblad. Selecteer **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en selecteer vervolgens **Enter** om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az_login). Noteer **de eigenschap** ID, die verwijst naar **Abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account#az_account_set). Noteer de **id-waarde uit** de **uitvoer az login** om te gebruiken als de waarde voor het argument **subscription** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Een firewallregel maken tijdens het maken van een flexibele server met behulp van Azure CLI

U kunt de opdracht gebruiken om de flexibele server met openbare toegang `az mysql flexible-server --public access` *(toegestane IP-adressen)* te maken en de firewallregels te configureren tijdens het maken van een flexibele server. U kunt de **schakelknop --public-access** gebruiken om de toegestane IP-adressen op te geven die verbinding kunnen maken met de server. U kunt één IP-adres of een bereik van IP-adressen op te geven dat moet worden opgenomen in de lijst met toegestane IP-adressen. IP-adresbereik moet worden gescheiden door streepjes en bevat geen spaties. Er zijn verschillende opties voor het maken van een flexibele server met behulp van CLI, zoals wordt weergegeven in het onderstaande voorbeeld.

Raadpleeg de azure [CLI-referentiedocumentatie](/cli/azure/mysql/flexible-server) voor de volledige lijst met configureerbare CLI-parameters. In de onderstaande opdrachten kunt u bijvoorbeeld desgewenst de resourcegroep opgeven.

- Maak een flexibele server met openbare toegang en voeg het IP-adres van de client toe voor toegang tot de server
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Maak een flexibele server met openbare toegang en voeg het IP-adresbereik toe voor toegang tot deze server

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Maak een flexibele server met openbare toegang en sta toepassingen van Azure IP-adressen toe om verbinding te maken met uw flexibele server
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Met deze optie configureert u de firewall om openbare toegang van Azure-services en -resources binnen Azure tot deze server toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
    >
- Een flexibele server met openbare toegang maken en alle IP-adressen toestaan
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > Met de bovenstaande opdracht maakt u een firewallregel met begin-IP-adres=0.0.0.0, eind-IP-adres=255.255.255.255 en worden er geen IP-adressen geblokkeerd. Elke host op internet heeft toegang tot deze server. Het wordt ten zeerste aangeraden deze regel alleen tijdelijk te gebruiken en alleen op testservers die geen gevoelige gegevens bevatten.

- Een flexibele server met openbare toegang en zonder IP-adres maken
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > We raden u niet aan om een server zonder firewallregels te maken. Als u geen firewallregels toevoegt, kan er geen client verbinding maken met de server.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Een firewallregel maken en beheren na het maken van de server
De **opdracht az mysql flexible-server firewall-rule** wordt gebruikt vanuit de Azure CLI om firewallregels te maken, te verwijderen, weer te geven, weer te geven en bij te werken.

Opdrachten:
- **create**: Maak een firewallregel voor flexibele servers.
- **list:** de firewallregels voor flexibele servers opneren.
- **update:** werk een firewallregel voor flexibele servers bij.
- **show:** de details van een firewallregel voor flexibele servers tonen.
- **delete:** verwijder een firewallregel voor flexibele servers.

Raadpleeg de [referentiedocumentatie](/cli/azure/mysql/flexible-server) voor Azure CLI voor de volledige lijst met configureerbare CLI-parameters. In de onderstaande opdrachten kunt u bijvoorbeeld eventueel de resourcegroep opgeven.

### <a name="create-a-firewall-rule"></a>Een firewallregel maken
Gebruik de `az mysql flexible-server firewall-rule create` opdracht om een nieuwe firewallregel te maken op de server.
Als u toegang wilt toestaan tot een bereik van IP-adressen, geeft u het IP-adres op als het begin-IP-adres en eind-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt toestaan voor één IP-adres, geeft u slechts één IP-adres op, zoals in dit voorbeeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Als u wilt dat toepassingen van Azure IP-adressen verbinding kunnen maken met uw flexibele server, geeft u het IP-adres 0.0.0.0 op als het begin-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall om openbare toegang van Azure-services en -resources binnen Azure tot deze server toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Als de opdracht is gemaakt, geeft elke uitvoer van de opdracht maken de details weer van de firewallregel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

### <a name="list-firewall-rules"></a>Firewallregels op een lijst zetten 
Gebruik de `az mysql flexible-server firewall-rule list` opdracht om de bestaande serverfirewallregels op de server weer te geven. U ziet dat het kenmerk servernaam is opgegeven in de **schakelknop --name.**
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
De uitvoer bevat de regels, indien van u, in JSON-indeling (standaard). U kunt de schakelknop --output table** gebruiken om de resultaten uit te geven in een beter leesbare tabelindeling.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Een firewallregel bijwerken
Gebruik de `az mysql flexible-server firewall-rule update` opdracht om een bestaande firewallregel op de server bij te werken. Geef de naam van de bestaande firewallregel op als invoer, evenals de begin-IP-adres- en eind-IP-adreskenmerken die moeten worden bijgewerkt.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Als de opdracht is geslaagd, geeft de uitvoer van de opdracht de details weer van de firewallregel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

> [!NOTE]
> Als de firewallregel niet bestaat, wordt de regel gemaakt met de opdracht bijwerken.

### <a name="show-firewall-rule-details"></a>Details van firewallregel tonen
Gebruik de `az mysql flexible-server firewall-rule show` opdracht om de bestaande firewallregeldetails van de server weer te geven. Geef de naam van de bestaande firewallregel op als invoer.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Als de opdracht is geslaagd, geeft de uitvoer van de opdracht de details weer van de firewallregel die u hebt opgegeven, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

### <a name="delete-a-firewall-rule"></a>Een firewallregel verwijderen
Gebruik de `az mysql flexible-server firewall-rule delete` opdracht om een bestaande firewallregel van de server te verwijderen. Geef de naam van de bestaande firewallregel op.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Als dit is gelukt, is er geen uitvoer. Als de fout is opgetreden, wordt de tekst van het foutbericht weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in Azure Database for MySQL Flexible Server](./concepts-networking.md)
- Meer informatie over [Azure Database for MySQL firewallregels voor Flexibele server](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Firewallregels voor Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-firewall-portal.md).