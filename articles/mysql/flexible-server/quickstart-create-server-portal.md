---
title: 'Quickstart: Een Azure Database for MySQL Flexible Server maken - Azure Portal'
description: Dit artikel leidt u stapsgewijs door Azure Portal zodat u in ongeveer vijf minuten een Azure-database for MySQL Flexible Server kunt maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 53878384f4eb056f0cb23ec9005043ac26c8fad2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492563"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Quickstart: Een Azure Database for MySQL Flexible Server maken met behulp van Azure Portal

Azure Database for MySQL Flexible Server is een beheerde service waarmee u MySQL-servers met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze quickstart ziet u hoe u met behulp van Azure Portal een flexibele server kunt maken.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Ga naar de [Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Een flexibele Azure Database for MySQL Flexible Server maken

U maakt een flexibele server met een gedefinieerde set [reken- en opslagresources](./concepts-compute-storage.md). De server wordt gemaakt binnen een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md).

Volg deze stappen om een flexibele server te maken:

1. Zoek en selecteer **Azure Database for MySQL-servers** in de portal:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Schermopname met een zoekopdracht naar Azure Database for MySQL-servers.":::

2. Selecteer **Toevoegen**. 

3. Op de pagina **Implementatieoptie Azure Database for MySQL selecteren** selecteert u **Flexibele server** als implementatieoptie:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Schermopname van de optie Flexibele server.":::    

