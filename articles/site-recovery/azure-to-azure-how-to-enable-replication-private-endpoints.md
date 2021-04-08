---
title: Replicatie inschakelen voor privé-eind punten in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie configureert voor Vm's met persoonlijke eind punten van de ene Azure-regio naar een andere met behulp van Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629824"
---
# <a name="replicate-machines-with-private-endpoints"></a>Computers repliceren met persoonlijke eind punten

Met Azure Site Recovery kunt u privé-eind punten van [Azure private link](../private-link/private-endpoint-overview.md) gebruiken voor het repliceren van uw computers in een geïsoleerd virtueel netwerk. Toegang tot een persoonlijk eind punt tot een Recovery kluis wordt ondersteund in alle Azure commerciële & overheids regio's.

In dit artikel vindt u instructies voor het uitvoeren van de volgende stappen:

- Maak een Azure Backup Recovery Services kluis om uw computers te beveiligen.
- Schakel een beheerde identiteit in voor de kluis en verleen de vereiste machtigingen voor toegang tot klant opslag accounts om verkeer van bron naar doel locaties te repliceren. Beheerde identiteits toegang voor opslag is nodig wanneer u de toegang tot persoonlijke koppelingen instelt voor de kluis.
- DNS-wijzigingen vereist maken voor privé-eind punten
- Privé-eind punten maken en goed keuren voor een kluis in een virtueel netwerk
- Maak privé-eind punten voor de opslag accounts. U kunt open bare of firewalld toegang voor opslag als nodig blijven toestaan. Het maken van een persoonlijk eind punt voor toegang tot opslag is niet verplicht voor Azure Site Recovery.
  
Hieronder vindt u een referentie architectuur voor het wijzigen van de replicatie werk stroom met persoonlijke eind punten.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Referentie architectuur voor Site Recovery met privé-eind punten.":::

## <a name="prerequisites-and-caveats"></a>Vereisten en aanvullende opmerkingen

