---
title: 'Zelfstudie: Verbinding maken met een Azure Cosmos-account met behulp van een privé-eindpunt in Azure'
titleSuffix: Azure Private Link
description: Ga aan de slag met deze zelfstudie en gebruik een privé-eindpunt in Azure om privé verbinding te maken met een Azure Cosmos-account.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: a544d0c5fafbdaf9d272fed552fb38eda613292f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522138"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Zelfstudie: Verbinding maken met een Azure Cosmos-account met behulp van een privé-eindpunt in Azure

Een privé-eindpunt in Azure is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een Bastion-host maken
> * Hiermee maakt u een virtuele machine.
> * Een Cosmos DB-account maken met een privé-eindpunt.
> * Verbinding met privé-eindpunt voor een Cosmos DB-account testen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Een virtueel netwerk en Bastion-host maken

In deze sectie leert u een virtueel netwerk, subnet en Bastion-host te maken. 

De Bastion-host wordt gebruikt om veilig verbinding te maken met de virtuele machine om het privé-eindpunt te testen.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **myResourceGroup**. |
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
    | Resourcegroep | Selecteer **myResourceGroup**. |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Een Cosmos DB-account maken met een privé-eindpunt

In deze sectie maakt u een Cosmos DB-account en configureert u het privé-eindpunt.

1. Selecteer in het menu aan de linkerkant **Een resource maken** > **Databases** > **Cosmos DB-account**, of zoek naar **Cosmos DB-account** in het zoekvak.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Cosmos DB-account maken**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Accountnaam | Voer **mycosmosdb** in. Als de naam niet beschikbaar is, voert u een unieke naam in. |
    | API | Selecteer **Core (SQL)**. |
    | Locatie | Selecteer **VS - oost**. |
    | Capaciteitsmodus | Laat de standaardinstelling **Ingerichte doorvoer** staan. |
    | Korting voor gratis lagen toepassen | Laat de standaardinstelling **Niet toepassen** staan. |
    | Georedundantie | Laat de standaardinstelling **Uitschakelen** staan. |
    | Schrijven voor meerdere regio's | Laat de standaardinstelling **Uitschakelen** staan. |
   
3. Selecteer het tabblad **Netwerken** of selecteer de **Volgende: Knop Netwerken**.

4. Op het tabblad **Netwerken** de volgende informatie invoeren of selecteren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerkverbinding** | |
    | Verbindingsmethode | Selecteer **Privé-eindpunt**. |
    | **Firewall configureren** | |
    | Toegang vanuit de Azure-portal toestaan | Laat de standaardinstelling **Toestaan** staan. |
    | Toegang toestaan vanaf mijn IP | Laat de standaardinstelling **Weigeren** staan. |

5. Selecteer **+ Toevoegen** onder **Privé-eindpunt**.

6. Voer de volgende gegevens in of selecteer deze onder **Privé-eindpunt maken**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | Locatie | Selecteer **VS - oost** |
    | Naam | Voer **myPrivateEndpoint** |
    | Subresource van doel | Laat de standaardinstelling **Core (SQL)** staan |
    | **Netwerken** |  |
    | Virtueel netwerk | **myVNet** selecteren |
    | Subnet | Selecteer **mySubnet** |
    | **Privé-DNS-integratie** |
    | Integreren met privé-DNS-zone | Laat de standaard **Ja** staan. |
    | Privé-DNS-zone | Laat de standaardinstelling (Nieuwe) privatelink.documents.azure.com staan |

7. Selecteer **OK**.

8. Selecteer **Controleren + maken**.

9. Selecteer **Maken**.

### <a name="add-a-database-and-a-container"></a>Een database en een container toevoegen

1. Selecteer **Naar resource gaan** of klik in het menu aan de linkerkant van de Azure-portal en selecteer **Alle resources** > **mycosmosdb**.

2. Selecteer in het menu aan de linkerkant **Data Explorer**.

3. Selecteer in het venster **Data Explorer** **Nieuwe container**.

4. Voer in **Container toevoegen** de volgende informatie in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Database-id | Laat de standaardinstelling van **Nieuwe maken** staan. </br> Voer **mydatabaseid** in het tekstvak in. |
    | Doorvoer (400 - 100.000 RU/s) | Laat de standaardinstelling van **Handmatig** staan. </br> Voer **400** in het tekstvak in. |
    | Container-id | Voer **mycontainerid** in |
    | Partitiesleutel | Voer **/myKey** in |

5. Selecteer **OK**.

10. Selecteer in de sectie **Instellingen** van het CosmosDB-account **Sleutels**.

11. Selecteer **myResourceGroup**.

12. Selecteer het opslagaccount dat u in de vorige stappen hebt gemaakt.

14. Selecteer en kopieer de **PRIMAIRE VERBINDINGSREEKS**.

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt om verbinding te maken met het Cosmos DB-account op het privé-eindpunt.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

2. Selecteer **myResourceGroup**.

3. Selecteer **myVM**.

4. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

7. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

8. Voer `nslookup <storage-account-name>.documents.azure.com` in. Vervang **\<storage-account-name>** door de naam op van het opslagaccount dat u in de eerdere stappen hebt gemaakt. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```

    Er wordt een privé-IP-adres van **10.1.0.5** geretourneerd voor de naam van het Cosmos DB-account.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

9. Installeer [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%252fazure%252fstorage%252fblobs%252ftoc.json) op de virtuele machine.

10. Selecteer **Voltooien** nadat **Microsoft Azure Storage Explorer** is geïnstalleerd.  Laat het selectievakje aangevinkt om de toepassing te openen.

11. Selecteer **Annuleren** op het scherm **Verbinding maken met Azure Storage**.

12. Selecteer in Storage Explorer **Cosmos DB-accounts** en selecteer **Verbinding maken met Cosmos DB**.

13. Behoud de standaardinstelling van **SQL** onder **API selecteren**.

14. Plak in het vak onder **Verbindingsreeks** de verbindingsreeks uit het Cosmos DB-account dat u in de vorige stappen hebt gekopieerd.

15. Selecteer **Next**.

16. Controleer of de instellingen juist zijn in **Samenvatting van de verbinding**.  

17. Selecteer **Verbinding maken**.

18. Verbreek de verbinding met **myVM**.


## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan het virtueel netwerk, de virtuele machine en het Cosmos DB-account met de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer **myResourceGroup**.

3. Selecteer **Resourcegroep verwijderen**.

4. In **TYPE THE RESOURCE GROUP** voert u **myResourceGroup** in.

5. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gemaakt:

* Een virtueel netwerk en Bastion-host.
* Een Virtuele machine.
* Een Cosmos DB-account.

Een Private Link-service maken:
> [!div class="nextstepaction"]
> [Een Private Link-service maken](create-private-link-service-portal.md)