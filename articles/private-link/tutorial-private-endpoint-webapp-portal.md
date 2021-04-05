---
title: 'Zelfstudie: Verbinding maken met een web-app met behulp van een privé-eindpunt in Azure'
titleSuffix: Azure Private Link
description: Ga aan de slag met deze zelfstudie en gebruik een privé-eindpunt in Azure om een privé verbinding te maken met een web-app.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896970"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Zelfstudie: Verbinding maken met een web-app met behulp van een privé-eindpunt in Azure

Een privé-eindpunt in Azure is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een Bastion-host maken
> * Hiermee maakt u een virtuele machine.
> * Een web-app maken.
> * Een privé-eindpunt maken.
> * Connectiviteit met privé-eindpunt van web-app testen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!Note]
> Privé-eindpunten zijn beschikbaar in openbare regio’s voor de prijscategorieën PremiumV2 en PremiumV3, voor web-apps in Windows en Linux, en voor het Azure Functions Premium-abonnement (soms het Elastische Premium-abonnement genoemd). 

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
    | Regio           | Selecteer **Europa - west** |

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
    | Region | Selecteer **Europa - west** |
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

## <a name="create-web-app"></a>Een web-app maken

In deze sectie maakt u een web-app.

1. Selecteer in het menu aan de linkerkant de opties **Een resource maken** > **Opslag** > **Web-app**, of zoek naar **Web-app** in het zoekvak.

2. Typ of selecteer op het tabblad **Basisbeginselen** van **Web-app maken** de volgende informatie:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam | Voer **mywebapp** in. Als de naam niet beschikbaar is, voert u een unieke naam in. |
    | Publiceren | Selecteer **Code**. |
    | Runtimestack | Selecteer **.NET Core 3.1 (LTS)** . |
    | Besturingssysteem | Selecteer **Windows**. |
    | Regio | Selecteer **Europa - west** |
    | **App Service Plan** |  |
    | Windows Plan (Europa - west) | Selecteer **Nieuw maken**. </br> Voer **myServicePlan** in bij **Naam**. |
    | SKU en grootte | Selecteer **Formaat wijzigen**. </br> Selecteer **P2V2** in het scherm **Specificatie kiezen**. </br> Selecteer **Toepassen**. |
   
3. Selecteer **Controleren + maken**.

4. Selecteer **Maken**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Tabblad Basisbeginselen van Web-app maken in Azure Portal." border="true":::

## <a name="create-private-endpoint"></a>Privé-eindpunt maken

1. Selecteer in het menu aan de linkerkant de opties **Alle resources** > **mywebapp**, of de naam die u hebt gekozen tijdens het maken.

2. Selecteer in het overzicht van de web-app de opties **Instellingen** > **Netwerken**.

3. Selecteer in **Netwerken** de optie **Uw privé-eindpuntverbindingen configureren**.

4. Selecteer **+ Toevoegen** op het scherm **Privé-eindpuntverbindingen**.

5. Typ of selecteer in het scherm **Privé-eindpunt toevoegen** de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **mywebappendpoint** in. |
    | Abonnement | Selecteer uw abonnement. |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet**. |
    | Integreren met privé-DNS-zone | Selecteer **Ja**. |

6. Selecteer **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt, om verbinding te maken met de web-app via het privé-eindpunt.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

2. Selecteer **myResourceGroup**.

3. Selecteer **myVM**.

4. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

7. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

8. Voer `nslookup <webapp-name>.azurewebsites.net` in. Vervang **\<webapp-name>** door de naam van de web-app die u in de voorgaande stappen hebt gemaakt.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Het privé-IP-adres **10.1.0.5** geretourneerd voor de naam van de web-app.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

9. Open een webbrowser op de lokale computer en voer de externe URL van uw web-app in, **https://\<webapp-name>.azurewebsites.net**.

10. Controleer of u een **403**-pagina ontvangt. Deze pagina geeft aan dat de web-app niet extern toegankelijk is.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="403-pagina voor het externe adres van de web-app." border="true":::

11. Open Internet Explorer in de bastionverbinding met **myVM**.

12. Voer de URL van de web-app in: **https://\<webapp-name>.azurewebsites.net**.

13. Controleer of de standaardpagina van de web-app wordt weergegeven.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Standaardpagina van web-app." border="true":::

18. Verbreek de verbinding met **myVM**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan het virtueel netwerk, de virtuele machine en de web-app met de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer **myResourceGroup**.

3. Selecteer **Resourcegroep verwijderen**.

4. In **TYPE THE RESOURCE GROUP** voert u **myResourceGroup** in.

5. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Een Private Link-service maken:
> [!div class="nextstepaction"]
> [Een Private Link-service maken](create-private-link-service-portal.md)
