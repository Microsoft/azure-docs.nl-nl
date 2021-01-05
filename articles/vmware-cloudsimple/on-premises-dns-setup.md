---
title: Azure VMware-oplossing door CloudSimple-DNS configureren voor CloudSimple-Privécloud
description: Hierin wordt beschreven hoe u een DNS-naam omzetting instelt voor toegang tot de vCenter-Server op een CloudSimple-Privécloud vanuit on-premises werk stations
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899249"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>DNS configureren voor naam omzetting voor Private Cloud vCenter-toegang vanaf on-premises werk stations

Om toegang te krijgen tot de vCenter-Server op een CloudSimple Privécloud vanuit on-premises werk stations, moet u de DNS-adres omzetting configureren zodat de vCenter-Server kan worden geadresseerd aan de hand van hostnaam en IP-adres.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Het IP-adres van de DNS-server voor uw Privécloud ophalen

1. Meld u aan bij de [CloudSimple-Portal](access-cloudsimple-portal.md).

2. Navigeer naar   >  **persoonlijke Clouds** van resources en selecteer de privécloud waarmee u verbinding wilt maken.

3. Kopieer op de pagina **samen vatting** van de Privécloud onder **basis informatie** het IP-adres van de DNS-server van de privécloud.

    ![DNS-servers in privécloud](media/private-cloud-dns-server.png)


Gebruik een van deze opties voor de DNS-configuratie.

* [Maak een zone op de DNS-server voor *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Een voorwaardelijke doorstuur server maken op uw lokale DNS-servers om *. cloudsimple.io te kunnen oplossen](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Maak een zone op de DNS-server voor *. cloudsimple.io

U kunt een zone instellen als stub-zone en verwijzen naar de DNS-servers in de Privécloud voor naam omzetting. Deze sectie bevat informatie over het gebruik van een BIND-DNS-server of een micro soft Windows DNS-server.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Een zone maken op een BIND-DNS-server

Het specifieke bestand en de para meters die moeten worden geconfigureerd, kunnen variëren op basis van de afzonderlijke DNS-instellingen.

Voor de standaard configuratie van de BIND-server bewerkt u bijvoorbeeld/etc/named.conf-bestand op uw DNS-server en voegt u de volgende zone gegevens toe.

> [!NOTE]
>Dit artikel bevat verwijzingen naar de term slave, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Een zone maken op een micro soft Windows DNS-server

1. Klik met de rechter muisknop op de DNS-server en selecteer **nieuwe zone**. 
  
    ![Scherm afbeelding die de menu optie nieuwe zone markeert.](media/DNS01.png)
2. Selecteer **stub-zone** en klik op **volgende**.

    ![Scherm afbeelding die de optie stub-zone markeert.](media/DNS02.png)
3. Selecteer de gewenste optie, afhankelijk van uw omgeving en klik op **volgende**.

    ![Scherm opname van de opties voor zone gegevens replicatie.](media/DNS03.png)
4. Selecteer **zone voor forward lookup** en klik op **volgende**.

    ![Scherm afbeelding die de optie zone voor forward lookup markeert.](media/DNS01.png)
5. Voer de zone naam in en klik op **volgende**.

    ![Scherm opname van de locatie waar de zone naam moet worden ingevoerd.](media/DNS05.png)
6. Voer de IP-adressen in van de DNS-servers voor uw Privécloud die u hebt verkregen via de CloudSimple-Portal.

    ![Nieuwe zone](media/DNS06.png)
7. Klik op **volgende** als dat nodig is om de installatie van de wizard te volt ooien.

## <a name="create-a-conditional-forwarder"></a>Een voorwaardelijke doorstuur server maken

Een voorwaardelijke doorstuur server stuurt alle aanvragen voor DNS-naam omzetting door naar de aangewezen server. Bij deze installatie wordt elke aanvraag voor *. cloudsimple.io doorgestuurd naar de DNS-servers die zich in de Privécloud bevinden. In de volgende voor beelden ziet u hoe u doorstuur servers instelt op verschillende typen DNS-server.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Voorwaardelijke doorstuur servers maken op een BIND-DNS-server

Het specifieke bestand en de para meters die moeten worden geconfigureerd, kunnen variëren op basis van de afzonderlijke DNS-instellingen.

Bijvoorbeeld: voor de standaard configuratie van de BIND-server bewerkt u/etc/named.conf-bestand op uw DNS-server en voegt u de volgende informatie over voorwaardelijke door sturing toe.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Voorwaardelijke doorstuur servers maken op een micro soft Windows DNS-server

1. Open DNS-beheer op de DNS-server.
2. Klik met de rechter muisknop op **voorwaardelijke doorstuur servers** en selecteer de optie om een nieuwe voorwaardelijke doorstuur server toe te voegen.

    ![Voorwaardelijke doorstuur server 1 Windows DNS](media/DNS08.png)
3. Voer het DNS-domein en het IP-adres van de DNS-servers in de Privécloud in en klik op **OK**.
