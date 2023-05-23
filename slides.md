# Cascade Persist/Remove (Doctrine)

--

## Was ist Cascade?

---

## Definition

> Ereignis breitet sich auf eine Reihe von Objekten aus
>
> Art & Weise Operationen auf einem Objekt auszuführen

---

## Persist
> "Persist" -> Speichert Objekt in die Datenbank
> 
> Hauptobjekt persist -> alle assoziierten Objekte persist

---

## Remove
> "Remove" -> Löscht Objekt aus der Datenbank
> 
> Hauptobjekt remove -> alle assoziierten Objekte remove

--

## Beispiel

* Postamt
* Schulgebäude
  * A103
  * A102
    * Tafel
    * ...
* Gerichtsgebäude

---
<!-- .slide:  data-transition="convex-in concave-out"-->
## Auswirkungen

> Cascade "remove" aktiviert
>
> Fall: Löschen des Raumes A102
> 
> Assoziierte Tafel,etc. werden ebenfalls gelöscht

--

## Vor- /Nachteile

---

## Vorteile

- Effizienz <!-- .element: class="fragment" data-fragment-index="1" -->
- Skalierbarkeit <!-- .element: class="fragment" data-fragment-index="2" -->
- Konsistenz <!-- .element: class="fragment" data-fragment-index="3" -->

---

## Nachteile

- Komplexität <!-- .element: class="fragment" data-fragment-index="1" -->
- Längere Entwicklungs- /Testzeiten <!-- .element: class="fragment" data-fragment-index="2" -->

--

## Cart.php 

```php [1-6|3-5]
 [ORM\OneToMany(mappedBy: 'cart', targetEntity: CartProduct::class, 
 cascade: ["persist", "remove"], orphanRemoval: true)] ➊
    private Collection $cartProducts;
```
- ➊ Übertragenes persist/remove Verhalten eines Carts auf ihre Products

---

<!-- .slide:  data-transition="convex-in concave-out"-->
## Erklärung Cart.php
Wird ein Cart persistiert<!-- .element: class="fragment" data-fragment-index="1" -->
- Alle zugehörigen CartProduct Entitäten ebenfalls <!-- .element: class="fragment" data-fragment-index="2" --> 
- Das Gleiche gilt beim Entfernen einer Cart Entität <!-- .element: class="fragment" data-fragment-index="3" -->
- orphanremoval: Entfernt Cartproducts ohne Zuordnung <!-- .element: class="fragment" data-fragment-index="4" -->
- Vorteil: Nicht alle CartProducts einzeln persistieren müssen <!-- .element: class="fragment" data-fragment-index="5" -->
---

## CartProduct.php

```php [1-6|3-5]
    #[ORM\ManyToOne(inversedBy: 'cartProducts')]
    #[ORM\JoinColumn(nullable: false, onDelete: 'CASCADE')] ➊
    private ?Product $product = null;

    #[ORM\ManyToOne(inversedBy: 'cartProducts')]
    #[ORM\JoinColumn(nullable: false, onDelete: 'CASCADE')] ➋
    private ?Cart $cart = null;
```
    
- ➊ Löschverhalten einer Fremdschlüsselbeziehung (Product)
- ➋ Löschverhalten einer Fremdschlüsselbeziehung (Cart)

<!-- .slide:  data-transition="convex-in concave-out"-->
---
## Erklärung CartProduct.php
- Löschen Products/Carts: Löschen der CartProducts
-Beispiel: Löschen der Maus 
  - Aus allen Carts mit einer Maus im Warenkorb wird diese gelöscht
- Nachteil: Unerwünschter Datenverlust kann entstehen
- Vorteile: Integrität der Datenbank gewährleistet

