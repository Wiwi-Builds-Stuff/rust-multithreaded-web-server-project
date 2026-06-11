# Implémentation d'un serveru web multithread en Rust
## Objectif du projet
L'objectif de ce projet est d'implémenter un serveur web sur le protocole HTTP en Rust avec le module `std::net` de la bibliothèque standard ainsi que les outils de programmation concurente du module `std::sync`.

## Utilisation
Pour utiliser ce serveur, vous devrez avoir une toolchain Rust sur votre machine, cloner le repositery et lancer le serveur avec `cargo run`. Le serveur exposera sur l'adresse `localhost:7878` à laquelle vous pourrez accéder aux URI suivants:
- `/` qui accèdera à une ressource `hello.html`
- `/sleep` qui accèdera à la même ressource mais bloquera le thread pendant 5 secondes. Cette latence a été volontairement introduite à des fins d'expérimentation avec la programmation concurrente.
- toute autre URI renverra un code erreur 404 et la ressource `404.html`

## Struture du projet 
Dans le dossier `src` se trouvent les fichiers suivants:
- `main.rs`: le point d'entrée du programme
- `lib.rs`: où se trouve l'implémentation du `ThreadPool`.

## Fonctionnement
Le protocole HTTP est bâtie sur le protocole TCP. TCP définit _comment_ le serveur et le client communiquent et HTTP défini le _contenu_ de leur échange. 

Dans le main.rs on lie un `TcpListener` à l'adresse `127.0.0.1:7878`. C'est à cette adresse que les clients devront envoyer leurs requêtes. Ensuite la fonction `handle_connection` renverra la réponse appropriée en fonction de la requête reçue. Dès qu'on introduit une latence (`/sleep`) on se rend vite compte que notre serveur ne peut gérer qu'une seule connexion à la fois. Les processeurs modernes sont capables de faire bien mieux et heureusement la bibliothèque standard de Rust fournit des outils pour gérer simplement la concurrence.

Une approche aurait été de créer un nouveau thread à chaque requête mais cela serait laisser la porte ouverte aux attaques DoS. À la place on crée un pool de threads c'est à dire un nombre limité de threads qui vont gérer tour à tour les requêtes. L'implémentation de ce pool se trouve dans le ficher `lib.rs` et peut être utilisée dans beaucoup d'autres contextes.

Ensuite on ferme gracieusement le serveur après un nombre de requêtes.

## À noter

Cette implémentation est rudimentaire et ne peut être utilisée en production. Il existe des dizaines d'implémentations complètes de serveurs web en Rust, prêts à l'emploi avec un plus haut niveau d'abstracion. Vous pouvez les retrouver sur [crates.io](https://crates.io/), le repository officiel de packages Rust. Ce projet est à but pédagogique et provient de [The Rust Programming Language](https://doc.rust-lang.org/book/ch21-00-final-project-a-web-server.html).