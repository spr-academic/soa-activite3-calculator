# Activité 3 — Atelier Calculator : découvrir, analyser et consommer un service Web SOAP

## Périmètre et état

Source : énoncé PROSIT_SOAP.pdf fourni par l'étudiant, trois pages.
Le fournisseur propose un service Calculator existant : il faut analyser son contrat et le tester avec SoapUI, avant une éventuelle intégration Java. Aucune implémentation Java ni serveur n'est demandé ici.

WSDL indiqué : http://www.dneonline.com/calculator.asmx?wsdl

État au 31 octobre 2026 : énoncé lu ; réponses préparées ; téléchargement du WSDL et appels distants non réalisés avec succès (résolution DNS impossible dans l'environnement). Les détails propres au service ci-dessous sont des hypothèses à confirmer dans son WSDL actuel. Aucun succès SoapUI n'est revendiqué.

## Partie 1 — Analyse

1. Il faut connaître l'adresse du point d'accès, l'opération exacte (casse comprise), les paramètres, leur ordre et leurs types, les namespaces, le format XML, la version SOAP et les informations de transport : méthode HTTP, Content-Type et, en SOAP 1.1 sur HTTP, SOAPAction. Il faut également respecter les exigences de sécurité si le contrat en prévoit.
2. On trouve ces informations dans le WSDL et les schémas XSD qu'il référence, complétés si nécessaire par les politiques et la documentation du fournisseur.
3. Sur ce service ASMX, `?wsdl` demande la description du service en Web Services Description Language. Cette URL décrit le contrat ; l'adresse d'invocation se lit dans le port du service, pas en supprimant arbitrairement le suffixe.
4. Un WSDL complet, avec ses imports, peut suffire à construire une requête conforme pour un service simple. Il n'exécute pas l'appel et ne prouve ni la disponibilité du serveur, ni la réussite d'une opération. Il faut un client, une connexion réseau et l'analyse de la réponse.

## Partie 2 — Hypothèses à vérifier dans le WSDL

Les chemins suivants correspondent à un contrat WSDL 1.1.

| Information | Disponible ? | Où ? |
|---|---|---|
| Nom du service | Oui | `wsdl:service/@name` |
| Opérations disponibles | Oui | `wsdl:portType/wsdl:operation`, puis association dans `wsdl:binding` |
| Paramètres d'entrée | Oui | `wsdl:message/wsdl:part` et éléments du schéma dans `wsdl:types` ou XSD importé |
| Type des paramètres | Oui | Attribut `type` et définitions XSD des éléments |
| Adresse du service | Oui | `wsdl:service/wsdl:port/soap:address/@location` (ou extension SOAP 1.2) |
| Format des messages | Oui | Messages et schémas ; `soap:binding/@style`, `soap:body/@use` dans le binding |
| Namespace | Oui | `targetNamespace` du WSDL et du schéma ; déclarations `xmlns` |

À confirmer : opération `Add`, paramètres `intA` et `intB` de type `xsd:int`, namespace `http://tempuri.org/`, et éventuelles opérations `Subtract`, `Multiply`, `Divide`. Ces valeurs ne sont pas des observations d'un WSDL téléchargé pendant cette séance.

## Partie 3 — Conception de add(15,20)

Le fichier [requests/add.xml](requests/add.xml) propose une enveloppe SOAP 1.1 à vérifier contre le contrat. Le nom attendu sur le réseau est `Add`, même si l'énoncé utilise `add`.

- `Envelope` est la racine du message, dans le namespace SOAP 1.1.
- `Header` est vide ; il est facultatif si aucun en-tête applicatif n'est requis.
- `Body` contient `Add` dans le namespace du service.
- `intA` vaut 15 et `intB` vaut 20.

Transport proposé, à confirmer dans le binding SOAP 1.1 :

```http
POST /calculator.asmx HTTP/1.1
Host: www.dneonline.com
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://tempuri.org/Add"
```

Résultat métier attendu : 35. Ce résultat attendu n'est pas une réponse effectivement reçue.

## Partie 4 — Discussion

Nous ne sommes pas d'accord avec l'affirmation selon laquelle il serait inutile de regarder les messages SOAP. Le WSDL décrit la structure attendue, les opérations et leur transport. L'observation du message réellement envoyé permet de contrôler les valeurs, les namespaces et la conformité de l'appel. L'analyse de la réponse permet de vérifier le résultat ou de comprendre un SOAP Fault. Un contrat valide ne garantit pas un service disponible ni une réponse correcte.

## Partie 5 — Validation expérimentale avec SoapUI

Cette partie reste à exécuter. Les éléments ci-dessous sont un protocole et des attentes, pas un compte rendu d'observations.

1. Dans SoapUI, créer un nouveau projet SOAP nommé `SOA-Activite3-Calculator`, saisir l'URL du WSDL et activer la création des requêtes exemples. En cas d'échec, conserver le message d'erreur exact.
2. Relever les interfaces/bindings, les opérations et les requêtes exemples générées. Vérifier quelles versions SOAP sont réellement proposées.
3. Identifier les données récupérées : opérations, paramètres et types, namespaces, adresse du service, formats et actions SOAP. Les comparer au tableau de la partie 2.
4. Comparer les noms exacts et le nombre d'opérations au contrat téléchargé. Noter toute différence ; ne pas conclure à une correspondance avant observation.
5. Ouvrir la requête de `Add` : identifier l'enveloppe, l'éventuel Header, le Body, les namespaces, l'opération et les paramètres générés. Remplacer les valeurs exemples par 15 et 20, puis envoyer.
6. Vérifier la réponse XML et le résultat 35 ; relever le statut HTTP et l'absence ou la présence d'un SOAP Fault. Un statut HTTP seul ne valide pas le résultat métier.
7. Enregistrer le projet SoapUI et les preuves anonymisées dans `evidence/` : WSDL effectivement utilisé, requête, réponse et capture du résultat.

### Compte rendu à compléter après exécution

| Élément | Observation |
|---|---|
| Date et version de SoapUI | À renseigner |
| Import du WSDL | Non validé |
| Nom du service et adresse | À relever |
| Bindings et opérations | À relever |
| Éléments générés automatiquement | À relever |
| Comparaison avec les hypothèses | À effectuer |
| Résultat de Add(15,20) | Attendu : 35 ; reçu : non observé |
| Statut HTTP / SOAP Fault | Non observé |
| Preuves | Aucune exécution SoapUI enregistrée |
