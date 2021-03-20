---
title: Microsoft Azure certificeringen voor SAP | Microsoft Docs
description: De lijst met huidige configuraties en certificeringen van SAP op het Azure-platform is bijgewerkt.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669698"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certificeringen en-configuraties die worden uitgevoerd op Microsoft Azure

SAP en micro soft hebben een lange geschiedenis van het samen werken in een sterk partnerschap met wederzijdse voor delen voor hun klanten. Micro soft werkt voortdurend het platform bij en verzendt nieuwe certificerings gegevens naar SAP om ervoor te zorgen dat Microsoft Azure het beste platform is waarop uw SAP-workloads worden uitgevoerd. In de volgende tabellen worden de door Azure ondersteunde configuraties en de lijst met groeiende SAP-certificeringen beschreven. Deze lijst bevat een overzicht van lijsten die hier kunnen afwijken en uit de officiële SAP-lijsten. De gedetailleerde gegevens worden beschreven in het artikel [wat SAP-software ondersteunt voor Azure-implementaties](./sap-supported-product-on-azure.md)

## <a name="sap-hana-certifications"></a>SAP HANA-certificeringen
Referentie

- [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor SAP Hana ondersteuning voor systeem eigen Azure-VM'S en Hana grote instanties.

| SAP-product | Ondersteund besturingssysteem | Azure-aanbiedingen |
| --- | --- | --- |
| SAP HANA Developer Edition (inclusief de HANA-client software, bestaande uit SQLODBC, alleen ODBO-Windows, ODBC-, JDBC-Stuur Programma's, HANA Studio en HANA-data base) | Red Hat Enterprise Linux, SUSE Linux Enter prise | VM-serie van de D-serie |
| Bedrijf 1 in HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enter prise | Gecontroleerde Beschik baarheid voor GS5. Volledige ondersteuning voor M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA op Azure (grote instanties) [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite op HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enter prise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA op Azure (grote instanties) [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enter prise voor BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enter prise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2 SAP HANA op Azure (grote instanties) [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enter prise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA op Azure (grote exemplaren) <br /> [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Houd er rekening mee dat SAP de term ' clustering ' gebruikt in [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) als synoniem voor ' scale-out ' en niet voor een clustering met hoge Beschik baarheid

## <a name="sap-netweaver-certifications"></a>Certificeringen voor SAP net-Weaver
Microsoft Azure is gecertificeerd voor de volgende SAP-producten, met volledige ondersteuning van micro soft en SAP.
Referentie

- [1928533-SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen](https://launchpad.support.sap.com/#/notes/1928533) voor alle toepassingen op basis van SAP netweave, waaronder SAP TREX, SAP LIVECACHE en SAP-inhouds server. En alle data bases, met uitzonde ring van SAP HANA.


| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business Suite-software | Windows, SUSE Linux Enter prise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, D2as_v4 to D64as_v4, E2s_v3 to E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enter prise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, D2as_v4 to D64as_v4, E2s_v3 to E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |N.v.t. |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, D2as_v4 to D64as_v4, E2s_v3 to E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enter prise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, D2as_v4 to D64as_v4, E2s_v3 to E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andere SAP-werk belasting ondersteund op Azure

| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business One op SQL Server | Windows  | SQL Server | Alle typen netwerkweaver gecertificeerde VM<br /> [SAP-notitie #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10,01 MS SP08 | Windows en Linux | | Alle typen netwerkweaver gecertificeerde VM<br /> SAP-notitie #2451795 |
| SAP Business Objects BI-platform | Windows en Linux | | SAP-notitie #2145537 |
| SAP Data Services 4,2 | | | SAP-notitie #2288344 |
| SAP Hybris-commerce platform  | Windows | SQL Server, Oracle | Alle typen netwerkweaver gecertificeerde VM <br /> [Documentatie voor Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris-commerce platform  | SLES 12 of meer recent | SAP HANA | Alle typen netwerkweaver gecertificeerde VM <br /> [Documentatie voor Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris-commerce platform  | RHEL 7 of meer recent | SAP HANA | Alle typen netwerkweaver gecertificeerde VM <br /> [Hybris-documentatie]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) commerce platform 1811 en hoger  | Windows, SLES of RHEL | SQL Azure DB | Alle typen netwerkweaver gecertificeerde VM <br /> [Documentatie voor Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
