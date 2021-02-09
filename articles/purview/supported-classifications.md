---
title: Lijst met ondersteunde classificaties
description: Op deze pagina vindt u een overzicht van de ondersteunde systeem classificaties in azure controle sfeer liggen.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 2/5/2021
ms.openlocfilehash: d98f2f80bf22627eb34855234e22e314c241c852
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988307"
---
# <a name="supported-classifications-in-azure-purview"></a>Ondersteunde classificaties in azure controle sfeer liggen

In dit artikel vindt u een overzicht van de ondersteunde en gedefinieerde systeem classificaties in azure controle sfeer liggen (preview).


- **Drempel waarde voor DISTINCT**: het totale aantal afzonderlijke gegevens waarden dat in een kolom moet worden gevonden voordat de scanner het gegevens patroon erop uitvoert. Voor onze systeem classificatie regels moet u ten minste 8 afzonderlijke waarden in elke kolom hebben om ze te kunnen classificeren. Het systeem vereist deze waarde om ervoor te zorgen dat de kolom voldoende gegevens bevat voor de scanner om deze nauw keurig te classificeren. Een kolom die bijvoorbeeld meerdere rijen bevat die alle de waarde 1 bevatten, wordt niet geclassificeerd. Kolommen die één rij met een waarde bevatten en de rest van de rijen hebben null-waarden, worden ook niet geclassificeerd. Als u meerdere patronen opgeeft, geldt deze waarde voor elk patroon.

- **Drempel waarde voor minimum overeenkomst**: dit is het minimum percentage van de gegevens waarde komt overeen met een kolom die moet worden gevonden door de scanner zodat de classificatie kan worden toegepast. De waarde van de systeem classificatie is ingesteld op 60%.


## <a name="defined-system-classifications"></a>Gedefinieerde systeem classificaties

