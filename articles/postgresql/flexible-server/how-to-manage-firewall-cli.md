---
title: Firewallregels beheren - Azure CLI - Azure Database for PostgreSQL - Flexible Server
description: Maak en beheer firewallregels voor Azure Database for PostgreSQL - Flexible Server met behulp van de Azure CLI-opdrachtregel.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: beed3dac1a2ca5bc6d2a87ba2a9044333e798fa9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778513"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Firewallregels voor Azure Database for PostgreSQL flexibele server maken en beheren met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL Flexibele server ondersteunt twee soorten wederzijds exclusieve netwerkconnectiviteitsmethoden om verbinding te maken met uw flexibele server. De twee opties zijn:

* Openbare toegang (toegestane IP-adressen)
* Privétoegang (VNet-integratie)

In dit artikel richten we ons op het maken van een PostgreSQL-server met openbare toegang **(toegestane IP-adressen)** met behulp van Azure CLI en bieden we een overzicht van Azure CLI-opdrachten die u kunt gebruiken om firewallregels te maken, bij te werken, te verwijderen, weer te geven en weer te geven nadat de server is gemaakt. Met *openbare toegang (toegestane IP-adressen)* zijn de verbindingen met de PostgreSQL-server beperkt tot alleen toegestane IP-adressen. De IP-adressen van de client moeten worden toegestaan in firewallregels. Raadpleeg Openbare toegang [(toegestane IP-adressen)](./concepts-networking.md#public-access-allowed-ip-addresses)voor meer informatie. De firewallregels kunnen worden gedefinieerd op het moment dat de server wordt gemaakt (aanbevolen), maar kunnen ook later worden toegevoegd.

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

U kunt de opdracht gebruiken om de flexibele server met openbare toegang `az postgres flexible-server --public access` *(toegestane IP-adressen)* te maken en de firewallregels te configureren tijdens het maken van flexibele servers. U kunt de **schakelknop --public-access** gebruiken om de toegestane IP-adressen op te geven die verbinding kunnen maken met de server. U kunt één IP-adres of een bereik van IP-adressen op te geven dat moet worden opgenomen in de lijst met toegestane IP-adressen. IP-adresbereik moet worden gescheiden door streepjes en bevat geen spaties. Er zijn verschillende opties voor het maken van een flexibele server met cli, zoals wordt weergegeven in het onderstaande voorbeeld.

Raadpleeg de referentiedocumentatie voor Azure CLI <!--FIXME --> voor de volledige lijst met configureerbare CLI-parameters. In de onderstaande opdrachten kunt u bijvoorbeeld desgewenst de resourcegroep opgeven.

- Maak een flexibele server met openbare toegang en voeg het IP-adres van de client toe om toegang te krijgen tot de server
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Maak een flexibele server met openbare toegang en voeg het ip-adresbereik toe om toegang te krijgen tot deze server

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Een flexibele server met openbare toegang maken en toepassingen van Azure IP-adressen toestaan verbinding te maken met uw flexibele server
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Met deze optie configureert u de firewall om openbare toegang van Azure-services en -resources binnen Azure naar deze server toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
    >
- - Een flexibele server maken met openbare toegang en alle IP-adressen toestaan
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Met de bovenstaande opdracht maakt u een firewallregel met begin-IP-adres=0.0.0.0, eind-IP-adres=255.255.255 en worden er geen IP-adressen geblokkeerd. Elke host op internet heeft toegang tot deze server. Het wordt ten zeerste aangeraden deze regel alleen tijdelijk te gebruiken en alleen op testservers die geen gevoelige gegevens bevatten.
- Een flexibele server maken met openbare toegang en zonder IP-adres
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Het is niet raadzaam om een server zonder firewallregels te maken. Als u geen firewallregels toevoegt, kan er geen client verbinding maken met de server.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Een firewallregel maken en beheren na het maken van de server
De **opdracht az postgres flexible-server firewall-rule** wordt vanuit de Azure CLI gebruikt om firewallregels te maken, te verwijderen, weer te geven, weer te geven en bij te werken.

Opdrachten:
- **maken:** maak een firewallregel voor een flexibele server.
- **list:** vermeld de firewallregels voor flexibele servers.
- **update:** werk een firewallregel voor flexibele servers bij.
- **show:** de details van een firewallregel voor flexibele servers tonen.
- **delete:** verwijder een firewallregel voor flexibele servers.

Raadpleeg de referentiedocumentatie voor Azure CLI <!--FIXME --> voor de volledige lijst met configureerbare CLI-parameters. In de onderstaande opdrachten kunt u bijvoorbeeld eventueel de resourcegroep opgeven.

### <a name="create-a-firewall-rule"></a>Een firewallregel maken
Gebruik de `az postgres flexible-server firewall-rule create` opdracht om een nieuwe firewallregel te maken op de server.
Als u toegang wilt toestaan tot een bereik van IP-adressen, geeft u het IP-adres op als het begin-IP-adres en eind-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt toestaan voor één IP-adres, geeft u slechts één IP-adres op, zoals in dit voorbeeld.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Als u wilt dat toepassingen van Azure IP-adressen verbinding kunnen maken met uw flexibele server, geeft u het IP-adres 0.0.0.0 op als het begin-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall om openbare toegang van Azure-services en -resources binnen Azure tot deze server toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 
Als de opdracht is gemaakt, geeft elke uitvoer van de opdracht maken de details weer van de firewallregel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

### <a name="list-firewall-rules"></a>Firewallregels op een lijst zetten 
Gebruik de `az postgres flexible-server firewall-rule list` opdracht om de bestaande serverfirewallregels op de server weer te geven. U ziet dat het kenmerk servernaam is opgegeven in de **schakelknop --name.** 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
De uitvoer bevat de regels, indien van u, in JSON-indeling (standaard). U kunt de schakelknop --output table** gebruiken om de resultaten uit te geven in een beter leesbare tabelindeling.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Een firewallregel bijwerken
Gebruik de `az postgres flexible-server firewall-rule update` opdracht om een bestaande firewallregel op de server bij te werken. Geef de naam van de bestaande firewallregel op als invoer, evenals de begin-IP-adres- en eind-IP-adreskenmerken die moeten worden bijgewerkt.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Als de opdracht is geslaagd, geeft de uitvoer van de opdracht de details weer van de firewallregel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

> [!NOTE]
> Als de firewallregel niet bestaat, wordt de regel gemaakt met de opdracht bijwerken.
### <a name="show-firewall-rule-details"></a>Details van firewallregel tonen
Gebruik de `az postgres flexible-server firewall-rule show` opdracht om de bestaande firewallregeldetails van de server weer te geven. Geef de naam van de bestaande firewallregel op als invoer.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Als de opdracht is geslaagd, geeft de uitvoer van de opdracht de details weer van de firewallregel die u hebt opgegeven, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

### <a name="delete-a-firewall-rule"></a>Een firewallregel verwijderen
Gebruik de `az postgres flexible-server firewall-rule delete` opdracht om een bestaande firewallregel van de server te verwijderen. Geef de naam van de bestaande firewallregel op.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Als dit is gelukt, is er geen uitvoer. Als de fout is opgetreden, wordt de tekst van het foutbericht weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in Azure Database for PostgreSQL - Flexible Server](./concepts-networking.md)
- Meer informatie over [Azure Database for PostgreSQL - Firewallregels voor flexibele servers](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Maak en beheer Azure Database for PostgreSQL - Firewallregels voor flexibele servers met behulp van Azure Portal](./how-to-manage-firewall-portal.md).
