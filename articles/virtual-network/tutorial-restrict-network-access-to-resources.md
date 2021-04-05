---
title: Toegang tot PaaS-resources beperken - zelfstudie - Azure Portal
description: In deze zelfstudie leert u hoe u de toegang tot Azure resources, zoals Azure Storage en Azure SQL Database, met service-eindpunten voor een virtueel netwerk kunt begrenzen en beperken met behulp van de Azure-portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368242"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Zelfstudie: Netwerktoegang tot PaaS-resources beperken met service-eindpunten voor een virtueel netwerk met behulp van de Azure-portal

Met service-eindpunten voor virtuele netwerken kunt u de netwerktoegang tot sommige Azure-servicebronnen beperken tot een subnet van een virtueel netwerk. U kunt ook internettoegang tot de resources verwijderen. Service-eindpunten zorgen voor een rechtstreekse verbinding van uw virtuele netwerk met ondersteunde Azure-services, zodat u de privéadresruimte van uw virtuele netwerk kunt gebruiken voor toegang tot de Azure-services. Verkeer dat bestemd is voor Azure-resources via de service-eindpunten blijft altijd op het Microsoft Azure-backbone-netwerk. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken met één subnet
> * Een subnet toevoegen en een service-eindpunt inschakelen
> * Een Azure-resource maken en alleen toegang ertoe toestaan vanaf een subnet
> * Een virtuele machine (VM) implementeren op elk subnet
> * Toegang tot een resource vanaf een subnet bevestigen
> * Bevestigen dat toegang wordt geweigerd aan een resource vanaf een subnet en internet