Azure controle sfeer liggen classificeert gegevens door het [filter](https://wikipedia.org/wiki/Bloom_filter) [regex](https://wikipedia.org/wiki/Regular_expression) en bloei. In de volgende lijsten worden de indeling, het patroon en de tref woorden voor de door Azure controle sfeer liggen gedefinieerde systeem classificaties beschreven.

Elke classificatie naam wordt voorafgegaan door micro soft.

## <a name="bloom-filter-classifications"></a>Classificaties van bloei-filter

## <a name="city-country-and-place"></a>Plaats, land en plaats

De plaats-, land-en plaatsings filters zijn voor bereid met behulp van de beste gegevens sets die beschikbaar zijn voor het voorbereiden van de data.

## <a name="person"></a>Person

Het filter voor de persoons bloei is voor bereid met behulp van de onderstaande twee gegevens sets.

- [2010 US heeft gegevens voor achternamen geteld (162-K-vermeldingen)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Populaire baby namen (van SSN), met alle jaren 1880-2019 (98-K vermeldingen)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx-classificaties

## <a name="aba-routing"></a>ABA-route ring

### <a name="format"></a>Indeling

Negen cijfers, die mogelijk in een opgemaakt of niet-opgemaakt patroon staan

### <a name="pattern"></a>Patroon

Opgemaakt

- vier cijfers die beginnen met 0, 1, 2, 3, 6, 7 of 8
- een koppel teken
- vier cijfers
- een koppel teken
- een cijfer niet-opgemaakt: negen opeenvolgende cijfers, te beginnen met 0, 1, 2, 3, 6, 7 of 8

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Sofi-nummer (Argentinië National Identity)

### <a name="format"></a>Indeling

Acht cijfers met of zonder punten

### <a name="pattern"></a>Patroon

Acht cijfers:

- twee cijfers
- een optionele periode
- drie cijfers
- een optionele periode
- drie cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Nummer van Australië-Bank account

### <a name="format"></a>Indeling

Zes tot 10 cijfers met of zonder een bank status vertakking nummer

### <a name="pattern"></a>Patroon

Het account nummer is zes tot 10 cijfers.

Australië Bank State Branch-nummer:

- drie cijfers
- een koppel teken
- drie cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Licentie nummer van het Australia-stuur programma

### <a name="format"></a>Indeling
Negen letters en cijfers

### <a name="pattern"></a>Patroon
negen letters en cijfers:

- twee cijfers of letters (niet hoofdletter gevoelig)
- twee cijfers
- vijf cijfers of letters (niet hoofdletter gevoelig)

OF

- een tot twee optionele letters (niet hoofdletter gevoelig)
- vier tot negen cijfers

OF

- negen cijfers of letters (niet hoofdletter gevoelig)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Australische Medicare-nummer

### <a name="format"></a>Indeling

10-11 cijfers

### <a name="pattern"></a>Patroon

10-11 cijfers:

- eerste cijfer bevindt zich in het bereik 2-6
- negende cijfer is een controle cijfer
- tiende cijfer is het probleem cijfer
- elfde cijfer (optioneel) is het individuele nummer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Australië-paspoort nummer

### <a name="format"></a>Indeling

Een letter gevolgd door zeven cijfers

### <a name="pattern"></a>Patroon

Een letter (niet hoofdletter gevoelig), gevolgd door zeven cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_passport"></a>Tref woord \_ Pass Port

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Tref woord \_ Australia \_ Pass Port- \_ nummer

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Australia-BTW-bestands nummer

### <a name="format"></a>Indeling

acht tot negen cijfers

### <a name="pattern"></a>Patroon

acht tot negen cijfers worden meestal als volgt weer gegeven:

- drie cijfers
- een optionele ruimte
- drie cijfers
- een optionele ruimte
- twee tot drie cijfers waarbij het laatste cijfer een controle cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_australia_tax_file_number"></a>Sleutel woord \_ Australia- \_ BTW- \_ bestands \_ nummer

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>België nationaal nummer

### <a name="format"></a>Indeling

11 cijfers plus optionele scheidings tekens

### <a name="pattern"></a>Patroon

11 cijfers plus scheidings tekens:

- zes cijfers en twee optionele Peri Oden in de notatie JJ. MM.DD voor geboorte datum
- Een optioneel scheidings teken van punt, streepje, spatie
- drie opeenvolgende cijfers (oneven voor mannetjes, zelfs voor wijfjes)
- Een optioneel scheidings teken van punt, streepje, spatie
- twee controle cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_belgium_national_number"></a>Sleutel woord \_ België \_ nationaal \_ nummer

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brazilië CPF-nummer

### <a name="format"></a>Indeling

11 cijfers met een controle cijfer en kunnen worden opgemaakt of niet-opgemaakt zijn

### <a name="pattern"></a>Patroon

Opgemaakt

- drie cijfers
- een periode
- drie cijfers
- een periode
- drie cijfers
- een koppel teken
- twee cijfers, wat controle cijfers zijn

Niet-opgemaakt:

- 11 cijfers waarbij de laatste twee cijfers controle cijfers zijn

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_brazil_cpf"></a>Tref woord \_ Brazilië \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Nummer van juridische entiteit in Brazilië (CNPJ)

### <a name="format"></a>Indeling

14 cijfers die een registratie nummer, vertakkings nummer en controle cijfers, plus scheidings tekens bevatten

### <a name="pattern"></a>Patroon

14 cijfers, plus scheidings tekens:

- twee cijfers
- een periode
- drie cijfers
- een periode
- drie cijfers (deze eerste acht cijfers zijn het registratie nummer)
- een slash
- vertakkings nummer van vier cijfers
- een koppel teken
- twee cijfers, wat controle cijfers zijn

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_brazil_cnpj"></a>Tref woord \_ Brazilië \_ CNPJ

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>National ID Card voor Brazilië (RG)

### <a name="format"></a>Indeling

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Patroon

:::no-loc text="Registro Geral (old format):":::

- twee cijfers
- een periode
- drie cijfers
- een periode
- drie cijfers
- een koppel teken
- Eén cijfer, wat een controle cijfer is

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 cijfers
- een koppel teken
- Eén cijfer, wat een controle cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_brazil_rg"></a>Tref woord \_ Brazilië \_ RG

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Rekening nummer Canada

### <a name="format"></a>Indeling

7 of 12 cijfers

### <a name="pattern"></a>Patroon

Een Canada-bank rekeningnummer is 7 of 12 cijfers.

Een door Voer van een Canada Bank account is:

- vijf cijfers
- een koppel teken
- drie cijfers of
- een nul &quot; 0&quot;
- acht cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_canada_bank_account_number"></a>Tref woord \_ Canada \_ Bank \_ account \_ Number

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Rijbewijs nummer van het Canada-stuur programma

### <a name="format"></a>Indeling

Varieert per provincie

### <a name="pattern"></a>Patroon

Diverse patronen die betrekking hebben op Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland/Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec en Saskatchewan

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_province_name_drivers_license_name"></a>Sleutel woord \_ [ \_ naam provincie \_ ] \_ licentie \_ naam Stuur Programma's

- De afkorting van de provincie, bijvoorbeeld AB
- De naam van de provincie, bijvoorbeeld Alberta

#### <a name="keyword_canada_drivers_license"></a>Licentie voor sleutel woord \_ Canada- \_ Stuur Programma's \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Canada-Health-Service nummer

### <a name="format"></a>Indeling

10 cijfers

### <a name="pattern"></a>Patroon

10 cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_canada_health_service_number"></a>Sleutel woord \_ Canada \_ Health- \_ service \_ nummer

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Canada paspoort nummer

### <a name="format"></a>Indeling

twee hoofd letters gevolgd door zes cijfers

### <a name="pattern"></a>Patroon

twee hoofd letters gevolgd door zes cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_canada_passport_number"></a>Tref woord \_ Canada \_ Pass Port- \_ nummer

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Tref woord \_ Pass Port

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Privé-identificatie nummer voor Canada (PHIN)

### <a name="format"></a>Indeling

negen cijfers

### <a name="pattern"></a>Patroon

negen cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_canada_phin"></a>Tref woord \_ Canada \_ Phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Sleutel woord \_ Canada \_ provincies

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Sofi-nummer Canada

### <a name="format"></a>Indeling

negen cijfers met tijdelijke afbreek streepjes of spaties

### <a name="pattern"></a>Patroon

Opgemaakt

- drie cijfers
- een koppel teken of spatie
- drie cijfers
- een koppel teken of spatie
- drie cijfers

Niet-geformatteerd: negen cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_sin"></a>Tref woord \_ Sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Tref woord \_ Sin \_ samen werk

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>ID-kaart nummer Chili

### <a name="format"></a>Indeling

zeven tot acht cijfers plus een controle cijfer of letter

### <a name="pattern"></a>Patroon

zeven tot acht cijfers plus scheidings tekens:

- een tot twee cijfers
- een optionele periode
- drie cijfers
- een optionele periode
- drie cijfers
- een streepje
- Eén cijfer of letter (niet hoofdletter gevoelig). Dit is een controle cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_chile_id_card"></a>Tref woord \_ Chili \_ id- \_ kaart

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>China-id-nummer van residente identiteits kaart (PRC)

### <a name="format"></a>Indeling

18 cijfers

### <a name="pattern"></a>Patroon

18 cijfers:

- zes cijfers, een adres code
- acht cijfers in de vorm JJJMMDD, die de geboorte datum zijn
- drie cijfers, een bestel code
- Eén cijfer, wat een controle cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_china_resident_id"></a>Tref woord in \_ China \_ residente \_ id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Creditcard nummer

### <a name="format"></a>Indeling

14 tot 16 cijfers, wat kan worden opgemaakt of niet-opgemaakt (dddddddddddddddd) en dat de Luhn-test moet door lopen.

### <a name="pattern"></a>Patroon

Complex en robuust patroon dat kaarten van alle belang rijke merken wereld wijd herkent, waaronder Visa, Master Card, Discover Card, JCB, American Express, geschenk kaarten en diner-kaarten.

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_cc_verification"></a>Verificatie sleutel woord \_ CC \_

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Tref woord \_ CC \_ naam

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Licentie nummer van het Kroatië-stuur programma

Deze entiteit van het gevoelige informatie type is alleen beschikbaar in het licentie nummer van het EU-stuur programma, afhankelijk van het gegevens type.

### <a name="format"></a>Indeling

acht cijfers zonder spaties en scheidings tekens

### <a name="pattern"></a>Patroon

acht cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_eu_drivers_license_number"></a>Tref \_ woorden \_ het \_ licentie \_ nummer van het EU-stuur programma

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Tref woorden voor \_ \_ \_ het \_ licentie \_ nummer van het EU-stuur programma

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>ID-kaart nummer van Kroatië

Deze entiteit van het gevoelige informatie type is opgenomen in het ABC-gegevens type van de EU en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

negen cijfers

### <a name="pattern"></a>Patroon

negen opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_croatia_id_card"></a>Trefwoord \_ \_ -id- \_ kaart

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>OIB-nummer (Kroatië Personal Identification)

### <a name="format"></a>Indeling

11 cijfers

### <a name="pattern"></a>Patroon

11 cijfers:

- 10 cijfers
- eind cijfer is een controle cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_croatia_oib_number"></a>Tref woord \_ Kroatië \_ OIB \_ Number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Privé-identificatie nummer Denemarken

### <a name="format"></a>Indeling

10 cijfers met een afbreek streepje

### <a name="pattern"></a>Patroon

10 cijfers:

- zes cijfers in de notatie DDMMYY, die de geboorte datum zijn
- een koppel teken
- vier cijfers waarbij het laatste cijfer een controle cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_denmark_id"></a>Tref woord \_ Denemarken \_ -id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Nummer ICL-betaalpas

### <a name="format"></a>Indeling

16 cijfers

### <a name="pattern"></a>Patroon

Complex en robuust patroon

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_eu_debit_card"></a>\_ABC- \_ betaalpas van het tref woord \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Woordenlijst \_ \_ voor \_ Spellings kaart

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Woorden \_ \_ \_ woordenlijst voor de beveiligings voorwaarden \_

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Woorden \_ voorstel \_ verval datum van kaart \_ \_

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Licentie nummer van het EU-stuur programma

Dit zijn de entiteiten in het licentie nummer van het EU-stuur programma, afhankelijk van het type informatie.

- Oostenrijk
- België
- Bulgarije
- Kroatië
- Cyprus
- Tsjechisch
- Denemarken
- Estland
- Finland
- Frankrijk
- Duitsland
- Griekenland
- Hongarije
- Ierland
- Italië
- Letland
- Litouwen
- Luxemburg
- Malta
- Nederland
- Polen
- Portugal
- Roemenië
- Slowakije
- Slovenië
- Spanje
- Zweden
- Engelse

## <a name="eu-national-identification-number"></a>Sofi-nummer van de EU

Dit zijn de entiteiten in het ABC-gegevens type van het EU-identificatie nummer.

- Oostenrijk
- België
- Bulgarije
- Kroatië
- Cyprus
- Tsjechisch
- Denemarken
- Estland
- Finland
- Frankrijk
- Duitsland
- Griekenland
- Hongarije
- Ierland
- Italië
- Letland
- Litouwen
- Luxemburg
- Malta
- Nederland
- Polen
- Portugal
- Roemenië
- Slowakije
- Slovenië
- Spanje
- Engelse

## <a name="eu-passport-number"></a>EU-paspoort nummer

Dit zijn de entiteiten in het EU-paspoort nummer gevoelige informatie typeThese zijn de entiteiten in de nummer bundel van het EU-paspoort.

- Oostenrijk
- België
- Bulgarije
- Kroatië
- Cyprus
- Tsjechisch
- Denemarken
- Estland
- Finland
- Frankrijk
- Duitsland
- Griekenland
- Hongarije
- Ierland
- Italië
- Letland
- Litouwen
- Luxemburg
- Malta
- Nederland
- Polen
- Portugal
- Roemenië
- Slowakije
- Slovenië
- Spanje
- Zweden
- Engelse

## <a name="eu-social-security-number-or-equivalent-identification"></a>Sociaal-fiscaal nummer van de EU of gelijkwaardige identificatie

Dit zijn de entiteiten in het sociaal-fiscaal nummer van de EU of een equivalent identificatie gevoelig gegevens type.

- Oostenrijk
- België
- Kroatië
- Tsjechisch
- Denemarken
- Finland
- Frankrijk
- Duitsland
- Griekenland
- Hongarije
- Portugal
- Spanje
- Zweden

## <a name="eu-tax-identification-number"></a>BTW-identificatie nummer van de EU

Deze entiteiten bevinden zich in het ABC-belasting identificatienummer van het gegevens type.

- Oostenrijk
- België
- Bulgarije
- Kroatië
- Cyprus
- Tsjechisch
- Denemarken
- Estland
- Finland
- Frankrijk
- Duitsland
- Griekenland
- Hongarije
- Ierland
- Italië
- Letland
- Litouwen
- Luxemburg
- Malta
- Nederland
- Polen
- Portugal
- Roemenië
- Slowakije
- Slovenië
- Spanje
- Zweden
- Engelse



## <a name="finland-national-id"></a>National ID Finland

### <a name="format"></a>Indeling

zes cijfers plus een teken dat een Century plus drie cijfers plus een controle cijfer aangeeft

### <a name="pattern"></a>Patroon

Het patroon moet het volgende bevatten:

- zes cijfers in de notatie DDMMYY, die de geboorte datum zijn
- Century-markering (-, + of a)
- persoonlijk identificatie nummer van drie cijfers
- een cijfer of letter (niet hoofdletter gevoelig) dat een controle cijfer is

### <a name="keywords"></a>Trefwoorden

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Finland paspoort nummer

Deze entiteit van het type gevoelige informatie is beschikbaar in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

combi natie van negen letters en cijfers

### <a name="pattern"></a>Patroon

combi natie van negen letters en cijfers:

- twee letters (niet hoofdletter gevoelig)
- zeven cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_eu_passport_number_common"></a>Tref woorden \_ EU \_ Pass Port- \_ nummer \_ Algemeen

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Trefwoord nummer van het sleutel woord \_ Finland \_ \_

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Rijbewijs nummer van Frank rijk stuur programma

Deze entiteit van het gevoelige informatie type is beschikbaar in het informatie type van het licentie nummer van het EU-stuur programma en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

12 cijfers

### <a name="pattern"></a>Patroon

12 cijfers met validatie om Vergelijk bare patronen zoals Franse telefoon nummers te reduceren

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_french_drivers_license"></a>Licentie voor het tref woord \_ Frans \_ Stuur Programma's \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Landse nationale ID-kaart (CNI)

### <a name="format"></a>Indeling

12 cijfers

### <a name="pattern"></a>Patroon

12 cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_france_eu_national_id_card"></a>Tref woorden \_ Frank rijk \_ EU \_ nationale \_ id- \_ kaart

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Frank rijk paspoort nummer

Deze entiteit van het type gevoelige informatie is beschikbaar in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

negen cijfers en letters

### <a name="pattern"></a>Patroon

negen cijfers en letters:

- twee cijfers
- twee letters (niet hoofdletter gevoelig)
- vijf cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_passport"></a>Tref woord \_ Pass Port

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Frank rijk sociaal-fiscaal nummer (INSEE) of gelijkwaardige identificatie

Deze entiteit van het gevoelige informatie type is opgenomen in het sociaal-fiscaal nummer van de EU en het equivalente ID-gegevens type en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

15 cijfers

### <a name="pattern"></a>Patroon

Moet overeenkomen met een van de twee patronen:

- 13 cijfers gevolgd door een spatie gevolgd door twee cijfers of
- 15 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_fr_insee"></a>Tref woord \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Rijbewijs nummer van het Duitse stuur programma

Deze entiteit van het type gevoelige informatie is opgenomen in het licentie nummer van het EU-stuur programma en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

combi natie van 11 cijfers en letters

### <a name="pattern"></a>Patroon

11 cijfers en letters (niet hoofdletter gevoelig):

- een cijfer of letter
- twee cijfers
- zes cijfers of letters
- een cijfer
- een cijfer of letter

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_german_drivers_license_number"></a>Tref woord \_ Duitse \_ Stuur Programma's \_ licentie \_ nummer

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Nummer van de Duitsland-identiteits kaart

### <a name="format"></a>Indeling

Sinds 1 november 2010: negen letters en cijfers

van 1 april 1987 tot en met 31 oktober 2010:10 cijfers

### <a name="pattern"></a>Patroon

Sinds 1 november 2010:

- Eén letter (niet hoofdletter gevoelig)
- acht cijfers

van 1 april 1987 tot en met 31 oktober 2010:

- 10 cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_germany_id_card"></a>Tref woord \_ Duitse \_ id- \_ kaart

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Duitsland-paspoort nummer

Deze entiteit van het type gevoelige informatie is opgenomen in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

10 cijfers of letters

### <a name="pattern"></a>Patroon

Het patroon moet het volgende bevatten:

- het eerste teken is een cijfer of een letter van deze set (C, F, G, H, J, K)
- drie cijfers
- vijf cijfers of letters uit deze set (C,-H, J-N, P, R, T, V-Z)
- een cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_german_passport"></a>Tref woord \_ Duits \_ Pass Port

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>National ID-kaart (Grieken land)

### <a name="format"></a>Indeling

Combi natie van 7-8 letters en cijfers plus een streepje

### <a name="pattern"></a>Patroon

Zeven letters en cijfers (oude indeling):

- Eén letter (wille keurige letter van het Griekse alfabet)
- Een streepje
- Zes cijfers

Acht letters en cijfers (nieuwe indeling):

- Twee letters met een hoofd letter in de Griekse en Latijnse alfabetten (ABEZHIKMNOPTYX)
- Een streepje
- Zes cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_greece_id_card"></a>Tref woord \_ Grieken land \_ id \_ kaart

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>HKID-nummer (Hong Kong Identity Card)

### <a name="format"></a>Indeling

Combi natie van 8-9 letters en cijfers plus optionele haakjes rond het laatste teken

### <a name="pattern"></a>Patroon

Combi natie van 8-9 letters:

- 1-2 letters (niet hoofdletter gevoelig)
- Zes cijfers
- Het laatste teken (wille keurig cijfer of letter A), het controle cijfer en is optioneel tussen haakjes.

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_hong_kong_id_card"></a>Tref woord \_ Hong \_ Kong \_ id- \_ kaart

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP-adres

### <a name="format"></a>Indeling

#### <a name="ipv4"></a>IPv6

Complex patroon: accounts voor geformatteerde (Peri Oden) en niet-geformatteerde versies van de IPv4-adressen

#### <a name="ipv6"></a>Ipconfiguration

Complex patroon voor de indeling van geformatteerde IPv6-nummers (waaronder dubbele punten)

### <a name="pattern"></a>Patroon

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_ipaddress"></a>Sleutel woord \_ IPAddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Permanent account nummer India (PAN)

### <a name="format"></a>Indeling

10 letters of cijfers

### <a name="pattern"></a>Patroon

10 letters of cijfers:

- Drie letters (niet hoofdletter gevoelig)
- Een letter in C, P, H, F, A, T, B, L, J, G (niet hoofdletter gevoelig)
- Een letter
- Vier cijfers
- Een letter (niet hoofdletter gevoelig)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_india_permanent_account_number"></a>Tref woord voor het \_ \_ permanente \_ account \_ nummer India

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Aadhaar-nummer (India-unieke identificatie)

### <a name="format"></a>Indeling

12 cijfers met optionele spaties of streepjes

### <a name="pattern"></a>Patroon

12 cijfers:

- Een cijfer, dat niet 0 of 1 is
- Drie cijfers
- Een optionele spatie of streepje
- Vier cijfers
- Een optionele spatie of streepje
- Het laatste cijfer, wat het controle cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_india_aadhar"></a>Tref woord \_ India \_ aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>KTP-nummer (Indonesië-identiteits kaart)

### <a name="format"></a>Indeling

16 cijfers met optionele Peri Oden

### <a name="pattern"></a>Patroon

16 cijfers:

- Provincie code van twee cijfers
- Een punt (optioneel)
- Regenten code van twee cijfers of plaatsnamen
- Code van twee cijfers in subdistrict
- Een punt (optioneel)
- Zes cijfers in de notatie DDMMYY, die de geboorte datum zijn
- Een punt (optioneel)
- Vier cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_indonesia_id_card"></a>Tref woord \_ Indonesië \_ id- \_ kaart

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Internationaal bankieren-account nummer (IBAN)

### <a name="format"></a>Indeling

Land nummer (twee letters) met controle cijfers (twee cijfers) plus :::no-loc text="bban"::: getal (Maxi maal 30 tekens)

### <a name="pattern"></a>Patroon

Het patroon moet het volgende bevatten:

- Land code van twee letters
- Twee controle cijfers (gevolgd door een optionele spatie)
- 1-7 groepen van vier letters of cijfers (kunnen worden gescheiden door spaties)
- 1-3 letters of cijfers

De notatie voor elk land is iets anders. Het IBAN-gevoelige informatie type geldt voor de volgende 60-landen:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Trefwoorden

Geen

## <a name="ireland-personal-public-service-pps-number"></a>Het PPS-nummer (Personal public service) in Ierland

### <a name="format"></a>Indeling

Oude indeling (tot en met 31 dec 2012):

- zeven cijfers gevolgd door 1-2 letters

Nieuwe indeling (1 Jan 2013 en na):

- zeven cijfers gevolgd door twee letters

### <a name="pattern"></a>Patroon

Oude indeling (tot en met 31 dec 2012):

- zeven cijfers
- een tot twee letters (niet hoofdletter gevoelig)

Nieuwe indeling (1 Jan 2013 en na):

- zeven cijfers
- een letter (niet hoofdletter gevoelig) die een alfabetisch controle cijfer is
- Een optionele letter in het bereik A-I of &quot; W&quot;

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_ireland_eu_national_id_card"></a>Tref woorden \_ Ierland \_ EU \_ National \_ id- \_ kaart

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Israël-bankrekening nummer

### <a name="format"></a>Indeling

13 cijfers

### <a name="pattern"></a>Patroon

Opgemaakt

- twee cijfers
- een streepje
- drie cijfers
- een streepje
- acht cijfers

Niet-opgemaakt:

- dertien opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_israel_bank_account_number"></a>Tref \_ woord \_ Israël \_ bankrekening \_ nummer

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Nationaal identificatie nummer Israël

### <a name="format"></a>Indeling

negen cijfers

### <a name="pattern"></a>Patroon

negen opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_israel_national_id"></a>Sleutel woord \_ Israël \_ nationale \_ id

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Licentie nummer van het Italië-stuur programma

Deze entiteit van het type gevoelige informatie is opgenomen in het licentie nummer van het EU-stuur programma en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

een combi natie van 10 letters en cijfers

### <a name="pattern"></a>Patroon

een combi natie van 10 letters en cijfers:

- Eén letter (niet hoofdletter gevoelig)
- de letter &quot; A &quot; of &quot; V &quot; (niet hoofdletter gevoelig)
- zeven cijfers
- Eén letter (niet hoofdletter gevoelig)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_italy_drivers_license_number"></a>Licentie nummer voor het sleutel woord \_ Italië- \_ Stuur Programma's \_ \_

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Japans bankrekening nummer

### <a name="format"></a>Indeling

zeven of acht cijfers

### <a name="pattern"></a>Patroon

bankrekening nummer:

- zeven of acht cijfers
- code bank rekening filiaal:
- vier cijfers
- een spatie of streepje (optioneel)
- drie cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_jp_bank_account"></a>Tref woord \_ JP \_ Bank \_ account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Sleutel woord \_ JP \_ Bank \_ filiaal \_ code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Rijbewijs nummer van het Japanse stuur programma

### <a name="format"></a>Indeling

12 cijfers

### <a name="pattern"></a>Patroon

12 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_jp_drivers_license_number"></a>Tref woord \_ JP \_ Stuur Programma's \_ licentie \_ nummer

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japans paspoort nummer

### <a name="format"></a>Indeling

twee letters gevolgd door zeven cijfers

### <a name="pattern"></a>Patroon

twee letters (niet hoofdletter gevoelig) gevolgd door zeven cijfers

#### <a name="keyword_jp_passport"></a>Tref woord \_ JP \_ Pass Port

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japans nummer van verblijfs kaart

### <a name="format"></a>Indeling

12 letters en cijfers

### <a name="pattern"></a>Patroon

12 letters en cijfers:

- twee letters (niet hoofdletter gevoelig)
- acht cijfers
- twee letters (niet hoofdletter gevoelig)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_jp_residence_card_number"></a>Tref woord \_ JP nummer van de \_ verblijfs \_ kaart \_

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japan-Resident registratie nummer

### <a name="format"></a>Indeling

11 cijfers

### <a name="pattern"></a>Patroon

11 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_jp_resident_registration_number"></a>Tref woord \_ JP \_ Resident \_ registratie \_ nummer

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japans sociaal verzekerings nummer (SIN)

### <a name="format"></a>Indeling

7-12 cijfers

### <a name="pattern"></a>Patroon

7-12 cijfers:

- vier cijfers
- een koppel teken (optioneel)
- zes cijfers of
- 7-12 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_jp_sin"></a>Tref woord \_ JP \_ Sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>ID-kaart nummer Maleisië

### <a name="format"></a>Indeling

12 cijfers met optionele afbreek streepjes

### <a name="pattern"></a>Patroon

12 cijfers:

- zes cijfers in de notatie JJMMDD, de geboorte datum
- een streepje (optioneel)
- code van twee letters voor plaatsen van geboorte
- een streepje (optioneel)
- drie wille keurige cijfers
- gender code van één cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_malaysia_id_card_number"></a>\_ \_ Id- \_ kaart \_ nummer sleutel woord Maleisië

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>BSN-nummer (Nederland burger)

### <a name="format"></a>Indeling

acht negen cijfers met optionele spaties

### <a name="pattern"></a>Patroon

acht negen cijfers:

- drie cijfers
- een spatie (optioneel)
- drie cijfers
- een spatie (optioneel)
- twee-drie cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_netherlands_eu_national_id_card"></a>Tref woorden \_ Nederland \_ EU \_ National \_ id- \_ kaart

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Nieuw-Zeelandse ministerie van status nummer

### <a name="format"></a>Indeling

drie letters, een spatie (optioneel) en vier cijfers

### <a name="pattern"></a>Patroon

- drie letters (niet hoofdletter gevoelig), met uitzonde ring van I en O
- een spatie (optioneel)
- vier cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_nz_terms"></a>Tref woord \_ Nz- \_ voor waarden

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Noor wegen-identificatie nummer

### <a name="format"></a>Indeling

11 cijfers

### <a name="pattern"></a>Patroon

11 cijfers:

- zes cijfers in de notatie DDMMYY, die de geboorte datum zijn
- individueel nummer van drie cijfers
- twee controle cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_norway_id_number"></a>Sleutel woord \_ Noor wegen \_ -id \_

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Uniforme id-nummer van de Filipijnen

### <a name="format"></a>Indeling

12 cijfers gescheiden door afbreek streepjes

### <a name="pattern"></a>Patroon

12 cijfers:

- vier cijfers
- een koppel teken
- zeven cijfers
- een koppel teken
- Eén cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_philippines_id"></a>Trefwoord \_ Filipijnen \_ -id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Polen-identiteits kaart

### <a name="format"></a>Indeling

drie letters en zes cijfers

### <a name="pattern"></a>Patroon

drie letters (niet hoofdletter gevoelig), gevolgd door zes cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_poland_national_id_passport_number"></a>Sleutel woord \_ Polen \_ National \_ id \_ Pass Port- \_ nummer

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Polen National ID (PESEL)

### <a name="format"></a>Indeling

11 cijfers

### <a name="pattern"></a>Patroon

- 6 cijfers die de geboorte datum vertegenwoordigen in de notatie JJMMDD
- 4 cijfers
- 1 controle cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_pesel_identification_number"></a>Tref woord \_ pesel- \_ identificatie \_ nummer

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Polen Pass Port-nummer

Deze entiteit van het type gevoelige informatie is opgenomen in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

twee letters en zeven cijfers

### <a name="pattern"></a>Patroon

Twee letters (niet hoofdletter gevoelig) gevolgd door zeven cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_poland_national_id_passport_number"></a>Sleutel woord \_ Polen \_ National \_ id \_ Pass Port- \_ nummer

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Portugal burger kaartnummer

### <a name="format"></a>Indeling

acht cijfers

### <a name="pattern"></a>Patroon

acht cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_portugal_citizen_card"></a>Tref woord \_ Portugal \_ burger \_ Card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Rijbewijs nummer van het Portugal-stuur programma

Deze entiteit van het gevoelige informatie type is alleen beschikbaar in het licentie nummer van het EU-stuur programma, afhankelijk van het gegevens type.

### <a name="format"></a>Indeling

twee patronen: twee letters gevolgd door 5-8 cijfers met speciale tekens

### <a name="pattern"></a>Patroon

Patroon 1: twee letters gevolgd door 5/6 met speciale tekens:

- Twee letters (niet hoofdletter gevoelig)
- Een afbreekstreepje
- Vijf of zes cijfers
- Een spatie
- Eén cijfer

Patroon 2: een letter gevolgd door 6/8 cijfers met speciale tekens:

- Eén letter (niet hoofdletter gevoelig)
- Een afbreekstreepje
- Zes of acht cijfers
- Een spatie
- Eén cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_eu_drivers_license_number"></a>Tref \_ woorden \_ het \_ licentie \_ nummer van het EU-stuur programma

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Tref \_ woorden \_ Portugal \_ het \_ licentie \_ nummer van het EU-stuur programma

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Land-ID Saudi-Arabië

### <a name="format"></a>Indeling

10 cijfers

### <a name="pattern"></a>Patroon

10 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_saudi_arabia_national_id"></a>Id van tref woord \_ Saudi \_ \_ \_ -Arabië

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>NRIC-nummer (National Registration Identity Card) voor Singapore

### <a name="format"></a>Indeling

negen letters en cijfers

### <a name="pattern"></a>Patroon

- negen letters en cijfers:
- de letter &quot; F &quot; , &quot; G &quot; , &quot; S &quot; of &quot; T &quot; (niet hoofdletter gevoelig)
- zeven cijfers
- een alfabetisch controle cijfer

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_singapore_nric"></a>Tref woord \_ Singapore \_ nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Zuid-Afrika-identificatie nummer

### <a name="format"></a>Indeling

13 cijfers die spaties mogen bevatten

### <a name="pattern"></a>Patroon

13 cijfers:

- zes cijfers in de notatie JJMMDD, de geboorte datum
- vier cijfers
- een burgerschap-indicator met één cijfer
- het cijfer &quot; 8 &quot; of &quot; 9&quot;
- Eén cijfer, wat een controlesom cijfer is

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_south_africa_identification_number"></a>Tref woord \_ Zuid- \_ Afrika- \_ identificatie \_ nummer

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Geresident registratie nummer Zuid-Korea

### <a name="format"></a>Indeling

13 cijfers met een afbreek streepje

### <a name="pattern"></a>Patroon

13 cijfers:

- zes cijfers in de notatie JJMMDD, de geboorte datum
- een koppel teken
- Eén cijfer bepaald door de Century en geslacht
- vier cijfers regio-geboorte code
- een cijfer dat wordt gebruikt om een onderscheid te maken tussen personen voor wie de voor gaande getallen identiek zijn
- een controle cijfer.

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_south_korea_resident_number"></a>Tref woord \_ Zuid- \_ Korea \_ Resident \_

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Sociaal-fiscaal nummer (SOFInummer) voor Spanje

Deze entiteit van het gevoelige informatie type is opgenomen in het sociaal-fiscaal nummer van de EU of een equivalent ID-gegevens type en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

11-12 cijfers

### <a name="pattern"></a>Patroon

11-12 cijfers:

- twee cijfers
- een slash (optioneel)
- zeven tot acht cijfers
- een slash (optioneel)
- twee cijfers

### <a name="keywords"></a>Trefwoorden

Geen

## <a name="sweden-national-id"></a>Zweden nationale ID

### <a name="format"></a>Indeling

10 of 12 cijfers en een optioneel scheidings teken

### <a name="pattern"></a>Patroon

10 of 12 cijfers en een optioneel scheidings teken:

- twee cijfers (optioneel)
- Zes cijfers in datum notatie JJMMDD
- scheidings teken &quot; - &quot; of &quot; + &quot; (optioneel)
- vier cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_swedish_national_identifier"></a>Tref woorden \_ Zweedse \_ nationale \_ id

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Zweden Pass Port-nummer

Deze entiteit van het type gevoelige informatie is opgenomen in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

acht cijfers

### <a name="pattern"></a>Patroon

acht opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_sweden_passport"></a>Tref woord \_ Zweden \_ Pass Port

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Tref woord \_ Pass Port

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT-code

### <a name="format"></a>Indeling

vier letters gevolgd door 5-31 letters of cijfers

### <a name="pattern"></a>Patroon

vier letters gevolgd door 5-31 letters of cijfers:

- bank code van vier letters (niet hoofdletter gevoelig)
- een optionele ruimte
- 4-28 letters of cijfers (het basis bankrekening nummer (BBAN))
- een optionele ruimte
- een tot drie letters of cijfers (rest van de BBAN)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_swift"></a>Tref woord \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Identificatie nummer van Taiwan

### <a name="format"></a>Indeling

Eén letter (in het Engels), gevolgd door negen cijfers

### <a name="pattern"></a>Patroon

Eén letter (in het Engels), gevolgd door negen cijfers:

- Eén letter (in het Engels, niet hoofdletter gevoelig)
- het cijfer &quot; 1 &quot; of &quot; 2&quot;
- acht cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_taiwan_national_id"></a>Sleutel \_ woord \_ Taiwan \_ -sofi

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Taiwan Pass Port-nummer

### <a name="format"></a>Indeling

- biometrisch paspoort nummer: negen cijfers
- niet-biometrisch paspoort nummer: negen cijfers

### <a name="pattern"></a>Patroon

biometrisch Pass Port-nummer:

- het teken &quot; 3&quot;
- acht cijfers

niet-biometrisch paspoort nummer:

- negen cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_taiwan_passport"></a>Tref woord \_ Taiwan \_ Pass Port

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Taiwan-Resident certificaat (ARC/TARC)-nummer

### <a name="format"></a>Indeling

10 letters en cijfers

### <a name="pattern"></a>Patroon

10 letters en cijfers:

- twee letters (niet hoofdletter gevoelig)
- acht cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_taiwan_resident_certificate"></a>Tref woord \_ Taiwan \_ Resident \_ certificaat

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Engelse rijbewijs nummer

Deze entiteit van het type gevoelige informatie is opgenomen in het licentie nummer van het EU-stuur programma en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

Combi natie van 18 letters en cijfers in de opgegeven indeling

### <a name="pattern"></a>Patroon

18 letters en cijfers:

- vijf letters (niet hoofdletter gevoelig) of het cijfer &quot; 9 &quot; in plaats van een letter
- Eén cijfer
- vijf cijfers in de datum notatie MMDDY voor geboorte datum (7-teken wordt verhoogd door 50 als het stuur programma vrouwelijk is, dat wil zeggen 51 tot 62 in plaats van 01 tot en met 12)
- twee letters (niet hoofdletter gevoelig) of het cijfer &quot; 9 &quot; in plaats van een letter
- vijf cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_uk_drivers_license"></a>Licentie voor het tref woord \_ UK- \_ Stuur Programma's \_

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Engelse aantal kiezers

### <a name="format"></a>Indeling

twee letters gevolgd door 1-4 cijfers

### <a name="pattern"></a>Patroon

twee letters (niet hoofdletter gevoelig) gevolgd door 1-4 cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_uk_electoral"></a>Tref woord \_ UK- \_ kiezers

- benoeming van de Raad
- het formulier voor de benoeming
- kiezers registratie
- kiezers draaien

## <a name="uk-national-health-service-number"></a>Engelse nationaal Health-Service nummer

### <a name="format"></a>Indeling

10-17 cijfers gescheiden door spaties

### <a name="pattern"></a>Patroon

10-17 cijfers:

- ofwel drie of tien cijfers
- een spatie
- drie cijfers
- een spatie
- vier cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_uk_nhs_number"></a>Tref woord \_ UK \_ NHS- \_ nummer

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Tref woord \_ UK \_ NHS \_ getal1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Tref woord \_ UK \_ NHS \_ number \_ Dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Engelse nationaal verzekerings nummer (NINO)

Deze entiteit van het gevoelige informatie type is opgenomen in het ABC-gegevens type van de EU en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

zeven tekens of negen tekens, gescheiden door spaties of streepjes

### <a name="pattern"></a>Patroon

twee mogelijke patronen:

- twee letters (geldige NINOs gebruiken alleen bepaalde tekens in dit voor voegsel, waarbij dit patroon wordt gevalideerd; niet hoofdletter gevoelig)
- zes cijfers
- A, B, C of 'D (zoals het voor voegsel, alleen bepaalde tekens zijn toegestaan in het achtervoegsel; niet hoofdletter gevoelig)

OF

- twee letters
- een spatie of streepje
- twee cijfers
- een spatie of streepje
- twee cijfers
- een spatie of streepje
- twee cijfers
- een spatie of streepje
- "A", "B", "C" of "


### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_uk_nino"></a>Tref woord \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Engelse Uniek belastingplichtige-referentie nummer

Dit gevoelige informatie type is alleen beschikbaar voor gebruik in:

- beleid voor preventie van gegevens verlies
- nalevings beleid voor communicatie
- Information governance
- record beheer
- Micro soft Cloud app Security

### <a name="format"></a>Indeling

10 cijfers zonder spaties en scheidings tekens

### <a name="pattern"></a>Patroon

10 cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keywords_uk_eu_tax_file_number"></a>Tref \_ woorden \_ IC-BTW- \_ \_ bestands \_ nummer UK

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Amerikaans bankrekening nummer

### <a name="format"></a>Indeling

6-17 cijfers

### <a name="pattern"></a>Patroon

6-17 opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_usa_bank_account"></a>Tref woord \_ USA \_ Bank \_ account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Rijbewijs nummer van het Amerikaanse stuur programma

### <a name="format"></a>Indeling

Is afhankelijk van de status

### <a name="pattern"></a>Patroon

is afhankelijk van de status, bijvoorbeeld New York:

- negen cijfers, opgemaakt zoals ddd, komen overeen.
- negen cijfers zoals xxxxxxxxx komen niet overeen.

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_us_drivers_license_abbreviations"></a>Tref \_ woord \_ Stuur Programma's \_ licentie \_ afkortingen

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Licentie voor het tref woord \_ Amerikaanse \_ Stuur Programma's \_

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Sleutel woord \_ [status \_ naam \_ ] \_ licentie \_ naam Stuur Programma's

- afkorting van provincie (bijvoorbeeld &quot; NY &quot; )
- status naam (bijvoorbeeld &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Amerikaans identificatie nummer voor de Amerikaanse belastingplichtige (ITIN)

### <a name="format"></a>Indeling

negen cijfers die beginnen met een &quot; 9 &quot; en een &quot; 7 &quot; of 8 &quot; bevatten &quot; als het vierde cijfer, optioneel opgemaakt met spaties of streepjes

### <a name="pattern"></a>Patroon

opgemaakt

- het cijfer &quot; 9&quot;
- twee cijfers
- een spatie of streepje
- een &quot; 7 &quot; of &quot; 8&quot;
- een cijfer
- een spatie of streepje
- vier cijfers

opgemaakt

- het cijfer &quot; 9&quot;
- twee cijfers
- een &quot; 7 &quot; of &quot; 8&quot;
- vijf cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_itin"></a>Tref woord \_ ITIN

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Amerikaans sociaal-fiscaal nummer (SSN)

### <a name="format"></a>Indeling

negen cijfers, die mogelijk in een opgemaakt of niet-opgemaakt patroon staan

>[!Note]
> Als een SSN vóór mid-2011 is uitgegeven, heeft het een sterke opmaak waarbij bepaalde onderdelen van het aantal moeten vallen binnen bepaalde bereiken, maar er is geen controlesom.
>

### <a name="pattern"></a>Patroon

vier functies zoeken naar burger servicenummers in vier verschillende patronen:

- Func \_ SSN zoekt burger servicenummers met een sterke notatie van 2011 die is opgemaakt met streepjes of spaties (ddd-DD-dddd of ddd DD dddd)
- Met de functie voor de FUNC \_ onopgemaakte \_ SSN vindt u burger servicenummers met een hoge indeling van 2011, die niet is opgemaakt als negen opeenvolgende cijfers (XXXXXXXXX)
- \_Met de wille keurige \_ opgemaakte functie \_ SSN vindt post-2011 burger servicenummers die zijn geformatteerd met streepjes of spaties (ddd-DD-dddd of ddd DD dddd)
- Met de functie \_ wille keurig \_ niet-geformatteerde \_ SSN vindt post-2011 burger servicenummers die niet zijn opgemaakt als negen opeenvolgende cijfers (XXXXXXXXX)

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_ssn"></a>Tref woord \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>VS/VK paspoort nummer

Het Verenigd Konink rijk het paspoort nummer van het gegevens type entiteit is beschikbaar in het gegevens type van het EU-paspoort nummer en is beschikbaar als een zelfstandige, gevoelige informatie van het type entiteit.

### <a name="format"></a>Indeling

negen cijfers

### <a name="pattern"></a>Patroon

negen opeenvolgende cijfers

### <a name="keywords"></a>Trefwoorden

#### <a name="keyword_passport"></a>Tref woord \_ Pass Port

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
