---
title: Meer informatie over de nieuwste versies van het Azure-gast besturingssysteem | Microsoft Docs
description: De nieuwste versie nieuws en SDK-compatibiliteit voor Azure Cloud Services-gast besturingssysteem.
services: cloud-services
documentationcenter: na
author: yohaddad
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 1/15/2021
ms.author: yohaddad
ms.openlocfilehash: 6c3d9f6858ced1838e062084ba08de8953e7990f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250897"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure Guest OS releases en SDK Compatibility Matrix
Voorziet in actuele informatie over de nieuwste versies van het Azure-gast besturingssysteem voor Cloud Services. Deze informatie helpt u bij het plannen van het upgradepad voordat een gast besturingssysteem wordt uitgeschakeld. Als u uw rollen configureert voor het gebruik van *automatische* updates van gast besturingssystemen zoals beschreven in de update-instellingen van het [Azure-gast besturingssysteem][Azure Guest OS Update Settings], is het niet belang rijk dat u deze pagina leest.

> [!IMPORTANT]
> Deze pagina is van toepassing op Cloud Services Web-en werk rollen, die bovenop een gast besturingssysteem worden uitgevoerd. Het is **niet van toepassing** op IaaS virtual machines.
>
>


> [!TIP]
>  Abonneer u op de RSS-feed voor het [gast besturingssysteem] om de meest recente melding over alle wijzigingen in het gast besturingssysteem te ontvangen.
>
>

> [!IMPORTANT]
> Alleen de nieuwste twee versies van het gast besturingssysteem worden ondersteund en zijn beschikbaar in de Azure Portal.
>
>

Weet u niet zeker hoe u uw gast besturingssysteem kunt bijwerken? Bekijk [Dit][cloud updates] uit.

## <a name="news-updates"></a>Nieuws updates

###### <a name="january-15-2021"></a>**15 januari 2021**
Het besturings systeem december gast is uitgebracht. 

###### <a name="december-19-2020"></a>**19 december 2020**
Het besturings systeem november gast is uitgebracht. 

###### <a name="november-17-2020"></a>**17 november 2020**
Het besturings systeem van oktober gast is uitgebracht. 

###### <a name="october-10-2020"></a>**10 oktober 2020**
Het besturings systeem september gast is uitgebracht. 

###### <a name="september-5-2020"></a>**5 september 2020**
Het besturings systeem voor augustus gast is uitgebracht. 

###### <a name="august-17-2020"></a>**17 augustus 2020**
Het besturings systeem van juli wordt uitgebracht. 

###### <a name="august-10-2020"></a>**10 augustus 2020**
Het besturings systeem van juni gast is uitgebracht. 

###### <a name="june-2-2020"></a>**2 juni 2020**
Het kan zijn dat het besturings systeem gast is losgelaten. 

###### <a name="may-4-2020"></a>**4 mei 2020**
Het besturings systeem van april gast is uitgebracht. 

###### <a name="april-2-2020"></a>**2 april 2020**
Het besturings systeem maart gast is uitgebracht. 

###### <a name="march-5-2020"></a>**5 maart 2020**
Het besturings systeem februari gast is uitgebracht. 

###### <a name="january-24-2020"></a>**24 januari 2020**
Het besturings systeem voor januari gast is uitgebracht. 

###### <a name="january-8-2020"></a>**8 januari 2020**
Het besturings systeem december gast is uitgebracht.

###### <a name="december-5-2019"></a>**5 december 2019**
Het besturings systeem november gast is uitgebracht.

###### <a name="november-1-2019"></a>**1 november 2019**
Het besturings systeem van oktober gast is uitgebracht.

###### <a name="october-7-2019"></a>**7 oktober 2019**
Het besturings systeem september gast is uitgebracht.

###### <a name="september-4-2019"></a>**4 september 2019**
Het besturings systeem voor augustus gast is uitgebracht.

###### <a name="july-26-2019"></a>**26 juli 2019**
Het besturings systeem van juli wordt uitgebracht.

###### <a name="july-8-2019"></a>**8 juli 2019**
Het besturings systeem van juni gast is uitgebracht.

###### <a name="june-6-2019"></a>**6 juni 2019**
Het kan zijn dat het besturings systeem gast is losgelaten.

###### <a name="may-7-2019"></a>**7 mei 2019**
Het besturings systeem van april gast is uitgebracht.

