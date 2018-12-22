## Θεέ μου, Πόσα Αστέρια!

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Ας χρησιμοποιήσουμε τα νέα εργαλεία που μάθαμε για να ζωγραφίσουμε μερικά αστέρια.
Για τους σκοπούς αυτής της άσκησης, ας υποθέσουμε ότι ένα αστέρι είναι ένα πολύγωνο με `p` κορυφές.
Όμως, αντί να ενώσουμε την κάθε κορυφή με τις γειτονικές της,
θα τις ενώσουμε με την νιοστή κορυφή της περιφέρειας του κύκλου.

Για παράδειγμα, η εικόνα [@fig:sequences:stars] δείχνει αστέρια με `p=11` και `n=1 to 5`.
Το `n=1` παράγει ένα συνηθισμένο πολύγωνο.
Όταν το `n` παίρνει τιμές από `2` και πάνω, παράγονται αστέρια με όλο και πιο μυτερές κορυφές:

![Αστέρια με `p=11` και `n=1 to 5`](./src/pages/sequences/stars.pdf+svg){#fig:sequences:stars}

Γράψτε κώδικα που δημιουργεί την παραπάνω εικόνα.
Ξεκινήστε γράφοντας μία μέθοδο η οποία παράγει ένα `star` (αστέρι) παίρνοντας ώς παράμετρο τα `p` και `n`:

```tut:silent:book
def star(p: Int, n: Int, radius: Double): Image =
  ???
```

*Βοήθεια:* χρησιμοποιήστε την τεχνική που είδαμε προηγουμένως για την μέθοδο `polygon`.

<div class="solution">
Παρακάτω μπορείτε να δείτε την μέθοδο `star`. Μετονομάσαμε τα `p` και `n` σε `points` (κορφές) και `skip` (αριθμός των κορυφών που θα παραλειφθούν) για καλύτερη κατανόηση:

```tut:silent:book
def star(sides: Int, skip: Int, radius: Double): Image = {
  import Point._
  import PathElement._

  val rotation = 360.degrees * skip / sides

  val start = moveTo(polar(radius, 0.degrees))
  val elements = (1 until sides).toList map { index =>
    val point = polar(radius, rotation * index)
    lineTo(point)
  }

  closedPath(start :: elements) lineWidth 2
}
```
</div>

Χρησιμοποιώντας δομημένη αναδρομή και την `beside` γράψτε μία μέθοδο με όνομα `allBeside` με την μορφή

```tut:book
def allBeside(images: List[Image]): Image =
  ???
```

<div class="solution">
Θα χρησιμοποιήσουμε την `allBeside` ώστε να δημιουργήσουμε μία σειρά από αστέρια.
Για την δημιουργία αυτής της εικόνας θα χρειαστεί να χρησιμοποιήσουμε μόνο τιμές της `skip`
από το `1` ως το `sides/2` (στρογγυλοποιημένα προς τα κάτω). Για παράδειγμα:

```tut:invisible
def allBeside(imgs: List[Image]): Image =
  imgs match {
    case Nil => Image.empty
    case hd :: tl => hd beside allBeside(tl)
  }
```

```tut:silent:book
allBeside(
  (1 to 5).toList map { skip =>
    star(11, skip, 100)
  }
)
```
</div>

Αφού τελειώσετε με την σειρά από αστέρια,
προσπαθήστε να φτιάξετε μία μεγαλύτερη εικόνα για διαφορετικές τιμές των `p` και `n`.
Μπορείτε να δείτε ένα παράδειγμα στην εικόνα [@fig:sequences:all-star]. *Βοήθεια:* Θα πρέπει να φτιάξετε μία μέθοδο `allAbove` παρόμοια με την `allBeside`.

![Αστέρια με `p=3 to 33 by 2` και `n=1 to p/2`](src/pages/sequences/all-star.pdf+svg){#fig:sequences:all-star}

<div class="solution">
Για την δημιουργία της εικόνας [@fig:sequences:stars2], ξεκινήσαμε φτιάχνοντας μία μέθοδο για την μορφή των αστεριών.

```tut:silent:book
def style(img: Image, hue: Angle): Image = {
  img.
    lineColor(Color.hsl(hue, 1.normalized, .25.normalized)).
    fillColor(Color.hsl(hue, 1.normalized, .75.normalized))
}
```

Μετά φτιάξαμε την μέθοδο `allAbove`, η οποία όπως θα δείτε μοιάζει πολύ με την `allBeside` (Δεν θα ήταν πολύ ωραίο να μπορούσαμε να τυποποιήσουμε αυτή τη μορφή;)

```tut:silent:book
def allAbove(imgs: List[Image]): Image =
  imgs match {
    case Nil => Image.empty
    case hd :: tl => hd above allAbove(tl)
  }
```

Η τελική εικόνα:

```tut:silent:book
allAbove((3 to 33 by 2).toList map { sides =>
  allBeside((1 to sides/2).toList map { skip =>
    style(star(sides, skip, 20), 360.degrees * skip / sides)
  })
})
```
</div>
