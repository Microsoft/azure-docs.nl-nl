---
title: 'Quickstart: Een Azure Database for MySQL Flexible Server maken - Azure Portal'
description: Dit artikel leidt u stapsgewijs door Azure Portal zodat u in ongeveer vijf minuten een Azure-database for MySQL Flexible Server kunt maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566755"
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
    Gebruikersnaam van beheerder |**mydemouser**| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De gebruikersnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.|
    Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Het moet ook tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 t/m 9) en niet-alfanumerieke tekens (!, $, #, % enzovoort).|
    Regio|De regio het dichtst bij uw gebruikers| De locatie die het dichtst bij uw gebruikers is.|
    Versie|**5.7**| Een hoofdversie van MySQL.|
    Berekening en opslag | **Burstable**, **Standard_B1ms**, **10 GiB**, **7 dagen** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Server configureren**. **Burstable**, **Standard_B1ms**, **10 GiB** en **7 dagen** zijn de standaardwaarden voor **Rekenlaag**, **Rekengrootte**, **Opslaggrootte** en **Bewaartermijn voor back-up**. U kunt deze waarden laten zoals ze zijn of ze aanpassen. Als u de berekening en opslagselectie wilt opslaan, selecteert u **Opslaan** om door te gaan met configuraties. In de volgende schermopname ziet u de opties voor berekenen en opslaan.|
    
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

Als u een flexibele server hebt gemaakt met Persoonlijke toegang (VNet-integratie), moet u verbinding maken met uw server vanuit een resource binnen hetzelfde virtuele netwerk als uw server. U kunt een virtuele machine maken en toevoegen aan het virtuele netwerk dat is gemaakt met uw flexibele server.

Als u uw flexibele server met Openbare toegang (toegestane IP-adressen) hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server.

U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) gebruiken om vanuit uw lokale omgeving verbinding met de server te maken. 

Als u mysql.exe gebruikt, maakt u verbinding met behulp van de volgende opdracht. Gebruik de servernaam, de gebruikersnaam en het wachtwoord in de opdracht. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
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