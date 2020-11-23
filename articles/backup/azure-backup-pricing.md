---
title: Prijzen van Azure Backup
description: Ontdek hoe u de kosten voor Azure Backup kunt schatten voor budgetteringsdoeleinden.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654528"
---
# <a name="azure-backup-pricing"></a>Prijzen van Azure Backup

Zie de [pagina met prijzen van Azure Backup](https://azure.microsoft.com/pricing/details/backup/) voor meer informatie over de prijzen van Azure Backup.

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Gedetailleerde schattingen voor prijzen van Azure Backup downloaden

Als u een kostenraming wilt maken voor budgetterings- of vergelijkingsdoeleinden, kunt u de gedetailleerde [schatter voor prijzen van Azure Backup](https://aka.ms/AzureBackupCostEstimates) downloaden.  

### <a name="what-does-the-estimator-contain"></a>Wat bevat de schatter?

Het werkblad van de schatter voor de kosten van Azure Backup bevat een optie waarmee u een schatting kunt maken voor alle mogelijke werkbelastingen waarvan u een back-up met Azure Backup wilt maken. Deze werkbelastingen zijn onder andere:

- Azure-VM's
- On-premises servers
- SQL in Azure VM's
- SAP HANA in Azure VM's
- Azure-bestandsshares

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Kosten schatten voor het maken van back-ups van Azure-VM's of on-premises servers

Als u de kosten voor het maken van back-ups van Azure-VM's of on-premises servers met behulp van Azure Backup wilt schatten, hebt u de volgende parameters nodig:

- Grootte van de VM's of on-premises servers waarvan u een back-up wilt maken
  - Geef de 'gebruikte grootte' op van schijven of servers waarvan een back-up moet worden gemaakt

- Aantal servers met deze grootte

- Wat is de verwachte hoeveelheid gegevensverloop op deze servers?<br>
  Verloop verwijst naar de hoeveelheid wijzigingen in gegevens. Als u bijvoorbeeld een VM met 200 GB aan gegevens hebt waarvan een back-up moet worden gemaakt en er 10 GB aan wijzigingen per dag wordt aangebracht, is het dagelijkse verloop 5%.

  - Een hoger verloop betekent dat u een back-up van meer gegevens maakt

  - Kies **Laag** of **Gemiddeld** voor bestandsservers en **Hoog** als u databases uitvoert

  - Als u het **churn%** (verlooppercentage) weet, kunt u de optie **Enter your own%** (Uw eigen percentage invoeren) gebruiken

- Het back-upbeleid kiezen

  - Hoe lang verwacht u de dagelijkse back-ups te zullen bewaren? (in dagen)

  - Hoe lang verwacht u de wekelijkse back-ups te zullen bewaren? (in weken)

  - Hoe lang verwacht u de maandelijkse back-ups te zullen bewaren? (in maanden)

  - Hoe lang verwacht u de jaarlijkse back-ups te zullen bewaren? (in jaren)

  - Hoe lang verwacht u momentopnamen voor direct herstel te zullen bewaren? (1-5 dagen)

    - Met deze optie kunt u op een snelle manier back-ups van tot wel zeven dagen geleden terugzetten, met behulp van momentopnamen die op schijven zijn opgeslagen.

- **Optioneel**: Back-up van selectieve schijf

  - Als u de optie **Back-up van selectieve schijf** gebruikt bij het maken van back-ups van Azure-VM's, kiest u de optie **Exclude Disk** en voert u het schijfpercentage dat van back-ups moet worden uitgesloten in termen van grootte in. Als u bijvoorbeeld een VM hebt die is verbonden met drie schijven waarvan 200 GB per schijf is gebruikt en u wilt twee van de schijven uitsluiten van back-ups, voert u 66,7% in.

- **Optioneel**: Opslagredundantie voor back-ups

  - Hiermee wordt de redundantie aangegeven van het opslagaccount waarnaar de back-upgegevens worden verzonden. U kunt het beste **GRS** gebruiken voor de hoogste beschikbaarheid. Dit zorgt ervoor dat een kopie van uw back-upgegevens wordt bewaard in een andere regio, wat u helpt om te voldoen aan meerdere nalevingsstandaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel- of testomgevingen waarvoor geen back-up op bedrijfsniveau is vereist. Selecteer de optie **RAGRS** in het werkblad als u meer inzicht wilt in de kosten wanneer [Herstellen in meerdere regio's](backup-azure-arm-restore-vms.md#cross-region-restore) is ingeschakeld voor uw back-ups.

- **Optioneel**: regionale prijzen wijzigen of kortingstarieven toepassen

  - Als u de schattingen voor een andere regio of kortingstarieven wilt zien, selecteert u **Ja** voor de optie **Wilt u schattingen voor een andere regio proberen?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Kosten schatten voor het maken van back-ups van SQL-servers in Azure-VM's

Als u de kosten voor het maken van back-ups van SQL-servers in Azure-VM's met behulp van Azure Backup wilt schatten, hebt u de volgende parameters nodig:

- Grootte van de VM's of on-premises servers waarvan u een back-up wilt maken

- Aantal SQL-servers met de bovenstaande grootte

- Wat is de verwachte compressie voor de back-upgegevens van uw SQL-servers?

  - De meeste Azure Backup-klanten zien dat de back-upgegevens 80% compressie hebben ten opzichte van de SQL-servergrootte wanneer de SQL-compressie is **ingeschakeld**.

  - Als u verwacht een andere compressie te zien, voert u die in dit veld in

- Wat is de verwachte grootte van back-ups van logboeken?

  - Het percentage geeft de dagelijkse logboekgrootte aan als een percentage van de SQL-servergrootte

- Wat is de verwachte dagelijkse hoeveelheid gegevensverloop op deze servers?

  - Databases hebben doorgaans een hoog verloop

  - Als u het **churn%** (verlooppercentage) weet, kunt u de optie **Enter your own%** (Uw eigen percentage invoeren) gebruiken

- Het back-upbeleid kiezen

  - Type back-up

    - Het meest effectieve beleid dat u kunt kiezen, is **Daily differentials** (Dagelijkse verschillen) met wekelijkse/maandelijkse/jaarlijkse volledige back-ups. Met Azure Backup kunt u herstellen op basis van verschillen met één druk op de muisknop uitvoeren.

    - U kunt ook kiezen voor een beleid met dagelijkse/wekelijkse/maandelijkse/jaarlijkse volledige back-ups. Bij deze optie wordt iets meer opslag verbruikt dan bij de eerste optie.

  - Hoe lang verwacht u de back-ups van logboeken te zullen bewaren? (in dagen) [7-35]

  - Hoe lang verwacht u de dagelijkse back-ups te zullen bewaren? (in dagen)

  - Hoe lang verwacht u de wekelijkse back-ups te zullen bewaren? (in weken)

  - Hoe lang verwacht u de maandelijkse back-ups te zullen bewaren? (in maanden)

  - Hoe lang verwacht u de jaarlijkse back-ups te zullen bewaren? (in jaren)

- **Optioneel**: Opslagredundantie voor back-ups

  - Hiermee wordt de redundantie aangegeven van het opslagaccount waarnaar de back-upgegevens worden verzonden. U kunt het beste **GRS** gebruiken voor de hoogste beschikbaarheid. Dit zorgt ervoor dat een kopie van uw back-upgegevens wordt bewaard in een andere regio, wat u helpt om te voldoen aan meerdere nalevingsstandaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel- of testomgevingen waarvoor geen back-up op bedrijfsniveau is vereist.

- **Optioneel**: regionale prijzen wijzigen of kortingstarieven toepassen

  - Als u de schattingen voor een andere regio of kortingstarieven wilt zien, selecteert u **Ja** voor de optie **Wilt u schattingen voor een andere regio proberen?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Kosten schatten voor het maken van back-ups van SAP HANA-servers in Azure-VM's

Als u de kosten voor het maken van back-ups van SAP HANA-servers in Azure-VM's met behulp van Azure Backup wilt schatten, hebt u de volgende parameters nodig:

- Totale grootte van de SAP HANA-databases waarvan u een back-up wilt maken. Dit moet de som zijn van de grootte van de volledige back-up van elke database, zoals gerapporteerd door SAP HANA.
- Aantal SAP HANA-servers met de bovenstaande grootte
- Wat is de verwachte grootte van back-ups van logboeken?
  
  - Het percentage geeft de gemiddelde dagelijkse logboekgrootte aan als een percentage van de totale grootte van SAP HANA-databases waarvan u een back-up maakt op de SAP HANA-server
- Wat is de verwachte dagelijkse hoeveelheid gegevensverloop op deze servers?
  - Het percentage geeft de gemiddelde dagelijkse verloopgrootte aan als een percentage van de totale grootte van SAP HANA-databases waarvan u een back-up maakt op de SAP HANA-server
  - Databases hebben doorgaans een hoog verloop
  - Als u het **churn%** (verlooppercentage) weet, kunt u de optie **Enter your own%** (Uw eigen percentage invoeren) gebruiken
- Het back-upbeleid kiezen
  - Type back-up
    - Het meest effectieve beleid dat u kunt kiezen, is **Daily differentials** (Dagelijkse verschillen) met **wekelijkse/maandelijkse/jaarlijkse** volledige back-ups. Met Azure Backup kunt u herstellen op basis van verschillen met één druk op de muisknop uitvoeren.
    - U kunt ook kiezen voor een beleid met **dagelijkse/wekelijkse/maandelijkse/jaarlijkse** volledige back-ups. Bij deze optie wordt iets meer opslag verbruikt dan bij de eerste optie.
  - Hoe lang verwacht u de back-ups van logboeken te zullen bewaren? (in dagen) [7-35]
  - Hoe lang verwacht u de dagelijkse back-ups te zullen bewaren? (in dagen)
  - Hoe lang verwacht u de wekelijkse back-ups te zullen bewaren? (in weken)
  - Hoe lang verwacht u de maandelijkse back-ups te zullen bewaren? (in maanden)
  - Hoe lang verwacht u de jaarlijkse back-ups te zullen bewaren? (in jaren)
- **Optioneel**: Opslagredundantie voor back-ups
  
  - Hiermee wordt de redundantie aangegeven van het opslagaccount waarnaar de back-upgegevens worden verzonden. U kunt het beste **GRS** gebruiken voor de hoogste beschikbaarheid. Dit zorgt ervoor dat een kopie van uw back-upgegevens wordt bewaard in een andere regio, wat u helpt om te voldoen aan meerdere nalevingsstandaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel- of testomgevingen waarvoor geen back-up op bedrijfsniveau is vereist.
- **Optioneel**: regionale prijzen wijzigen of kortingstarieven toepassen
  
  - Als u de schattingen voor een andere regio of kortingstarieven wilt zien, selecteert u **Ja** voor de optie **Wilt u schattingen voor een andere regio proberen?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Kosten schatten voor het maken van back-ups van Azure-bestandsshares

Als u een schatting wilt van de kosten voor het maken van back-ups van Azure-bestandsshares met behulp van de [back-upoplossing op basis van momentopnamen](azure-file-share-backup-overview.md) van Azure Backup, hebt u de volgende parameters nodig:

- Grootte (**in GB**) van de bestandsshares waarvan u een back-up wilt maken.

- Als u een back-up van bestandsshares van meerdere opslagaccounts wilt maken, geeft u het aantal opslagaccounts op dat als host fungeert voor de bestandsshares met de bovenstaande grootte.

- Verwachte hoeveelheid gegevensverloop op de bestandsshares waarvan u een back-up wilt maken. <br>Het verloop is de hoeveelheid wijzigingen in gegevens en is rechtstreeks van invloed op de opslaggrootte van de momentopname. Als u bijvoorbeeld een bestandsshare met 200 GB aan gegevens hebt waarvan een back-up moet worden gemaakt en er 10 GB aan wijzigingen per dag wordt aangebracht, is het dagelijkse verloop 5%.
  - Een hoger verloop betekent dat er elke dag veel gegevens in de bestandsshare worden gewijzigd, en dat dus ook incrementele momentopnamen (waarin alleen de gegevenswijzigingen worden vastgelegd) groter zullen zijn.
  - Selecteer Laag (1%), Gemiddeld (3%) of Hoog (5%), afhankelijk van de kenmerken en het gebruik van uw bestandsshare.
  - Als u het exacte **churn%** (verlooppercentage) voor uw bestandsshare weet, kunt u de optie **Enter your own%** (Uw eigen percentage invoeren) in het keuzemenu selecteren. Geef de waarden op (in%) voor het dagelijkse, wekelijkse, maandelijkse en jaarlijkse verloop.

- Type opslagaccount (Standard of Premium) en de instelling voor de opslagredundantie van het opslagaccount dat als host fungeert voor de back-up van de bestandsshare. <br>In de huidige back-upoplossing voor Azure-bestandsshares worden momentopnamen opgeslagen in hetzelfde opslagaccount als de back-up van de bestandsshare. De opslagkosten die zijn gekoppeld aan momentopnamen worden dus gefactureerd als onderdeel van uw factuur voor Azure-bestanden, op basis van de prijzen van momentopnamen voor het accounttype en de redundantie-instelling van het opslagaccount dat als host fungeert voor de bestandsshare en momentopnamen waarvan een back-up wordt gemaakt.

- Retentie voor verschillende back-ups
  - Hoe lang verwacht u de dagelijkse back-ups te zullen bewaren? (in dagen)
  - Hoe lang verwacht u de wekelijkse back-ups te zullen bewaren? (in weken)
  - Hoe lang verwacht u de maandelijkse back-ups te zullen bewaren? (in maanden)
  - Hoe lang verwacht u de jaarlijkse back-ups te zullen bewaren? (in jaren)

  Raadpleeg [de ondersteuningsmatrix voor Azure-bestandsshares](azure-file-share-support-matrix.md#retention-limits) voor de maximaal ondersteunde retentiewaarden per categorie.

- **Optioneel**: regionale prijzen wijzigen of kortingstarieven toepassen.
  - De standaardwaarden die zijn ingesteld voor de opslagkosten voor momentopnamen per GB en de kosten van beveiligde instanties in de schatter zijn voor de regio US - oost. Als u de schattingen voor een andere regio of kortingstarieven wilt zien, selecteert u **Ja** voor de optie **Wilt u schattingen voor een andere regio proberen?** Voer vervolgens de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="next-steps"></a>Volgende stappen

[Wat is de Azure Backup-service?](backup-overview.md)
