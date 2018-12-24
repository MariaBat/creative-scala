## For Comprehension

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

<div class="callout callout-info">
Σε αυτήν την ενότητα, εκτός από τα imports που δίνονται στην αρχή του κεφαλαίου, χρειαζόμαστε και το παρακάτω:

```tut:silent
import doodle.random._
```
</div>

Στην Scala, υπάρχει μία ειδική σύνταξη που ονομάζεται *for comprehension*, η οποία μας επιτρέπει να γράψουμε απλούστερα τις εκφράσεις που περιέχουν μεθόδους όπως η `map` και η `flatMap`.

Για παράδειγμα, ο κώδικας της `randomConcentricCircles` καλεί την `map` και την `flatMap`.

```tut:silent:invisible
def randomAngle: Random[Angle] =
  Random.double.map(x => x.turns)

def randomColor(s: Normalized, l: Normalized): Random[Color] =
  randomAngle map (hue => Color.hsl(hue, s, l))

val randomPastel = randomColor(0.7.normalized, 0.7.normalized)

def randomCircle(r: Double, color: Random[Color]): Random[Image] =
  color map (fill => Image.circle(r) fillColor fill)
```

```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Random.always(Image.empty)
    case n => 
      randomCircle(size, randomPastel) flatMap { circle =>
        randomConcentricCircles(n-1, size + 5) map { circles =>
          circle on circles
        }
      }
  }
```

Αυτό μπορεί να αντικατασταθεί από μία for comprehension.

```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Random.always(Image.empty)
    case n => 
      for {
        circle  <- randomCircle(size, randomPastel) 
        circles <- randomConcentricCircles(n-1, size + 5)
      } yield circle on circles 
  }
```

Συνήθως, το for comprehension είναι πιο ευανάγνωστο από την `map` και την `flatMap`.

Γενικά, το for comprehension

```tut:book:invisible
val a: Seq[Int] = Seq.empty
val b: Seq[Int] = Seq.empty
val c: Seq[Int] = Seq.empty
val e: Int = 0
```

```tut:book:silent
for {
  x <- a
  y <- b
  z <- c
} yield e
```

μεταφράζεται σε:

```tut:book:silent
a.flatMap(x => b.flatMap(y => c.map(z => e)))
```

Δηλαδή κάθε `<-`, εκτός από το τελευταίο, μετατρέπεται σε `flatMap`, ενώ το τελευταίο `<-` μετατρέπεται σε `map`.

Ο μεταγλωττιστής μεταφράζει το for comprehensions σε `flatMap` και `map`.
Δεν συμβαίνει τίποτα μαγικό!
Έχουμε απλά έναν διαφορετικό τρόπο σύνταξης ο οποίος αποφεύγει βαθιές εμφωλευμένες εκφράσεις.

Σημειώστε ότι η σύνταξη του for comprehension είναι πιο ευέλικτη από όσα έχουμε παρουσιάσει.
Για παράδειγμα, αν παραλείψετε το `yield`, ο κώδικα θα μεταγλωττιστεί.
Δεν θα επιστρέψει όμως κάποιο αποτέλεσμα.
