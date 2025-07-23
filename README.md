# 🦀 TP_RUST

## Introduction

**Pourquoi Rust ?**  

Langage compilé, rapide, sûr en mémoire.  

Alternative moderne à C / C++ sans gestion manuelle de la mémoire (pas de `malloc`, pas de `free`).  

Garantie d'absence de segfaults grâce au *borrowing* et au *ownership*.

---

## Concepts de base

- `let nom = "Erwan";` → **immutable**
- `let mut age = 20;` → **mutable**
- `let` = déclaration
- `mut` = rend la variable modifiable

Types courants
i32, u32, f64, bool, String

Fonctions

```rust 
fn addition(a: i32, b: i32) -> i32 {
    a + b
}
```

fn définit une fonction.

Pas besoin de return si la dernière ligne est une expression.

Conditions & Boucles

```rust
if x > 0 { ... }
for i in 0..5 { ... }
while i < 10 { ... }
loop { break; }
for sur intervalle 1..=10 (inclusif), ou 1..10 (exclusif).
```

loop est une boucle infinie qu’on interrompt avec break.

Tableaux & Vecteurs

```rust
let tab = ["a", "b", "c"];     // fixe
let vec = vec![1, 2, 3];       // dynamique
Utiliser enumerate() pour les index.
```

Vecteur (Vec<T>) = tableau dynamique.

Struct et impl

Exemple

```rust
struct CompteBancaire {
    nom: String,
    solde: f64,
}
```

```rust
impl CompteBancaire {
    fn afficher(&self) { ... }
    fn deposer(&mut self, montant: f64) { ... }
    fn retirer(&mut self, montant: f64) { ... }
    fn fermer(&mut self) { ... }
}
```

struct = structure de données (comme une classe sans héritage).

impl = méthodes associées à la struct.

&self = référence en lecture.

&mut self = référence modifiable.

Traits (≃ interfaces)

```rust
trait Animal {
    fn chanter(&self);
}
```

```rust
struct Chien;
impl Animal for Chien {
    fn chanter(&self) {
        println!("wouaf!");
    }
}
```

Rust n’a pas d’héritage mais des trait : puissants et flexibles.

trait ≈ interface en Java/C#.

On utilise impl pour définir le comportement d’un trait.

Interaction utilisateur (console)

```rust
use std::io;
let mut input = String::new();
io::stdin().read_line(&mut input).expect("Erreur lecture");
let nombre: usize = input.trim().parse().unwrap();
```

Lire et convertir l'entrée utilisateur.

Projet : Gestion de comptes bancaires

Objectifs :
Créer, afficher, déposer, retirer, fermer, lister les comptes.

Utilise Vec<CompteBancaire>, loop, match, trait.

## Exercices

Exercice 1 – Struct simple et méthodes
Créer une struct Produit avec nom, prix, stock. Ajouter méthodes : afficher, vendre, restocker.

```rust
struct Produit {
    nom: String,
    prix: f64,
    stock: u32,
}

impl Produit {
    fn afficher(&self) {
        println!("Produit: {}, Prix: {}€, Stock: {}", self.nom, self.prix, self.stock);
    }

    fn vendre(&mut self, quantite: u32) {
        if self.stock >= quantite {
            self.stock -= quantite;
            println!("{} vendu(s)", quantite);
        } else {
            println!("Stock insuffisant");
        }
    }

    fn restocker(&mut self, quantite: u32) {
        self.stock += quantite;
        println!("{} ajouté(s) au stock", quantite);
    }
}

fn main() {
    let mut produit = Produit {
        nom: String::from("Clavier"),
        prix: 49.99,
        stock: 10,
    };

    produit.afficher();
    produit.vendre(3);
    produit.restocker(5);
    produit.afficher();
}
```

Exercice 2 – Mini Système de Banque Interactif
Faire une boucle avec menu : créer compte, afficher, déposer, retirer, fermer, quitter.

