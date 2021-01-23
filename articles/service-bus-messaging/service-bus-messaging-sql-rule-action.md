---
title: Regel voor Azure Service Bus abonnement de SQL-actie syntaxis | Microsoft Docs
description: Dit artikel bevat een verwijzing voor de syntaxis van de SQL-regel actie. De acties worden geschreven in de op SQL-taal gebaseerde syntaxis die wordt uitgevoerd op een bericht.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: f7b8cdfcccc22508b98a42391d2a0ef9955232d0
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742676"
---
# <a name="subscription-rule-sql-action-syntax"></a>Syntaxis van SQL-actie voor abonnements regel

Een *SQL-actie* wordt gebruikt om de meta gegevens van een bericht te manipuleren nadat een bericht is geselecteerd door een filter van een abonnements regel. Het is een tekst expressie die wordt geleaneerd op een subset van de SQL-92-standaard. Actie-expressies worden gebruikt met het `sqlExpression` element van de eigenschap Action van een service bus `Rule` in een [Azure Resource Manager sjabloon](service-bus-resource-manager-namespace-topic-with-rule.md)of het argument van de Azure cli- `az servicebus topic subscription rule create` opdracht [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) , en verschillende SDK-functies waarmee abonnements regels kunnen worden beheerd.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumenten  
  
-   `<scope>` is een optionele teken reeks die het bereik van de aangeeft `<property_name>` . Geldige waarden zijn `sys` of `user` . De `sys` waarde geeft de systeem Scope aan waar de `<property_name>` naam van een open bare eigenschap van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)is. `user` Hiermee wordt het gebruikers bereik aangegeven waarbij `<property_name>` een sleutel van de [BrokeredMessage-klassen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) woordenlijst is. `user` Scope is het standaard bereik als `<scope>` niet is opgegeven.  
  
### <a name="remarks"></a>Opmerkingen  

Er is een fout opgetreden bij een poging toegang te krijgen tot een niet-bestaande systeem eigenschap. een poging om toegang te krijgen tot een niet-bestaande gebruikers eigenschap is geen fout. In plaats daarvan wordt een niet-bestaande gebruikers eigenschap intern geëvalueerd als een onbekende waarde. Een onbekende waarde wordt speciaal behandeld tijdens de evaluatie van de operator.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenten  
 `<regular_identifier>` is een teken reeks die wordt vertegenwoordigd door de volgende reguliere expressie:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Dit betekent een wille keurige teken reeks die begint met een letter en wordt gevolgd door een of meer onderstrepings tekens/letter/cijfer.  
  
 `[:IsLetter:]` betekent elk Unicode-teken dat is gecategoriseerd als Unicode-letter. `System.Char.IsLetter(c)` retourneert `true` `c` een Unicode-letter.  
  
 `[:IsDigit:]` betekent elk Unicode-teken dat is gecategoriseerd als een decimaal cijfer. `System.Char.IsDigit(c)` retourneert `true` of `c` een Unicode-cijfer is.  
  
 Een `<regular_identifier>` kan geen gereserveerd tref woord zijn.  
  
 `<delimited_identifier>` is een teken reeks die wordt Inge sloten met vier Kante haken links/rechts ([]). Een rechter rechte haak wordt weer gegeven als twee rechter rechte haken. Hier volgen enkele voor beelden van `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` is een teken reeks die tussen dubbele aanhalings tekens is geplaatst. Een dubbel aanhalings teken in id wordt weer gegeven als twee dubbele aanhalings tekens. Het is niet raadzaam om id's van aanhalings tekens te gebruiken omdat deze eenvoudig kunnen worden verward met een teken reeks constante. Gebruik, indien mogelijk, een gescheiden id. Hier volgt een voor beeld van `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<pattern>` moet een expressie zijn die als een teken reeks wordt geëvalueerd. Deze wordt gebruikt als een patroon voor de LIKE-operator.      De naam kan de volgende joker tekens bevatten:  
  
-   `%`: Een wille keurige teken reeks van nul of meer tekens.  
  
-   `_`: Eén wille keurig teken.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<escape_char>` moet een expressie zijn die als een teken reeks met een lengte van 1 wordt geëvalueerd. Deze wordt gebruikt als escape-teken voor de operator LIKE.  
  
 Bijvoorbeeld, `property LIKE 'ABC\%' ESCAPE '\'` komt overeen met `ABC%` een teken reeks die begint met `ABC` .  
  
## <a name="constant"></a>bedrag  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenten  
  
-   `<integer_constant>` is een teken reeks die niet tussen aanhalings tekens staat en geen decimale punten bevat. De waarden worden intern opgeslagen `System.Int64` en volgen hetzelfde bereik.  
  
     Hier volgen enkele voor beelden van lange constanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` is een teken reeks met getallen die niet tussen aanhalings tekens staan en die een decimaal teken bevatten. De waarden worden intern opgeslagen `System.Double` en volgen hetzelfde bereik/dezelfde precisie.  
  
     In een toekomstige versie kan dit nummer worden opgeslagen in een ander gegevens type ter ondersteuning van nauw keurige semantiek, zodat u niet hoeft te vertrouwen op het feit dat het onderliggende gegevens type `System.Double` voor is `<decimal_constant>` .  
  
     Hier volgen enkele voor beelden van decimale constanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` is een getal dat is geschreven in een weten schappelijke notatie. De waarden worden intern opgeslagen `System.Double` en volgen hetzelfde bereik/dezelfde precisie. Hier volgen enkele voor beelden van constanten met een benaderende waarde:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Opmerkingen
  
Booleaanse constanten worden vertegenwoordigd door de tref woorden `TRUE` of `FALSE` . De waarden worden opgeslagen als `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
Teken reeks constanten worden tussen enkele aanhalings tekens geplaatst en bevatten geldige Unicode-tekens. Een enkel aanhalings teken in een teken reeks constante wordt weer gegeven als twee enkele aanhalings tekens.  
  
## <a name="function"></a>functieassembly  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Opmerkingen  

De `newid()` functie retourneert een `System.Guid` gegenereerd door de- `System.Guid.NewGuid()` methode.  
  
De `property(name)` functie retourneert de waarde van de eigenschap waarnaar wordt verwezen door `name` . De `name` waarde kan een geldige expressie zijn die een teken reeks waarde retourneert.  

[!INCLUDE [service-bus-filter-examples](../../includes/service-bus-filter-examples.md)]
  
## <a name="considerations"></a>Overwegingen

- SET wordt gebruikt om een nieuwe eigenschap te maken of de waarde van een bestaande eigenschap bij te werken.
- VERWIJDEREN wordt gebruikt om een eigenschap te verwijderen.
- SET voert impliciete omzetting uit indien mogelijk wanneer het expressie type en het bestaande eigenschaps type verschillend zijn.
- De actie mislukt als er naar niet-bestaande systeem eigenschappen wordt verwezen.
- De actie mislukt als er naar niet-bestaande gebruikers eigenschappen wordt verwezen.
- Een niet-bestaande gebruikers eigenschap wordt intern geëvalueerd als ' onbekend ', volgens dezelfde semantiek als [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) bij het evalueren van Opera tors.

## <a name="next-steps"></a>Volgende stappen

- [SQLRuleAction-klasse (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction-klasse (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction-klasse (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (Java script)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)