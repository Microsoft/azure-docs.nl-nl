---
title: Integreren met de Azure Private Link-service
description: Leren hoe Azure Key Vault te integreren met Azure Private Link-service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749529"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Sleutelkluis integreren met Azure Private Link

Met Azure Private Link hebt u via een privé-eindpunt in uw virtuele netwerk toegang tot Azure-services (bijvoorbeeld Azure Key Vault, Azure Storage en Azure Cosmos DB) en in Azure gehoste services van klanten of partners.

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Zie [Wat is een Azure Private Link?](../../private-link/private-link-overview.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u een sleutelkluis met een Azure Private Link wilt integreren, hebt u het volgende nodig:

- Een sleutelkluis.
- Een Azure Virtual Network.
- Een subnet binnen het virtueel netwerk.
- Eigenaar- of inzendermachtigingen voor zowel de sleutelkluis als het virtueel netwerk.

Uw privé-eindpunt en het virtueel netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het privé-eindpunt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. Uw sleutelkluis kan zich in een andere regio bevinden.

Uw privé-eindpunt maakt gebruik van een privé IP-adres in uw virtueel netwerk.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Een verbinding met een Private Link tot stand brengen met Key Vault met behulp van de Azure Portal 

Maak eerst een virtueel netwerk aan de hand van de stappen in [Een virtueel netwerk maken met behulp van de Azure Portal](../../virtual-network/quick-create-portal.md)

U kunt vervolgens een nieuwe sleutelkluis maken of een verbinding met een Private Link tot stand brengen met een bestaande sleutelkluis.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Een nieuwe sleutelkluis maken en een verbinding met een Private Link tot stand brengen

U kunt een nieuwe sleutelkluis maken met de [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md).

Nadat u de basisbeginselen van de sleutelkluis hebt geconfigureerd, selecteert u het tabblad Netwerken en voert u de volgende stappen uit:

1. Selecteer het keuzerondje Privé-eindpunt op het tabblad Netwerken.
1. Klik op de knop “+Toevoegen” om een privé-eindpunt toe te voegen.

    ![Schermopname van het tabblad Netwerken op de pagina Sleutelkluis maken.](../media/private-link-service-1.png)
 
1. Selecteer in het veld “Locatie” van de blade Privé-eindpunt maken de regio waarin uw virtueel netwerk zich bevindt. 
1. Maak in het veld “Naam” een beschrijvende naam waarmee dit privé-eindpunt kan worden geïdentificeerd. 
1. Selecteer het virtueel netwerk en het subnet waarvoor u dit privé-eindpunt wilt maken in de vervolgkeuzelijst. 
1. Laat de optie “integreren met de privézone DNS” ongewijzigd.  
1. Selecteer “OK”.

    ![Schermopname van de pagina Privé-eindpunt maken, met geselecteerde instellingen.](../media/private-link-service-8.png)
 
U kunt nu het geconfigureerde privé-eindpunt zien. U hebt nu de mogelijkheid om dit privé-eindpunt te verwijderen en te bewerken. Selecteer de knop “Beoordelen + maken” en maak de sleutelkluis. Het duurt 5-10 minuten voordat de implementatie is voltooid. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Verbinding met een Private Link tot stand brengen met een bestaande sleutelkluis

Als u al een sleutelkluis hebt, kunt u een verbinding met een Private Link maken door de volgende stappen uit te voeren:

1. Meld u aan bij Azure Portal. 
1. Typ “sleutelkluizen” in de zoekbalk
1. Selecteer de sleutelkluis in de lijst waaraan u een privé-eindpunt wilt toevoegen.
1. Selecteer het tabblad “Netwerken” onder Instellingen
1. Selecteer het tabblad Verbindingen met privé-eindpunt boven aan de pagina
1. Selecteer de knop “+ Privé-eindpunt” boven aan de pagina.

    ![Schermopname van de knop + Privé-eindpunt op de pagina Netwerken.](../media/private-link-service-3.png)
    ![Schermopname van het tabblad Basisbeginselen op de pagina Een privé-eindpunt maken (preview).](../media/private-link-service-4.png)

U kunt kiezen voor het maken van een privé-eindpunt voor elke Azure-resource in het gebruik van deze blade. U kunt de vervolgkeuzemenu's gebruiken om een resourcetype te selecteren en een resource in uw directory te selecteren, of u kunt verbinding maken met elke Azure-resource met een bron-id. Laat de optie “integreren met de privézone DNS” ongewijzigd.  

![Schermopname van het toevoegen van een privé-eindpunt met behulp van de huidige blade.](../media/private-link-service-3.png)
![Schermopname met een voorbeeld van de pagina Een privé-eindpunt maken (preview).](../media/private-link-service-4.png)

Wanneer u een privé-eindpunt maakt, moet de verbinding worden goedgekeurd. Als de bron waarvoor u een privé-eindpunt maakt zich in uw directory bevindt, kunt u de verbindingsaanvraag goedkeuren op voorwaarde dat u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die resource uw verbindingsaanvraag heeft goedgekeurd.

Er zijn vier inrichtingsstatussen:

| Actie voor service leveren | Status privé-eindpunt serviceconsument | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en in afwachting van goedkeuring door de resource-eigenaar van de Private Link. |
| Goedkeuren | Goedgekeurd | De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgewezen | De verbinding werd afgewezen door de resource-eigenaar van de private link. |
| Verwijderen | Ontkoppeld | De verbinding is verwijderd door de resource-eigenaar van de private link, het privé-eindpunt wordt informatief en moet worden verwijderd voor opschoning. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Een verbinding met een privé-eindpunt beheren met Key Vault met behulp van de Azure Portal 

1. Meld u aan bij Azure Portal.
1. Typ “sleutelkluizen” in de zoekbalk
1. Selecteer de sleutelkluis die u wilt beheren.
1. Selecteer het tabblad “Netwerken”.
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weergegeven met de inrichtingsstatus “In behandeling”. 
1. Selecteer het privé-eindpunt dat u wilt goedkeuren
1. Selecteer de knop goedkeuren.
1. Als er privé-eindpunt-verbindingen zijn die u wilt afwijzen, ongeacht of het een aanvraag in behandeling of een bestaande verbinding is, selecteert u de verbinding en klikt u op de knop “Afwijzen”.

    ![Installatiekopie](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Een verbinding met een Private Link tot stand brengen met Sleutelkluis met behulp van CLI (initiële installatie)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Een privé-eindpunt maken (automatisch goedkeuren) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Een privé-eindpunt maken (handmatig goedkeuring aanvragen) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Private Link-verbindingen beheren

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Privé-DNS-records toevoegen
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de Private Link werkt

U moet controleren of de resources binnen hetzelfde subnet van het privé-eindpunt verbinding maken met uw sleutelkluis via een privé-IP-adres en dat ze de juiste integratie van de persoonlijke DNS-zone hebben.

Maak eerst een nieuwe virtuele machine door de instructies te volgen in [Een virtuele Windows-machine in de Azure Portal maken](../../virtual-machines/windows/quick-create-portal.md)

In het tabblad “Netwerken”:

1. Specificeer Virtueel netwerk en Subnet. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. Als u een bestaand selecteert, moet u ervoor zorgen dat de regio overeenkomt.
1. Specificeer een openbare IP-resource.
1. Selecteer “Geen” in de “NIC-netwerkbeveiligingsgroep”.
1. Selecteer “Nee” in de “Taakverdeling”.

Voer de volgende opdracht uit via de opdrachtregel:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een sleutelkluis via een openbaar eindpunt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een sleutelkluis via een privé-eindpunt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

* Controleer of het privé-eindpunt is goedgekeurd. 
    1. U kunt dit controleren en oplossen in de Azure-portal. Open de Key Vault-resource en klik op de optie Netwerken. 
    2. Selecteer vervolgens het tabblad Verbindingen met privé-eindpunt. 
    3. Controleer of de verbindingsstatus Goedgekeurd is, en de inrichtingsstatus Geslaagd. 
    4. U kunt ook naar de privé-eindpuntresource navigeren en dezelfde eigenschappen daar bekijken om te controleren of het virtuele netwerk overeenkomt met het virtuele netwerk dat u gebruikt.

* Controleer of u een privé-DNS-zoneresource hebt. 
    1. U moet een privé-DNS-zoneresource hebben met de exacte naam: privatelink.vaultcore.azure.net. 
    2. Zie de volgende koppeling voor meer informatie over het instellen hiervan. [Privé-DNS-zones](../../dns/private-dns-privatednszone.md)
    
* Controleer of de privé-DNS-zone niet is gekoppeld aan het virtuele netwerk. Dit kan het probleem zijn als het openbare IP-adres nog steeds wordt geretourneerd. 
    1. Als de privé-DNS-zone niet is gekoppeld aan het virtuele netwerk, retourneert de DNS-query die afkomstig is van het virtuele netwerk het openbare IP-adres van de sleutelkluis. 
    2. Navigeer naar de privé-DNS-zoneresource in de Azure-portal en klik op de optie Virtuele netwerkkoppelingen. 
    4. Het virtuele netwerk waarmee aanroepen van de sleutelkluis worden uitgevoerd, moet worden weergegeven. 
    5. Als dat niet het geval is, voegt u het toe. 
    6. Zie het volgende document voor gedetailleerde stappen: [Het virtuele netwerk koppelen aan de privé-DNS-zone](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Controleer of in de privé-DNS-zone geen A-record voor de sleutelkluis ontbreekt. 
    1. Navigeer naar de pagina Privé-DNS-zone. 
    2. Klik op Overzicht en controleer of er een A-record is met de eenvoudige naam van uw sleutelkluis (fabrikam). Geef geen achtervoegsel op.
    3. Controleer de spelling en maak of corrigeer de A-record. U kunt een TTL van 3600 (1 uur) gebruiken. 
    4. Zorg ervoor dat u het juiste privé-IP-adres opgeeft. 
    
* Controleer of de A-record het juiste IP-adres heeft. 
    1. U kunt het IP-adres controleren door de resource van het privé-eindpunt te openen in de Azure-portal.
    2. Navigeer naar de resource Microsoft.Network/privateEndpoints in de Azure-portal (niet de resource in de sleutelkluis)
    3. Zoek op de overzichtspagina naar Netwerkinterface en klik op die koppeling. 
    4. De koppeling toont het overzicht van de NIC-resource, met de eigenschap Privé-IP-adres. 
    5. Controleer of dit het juiste IP-adres is dat is opgegeven in de A-record.

## <a name="limitations-and-design-considerations"></a>Beperkingen en overwegingen bij het ontwerp

> [!NOTE]
> Het aantal sleutelkluizen met ingeschakelde privé-eindpunten per abonnement is een aanpasbare limiet. De limiet die hieronder wordt weergegeven, is de standaardlimiet. Als u een hogere limiet wilt aanvragen voor uw service, kunt u een e-mail sturen naar akv-privatelink@microsoft.com. We zullen deze aanvragen per geval goedkeuren.

**Pricing**: Zie [Prijs van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over prijzen.

**Beperkingen**:  Privé-eindpunt voor Azure Key Vault is alleen beschikbaar in openbare Azure-regio's.

**Maximum aantal privé-eindpunten per Sleutelkluis**: 64.

**Standaardaantal sleutelkluizen met privé-eindpunten per abonnement**: 400.

Zie [Azure Private Link-service: beperkingen](../../private-link/private-link-service-overview.md#limitations) voor meer informatie

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](../../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Key Vault](overview.md)
