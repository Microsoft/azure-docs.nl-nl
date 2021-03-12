---
title: Azure SQL-VM-verbinding voor zoek indexering
titleSuffix: Azure Cognitive Search
description: Schakel versleutelde verbindingen in en configureer de firewall om verbindingen met SQL Server op een virtuele Azure-machine (VM) toe te staan vanuit een Indexeer functie op Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: dce4c41d0d6f15ac9dc33e687c9a5ac7b7b96e06
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200787"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Een verbinding van een Azure Cognitive Search Indexeer functie configureren om te SQL Server op een Azure VM

Zoals beschreven in het [verbinden van Azure SQL database met azure Cognitive Search met Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), wordt het maken van Indexeer functies op basis **van SQL Server op Azure-vm's** (of **SQL Azure vm's** voor kort) ondersteund door Azure Cognitive Search, maar er zijn een paar aan beveiligings vereisten verbonden voor waarden die u eerst moet uitvoeren. 

Verbindingen van Azure Cognitive Search naar SQL Server op een VM is een open bare Internet verbinding. Alle beveiligings maatregelen die u normaal gesp roken uitvoert, zijn hier ook van toepassing:

+ Verkrijg een certificaat van een [certificerings instantie provider](https://en.wikipedia.org/wiki/Certificate_authority#Providers) voor de Fully Qualified Domain name van de SQL Server instantie op de virtuele Azure-machine.
+ Installeer het certificaat op de VM en configureer vervolgens versleutelde verbindingen op de VM met behulp van de instructies in dit artikel.

## <a name="enable-encrypted-connections"></a>Versleutelde verbindingen inschakelen
Voor Azure Cognitive Search is een versleuteld kanaal vereist voor alle indexer aanvragen via een open bare Internet verbinding. In deze sectie vindt u de stappen voor het maken van dit werk.

1. Controleer de eigenschappen van het certificaat om te controleren of de onderwerpnaam de Fully Qualified Domain Name (FQDN) van de virtuele Azure-machine is. U kunt een hulp programma gebruiken zoals CertUtil of de module Certificaten om de eigenschappen weer te geven. U kunt de FQDN-naam ophalen uit de sectie belangrijkste onderdelen van de VM-service in het veld **openbaar IP-adres/DNS-label** in het [Azure Portal](https://portal.azure.com/).
   
   * Voor virtuele machines die zijn gemaakt met de nieuwere **Resource Manager** -sjabloon, wordt de FQDN-indeling `<your-VM-name>.<region>.cloudapp.azure.com`
   * Voor oudere Vm's die zijn gemaakt als een **klassieke** virtuele machine, wordt de FQDN-indeling als `<your-cloud-service-name.cloudapp.net>` .

2. Configureer SQL Server om het certificaat te gebruiken met behulp van de REGI ster-editor (regedit). 
   
    Hoewel SQL Server Configuration Manager vaak wordt gebruikt voor deze taak, kunt u dit niet gebruiken voor dit scenario. Het geïmporteerde certificaat wordt niet gevonden omdat de FQDN-naam van de virtuele machine in azure niet overeenkomt met de FQDN die door de virtuele machine wordt bepaald (het domein wordt aangeduid als de lokale computer of het netwerk domein waarmee het is verbonden). Wanneer namen niet overeenkomen, gebruikt u regedit om het certificaat op te geven.
   
   * In Regedit, bladert u naar deze register sleutel: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` .
     
     Het `[MSSQL13.MSSQLSERVER]` deel is afhankelijk van versie en exemplaar naam. 
   * Stel de waarde van de **certificaat** sleutel in op de **vinger afdruk** van het TLS/SSL-certificaat dat u in de virtuele machine hebt geïmporteerd.
     
     Er zijn verschillende manieren om de vinger afdruk te verkrijgen, wat beter is dan andere. Als u het kopieert vanuit de module **certificaten** in MMC, haalt u waarschijnlijk een onzichtbare voorloop tekens op [zoals beschreven in dit ondersteunings artikel](https://support.microsoft.com/kb/2023869/), wat resulteert in een fout wanneer u een verbinding probeert te maken. Er zijn verschillende tijdelijke oplossingen voor het oplossen van dit probleem. Het eenvoudigste is om op BACKSPACE te drukken en vervolgens het eerste teken van de vinger afdruk opnieuw te typen om het voorloop teken te verwijderen uit het veld sleutel waarde in Regedit. U kunt ook een ander hulp programma gebruiken om de vinger afdruk te kopiëren.

3. Machtigingen verlenen aan het service account. 
   
    Zorg ervoor dat aan het SQL Server-service account de juiste machtigingen worden verleend voor de persoonlijke sleutel van het TLS/SSL-certificaat. Als u deze stap verkrijgt, wordt SQL Server niet gestart. U kunt de module **certificaten** of **certutil** voor deze taak gebruiken.
    
4. Start de SQL Server-service opnieuw.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server connectiviteit in de VM configureren
Nadat u de versleutelde verbinding hebt ingesteld die is vereist voor Azure Cognitive Search, zijn er extra configuratie stappen ingebouwd in SQL Server op Azure-Vm's. Als u dit nog niet hebt gedaan, is de volgende stap het volt ooien van de configuratie met behulp van een van deze artikelen:

* Zie [verbinding maken met een virtuele machine van SQL Server in azure met behulp van Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)voor een **Resource Manager** -VM. 
* Zie [verbinding maken met een virtuele machine van SQL Server op Azure Classic](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)voor een **klassieke** VM.

Bekijk in het bijzonder de sectie in elk artikel voor ' verbinding maken via internet '.

## <a name="configure-the-network-security-group-nsg"></a>De netwerk beveiligings groep (NSG) configureren
Het is niet ongebruikelijk om de NSG en de bijbehorende Azure-eind punt of Access Control lijst (ACL) te configureren om uw Azure-VM toegankelijk te maken voor andere partijen. U hebt dit gedaan voordat u uw eigen toepassings logica kunt koppelen aan uw SQL Azure-VM. Het is geen verschil voor een Azure Cognitive Search-verbinding met uw SQL Azure VM. 

De onderstaande koppelingen bieden instructies voor de NSG-configuratie voor VM-implementaties. Gebruik deze instructies om een Azure Cognitive Search-eind punt te ACL op basis van het IP-adres.

> [!NOTE]
> Zie [Wat is een netwerk beveiligings groep?](../virtual-network/network-security-groups-overview.md) voor achtergrond informatie.
> 
> 

* Zie [nsg's maken voor arm-implementaties](../virtual-network/tutorial-filter-network-traffic.md)voor een **Resource Manager** -VM. 
* Zie [nsg's maken voor klassieke implementaties](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)voor een **klassieke** virtuele machine.

IP-adres Sering kan enkele uitdagingen opleveren die eenvoudig kunnen worden verholpen als u op de hoogte bent van het probleem en mogelijke oplossingen. De overige secties bevatten aanbevelingen voor het verwerken van problemen met betrekking tot IP-adressen in de ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Toegang tot de Azure-Cognitive Search beperken
We raden u ten zeerste aan de toegang tot het IP-adres van de zoek service en het IP-adres bereik van de `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) in de ACL te beperken, in plaats van uw SQL Azure-vm's open te maken voor alle verbindings aanvragen.

U kunt het IP-adres vinden door de FQDN-namen (bijvoorbeeld `<your-search-service-name>.search.windows.net` ) van uw zoek service te pingen. Hoewel het mogelijk is dat het IP-adres van de zoek service kan worden gewijzigd, is het onwaarschijnlijk dat het wordt gewijzigd. Het IP-adres is doorgaans statisch voor de levens duur van de service.

U kunt het IP-adres bereik van de `AzureCognitiveSearch` [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) van de service opvragen door [Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) te gebruiken of via de [service tag discovery-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Het IP-adres bereik wordt wekelijks bijgewerkt.

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>De IP-adressen van de Azure Cognitive Search-Portal toevoegen
Als u de Azure Portal gebruikt om een Indexeer functie te maken, moet Azure Cognitive Search-Portal-logica ook toegang hebben tot uw SQL Azure VM tijdens de aanmaak tijd. IP-adressen van Azure Cognitive Search-Portal kunnen worden gevonden door te pingen `stamp2.search.ext.azure.com` .

## <a name="next-steps"></a>Volgende stappen
Met configuratie uit de weg kunt u nu een SQL Server op de Azure-VM opgeven als de gegevens bron voor een Azure Cognitive Search indexer. Zie [verbinding maken tussen Azure SQL database en Azure Cognitive Search met behulp van Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.