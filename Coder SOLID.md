# Coder SOLID

> En tant que développeur, il est de notre devoir de créer des **logiciels qui durent** - càd des logiciels prêts à évoluer. (Et oui, le métier bouge sans cesse, le logiciel doit bien suivre !) C'est simple dit comme ça, mais c'est bien moins trivial en pratique. Alors des gens très bien ont défini des principes sous le sigle "SOLID", qui corrigent, en partie, certaines de nos erreurs courantes en POO.

## S for "Single responsibility principle"
**TLDR : une seule responsabilité par classe.**

    class BeerDrinker {
      void DrinkBeer() { ... }
      void EatCrisp() { ... } // Trop de responsabilité !
    }

Notre classe fait plusieurs choses ? On la splitte.

    class BeerDrinker {
      void DrinkBeer() { ... }
    }
    // Mieux (chacun chez soi et les hippopotames seront bien gardés)
    class CrispEater {
      void EatCrisp() { ... }
    }

Oui, ça fait plus de fichiers. Non, ça ne complexifie pas le code, à condition de bien l'organiser.

Le plus dur est de trouver la bonne granularité de responsabilité : il faut **garder ensemble ce qui change ensemble** ! Tout est dans l'équilibre entre l'aggrégation et la division... *(Comme notre jolie France trop centralisée ?)*

    // Pourquoi pas si la bière et les chips vont toujours ensemble !
    class HappyHour {
      void DrinkBeer() { ... }
      void EatCrisp() { ... }
    }

## O for "Open/closed principle"
**TLDR : ouvert à l'extension, fermé à la modification.** L'idée, c'est de ne pas casser le code client de la *madre* à chaque ajout de comportement.

    class BeerDrinker {
      void DrinkBeer() { ... }      
      void AdmireStout() { ... } // Cas particulier inutile pour la majorité du code client !
    }

Comment ? En passant par une nouvelle fille !

    class BeerDrinker {
      void DrinkBeer() { ... }
    }
    class StoutDrinker : BeerDrinker {
      void AdmireStout() { ... }
    }

## L for "Liskov substition principle"
**TLDR : une fille peut toujours remplacer sa mère.** Rien ne doit casser quand on utilise la fille, donc soit on réimplémente le comportement, soit on utilise celui de la mère par défaut.

    class BeerDrinker {
      void DrinkBeer() { ... }
    }
    class BeerAdmirer : BeerDrinker {
      void DrinkBeer() { } // Outch, le code qui ne fait rien => comportement perdu !
      void AdmireColor() { ... }
    }

Quand ça arrive, il y a des chances que la mère en fasse trop, ou que la fille soit illégitime !

    // Aucune raison d'étendre la mère si la fille ne boit pas de bière
    class BeerAdmirer {
      void AdmireColor() { ... }
    }

## I for "Interface segregation principle"
**TLDR : une interface ne doit jamais imposer d'implémentation inutile.** Sinon, les classes doivent remplir un contrat plus important que nécessaire. Pô content le collègue qui se farcit du code inutile, ou du `NotImplementedException` par flemme !

    interface IBeerDrinker {
      void DrinkBeer();
      void AdmireColor();
    }
    class BeerAbuser : IBeerDrinker {
      void DrinkBeer() { ... }
      void AdmireColor() { } // Outch, le code qui ne fait rien
    }

La solution ? Plusieurs interfaces atomiques plutôt qu'une moléculaire !

    interface IBeerDrinker {
      void DrinkBeer();
    }
    interface IBeerFan {
      void AdmireColor();
    }
    class BeerAbuser : IBeerDrinker {
      void DrinkBeer() { ... }
    }
    class StoutDrinker : IBeerDrinker, IBeerFan {
      void DrinkBeer() { ... }
      void AdmireColor() { ... }
    }

## D for "Dependency inversion"
**TLDR : une dépendance abstraite est *over 9000* fois mieux qu'une concrète.** (S'il y a des `new` partout, ça ne sent pas bon.) Pourquoi ? Une dépendance concrète peut casser le code client quand on la modifie !

    class Pub {
      private BeerServer _beerServer; // Dépendance explicite => mauvais découplage
      public Pub() {
        _beerServer = new BeerServer(); // Casse si le constructeur de BeerServer est modifié
      }
    }

Il est préférable de plutôt dépendre d'une interface, pour gagner en modularité. En plus, ça permet la mise en place de l'**injection de dépendances**, yay !

    class Pub {
      private IBeerServer _beerServer; // Dépendance abstraite => meilleur découplage
      public Pub(IBeerServer beerServer) {
        _beerServer = beerServer; // Injection dépendance selon système mis en place
      }
    }

## Un peu de recul
Ces principes ne sont pas des recettes magiques ! À nous développeurs de les considérer avec le **bon recul** dans le cadre de nos projets ayant chacun ses spécificités. Rester critique est primordial pour ne pas surcomplexifier notre code. **Keep It Simple Stupid.**

> Conclusion : merci d'avoir lu ce survol des principes SOLID, en espérant qu'il aura été intéressant. Happy coding et bonne route sur le long chemin de la sagesse !