U kunt deze zelfstudie desgewenst volgen met behulp van de [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) of [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Netwerken** en selecteer vervolgens **Virtuele netwerken**.
3. Klik op **+ Toevoegen** en voer de volgende informatie in: 

   |Instelling|Waarde|
   |----|----|
   |Abonnement| Selecteer uw abonnement|
   |Resourcegroep | Selecteer **Nieuwe maken** en voer *myResourceGroup* in.|
   |Naam| Voer *myVirtualNetwork* in |
   |Regio| Selecteer **(US) VS - oost** |

   ![Basisinformatie over uw virtuele netwerk invoeren](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Klik op **Next: IP-adressen >**
   
   |Instelling|Waarde|
   |----|----|
   |IPv4-adresruimte| Laat de standaardwaarden staan |
   |Subnetnaam| Klik op **standaard** en wijzig de naam van 'standaard' in 'openbaar'|
   |Subnetadresbereik| Laat de standaardwaarden staan|

5. Klik op **Next: Beveiliging >** 
   
   |Instelling|Waarde|
   |----|----|   
   |BastionHost| Uitschakelen|
   |DDoS-bescherming| Uitschakelen|
   |Firewall| Uitschakelen|

6. Wanneer u klaar bent, klikt u op **Controleren en maken**.
7. Als de validatiecontroles zijn geslaagd, klikt u op **Maken**.
8. Wacht tot de implementatie is voltooid. Klik vervolgens op **Ga naar resource** of ga naar de volgende sectie. 

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen

Service-eindpunten worden ingeschakeld per service, per subnet. Een subnet maken en een service-eindpunt voor het subnet inschakelen:

1. Als u zich nog niet op de pagina van het virtuele netwerk bevindt, kunt u het zojuist gemaakte netwerk zoeken in het vak **Resources, services en documenten zoeken** bovenaan de portal. Voer *myVirtualNetwork* in en selecteert dit in de lijst.
2. Selecteer in het menu **Instellingen** (links) de optie **Subnetten**, en selecteer vervolgens **+ Subnet**, zoals weergegeven:

    ![Subnet toevoegen](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Selecteer onder **Subnet toevoegen** de volgende informatie of voer deze in, en selecteer **OK**:

    |Instelling|Waarde|
    |----|----|
    |Naam| Persoonlijk |
    |Adresbereik| Laat de standaardwaarden staan|
    |Service-eindpunten| Selecteer **Microsoft.Storage**|
    |Beleid voor service-eindpunten | Laat de standaardwaarden staan op 0 |

> [!CAUTION]
> Zie [Subnetinstellingen wijzigen](virtual-network-manage-subnet.md#change-subnet-settings) voordat u een servicepunt voor een bestaand subnet met resources inschakelt.

4. Klik op **Opslaan** en sluit vervolgens het venster Subnet aan de rechterkant. Het zojuist gemaakte subnet is te zien in de lijst.

## <a name="restrict-network-access-for-a-subnet"></a>Netwerktoegang voor een subnet beperken

Standaard kunnen alle VM-exemplaren in een subnet met alle resources communiceren. U kunt de communicatie naar en van alle resources in een subnet beperken door een netwerkbeveiligingsgroep te maken en deze aan het subnet te koppelen:

1. Selecteer **Alle services** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Netwerken** en selecteer vervolgens (of zoek) **Netwerkbeveiligingsgroepen**.
3. Klik op de pagina **Netwerkbeveiligingsgroepen** op **+ Toevoegen**.
4. Voer de volgende gegevens in 

    |Instelling|Waarde|
    |----|----|
    |Abonnement| Selecteer uw abonnement|
    |Resourcegroep | Selecteer *myResourceGroup* in de lijst|
    |Naam| Voer **myNsgPrivate** in |
    |Locatie| Selecteer **VS - oost** |

5. Selecteer **Controleren en maken** en klik wanneer de validatie is voltooid op **Maken**.
6. Nadat de netwerkbeveiligingsgroep is gemaakt, klikt u op **Ga naar resource** of zoekt u naar *myNsgPrivate*.
7. Selecteer onder **Instellingen** aan de linkerkant de optie **Uitgaande beveiligingsregels**.
8. Selecteer **+ Toevoegen**.
9. Maak een regel die uitgaande communicatie naar de Azure Storage-service toestaat. Voer de volgende gegevens in of selecteer deze, en selecteer **Toevoegen**:

    |Instelling|Waarde|
    |----|----|
    |Bron| Selecteer **VirtualNetwork** |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **Servicetag**|
    |Doelservicetag | Selecteer **Storage**|
    |Poortbereiken van doel| Laat de standaardwaarden staan op *8080* |
    |Protocol|Elk|
    |Actie|Toestaan|
    |Prioriteit|100|
    |Naam|Wijzig de naam in **Allow-Storage-All**|

10. Maak een uitgaande beveiligingsregel die communicatie naar internet weigert. Deze regel overschrijft een standaardregel in alle netwerkbeveiligingsgroepen waarmee uitgaande internetcommunicatie mogelijk is. Voer stap 6-9 hierboven uit met behulp van de volgend waarden:

    |Instelling|Waarde|
    |----|----|
    |Bron| Selecteer **VirtualNetwork** |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **Servicetag**|
    |Doelservicetag| Selecteer **Internet**|
    |Poortbereiken van doel| * |
    |Protocol|Elk|
    |Actie|**Wijzig de standaardwaarde in *Weigeren*** |
    |Prioriteit|110|
    |Name|Wijzig in *Deny-Internet-All*|

11. Maak een *inkomende beveiligingsregel* waarmee RDP-verkeer (Remote Desktop Protocol) naar het subnet vanaf elke locatie wordt toegestaan. De regel overschrijft een standaardbeveiligingsregel waardoor al het inkomende verkeer van internet wordt geweigerd. Externe bureaublad-verbindingen worden toegestaan voor het subnet zodat de verbinding in een later stadium kan worden getest. 
12. Selecteer onder **Instellingen** **Inkomende beveiligingsregels**.
13. Selecteer **+ Toevoegen** en gebruik de volgende waarden:

    |Instelling|Waarde|
    |----|----|
    |Bron| Elk |
    |Poortbereiken van bron| * |
    |Doel | Selecteer **VirtualNetwork**|
    |Poortbereiken van doel| Wijzig in *3389* |
    |Protocol|Elk|
    |Actie|Toestaan|
    |Prioriteit|120|
    |Name|Wijzig in *Allow-RDP-All*|

   >[!WARNING] 
   > RDP-poort 3389 wordt beschikbaar voor internet. Dit wordt alleen aanbevolen voor testen. Voor *Productieomgevingen* raden we u aan een VPN-verbinding of particuliere verbinding te gebruiken.

1.  Selecteer onder **Instellingen** de optie **Subnetten**.
2.  Klik op **Koppelen**.
3.  Selecteer **myVirtualNetwork** onder **Virtueel netwerk**.
4.  Selecteer **Privé** onder **Subnet**, en selecteer vervolgens **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Netwerktoegang tot een resource beperken

De stappen die vereist zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van deze zelfstudie bevat stappen voor het beperken van netwerktoegang voor een Azure Storage-account, als voorbeeld.

### <a name="create-a-storage-account"></a>Create a storage account

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Voer in de zoekbalk in 'Opslagaccount', en selecteer het in de vervolgkeuzelijst.
3. Klik op **+ Toevoegen**.
4. Voer de volgende informatie in:

    |Instelling|Waarde|
    |----|----|
    |Abonnement| Selecteer uw abonnement|
    |Resourcegroep| Selecteer *myResourceGroup*.|
    |Naam van opslagaccount| Voer een naam die uniek is voor alle Azure locaties, 3 tot 24 tekens lang is en alleen cijfers en kleine letters bevat.|
    |Locatie| Selecteer **(US) VS - oost** |
    |Prestaties|Standard|
    |Soort account| StorageV2 (algemeen v2)|
    |Replicatie| Lokaal redundante opslag (LRS)|

5. Selecteer **Controleren en maken** en klik wanneer de validatie is voltooid op **Maken**. 

>[!NOTE] 
> Het uitvoeren van de implementatie kan enkele minuten duren.

6. Nadat het opslagaccount is gemaakt, klik u op **Ga naar resource**

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

1. Ga naar de overzichtspagina voor uw opslagaccount.
2. Selecteer het app-pictogram **Bestandsshares**. Klik vervolgens op **+ Bestandsshare**.

    |Instelling|Waarde|
    |----|----|
    |Naam| my-file-share|
    |Quota| Instellen op maximum |

   ![Storage-account](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Klik op **Maken**.
4. De bestandsshare wordt weergegeven in het Azure-venster. Als dit niet het geval is, klikt u op **Vernieuwen**

### <a name="restrict-network-access-to-a-subnet"></a>Netwerktoegang tot een subnet beperken

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk, inclusief internet. U kunt de netwerktoegang vanaf internet en voor alle andere subnetten in alle virtuele netwerken beperken. (Behalve voor het *Privé* subnet in het virtuele netwerk *myVirtualNetwork*.) Netwerktoegang tot een subnet beperken:

1. Selecteer onder **Instellingen** voor het opslagaccount (met een unieke naam) de optie **Netwerk**.
2. Selecteer **Geselecteerde netwerken**.
3. Selecteer **+ Bestaand virtueel netwerk toevoegen**.
4. Selecteer onder **Netwerken toevoegen** de volgende waarden en selecteer **Toevoegen**:

    |Instelling|Waarde|
    |----|----|
    |Abonnement| Selecteer uw abonnement|
    |Virtuele netwerken| **myVirtualNetwork**|
    |Subnetten| **Privé**|

    ![Schermopname van het deelvenster Netwerken toevoegen, waarin u de opgegeven waarden kunt invoeren.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Klik op **Toevoegen** en klik vervolgens onmiddellijk op het pictogram **Opslaan** om de wijzigingen op te slaan.
6. Kies onder **Instellingen** de optie **Toegangssleutels** voor het opslagaccount, zoals in de volgende afbeelding:

      ![Schermopname waarop Toegangssleutels is geselecteerd in Instellingen, waar u een sleutel kunt verkrijgen.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Klik op **Sleutels weergeven** en noteer de waarde bij **Sleutel**. U moet key1 op een latere stap handmatig invoeren bij het toewijzen van de bestandsshare aan een stationsletter op een VM.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Implementeer een VM in elk subnet om de netwerktoegang tot een opslagaccount te testen.

### <a name="create-the-first-virtual-machine"></a>De eerste virtuele machine maken

1. Zoek op de balk Resources zoeken. . ." naar **Virtuele machines**.
2. Selecteer **+ Toevoegen > Virtuele machine**. 
3. Voer de volgende gegevens in:

   |Instelling|Waarde|
   |----|----|
   |Abonnement| Selecteer uw abonnement|
   |Resourcegroep| Selecteer **myResourceGroup, die eerder is gemaakt.|
   |Naam van de virtuele machine| Voer *myVmPublic* in|
   |Regio | (US) US - oost
   |Beschikbaarheidsopties| Beschikbaarheidszone|
   |Beschikbaarheidszone | 1 |
   |Installatiekopie | Windows Server 2019 Datacenter - Gen 1 |
   |Grootte | Selecteer de grootte voor het VM-exemplaar dat u wilt gebruiken |
   |Gebruikersnaam|Voer een gebruikersnaam naar keuze in.|
   |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Openbare poorten voor inkomend verkeer | Geselecteerde poorten toestaan |
   |Binnenkomende poorten selecteren | Laat de standaardwaarde ingesteld op *RDP (3389)* |

   ![Een virtueel netwerk selecteren](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Selecteer het tabblad **Netwerken** en selecteer vervolgens **myVirtualNetwork**. 
5. Selecteer het *Openbare* subnet.
6. Selecteer onder **NIC-netwerkbeveiligingsgroep** de optie **Geavanceerd**. De portal maakt automatisch een netwerkbeveiligingsgroep voor u waarin poort 3389 is toegestaan, die u later moeten openen om verbinding te maken met de virtuele machine. 

   ![Basisinformatie invoeren over een virtuele machine](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Selecteer **Controleren en maken** en vervolgens **Maken**. Wacht tot de implementatie is voltooid.
8. Klik op **Ga naar resource**, of open de pagina **Start > Virtuele machines** en selecteer de VM die u zojuist hebt gemaakt: *myVmPublic*. Deze wordt nu gestart.

### <a name="create-the-second-virtual-machine"></a>De tweede virtuele machine maken

1. Voer stap 1-8 nogmaals uit, maar geef in stap 3 de virtuele machine *myVmPrivate* een naam, en stel **Openbare inkomende poort** in op Geen. 
2. Selecteer in stap 4 en 5 de optie **Privé** subnet.

>[!NOTE]
> De instellingen **NIC-netwerkbeveiligingsgroep** en **Openbare inkomende poort** moeten eruit zien zoals in de onderstaande afbeelding, inclusief het blauwe bevestigingsvenster waarin staat: 'al het openbare internetverkeer wordt standaard geblokkeerd'.

   ![Een privé-VM maken](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Selecteer **Controleren en maken** en vervolgens **Maken**. Wacht tot de implementatie is voltooid. 

>[!WARNING]
> Ga niet door met de volgende stap totdat de implementatie is voltooid.

4. Wacht tot hieronder het bevestigingsvenster wordt weergegeven en klik op **Ga naar resource**.

   ![Bevestigingsvenster Een privé-VM maken](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

1. Wanneer de VM *myVmPrivate* is gemaakt, klikt u op **Ga naar resource**. 
2. Maak verbinding met de VM door **Verbinding maken > RDP** te selecteren.
3. Nadat u de knop **Verbinding maken** hebt geselecteerd, wordt er een RDP-bestand (Remote Desktop Protocol) gemaakt. Klik op **RDP-bestand downloaden** om het bestand te downloaden op de computer.  
4. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM. Voer voor het e-mailveld de referenties Beheerdersaccount: gebruikersnaam in die u eerder hebt opgegeven. 
5. Selecteer **OK**.
6. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding. U ziet nu dat de VM wordt gestart zoals weergegeven:

   ![Actieve privé-VM weergeven](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. Open in het VM-venster een PowerShell CLI-exemplaar.
8. Wijs met behulp van het onderstaande script de Azure-bestandsshare toe aan station Z, met behulp van PowerShell. Voordat u de opdrachten die volgen uitvoert, vervangt u `<storage-account-key>` en beide velden `<storage-account-name>` door waarden die u eerder hebt opgegeven en opgehaald in [Een opslagaccount maken](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell retourneert uitvoer die er ongeveer zo uitziet als in dit voorbeeld:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   De Azure-bestandsshare is toegewezen aan station Z.

9.   Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

1. Voer in het vak **Resources, services en documenten zoeken** bovenaan de portal *myVmPublic* in.
2. Wanneer **myVmPublic** wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Voltooi de stappen 1-8 hierboven in [Toegang tot opslagaccount bevestigen](#confirm-access-to-storage-account) voor de VM *myVmPublic*.

   Na enkele ogenblikken ontvangt u een fout `New-PSDrive : Access is denied`. De toegang wordt geweigerd omdat de VM *myVmPublic* is geïmplementeerd in het *Openbare* subnet. Het *Openbare* subnet heeft geen service-eindpunt voor Azure Storage. Het opslagaccount staat alleen netwerktoegang toe vanuit het *Privé*-subnet, niet het *Openbare* subnet.

4. Sluit de externe bureaubladsessie naar de VM *myVmPublic*.
5. Ga terug naar de Azure-portal en ga naar het opslagaccount met de unieke naam dat u eerder hebt gemaakt.
6. Selecteer onder Bestandsservice bij **Bestandsshares** de *my-file-share* die u eerder hebt gemaakt.
7. U ontvangt het volgende foutbericht:

   ![Fout: Toegang geweigerd](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> De toegang wordt geweigerd omdat de computer zich niet in het *Privé*-subnet van het virtuele netwerk *MyVirtualNetwork* bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet langer nodig hebt, verwijdert u de resourcegroep en alle resources die deze bevat:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een service-eindpunt voor een subnet van een virtueel netwerk ingeschakeld. U hebt geleerd dat u service-eindpunten kunt inschakelen voor vanaf meerdere Azure-services geïmplementeerde resources. U hebt een Azure Storage-account gemaakt en netwerktoegang tot het opslagaccount beperkt tot alleen resources binnen een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunten [Overzicht voor service-eindpunten](virtual-network-service-endpoints-overview.md) en [Subnetten beheren](virtual-network-manage-subnet.md).

Als u meerdere virtuele netwerken in uw account hebt, kunt u twee virtuele netwerken met elkaar verbinden zodat de resources in beide virtuele netwerken met elkaar kunnen communiceren. Ga verder met de volgende zelfstudie om te leren hoe u virtuele netwerken met elkaar kunt verbinden.

> [!div class="nextstepaction"]
> [Virtuele netwerken met elkaar verbinden](./tutorial-connect-virtual-networks-portal.md)
