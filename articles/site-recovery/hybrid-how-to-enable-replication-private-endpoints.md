---
title: Replicatie inschakelen voor on-premises machines met privé-eind punten
description: In dit artikel wordt beschreven hoe u replicatie configureert voor on-premises machines met behulp van privé-eind punten in Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629790"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>On-premises machines repliceren met behulp van privé-eind punten

Met Azure Site Recovery kunt u privé-eind punten van [Azure private link](../private-link/private-endpoint-overview.md) gebruiken om uw on-premises machines te repliceren naar een virtueel netwerk in Azure. Toegang tot een persoonlijk eind punt tot een Recovery kluis wordt ondersteund in alle Azure commerciële & overheids regio's.

In dit artikel wordt beschreven hoe u de volgende stappen uitvoert:

- Maak een Azure Backup Recovery Services kluis om uw computers te beveiligen.
- Schakel een beheerde identiteit in voor de kluis. Ken de vereiste machtigingen toe om toegang te krijgen tot de opslag accounts om de replicatie van verkeer van on-premises naar Azure-doel locaties mogelijk te maken. Beheerde identiteits toegang voor opslag is vereist voor persoonlijke koppelings toegang tot de kluis.

- Breng DNS-wijzigingen aan die vereist zijn voor privé-eind punten.
- Privé-eind punten maken en goed keuren voor een kluis in een virtueel netwerk.
- Maak privé-eind punten voor de opslag accounts. U kunt open bare of firewalld toegang voor opslag als nodig blijven toestaan. Het maken van een persoonlijk eind punt voor toegang tot de opslag is niet vereist voor de Azure Site Recovery.
  
In het volgende diagram ziet u de replicatie werk stroom voor hybride herstel na nood gevallen met persoonlijke eind punten. U kunt geen privé-eind punten maken in uw on-premises netwerk. Als u persoonlijke koppelingen wilt gebruiken, moet u een virtueel Azure-netwerk (een *netwerk overs Laan* in dit artikel) maken, een persoonlijke verbinding tot stand brengen tussen on-premises en het netwerk overs Laan en vervolgens privé-eind punten maken in het netwerk overs Laan. U kunt elke vorm van persoonlijke connectiviteit kiezen.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagram waarin de architectuur voor Azure Site Recovery en privé-eind punten wordt weer gegeven.":::

## <a name="prerequisites-and-caveats"></a>Vereisten en aanvullende opmerkingen

- Persoonlijke koppelingen worden ondersteund in Site Recovery 9,35 en hoger.
- U kunt alleen persoonlijke eind punten maken voor nieuwe Recovery Services kluizen waarvoor geen items zijn geregistreerd. Daarom moet u persoonlijke eind punten maken voordat er items worden toegevoegd aan de kluis. Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link/) voor prijs informatie.
- Wanneer u een persoonlijk eind punt maakt voor een kluis, wordt de kluis vergrendeld. Het kan alleen worden geopend vanuit netwerken met persoonlijke eind punten.
- Azure Active Directory biedt momenteel geen ondersteuning voor persoonlijke eind punten. Daarom moet u uitgaande toegang toestaan vanuit het beveiligde virtuele netwerk van Azure tot IP-adressen en volledig gekwalificeerde domein namen die vereist zijn om Azure Active Directory te kunnen werken in een regio.
  Zoals van toepassing, kunt u ook de code Azure Active Directory van de netwerk beveiligings groep en Azure Firewall-Tags gebruiken om toegang tot Azure Active Directory toe te staan.
- Er zijn vijf IP-adressen vereist in het netwerk overs Laan waar u uw persoonlijke eind punt maakt. Wanneer u een persoonlijk eind punt voor de kluis maakt, maakt Site Recovery vijf persoonlijke koppelingen voor toegang tot de micro Services.
- Er is één aanvullend IP-adres vereist in het netwerk overs laan voor particuliere eind punt connectiviteit met een cache-opslag account. U kunt elke verbindings methode tussen on-premises en het eind punt van uw opslag account gebruiken. U kunt bijvoorbeeld Internet of Azure [ExpressRoute](../expressroute/index.yml)gebruiken. Het tot stand brengen van een persoonlijke koppeling is optioneel. U kunt alleen privé-eind punten maken voor opslag in Algemeen v2-accounts. Zie [prijzen voor Azure page blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/) voor informatie over de prijzen voor gegevens overdracht op algemeen v2-accounts.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Privé-eind punten maken en gebruiken voor site Recovery

 In de volgende secties worden de stappen beschreven die u moet uitvoeren om privé-eind punten te maken en te gebruiken voor site Recovery in uw virtuele netwerken.

