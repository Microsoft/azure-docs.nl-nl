---
title: App Service Environment netwerken
description: App Service Environment netwerk gegevens
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 680b1f3b6af186eba27a4dd926016a04cd863760
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013482"
---
# <a name="app-service-environment-networking"></a>App Service Environment netwerken

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v3 (preview-versie)
> 

De App Service Environment (ASE) is een implementatie van één Tenant van de Azure App Service die als host fungeert voor web apps, API apps en functie-apps. Wanneer u een ASE installeert, kiest u de Azure-Virtual Network (VNet) die u wilt implementeren in. Alle binnenkomende en uitgaande verkeers toepassingen bevindt zich in het VNet dat u opgeeft.  

De ASEv3 gebruikt twee subnetten.  Er wordt één subnet gebruikt voor het persoonlijke eind punt dat inkomend verkeer afhandelt. Dit kan een al bestaand subnet zijn of een nieuwe.  Het inkomende subnet kan worden gebruikt voor andere doel einden dan het persoonlijke eind punt. Het uitgaande subnet kan alleen worden gebruikt voor uitgaand verkeer van de ASE. Niets anders kan het ASE-uitgaand subnet gaan.

## <a name="addresses"></a>Adressen 
De ASE heeft de volgende adressen bij het maken:

| Adres type | beschrijving |
|--------------|-------------|
| Binnenkomend adres | Het inkomende adres is het adres van het privé-eind punt dat wordt gebruikt door uw ASE. |
| Uitgaand subnet | Het uitgaande subnet is ook het ASE-subnet. Tijdens de preview-periode wordt dit subnet alleen gebruikt voor uitgaand verkeer. |
| Windows uitgaand adres | De Windows-apps in deze ASE maken standaard gebruik van dit adres wanneer uitgaande oproepen naar Internet worden gemaakt. |
| Uitgaand Linux-adres | De Linux-apps in deze ASE gebruiken standaard dit adres bij het maken van uitgaande oproepen naar het internet. |

De ASEv3 bevat details over de adressen die worden gebruikt door de ASE in het gedeelte **IP-adressen** van de ASE-Portal.

![ASE-adressen in gebruikers interface](./media/networking/networking-ip-addresses.png)

Als u het persoonlijke eind punt verwijdert dat door de ASE wordt gebruikt, is het niet mogelijk om de apps in uw ASE te bereiken.  

De ASE gebruikt adressen in het uitgaande subnet ter ondersteuning van de infra structuur die wordt gebruikt door de ASE. Wanneer u uw App Service-abonnementen in uw ASE schaalt, gebruikt u meer adressen. Apps in de ASE hebben geen toegewezen adressen in het uitgaande subnet. De adressen die worden gebruikt door een app in het uitgaande subnet van een app, worden in de loop van de tijd gewijzigd.

## <a name="ports"></a>Poorten

De ASE ontvangt toepassings verkeer op de poorten 80 en 443.  Er zijn geen binnenkomende of uitgaande poort vereisten voor de ASE. 

## <a name="extra-configurations"></a>Extra configuraties

In tegens telling tot de ASEv2 kunt u met ASEv3 netwerk beveiligings groepen (Nsg's) en route tabellen (Udr's) zo instellen dat ze zonder beperking overeenkomen. Als u alle uitgaande verkeer van uw ASE naar een NVA-apparaat wilt tunnelen. U kunt WAF-apparaten vóór al het binnenkomende verkeer naar uw ASE plaatsen. 

## <a name="dns"></a>DNS

De apps in uw ASE gebruiken de DNS waarvoor uw VNet is geconfigureerd. Volg de instructies in [een app service Environment gebruiken](https://docs.microsoft.com/azure/app-service/environment/using#dns-configuration) om uw DNS-server te laten verwijzen naar uw ASE. Als u wilt dat sommige apps een andere DNS-server gebruiken dan uw VNet is geconfigureerd met, kunt u deze hand matig instellen op basis van de app-instellingen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER. Met de app-instelling WEBSITE_DNS_ALT_SERVER configureert u de secundaire DNS-server. De secundaire DNS-server wordt alleen gebruikt wanneer er geen reactie is van de primaire DNS-server. 

## <a name="preview-limitation"></a>Voor beeld van beperking

Er zijn een aantal netwerk functies die niet beschikbaar zijn in ASEv3.  De dingen die niet beschikbaar zijn in ASEv3 zijn:

• FTP • externe fout opsporing • externe load balancer implementatie • de mogelijkheid om toegang te krijgen tot een persoonlijk container register voor container implementaties • de mogelijkheid om aanroepen naar een wereld wijd peered Vnets • de mogelijkheid om back-ups te maken/te herstellen met een service-eind punt of beveiligde opslag met een persoonlijk eind punt account • de mogelijkheid om met behulp van de sleutel kluis verwijzingen van de app-instellingen op service-eind punten of beveiligde BYOS te gebruiken • de mogelijkheid om een service-eind punt of een beveiligde opslag account voor een persoonlijk eind punt gebruiken • gebruik van Network Watcher of NSG stroom op uitgaand verkeer
    
    

