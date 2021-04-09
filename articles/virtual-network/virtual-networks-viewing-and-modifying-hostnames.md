---
title: Hostnamen weer geven en wijzigen | Microsoft Docs
description: Hostnamen weer geven en wijzigen voor Azure virtual machines, Web-en werk rollen voor naam omzetting
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222782"
---
# <a name="viewing-and-modifying-hostnames"></a>Hostnamen weer geven en wijzigen
Als u wilt toestaan dat naar de rolinstanties wordt verwezen door de hostnaam, moet u de waarde voor de hostnaam in het service configuratie bestand voor elke rol instellen. U doet dit door de gewenste hostnaam toe te voegen aan het kenmerk **vmName** van het element **Role** . De waarde van het kenmerk **vmName** wordt gebruikt als basis voor de hostnaam van elke rolinstantie. Als **vmName** bijvoorbeeld een *webrol* is en er drie exemplaren van die rol zijn, worden de hostnamen van de instanties *webrole0*, *webrole1* en *webrole2*. U hoeft geen hostnaam op te geven voor virtuele machines in het configuratie bestand, omdat de hostnaam van een virtuele machine is ingevuld op basis van de naam van de virtuele machine. Zie [Azure service configuration schema (cscfg-bestand)](/previous-versions/azure/reference/ee758710(v=azure.100)) voor meer informatie over het configureren van een Microsoft Azure-service.

## <a name="viewing-hostnames"></a>Hostnamen weer geven
U kunt de hostnamen van virtuele machines en rolinstanties in een Cloud service weer geven met behulp van de onderstaande hulpprogram ma's.

### <a name="service-configuration-file"></a>Service configuratie bestand
U kunt het service configuratie bestand voor een geïmplementeerde service downloaden via de Blade voor het **configureren** van de service in de Azure Portal. U kunt vervolgens zoeken naar het kenmerk **vmName** voor het element **rolnaam** om de hostnaam weer te geven. Houd er wel voor dat deze hostnaam wordt gebruikt als basis voor de hostnaam van elke rolinstantie. Als **vmName** bijvoorbeeld een *webrol* is en er drie exemplaren van die rol zijn, worden de hostnamen van de instanties *webrole0*, *webrole1* en *webrole2*.

### <a name="remote-desktop"></a>Extern bureaublad
Nadat u Extern bureaublad (Windows), Windows Power shell Remoting (Windows) of SSH-verbindingen (Linux en Windows) naar uw virtuele machines of rolinstanties hebt ingeschakeld, kunt u de hostnaam van een actieve Extern bureaublad verbinding op verschillende manieren bekijken:

* Typ hostname bij de opdracht prompt of SSH-terminal.
* Typ ipconfig/all bij de opdracht prompt (alleen Windows).
* Bekijk de computer naam in de systeem instellingen (alleen Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management-REST API
Voer de volgende instructies uit vanuit een REST-client:

1. Zorg ervoor dat u een client certificaat hebt om verbinding te maken met de Azure Portal. Als u een client certificaat wilt verkrijgen, volgt u de stappen in [procedure: publicatie-instellingen en abonnements gegevens downloaden en importeren](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70)). 
2. Stel een header-vermelding met de naam x-MS-version in met de waarde 2013-11-01.
3. Een aanvraag verzenden in de volgende indeling: https: \/ /Management.core.Windows.net/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? Embed-detail = True
4. Zoek naar het **hostname** -element voor elk **RoleInstance** -element.

> [!WARNING]
> U kunt ook het achtervoegsel van het interne domein voor uw Cloud service weer geven op basis van het antwoord van de REST-aanroep door het **InternalDnsSuffix** -element te controleren of door ipconfig/all uit te voeren vanaf een opdracht prompt in een extern bureaublad-sessie (Windows) of door cat/etc/resolv.conf uit te voeren vanaf een SSH-terminal (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Een hostnaam wijzigen
U kunt de hostnaam voor elke virtuele machine of rolinstantie wijzigen door een gewijzigd service configuratie bestand te uploaden of door de naam van de computer te wijzigen in een Extern bureaublad sessie.

## <a name="next-steps"></a>Volgende stappen
[Naam omzetting (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Configuratie schema van Azure-service (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Configuratie schema voor Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))

[DNS-instellingen opgeven met behulp van netwerk configuratie bestanden](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)