---
title: De draaiing van het certificaat voor Azure Database for MySQL
description: Meer informatie over de aanstaande wijzigingen van basis certificaat wijzigingen die van invloed zijn op Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313361"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Informatie over de wijzigingen in de basis-CA-wijziging voor Azure Database for MySQL één server

Azure Database for MySQL Eén server heeft de wijziging van het basis certificaat op **15 februari 2021 (02/15/2021)** voltooid als onderdeel van de best practices voor standaard onderhoud en-beveiliging. In dit artikel vindt u meer informatie over de wijzigingen, de betrokken resources en de stappen die nodig zijn om ervoor te zorgen dat uw toepassing verbinding met uw database server onderhoudt.

> [!NOTE]
> Dit artikel is alleen van toepassing op [Azure database for MySQL-één server](single-server-overview.md) . Voor [Azure database for MySQL-flexibele server](flexible-server/overview.md)is het certificaat dat nodig is voor communicatie via SSL [DigiCert globale basis certificerings instantie](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
>
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Waarom is een basis certificaat update vereist?

Azure Database for MySQL gebruikers kunnen het vooraf gedefinieerde certificaat alleen gebruiken om verbinding te maken met de MySQL-server, die [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)zich bevindt. Het [browser forum van Certificate Authority (CA)](https://cabforum.org/)   heeft echter onlangs rapporten gepubliceerd van meerdere certificaten die zijn uitgegeven door leveranciers van de certificerings instantie om niet-compatibel te zijn.

Op basis van de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie de CA-certificaten voor niet-compatibele Ca's te intrekken, waardoor de servers certificaten moeten gebruiken die zijn uitgegeven door compatibele Ca's en moeten worden ondertekend door CA-certificaten van die compatibele certificerings instanties. Sinds Azure Database for MySQL een van deze niet-compatibele certificaten heeft gebruikt, moesten we het certificaat naar de compatibele versie draaien om de mogelijke dreiging van uw MySQL-servers tot een minimum te beperken.

Het nieuwe certificaat wordt uitgerold en is van kracht op 15 februari 2021 (02/15/2021).

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Welke wijziging is uitgevoerd op 15 februari 2021 (02/15/2021)?

Op 15 februari 2021 is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) vervangen door een **compatibele versie** van hetzelfde [BaltimoreCyberTrustRoot-basis certificaat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) om ervoor te zorgen dat bestaande klanten niets hoeven te wijzigen en zijn er geen gevolgen voor de verbindingen met de server. Tijdens deze wijziging is het [basis certificaat BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **niet vervangen** door [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) en wordt deze wijziging uitgesteld zodat klanten meer tijd kunnen aangaan om de wijziging aan te brengen.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Moet ik wijzigingen aanbrengen op mijn client om de connectiviteit te behouden?

Er is geen wijziging vereist aan de client zijde. Als u onze vorige aanbeveling hieronder hebt gevolgd, kunt u blijven verbinding maken zolang het **BaltimoreCyberTrustRoot-certificaat niet is verwijderd** uit het gecombineerde CA-certificaat. **Als u de connectiviteit wilt behouden, wordt u aangeraden de BaltimoreCyberTrustRoot in uw gecombineerde CA-certificaat te bewaren tot verdere kennisgeving.**

###### <a name="previous-recommendation"></a>Vorige aanbeveling

Gebruik de volgende stappen om te voor komen dat de beschik baarheid van uw toepassing wordt onderbroken als gevolg van een onverwacht aantal certificaten of een update van een ingetrokken certificaat. Het is een goed idee om een nieuw *. pem* -bestand te maken, waarin het huidige certificaat en de nieuwe en tijdens de validatie van het SSL-certificaat worden gecombineerd, een van de toegestane waarden wordt gebruikt. Raadpleeg de volgende stappen:

1. Down load BaltimoreCyberTrustRoot & DigiCertGlobalRootG2-basis-CA van de volgende koppelingen:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Genereer een gecombineerd CA-certificaat archief met zowel **BaltimoreCyberTrustRoot** als **DigiCertGlobalRootG2** -certificaten.

    * Voor Java-gebruikers (MySQL-Connector/J) voert u de volgende handelingen uit:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Vervang vervolgens het bestand van de oorspronkelijke opslag plaats door de nieuwe, gegenereerde versie:

      * System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
      * System. setProperty ("javax. net. SSL. trustStorePassword", "wacht woord");

    * Voor .NET-gebruikers (MySQL-Connector/NET, MySQLConnector) moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in Windows-certificaat archief, vertrouwde basis certificerings instanties. Als er geen certificaten bestaan, importeert u het ontbrekende certificaat.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL .NET-certificaat diagram":::

    * Voor .NET-gebruikers op Linux met behulp van SSL_CERT_DIR, moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in de map die wordt aangegeven door SSL_CERT_DIR. Als er geen certificaten bestaan, maakt u het ontbrekende certificaat bestand.

    * Voor andere gebruikers (MySQL client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) kunt u twee CA-certificaat bestanden samen voegen in de volgende indeling:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Vervang het oorspronkelijke PEM-bestand van de basis-CA door het gecombineerde basis-CA-bestand en start de toepassing/client opnieuw.

   Wanneer het nieuwe certificaat in de toekomst op de server is geïmplementeerd, kunt u het PEM-bestand van de CA wijzigen in DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Verwijder het **Baltimore-certificaat** niet en pas het pas toe nadat het certificaat is gewijzigd. Er wordt een communicatie verzonden nadat de wijziging is aangebracht, en het is dan veilig om het Baltimore- **certificaat** te verwijderen.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Waarom is het BaltimoreCyberTrustRoot-certificaat tijdens deze wijziging op 15 februari 2021 niet vervangen door DigiCertGlobalRootG2?

We hebben de gereedheid van de klant geëvalueerd voor deze wijziging en gerealiseerd dat veel klanten op zoek zijn naar extra doorloop tijd om deze wijziging te beheren. Om klanten meer doorloop tijd voor gereedheid te bieden, hebben we besloten de wijziging van het certificaat voor ten minste een jaar uit te stellen op DigiCertGlobalRootG2, waardoor er voldoende lever tijd aan de klanten en eind gebruikers wordt geboden.

Onze aanbeveling voor gebruikers is de voor noemde stappen te gebruiken om een gecombineerd certificaat te maken en te verbinden met uw server, maar het BaltimoreCyberTrustRoot-certificaat niet te verwijderen totdat we een communicatie hebben verzonden om het te verwijderen.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Wat gebeurt er als we het BaltimoreCyberTrustRoot-certificaat hebben verwijderd?

U kunt verbindings fouten tegen komen wanneer u verbinding maakt met uw Azure Database for MySQL server. U moet SSL met het [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat opnieuw [configureren](howto-configure-ssl.md) om de connectiviteit te behouden.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Als ik SSL/TLS niet gebruik, moet ik dan nog steeds de basis-CA bijwerken?

  Er zijn geen acties vereist als u SSL/TLS niet gebruikt.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Als ik SSL/TLS gebruik, moet ik mijn database server opnieuw starten om de basis-CA bij te werken?

Nee, u hoeft de database server niet opnieuw op te starten om het nieuwe certificaat te gebruiken. Dit basis certificaat is een wijziging aan de client zijde en de binnenkomende client verbindingen moeten het nieuwe certificaat gebruiken om ervoor te zorgen dat ze verbinding kunnen maken met de database server.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Hoe kan ik weet ik of ik SSL/TLS gebruik met verificatie van het basis certificaat?

U kunt bepalen of uw verbindingen het basis certificaat verifiëren door uw connection string te controleren.

* Als uw connection string bevat `sslmode=verify-ca` of `sslmode=verify-identity` , moet u het certificaat bijwerken.
* Als uw Connection String bevat `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` , of `sslmode=require` , hoeft u geen certificaten bij te werken.
* Als uw connection string geen sslmode opgeeft, hoeft u geen certificaten bij te werken.

Als u een client gebruikt die de connection string wegsamen vatting, raadpleegt u de documentatie van de client om te begrijpen of de certificaten worden geverifieerd.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Wat is de invloed van het gebruik van App Service met Azure Database for MySQL?

Voor Azure app services die verbinding maken met Azure Database for MySQL, zijn er twee mogelijke scenario's, afhankelijk van de wijze waarop u SSL met uw toepassing gebruikt.

* Dit nieuwe certificaat is toegevoegd aan App Service op platform niveau. Als u de SSL-certificaten gebruikt die zijn opgenomen in App Service platform in uw toepassing, is er geen actie vereist. Dit is het meest voorkomende scenario.
* Als u het pad naar het SSL-certificaat bestand in uw code expliciet opneemt, moet u het nieuwe certificaat downloaden en een gecombineerd certificaat maken zoals hierboven wordt vermeld en het certificaat bestand gebruiken. Een goed voor beeld van dit scenario is wanneer u aangepaste containers in App Service gebruikt zoals gedeeld in de [app service documentatie](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Dit is een ongebruikelijk scenario, maar er zijn enkele gebruikers die hiervoor gebruikmaken van.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Wat is de impact van het gebruik van Azure Kubernetes Services (AKS) met Azure Database for MySQL?

Als u probeert verbinding te maken met de Azure Database for MySQL met behulp van Azure Kubernetes Services (AKS), is dit vergelijkbaar met de toegang vanuit een specifieke host-omgeving van klanten. Raadpleeg de stappen [hier](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Wat is de invloed van het gebruik van Azure Data Factory om verbinding te maken met Azure Database for MySQL?

Voor een connector die gebruikmaakt van Azure Integration Runtime gebruikt de connector certificaten in het Windows-certificaat archief in de op Azure gehoste omgeving. Deze certificaten zijn al compatibel met de zojuist toegepaste certificaten en daarom is er geen actie vereist.

Voor een connector die gebruikmaakt van zelf-hostende Integration Runtime waarbij u het pad naar het SSL-certificaat bestand in uw connection string expliciet opneemt, moet u het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) downloaden en de Connection String bijwerken om het te gebruiken.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Moet ik de downtime van een database server onderhoud voor deze wijziging plannen?

Nee. Aangezien de wijziging alleen aan de client zijde wordt uitgevoerd om verbinding te maken met de database server, is er geen uitval tijd nodig voor de database server voor deze wijziging.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Als ik een nieuwe server Maak na 15 februari 2021 (02/15/2021), wordt dit van invloed?

Voor servers die zijn gemaakt na 15 februari 2021 (02/15/2021), blijft u de [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) voor uw toepassingen gebruiken om verbinding te maken met behulp van SSL.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Hoe vaak werkt micro soft hun certificaten bij of wat is het verloop beleid?

Deze certificaten die worden gebruikt door Azure Database for MySQL worden door vertrouwde certificerings instanties (CA) verschaft. De ondersteuning van deze certificaten is dus gebonden aan de ondersteuning van deze certificaten per CA. Het [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certificaat is gepland om te verlopen in 2025. micro soft moet een certificaat wijziging vóór de verval datum uitvoeren. Als er sprake is van onvoorziene fouten in deze vooraf gedefinieerde certificaten, moet micro soft de draaiing van het certificaat zo snel mogelijk maken, vergelijkbaar met de wijziging die op 15 februari 2021 wordt uitgevoerd om ervoor te zorgen dat de service te allen tijde is beveiligd en compatibel is.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Moet ik deze update alleen uitvoeren op de bron server of de Lees replica's als ik met replica's Lees?

Omdat deze update een wijziging aan de client zijde is, moet u ook de wijzigingen voor deze clients Toep assen als de client gebruikt om gegevens te lezen van de replica-server.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Als ik gegevens replicatie gebruik, moet ik dan elke actie uitvoeren?

Als u [gegevens replicatie](concepts-data-in-replication.md) gebruikt om verbinding te maken met Azure database for MySQL, moet u rekening houden met twee dingen:

* Als de gegevens replicatie van een virtuele machine (on-premises of Azure virtual machine) naar Azure Database for MySQL is, moet u controleren of SSL wordt gebruikt om de replica te maken. Voer de **status van slave weer geven** uit en controleer de volgende instelling.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Als u ziet dat het certificaat is ingesteld voor de CA_file, SSL_Cert en SSL_Key, moet u het bestand bijwerken door het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) toe te voegen en een gecombineerd certificaat bestand te maken.

* Als de gegevens replicatie tussen twee Azure Database for MySQL servers plaatsvindt, moet u de replica opnieuw instellen door het **aanroepen van MySQL.az_replication_change_master** uit te voeren en het nieuwe dubbele basis certificaat als laatste para meter op te geven [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Is er een query aan de server zijde om te bepalen of SSL wordt gebruikt?

Raadpleeg [SSL-verificatie](howto-configure-ssl.md#step-4-verify-the-ssl-connection)om te controleren of u SSL-verbinding gebruikt om verbinding te maken met de server.

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Is er een actie nodig als ik de DigiCertGlobalRootG2 al in mijn certificaat bestand heb?

Nee. Er is geen actie nodig als uw certificaat bestand al het **DigiCertGlobalRootG2** heeft.

#### <a name="what-if-i-have-further-questions"></a>Wat moet ik doen als ik meer vragen heb?

Voor vragen kunt u antwoorden krijgen van deskundigen van [de community in micro soft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Als u een ondersteunings abonnement hebt en technische hulp nodig hebt, kunt u [contact met ons](mailto:AzureDatabaseforMySQL@service.microsoft.com)opnemen.
