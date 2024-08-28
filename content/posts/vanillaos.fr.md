+++
title = "[fr] Vanilla OS"
date = "2024-08-25"

[taxonomies]
tags = ["linux", "review"]

[extra]
repo_view = true
+++

---

# Le début

Dans un monde où il y a plus de distributions GNU/Linux que de personnes utilisant GNU/Linux quotidiennement, un petit groupe existant depuis maintenant quelques années se distingue des autres : les distros immuables.

## Les distros immuables

Premièrement le terme "immuable" n'est pas forcément le plus adapté. En les appelant comme ça, on a l'impression qu'on ne peut que regarder, sans jamais pouvoir sauvegarder ou bien modifier des fichiers, ce qui est, on ne va pas se mentir, peu pratique quand on souhaite faire des choses sur sa machine.

**Mais alors, qu'est ce qui fait une distro "immuable" ?**

Un système immuable comprend dans la plupart des cas ces différentes "technologies" :
- un système de fichiers racine en lecture seule
- des mises à jour atomiques/transactionnelles qui n'altèrent pas le système actuel et qui garantissent un ordinateur toujours en état de marche
- des mises à jour basés sur des images (souvent [OCI](https://opencontainers.org/))
- un système de retour en arrière en cas de modification/mise à jour qui casse le système
- une méthode de travail axée sur l'utilisation de containers
- l'utilisation de paquets universels, installés dans le répertoire utilisateur avec principalement [Flatpak](https://flatpak.org)

Vanilla OS coche toutes ces cases.

Dans les principales distributions immuables, vous avez :
- Fedora [Silverblue](https://fedoraproject.org/fr/atomic-desktops/silverblue/)/[Kinoite](https://fedoraproject.org/fr/atomic-desktops/kinoite/) et consorts
- Le projet [Universal Blue](https://universal-blue.org/) basé sur [Fedora Atomic Desktop](https://fedoraproject.org/atomic-desktops/)
- [OpenSUSE Aeon](https://aeondesktop.org)
- [NixOS](https://nixos.org)
- [VanillaOS](https://vanillaos.org)

Tous ces systèmes vous font la promesse d'une expérience sans encombre avec un terminal toujours opérationnel quelque soit la tâche (jeux, bureautique, travail etc), mais est-ce vraiment le cas ? TLDR : oui, *mais ça dépend*.

# La distro

## Les promesses

Dès notre arrivée sur le [site](https://vanillaos.org/) du projet, tout de suite les mots sont lourds :

> Vanilla OS is your next Operating System.

On voit que le système d'exploitation se veut la Rolls Royce des OSs en nous affirmant que "Vanilla OS est votre *prochain* système d'exploitation.". Ici tout le monde est ciblé : du geek au fond de sa chambre, à notre grand-père qui galère à ne serait-ce consulter ses mails.

La suite s'axe sur 6 axes :
- Le travail avec un environnement fiable et une interface intuitive
- Le jeu vidéo à travers les différents lanceurs dont Steam disponibles via Flatpak
- Le développement à l'aide de leur outil de gestion de containers
- L'accès à un large jeu d'applications disponibles sur [Flathub](https://flathub.org)
- La solidité grâce à un système immuable et toujours démarrable
- La versatilité avec des images système configurables par n'importe qui s'en donne les moyens ([ce que je fais](https://github.com/Babilinx/styx-vanilla-image))

Un système où il n'y a pas de maintenance et qui marche quand-même, ça fait rêver pas vrai ?

## La sécurité

Vanilla OS met un point d'honneur à proposer une solution qui permet à ses utilisateurs de garder un système sécurisé. Le chiffrement des données utilisateur est activé par défaut dans l'installateur, mais il y a aussi un outil, [FSGuard](https://github.com/linux-immutability-tools/FsGuard), qui vérifie à chaque démarrage l'intégrité du système de fichiers. Ainsi un attaquant n'a ni l'accès à vos données, ni la possibilité d'altérer le système de fichiers racine sans que vous vous en rendiez compte. De plus, le Secure Boot est supporté par défaut.

<figure>
<img src="/assets/posts/vanillaos/orchid_integrity_check.png" alt="orchid integrity check" />
<figcaption>Impossible de ne pas le voir au démarrage</figcaption>
</figure>

Le système "base" n'a même pas `sudo` d'installé, qui est connu pour avoir des vulnérabilités. Il est remplacé par une utilisation de polices PolKit qui permettent un contrôle plus fin sur les permissions admin. Ainsi il n'y a aucun moyen (hors faille de sécurité dans PolKit) que quoique ce soit ne soit modifié en tant que root sans que le mot/phrase de passe de l'administrateur ne soit demandé.

J'ai tenté d'utiliser la puce TPM2 de mon ordinateur pour permettre un démarrage sans avoir à entrer le mot de passe permettant de déchiffrer les données à l'aide de `systemd-cryptenroll`, mais pour l'instant cette tentative n'est pas un succès et personne n'a répondu à ma demande d'aide sur le forum du serveur Discord (on en reparle plus tard).

## L'installation

{{ note(header="Note", body="Ceci n'est en aucun cas un tutoriel d'installation. Bien se référer au [guide officiel](https://docs.vanillaos.org/handbook/en/installation) qui sera le plus à jour. Cependant il n'est pas impossible que j'en fasse un un jour !") }}

Le processus d'installation est plutôt classique, et peu déroutant pour un débutant selon moi. Il faut tout de même noter qu'il faut **obligatoirement** disposer d'un système disposant d'un UEFI, sinon il faudra passer votre chemin.

Lors de ma première installation sur mon PC portable, j'ai eu quelques soucis :

1. La liste des différents agencements clavier qui ne s'affichait pas => un simple redémarrage a suffit.

{{ note(header="Note", body="Notez que lors de l'installation sur mon PC fixe, avec une ISO un peu plus récente, ce problème n'était plus observable.") }}

2. L'utilisation du WiFi comme source d'accès internet était impossible. Non pas que la connexion ne fonctionnait pas, mais l'installateur lui plantait => passage en Ethernet obligatoire.

{{ note(header="Note (26-08-2024)", body="Ce bug est désormais fixé dans la dernière version de l'ISO.") }}

3. L'utilisation d'un téléphone en mode "Modem USB" pour l'accès internet ne semble pas implémenté dans l'installeur. Le système reconnait la connexion, mais l'installeur lui ne continue pas l'installation.

Lors du choix du chiffrement, je vous recommande de l'activer pour rester certain que vos données ne seront pas lisibles en cas de vol ou de vente du support de stockage si il a été mal effacé (**[vos fichiers ne sont pas réellement effacés !](https://fr.wikipedia.org/wiki/R%C3%A9manence_des_donn%C3%A9es)**). De plus, **[utilisez un mot de passe fort](https://www.cnil.fr/fr/les-conseils-de-la-cnil-pour-un-bon-mot-de-passe) !**. Si vous avez choisi d'utiliser le chiffrement, votre mot/phrase de passe vous sera demandé lors de chaque démarrage.

L'installateur ne configure ni les utilisateurs, ni les applications pré-installés. Tout ceci est fait après.

## Le premier démarrage

Lors du premier démarrage, vous êtes convié à créer un utilisateur administrateur ainsi qu'a choisir les applications à installer par défaut. Un conseil : ne prenez que le strict minimum et installez le reste ensuite si vous en avez le besoin. Notez qu'il est possible de cliquer sur la catégorie pour ne choisir que certaines apps. Vous êtes ensuite convié à redémarrer.

## Le premier redémarrage

Après avoir fini de démarrer, vous arriverez sur l'écran de connexion. Celui-ci demandera un mot de passe (encore une fois, **utilisez un mot/phrase de passe fort ! Le premier compte a les droits administrateurs !**). Se suit la première connexion lors de laquelle les applications choisies précédemment s'installent et le système se met à jour. Lorsque tout est terminé, n'hésitez pas à redémarrer.

## L'utilisation

L'utilisation quotidienne de Vanilla OS s'avère très fluide, sans accros ni besoin de passer par la case terminal dans une utilisation "normale" (c'est-à-dire sans vouloir bidouiller le système ou faire des tests). Le système semble être le Chrome OS du monde ouvert : simple, léger et accessible à tous. Je ne vois donc aucun problème à installer ce système d'exploitation sur l'ordinateur de papi-mamie ou même du reste de la famille.

### L'environnement de bureau

Vanilla OS est livrée seulement avec [GNOME](https://www.gnome.org/). On aime ou on n'aime pas. Personnellement, je trouve GNOME très épuré et très fonctionnel, sans chichi. Il est possible de personnaliser l'interface à l'aide d'extensions accessibles avec l'application *[Gestionnaire d'extensions](https://mattjakeman.com/apps/extension-manager)* disponible dans le magasin de logiciels.

{{ note(header="Note", body="Pour ceux qui souhaitent modifier leur système un peu plus en profondeur, je conseille l'installation de GNOME Tweaks [via ABRoot](https://docs.vanillaos.org/handbook/en/install-gnome-tweaks). Notez qu'il faudra passer par le terminal pour effectuer cette opération. Promis il ne faut le faire qu'une fois pour installer l'application.") }}

Voila à quoi cela ressemble chez moi :

<figure>
<img src="/assets/posts/vanillaos/gnome_overview.png" alt="GNOME overview" />
<figcaption>Avec Luminious Shell et GNOME 4X UI Improvements</figcaption>
</figure>

On utilise `<super>` (touche Windows) pour afficher le menu Activités où on peut directement taper au clavier pour chercher des apps, des paramètres ou autre (configurable dans les Paramètres -> Recherche), ou bien afficher le menu des applications en appuyant sur les 9 points disposés en carré.

<figure>
<img src="/assets/posts/vanillaos/gnome_app_menu.png" alt="GNOME app menu" />
<figcaption>Le menu des applications, organisable facilement</figcaption>
</figure>

L'interface est en elle-même très fluide et très agréable à utiliser. Cependant celle-ci peut rebuter les utilisateurs habitués de Windows de longue date.

### La maintenance

La maintenance est (presque) invisible. Les mises à jour se font automatiquement. Le seul moyen de voir qu'une est entrain d'être téléchargée et installée et de voir dans le coin droit en haut une icône de téléchargement.

<figure>
<img src="/assets/posts/vanillaos/vanilla_on_going_extension.png" alt="" />
<figcaption>Il est possible d'arrêter la mise à jour en cours</figcaption>
</figure>

Mis à part ça, le système tourne tout seul comme une horloge quoi qu'il se passe.

### Les applications graphiques

Vous disposez de l'ensemble du catalogue des applications disponibles sur [Flathub](https://flathub.org), et celles-ci sont toutes disponibles à l'installation à travers GNOME Logiciels.

<figure>
<img src="/assets/posts/vanillaos/gnome_software_search.png" alt="" />
<figcaption>La recherche d'applications est simple et rapide</figcaption>
</figure>

Il faut tout de même noter que GNOME Logiciels peut parfois faire des siennes avec des petits ralentissements lors de l'installation d'une application par exemple, mais cela reste peu fréquent et peu dérangent.

# Les outils

Vanilla OS est fourni avec une multitude d'outils plus intéressants les uns que les autres. Je vais essayer de tous les aborder.

## Vanilla update utility

Cet outil permet juste de définir la fréquence de vérification des mises à jour système (chaque jour/semaine/mois), mais aussi de les appliquer de manière "intelligente" pendant que le système n'est pas beaucoup utilisé pour ainsi éviter de pomper trop de débit internet par exemple.

Il est accessible via Paramètres -> Système -> Mises à jour logicielles.

<figure>
<img src="/assets/posts/vanillaos/vanilla_update_utility.png" alt="Vanilla OS update utility" />
<figcaption>Très simple, mais très fonctionnel</figcapture>
</figure>

## Vanilla prime utility

Cet outil permet de configurer le profil PRIME sur les PC portables disposants d'une carte graphique NVIDIA. Vous pouvez choisir d'utiliser le GPU dédié, le GPU intégré au processeur ou bien d'avoir un changement dynamique en fonction de la charge.

{{ note(header="Note", body="Ne disposant pas de PC portable avec une carte NVIDIA et les pilotes propriétaires, je n'ai pas testé cette fonctionnalité.") }}

<figure>
<img src="/assets/posts/vanillaos/vanilla_prime_utility.png" alt="Vanilla OS PRIME utility" />
<figcaption>Image tirée du Github du projet</figcaption>
</figure>

## Apx

Apk est le gestionnaire de paquets de Vanilla OS. Mais ce n'est pas un gestionnaire de paquets comme les autres : il est un enrobage d'autres gestionnaires de paquets gérés dans des containers. De plus, il est possible de créer des environnements séparés du système avec le support de la création et de la gestion de containers (appelés *subsystems*). Il est basé sur les outils de [Distrobox](https://github.com/89luca89/distrobox), un outil connu et reconnu pour permettre de faire tourner n'importe quelle distribution dans un container, tout an ayant une intégration avec le système hôte excellent. Vous pouvez donc installer des paquets provenant de plusieurs distros, tout en profitant d'une intégration dans le système principal.

À l'aide d'un client graphique, la création de *subsystem*, de *stack* ou même l'ajout de nouveaux gestionnaires de paquets est plutôt simple.

<figure>
<img src="/assets/posts/vanillaos/apx_gui.png" alt="Apx graphical interface" />
<figcaption>Interface de gestion d'un subsystem</figcaption>
</figure>

## Vanilla system operator (VSO)

Lorsque vous lancez le terminal la première fois (Black Box par défaut), vous vous rendrez compte qu'il ne s'agit pas du shell système, mais d'un container. C'est avec celui-ci que vous allez interagir avec la ligne de commande tout le long de votre expérience sur Vanilla OS. Vous pouvez l'utiliser comme si c'était le shell système et installer des applications dessus avec `apt`. Il est isolé du système et de chaque utilisateur. Ainsi si quelque chose est cassé dans ce container, vous pouvez le réinitialiser avec `reset-vso` et le tour est joué, bien sûr sans ne perdre aucune de vos données dans le répertoire utilisateur.

<figure>
<img src="/assets/posts/vanillaos/default_shell.png" alt="default shell" />
<figcaption>La ligne de commande n'est pas celle du système</figcaption>
</figure>

Mais les fonctionnalités de VSO ne s'arrêtent pas là ! En effet il est possible de créer des tâches qui se déroulent sous certaines conditions, d'exécuter des commandes de gestion système et encore plus.

Je tiens à vous dire que je n'ai jamais vraiment utilisé cet outil par moi-même, car je n'en n'a pas eu l'utilité vu que tout se fait tout seul, et aussi car je n'ai exporté aucune application. J'avais bien tenté de créer des automatisations sur mon ordinateur portable permettant de changer automatiquement de profil d'alimentation en fonction de certaines conditions, mais rien a faire ; ça n'a jamais marché. J'ai peut-être fait les choses mal, mais il n'existe **aucune** documentation à l'heure où j'écris ces lignes pour la v2 de VSO (on en reparle plus tard).

## Sideload utility

Cet utilitaire graphique utilise une fonctionnalité de VSO disponible en ligne de commande permettant de charger des applications en `.deb` *et* `.apk` (oui oui, des applications Android, on en reparle dans un autre post) pour les rendre disponibles dans le système sans à avoir à passer par la ligne de commande.

<figure>
<img src="/assets/posts/vanillaos/sideload_utility_install.webp" alt="" />
<figcaption>Exemple d'installation de Chromium en version Android</figcaption>
</figure>

## ABRoot

ABRoot est l'outil qui fait de Vanilla ce qu'elle est. Il permet de jongler entre deux partitions racines, la "A" et la "B", tout comme sur les [dernières versions d'Android](https://source.android.com/docs/core/ota/virtual_ab?hl=fr). Le principe est simple : tandis qu'une partition est la racine actuelle, l'autre contient l'ancienne version du système.

<figure>
<img src="/assets/posts/vanillaos/lvm_partitioning_structure.png" alt="" />
<figcaption>Le partitionnement du système</figcaption>
</figure>

Lors d'une mise à jour, la partition qui n'est pas l'actuelle se voit appliquer la mise à jour, et elle deviendra la nouvelle partition racine au prochain redémarrage. L'existence de l'ancienne partition permet d'annuler une modification si celle-ci casse quelque chose, mais aussi de permettre une mise à jour qui ne modifie pas le système actuel en marche. Pour avoir fait une erreur lors de mes essais avec les images customs, je peux dire que le système de rollback fonctionne très bien.

<figure>
<img src="/assets/posts/vanillaos/orchid-rollback.png" alt="" />
<figcaption>Interface de rollback lors du démarrage sur l'ancienne racine</figcaption>
</figure>

ABRoot permet aussi l'ajout et la suppression de paquets système (et non pas dans un container), tout en gardant l'intégrité du système intacte. Le système fonctionne très bien et fait ce que l'on lui dit de faire.

## Waydroid

Vanilla OS est fourni avec la possibilité d'activer [Waydroid](https://waydro.id/), qui est un moyen de faire fonctionner les application Android dans un environnement conteneurisé tout en les intégrant au système.

C'est une fonctionnalité que je n'ai pas eu l'occasion de tester. J'en reparlerai peut-être dans un autre billet un jour.

# La review

Le système de review se base sur une notation sur 50 points, répartie sur 9 points :

1. La simplicité de l'utilisation et de la maintenance **/11**
2. La sécurité du système et de ses données **/5**
3. L'installation **/3**
4. La documentation et communauté **/7**
5. L'adaptabilité **/5**
6. La fraîcheur des paquets **/2**
7. La logithèque **/3**
8. La distro et ce qu'elle représente (philosophie, pourquoi ? pour quelle raisons ? changements ? innovations ? évolutions ? différences ?) **/11**
9. Compatibilité **/3**

## La simplicité de l'utilisation et de la maintenance

Vanilla OS est un système très simple à maintenir car tout se fait de manière graphique ou automatique. L'installation ainsi que la gestion des applications installés se fait à l'aide de GNOME Logiciels. Pour l'installation d'applications non présentes dans le magasin, la présence de Sideload permet d'installer des paquets tiers de manière graphique très simplement. Cependant pour installer un paquet au niveau du système (et non dans un environnement conteneurisé comme les deux autres options) il faut obligatoirement passer par la ligne de commande. De plus, il est impossible de forcer une vérification de mise à jour du système de manière graphique.

Cela lui vaudra une note de 8/11.

## La sécurité du système et de ses données

{{ note(header="Note", body="La sécurité du système ainsi que celle des données est évaluée sans aucune modification des paramètres par défaut.") }}

Dès l'installation le chiffrement de la partition des données système et personnelles est proposée. Lors de chaque démarrage, le mot/phrase de passe est demandé dans une interface graphique ce qui rend la procédure moins austère. Le support du Secure Boot ainsi que de la vérification de l'intégrité de l'image système est très appréciée. Cependant on note l'absence de système de contrôle d'accès tel que [SELinux](https://fr.wikipedia.org/wiki/SELinux) ou [Apparmor](https://fr.wikipedia.org/wiki/AppArmor), bien que Debian [supporte SELinux](https://wiki.debian.org/SELinux).

<figure>
<img src="/assets/posts/vanillaos/security_review.png" alt="" />
<figcaption>Tout semble au vert dans le rapport de sécurité</figcaption>
</figure>

Malgré l'absence de [MAC](https://en.wikipedia.org/wiki/Mandatory_access_control), Vanilla OS fait mieux que la plupart des autres systèmes Linux par défaut. Je lui donne une note de 4/5.

## L'installation

L'installation se passe plutôt bien et le paramétrage des options est plutôt simple, même pour un novice. Cependant certaines parties ne sont pas traduites que ce soit lors de l'installation ou de la post installation. Une personne n'ayant jamais effectué d'installation de système d'exploitation devra se faire aider grâce à une personne ou bien un tutoriel vidéo, mais ce point est valable pour toute installation de système d'exploitation, quel qu'il soit.

Il est possible de faire mieux, mais c'est déjà ça. 2/3.

## La documentation et la communauté

C'est ici que ça se corse. La communauté est gentille, aidante et plutôt patiente sur le serveur Discord, mais il arrive trop souvent que des personnes n'aient aucune aide suite à leur post dans la section "forum". Pour ce qui est de la documentation.. il faut dire qu'ils ont besoin d'aide ! On voit que tous leur temps a été consacré à l'écriture et à l'amélioration de cette v2 du système d'exploitation, en dépit de la documentation qui est dans le meilleur des cas incomplète, ou inexistante dans la v2 des outils ou tout simplement inexistante tout court. C'est vraiment un point sur lequel il faut qu'ils s'améliorent, mais la communauté peut aussi aider. Il faut juste se lancer. De plus avec un système relativement éloigné du fonctionnement des autres distros les autres tutoriels du net n'aident pas beaucoup si on ne sait pas trop ce que l'on fait.

Je compte refaire une mise à jour de cette review dans quelques temps, mais pour l'instant cela vaut un petit 2/7.

## L'adaptabilité

Au premier abord il n'est pas si compliqué d'adapter la distribution de la manière que l'on souhaite. Pour l'environnement de bureau, il y a les extensions, pour l'ajout d'applications, c'est aussi simple. Là où ça devient complexe, c'est si on souhaite appliquer des modifications plus profondes dans le système. Dans ce cas, il va falloir passer par la génération d'images système custom. Le processus n'est pas en soit complexe, mais il faut y passer un peu de temps. Je pense que j'en ferai un tutoriel un jour ou l'autre. Les outils fonctionnent bien et ils sont faciles à déboguer si jamais on fait des erreurs.

Je donne la note de 4/5 pour cette partie.

## La fraîcheur des paquets

Vanilla OS est basée sur Debian Sid (experimental). Les repos de Vanilla sont mis à jour environ tous les deux jours pour permettre des tests avant la mise en production. On évite ainsi le retard d'une base Debian stable ou unstable, mais on reste toujours dans l'esprit stabilité de Debian. Le modèle de mise à jour est donc semi-rolling car il ne faut pas attendre une nouvelle version pour profiter des nouveaux paquets.

Les paquets sont donc testés **et** à jour, ce qui offre un très bon équilibre. 2/2.

## La logithèque

La distro étant une base Debian, on retrouve donc l'immense quantité de paquets disponibles ainsi qu'un support plutôt privilégié car le format de paquet deb est le plus utilisé dans le monde de Linux, garantissant ainsi encore plus de paquets venant de projets plus petits. On note aussi la possibilité avec Apx d'installer des paquets venant de n'importe quelle distribution telle que Alpine Linux, Arch, Fedora ou encore OpenSUSE. Il y a aussi le format Flatpak qui est le format privilégié pour les applications graphiques qui offre énormément de choix car il est le format multi-distro adopté pas tous.

Avec tous ces avantages, vous avez l'une si ce n'est *la* plus grande quantité de paquets disponibles. Je donne la note de 3/3.

## La distro et ce qu'elle représente

Cette distribution Linux, quand on la compare à la myriade déjà existante, apporte de la valeur ajouté pour l'utilisateur. Ce n'est pas un simple thème ou quelques petites modifications faites par-ci par-là sur une distro mère. Ici on trouve de vrais outils développés *par* l'équipe derrière le système et qui vont dans un sens commun : faire en sorte que Vanilla OS soit fiable, sécurisé et simple d'utilisation. Avec ABRoot par exemple, qui fonctionne avec un partitionnement qui n'a rien d'habituel, ou encore sur le fait de ne pas vraiment d'avoir de gestionnaire de paquets, mais *plusieurs*. Toutes ces modifications la rend atypique, mais dans le bon sens.

Je lui donne donc une note de 11/11, j'adore l'idée.

## La compatibilité

La compatibilité n'est vraiment pas grande ; seulement l'architecture X86_64 est supporté et les systèmes BIOS ne sont pas supportés. Cela dit, toutes les machines d'après 2010 (voir un peu avant, car [l'UEFI 2.0 est sorti en 2006](https://fr.wikipedia.org/wiki/UEFI)).

Avec autant de restrictions, je ne peux donner que la note de 1/3.

# La fin

Pour conclure, Vanilla OS se trouve être un très bon OS, qui restera sur mes machines encore quelques temps. Malgré le manque de raffinement sur certains points, on reste sur un système que Jean-tout-le-monde peut utiliser sans avoir à demander de l'aide tous les jours. On verra dans le futur si tout s'améliore, mais j'y crois beaucoup. Je risque d'ajouter des édits sur les points qui ont changés.

J'espère que l'organisation n'était pas trop brouillon, car c'est pour moi la première fois que j'écris autant. Je vais encore apporter des modifications sur des phrases mal formulés, ou encore des fautes d'orthographe et/ou de frappe. Merci d'avoir lu jusque ici. Le prochain billet devrait parler de la review très long terme de mon smartphone.

---

À la revoyure !