> [!NOTE]
> U wordt aangeraden deze stappen in de aangegeven volg orde uit te voeren. Als dat niet het geval is, kunt u mogelijk geen privé-eind punten gebruiken in de kluis en moet u het proces mogelijk opnieuw opstarten met een nieuwe kluis.

### <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis bevat de replicatie gegevens van de machines. Het wordt gebruikt om Site Recovery-bewerkingen te activeren. Zie [een Recovery Services kluis maken](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)voor informatie over het maken van een Recovery Services kluis in de Azure-regio waar u een failover wilt uitvoeren als er sprake is van een nood geval.

### <a name="enable-the-managed-identity-for-the-vault"></a>De beheerde identiteit voor de kluis inschakelen

Met een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) kan de kluis toegang krijgen tot uw opslag accounts. Site Recovery moet mogelijk toegang hebben tot de doel opslag-en cache-en logboek opslag accounts, afhankelijk van uw vereisten. Toegang tot beheerde identiteit is vereist wanneer u de privé koppelings service voor de kluis gebruikt.

1. Ga naar de Recovery Services kluis. Selecteer **identiteit** onder **instellingen**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Scherm opname van de pagina identiteits instellingen.":::

1. Wijzig de **status** in **op aan** en selecteer **Opslaan**.

   Er wordt een object-ID gegenereerd. De kluis is nu geregistreerd bij Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Privé-eind punten maken voor de Recovery Services kluis

