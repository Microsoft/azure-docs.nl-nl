---
title: De draaiing van het certificaat voor Azure Database for PostgreSQL één server
description: Meer informatie over de aanstaande wijzigingen van basis certificaat wijzigingen die van invloed zijn op Azure Database for PostgreSQL één server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123986"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Informatie over de wijzigingen in de basis-CA-wijziging voor Azure Database for PostgreSQL één server

Azure Database for PostgreSQL Eén server heeft de wijziging van het basis certificaat op **15 februari 2021 (02/15/2021)** voltooid als onderdeel van de best practices voor standaard onderhoud en-beveiliging. In dit artikel vindt u meer informatie over de wijzigingen, de betrokken resources en de stappen die nodig zijn om ervoor te zorgen dat uw toepassing verbinding met uw database server onderhoudt.

## <a name="why-root-certificate-update-is-required"></a>Waarom is het bijwerken van het basis certificaat vereist?

Azure data base for PostgreSQL-gebruikers kunnen alleen het vooraf gedefinieerde certificaat gebruiken om verbinding te maken met hun PostgreSQL-server, die [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)zich bevindt. Het [browser forum van Certificate Authority (CA)](https://cabforum.org/)   heeft echter onlangs rapporten gepubliceerd van meerdere certificaten die zijn uitgegeven door leveranciers van de certificerings instantie om niet-compatibel te zijn.

Conform de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie CA-certificaten voor niet-compatibele Ca's te intrekken, waardoor het voor servers vereist dat certificaten worden gebruikt die zijn uitgegeven door compatibele Ca's en ondertekend zijn door CA-certificaten van die compatibele certificerings instanties. Sinds Azure Database for MySQL een van deze niet-compatibele certificaten heeft gebruikt, moesten we het certificaat naar de compatibele versie draaien om de mogelijke dreiging van uw MySQL-servers tot een minimum te beperken.

Het nieuwe certificaat wordt uitgevoerd vanaf 15 februari 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Welke wijziging is uitgevoerd op 15 februari 2021 (02/15/2021)?

Op 15 februari 2021 is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) vervangen door een **compatibele versie** van hetzelfde [BaltimoreCyberTrustRoot-basis certificaat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) om ervoor te zorgen dat bestaande klanten niets hoeven te wijzigen en zijn er geen gevolgen voor de verbindingen met de server. Tijdens deze wijziging is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **niet vervangen** door [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) en wordt deze wijziging uitgesteld zodat klanten meer tijd kunnen aangaan om de wijziging aan te brengen.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Moet ik wijzigingen aanbrengen op mijn client om de connectiviteit te behouden?

Er is geen wijziging vereist aan de client zijde. Als u onze vorige aanbeveling hieronder hebt gevolgd, kunt u nog steeds verbinding maken zolang het **BaltimoreCyberTrustRoot-certificaat niet is verwijderd** uit het gecombineerde CA-certificaat. **U wordt aangeraden de BaltimoreCyberTrustRoot niet uit uw gecombineerde CA-certificaat te verwijderen tot u meer kennisgeving hebt over het onderhouden van de connectiviteit.**

### <a name="previous-recommendation"></a>Vorige aanbeveling

*   Down load BaltimoreCyberTrustRoot & DigiCertGlobalRootG2-basis certificerings instantie via onderstaande koppelingen:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Genereer een gecombineerd CA-certificaat archief met zowel **BaltimoreCyberTrustRoot** als **DigiCertGlobalRootG2** -certificaten.
    *   Voor Java-gebruikers (PostgreSQL JDBC) die gebruikmaken van DefaultJavaSSLFactory, voert u de volgende handelingen uit:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Vervang vervolgens het bestand van de oorspronkelijke opslag plaats door de nieuwe, gegenereerde versie:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "wacht woord");
        
    *   Voor .NET (Npgsql)-gebruikers in Windows, moet u ervoor zorgen dat **Baltimore Cyber Trust Root** en **DigiCert Global root G2** beide aanwezig zijn in Windows-certificaat archief, vertrouwde basis certificerings instanties. Als er geen certificaten bestaan, importeert u het ontbrekende certificaat.

        ![Azure Database for PostgreSQL .net-certificaat](media/overview/netconnecter-cert.png)

    *   Voor .NET (Npgsql)-gebruikers op Linux met SSL_CERT_DIR, moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in de map die wordt aangegeven door SSL_CERT_DIR. Als er geen certificaten bestaan, maakt u het ontbrekende certificaat bestand.

    *   Voor andere PostgreSQL-client gebruikers kunt u twee CA-certificaat bestanden als onderstaande indeling samen voegen

        </br>-----BEGIN CERTIFICAAT-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----EIND CERTIFICAAT-----
 </br>-----BEGIN CERTIFICAAT-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----EIND CERTIFICAAT-----