- Privé-eind punten kunnen alleen worden gemaakt voor nieuwe Recovery Services kluizen waarvoor geen items zijn geregistreerd bij de kluis. Daarom moeten persoonlijke eind punten **worden gemaakt voordat er items worden toegevoegd aan de kluis**. Bekijk de prijs structuur voor [privé-eind punten](https://azure.microsoft.com/pricing/details/private-link/).
- Wanneer er een persoonlijk eind punt wordt gemaakt voor een kluis, wordt de kluis vergrendeld en is deze **niet toegankelijk vanuit andere netwerken dan die netwerken met persoonlijke eind punten**.
- Azure Active Directory biedt momenteel geen ondersteuning voor privé-eind punten. Daarom moeten Ip's en FQDN-namen (Fully Qualified Domain names) die vereist zijn voor het gebruik van Azure Active Directory in een regio, uitgaande toegang hebben vanaf het beveiligde netwerk. U kunt ook de netwerk beveiligings groep label Azure Active Directory en Azure Firewall Tags gebruiken om toegang tot Azure Active Directory toe te staan, zoals van toepassing.
- **Er zijn ten minste zeven IP-adressen vereist** in de subnetten van de bron machines en de herstel machines. Wanneer u een persoonlijk eind punt voor de kluis maakt, maakt Site Recovery vijf persoonlijke koppelingen voor toegang tot de micro Services. Wanneer u de replicatie inschakelt, worden er twee extra persoonlijke koppelingen toegevoegd voor het koppelen van de bron-en doel regio.
- In zowel de bron-als het herstel subnet **is een extra IP-adres vereist** . Dit IP-adres is alleen nodig als u persoonlijke eind punten moet gebruiken om verbinding te maken met opslag accounts in de cache.
  Privé-eind punten voor opslag kunnen alleen worden gemaakt voor Algemeen v2-type. Bekijk de prijs structuur voor [gegevens overdracht op GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Privé-eind punten maken en gebruiken voor Site Recovery

 In deze sectie vindt u informatie over de stappen voor het maken en gebruiken van privé-eind punten voor Azure Site Recovery binnen uw virtuele netwerken.

> [!NOTE]
> Het wordt ten zeerste aangeraden deze stappen uit te voeren in dezelfde volg orde als de voor waarde. Als u dit niet doet, kan dit ertoe leiden dat de kluis wordt gerenderd en dat het proces niet kan worden gebruikt met een nieuwe kluis.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services-kluis is een entiteit die de replicatie gegevens van machines bevat en wordt gebruikt om Site Recovery bewerkingen te activeren. Zie [een Recovery Services kluis maken](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)voor meer informatie.

## <a name="enable-the-managed-identity-for-the-vault"></a>Schakel de beheerde identiteit voor de kluis in.

Met een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) kan de kluis toegang krijgen tot de opslag accounts van de klant. Site Recovery moet toegang hebben tot de bron opslag, de doel opslag en de opslag-en logboek registratie van Storage-accounts, afhankelijk van de vereiste van het scenario.
Toegang tot beheerde identiteit is essentieel wanneer u gebruikmaakt van een persoonlijke koppelingen service voor de kluis.

1. Ga naar de Recovery Services kluis. Selecteer **identiteit** onder _instellingen_.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Hiermee worden de Azure Portal en de Recovery Services pagina weer gegeven.":::

1. Wijzig de **status** in _op aan_ en selecteer **Opslaan**.

1. Er wordt een **object-id** gegenereerd die aangeeft dat de kluis nu is geregistreerd bij Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Privé-eind punten maken voor de Recovery Services kluis

Als u zowel failover als failback voor virtuele Azure-machines wilt inschakelen, hebt u twee persoonlijke eind punten nodig voor de kluis. Eén persoonlijk eind punt voor de beveiliging van machines in het bron netwerk en een andere voor het opnieuw beveiligen van defecte computers in het herstel netwerk.

Zorg ervoor dat u tijdens het installatie proces een virtueel netwerk voor herstel in de doel regio maakt.

Maak het eerste persoonlijke eind punt voor uw kluis in het virtuele bron netwerk met behulp van het privé koppelings centrum in de portal of via [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Maak het tweede persoonlijke eind punt voor de kluis in het herstel netwerk. Hieronder volgen de stappen voor het maken van het persoonlijke eind punt in het bron netwerk. Herhaal dezelfde richt lijnen voor het maken van het tweede persoonlijke eind punt.

1. Zoek en selecteer ' persoonlijke koppeling ' in de zoek balk Azure Portal. Met deze actie gaat u naar het persoonlijke koppelings centrum.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Hiermee wordt gezocht naar de Azure Portal voor het privé-koppelings centrum.":::

1. Selecteer **privé-eind punten** op de navigatie balk aan de linkerkant. Selecteer op de pagina privé-eind punten **\+ toevoegen** om te beginnen met het maken van een persoonlijk eind punt voor uw kluis.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Toont het maken van een persoonlijk eind punt in het persoonlijke koppelings centrum.":::

1. Wanneer u in de ervaring ' persoonlijk eind punt maken ' bent, moet u details opgeven voor het maken van uw verbinding met een privé-eind punt.

   1. **Basis beginselen**: Vul de basis gegevens in voor uw privé-eind punten. De regio moet hetzelfde zijn als de bron machines.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Hier worden het tabblad basis, project details, abonnementen en andere gerelateerde velden weer gegeven voor het maken van een persoonlijk eind punt in de Azure Portal.":::

   1. **Resource**: op dit tabblad moet u de platform-as-a-Service-Resource vermelden waarvoor u de verbinding wilt maken. Selecteer _micro soft. Recovery Services/kluizen_ van het **resource type** voor het geselecteerde abonnement. Kies vervolgens de naam van de Recovery Services kluis voor de **resource** en stel _Azure site Recovery_ in als de **subresource** van het doel.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Hier worden het tabblad Resource, het resource type, de resource en de doel resource velden weer gegeven die u kunt koppelen aan een persoonlijk eind punt in de Azure Portal.":::

   1. **Configuratie**: Geef in configuratie het virtuele netwerk en het subnet op waar u het persoonlijke eind punt wilt maken. Dit virtuele netwerk is het netwerk waar de virtuele machine zich bevindt. Schakel de optie **Ja** in als u integratie met een particuliere DNS-zone wilt inschakelen. Kies een al gemaakte DNS-zone of maak een nieuwe. Als u **Ja** selecteert, wordt de zone automatisch gekoppeld aan het virtuele bron netwerk en worden de DNS-records toegevoegd die vereist zijn voor de DNS-omzetting van nieuwe ip's en volledig gekwalificeerde domein namen die zijn gemaakt voor het persoonlijke eind punt.

      Zorg ervoor dat u een nieuwe DNS-zone maakt voor elk nieuw persoonlijk eind punt dat verbinding maakt met dezelfde kluis. Als u een bestaande privé-DNS-zone kiest, worden de vorige CNAME-records overschreven. Raadpleeg de [richt lijnen voor het persoonlijke eind punt](../private-link/private-endpoint-overview.md#private-endpoint-properties) voordat u doorgaat.

      Als uw omgeving een hub-en-spoke-model heeft, hebt u slechts één persoonlijk eind punt en slechts één privé-DNS-zone voor de volledige installatie nodig, omdat er al een peering tussen de virtuele netwerken is ingeschakeld. Zie voor meer informatie [persoonlijk endpoint DNS-integratie](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Volg de stappen in [persoonlijke DNS-zones maken en DNS-records hand matig toevoegen](#create-private-dns-zones-and-add-dns-records-manually)als u de persoonlijke DNS-zone hand matig wilt maken.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Hiermee wordt het tabblad Configuratie met netwerk-en DNS-integratie velden weer gegeven voor de configuratie van een persoonlijk eind punt in de Azure Portal.":::

   1. **Tags**: u kunt desgewenst labels voor uw persoonlijke eind punt toevoegen.

   1. **Controleren \+ maken**: Selecteer **maken** om het persoonlijke eind punt te maken wanneer de validatie is voltooid.

Zodra het persoonlijke eind punt is gemaakt, worden er vijf volledig gekwalificeerde domein namen toegevoegd aan het persoonlijke eind punt. Met deze koppelingen kunnen de computers in het virtuele netwerk toegang krijgen tot alle vereiste Site Recovery micro Services in de context van de kluis. Later, wanneer u de replicatie inschakelt, worden twee extra volledig gekwalificeerde domein namen toegevoegd aan hetzelfde persoonlijke eind punt.

De vijf domein namen zijn ingedeeld met het volgende patroon:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Privé-eind punten voor Site Recovery goed keuren

Als de gebruiker die het persoonlijke eind punt maakt ook de eigenaar is van de Recovery Services kluis, wordt het persoonlijke eind punt dat hierboven is gemaakt, automatisch goedgekeurd binnen een paar minuten. Anders moet de eigenaar van de kluis het persoonlijke eind punt goed keuren voordat u het gebruikt. Als u een aangevraagde particuliere endpoint-verbinding wilt goed keuren of afwijzen, gaat u naar het tabblad instellingen op de pagina herstel kluis en klikt u op **verbindingen met privé** -eind

U kunt naar de persoonlijke eindpunt resource gaan om de status van de verbinding te bekijken voordat u doorgaat.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Toont de pagina verbindingen met privé-eind punten van de kluis en de lijst met verbindingen in de Azure Portal.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Beschrijving Privé-eind punten maken voor het cache-opslag account

Er mag een persoonlijk eind punt naar Azure Storage worden gebruikt. Het maken van privé-eind punten voor toegang tot opslag is _optioneel_ voor replicatie van Azure site Recovery. Bij het maken van een persoonlijk eind punt voor opslag gelden de volgende vereisten:

- U hebt een persoonlijk eind punt nodig voor het cache-en logboek opslag account in uw virtuele bron netwerk.
- U hebt een tweede persoonlijk eind punt nodig bij het opnieuw beveiligen van de apparaten waarvoor failover is uitgevoerd in het herstel netwerk. Dit persoonlijke eind punt is voor het nieuwe opslag account dat is gemaakt in de doel regio.

> [!NOTE]
> Als privé-eind punten niet zijn ingeschakeld voor het opslag account, is de beveiliging nog steeds geslaagd. Replicatie verkeer zou echter door gaan naar Azure Site Recovery open bare eind punten. Om ervoor te zorgen dat replicatie verkeer via persoonlijke koppelingen stromen loopt, moet u het opslag account inschakelen met persoonlijke eind punten.

> [!NOTE]
> Privé-eind punt voor opslag kan alleen worden gemaakt op een **Algemeen v2** -opslag account. Zie [prijzen voor standaard pagina-blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/)voor prijs informatie.

Volg de [richt lijnen voor](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) het maken van een opslag account met een persoonlijk eind punt. Zorg ervoor dat u **Ja** selecteert om te integreren met een privé-DNS-zone. Selecteer een al gemaakte DNS-zone of maak een nieuwe.

## <a name="grant-required-permissions-to-the-vault"></a>De vereiste machtigingen voor de kluis verlenen

Als uw virtuele machines beheerde schijven gebruiken, moet u de beheerde identiteits machtigingen alleen toekennen aan de opslag accounts in de cache. Als de virtuele machines niet-beheerde schijven gebruiken, moet u de beheerde identiteits machtigingen verlenen voor de bron-, cache-en doel opslag accounts. In dit geval moet u het doel opslag account vooraf maken.

Voordat u de replicatie van virtuele machines inschakelt, moet de beheerde identiteit van de kluis de volgende rolmachtigingen hebben, afhankelijk van het type opslag account:

- Op Resource Manager gebaseerde opslag accounts (standaard type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Inzender voor Storage Blob-gegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Op Resource Manager gebaseerde opslag accounts (Premium-type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Eigenaar van opslagblobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassieke opslag accounts:
  - [Inzender voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Service functie voor de sleutel operator voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

In de volgende stappen wordt beschreven hoe u een functie toewijzing aan uw opslag accounts kunt toevoegen, één per keer:

1. Ga naar het opslag account en navigeer naar **toegangs beheer (IAM)** aan de linkerkant van de pagina.

1. Eenmaal op **toegangs beheer (IAM)** in het vak ' roltoewijzing toevoegen ' selecteert u **toevoegen**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Toont de pagina toegangs beheer (IAM) in een opslag account en de knop toewijzing van een rol toevoegen in de Azure Portal.":::

1. Kies op de pagina een roltoewijzing toevoegen de rol in de bovenstaande lijst in de vervolg keuzelijst **rol** . Voer de **naam** van de kluis in en selecteer **Opslaan**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Toont de pagina toegangs beheer (IAM) in een opslag account en de opties om een rol te selecteren en aan welke Principal deze rol moet worden verleend in de Azure Portal.":::

Naast deze machtigingen moet voor MS-vertrouwde services ook toegang worden verleend. Ga naar ' firewalls en virtuele netwerken ' en selecteer ' vertrouwde micro soft-Services toegang geven tot dit opslag account ' in **uitzonde ringen**.

## <a name="protect-your-virtual-machines"></a>Uw virtuele machines beveiligen

Als alle bovenstaande configuraties zijn voltooid, gaat u verder met het inschakelen van replicatie voor uw virtuele machines. Alle Site Recovery bewerkingen werken zonder extra stappen als de DNS-integratie is gebruikt tijdens het maken van privé-eind punten in de kluis. Als de DNS-zones echter hand matig worden gemaakt en geconfigureerd, moet u extra stappen uitvoeren om specifieke DNS-records toe te voegen in zowel de bron-als doel-DNS-zones nadat de replicatie is ingeschakeld. Zie voor meer informatie en stappen een [persoonlijke DNS-zone maken en DNS-records hand matig toevoegen](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Persoonlijke DNS-zones maken en DNS-records hand matig toevoegen

Volg de stappen in deze sectie als u de optie voor de integratie met de persoonlijke DNS-zone op het moment van het maken van een persoonlijk eind punt niet hebt geselecteerd voor de kluis.

Maak één privé-DNS-zone zodat de Mobility-agent persoonlijke koppelingen volledig gekwalificeerde domein namen kan omzetten naar privé Ip's.

1. Een privé-DNS-zone maken

   1. Zoek in de zoek balk **alle services** naar ' privé-DNS zone ' en selecteer ' privé-DNS zones ' in de vervolg keuzelijst.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Hiermee wordt gezocht naar ' privé DNS-zone ' op de pagina nieuwe resources in het Azure Portal.":::

   1. Selecteer op de pagina Privé-DNS zones de knop **\+ toevoegen** om te beginnen met het maken van een nieuwe zone.

   1. Vul op de pagina ' persoonlijke DNS-zone maken ' de vereiste gegevens in. Voer de naam van de privé-DNS-zone in als `privatelink.siterecovery.windowsazure.com` . U kunt elke resource groep en elk abonnement kiezen om deze te maken.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Toont het tabblad basis principes van de pagina Privé-DNS zone maken en gerelateerde project details in de Azure Portal.":::

   1. Ga naar het tabblad **controleren \+ maken** om de DNS-zone te controleren en te maken.

1. Een persoonlijke DNS-zone koppelen aan uw virtuele netwerk

   De hierboven gemaakte privé-DNS-zones moeten nu zijn gekoppeld aan het virtuele netwerk waar uw servers momenteel zijn. U moet ook de privé-DNS-zone van tevoren koppelen aan het virtuele doel netwerk.

   1. Ga naar de privé-DNS-zone die u in de vorige stap hebt gemaakt en navigeer naar **virtuele netwerk koppelingen** aan de linkerkant van de pagina. Selecteer vervolgens de knop **\+ toevoegen** .

   1. Vul de vereiste gegevens in. De velden **abonnement** en **virtueel netwerk** moeten worden ingevuld met de bijbehorende details van het virtuele netwerk waarin uw servers zijn opgenomen. De andere velden moeten blijven staan.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Toont de pagina voor het toevoegen van een koppeling van een virtueel netwerk met de naam van de koppeling, het abonnement en het gerelateerde virtuele netwerk in de Azure Portal.":::

1. DNS-records toevoegen

   Zodra u de vereiste persoonlijke DNS-zones en de persoonlijke eind punten hebt gemaakt, moet u DNS-records toevoegen aan uw DNS-zones.

   > [!NOTE]
   > Als u een aangepaste privé-DNS-zone gebruikt, moet u ervoor zorgen dat vergelijk bare vermeldingen worden gemaakt zoals hieronder wordt beschreven.

   Voor deze stap moet u vermeldingen voor elke Fully Qualified Domain Name in uw persoonlijke eind punt in uw privé-DNS-zone maken.

   1. Ga naar de privé-DNS-zone en navigeer naar de sectie **overzicht** aan de linkerkant van de pagina. Selecteer vervolgens **\+ record set** om records toe te voegen.

   1. Voeg op de pagina recordset toevoegen die wordt geopend, een vermelding voor elke Fully Qualified Domain Name en een persoonlijk IP-adres toe als _een_ type record. De lijst met volledig gekwalificeerde domein namen en IP-adressen kan worden verkregen via de pagina ' persoonlijk eind punt ' in **overzicht**. Zoals u in het onderstaande voor beeld ziet, wordt de eerste Fully Qualified Domain Name uit het persoonlijke eind punt toegevoegd aan de recordset in de privé-DNS-zone.

      De volledig gekwalificeerde domein namen komen overeen met het patroon: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Toont de pagina voor het toevoegen van een type record van een DNS voor de Fully Qualified Domain Name aan het persoonlijke eind punt in de Azure Portal.":::

   > [!NOTE]
   > Nadat u replicatie hebt ingeschakeld, worden er twee volledig gekwalificeerde domein namen gemaakt op de persoonlijke eind punten in beide regio's. Zorg ervoor dat u ook de DNS-records voor deze nieuwe volledig gekwalificeerde domein namen toevoegt.

## <a name="next-steps"></a>Volgende stappen

Nu u persoonlijke eind punten hebt ingeschakeld voor de replicatie van virtuele machines, raadpleegt u de volgende pagina's voor aanvullende en verwante informatie:

- [Virtuele Azure-machines repliceren naar een andere Azure-regio](./azure-to-azure-how-to-enable-replication.md)
- [Zelfstudie: Herstel na noodgevallen instellen voor Azure-VM's](./azure-to-azure-tutorial-enable-replication.md)