# Practical Session #1: Introduction

Yoann DEWILDE\
Enora DANILO\
M2 ILA - Groupe 1

---

1. Find in news sources a general public article reporting the discovery of a software bug. Describe the bug. If possible, say whether the bug is local or global and describe the failure that manifested its presence. Explain the repercussions of the bug for clients/consumers and the company or entity behind the faulty program. Speculate whether, in your opinion, testing the right scenario would have helped to discover the fault.

2. Apache Commons projects are known for the quality of their code and development practices. They use dedicated issue tracking systems to discuss and follow the evolution of bugs and new features. The following link https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-794?filter=doneissues points to the issues considered as solved for the Apache Commons Collections project. Among those issues find one that corresponds to a bug that has been solved. Classify the bug as local or global. Explain the bug and the solution. Did the contributors of the project add new tests to ensure that the bug is detected if it reappears in the future?

3. Netflix is famous, among other things we love, for the popularization of *Chaos Engineering*, a fault-tolerance verification technique. The company has implemented protocols to test their entire system in production by simulating faults such as a server shutdown. During these experiments they evaluate the system's capabilities of delivering content under different conditions. The technique was described in [a paper](https://arxiv.org/ftp/arxiv/papers/1702/1702.05843.pdf) published in 2016. Read the paper and briefly explain what are the concrete experiments they perform, what are the requirements for these experiments, what are the variables they observe and what are the main results they obtained. Is Netflix the only company performing these experiments? Speculate how these experiments could be carried in other organizations in terms of the kind of experiment that could be performed and the system variables to observe during the experiments.