*   Vervang het oorspronkelijke PEM-bestand van de basis-CA door het gecombineerde basis-CA-bestand en start de toepassing/client opnieuw.
*    Nadat het nieuwe certificaat op de server is geïmplementeerd, kunt u in de toekomst het PEM-bestand van de CA wijzigen in DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Verwijder het **Baltimore-certificaat** niet en pas het pas toe nadat het certificaat is gewijzigd. Er wordt een communicatie verzonden zodra de wijziging is aangebracht, waarna deze de Baltimore-certificaat kan verwijderen. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Waarom is het BaltimoreCyberTrustRoot-certificaat tijdens deze wijziging op 15 februari 2021 niet vervangen door DigiCertGlobalRootG2?

We hebben de gereedheid van de klant voor deze wijziging geëvalueerd en veel klanten hebben op zoek naar extra doorloop tijd om deze wijziging te beheren. In het belang van het leveren van meer lever tijd aan klanten voor de gereedheid, hebben we besloten de wijziging van het certificaat uit te stellen op DigiCertGlobalRootG2 voor ten minste een jaar dat voldoende lever tijd aan de klanten en eind gebruikers levert. 

Onze aanbevelingen voor gebruikers is, met behulp van de volgende stappen om een gecombineerd certificaat te maken en te verbinden met uw server, maar het BaltimoreCyberTrustRoot-certificaat niet te verwijderen totdat we een communicatie hebben verzonden om het te verwijderen. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Wat gebeurt er als we het BaltimoreCyberTrustRoot-certificaat hebben verwijderd?

