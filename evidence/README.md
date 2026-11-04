# Preuves de validation — 31 octobre 2026

| Fichier | Origine |
|---|---|
| [add-http-headers.txt](add-http-headers.txt) | En-têtes reçus lors de l’appel HTTP direct |
| [add-response.xml](add-response.xml) | Réponse réelle à l’appel HTTP direct `Add(15,20)` |
| [generated-CalculatorSoap-Add.xml](generated-CalculatorSoap-Add.xml) | Modèle SOAP 1.1 généré par SoapUI, avant saisie des valeurs |
| [generated-CalculatorSoap12-Add.xml](generated-CalculatorSoap12-Add.xml) | Modèle SOAP 1.2 généré par SoapUI, avant saisie des valeurs |
| [soapui-validation.txt](soapui-validation.txt) | Observations exportées pendant l’import et l’exécution réelle avec SoapUI 5.9.1 |
| [soapui-add-response.xml](soapui-add-response.xml) | Réponse réellement reçue par le test SoapUI |

Le moteur SoapUI a été utilisé en mode automatisé. Aucune capture d’interface graphique n’est présentée. Le projet complet est disponible à la racine : [calculator-soapui-project.xml](../calculator-soapui-project.xml).

## Vérification du 4 novembre 2026

Un nouvel appel HTTP SOAP `Add(15,20)` a renvoyé `200 OK` et `35`. La réponse se trouve dans [add-response-2026-11-04.xml](add-response-2026-11-04.xml) et le relevé dans [verification-2026-11-04.json](verification-2026-11-04.json). Cette vérification a été effectuée par HTTP directement ; elle ne remplace pas l’exécution SoapUI enregistrée le 31 octobre.
