---
title: De draaiing van het certificaat voor Azure Database for MariaDB
description: Meer informatie over de aanstaande wijzigingen van basis certificaat wijzigingen die van invloed zijn op Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 105bc7f14f9ddcc4a64564edc1eebcd17b898bc6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698991"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Informatie over de wijzigingen in de basis-CA-wijziging voor Azure Database for MariaDB

Azure Database for MariaDB de wijziging van het basis certificaat op **15 februari 2021 (02/15/2021)** hebt voltooid als onderdeel van de best practices voor standaard onderhoud en-beveiliging. In dit artikel vindt u meer informatie over de wijzigingen, de betrokken resources en de stappen die nodig zijn om ervoor te zorgen dat uw toepassing verbinding met uw database server onderhoudt.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

## <a name="why-root-certificate-update-is-required"></a>Waarom is het bijwerken van het basis certificaat vereist?

Azure data base for MariaDB-gebruikers kunnen het vooraf gedefinieerde certificaat alleen gebruiken om verbinding te maken met een Azure Database for MariaDB-server, die [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)zich bevindt. Het [browser forum van Certificate Authority (CA)](https://cabforum.org/)   heeft echter onlangs rapporten gepubliceerd van meerdere certificaten die zijn uitgegeven door leveranciers van de certificerings instantie om niet-compatibel te zijn.

Conform de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie CA-certificaten voor niet-compatibele Ca's te intrekken, waardoor het voor servers vereist dat certificaten worden gebruikt die zijn uitgegeven door compatibele Ca's en ondertekend zijn door CA-certificaten van die compatibele certificerings instanties. Sinds Azure Database for MariaDB een van deze niet-compatibele certificaten heeft gebruikt, moesten we het certificaat naar de compatibele versie draaien om de mogelijke dreiging van uw MySQL-servers tot een minimum te beperken.

Het nieuwe certificaat wordt uitgevoerd vanaf 15 februari 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Welke wijziging is uitgevoerd op 15 februari 2021 (02/15/2021)?

Op 15 februari 2021 is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) vervangen door een **compatibele versie** van hetzelfde [BaltimoreCyberTrustRoot-basis certificaat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) om ervoor te zorgen dat bestaande klanten niets hoeven te wijzigen en zijn er geen gevolgen voor de verbindingen met de server. Tijdens deze wijziging is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **niet vervangen** door [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) en wordt deze wijziging uitgesteld zodat klanten meer tijd kunnen aangaan om de wijziging aan te brengen.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Moet ik wijzigingen aanbrengen op mijn client om de connectiviteit te behouden?

Er is geen wijziging vereist aan de client zijde. Als u onze vorige aanbeveling hieronder hebt gevolgd, kunt u nog steeds verbinding maken zolang het **BaltimoreCyberTrustRoot-certificaat niet is verwijderd** uit het gecombineerde CA-certificaat. **U wordt aangeraden de BaltimoreCyberTrustRoot niet uit uw gecombineerde CA-certificaat te verwijderen tot u meer kennisgeving hebt over het onderhouden van de connectiviteit.**

### <a name="previous-recommendation"></a>Vorige aanbeveling

