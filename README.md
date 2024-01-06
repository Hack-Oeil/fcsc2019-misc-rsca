# FCSC 2019 RScA

À la suite d’une intervention, nos équipes ont récupéré un téléphone sécurisé utilisé par un agent appartenant à une organisation criminelle. Depuis sa récupération, nous avons pris soin de laisser le téléphone en permanence sous tension.

Habituellement, ce téléphone est notamment utilisé pour recevoir des instructions signées par un individu plus haut dans la chaîne hiérarchique. Le téléphone est chargé de vérifier les signatures de ces messages. Une phase de rétroconception nous a permis d’identifier que l’algorithme utilisé pour cette opération est un RSA.

La particularité forte de notre contexte est l’absence totale d’information sur les paramètres publics utilisés par l’algorithme. De plus, la vérification est implémentée de manière sécurisée, de façon à empêcher la récupération de ces paramètres.

L’objectif final de notre mission est de parvenir à signer des messages à la place du supérieur hiérarchique, afin de tendre un piège aux membres de l’organisation. Pour cela, nous vous demandons de retrouver tous les paramètres utilisés.

La rétroconception nous a appris plusieurs points importants.

Premièrement, nous avons pu retrouver l’implémentation du RSA. Voici le pseudo-code que nous avons pu reconstruire, où on note phi(N) l’indicatrice d’Euler de N :

```
Function RSA(m, e, phi(N), N):
  r  <- random(0, 2**32)
  e' <- e + r * phi(N)
  accumulator = 1
  dummy = 1
  for i from len(e') - 1 to 0:
    accumulator ← (accumulator * accumulator) mod N
    tmp ← (accumulator * m) mod N
    if (i-th lsb of e') == 1:
      accumulator ← tmp
    else:
      dummy ← tmp
  return accumulator
```

Deuxièmement, il s’avère que les deux opérations de multiplications modulaires
```
accumulator ← (accumulator * accumulator) mod N
tmp ← (accumulator * m) mod N
```
sont effectuées en faisant appel à un accélérateur hardware.

Au démarrage du téléphone, le bloc responsable de la vérification de signature va chercher dans une carte SIM les paramètres e et N. Le paramètre N est fourni à l’accélérateur et stocké dans une SRAM non lisible. Tout redémarrage du téléphone impliquerait le verrouillage de la carte SIM, que nous ne pouvons pas débloquer.

Du côté des bonnes nouvelles, nous sommes parvenus lors de notre intervention à récupérer un morceau de la documentation de cet accélérateur, dont nous vous fournissons un mémo. De plus, nous sommes parvenus à sniffer le bus de communication entre cet accélérateur et le bloc responsable de la vérification de signature.

Depuis l’intervention, nous avons reçu deux messages différents de l’extérieur. La capture du bus lors de la vérification des signatures de chacun de ces messages vous est donnée. Le contenu des messages en lui-même est anecdotique.

Votre objectif est de retrouver les paramètres (N, p, q, e, d) du RSA, avec :

- N : le module "public" du RSA,
- p, q : les facteurs premiers de N (p*q = N, et p < q),
- e : l’exposant "public" stocké sur le téléphone (0 < e < phi(N)),
- d : l’exposant "privé" utilisé pour signer les messages (0 < d < phi(N)).

Le flag à retrouver est de la forme ECSC{N + p + q + e + d} avec la somme N + p + q + e + d écrite en hexadécimal.

Auteur : Alternative

Origine : [RScA](https://hackropole.fr/fr/challenges/misc/fcsc2019-misc-rsca/)


## Challenge

- [Protocole_de_communication.md](Protocole_de_communication.md)
- [sniff_0.txt](sniff_0.txt)
- [sniff_1.txt](sniff_1.txt)


-----------

## Installation manuel
Vous n'utilisez pas l'application **les CTFs de Cyrhades** ? C'est dommage !
Mais voici comment installer ce CTF manuellement :

> git clone https://github.com/Hack-Oeil/fcsc2019-misc-rsca.git

> cd fcsc2019-misc-rsca


-----------

## Sur le site officiel hackropole.fr
> https://hackropole.fr/fr/challenges/misc/fcsc2019-misc-rsca/