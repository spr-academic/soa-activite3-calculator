# SOA — Activité 3 : Atelier Calculator

**ESPRIT · Année universitaire 2026–2027**  
**Étudiant : Sayed Touati**  
**Sujet : Découvrir, analyser et consommer un service Web SOAP**

Ce travail analyse le contrat du service Calculator et sa consommation par messages SOAP. Le service étant déjà fourni, aucune implémentation de serveur ni application Java n'est nécessaire pour cet atelier.

## Livrables

| Fichier | Contenu |
|---|---|
| [Contrat WSDL](wsdl/calculator.wsdl) | Copie du contrat récupéré auprès du fournisseur |
| [Requête Add](requests/add.xml) | Message SOAP 1.1 pour `Add(15,20)` |
| [Réponse du service](evidence/add-response.xml) | Réponse XML reçue lors de l'appel HTTP |
| [En-têtes HTTP](evidence/add-http-headers.txt) | Statut et métadonnées de la réponse |
| [Projet SoapUI](calculator-soapui-project.xml) | Deux interfaces, requêtes générées et test de l’addition |
| [Compte rendu d’exécution SoapUI](evidence/soapui-validation.txt) | Opérations importées, assertions et résultat du test |
| [Réponse reçue par SoapUI](evidence/soapui-add-response.xml) | Résultat de l’exécution du test |

**WSDL du fournisseur :** http://www.dneonline.com/calculator.asmx?wsdl  
**Point d'accès :** http://www.dneonline.com/calculator.asmx  
**Namespace métier :** `http://tempuri.org/`

## 1. Analyse

### 1.1 Informations nécessaires pour invoquer une opération

Il faut connaître le point d'accès, le nom exact de l'opération, les paramètres d'entrée, leur ordre et leurs types, ainsi que les espaces de noms XML. Il faut également respecter la version SOAP, le format du message et les informations HTTP définies par le binding.

Pour l'addition étudiée :

| Élément | Valeur relevée |
|---|---|
| Service | `Calculator` |
| Opération | `Add` (la casse est significative) |
| Paramètres | `intA`, puis `intB` |
| Types | `xsd:int` pour les deux paramètres |
| Style et encodage | Document / literal |
| Action SOAP 1.1 | `http://tempuri.org/Add` |

### 1.2 Où trouver ces informations ?

Elles se trouvent dans le WSDL : `types` décrit les éléments XML et leurs types, `message` les messages échangés, `portType` les opérations, `binding` leur représentation technique et `service` les points d'accès.

### 1.3 Pourquoi l'URL se termine-t-elle par `?wsdl` ?

Sur ce service ASMX, le paramètre `?wsdl` permet d'obtenir la description du service en **Web Services Description Language**. Il s'agit de l'adresse du contrat ; l'adresse d'invocation est indiquée dans le port du service.

### 1.4 Le WSDL suffit-il pour appeler une opération ?

Le contrat contient les informations nécessaires à la construction d'une requête conforme pour ce service. Il ne réalise cependant aucun appel : il faut un client tel que SoapUI et une connexion au serveur. Seule l'analyse de la réponse permet de vérifier le résultat ou de détecter une erreur SOAP.

## 2. Informations disponibles dans le WSDL

| Information | Disponible ? | Emplacement et observation |
|---|---|---|
| Nom du service | Oui | `wsdl:service/@name` : `Calculator` |
| Opérations | Oui | `wsdl:portType/wsdl:operation` : `Add`, `Subtract`, `Multiply`, `Divide` |
| Paramètres d'entrée | Oui | `wsdl:types`, éléments de chaque opération : `intA`, `intB` |
| Types des paramètres | Oui | Schéma XSD : `s:int`, soit des entiers signés sur 32 bits |
| Adresse du service | Oui | `soap:address/@location` et `soap12:address/@location` |
| Format des messages | Oui | Schéma XSD et bindings `document` / `literal` |
| Namespace | Oui | `targetNamespace` : `http://tempuri.org/` |

Le contrat expose deux bindings, **CalculatorSoap** (SOAP 1.1) et **CalculatorSoap12** (SOAP 1.2), pour les mêmes quatre opérations.

## 3. Conception du message `Add(15,20)`

```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <Add xmlns="http://tempuri.org/">
      <intA>15</intA>
      <intB>20</intB>
    </Add>
  </soap:Body>
</soap:Envelope>
```

- **Envelope** délimite le message SOAP 1.1.
- **Header** est vide : aucun en-tête applicatif n'est requis pour cet appel. Il peut être omis.
- **Body** contient l'opération `Add` dans le namespace du service.
- **intA** et **intB** portent les valeurs 15 et 20, dans l'ordre défini par le schéma.

L'appel SOAP 1.1 utilise HTTP `POST`, `Content-Type: text/xml; charset=utf-8` et `SOAPAction: "http://tempuri.org/Add"`. L'opération est nommée `Add` dans le contrat, même si l'énoncé emploie `add`.

## 4. Discussion

