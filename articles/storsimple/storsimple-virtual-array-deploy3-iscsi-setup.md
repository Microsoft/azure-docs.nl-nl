---
title: Setup van iSCSI-Server Microsoft Azure StorSimple virtuele matrix | Microsoft Docs
description: Hierin wordt beschreven hoe u een initiële installatie uitvoert, uw StorSimple iSCSI-server registreert en de installatie van het apparaat voltooit.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956548"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple Virtual array implementeren: ingesteld als een iSCSI-server via Azure Portal

![proces stroom voor het instellen van iSCSI-installatie](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Deze implementatie-zelf studie is van toepassing op de Microsoft Azure StorSimple virtuele matrix. In deze zelf studie wordt beschreven hoe u de eerste installatie uitvoert, uw StorSimple iSCSI-server registreert, de installatie van het apparaat voltooit en vervolgens volumes maakt, koppelt, initialiseert en formatteert op de virtuele StorSimple-matrix die is geconfigureerd als een iSCSI-server. 

De procedures die hier worden beschreven, nemen ongeveer 30 minuten tot 1 uur in beslag. De informatie die in dit artikel wordt gepubliceerd, is alleen van toepassing op virtuele StorSimple-matrices.

## <a name="setup-prerequisites"></a>Vereisten voor installatie

Voordat u de virtuele StorSimple-matrix configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtuele matrix ingericht en er verbinding mee gemaakt zoals beschreven in [de implementatie StorSimple Virtual array: een virtuele matrix inrichten in Hyper-V](./storsimple-virtual-array-deploy2-provision-hyperv.md) of [StorSimple Virtual array implementeren: een virtuele matrix inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* U hebt de service registratie sleutel van de StorSimple Apparaatbeheer-service die u hebt gemaakt voor het beheren van uw StorSimple virtuele arrays. Zie voor meer informatie **stap 2: de service registratie sleutel ophalen** in [StorSimple Virtual array implementeren: bereid de portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)voor.
* Als dit de tweede of volgende virtuele matrix is die u registreert met een bestaande StorSimple-Apparaatbeheer service, moet u de versleutelings sleutel voor service gegevens hebben. Deze sleutel is gegenereerd toen het eerste apparaat is geregistreerd bij deze service. Als u deze sleutel hebt verloren, raadpleegt u de versleutelings **sleutel voor service gegevens ophalen** in [de Web-UI gebruiken om uw virtuele StorSimple-matrix te beheren](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Stapsgewijze installatie

Gebruik de volgende stapsgewijze instructies voor het instellen en configureren van uw virtuele StorSimple-matrix:

* [Stap 1: de installatie van de lokale webgebruikersinterface volt ooien en uw apparaat registreren](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Stap 2: de vereiste Apparaatinstellingen volt ooien
* [Stap 3: een volume toevoegen](#step-3-add-a-volume)
* [Stap 4: een volume koppelen, initialiseren en Format teren](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Stap 1: de installatie van de lokale webgebruikersinterface volt ooien en uw apparaat registreren

#### <a name="to-complete-the-setup-and-register-the-device"></a>De installatie volt ooien en het apparaat registreren

1. Open een browservenster. Verbinding maken met het type webgebruikersinterface:
   
    `https://<ip-address of network interface>`
   
    Gebruik de verbindings-URL die u in de vorige stap hebt genoteerd. Er wordt een fout bericht weer geven met de melding dat er een probleem is met het beveiligings certificaat van de website. Klik op **door gaan naar deze webpagina**.
   
    ![fout in beveiligings certificaat](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Meld u als **StorSimpleAdmin** aan bij de Web-UI van uw virtuele apparaat. Voer het wacht woord voor het beheerders wachtwoord in dat u hebt gewijzigd in stap 3: het virtuele apparaat in [Deploy StorSimple Virtual array starten: een virtueel apparaat inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of [StorSimple Virtual array implementeren: een virtueel apparaat inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Aanmeldings pagina](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. U gaat naar de **Start** pagina. Op deze pagina worden de verschillende instellingen beschreven die nodig zijn om het virtuele apparaat te configureren en te registreren bij de StorSimple-Apparaatbeheer service. Houd er rekening mee dat de **netwerk instellingen**, **web proxy-instellingen** en **tijd instellingen** optioneel zijn. De enige vereiste instellingen zijn **Apparaatinstellingen** en **Cloud instellingen**.
   
    ![Startpagina](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Op de pagina **netwerk instellingen** onder **netwerk interfaces** wordt gegevens 0 automatisch voor u geconfigureerd. Elke netwerk interface is standaard ingesteld op het automatisch ophalen van een IP-adres (DHCP). Daarom wordt automatisch een IP-adres, subnet en gateway toegewezen (voor zowel IPv4 als IPv6).
   
    Als u van plan bent om uw apparaat te implementeren als een iSCSI-server (voor het inrichten van blok opslag), raden we u aan de optie **IP-adres automatisch ophalen** uit te scha kelen en statische IP-adressen te configureren.
   
    ![Pagina netwerk instellingen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Als u meer dan één netwerk interface hebt toegevoegd tijdens het inrichten van het apparaat, kunt u deze hier configureren. Opmerking: u kunt uw netwerk interface alleen configureren als IPv4 of als IPv4 en IPv6. Alleen IPv6-configuraties worden niet ondersteund.
5. DNS-servers zijn vereist omdat ze worden gebruikt wanneer het apparaat probeert te communiceren met uw service providers voor de Cloud opslag of als het apparaat moet worden omgezet op naam als het is geconfigureerd als een bestands server. Op de pagina **netwerk instellingen** van de **DNS-servers**:
   
   1. Er wordt automatisch een primaire en secundaire DNS-server geconfigureerd. Als u statische IP-adressen wilt configureren, kunt u DNS-servers opgeven. Voor maximale Beschik baarheid kunt u het beste een primaire en secundaire DNS-server configureren.
   2. Klik op **Toepassen**. Hiermee worden de netwerk instellingen toegepast en gevalideerd.
6. Op de pagina **Apparaatinstellingen** :
   
   1. Wijs een unieke **naam** toe aan uw apparaat. Deze naam mag 1-15 tekens lang zijn en mag letter, cijfers en afbreek streepjes bevatten.
   2. Klik op het iSCSI- **Server** pictogram ![ iSCSI-server pictogram ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) voor het **type** apparaat dat u maakt. Een iSCSI-server biedt u de mogelijkheid om blok opslag in te richten.
   3. Geef op of u wilt dat dit apparaat lid is van een domein. Als uw apparaat een iSCSI-server is, is het toevoegen van het domein optioneel. Als u besluit uw iSCSI-server niet aan een domein toe te voegen, klikt u op **Toep assen**, wacht u totdat de instellingen worden toegepast en gaat u verder met de volgende stap.
      
       Als u het apparaat wilt toevoegen aan een domein. Voer een **domein naam** in en klik vervolgens op **Toep assen**.
      
      > [!NOTE]
      > Als u uw iSCSI-server lid maakt van een domein, moet u ervoor zorgen dat uw virtuele array zich in een eigen organisatie-eenheid (OE) bevindt voor Microsoft Azure Active Directory en er geen groeps beleidsobjecten (GPO) op worden toegepast.
      > 
      > 
   4. Er wordt een dialoog venster weer gegeven. Voer uw domein referenties in de opgegeven indeling in. Klik op het vinkje ![vinkje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). De domein referenties worden gecontroleerd. Er wordt een fout bericht weer gegeven als de referenties onjuist zijn.
      
       ![referenties](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klik op **Toepassen**. Hiermee worden de apparaatinstellingen toegepast en gevalideerd.
7. (Optioneel) Configureer uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd.
   
    ![webproxy configureren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Op de pagina **webproxy** :
   
   1. Geef de **URL van de webproxy** op in deze indeling: *http: \/ /host-IP-adres* of *FQDN: poort nummer*. Let op: HTTPS-Url's worden niet ondersteund.
   2. Geef **verificatie** op als **basis** of **geen**.
   3. Als u verificatie gebruikt, moet u ook een **gebruikers naam** en **wacht woord** opgeven.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde web proxy-instellingen gevalideerd en toegepast.
8. (Optioneel) de tijd instellingen voor uw apparaat configureren, zoals de tijd zone en de primaire en secundaire NTP-servers. NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders.
   
    ![Tijd instellingen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Op de pagina **tijd instellingen** :
   
   1. Selecteer in de vervolg keuzelijst de **tijd zone** op basis van de geografische locatie waar het apparaat wordt geïmplementeerd. De standaard tijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
   2. Geef een **primaire NTP-server** voor uw apparaat op of accepteer de standaard waarde van time.Windows.com. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.
   3. Geef eventueel een **secundaire NTP-server** op voor uw apparaat.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde tijd instellingen gevalideerd en toegepast.
9. Configureer de Cloud instellingen voor uw apparaat. In deze stap voltooit u de configuratie van het lokale apparaat en registreert u het apparaat met uw StorSimple-Apparaatbeheer service.
   
   1. Voer de **service registratie sleutel** in die u hebt ontvangen in **stap 2: de service registratie sleutel ophalen** in [de StorSimple virtuele matrix implementeren: bereid de portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)voor.
   2. Als dit niet het eerste apparaat is dat u bij deze service registreert, moet u de **versleutelings sleutel voor de service gegevens** opgeven. Deze sleutel is vereist bij de service registratie sleutel om extra apparaten te registreren bij de StorSimple-Apparaatbeheer service. Raadpleeg voor meer informatie [de versleutelings sleutel voor service gegevens ophalen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) op uw lokale webinterface.
   3. Klik op **Registreren**. Hiermee wordt het apparaat opnieuw opgestart. Mogelijk moet u 2-3 minuten wachten voordat het apparaat is geregistreerd. Nadat het apparaat opnieuw is opgestart, wordt u naar de aanmeldings pagina geleid.
      
      ![Apparaat registreren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Ga terug naar Azure Portal.
11. Navigeer naar de Blade **apparaten** van uw service. Als u veel resources hebt, klikt u op **alle resources**, klikt u op de naam van de service (Zoek indien nodig ernaar) en klikt u vervolgens op **apparaten**.
12. Controleer op de Blade **apparaten** of het apparaat is verbonden met de service door de status op te zoeken. Het apparaat moet de status **Gereed voor configuratie** hebben.
    
    ![Apparaat implementeren](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Stap 2: het apparaat configureren als iSCSI-server

Voer de volgende stappen uit in de Azure Portal om de vereiste apparaatinstellingen te volt ooien.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Het apparaat als iSCSI-server configureren

1. Ga naar de StorSimple-Apparaatbeheer service en ga vervolgens naar **beheer > apparaten**. Selecteer op de Blade **apparaten** het apparaat dat u zojuist hebt gemaakt. Dit apparaat wordt weer gegeven als **gereed voor configuratie**.
   
    ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klik op het apparaat. er wordt een banner bericht weer gegeven dat aangeeft dat het apparaat gereed is voor installatie.
   
    ![Apparaat configureren als iSCSI-Server 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klik op **configureren** op de opdracht balk van het apparaat. Hiermee opent u de Blade **configureren** . Ga als volgt te werk op de Blade **configureren** :
   
   * De iSCSI-server naam wordt automatisch ingevuld.
   * Zorg ervoor dat de versleuteling van de Cloud opslag is ingesteld op **ingeschakeld**. Dit zorgt ervoor dat de gegevens die van het apparaat naar de cloud worden verzonden, worden versleuteld.
   * Geef een versleutelings sleutel van 32 tekens op en neem deze op in een app voor sleutel beheer, zodat u deze later kunt raadplegen.
   * Selecteer een opslag account dat u wilt gebruiken met uw apparaat. In dit abonnement kunt u een bestaand opslag account selecteren, of u kunt op **toevoegen** klikken om een account uit een ander abonnement te kiezen.
     
     ![Apparaat configureren als iSCSI-Server 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klik op **configureren** om het instellen van de iSCSI-server te volt ooien.
   
    ![Apparaat configureren als iSCSI-Server 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. U wordt gewaarschuwd dat het maken van de iSCSI-server wordt uitgevoerd. Nadat de iSCSI-server is gemaakt, wordt de Blade **apparaten** bijgewerkt en is de bijbehorende Apparaatstatus **online**.
   
    ![Apparaat configureren als iSCSI-Server 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Stap 3: een volume toevoegen

1. Selecteer op de Blade **apparaten** het apparaat dat u zojuist hebt geconfigureerd als een iSCSI-server. Klik op **...** (in deze rij kunt u ook met de rechter muisknop klikken) en selecteer **volume toevoegen** in het context menu. U kunt ook op **+ volume toevoegen** klikken vanaf de opdracht balk. Hiermee opent u de Blade **volume toevoegen** .
   
    ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Ga als volgt te werk op de Blade **volume toevoegen** :
   
   * Voer in het veld **volume naam** een unieke naam in voor het volume. De naam moet een teken reeks zijn die tussen de 3 en 127 tekens bevat.
   * Geef in de vervolg keuzelijst **type** de keuze aan of u een **gelaagd** of **lokaal vastgemaakt** volume wilt maken. Voor werk belastingen waarvoor lokale garanties, lage latenties en hogere prestaties zijn vereist, selecteert u **lokaal vastgemaakt** **volume**. Voor alle andere gegevens selecteert u **gelaagd** **volume**.
   * Geef in het veld **capaciteit** de grootte van het volume op. Een gelaagd volume moet tussen 500 GB en 5 TB liggen en een lokaal vastgemaakt volume moet tussen 50 GB en 500 GB liggen.
     
     Een lokaal vastgemaakt volume is dik ingericht en zorgt ervoor dat de primaire gegevens in het volume op het apparaat blijven en niet overvloeien naar de Cloud.
     
     Een gelaagd volume aan de andere kant is dun ingericht. Wanneer u een gelaagd volume maakt, wordt ongeveer 10% van de ruimte ingericht op de lokale laag en wordt 90% van de ruimte ingericht in de Cloud. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de Cloud gebruikt wanneer de gegevens lagen. Dit betekent dat als u alle lokale ruimte op het apparaat uitvoert, u geen gelaagde share kunt inrichten (omdat de 10% niet beschikbaar is).
     
     ![Een volume 2 toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klik op **verbonden hosts**, selecteer een Access Control record (ACR) dat overeenkomt met de iSCSI-initiator die u wilt verbinden met dit volume en klik vervolgens op **selecteren**. <br><br> 
3. Als u een nieuwe verbonden host wilt toevoegen, klikt u op **Nieuw toevoegen**, voert u een naam in voor de host en de iSCSI QUALIFIED name (IQN), en klikt u vervolgens op **toevoegen**. Als u de IQN niet hebt, gaat u naar [bijlage A: de IQN van een Windows Server-host ophalen](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Een volume toevoegen 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Klik op **OK** wanneer u klaar bent met het configureren van het volume. Er wordt een volume gemaakt met de opgegeven instellingen en er wordt een melding weer gegeven. Bewaking en back-up worden standaard ingeschakeld voor het volume.
   
     ![Een volume toevoegen 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Ga naar de Blade **volumes** om te controleren of het volume is gemaakt. Het volume wordt weer gegeven.
   
   ![Een volume toevoegen 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Stap 4: een volume koppelen, initialiseren en Format teren

Voer de volgende stappen uit om uw StorSimple-volumes te koppelen, initialiseren en Format teren op een Windows Server-host.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Een volume koppelen, initialiseren en formatteren

1. Open de app **iSCSI-initiator** op de juiste server.
2. Klik in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Detectie** op **Portal detecteren**.
   
    ![Portal ontdekken](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Geef in het dialoogvenster **Doelportaal detecteren** het IP-adres op van de netwerkinterface met iSCSI-functionaliteit en klik vervolgens op **OK**.
   
    ![IP-adres](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Ga in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Doelen** naar **Gedetecteerde doelen**. (Elk volume zal een gedetecteerd doel zijn.) De apparaatstatus moet worden weer gegeven als **inactief**.
   
    ![gedetecteerde doelen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecteer een doel apparaat en klik vervolgens op **verbinding maken**. Nadat het apparaat is verbonden, moet de status zijn gewijzigd in **Verbonden**. (Zie [micro soft iSCSI-initiator installeren en configureren][1]voor meer informatie over het gebruik van de micro soft iSCSI-initiator.
   
    ![doel apparaat selecteren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Druk op uw Windows-hots op de Windows-toets + X en klik vervolgens op **Uitvoeren**.
7. Typ in het dialoogvenster **Uitvoeren****Diskmgmt.msc**. Klik op **OK**. Het dialoogvenster **Schijfbeheer** wordt weergegeven. In het rechterdeelvenster worden de volumes op uw host weergeven.
8. In het venster **Schijfbeheer** worden de gekoppelde volumes weergegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.
   
    ![schijf beheer](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klik met de rechter muisknop en selecteer **schijf initialiseren**.
   
    ![schijf 1 initialiseren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Selecteer in het dialoog venster de schijven die u wilt initialiseren en klik vervolgens op **OK**.
    
    ![schijf 2 initialiseren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. De wizard Nieuw eenvoudig volume wordt gestart. Selecteer een schijf grootte en klik vervolgens op **volgende**.
    
    ![wizard Nieuw volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Wijs een stationsletter aan het volume toe en klik vervolgens op **volgende**.
    
    ![wizard Nieuw volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Geef de para meters op voor het format teren van het volume. **Op Windows Server wordt alleen NTFS ondersteund.** Stel de Allocation Unit Size in op 64 kB. Geef een label op voor uw volume. Het is een aanbevolen best practice voor deze naam gelijk te zijn aan de volume naam die u hebt ingevoerd op de virtuele StorSimple-matrix. Klik op **Volgende**.
    
    ![wizard Nieuw volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Controleer de waarden voor uw volume en klik vervolgens op **volt ooien**.
    
    ![wizard Nieuw volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    De volumes worden weer gegeven als **online** op de pagina **schijf beheer** .
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de lokale web-UI voor [het beheren van uw StorSimple-virtuele matrix](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Bijlage A: de IQN van een Windows Server-host ophalen

Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server 2012 wordt uitgevoerd.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Het IQN ophalen van een Windows-host

1. Start de Microsoft iSCSI-initiator op uw Windows-host.
2. Selecteer en kopieer in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Configuratie** de tekenreeks in het veld **Naam van initiator**.
   
    ![Eigenschappen iSCSI-initiator](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Bewaar deze tekenreeks.

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)