```rust
use std::io;

struct Compte {
    nom: String,
    solde: f64,
}

impl Compte {
    fn afficher(&self) {
        println!("Compte de {}: {}€", self.nom, self.solde);
    }

    fn deposer(&mut self, montant: f64) {
        self.solde += montant;
        println!("+{}€ déposé", montant);
    }

    fn retirer(&mut self, montant: f64) {
        if self.solde >= montant {
            self.solde -= montant;
            println!("-{}€ retiré", montant);
        } else {
            println!("Fonds insuffisants !");
        }
    }

    fn fermer(&mut self) {
        println!("Compte de {} fermé. Dernier solde : {}€", self.nom, self.solde);
        self.solde = 0.0;
    }
}

fn lire_input() -> String {
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Erreur lecture");
    input.trim().to_string()
}

fn main() {
    let mut comptes: Vec<Compte> = Vec::new();

    loop {
        println!("\n1. Créer un compte\n2. Afficher les comptes\n3. Dépôt\n4. Retrait\n5. Fermer un compte\n6. Quitter");
        let choix = lire_input();

        match choix.as_str() {
            "1" => {
                println!("Nom du titulaire : ");
                let nom = lire_input();
                comptes.push(Compte { nom, solde: 0.0 });
                println!("Compte créé !");
            }
            "2" => {
                for (i, compte) in comptes.iter().enumerate() {
                    println!("{} - ", i + 1);
                    compte.afficher();
                }
            }
            "3" => {
                println!("Numéro du compte :");
                let i: usize = lire_input().parse().unwrap_or(0) - 1;
                println!("Montant à déposer :");
                let m: f64 = lire_input().parse().unwrap_or(0.0);
                if let Some(compte) = comptes.get_mut(i) {
                    compte.deposer(m);
                }
            }
            "4" => {
                println!("Numéro du compte :");
                let i: usize = lire_input().parse().unwrap_or(0) - 1;
                println!("Montant à retirer :");
                let m: f64 = lire_input().parse().unwrap_or(0.0);
                if let Some(compte) = comptes.get_mut(i) {
                    compte.retirer(m);
                }
            }
            "5" => {
                println!("Numéro du compte à fermer :");
                let i: usize = lire_input().parse().unwrap_or(0) - 1;
                if let Some(compte) = comptes.get_mut(i) {
                    compte.fermer();
                }
            }
            "6" => {
                println!("Au revoir !");
                break;
            }
            _ => println!("Choix invalide."),
        }
    }
}
```

Exercice 3 : Reséau TCP avec gestion clients

Objectif du TP
Créer un serveur TCP en Rust qui :

- Accepte plusieurs connexions clients
- Garde une liste des clients connectés
- Utilise bien Ownership et Membership
- Utilise les bibliothèques :

```rust
use std::net::{TcpListener, TcpStream};
use std::io::{Read, Write};
```

Étapes de développement :

1 Définir une structure Client
Pour garder des infos de base sur les clients :

```rust
struct Client {
    id: usize,
    stream: TcpStream,
}
```

2 Créer un serveur TCP basique

```rust
use std::net::{TcpListener, TcpStream};
use std::io::{Read, Write};
use std::thread;
use std::sync::{Arc, Mutex};

struct Client {
    id: usize,
    stream: TcpStream,
}

fn handle_client(mut stream: TcpStream, id: usize) {
    let mut buffer = [0; 512];
    loop {
        match stream.read(&mut buffer) {
            Ok(0) => {
                println!("Client {} déconnecté", id);
                break;
            }
            Ok(n) => {
                println!("Client {}: {}", id, String::from_utf8_lossy(&buffer[..n]));
                stream.write_all(b"Message bien reçu\n").unwrap();
            }
            Err(e) => {
                println!("Erreur client {}: {}", id, e);
                break;
            }
        }
    }
}

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").expect("Erreur lors de la liaison");
    println!("Serveur TCP en écoute sur le port 7878");

    let mut id_counter = 0;

    for stream in listener.incoming() {
        match stream {
            Ok(stream) => {
                id_counter += 1;
                println!("Nouveau client connecté avec l'ID {}", id_counter);

                // On clone le stream pour le thread
                let thread_stream = stream.try_clone().expect("Échec du clonage du stream");

                thread::spawn(move || {
                    handle_client(thread_stream, id_counter);
                });
            }
            Err(e) => {
                println!("Erreur de connexion: {}", e);
            }
        }
    }
}
```
 
