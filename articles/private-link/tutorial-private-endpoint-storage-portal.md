---
title: 'Zelfstudie: Verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure'
titleSuffix: Azure Private Link
description: Ga aan de slag met deze zelfstudie en gebruik een privé-eindpunt in Azure om privé verbinding te maken met een opslagaccount.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: eefef0eb61df9aa0597206b537f3d58e8b8b3213
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96484818"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Zelfstudie: Verbinding maken met een opslagaccount met behulp van een privé-eindpunt in Azure

Een privé-eindpunt in Azure is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een Bastion-host maken
> * Hiermee maakt u een virtuele machine.
> * Opslagaccounts maken met een privé-eindpunt.
> * Verbinding met privé-eindpunt voor opslagaccount testen.

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

## <a name="create-storage-account-with-a-private-endpoint"></a>Opslagaccounts maken met een privé-eindpunt

In deze sectie maakt u een opslagaccount en configureert u het privé-eindpunt.

1. Selecteer in het menu aan de linkerkant **Een resource maken** > **Opslag** > **Opslagaccount**, of zoek naar **Opslagaccount** in het zoekvak.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Opslagaccount maken**.

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van het opslagaccount | Voer **mystorageaccount** in. Als de naam niet beschikbaar is, voert u een unieke naam in. |
    | Locatie | Selecteer **VS - oost** |
    | Prestaties | Laat de standaardwaarde **Standard** staan. |
    | Soort account | Laat de standaardwaarde **Storage (algemeen gebruik v2)** staan. |
    | Replicatie| Laat de standaard **Geografisch redundante opslag met leestoegang (RA-GRS)** staan |
   
3. Selecteer het tabblad **Netwerken** of selecteer de **Volgende: Knop Netwerken**.

4. Selecteer op het tabblad **Netwerken** onder **Verbindingsmethode** de optie **privé-eindpunt**.

5. Selecteer **+ Toevoegen** onder **Privé-eindpunt**.

6. Voer de volgende gegevens in of selecteer deze onder **Privé-eindpunt maken**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | Locatie | Selecteer **VS - oost** |
    | Naam | Voer **myPrivateEndpoint** |
    | Opslagsubresource | Laat de standaard **blob** staan. |
    | **Netwerken** |  |
    | Virtueel netwerk | **myVNet** selecteren |
    | Subnet | Selecteer **mySubnet** |
    | **Privé-DNS-integratie** |
    | Integreren met privé-DNS-zone | Laat de standaard **Ja** staan. |
    | Privé-DNS-zone | Laat de standaard (Nieuwe) privatelink.blob.core.windows.net staan |

7. Selecteer **OK**.

8. Selecteer **Controleren + maken**.

9. Selecteer **Maken**.

10. Selecteer **Resourcegroepen** in het linkernavigatievenster.

11. Selecteer **myResourceGroup**.

12. Selecteer het opslagaccount dat u in de vorige stappen hebt gemaakt.

13. In de sectie **Instellingen** van de opslagaccounts selecteert u **Toegangssleutels**.

14. Selecteer kopiëren voor de **Verbindingsreeks** voor **key1**.

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt om verbinding te maken met het opslagaccount op het privé-eindpunt.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

2. Selecteer **myResourceGroup**.

3. Selecteer **myVM**.

4. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

7. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

8. Voer `nslookup <storage-account-name>.blob.core.windows.net` in. Vervang **\<storage-account-name>** door de naam op van het opslagaccount dat u in de eerdere stappen hebt gemaakt.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Er wordt een privé-IP-adres van **10.1.0.5** geretourneerd voor de naam van het opslagaccount.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

9. Installeer [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) op de virtuele machine.

10. Selecteer **Voltooien** nadat **Microsoft Azure Storage Explorer** is geïnstalleerd.  Laat het selectievakje aangevinkt om de toepassing te openen.

11. Selecteer **Een verbindingsreeks gebruiken** gebruiken op het scherm **Verbinding maken met Azure Storage**.

12. Selecteer **Next**.

13. Voer de naam van uw opslagaccount in uit de vorige stappen in **Weergavenaam**.

14. Plak in het vak onder **Verbindingsreeks** de verbindingsreeks uit het opslagaccount dat u in de vorige stappen hebt gekopieerd.

15. Selecteer **Next**.

16. Controleer of de instellingen juist zijn in **Samenvatting van de verbinding**.  

17. Selecteer **Verbinding maken**.

18. Verbreek de verbinding met **myVM**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan het virtueel netwerk, de virtuele machine en het opslagaccount met de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer **myResourceGroup**.

3. Selecteer **Resourcegroep verwijderen**.

4. In **TYPE THE RESOURCE GROUP** voert u **myResourceGroup** in.

5. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Een Private Link-service maken:
> [!div class="nextstepaction"]
> [Een Private Link-service maken](create-private-link-service-portal.md)