L'affirmation « Le WSDL contient tout ce qu'il faut. Il est donc inutile de regarder les messages SOAP » est incorrecte.

Le WSDL définit le contrat attendu. L'observation des messages permet de vérifier que le client envoie les bonnes valeurs, les bons namespaces et une structure conforme. L'analyse de la réponse permet ensuite de contrôler le résultat et de comprendre un éventuel `SOAP Fault`. Un contrat correct ne garantit ni la disponibilité du service, ni la réussite d'un appel.

## 5. Validation expérimentale

### Appel du service

L'appel HTTP effectué le **31 octobre 2026** a renvoyé **HTTP 200 OK** et le résultat **35** pour `Add(15,20)`. La réponse reçue contient :

```xml
<AddResponse xmlns="http://tempuri.org/">
  <AddResult>35</AddResult>
</AddResponse>
```

Le résultat reçu correspond au calcul attendu : **15 + 20 = 35**. Aucun élément `SOAP Fault` n'est présent dans cette réponse.

Une nouvelle vérification HTTP le **4 novembre 2026** a également renvoyé **200 OK** et **35**. La [réponse reçue](evidence/add-response-2026-11-04.xml) et le [relevé de l’appel](evidence/verification-2026-11-04.json) sont conservés dans le dépôt. Cette vérification complète les résultats SoapUI du 31 octobre.

### Workshop SoapUI

Le projet **SOA-Activite3-Calculator** a été créé et exécuté le **31 octobre 2026** avec **SoapUI Open Source 5.9.1**, à partir de l’URL WSDL du fournisseur. L’import, la génération des requêtes et le test ont été réalisés avec le moteur SoapUI en mode automatisé, sans interface graphique.

#### 5.1 Création du projet à partir du WSDL

L’import a créé deux interfaces : `CalculatorSoap` pour SOAP 1.1 et `CalculatorSoap12` pour SOAP 1.2. Le [projet enregistré](calculator-soapui-project.xml) contient également la suite **Validation Calculator**, le cas **Add 15 et 20** et l’étape **Add(15,20)**.

#### 5.2 Éléments générés automatiquement

Chaque interface contient les quatre opérations `Add`, `Divide`, `Multiply`, `Subtract`, avec une requête exemple pour chacune : huit requêtes exemples au total. Les modèles originaux de `Add` sont conservés pour [SOAP 1.1](evidence/generated-CalculatorSoap-Add.xml) et [SOAP 1.2](evidence/generated-CalculatorSoap12-Add.xml).

#### 5.3 Informations récupérées dans le contrat

SoapUI a récupéré les bindings, leurs versions SOAP, les noms des opérations, les actions SOAP, le point d’accès et les schémas des messages. Ces schémas lui permettent de générer les paramètres `intA` et `intB` dans le namespace `http://tempuri.org/`.

#### 5.4 Comparaison avec l’analyse du WSDL

Les quatre opérations générées correspondent exactement à celles du contrat. Leur ordre d’affichage peut varier ; leurs noms et leur nombre sont identiques. L’opération `Add` reçoit bien deux entiers, `intA` puis `intB`.

#### 5.5 Structure du message généré

SoapUI génère automatiquement `Envelope`, un `Header` vide, `Body`, les déclarations de namespaces, l’élément de l’opération et les paramètres. Les valeurs sont initialement représentées par `?` ; elles doivent être renseignées avant l’appel. Le préfixe `tem` choisi par SoapUI et le namespace par défaut utilisé dans notre requête désignent le même namespace métier.

#### Résultat du test

Le test utilise le binding **SOAP 1.1**. Les deux valeurs ont été fixées à 15 et 20.

| Contrôle | Résultat observé |
|---|---|
| Import du WSDL | Réussi : deux interfaces, quatre opérations par interface |
| Exécution du cas de test | `FINISHED` |
| Statut HTTP | `200` |
| Assertion `SOAP Response` | `VALID` |
| Assertion `XPath Match` sur `AddResult` | `VALID` : valeur égale à `35` |
| Présence d’un `SOAP Fault` | Aucune dans la réponse reçue |

Le compte rendu et la réponse XML sont disponibles dans [evidence/](evidence/README.md). Les requêtes SOAP 1.2 ont été générées et examinées ; le test métier enregistré porte sur SOAP 1.1.

## Reproduire la validation

1. Télécharger ou cloner ce dépôt.
2. Dans SoapUI, choisir **File → Import Project** et ouvrir `calculator-soapui-project.xml`.
3. Ouvrir **Validation Calculator → Add 15 et 20 → Add(15,20)**.
4. Exécuter le cas de test et vérifier que les deux assertions passent et que `AddResult` vaut `35`.

Le service distant doit être accessible. Le contrat utilisé lors du travail est conservé dans `wsdl/calculator.wsdl` pour consultation.

## Conclusion

Le contrat a permis d’identifier les opérations et de construire une requête conforme. L’exécution réelle avec SoapUI a confirmé le fonctionnement de l’addition pour le cas étudié : **Add(15,20) = 35**.