###### <a name="march-26-2019"></a>**26 maart 2019**
Het besturings systeem maart gast is uitgebracht.

###### <a name="march-12-2019"></a>**12 maart 2019**
Het besturings systeem februari gast is uitgebracht.

###### <a name="february-5-2019"></a>**5 februari 2019**
Het besturings systeem voor januari gast is uitgebracht.

###### <a name="january-24-2019"></a>**24 januari 2019**
Family 6-gast besturingssysteem (Windows Server 2019) is uitgebracht.

###### <a name="january-7-2019"></a>**7 januari 2019**
Het besturings systeem december gast is uitgebracht.

###### <a name="december-14-2018"></a>**14 december 2018**
Het besturings systeem november gast is uitgebracht.

###### <a name="november-8-2018"></a>**8 november 2018**
Het besturings systeem van oktober gast is uitgebracht.

###### <a name="october-12-2018"></a>**12 oktober 2018**
Het besturings systeem september gast is uitgebracht.

## <a name="releases"></a>Releases

## <a name="family-6-releases"></a>Versies van familie 6
**Windows Server 2019**

.NET Framework geïnstalleerd: 3,5, 4.7.2

> [!NOTE]
> De Windows Azure SDK voor .NET-3,0 kan [hier][Windows Azure SDK]worden gedownload.
>
>Installatie stappen:
>1. Verwijder alle oudere versies van MicrosoftAzureAuthoringTools*. msi
>2. De [Azure SDK voor .net-3,0][Windows Azure SDK] installeren
>3. De computer opnieuw opstarten
>4. Een nieuw Cloud service project maken en één worker-rol toevoegen
>5. De besturingssysteem familie wijzigen in 6 en een pakket bouwen
>6. Het pakket implementeren op Azure met behulp van de Azure Portal of Visual Studio
>
>De release van het gast besturingssysteem Family 6 dwingt TLS 1,2 af door TLS 1,0 en 1,1 expliciet uit te scha kelen en een specifieke set coderings suites te definiëren. [Meer]informatie.


| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-6.26 _202012-01 |  15 januari 2021  |  Post 6,28  |  
|  WA-GUEST-OS-6,25 _202011-01 |  19 december 2020  |  Post 6,27  |  
|~~WA-GUEST-OS-6.24 _202010-02~~|  17 november 2020  |  15 januari 2021  |  
|~~WA-GUEST-OS-6.23 _202009-01~~|  10 oktober 2020  |  19 december 2020  |  
|~~WA-GUEST-OS-6.22 _202008-02~~|  5 september 2020  |  17 november 2020  |  
|~~WA-GUEST-OS-6.21 _202007-01~~|  17 augustus 2020  |  10 oktober 2020  |  
|~~WA-GUEST-OS-6.20 _202006-02~~|  10 augustus 2020  |  5 september 2020  |  
|~~WA-GUEST-OS-6.19 _202005-02~~|  2 juni 2020  |  17 augustus 2020  |  
|~~WA-GUEST-OS-6.18 _202004-01~~|  4 mei 2020  |  10 augustus 2020  |  
|~~WA-GUEST-OS-6.17 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA-GUEST-OS-6.16 _202002-01~~|  5 maart 2020  |  4 mei 2020  |  
|~~WA-GUEST-OS-6.15 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA-GUEST-OS-6.14 _201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-6.13 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-6,12 _201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-6.11 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-6.10 _201908-01~~| 4 augustus 2019 | 1 november 2019  |  
|~~WA-GUEST-OS-6,9 _201907-0~~|26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-6,8 _201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-6,7 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GAST BESTURINGSSYSTEEM-6,6 _201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-6.5 _201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-6.4 _201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-6.3 _201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GAST BESTURINGSSYSTEEM-6.2 _201812-01~~ |24 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-6.1 _201811-01~~ |24 januari 2019 |5 februari 2019 |

## <a name="family-5-releases"></a>Versies van familie 5
**Windows Server 2016**

.NET Framework geïnstalleerd: 3,5, 4.6.2

> [!NOTE]
> Het RDP-wacht woord voor besturingssysteem familie 5 moet mini maal 10 tekens bevatten.
>


| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-5.50 _202012-01  |  15 januari 2021  |  Post 5,52  | 
|  WA-GUEST-OS-5.49 _202011-01  |  19 december 2020  |  Post 5,51  | 
|~~WA-GUEST-OS-5.48 _202010-02~~|  17 november 2020  |  15 januari 2021  | 
|~~WA-GUEST-OS-5.47 _202009-01~~|  10 oktober 2020  |  19 december 2020  | 
|~~WA-GUEST-OS-5.46 _202008-02~~|  5 september 2020  |  17 november 2020  |  
|~~WA-GUEST-OS-5.45 _202007-01~~|  17 augustus 2020  |  10 oktober 2020  |  
|~~WA-GUEST-OS-5.44 _202006-02~~|  10 augustus 2020  |  5 september 2020  |  
|~~WA-GUEST-OS-5.43 _202005-02~~|  2 juni 2020  |  17 augustus 2020  |  
|~~WA-GUEST-OS-5.42 _202004-01~~|  4 mei 2020  |  10 augustus 2020  |  
|~~WA-GUEST-OS-5.41 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA-GUEST-OS-5.40 _202002-01~~|  5 maart 2020  |  4 mei 2020  |  
|~~WA-GUEST-OS-5.39 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA-GUEST-OS-5.38 _201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-5.37 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-5.36 _201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-5.35 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-5.34 _201908-01~~|  4 augustus 2019  | 1 november 2019 |  
|~~WA-GUEST-OS-5.33 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |  
|~~WA-GUEST-OS-5.32 _201906-01~~|8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-5.31 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-5.30 _201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-5.29 _201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-5.28 _201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-5.27 _201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-5.26 _201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GAST BESTURINGSSYSTEEM-5,25 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-5.24 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-5.23 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-4-releases"></a>Releases van familie 4
**Windows Server 2012 R2**

.NET Framework geïnstalleerd: 3,5, 4.5.1, 4.5.2

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-4.85 _202012-01  |  15 januari 2021  |  Post 4,87  | 
|  WA-GUEST-OS-4.84 _202011-01  |  19 december 2020  |  Post 4,86  | 
|~~WA-GUEST-OS-4.83 _202010-02~~|  17 november 2020  |  15 januari 2021  | 
|~~WA-GUEST-OS-4.82 _202009-01~~|  10 oktober 2020  |  19 december 2020  | 
|~~WA-GUEST-OS-4.81 _202008-02~~|  5 september 2020  |  17 november 2020  | 
|~~WA-GUEST-OS-4.80 _202007-01~~|  17 augustus 2020  |  10 oktober 2020  | 
|~~WA-GUEST-OS-4.79 _202006-02~~|  10 augustus 2020  |  5 september 2020  | 
|~~WA-GUEST-OS-4.78 _202005-02~~|  2 juni 2020  |  17 augustus 2020  |  
|~~WA-GUEST-OS-4.77 _202004-01~~|  4 mei 2020  |  10 augustus 2020  |  
|~~WA-GUEST-OS-4.76 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA-GUEST-OS-4.75 _202002-01~~|  5 maart 2020  |  4 mei 2020  |  
|~~WA-GUEST-OS-4.74 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA-GUEST-OS-4.73 _201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-4.72 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-4.71 _201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-4.70 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-4.69 _201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-4.68 _201907-01~~| 26 juli 2019  | 7 oktober 2019 |
|~~WA-GUEST-OS-4.67 _201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-4.66 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-4.65 _201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-4.64 _201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-4.63 _201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-4.62 _201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-4.61 _201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-4.60 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-4.59 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-4.58 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-3-releases"></a>Versies van familie 3
**Windows Server 2012**

