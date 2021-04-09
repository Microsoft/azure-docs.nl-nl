---
title: Het draaien van certificaten voor Azure SQL Database & SQL Managed instance
description: Meer informatie over de aanstaande wijzigingen van basis certificaat wijzigingen die van invloed zijn op Azure SQL Database en Azure SQL Managed instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590848"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Informatie over de wijzigingen in de basis-CA-wijziging voor Azure SQL Database & SQL Managed instance

Azure SQL Database & SQL Managed instance wijzigt het basis certificaat voor de client toepassing/het stuur programma dat is ingeschakeld met SSL, dat wordt gebruikt om een veilige TDS-verbinding tot stand te brengen. Het [huidige basis certificaat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) is ingesteld op een verval datum van 26 oktober 2020 als onderdeel van de aanbevolen procedures voor standaard onderhoud en beveiliging. In dit artikel vindt u meer informatie over de aanstaande wijzigingen, de bronnen die worden beïnvloed en de stappen die nodig zijn om ervoor te zorgen dat uw toepassing verbinding met uw database server onderhoudt.

## <a name="what-update-is-going-to-happen"></a>Wat gebeurt er met de update?

Het browser forum van de certificerings [instantie (CA)](https://cabforum.org/) heeft onlangs rapporten gepubliceerd van meerdere certificaten die zijn uitgegeven door leveranciers van de certificerings instantie.

Conform de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie CA-certificaten voor niet-compatibele Ca's te intrekken, waardoor het voor servers vereist dat certificaten worden gebruikt die zijn uitgegeven door compatibele Ca's en ondertekend zijn door CA-certificaten van die compatibele certificerings instanties. Omdat Azure SQL Database & SQL Managed Instance momenteel een van deze niet-compatibele certificaten gebruikt, die client toepassingen gebruiken om hun SSL-verbindingen te valideren, moeten we ervoor zorgen dat de juiste acties worden ondernomen (hieronder beschreven) om de mogelijke gevolgen voor uw Azure SQL-servers te minimaliseren.

Het nieuwe certificaat wordt vanaf 26 oktober 2020 gebruikt. Als u de volledige validatie van het server certificaat gebruikt wanneer u verbinding maakt vanaf een SQL-client (TrustServerCertificate = True), moet u ervoor zorgen dat uw SQL-client het nieuwe basis certificaat kan valideren vóór 26 oktober 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Hoe kan ik weet of mijn toepassing kan worden beïnvloed?

Alle toepassingen die gebruikmaken van SSL/TLS en controleren of het basis certificaat het basis certificaat moet bijwerken om verbinding te maken met Azure SQL Database & SQL Managed instance. 

Als u momenteel geen gebruikmaakt van SSL/TLS, is er geen invloed op de beschik baarheid van uw toepassing. U kunt controleren of uw client toepassing het basis certificaat probeert te verifiëren door de connection string te bekijken. Als TrustServerCertificate expliciet is ingesteld op True, wordt dit niet beïnvloed.

Als uw client stuur programma het certificaat archief van het besturings systeem gebruikt, en het besturings systeem wordt regel matig bewaard, is dit waarschijnlijk niet van invloed op de wijziging, omdat het basis certificaat dat wij overschakelen, al beschikbaar moet zijn in uw vertrouwde basis certificaat archief. Controleer op Baltimore CyberDigiCert GlobalRoot G2 en controleer of deze aanwezig is.

Als uw client stuur programma het lokale certificaat archief van het bestand gebruikt om te voor komen dat de beschik baarheid van uw toepassing wordt onderbroken omdat de certificaten onverwacht zijn ingetrokken, of als u een certificaat wilt bijwerken dat is ingetrokken, raadpleegt u de sectie [**Wat heb ik nodig om de connectiviteit te behouden**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Wat heb ik nodig om de connectiviteit in stand te houden

Volg de onderstaande stappen om te voor komen dat de beschik baarheid van uw toepassing wordt onderbroken omdat de certificaten onverwacht zijn ingetrokken, of om een certificaat bij te werken dat is ingetrokken:

*   Down load Baltimore Cyber Trust Root & DigiCert GlobalRoot G2 root CA van onderstaande koppelingen:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Genereer een gecombineerd CA-certificaat archief met zowel **BaltimoreCyberTrustRoot** als **DigiCertGlobalRootG2** -certificaten.

## <a name="what-can-be-the-impact"></a>Wat kan de impact hebben?
Als u server certificaten valideert zoals hier wordt beschreven, kan de beschik baarheid van uw toepassing worden onderbroken omdat de data base niet bereikbaar is. Afhankelijk van uw toepassing kunnen er diverse fout berichten worden weer gegeven, inclusief, maar niet beperkt tot:
*   Ongeldig certificaat/ingetrokken certificaat
*   Time-out opgetreden voor verbinding
*   Fout, indien van toepassing

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Als ik SSL/TLS niet gebruik, moet ik dan nog steeds de basis-CA bijwerken?
Er zijn geen acties met betrekking tot deze wijziging vereist als u SSL/TLS niet gebruikt. U moet nog steeds een plan instellen om te beginnen met het gebruik van de meest recente TLS-versie bij het plannen van TLS-afdwinging in de nabije toekomst.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Wat gebeurt er als ik het basis certificaat niet bijwerk vóór 26 oktober 2020?
Als u het basis certificaat niet vóór 30 november 2020 bijwerkt, worden uw toepassingen die verbinding maken via SSL/TLS en verificatie voor het basis certificaat, niet kunnen communiceren met de Azure SQL Database & SQL Managed instance en de toepassing verbindings problemen ondervindt met uw Azure SQL Database & SQL Managed instance.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Moet ik een downtime voor onderhoud voor deze wijziging plannen?<BR>
Nee. Aangezien de wijziging hier alleen aan de client zijde wordt weer gegeven om verbinding te maken met de-server, is er geen downtime voor onderhoud nodig die u hier kunt volgen voor deze wijziging.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Wat moet ik doen als ik vóór 26 oktober 2020 geen geplande downtime voor deze wijziging krijg?
Omdat de clients die zijn gebruikt voor het maken van een verbinding met de server, de certificaat gegevens moeten bijwerken zoals beschreven in de sectie oplossing [hier](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), hoeven we in dit geval geen downtime voor de server te gebruiken.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Als ik na 30 november 2020 een nieuwe server maak, geldt dit?
Voor servers die zijn gemaakt na 26 oktober 2020, kunt u het zojuist uitgegeven certificaat voor uw toepassingen gebruiken om verbinding te maken via SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Hoe vaak werkt micro soft hun certificaten bij of wat is het verloop beleid?
Deze certificaten die worden gebruikt door Azure SQL Database & SQL Managed instance worden door vertrouwde certificerings instanties (CA) verschaft. De ondersteuning van deze certificaten op Azure SQL Database & SQL Managed instance is dus gebonden aan de ondersteuning van deze certificaten per CA. In dit geval kunnen er echter onvoorziene fouten voor komen in deze vooraf gedefinieerde certificaten, die op het eerst moeten worden opgelost.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Als ik lees replica's gebruik, moet ik deze update alleen op de primaire server of alle Lees replica's uitvoeren?
Omdat deze update een wijziging aan de client zijde is, moeten we ook de wijzigingen voor deze clients Toep assen als de client gebruikt voor het lezen van gegevens van de replica server. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Hebben we query's aan de server zijde om te controleren of SSL wordt gebruikt?
Omdat deze configuratie aan de client zijde is, zijn er geen gegevens beschikbaar aan de server zijde.

### <a name="what-if-i-have-further-questions"></a>Wat moet ik doen als ik meer vragen heb?
Als u een ondersteunings abonnement hebt en u technische hulp nodig hebt, kunt u een ondersteunings aanvraag voor Azure maken raadplegen om een ondersteunings [aanvraag voor Azure te maken](../../azure-portal/supportability/how-to-create-azure-support-request.md).