Als u de computers in het on-premises bron netwerk wilt beveiligen, hebt u één persoonlijk eind punt voor de kluis in het netwerk overs Laan nodig. Maak het persoonlijke eind punt met behulp van persoonlijk koppelings centrum in de Azure Portal of met behulp van [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Zoek in het zoekvak van Azure Portal op ' persoonlijke koppeling '. Selecteer **persoonlijke koppeling** om naar het persoonlijk koppelings centrum te gaan:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Scherm opname van het zoeken naar het Azure Portal voor privé koppelings centrum.":::

1. Selecteer **privé-eind punten** in het linkerdeel venster. Selecteer op de pagina **privé-eind punten** **toevoegen** om te beginnen met het maken van een persoonlijk eind punt voor uw kluis:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Scherm afbeelding die laat zien hoe u een persoonlijk eind punt maakt in het persoonlijke koppelings centrum.":::

1. Geef op de pagina **een persoonlijk eind punt maken** de details op voor het maken van uw particuliere endpoint-verbinding.

   1. **Basis beginselen**. Geef de basis gegevens op voor uw privé-eind punten. Gebruik de regio die u hebt gebruikt voor het netwerk overs Laan:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Scherm opname van het tabblad basis voor het maken van een persoonlijk eind punt.":::

   1. **Resource**. Op dit tabblad moet u de platform-as-a-Service-resource opgeven waarvoor u de verbinding wilt maken. Selecteer **micro soft. Recovery Services/kluizen** onder **resource type** voor het geselecteerde abonnement. Kies de naam van uw Recovery Services kluis onder **resource**. Selecteer **Azure site Recovery** als de **subresource** van het doel.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Scherm opname van het tabblad Resource voor het koppelen aan een persoonlijk eind punt.":::

   1. **Configuratie**. Geef op dit tabblad het netwerk overs Laan en het subnet op waar u het persoonlijke eind punt wilt maken. 

      Schakel de optie **Ja** in als u de integratie met een privé-DNS-zone wilt inschakelen.
      Kies een bestaande DNS-zone of maak een nieuwe. Als u **Ja** selecteert, wordt de zone automatisch gekoppeld aan het netwerk overs Laan. Met deze actie worden ook de DNS-records toegevoegd die vereist zijn voor de DNS-omzetting van nieuwe Ip's en volledig gekwalificeerde domein namen die zijn gemaakt voor het persoonlijke eind punt.

      Zorg ervoor dat u een nieuwe DNS-zone maakt voor elk nieuw persoonlijk eind punt dat verbinding maakt met dezelfde kluis. Als u een bestaande privé-DNS-zone kiest, worden de vorige CNAME-records overschreven. Zie de [richt lijnen voor privé-eind punten](../private-link/private-endpoint-overview.md#private-endpoint-properties) voordat u doorgaat.

      Als uw omgeving een hub-en spoke-model heeft, hebt u slechts één persoonlijk eind punt en slechts één privé-DNS-zone voor de volledige installatie nodig. Dit komt doordat voor al uw virtuele netwerken een peering is ingeschakeld. Zie voor meer informatie [persoonlijk endpoint DNS-integratie](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Volg de stappen in [persoonlijke DNS-zones maken en DNS-records hand matig toevoegen](#create-private-dns-zones-and-add-dns-records-manually)als u de persoonlijke DNS-zone hand matig wilt maken.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Scherm opname van het tabblad Configuratie voor het configureren van een persoonlijk eind punt.":::

   1. **Tags**. U kunt eventueel labels voor uw persoonlijke eind punt toevoegen.

   1. **Controleren \+ maken**. Wanneer de validatie is voltooid, selecteert u **maken** om het persoonlijke eind punt te maken.

Wanneer het persoonlijke eind punt wordt gemaakt, worden er vijf FQDN-namen (FULLy Qualified Domain names) toegevoegd aan het persoonlijke eind punt. Deze koppelingen zorgen ervoor dat de computers in het on-premises netwerk toegang hebben tot, via het netwerk overs Laan, alle vereiste Site Recovery micro Services in de context van de kluis. U kunt hetzelfde persoonlijke eind punt gebruiken voor de beveiliging van elke Azure-machine in het netwerk overs Laan en alle peered netwerken.

De vijf domein namen zijn ingedeeld in dit patroon:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Privé-eind punten voor site Recovery goed keuren

Als u het persoonlijke eind punt maakt en u ook de eigenaar van de Recovery Services kluis bent, wordt het persoonlijke eind punt dat u eerder hebt gemaakt, binnen een paar minuten automatisch goedgekeurd. Anders moet de eigenaar van de kluis het persoonlijke eind punt goed keuren voordat u het kunt gebruiken. Als u een aangevraagde particuliere endpoint-verbinding wilt goed keuren of afwijzen, gaat u naar **persoonlijke eindpunt verbindingen** onder **instellingen** op de pagina herstel kluis.

U kunt naar de persoonlijke eindpunt resource gaan om de status van de verbinding te bekijken voordat u doorgaat:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Scherm opname van de pagina met persoonlijke eindpunt verbindingen van de kluis en de lijst met verbindingen.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Beschrijving Privé-eind punten maken voor het cache-opslag account

U kunt een persoonlijk eind punt gebruiken om te Azure Storage. Het maken van privé-eind punten voor toegang tot opslag is optioneel voor replicatie van Azure Site Recovery. Als u een persoonlijk eind punt voor opslag maakt, hebt u een persoonlijk eind punt nodig voor het cache-en logboek opslag account in het virtuele netwerk overs Laan.

> [!NOTE]
> Als privé-eind punten niet zijn ingeschakeld voor het opslag account, is de beveiliging nog steeds geslaagd. Replicatie verkeer zou echter door gaan via internet naar Azure Site Recovery open bare eind punten. Om ervoor te zorgen dat replicatie verkeer via persoonlijke koppelingen stromen loopt, moet u het opslag account inschakelen met persoonlijke eind punten.

> [!NOTE]
> Privé-eind punten voor opslag kunnen alleen worden gemaakt voor Algemeen v2-opslag accounts. Zie prijzen voor Azure- [pagina-blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/)voor prijs informatie.

Volg de [richt lijnen voor het maken van](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) een opslag account met een persoonlijk eind punt voor het maken van persoonlijke opslag. Zorg ervoor dat u **Ja** selecteert onder **integreren met privé-DNS-zone**. Selecteer een bestaande DNS-zone of maak een nieuwe.

### <a name="grant-required-permissions-to-the-vault"></a>De vereiste machtigingen voor de kluis verlenen

Afhankelijk van uw instellingen hebt u mogelijk een of meer opslag accounts nodig in de Azure-doel regio. Verleen vervolgens de beheerde identiteits machtigingen voor alle opslag accounts in de cache/het logboek die vereist zijn voor de Site Recovery. In dit geval moet u de vereiste opslag accounts vooraf maken.

Voordat u de replicatie van virtuele machines inschakelt, moet de beheerde identiteit van de kluis over de volgende rolmachtigingen beschikken, afhankelijk van het type opslag account.

- Opslag accounts op basis van Resource Manager (standaard type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Inzender voor Storage Blob-gegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Op Resource Manager gebaseerde opslag accounts (Premium-type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Eigenaar van opslagblobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassieke opslag accounts:
  - [Inzender voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Service functie voor de sleutel operator voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

In deze stappen wordt beschreven hoe u een roltoewijzing toevoegt aan uw opslag account:

1. Ga naar het opslagaccount. Selecteer **toegangs beheer (IAM)** in het linkerdeel venster.

1. Selecteer in de sectie **toewijzing van een rol toevoegen** de optie **toevoegen**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Scherm opname van de pagina toegangs beheer (IAM) voor een opslag account.":::

1. Selecteer op de pagina **een roltoewijzing toevoegen** in de lijst **rol** de rol in de lijst aan het begin van deze sectie. Voer de naam van de kluis in en selecteer vervolgens **Opslaan**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Scherm opname van de pagina roltoewijzing toevoegen.":::

Nadat u deze machtigingen hebt toegevoegd, moet u toegang tot vertrouwde micro soft-Services toestaan. Ga naar **firewalls en virtuele netwerken** en selecteer **vertrouwde micro soft-Services toegang geven tot dit opslag account** in **uitzonde ringen**.

### <a name="protect-your-virtual-machines"></a>Uw virtuele machines beveiligen

Nadat u de voor gaande taken hebt voltooid, gaat u verder met de installatie van uw on-premises infra structuur. Ga door met het volt ooien van een van de volgende taken: 

- [Een configuratie server voor VMware en fysieke machines implementeren](./vmware-azure-deploy-configuration-server.md)
- [De Hyper-V-omgeving instellen voor replicatie](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Nadat de installatie is voltooid, schakelt u replicatie in voor uw bron machines. Stel de infra structuur pas in nadat de persoonlijke eind punten voor de kluis zijn gemaakt in het netwerk overs Laan.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Persoonlijke DNS-zones maken en DNS-records hand matig toevoegen

Als u de optie voor de integratie met een privé-DNS-zone niet hebt geselecteerd wanneer u het persoonlijke eind punt voor de kluis hebt gemaakt, volgt u de stappen in deze sectie.

Maak één privé-DNS-zone om de Site Recovery provider (voor Hyper-V-computers) of de proces server (voor VMware/fysieke machines) toe te staan om particuliere FQDN-adressen om te zetten naar privé Ip's.

1. Maak een privé-DNS-zone.

   1. Zoek naar ' persoonlijke DNS-zone ' in het zoekvak **alle services** en selecteer vervolgens **privé-DNS zone** in de resultaten:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Scherm opname van het zoeken naar een privé-DNS-zone op de pagina nieuwe resources in het Azure Portal.":::

   1. Selecteer op de pagina **privé-DNS zones** de knop **toevoegen** om te beginnen met het maken van een nieuwe zone.

   1. Voer op de pagina **persoonlijke DNS-zone maken** de vereiste gegevens in. Voer **privatelink.siterecovery.windowsazure.com** in als de naam van de privé-DNS-zone. U kunt elke resource groep en elk abonnement kiezen.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Scherm opname van het tabblad basis principes van de pagina Privé-DNS zone maken.":::

   1. Ga naar het tabblad **controleren \+ maken** om de DNS-zone te controleren en te maken.

1. Koppel de privé-DNS-zone aan uw virtuele netwerk.

   U moet nu de privé-DNS-zone koppelen die u hebt gemaakt voor het overs Laan.

   1. Ga naar de privé-DNS-zone die u in de vorige stap hebt gemaakt en ga vervolgens naar **virtuele netwerk koppelingen** in het linkerdeel venster. Selecteer **Toevoegen**.

   1. Voer de vereiste gegevens in. Selecteer in de lijsten **abonnement** en **virtueel netwerk** de optie Details die overeenkomen met het netwerk overs Laan. Laat de standaardwaarden in de andere velden staan.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Scherm opname van de pagina koppeling van virtueel netwerk toevoegen.":::

1. Voeg DNS-records toe.

   Nu u de vereiste privé-DNS-zone en het persoonlijke eind punt hebt gemaakt, moet u DNS-records toevoegen aan uw DNS-zone.

   > [!NOTE]
   > Als u een aangepaste privé-DNS-zone gebruikt, zorg er dan voor dat u vergelijk bare vermeldingen maakt, zoals wordt beschreven in de volgende stap.

   In deze stap moet u vermeldingen voor elke FQDN in uw privé-eind punt in uw privé-DNS-zone maken.

   1. Ga naar uw privé-DNS-zone en ga vervolgens naar het gedeelte **overzicht** in het linkerdeel venster. Selecteer **Recordset** om records toe te voegen.

   1. Voeg op de pagina **recordset toevoegen** een vermelding toe voor elke Fully Qualified Domain name en een persoonlijk IP-adres als **een** type record. In **overzicht** vindt u een lijst met de volledig gekwalificeerde domein namen en IP-adressen op de pagina met het **persoonlijke eind punt** . Zoals u kunt zien in de volgende scherm afbeelding, wordt de eerste Fully Qualified Domain Name van het persoonlijke eind punt toegevoegd aan de recordset in de privé-DNS-zone.

      Deze volledig gekwalificeerde domein namen komen overeen met dit patroon: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Scherm opname van de pagina recordset toevoegen.":::

## <a name="next-steps"></a>Volgende stappen

Nu u persoonlijke eind punten hebt ingeschakeld voor de replicatie van virtuele machines, raadpleegt u de volgende artikelen voor aanvullende en verwante informatie:

- [Een on-premises configuratie server implementeren](./vmware-azure-deploy-configuration-server.md)
- [Herstel na noodgevallen instellen voor on-premises Hyper-V-VM's naar Azure](./hyper-v-azure-tutorial.md)