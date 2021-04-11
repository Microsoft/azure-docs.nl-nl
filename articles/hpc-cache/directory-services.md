---
title: Uitgebreide groepen gebruiken in de Azure HPC-cache
description: Adreslijst Services configureren voor client toegang tot opslag doelen in azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563366"
---
# <a name="configure-directory-services"></a>Adreslijst Services configureren

Met de instellingen van **Directory Services** kan uw Azure HPC-cache een externe bron gebruiken om gebruikers te verifiëren voor toegang tot back-end-opslag.

Mogelijk moet u **uitgebreide groepen** inschakelen als uw werk stroom NFS-opslag doelen bevat en clients die lid zijn van meer dan 16 groepen.

Nadat u op de knop hebt geklikt om uitgebreide groepen in te scha kelen, moet u de bron kiezen die door Azure HPC cache wordt gebruikt om gebruikers-en groeps referenties op te halen.

* [Active Directory](#configure-active-directory) -referenties ophalen van een externe Active Directory-server. U kunt Azure Active Directory voor deze taak niet gebruiken.
* [Plat bestand](#configure-file-download) : down load `/etc/group` en `/etc/passwd` bestanden van een netwerk locatie.
* [LDAP](#configure-ldap) -referenties ophalen van een LDAP-compatibele bron (Lightweight Directory Access Protocol).

> [!NOTE]
> Zorg ervoor dat uw cache toegang kan krijgen tot de gegevens bron van de groep in het beveiligde subnetwerk.<!-- + details/examples -->

Het veld **gebruikers naam gedownload** bevat de status van de meest recente down load van de groeps gegevens.

![scherm afbeelding van de pagina pagina-instellingen van Directory Services in de portal, met de optie Ja geselecteerd voor uitgebreide groepen en de vervolg keuzelijst gelabelde Download bron open](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Active Directory configureren

In deze sectie wordt uitgelegd hoe u de cache instelt om gebruikers-en groeps referenties op te halen van een externe Active Directory (AD) server.

Geef onder **Active Directory-details** de volgende waarden op:

* **Primair DNS** : Geef het IP-adres van een domeinnaam server op die de cache kan gebruiken om de AD-domein naam op te lossen.

* **Secundaire DNS** (optioneel): Voer het adres in van een naam server die moet worden gebruikt als de primaire server niet beschikbaar is.

* **AD DNS-domein naam** : geef de Fully Qualified Domain name van de ad-server waaraan de cache wordt toegevoegd om de referenties op te halen.

* **Cache server naam (computer account)** : Stel de naam in die wordt toegewezen aan deze HPC-cache wanneer deze lid wordt van het AD-domein. Geef een naam op die gemakkelijk te herkennen is als deze cache. De naam mag Maxi maal 15 tekens lang zijn en kan bestaan uit hoofd letter-of kleine letters, cijfers en afbreek streepjes (-).

Geef in de sectie **referenties** een gebruikers naam en wacht woord voor de AD-beheerder op die de Azure HPC-cache kan gebruiken om toegang te krijgen tot de ad-server. Deze informatie wordt versleuteld wanneer deze wordt opgeslagen en kan niet worden opgevraagd.

Sla de instellingen op door te klikken op de knop boven aan de pagina.

![scherm afbeelding van de sectie Download Details met Active Directory waarden ingevuld](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Downloaden van bestanden configureren

Deze waarden zijn vereist als u bestanden wilt downloaden met uw gebruikers-en groeps gegevens. De bestanden moeten de standaard Linux/UNIX en de `/etc/group` `/etc/passwrd` indeling hebben.

* **URI van gebruikers bestand** : Voer de volledige URI voor het `/etc/passwrd` bestand in.
* **Groeps bestand-URI** : Voer de volledige URI voor het `/etc/group` bestand in.

![scherm afbeelding van Download Details sectie voor het downloaden van een plat bestand](media/group-download-details-file.png)

## <a name="configure-ldap"></a>LDAP configureren

Vul deze waarden in als u een niet-AD-LDAP-bron wilt gebruiken om gebruikers-en groeps referenties op te halen. Neem contact op met uw LDAP-beheerder als u hulp nodig hebt bij deze waarden.

* **LDAP-server** : voer de Fully Qualified Domain name of het IP-adres van de te gebruiken LDAP-server in. <!-- only one, not up to 3 -->

* **LDAP-basis-DN** : Geef de DN-naam op voor het LDAP-domein in DN-indeling. Vraag uw LDAP-beheerder als u uw basis-DN niet weet.

De gegevens van de server en de basis-DN zijn de enige vereiste instellingen voor het maken van LDAP-werk, maar de extra opties maken uw verbinding veiliger.

![scherm afbeelding van het LDAP-configuratie gebied van de pagina pagina-instellingen van Directory Services](media/group-download-details-ldap.png)

In de sectie **beveiligde toegang** kunt u versleuteling en certificaat validatie inschakelen voor de LDAP-verbinding. Nadat u op **Ja** hebt geklikt om versleuteling in te scha kelen, hebt u de volgende opties:

* **Certificaat valideren** : wanneer dit is ingesteld, wordt het certificaat van de LDAP-server geverifieerd aan de hand van de certificerings instantie in het onderstaande URI-veld.

* **CA-certificaat-URI** : Geef het pad op naar het gezaghebbende certificaat. Dit kan een koppeling zijn naar een CA-gevalideerd certificaat of een zelfondertekend certificaat. Dit veld is vereist voor het gebruik van de instelling voor extern gevalideerde certificaten.

* **Certificaat automatisch downloaden** : Kies **Ja** als u een certificaat wilt downloaden zodra u deze instellingen verzendt.

Vul de sectie **referenties** in als u statische referenties wilt gebruiken voor LDAP-beveiliging. Deze informatie wordt versleuteld wanneer deze wordt opgeslagen en kan niet worden opgevraagd.

* **Binding DN** : Geef de DN-naam op die moet worden gebruikt om de LDAP-server te verifiëren. (Gebruik DN-indeling.)
* **Bindings wachtwoord** : Geef het wacht woord op voor de binding-DN.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over client toegang in [de Azure HPC-cache koppelen](hpc-cache-mount.md)
* Als uw referenties niet correct worden gedownload, neemt u contact op met de beheerder voor uw bron van referenties. Open zo nodig een [ondersteunings ticket](hpc-cache-support-ticket.md) .
