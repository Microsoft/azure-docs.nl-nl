---
title: 'Zelfstudie: Verbinding maken met een Azure SQL-server met behulp van een privé-eindpunt in het Azure - Portaal'
description: Gebruik deze zelfstudie om te leren hoe u een Azure SQL-server maakt met een privé-eindpunt met behulp van het Azure-portaal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: a7eca29dc9390306ac6ad4e66eec75a25c2d33ff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522155"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Zelfstudie: Verbinding maken met een Azure SQL-server met behulp van een privé-eindpunt in Azure - Azure-portaal

Een privé-eindpunt in Azure is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een Bastion-host maken
> * Hiermee maakt u een virtuele machine.
> * Maak een Azure SQL-server en een privé-eindpunt.
> * Connectiviteit met het privé-eindpunt van SQL-server testen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.


## <a name="create-a-virtual-network-and-bastion-host"></a>Een virtueel netwerk en Bastion-host maken

In deze sectie leert u een virtueel netwerk, subnet en Bastion-host te maken. 

De Bastion-host wordt gebruikt om veilig verbinding te maken met de virtuele machine om het privé-eindpunt te testen.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **CreateSQLEndpointTutorial-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **VS - oost** |

3. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

4. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **10.1.0.0/16** in |

5. Onder **Subnetnaam** selecteert u het woord **standaard**.

6. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Open **mySubnet** |
    | Subnetadresbereik | Voer **10.1.0.0/24** in |

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Beveiliging**.

9. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |


8. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

9. Selecteer **Maken**.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

In deze sectie maakt u een virtuele machine die wordt gebruikt om het persoonlijke eindpunt te testen.

1. Selecteer in de linkerbovenhoek van het portaal de optie **Een resource maken** > **Compute** > **Virtuele machine** of zoek naar **Virtuele machine** via het zoekvak.
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **CreateSQLEndpointTutorial** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Open **myVM** |
    | Region | Selecteer **VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter - Gen1** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **mySubnet** |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | **Basic**|
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |
   
5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Een Azure SQL-server en een privé-eindpunt maken

In deze sectie maakt u een SQL-server in Azure. 

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal de optie **Een resource maken** > **Databases** > **SQL-database**.

1. Typ of selecteer op het tabblad **Basisprincipes** van **SQL-database maken** deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **CreateSQLEndpointTutorial**. U hebt deze resourcegroep in de vorige sectie gemaakt.|
    | **Databasedetails** |  |
    | Databasenaam  | Voer **mysqldatabase** in. Als deze naam al wordt gebruikt, maakt u een unieke naam. |
    | Server | Selecteer **Nieuw maken**. |

6. Typ of selecteer in **Nieuwe server** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servernaam  | Voer **mysqlserver** in. Als deze naam al wordt gebruikt, maakt u een unieke naam.|
    | Aanmeldgegevens van serverbeheerder | Voer de naam van de beheerder van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 8 tekens lang zijn en moet voldoen aan de vooraf gedefinieerde vereisten. |
    | Locatie | Selecteer **VS - oost** |
    
7. Selecteer **OK**.

8. Selecteer het tabblad **Netwerken** of selecteer de **Volgende: Knop Netwerken**.

9. Typ of selecteer op het tabblad **Netwerken** deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerkverbinding** | |
    | Verbindingsmethode | Selecteer **Privé-eindpunt**. |
   
10. Selecteer **+ Privé-eindpunt toevoegen** in **Privé-eindpunten**.

11. Typ of selecteer in **Privé-eindpunt maken** deze informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **CreateSQLEndpointTutorial**. |
    | Locatie | Selecteer **VS - oost**. |
    | Name | Voer **myPrivateSQLendpoint** in. |
    | Stel subresource in | Selecteer **SQLServer**. |
    | **Netwerken** |  |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet**. |
    | **Privé-DNS-integratie** | |
    | Integreren met privé-DNS-zone | Laat de standaardwaarde **Ja** staan. |
    | Privé-DNS-zone | Laat de standaardwaarde **(Nieuwe) privatelink.blob.core.windows.net** staan. |

12. Selecteer **OK**. 

13. Selecteer **Controleren + maken**.

14. Selecteer **Maken**.

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt, om verbinding te maken met de SQL-server via het privé-eindpunt.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

2. Selecteer **CreateSQLEndpointTutorial**.

3. Selecteer **myVM**.

4. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

7. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

8. Voer `nslookup <sqlserver-name>.database.windows.net` in. Vervang **\<sqlserver-name>** door de naam van de SQL-server die u hebt gemaakt in de vorige stappen.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Er wordt een privé-IP-adres **10.1.0.5** geretourneerd voor de naam van de SQL-server.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.


9. Installeer [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) op **myVM**.

10. Open **SQL Server Management Studio**.

4. Typ of selecteer in **Verbinding maken met server** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype | Selecteer **Database Engine**.|
    | Servernaam | Voer **\<sqlserver-name>.database.windows.net** in |
    | Verificatie | Selecteer **SQL Server-verificatie**. |
    | Gebruikersnaam | Voer de gebruikersnaam in die u hebt opgegeven tijdens het maken van de server |
    | Wachtwoord | Voer het wachtwoord in dat u hebt opgegeven tijdens het maken van de server |
    | Wachtwoord onthouden | Selecteer **Ja**. |

1. Selecteer **Verbinden**.
2. Blader in het menu aan de linkerkant door databases.
3. (Optioneel) U kunt **mysqldatabase** maken of er een query op uitvoeren.
4. Sluit de externe bureaubladverbinding met **myVM**. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privé-eindpunt, de SQL-server en de VM, verwijdert u de resourcegroep en alle resources die deze bevat: 
1. Typ **CreateSQLEndpointTutorial** in het **Zoekvak** bovenaan de portal, en selecteer **CreateSQLEndpointTutorial** in de zoekresultaten. 
2. Selecteer **Resourcegroep verwijderen**. 
3. Type CreateSQLEndpointTutorial bij **NAAM VAN RESOURCEGROEP TYPEN** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gemaakt:

* Een virtueel netwerk en Bastion-host.
* Een virtuele machine.
* Een Azure SQL-server met een privé-eindpunt.

U hebt de virtuele machine gebruikt om de connectiviteit met de SQL-server via het privé-eindpunt veilig te testen.

Een Private Link-service maken:
> [!div class="nextstepaction"]
> [Een Private Link-service maken](create-private-link-service-portal.md)