---
title: BareMetal inrichten voor Oracle
description: Meer informatie over het inrichten van uw BareMetal-infrastructuur voor Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559016"
---
# <a name="provision-baremetal-for-oracle"></a>BareMetal inrichten voor Oracle

In dit artikel wordt beschreven hoe u uw BareMetal Infrastructure-exemplaren inrichten voor Oracle-workloads. 

De eerste stap voor het inrichten van uw BareMetal-exemplaren is het werken met uw Microsoft CSA. Ze helpen u op basis van uw specifieke workloadbehoeften en de architectuur die u implementeert, of dit nu één exemplaar, één knooppunt-RAC of RAC is. Zie Architecture [of BareMetal Infrastructure for Oracle (Architectuur van BareMetal-infrastructuur voor Oracle) voor meer informatie over deze topologies.](oracle-baremetal-architecture.md)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een actief Azure-abonnement
> * Microsoft Premier Support-contract
> * Licenties voor Red Hat Enterprise Linux 7.6
> * Oracle-ondersteuningscontract 
> * Licenties en software-installatieonderdelen voor Oracle
> * ExpressRoute is **on-premises verbonden** met Azure **(** configureer optioneel ExpressRoute Global Reach voor directe connectiviteit van on-premises naar de Oracle Database)   
> * Virtueel netwerk
> * Het maken van de gateway
> * Virtuele machines (VM's) in het virtuele netwerk (jumpboxs)

## <a name="information-to-provide-microsoft-operations"></a>Informatie voor het leveren van Microsoft-bewerkingen

U moet de volgende informatie aan uw CSA verstrekken:

1. Adresruimte van virtueel netwerk. Dit bereik moet /24 subnet zijn; bijvoorbeeld 10.11.0.0/24.
2. P2P-bereik. Dit bereik moet een /29-subnet zijn; bijvoorbeeld 10.12.0.0/29.
3. IP-adresgroep van server. Het aanbevolen bereik is /24; bijvoorbeeld 10.13.0.0/24.
4. IP-adres van server. Kies een IP-adres uit de IP-adresgroep van de server.

    > [!Note] 
    > De eerste dertig IP-adressen zijn gereserveerd voor de configuratie van de Microsoft-infrastructuur. In dit voorbeeld is uw eerste beschikbare IP-adres voor een blade dus 10.13.0.30.

5. De Azure-regio vereist; bijvoorbeeld VS - west 2.
6. De BareMetal Infrastructure-SKU vereist; bijvoorbeeld S192 (twee machines).

## <a name="storage-requirements"></a>Opslagvereisten

Werk tijdens de inrichtingsaanvraag samen met uw CSA-vertegenwoordiger voor uw opslagbehoeften, inclusief de verwachte opslagbehoeften op basis van toekomstige groei. Toegevoegde opslag is in stappen van 1 TB.

Voor volumes volgen we de Standaard voor optimale flexibele architectuur [(OFA)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)van Oracle, met de Basic-laag en de Enterprise-configuratie. Als u andere aangepaste opslagvereisten hebt dan standaard 'T-shirt aanpassen', maakt u uw aangepaste aanvraag via uw CSA.

| Basisconfiguratie (POC/testen) | Description | Klein | Normaal | Groot |
| --- | --- | --- | --- | --- |
| /u01 | Binaire Oracle-bestanden | 500 GB | 500 GB | 500 GB |
| /u02 | Leesintensief/beheerder | 500 GB | 1 TB | 5 TB |
| /u03 | Intensief/logboeken schrijven | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Enterprise Configuration | Description | Klein | Normaal | Groot | Extra groot |
| --- | --- | --- | --- | --- | --- |
| /u01 | Binaire Oracle-bestanden | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Beheerder | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 tot /u59 | Leesintensief | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 naar /u89 | Schrijfintensief | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 naar /u91 | Logboeken opnieuw doen | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Archiveren | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Volgende stap

Meer informatie over de BareMetal-infrastructuur voor Oracle.

> [!div class="nextstepaction"]
> [Wat is een BareMetal-infrastructuur in Azure?](../../concepts-baremetal-infrastructure-overview.md)