.NET Framework geïnstalleerd: 3,5, 4,5

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-3.92 _202012-01  |  15 januari 2021  |  Post 3,94  |  
|  WA-GUEST-OS-3.91 _202011-01  |  19 december 2020  |  Post 3,93  |  
|~~WA-GUEST-OS-3.90 _202010-02~~|  17 november 2020  |  15 januari 2021  |  
|~~WA-GUEST-OS-3.89 _202009-01~~|  10 oktober 2020  |  19 december 2020  |  
|~~WA-GUEST-OS-3.88 _202008-02~~|  5 september 2020  |  17 november 2020  |  
|~~WA-GUEST-OS-3.87 _202007-01~~|  17 augustus 2020  |  10 oktober 2020  |  
|~~WA-GUEST-OS-3.86 _202006-02~~|  10 augustus 2020  |  5 september 2020  |  
|~~WA-GUEST-OS-3.85 _202005-02~~|  2 juni 2020  |  17 augustus 2020  |  
|~~WA-GUEST-OS-3.84 _202004-01~~|  4 mei 2020  |  10 augustus 2020  |  
|~~WA-GUEST-OS-3.83 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA-GUEST-OS-3.82 _202002-01~~|  5 maart 2020  |  4 mei 2020  |  
|~~WA-GUEST-OS-3.81 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA-GUEST-OS-3.80 _201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-3.79 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-3.78 _201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-3.77 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-3.76 _201908-01~~|  4 augustus 2019  |  1 november 2019  |  
|~~WA-GAST BESTURINGSSYSTEEM-3,75 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-3.74 _201906-01~~| 8 juli 2019 |4 augustus 2019 |
|~~WA-GUEST-OS-3.73 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-3.72 _201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-3.71 _201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GAST BESTURINGSSYSTEEM-3.70 _201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-3.69 _201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-3.68 _201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-3.67 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-3.66 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-3.65 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="family-2-releases"></a>Versies van familie 2
**Windows Server 2008 R2 SP1**

.NET Framework geïnstalleerd: 3,5 (inclusief 2,0 en 3,0), 4,5

| Configuratie teken reeks | Releasedatum | Datum uitschakelen |
| --- | --- | --- |
|  WA-GUEST-OS-2.105 _202012-01  |  15 januari 2021  |  Post 2,107  |  
|  WA-GUEST-OS-2.104 _202011-01  |  19 december 2020  |  Post 2,106  |  
|~~WA-GUEST-OS-2.103 _202010-02~~|  17 november 2020  |  15 januari 2021  |  
|~~WA-GUEST-OS-2.102 _202009-01~~|  10 oktober 2020  |  19 december 2020  |  
|~~WA-GUEST-OS-2.101 _202008-02~~|  5 september 2020  |  17 november 2020 |    
|~~WA-GUEST-OS-2.100 _202007-01~~|  17 augustus 2020  |  10 oktober 2020  |  
|~~WA-GUEST-OS-2.99 _202006-02~~|  10 augustus 2020  | 5 september 2020  |  
|~~WA-GUEST-OS-2.98 _202005-02~~|  2 juni 2020  |  17 augustus 2020  |  
|~~WA-GUEST-OS-2.97 _202004-01~~|  4 mei 2020  |  10 augustus 2020  |  
|~~WA-GUEST-OS-2.96 _202003-01~~|  2 april 2020  |  2 juni 2020  |  
|~~WA-GUEST-OS-2.95 _202002-01~~|  5 maart 2020  |  4 mei 2020  |  
|~~WA-GUEST-OS-2.94 _202001-01~~|  24 januari 2020  |  2 april 2020  |  
|~~WA-GUEST-OS-2.93 _201912-01~~| 8 januari 2020 | 5 maart 2020 |  
|~~WA-GUEST-OS-2.92 _201911-01~~| 5 december 2019 | 24 januari 2020 |  
|~~WA-GUEST-OS-2.91 _201910-01~~| 1 november 2019 | 8 januari 2020 |  
|~~WA-GUEST-OS-2.90 _201909-01~~| 7 oktober 2019 | 5 december 2019 |  
|~~WA-GUEST-OS-2.89 _201908-01~~| 4 augustus 2019 | 1 november 2019 |  
|~~WA-GUEST-OS-2,88 _201907-01~~| 26 juli 2019 | 7 oktober 2019 |
|~~WA-GUEST-OS-2.87 _201906-01~~|8 juli 2019 | 4 augustus 2019 |
|~~WA-GUEST-OS-2.86 _201905-01~~ |6 juni 2019 |26 juli 2019 |
|~~WA-GUEST-OS-2.85 _201904-01~~ |7 mei 2019 |8 juli 2019 |
|~~WA-GUEST-OS-2.84 _201903-01~~ |26 maart 2019 |6 juni 2019 |
|~~WA-GUEST-OS-2.83 _201902-01~~ |12 maart 2019 |7 mei 2019 |
|~~WA-GUEST-OS-2.82 _201901-01~~ |5 februari 2019 |26 maart 2019 |
|~~WA-GUEST-OS-2.81 _201812-01~~ |7 januari 2019 |12 maart 2019 |
|~~WA-GUEST-OS-2,80 _201811-01~~ |14 december 2018 |5 februari 2019 |
|~~WA-GUEST-OS-2.79 _201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-2.78 _201809-01~~ |12 oktober 2018 |14 december 2018 |