- Down load **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** CA via onderstaande koppelingen:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Genereer een gecombineerd CA-certificaat archief met zowel **BaltimoreCyberTrustRoot** als **DigiCertGlobalRootG2** -certificaten.

  - Voor Java-gebruikers (MariaDB Connector/J) voert u het volgende uit:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Vervang vervolgens het bestand van de oorspronkelijke opslag plaats door de nieuwe, gegenereerde versie:

    - System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
    - System. setProperty ("javax. net. SSL. trustStorePassword", "wacht woord");

  - Voor .NET-gebruikers (MariaDB Connector/NET, MariaDBConnector) moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in Windows-certificaat archief, vertrouwde basis certificerings instanties. Als er geen certificaten bestaan, importeert u het ontbrekende certificaat.

    [![Azure Database for MariaDB .net-certificaat](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - Voor .NET-gebruikers op Linux met behulp van SSL_CERT_DIR, moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in de map die wordt aangegeven door SSL_CERT_DIR. Als er geen certificaten bestaan, maakt u het ontbrekende certificaat bestand.

  - Voor andere gebruikers (MariaDB client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) kunt u twee CA-certificaat bestanden als volgt samen voegen:

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Vervang het oorspronkelijke PEM-bestand van de basis-CA door het gecombineerde basis-CA-bestand en start de toepassing/client opnieuw.
- Nadat het nieuwe certificaat op de server is geïmplementeerd, kunt u in de toekomst het PEM-bestand van de CA wijzigen in DigiCertGlobalRootG2. CRT. pem.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Waarom is het BaltimoreCyberTrustRoot-certificaat tijdens deze wijziging op 15 februari 2021 niet vervangen door DigiCertGlobalRootG2?

We hebben de gereedheid van de klant voor deze wijziging geëvalueerd en veel klanten hebben op zoek naar extra doorloop tijd om deze wijziging te beheren. In het belang van het leveren van meer lever tijd aan klanten voor de gereedheid, hebben we besloten de wijziging van het certificaat uit te stellen op DigiCertGlobalRootG2 voor ten minste een jaar dat voldoende lever tijd aan de klanten en eind gebruikers levert. 

Onze aanbevelingen voor gebruikers is, met behulp van de volgende stappen om een gecombineerd certificaat te maken en te verbinden met uw server, maar het BaltimoreCyberTrustRoot-certificaat niet te verwijderen totdat we een communicatie hebben verzonden om het te verwijderen. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Wat gebeurt er als we het BaltimoreCyberTrustRoot-certificaat hebben verwijderd?

U begint verbinding te maken met de verbinding met uw Azure Database for MariaDB-server. U moet SSL met [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat opnieuw [configureren](howto-configure-ssl.md) om de connectiviteit te behouden.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. als ik SSL/TLS niet gebruik, moet ik dan nog steeds de basis-CA bijwerken?

Er zijn geen acties vereist als u SSL/TLS niet gebruikt.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. als ik SSL/TLS gebruik, moet ik mijn database server opnieuw starten om de basis-CA bij te werken?

Nee, u hoeft de database server niet opnieuw op te starten om het nieuwe certificaat te gebruiken. Certificaat update is een wijziging aan de client zijde en de binnenkomende client verbindingen moeten het nieuwe certificaat gebruiken om ervoor te zorgen dat ze verbinding kunnen maken met de database server.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Hoe kan ik weet ik of ik SSL/TLS gebruik met verificatie van het basis certificaat?

U kunt bepalen of uw verbindingen het basis certificaat verifiëren door uw connection string te controleren.

- Als uw connection string bevat `sslmode=verify-ca` of `sslmode=verify-identity` , moet u het certificaat bijwerken.
- Als uw Connection String bevat `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` , of `sslmode=require` , hoeft u geen certificaten bij te werken.
- Als uw connection string geen sslmode opgeeft, hoeft u geen certificaten bij te werken.

Als u een client gebruikt die de connection string wegsamen vatting, raadpleegt u de documentatie van de client om te begrijpen of de certificaten worden geverifieerd.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Wat is de impact van het gebruik van App Service met Azure Database for MariaDB?

Voor Azure app Services is het maken van een verbinding met Azure Database for MariaDB twee mogelijke scenario's, afhankelijk van de manier waarop u SSL met uw toepassing gebruikt.

- Dit nieuwe certificaat is toegevoegd aan App Service op platform niveau. Als u de SSL-certificaten gebruikt die zijn opgenomen in App Service platform in uw toepassing, is er geen actie vereist. Dit is het meest voorkomende scenario. 
- Als u het pad naar het SSL-certificaat bestand in uw code expliciet opneemt, moet u het nieuwe certificaat downloaden en de code bijwerken om het nieuwe certificaat te gebruiken. Een goed voor beeld van dit scenario is wanneer u aangepaste containers in App Service gebruikt zoals gedeeld in de [app service documentatie](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Dit is een ongebruikelijk scenario, maar er zijn enkele gebruikers die hiervoor gebruikmaken van.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Wat is de impact van het gebruik van Azure Kubernetes Services (AKS) met Azure Database for MariaDB?

Als u probeert verbinding te maken met de Azure Database for MariaDB met behulp van Azure Kubernetes Services (AKS), is dit vergelijkbaar met de toegang vanuit een specifieke host-omgeving van klanten. Raadpleeg de stappen [hier](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Wat is de impact van Azure Data Factory om verbinding te maken met Azure Database for MariaDB?

Voor connector die gebruikmaakt van Azure Integration Runtime gebruikt de connector certificaten in het Windows-certificaat archief in de op Azure gehoste omgeving. Deze certificaten zijn al compatibel met de zojuist toegepaste certificaten. u hoeft dus geen actie te ondernemen.

Voor connectors die gebruikmaken van zelf-hostende Integration Runtime waarbij u het pad naar het SSL-certificaat bestand in uw connection string expliciet opneemt, moet u het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) downloaden en de Connection String bijwerken om het te gebruiken.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. moet ik de downtime van een database server onderhoud voor deze wijziging plannen?

Nee. Aangezien de wijziging hier alleen aan de client zijde wordt weer gegeven om verbinding te maken met de database server, is er geen uitval tijd nodig voor de database server voor deze wijziging.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. als ik een nieuwe server Maak na 15 februari 2021 (02/15/2021), wordt dit van invloed?

Voor servers die zijn gemaakt na 15 februari 2021 (02/15/2021), blijft u de [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) voor uw toepassingen gebruiken om verbinding te maken met behulp van SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. hoe vaak werkt micro soft hun certificaten bij of wat is het verloop beleid?

Deze certificaten die worden gebruikt door Azure Database for MariaDB worden door vertrouwde certificerings instanties (CA) verschaft. De ondersteuning van deze certificaten is dus gebonden aan de ondersteuning van deze certificaten per CA. Het [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat is gepland om te verlopen in 2025. micro soft moet een certificaat wijziging vóór de verval datum uitvoeren. Als er sprake is van onvoorziene fouten in deze vooraf gedefinieerde certificaten, moet micro soft de draaiing van het certificaat zo snel mogelijk maken, vergelijkbaar met de wijziging die op 15 februari 2021 wordt uitgevoerd om ervoor te zorgen dat de service te allen tijde is beveiligd en compatibel is.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. als ik replica's gebruik, moet ik deze update alleen uitvoeren op de bron server of de Lees replica's?

Omdat deze update een wijziging aan de client zijde is, moet u ook de wijzigingen voor deze clients Toep assen als de client gebruikt om gegevens te lezen van de replica-server.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. als ik replicatie van gegevens in gebruik, moet ik dan elke actie uitvoeren?

- Als de gegevens replicatie van een virtuele machine (on-premises of Azure virtual machine) naar Azure Database for MySQL is, moet u controleren of SSL wordt gebruikt om de replica te maken. Voer de **status van slave weer geven** uit en controleer de volgende instelling.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Als u [gegevens replicatie](concepts-data-in-replication.md) gebruikt om verbinding te maken met Azure database for MySQL, moet u rekening houden met twee dingen:

- Als de gegevens replicatie van een virtuele machine (on-premises of Azure virtual machine) naar Azure Database for MySQL is, moet u controleren of SSL wordt gebruikt om de replica te maken. Voer de **status van slave weer geven** uit en controleer de volgende instelling. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Als het certificaat wordt weer gegeven voor de CA_file, SSL_Cert en SSL_Key, moet u het bestand bijwerken door het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) toe te voegen en een gecombineerd certificaat bestand te maken.

- Als de gegevens replicatie tussen twee Azure Database for MySQL ligt, moet u de replica opnieuw instellen door het **aanroepen van MySQL.az_replication_change_master** uit te voeren en het nieuwe dubbele basis certificaat als laatste para meter op te geven [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. hebben we een query aan de server zijde om te controleren of SSL wordt gebruikt?

Raadpleeg [SSL-verificatie](howto-configure-ssl.md#verify-the-ssl-connection)om te controleren of u SSL-verbinding gebruikt om verbinding te maken met de server.

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. is er een actie vereist als ik DigiCertGlobalRootG2 al in mijn certificaat bestand heb?

Nee. Er is geen actie nodig als uw certificaat bestand al het **DigiCertGlobalRootG2** heeft.

### <a name="14-what-if-i-have-further-questions"></a>14. Wat moet ik doen als ik meer vragen heb?

Als u vragen hebt, kunt u antwoorden krijgen van experts van community's in [micro soft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). Als u een ondersteunings abonnement hebt en technische hulp nodig hebt, kunt u [contact met ons](mailto:AzureDatabaseformariadb@service.microsoft.com)opnemen.
