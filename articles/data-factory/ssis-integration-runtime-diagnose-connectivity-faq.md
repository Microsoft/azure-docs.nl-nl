---
title: De functie connectiviteits problemen oplossen gebruiken in de SSIS Integration runtime
description: Verbindings problemen in de SSIS Integration runtime oplossen met behulp van de functie connectiviteit diagnosticeren.
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361852"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>De functie connectiviteits problemen oplossen gebruiken in de SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

U kunt verbindings problemen vinden tijdens het uitvoeren van SQL Server Integration Services (SSIS)-pakketten in de SSIS Integration runtime. Deze problemen treden vooral op als uw SSIS Integration runtime lid wordt van het virtuele Azure-netwerk.

Verbindings problemen oplossen met behulp van de functie *connectiviteit diagnosticeren* om verbindingen te testen. De functie bevindt zich op de pagina monitoring SSIS Integration runtime van de Azure Data Factory Portal.

 ![Pagina controleren-connectiviteit vaststellen](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Pagina bewaken-verbinding testen](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

Gebruik de volgende secties voor meer informatie over de meest voorkomende fouten die zich voordoen wanneer u verbindingen wilt testen. In elke sectie wordt de volgende informatie beschreven:

- Foutcode
- Foutbericht
- Mogelijke oorzaak (en) van de fout
- Aanbevolen oplossing (en)

## <a name="error-code-invalidinput"></a>Fout code: InvalidInput

- **Fout bericht**: "Controleer of de invoer juist is."
- **Mogelijke oorzaak**: de invoer is onjuist.
- **Aanbeveling**: Controleer uw invoer.

## <a name="error-code-firewallornetworkissue"></a>Fout code: FirewallOrNetworkIssue

- **Fout bericht**: Controleer of deze poort is geopend op de firewall/server-NSG en of het netwerk stabiel is.
- **Mogelijke oorzaken:**
  - Uw server heeft de poort niet geopend.
  - Uw netwerk beveiligings groep wordt uitgaand verkeer op de poort geweigerd.
  - Met uw NVA/Azure Firewall/on-premises firewall wordt de poort niet geopend.
- **Vereisten**
  - Open de poort op de server.
  - Werk de netwerk beveiligings groep bij om uitgaand verkeer op de poort toe te staan.
  - Open de poort op de firewall NVA/Azure Firewall/on-premises.

## <a name="error-code-misconfigureddnssettings"></a>Fout code: MisconfiguredDnsSettings

- **Fout bericht**: als u uw eigen DNS-server gebruikt in het VNet dat is gekoppeld aan uw Azure-SSIS IR, controleert u of de naam van uw host kan worden omgezet. "
- **Mogelijke oorzaken:**
  -  Er is een probleem met uw aangepaste DNS.
  -  U gebruikt geen Fully Qualified Domain Name (FQDN) voor de naam van uw particuliere host.
- **Vereisten**
  -  Los uw aangepaste DNS-probleem op om er zeker van te zijn dat de hostnaam kan worden omgezet.
  -  Gebruik de FQDN. Azure-SSIS IR wordt niet automatisch uw eigen DNS-achtervoegsel toegevoegd. Gebruik bijvoorbeeld **<your_private_server>. contoso.com** in plaats van **<your_private_server**>.

## <a name="error-code-servernotallowremoteconnection"></a>Fout code: ServerNotAllowRemoteConnection

- **Fout bericht**: "Controleer of de server externe TCP-verbindingen via deze poort toestaat."
- **Mogelijke oorzaken:**
  -  De firewall van uw server staat geen externe TCP-verbindingen toe.
  -  Uw server is niet online.
- **Vereisten**
  -  Externe TCP-verbindingen op de Server Firewall toestaan.
  -  Start de server.
   
## <a name="error-code-misconfigurednsgsettings"></a>Fout code: MisconfiguredNsgSettings

- **Fout bericht**: ' Controleer of het NSG van uw VNet uitgaand verkeer via deze poort toestaat. Als u Azure ExpressRoute en of een UDR gebruikt, moet u controleren of deze poort is geopend op uw firewall/server.
- **Mogelijke oorzaken:**
  -  Uw netwerk beveiligings groep wordt uitgaand verkeer op de poort geweigerd.
  -  Met uw NVA/Azure Firewall/on-premises firewall wordt de poort niet geopend.
- **Advies**
  -  Werk de netwerk beveiligings groep bij om uitgaand verkeer op de poort toe te staan.
  -  Open de poort op de firewall NVA/Azure Firewall/on-premises.

## <a name="error-code-genericissues"></a>Fout code: GenericIssues

- **Fout bericht**: de test verbinding is mislukt vanwege algemene problemen.
- **Mogelijke oorzaak**: de test verbinding heeft een algemeen tijdelijk probleem aangetroffen.
- **Aanbeveling**: Voer de test verbinding later opnieuw uit. Neem contact op met het ondersteunings team van Azure Data Factory als u het niet meer wilt proberen.

## <a name="error-code-pspingexecutiontimeout"></a>Fout code: PSPingExecutionTimeout

- **Fout bericht**: de time-out voor de verbinding testen, probeer het later opnieuw.
- **Mogelijke oorzaak**: er is een time-out opgetreden voor de verbinding testen.
- **Aanbeveling**: Voer de test verbinding later opnieuw uit. Neem contact op met het ondersteunings team van Azure Data Factory als u het niet meer wilt proberen.

## <a name="error-code-networkinstable"></a>Fout code: NetworkInstable

- **Fout bericht**: de verbindings test is onregelmatig geslaagd vanwege een instabiliteit van het netwerk.
- **Mogelijke oorzaak**: tijdelijk netwerk probleem.
- **Aanbeveling**: Controleer of de server of het firewall netwerk stabiel is.

## <a name="next-steps"></a>Volgende stappen

- [Een SSIS-project implementeren in azure met SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [SSIS-pakketten uitvoeren in azure met SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [SSIS-pakketten plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)