U begint verbinding te maken met de verbinding met uw Azure Database for PostgreSQL-server. U moet SSL met [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat opnieuw configureren om de connectiviteit te behouden.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. als ik SSL/TLS niet gebruik, moet ik dan nog steeds de basis-CA bijwerken?
Er zijn geen acties vereist als u SSL/TLS niet gebruikt. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. als ik SSL/TLS gebruik, moet ik mijn database server opnieuw starten om de basis-CA bij te werken?
Nee, u hoeft de database server niet opnieuw op te starten om het nieuwe certificaat te gebruiken. Dit is een wijziging aan de client zijde en de binnenkomende client verbindingen moeten het nieuwe certificaat gebruiken om ervoor te zorgen dat ze verbinding kunnen maken met de database server.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Hoe kan ik weet ik of ik SSL/TLS gebruik met verificatie van het basis certificaat?

U kunt bepalen of uw verbindingen het basis certificaat verifiëren door uw connection string te controleren.
-    Als uw connection string bevat `sslmode=verify-ca` of `sslmode=verify-full` , moet u het certificaat bijwerken.
-    Als uw Connection String bevat `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` , of `sslmode=require` , hoeft u geen certificaten bij te werken. 
-    Als uw connection string geen sslmode opgeeft, hoeft u geen certificaten bij te werken.

Als u een-client gebruikt die het connection string verwijderd, raadpleegt u de documentatie van de client om te begrijpen of de certificaten worden geverifieerd. Zie de beschrijvingen van de [SSL-modus](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) in de postgresql-documentatie voor meer informatie over postgresql sslmode.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. Wat is de impact van het gebruik van App Service met Azure Database for PostgreSQL?
Voor Azure app Services, het maken van verbinding met Azure Database for PostgreSQL, kunnen we twee mogelijke scenario's hebben. Dit is afhankelijk van hoe u SSL met uw toepassing gebruikt.
*   Dit nieuwe certificaat is toegevoegd aan App Service op platform niveau. Als u de SSL-certificaten gebruikt die zijn opgenomen in App Service platform in uw toepassing, is er geen actie vereist.
*   Als u expliciet het pad naar het SSL-certificaat bestand in uw code opneemt, moet u het nieuwe certificaat downloaden en de code bijwerken om het nieuwe certificaat te gebruiken. Een goed voor beeld van dit scenario is wanneer u aangepaste containers in App Service gebruikt zoals gedeeld in de [app service documentatie](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. Wat is de impact van het gebruik van Azure Kubernetes Services (AKS) met Azure Database for PostgreSQL?
Als u probeert verbinding te maken met de Azure Database for PostgreSQL met behulp van Azure Kubernetes Services (AKS), is dit vergelijkbaar met de toegang vanuit een specifieke host-omgeving van klanten. Raadpleeg de stappen [hier](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. Wat is de impact van Azure Data Factory om verbinding te maken met Azure Database for PostgreSQL?
Voor connector met Azure Integration Runtime gebruikt de connector certificaten in het Windows-certificaat archief in de op Azure gehoste omgeving. Deze certificaten zijn al compatibel met de zojuist toegepaste certificaten en daarom is er geen actie vereist.

Voor connectors die gebruikmaken van zelf-hostende Integration Runtime waarbij u het pad naar het SSL-certificaat bestand in uw connection string expliciet opneemt, moet u het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) downloaden en de Connection String bijwerken om het te gebruiken.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. moet ik de downtime van een database server onderhoud voor deze wijziging plannen?
Nee. Omdat de wijziging hier alleen aan de client zijde wordt weer gegeven om verbinding te maken met de database server, is er geen uitval tijd nodig voor de database server voor deze wijziging.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. als ik een nieuwe server Maak na 15 februari 2021 (02/15/2021), wordt dit van invloed?
Voor servers die zijn gemaakt na 15 februari 2021 (02/15/2021), blijft u de [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) voor uw toepassingen gebruiken om verbinding te maken met behulp van SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. hoe vaak werkt micro soft hun certificaten bij of wat is het verloop beleid?
Deze certificaten die worden gebruikt door Azure Database for PostgreSQL worden door vertrouwde certificerings instanties (CA) verschaft. De ondersteuning van deze certificaten is dus gebonden aan de ondersteuning van deze certificaten per CA. Het [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat is gepland om te verlopen in 2025. micro soft moet een certificaat wijziging vóór de verval datum uitvoeren. Als er sprake is van onvoorziene fouten in deze vooraf gedefinieerde certificaten, moet micro soft de draaiing van het certificaat zo snel mogelijk maken, vergelijkbaar met de wijziging die op 15 februari 2021 wordt uitgevoerd om ervoor te zorgen dat de service te allen tijde is beveiligd en compatibel is.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. als ik lees replica's gebruik, moet ik deze update alleen op de primaire server of de Lees replica's uitvoeren?
Omdat deze update een wijziging aan de client zijde is, moet u de wijzigingen voor deze clients ook Toep assen als de client de gegevens van de replica server heeft gelezen. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. hebben we een query aan server zijde om te controleren of SSL wordt gebruikt?
Raadpleeg [SSL-verificatie](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)om te controleren of u SSL-verbinding gebruikt om verbinding te maken met de server.

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. is er een actie vereist als ik DigiCertGlobalRootG2 al in mijn certificaat bestand heb?
Nee. Er is geen actie vereist als uw certificaat bestand al het **DigiCertGlobalRootG2** heeft.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. Wat gebeurt er als u docker-installatie kopie van PgBouncer-zijspan van micro soft gebruikt?
[Hieronder vindt](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) u een nieuwe docker-installatie kopie die zowel [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) als [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ondersteunt. U kunt deze nieuwe installatie kopie ophalen om onderbrekingen te voor komen in connectiviteit vanaf 15 februari 2021. 

### <a name="14-what-if-i-have-further-questions"></a>14. Wat moet ik doen als ik meer vragen heb?
Als u vragen hebt, kunt u antwoorden krijgen van experts van community's in [micro soft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Als u een ondersteunings abonnement hebt en technische hulp nodig hebt, kunt u [contact met ons](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) opnemen