## <a name="msrc-patch-updates"></a>Updates van MSRC-patch
De lijst met patches die zijn opgenomen in elke maandelijkse versie van het gast besturingssysteem, is [hier][patches]beschikbaar.

## <a name="sdk-support"></a>SDK-ondersteuning
Hoewel het [pensionerings beleid voor de Azure SDK][retire policy sdk] aangeeft dat alleen de versies hoger dan 2,2 worden ondersteund, kunt u met specifieke gast besturingssysteem families eerdere versies gebruiken. U moet altijd de meest recente ondersteunde SDK gebruiken.

| Gast besturingssysteem familie | Compatibele SDK-versies |
| --- | --- |
| 6 |Versie 2.9.6 + |
| 5 |Versie 2.9.5.1 + |
| 4 |Versie 2.1 + |
| 3 |Versie 1.8 + |
| 2 |Versie 1.3 + |
| 1 |Versie 1.0 + |

## <a name="guest-os-release-information"></a>Release-informatie voor het gast besturingssysteem
Er zijn drie datums die belang rijk zijn voor de releases van gast besturingssystemen: **release** datum, **Uitgeschakelde** datum en **verval** datum. Een gast besturingssysteem wordt beschouwd als beschikbaar wanneer het zich in de portal bevindt en kan worden geselecteerd als het doel-gast besturingssysteem. Wanneer een gast besturingssysteem de **Uitgeschakelde** datum bereikt, wordt het verwijderd uit Azure. Elke Cloud service die gericht is op het gast besturingssysteem, blijft echter gewoon functioneren.

In het venster tussen de **Uitgeschakelde** datum en de **verval** datum kunt u eenvoudig van het ene naar het andere gast besturingssysteem overstappen. Als u *automatisch* gebruikt als uw gast besturingssysteem, hebt u altijd de nieuwste versie en hoeft u zich geen zorgen te maken over het verlopen van het besturings systeem.

Wanneer de **verloop** datum wordt verstreken, wordt een Cloud service die nog steeds gebruikmaakt van dat gast besturingssysteem, gestopt, verwijderd of gedwongen bijgewerkt. U kunt [hier][retirepolicy]meer lezen over het pensionerings beleid.

## <a name="guest-os-family-version-explanation"></a>Gast besturingssysteem familie-versie uitleg
De gast besturingssysteem families zijn gebaseerd op release versies van micro soft Windows Server. Het gast besturingssysteem is het onderliggende besturings systeem waarop Azure Cloud Services worden uitgevoerd. Elk gast besturingssysteem heeft een familie, versie en release nummer.

* **Gast besturingssysteem familie**  
  Een versie van het Windows Server-besturings systeem waarop een gast besturingssysteem is gebaseerd. Bijvoorbeeld: *Family 3* is gebaseerd op Windows Server 2012.
* **Versie van gast besturingssysteem**  
  Specifiek voor een installatie kopie van een gast besturingssysteem-serie en relevante [MSRC-patches (micro soft Security Response Center)][msrc] die beschikbaar zijn op de datum waarop de nieuwe versie van het gast besturingssysteem is geproduceerd. Mogelijk zijn niet alle patches opgenomen.

    Nummers beginnen bij 0 en elke keer dat er een nieuwe set updates wordt toegevoegd, verhoogd met 1. Volg nullen worden alleen weer gegeven als belang rijk. Dat wil zeggen versie 2,10 is een andere, veel latere versie dan versie 2,1.
* **Release van gast besturingssysteem**  
  Een heruitgave van een versie van een gast besturingssysteem. Een heruitgave treedt op als micro soft problemen tijdens het testen vindt; wijzigingen moeten worden aangebracht. De meest recente versie heeft altijd voor rang op eerdere releases, openbaar of niet. Met de Azure Portal kunnen gebruikers alleen de meest recente release voor een bepaalde versie kiezen. Implementaties die op een eerdere versie worden uitgevoerd, worden doorgaans niet geforceerd bijgewerkt, afhankelijk van de ernst van de fout.

In het onderstaande voor beeld is 2 de familie, 12 de versie en ' rel2 ' is de release.