4. [WebAssembly](https://webassembly.org/) has become the fourth official language supported by web browsers. The language was born from a joint effort of the major players in the Web. Its creators presented their design decisions and the formal specification in [a scientific paper](https://people.mpi-sws.org/~rossberg/papers/Haas,%20Rossberg,%20Schuff,%20Titzer,%20Gohman,%20Wagner,%20Zakai,%20Bastien,%20Holman%20-%20Bringing%20the%20Web%20up%20to%20Speed%20with%20WebAssembly.pdf) published in 2018. The goal of the language is to be a low level, safe and portable compilation target for the Web and other embedding environments. The authors say that it is the first industrial strength language designed with formal semantics from the start. This evidences the feasibility of constructive approaches in this area. Read the paper and explain what are the main advantages of having a formal specification for WebAssembly. In your opinion, does this mean that WebAssembly implementations should not be tested? 

5.  Shortly after the appearance of WebAssembly another paper proposed a mechanized specification of the language using Isabelle. The paper can be consulted here: https://www.cl.cam.ac.uk/~caw77/papers/mechanising-and-verifying-the-webassembly-specification.pdf. This mechanized specification complements the first formalization attempt from the paper. According to the author of this second paper, what are the main advantages of the mechanized specification? Did it help improving the original formal specification of the language? What other artifacts were derived from this mechanized specification? How did the author verify the specification? Does this new specification removes the need for testing?

## Answers

### Question 1


Un bug sur Google Drive supprime des dossiers et fichiers. Certains utilisateurs ont perdu des données depuis plusieurs mois (https://www.lemondeinformatique.fr/actualites/lire-un-bug-dans-google-drive-supprime-certains-fichiers-92255.html).
Il a été découvert le 22/11/2023 par [Yeonjoong](https://support.google.com/drive/thread/245055606/google-drive-files-suddenly-disappeared-the-drive-literally-went-back-to-condition-in-may-2023?hl=en&sjid=14021173013966303901-EU3?hl=en&sjid=14021173013966303901-EU), lorsqu'il a expliqué avoir perdu ses données depuis mai.
De plus, il survient sur les versions web et desktop.
Google n'ayant pas communiqué sur la cause pour l'instant, on ne peut pas déterminer s'il est global ou local.\
Le bug a supprimé des données pour plusieurs utilisateurs, depuis plusieurs mois. 
Google pourrait perdre des utilisateurs de Drive, ou d'autres de ses services, et également avoir une moins bonne réputation.

Tester le bon scénario me semble difficile, car il y a très peu d'informations sur le bug.

### Question 2

Bug: https://issues.apache.org/jira/browse/COLLECTIONS-813

Le bug concerne la méthode Collection.retainAll(). Si l'un des paramètres est `null`, elle doit lancer une `NullPointerException` (NPE). Or, elle ne le fait pas.

Le bug est local car il concerne directement le code.
La solution a été de rajouter la vérification du paramètre dans la fonction. \
Le test qui a permis de découvrir le bug n'a pas été modifié, car il échouait. Depuis la correction, il réussit.
Des tests ont également été rajoutés.

### Question 3

Le système de Netflix est tellement complexe qu'il ne peut être simulé entièrement.
Des expériences de tolérance aux fautes sont donc effectuées sur l'environnement de production.
Le but de ces tests n'est pas de prouver que le système répond aux spécifications, mais de voir si les utilisateurs remarquent ou non ces fautes.\
Ces expériences peuvent être :
- éteindre des VMs ;
- injecter des latences entre les requêtes entre services ;
- faire échouer des requêtes entre services ;
- faire échouer un service interne ;
- rendre une région Amazon entière non disponible.

La mise en place des ces exépriences requiert :
- d'émettre des hypothèses sur l'influence des expériences sur le *steady-state behaviour*, c'est-à-dire le comportement normal de l'application, plus précisément des métriques mesurées pendant l'expérience ;
- des variables indépendantes ;
- des variables dépendantes ;
- le contexte.

Les variables observées sont :
- disponibilité du système, car le dysfonctionnement de certains services n'impacte pas forcément la disponibilité du système ;
- *(stream) starts per second* (SPS) : nombre de lancements de vidéos à chaque seconde, pour savoir si les utilisateurs peuvent regarder leur contenu ;
- nouvelles créations de comptes par seconde : indique si le service est disponibles ou non.


Définition d'une expérience :
1. Définir le *steady-state behaviour*.
2. Définir 2 groupes d'utilisateur : le groupe de contrôle, qui utilise l'application normale ; et le groupe expériemental, qui utilise l'application avec l'erreur introduite.
3. Admettre que ce comportement normal perdure dans les 2 groupes.
4. Introduire des variables qui représentent des événements réels, comme des crashs de serveurs, des dysfonctionnements du hardware, ...
5. Démontrer une différence entre les comportements du système pour les 2 groupes.

Si les 2 comportements sont identiques, alors le système est résilient à l'expérience. 
Sinon, l'expérience montre une amélioration possible.


Résultats:
Certaines fautes nécessitent des tests d'intégration, car elles sont liées aux interactions entre les composants.

Leur système est fiable
De plus, les ingénieurs de Netflix savent reconnaître les fluctuations normales d'une métrique des fluctuations anormales.
Cependant, 

Sont-ils les seuls ?
Non, Google, Facebook, Amazon, Microsoft utilisent cette technique.


### Question 4

Avantages de la spécification formelle :
- typage sûr : tout programme plante, diverge, ou se termine avec les valeurs des bons types ;
- abstraction du *host environment* (implémentation de l'interpréteur WebAssembly au sein d'un moteur JS), 
- 

A mon avis, les implémentations de WebAssembly doivent être testées, afin de vérifier qu'elles respectent la spécification.

### Question 5

#### Avantages

- Les calculs arithmétiques se basent sur leur implémentation dans Isabelle, qui est prouvée.
- Les calculs arithmétiques, le tas et le comportement du *host environment* sont abstraits grâce aux *locales* d'Isabelle. La *locale* est un **....** Ici, elle encode les propriétés du tas.
Cela permet de représenter toutes les possibilités, tant que les preuves respectent les hypothèses de la *locale*.

#### Ca a amélioré la spécification ?

A montré plusieurs défaillances :
- Le typage n'était pas assez robuste :
    - L'instruction `trap`, qui correspond à une exception ingérable, doit se propager jusqu'au début de la pile, et arrêter le programme. 

    - L'opération `return` pouvait être considérée comme bien typée alors qu'elle n'était pas dans une fonction.

    - Les *host functions* (fonction exécutée sur le *host environment*) doivent respecter des invariants. Or, certains invariants n'étaient pas assez précis dans la spécification. Par exemple : une *host function* peut modifier le type d'une variable immuable globale, alors que cela ne devrait pas être possible.

L'auteur a trouvé un contre-exemple pour chaque propriété. Les failles ont été communiquées au groupe de travail de WebAssembly, et ont été corrigées dans la spécification du langage.

#### Quels artéfacts ont été dérivés ?

Un interpréteur et un *type checker* vérifiés.

#### Comment l'auteur vérifie cette vérification ?

Tests de l'interpréteur :

- Tests de conformité disponibles sur le dépôt WebAssembly.
- Tests *fuzzing* contre plusieurs interpréteurs existants. But : valiser et détecter des bugs dans les autres implémentations. Les tests sont générés en C avec l'outil CSmith, et convertis en WebAssembly grâce à la chaîne d'outils Binaryen. Cela simule la façon dont la plupart des programmes WebAssembly sont créés.

Aucun bug n'a été trouvé dans leur implémentation, ni dans les autres. Cependant, un bug a été trouvé dans Binaryen, qui a été signalé et corrigé.

#### Cela supprime-t-il le besoin de tester ?

Non, car l'automatisation peut ne pas respecter la spécification à 100 %. C'est le cas avec la reproduction limitée du comprtement des *host functions*.