4. Voer in het tabblad **Basisprincipes** de volgende gegevens in: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Schermopname waarin het tabblad Basis van de pagina Flexibele server wordt weergegeven."::: 
                                    
    |**Instelling**|**Voorgestelde waarde**|**Beschrijving**|
    |---|---|---|
    Abonnement|De naam van uw abonnement|Het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wilt worden gefactureerd voor de resource.|
    Resourcegroep|**myresourcegroup**| Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.|
    Servernaam |**mydemoserver**|Een unieke naam die uw flexibele server identificeert. De domeinnaam `mysql.database.azure.com` wordt toegevoegd aan de naam van de server die u opgeeft. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Dit wachtwoord moet tussen 3 en 63 tekens bevatten.|
    Regio|De regio het dichtst bij uw gebruikers| De locatie die het dichtst bij uw gebruikers is.|
    Type werk belasting| Ontwikkeling | Voor de werk belasting van de productie kunt u kiezen voor kleine/middel groot of groot formaat, afhankelijk van de [max_connections](concepts-server-parameters.md#max_connections) vereisten|
    Beschikbaarheidszone| Geen voor keur | Als uw toepassing in azure Vm's, virtuele-machine schaal sets of AKS-exemplaar is ingericht in een specifieke beschikbaarheids zone, kunt u uw flexibele server in dezelfde beschikbaarheids zone opgeven voor termijnen-toepassing en-Data Base om de prestaties te verbeteren door de netwerk latentie in meerdere zones te verlagen.|
    Hoge beschikbaarheid| Standaard | Voor productie servers wordt het inschakelen van een zone redundant hoge Beschik baarheid (HA) sterk aanbevolen voor bedrijfs continuïteit en bescherming tegen zone fouten|
    MySQL-versie|**5.7**| Een hoofdversie van MySQL.|
    Gebruikersnaam van beheerder |**mydemouser**| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De gebruikersnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.|
    Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Het moet ook tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 t/m 9) en niet-alfanumerieke tekens (!, $, #, % enzovoort).|
    Berekening en opslag | **Burstable**, **Standard_B1ms**, **10 GiB**, **100 IOPS**, **7 dagen** | De berekenings-, opslag-, IOPS-en back-upconfiguraties voor uw nieuwe server. Selecteer **Server configureren**. **Burstable**, **Standard_B1ms**, **10 GiB**, **100 IOPS** en **7 dagen** zijn de standaard waarden voor de **Compute-laag**, de **reken grootte**, de **opslag grootte**, **IOPS** en de **Bewaar periode** voor back-ups. U kunt deze waarden laten zoals ze zijn of ze aanpassen. Voor een snellere gegevens belasting tijdens de migratie wordt aangeraden de IOPS te verhogen tot de maximale grootte die wordt ondersteund door de reken grootte, en deze later opnieuw te schalen om kosten te besparen. Als u de berekening en opslagselectie wilt opslaan, selecteert u **Opslaan** om door te gaan met configuraties. In de volgende schermopname ziet u de opties voor berekenen en opslaan.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Schermopname met de opties voor berekenen en opslaan.":::

5. Netwerkopties configureren.

    Op het tabblad **Netwerken** kunt u kiezen hoe de server bereikbaar is. Azure Database for MySQL Flexible Server biedt twee netwerkopties om verbinding te maken met uw server: 
   - Openbare toegang (toegestane IP-adressen)
   - Privétoegang (VNet-integratie) 
   
   Als u openbare toegang gebruikt, is de toegang tot uw server beperkt tot toegestane IP-adressen die u toevoegt aan een firewallregel. Met deze methode wordt voorkomen dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij u een firewallregel maakt om de firewall te openen voor specifieke IP-adressen. Als u privétoegang (VNet-integratie) gebruikt, is de toegang tot uw server beperkt tot het virtuele netwerk. In deze quickstart leest u hoe u openbare toegang tot de server kunt inschakelen. [Meer informatie over connectiviteitsmethoden vindt u in het artikel over concepten.](./concepts-networking.md)

    > [!NOTE]
    > U kunt de verbindingsmethode niet wijzigen nadat u de server hebt gemaakt. Als u bijvoorbeeld **Openbare toegang (toegestane IP-adressen)** selecteert tijdens het maken van de server, kunt u niet wijzigen naar **Persoonlijke toegang (VNet-integratie)** nadat de server is gemaakt. We raden u ten zeerste aan om uw server te maken met privétoegang om de toegang tot uw server via VNet-integratie te helpen beveiligen. [Meer informatie over privétoegang vindt u in het artikel over concepten.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Schermopname van het tabblad Netwerken.":::  

6. Selecteer **Beoordelen + maken** om uw flexibele serverconfiguratie te controleren.

7. Selecteer **Maken** om de server in te richten. Het inrichten kan enkele minuten duren.

8. Selecteer **Meldingen** op de werkbalk (de knop met de klok) om het implementatieproces te bewaken. Na de implementatie kunt u **Vastmaken aan dashboard** selecteren. Hiermee maakt u een tegel voor deze flexibele server op uw dashboard in Azure Portal. Deze tegel is een snelkoppeling naar de **overzichtspagina** van de server. Als u **Naar de resource gaan** selecteert, wordt de **overzichtspagina** van de server weergegeven.

Standaard worden deze databases gemaakt voor de server: information_schema, mysql, performance_schema en sys.

> [!NOTE]
> Controleer of uw netwerk uitgaand verkeer toestaat via poort 3306, die wordt gebruikt door Azure Database for MySQL Flexible Server, om connectiviteitsproblemen te vermijden.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Verbinding maken met de server met behulp van mysql.exe

Als u een flexibele server hebt gemaakt met Persoonlijke toegang (VNet-integratie), moet u verbinding maken met uw server vanuit een resource binnen hetzelfde virtuele netwerk als uw server. U kunt een virtuele machine maken en toevoegen aan het virtuele netwerk dat is gemaakt met uw flexibele server. Raadpleeg de documentatie over het configureren van [persoonlijke toegang](how-to-manage-virtual-network-portal.md) voor meer informatie.

Als u uw flexibele server met Openbare toegang (toegestane IP-adressen) hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server. Raadpleeg de [documentatie over het maken of beheren van firewall regels](how-to-manage-firewall-portal.md) voor stapsgewijze instructies.

U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) gebruiken om vanuit uw lokale omgeving verbinding met de server te maken. Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-service met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten. Als u verbinding wilt maken met uw flexibele MySQL-server, moet u het [open bare SSL-certificaat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) voor verificatie van de certificerings instantie downloaden.

In het volgende voor beeld ziet u hoe u verbinding maakt met uw flexibele server met behulp van de MySQL-opdracht regel interface. U moet eerst de MySQL-opdracht regel installeren als deze nog niet is geïnstalleerd. U downloadt het DigiCertGlobalRootCA-certificaat dat vereist is voor SSL-verbindingen. Gebruik de instelling--SSL-modus = vereist connection string om TLS/SSL-certificaat verificatie af te dwingen. Geef het pad van het lokale certificaat bestand door aan de para meter--ssl-ca. Vervang de waarden door uw werkelijke servernaam en wachtwoord.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Als u uw flexibele server hebt ingericht met behulp van **open bare toegang**, kunt u ook [Azure Cloud shell](https://shell.azure.com/bash) gebruiken om verbinding te maken met uw flexibele server met behulp van een vooraf geïnstalleerde mysql-client, zoals hieronder wordt weer gegeven:

Als u Azure Cloud Shell wilt gebruiken om verbinding te maken met uw flexibele server, moet u toegang tot het netwerk van Azure Cloud Shell op uw flexibele server toestaan. Hiertoe gaat u naar de Blade **netwerk** op Azure portal voor uw flexibele mysql-server en schakelt u het selectie vakje onder **firewall** in, waarbij ' open bare toegang vanaf een Azure-service in azure op deze server toestaan ' wordt weer gegeven in de onderstaande scherm afbeelding. Klik vervolgens op Opslaan om de instelling te behouden.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Cloud Shell toegang tot MySQL flexibele server voor open bare netwerk configuratie kunt toestaan.":::

> [!NOTE]
> Het controleren of het toegankelijk is voor het gebruik **van een Azure-service in azure naar deze server** moet alleen worden gebruikt voor ontwikkelen of testen. Hiermee wordt de firewall geconfigureerd om verbindingen toe te staan van IP-adressen die zijn toegewezen aan een Azure-service of-Asset, met inbegrip van verbindingen van de abonnementen van andere klanten.

Klik op **proberen** om de Azure Cloud shell te starten en gebruik de volgende opdrachten om verbinding te maken met uw flexibele server. Gebruik de servernaam, de gebruikersnaam en het wachtwoord in de opdracht. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Wanneer u verbinding maakt met uw flexibele server met behulp van Azure Cloud Shell, moet u de para meter--SSL = True gebruiken en niet-SSL-modus = vereist.
> De primaire reden hiervoor is Azure Cloud Shell worden geleverd met een vooraf geïnstalleerde mysql.exe-client van een MariaDB-distributie waarvoor--SSL-para meter vereist is, terwijl de mysql-client van de distributie van Oracle vereist--SSL-modus para meter.

Als het volgende fout bericht wordt weer gegeven wanneer u verbinding maakt met uw flexibele server met behulp van de eerder genoemde opdracht, hebt u de firewall regel niet meer ingesteld met de optie ' open bare toegang vanaf een Azure-service toestaan in azure naar deze server ', maar eerder wel of niet opgeslagen. Stel de firewall opnieuw in en probeer het opnieuw.

FOUT 2002 (HY000): kan geen verbinding maken met MySQL-server op <servername> (115)

## <a name="clean-up-resources"></a>Resources opschonen
U hebt nu een flexibele Azure Database for MySQL-server in een resourcegroep gemaakt. Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep te verwijderen. U kunt ook de MySQL-server verwijderen. Voltooi de volgende stappen om de resourcegroep te verwijderen:

1. Zoek en selecteer **Resourcegroepen** in de Azure-portal.
1. Selecteer in de lijst met resourcegroepen de naam van de resourcegroep.
1. Selecteer op de **overzichtspagina** voor uw resourcegroep de optie **Resourcegroep verwijderen**.
1. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

Om uw server te verwijderen, selecteert u **Verwijderen** op de **overzichtspagina** van uw server, zoals hier wordt weergegeven:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Schermopname waarin wordt weergegeven hoe een server kan worden verwijderd.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een PHP-web-app (Laravel) bouwen met MySQL](tutorial-php-database-app.md)