**Release van gast besturingssysteem** -2,12 rel2

**Configuratie teken reeks voor deze release** -wa-Guest-OS-2.12 _201208-02

In de configuratie teken reeks voor een gast besturingssysteem zijn dezelfde gegevens Inge sloten, samen met een datum waarop wordt aangegeven welke MSRC-patches voor die release werden overwogen. In dit voor beeld worden de MSRC-patches die voor Windows Server 2008 R2 zijn geproduceerd tot en met augustus 2012, opgenomen. Er zijn alleen patches opgenomen die specifiek van toepassing zijn op die versie van Windows Server. Als een MSRC-patch bijvoorbeeld van toepassing is op Microsoft Office, wordt deze niet opgenomen omdat dat product geen deel uitmaakt van de basis installatie kopie van Windows Server.

## <a name="guest-os-system-update-process"></a>Update proces voor het gast besturingssysteem systeem
Deze pagina bevat informatie over aanstaande versies van het gast besturingssysteem. Klanten hebben aangegeven dat ze willen weten wanneer een release plaatsvindt omdat hun Cloud service rollen opnieuw worden opgestart als ze zijn ingesteld op ' automatische ' update. Gast besturingssysteem releases vinden doorgaans 2-3 weken na de release van de MSRC-update die op de tweede dinsdag van elke maand plaatsvindt. Nieuwe releases bevatten alle relevante MSRC-patches voor elke gast besturingssysteem familie.

De updates worden voortdurend door Microsoft Azure vrijgegeven. Het gast besturingssysteem is slechts één update in de pijp lijn. Een release kan worden beïnvloed door een groot aantal factoren die hier kunnen worden weer gegeven. Bovendien wordt Azure op duizenden computers uitgevoerd op meer dan duizend tallen. Dit betekent dat het niet mogelijk is om een exacte datum en tijd te geven wanneer uw rol (len) opnieuw wordt opgestart. Er wordt gewerkt aan een abonnement om het opnieuw opstarten te beperken of in te stellen.

Wanneer een nieuwe versie van het gast besturingssysteem wordt gepubliceerd, kan het enige tijd duren voordat Azure wordt door gegeven. Wanneer Services worden bijgewerkt naar het nieuwe gast besturingssysteem, worden ze opnieuw opgestart met update domeinen. Services die zijn ingesteld voor het gebruik van automatische updates, krijgen eerst een release. Na de update wordt de nieuwe versie van het gast besturingssysteem voor uw service weer gegeven in de Azure Portal. Tijdens deze periode kunnen er opnieuw uitgebrachte versies plaatsvinden. Sommige versies kunnen meer tijd in beslag nemen en het automatisch opnieuw opstarten van de upgrade wordt mogelijk niet veel weken na de officiële release datum uitgevoerd. Zodra een gast besturingssysteem beschikbaar is, kunt u de desbetreffende versie expliciet kiezen in de portal of in uw configuratie bestand.

Zie het MSDN-blog bericht met de titel [instantie wordt opnieuw opgestart vanwege upgrades van het besturings systeem][restarts]voor een grote hoeveelheid informatie over het opnieuw opstarten en verwijzingen naar meer informatie technische details van gast-en host OS-updates.

Als u het gast besturingssysteem hand matig bijwerkt, raadpleegt u het [uittredings beleid voor gast besturingssysteem][retirepolicy] voor aanvullende informatie.

## <a name="guest-os-supportability-and-retirement-policy"></a>Ondersteunings beleid voor gast besturingssystemen en buiten gebruik stellen
Het beleid voor de ondersteuning van gast besturingssystemen en het buiten gebruik stellen wordt [hier][retirepolicy]uitgelegd.

[cloud updates]: ./cloud-services-update-azure-service.md
[RSS-feed voor het gast besturingssysteem bijwerken]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: ./cloud-services-dotnet-install-dotnet.md?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: /security-updates/SecurityAdvisories/2015/3009008
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: /security-updates/SecurityBulletins/2014/ms14-066
[MS14-046]: /security-updates/SecurityBulletins/2014/ms14-046
[retire policy sdk]: /previous-versions/azure/reference/dn479282(v=azure.100)
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: /archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: /security-updates/SecurityBulletins/2017/ms17-010
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[grotere]: ./applications-dont-support-tls-1-2.md
