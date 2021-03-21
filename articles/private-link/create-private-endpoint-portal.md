---
title: 'Quickstart: een privé-eindpunt maken met behulp van Azure Portal'
description: Gebruik deze quickstart om te leren hoe u een privé-eindpunt kunt maken met behulp van Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018066"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Quickstart: Een privé-eindpunt maken met behulp van Azure Portal

Ga aan de slag met Azure Private Link door een privé-eindpunt te gebruiken om veilig verbinding te maken met een Azure-web-app.

In deze quickstart maakt u een privé-eindpunt voor een Azure-web-app en implementeert u een virtuele machine om de privé-verbinding te testen.  

Er kunnen privé-eindpunten worden gemaakt voor verschillende soorten Azure-services, zoals Azure SQL en Azure Storage.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een Azure-web-app met een **PremiumV2**- of hoger app-serviceplan geïmplementeerd in uw Azure-abonnement.  
    * Zie voor meer informatie en een voorbeeld [Quickstart: Een ASP.NET Core-web-app maken in Azure](../app-service/quickstart-dotnetcore.md). 
    * Zie voor een gedetailleerde zelfstudie over het maken van een web-app en een eindpunt [Zelfstudie: Verbinding maken met een web-app met behulp van een privé-eindpunt in Azure](tutorial-private-endpoint-webapp-portal.md).

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
    | Resourcegroep   | Selecteer **CreatePrivateEndpointQS-rg** |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteer **Europa - west**.|

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
    | Resourcegroep | Selecteer **CreatePrivateEndpointQS-rg** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Open **myVM** |
    | Region | Selecteer **Europa - west**. |
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

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

In deze sectie maakt u een privé-eindpunt voor de web-app die u in de sectie met vereisten hebt gemaakt.

1. Selecteer in de linkerbovenhoek van het scherm in de portal **Een resource maken** > **Netwerken** > **Private Link**, of typ **Private Link** in het zoekvak.

2. Selecteer **Maken**.

3. Selecteer in **Private link Center** **Privé-eindpunt** in het menu aan de linkerkant.

4. Selecteer **+ Toevoegen** onder **Privé-eindpunten**.

5. Typ of selecteer op het tabblad **Basisgegevens** van **Een privé-eindpunt maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **CreatePrivateEndpointQS-rg**. U hebt deze resourcegroep in de vorige sectie gemaakt.|
    | **Exemplaardetails** |  |
    | Naam  | Voer **myPrivateEndpoint** in. |
    | Regio | Selecteer **Europa - west**. |

6. Selecteer het tabblad **Resource** of de knop **Volgende: resource** onderaan de pagina.
    
7. Typ of selecteer in **Resource** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsmethode | Selecteer **Verbinding maken met een Azure-resource in mijn directory**. |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.Web/sites**. |
    | Resource | Selecteer **\<your-web-app-name>**. </br> Selecteer de naam van de web-app die u bij de vereisten hebt gemaakt. |
    | Stel subresource in | Selecteer **sites**. |

8. Selecteer het tabblad **Configuratie** of de knop **Volgende: configuratie** onderaan het scherm.

9. Typ of selecteer in **Configuratie** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerken** |  |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet**. |
    | **Privé-DNS-integratie** |  |
    | Integreren met privé-DNS-zone | Laat de standaardwaarde **Ja** staan. |
    | Abonnement | Selecteer uw abonnement. |
    | Privé-DNS-zones | Laat de standaardwaarde **(New) privatelink.azurewebsites.net** staan.
    

13. Selecteer **Controleren + maken**.

14. Selecteer **Maken**.

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt, om verbinding te maken met de web-app via het privé-eindpunt.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

2. Selecteer **CreatePrivateEndpointQS-rg**.

3. Selecteer **myVM**.

4. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

7. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

8. Voer `nslookup <your-webapp-name>.azurewebsites.net` in. Vervang **\<your-webapp-name>** door de naam van de web-app die u in de voorgaande stappen hebt gemaakt.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Het privé-IP-adres **10.1.0.5** geretourneerd voor de naam van de web-app.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

11. Open Internet Explorer in de bastionverbinding met **myVM**.

12. Voer de URL van de web-app in: **https://\<your-webapp-name>.azurewebsites.net**.

13. U ziet de standaardpagina voor web-apps als uw toepassing niet is geïmplementeerd:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standaardpagina van web-app." border="true":::

18. Verbreek de verbinding met **myVM**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan het virtueel netwerk, de virtuele machine en de web-app met de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer **CreatePrivateEndpointQS-rg**.

3. Selecteer **Resourcegroep verwijderen**.

4. Voer **CreatePrivateEndpointQS-rg** in bij **TYP DE NAAM VAN DE RESOURCEGROEP**.

5. Selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* Een virtueel netwerk en Bastion-host.
* Een virtuele machine.
* Een privé-eindpunt voor een Azure-web-app.

U hebt de virtuele machine gebruikt voor het veilig testen van de connectiviteit van de web-app via het privé-eindpunt.



Zie voor meer informatie over de services die een privé-eindpunt ondersteunen:
> [!div class="nextstepaction"]
> [Beschikbaarheid van Private Link](private-link-overview.md#availability)
