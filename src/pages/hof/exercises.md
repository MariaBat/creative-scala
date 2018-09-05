## Ασκήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Τώρα που έχουμε μάθει τόσα για τις συναρτήσεις, θα επιστρέψουμε στο πρόβλημα της δημιουργίας λουλουδιών.
Από δω και πέρα θα σας ζητάμε να σχεδιάζετε σχήματα περισσότερο από πριν.

Η εργασία σας θα είναι να σπάσετε την δημιουργία του λουλουδιού σε μικρότερες συναρτήσεις που δουλεύουν μαζί.
Αφήστε την δημιουργικότητά σας ελεύθερη καθώς μετατρέπετε το κάθε ξεχωριστό στοιχείο του κώδικα σε συνάρτηση.

Προσπαθήστε να το κάνετε τώρα. Αν αντιμετωπίσετε προβλήματα δείτε παρακάτω τον δικό μας τρόπο.

### Ξεχωριστά Στοιχεία

Αναγνωρίσαμε δύο στοιχεία στον σχεδιασμό λουλουδιών:

- την παραμετρική εξίσωση και
- την δομημένη αναδρομή σε γωνίες.

Τι άλλα στοιχεία θα μπορούσαμε να βρούμε στις συναρτήσεις; Ποιοι είναι οι τύποι τους;
(Αυτή η ερώτηση είναι τόσο γενική εσκεμμένα! Εξερευνήστε μόνοι σας!)

<div class="solution">
Όταν σχεδιάζουμε τις παραμετρικές καμπύλες το πιο πιθανό είναι ότι θα θελήσουμε να αλλάξουμε την ακτίνα των διαφορετικών καμπυλών.
Μπορούμε να το κάνουμε με μία συνάρτηση.
Ποιος πρέπει να είναι ο τύπος αυτή της συνάρτης;
Ίσως η πιο φανερή προσέγγιση είναι να θέσουμε ως τύπο της συνάρτησης τον `(Point, Double) => Point`, όπου η παράμετρος `Double` είναι η ποσότητα με την οποία μετράμε το σημείο.
Όμως η χρήση αυτού του τύπου είναι κάπως ενοχλητική. Πρέπει να περνάμε συνεχώς γύρω από την `Double`, η οποία δεν μπορεί να πάρει διαφορετική τιμή από την αρχική της.

Μία καλύτερη δομή είναι να φτιάξουμε μία συνάρτηση με τύπο `Double => (Point => Point)`.
Αυτή είναι μία συνάρτηση στην οποία περνάμε τον παράγοντα μέτρησης.
Επιστρέφεται μία συνάρτηση η οποία μετατρέπει ένα `Point` σύμφωνα με τον παράγοντα μέτρησης.
Μ' αυτόν τον τρόπο ξεπερνάμε το ότι η παράμετρος δεν μπορούσε να αλλάξει τιμή.
Ένας τρόπος λύσης είναι ο παρακάτω

```tut:silent:book
def scale(factor: Double): Point => Point = 
  (pt: Point) => {
    Point.polar(pt.r * factor, pt.angle)
  }
```

Προηγουμένως είπαμε ότι θα προτιμούσαμε να ξεχωρίσουμε την παραμετρική εξίσωση από την `sample`.
Αυτό μπορούμε εύκολα να το κάνουμε όπως εδώ

```tut:invisible
def parametricCircle(angle: Angle): Point =
  Point.cartesian(angle.cos, angle.sin)
```

```tut:silent:book
def sample(start: Angle, samples: Int, location: Angle => Point): Image = {
  // Το Angle.one είναι μία ολόκληρη περιστροφή, δηλαδή 360 μοίρες
  val step = Angle.one / samples
  val dot = triangle(10, 10)
  def loop(count: Int): Image = {
    val angle = step * count
    count match {
      case 0 => Image.empty
      case n =>
        dot.at(location(angle).toVec) on loop(n - 1)
    }
  }
  
  loop(samples)
}
```

Ίσως ακόμη να θέλουμε να απομονώσουμε την επιλογή της βασικής εικόνας (το `dot` ή το `Image.triangle` παραπάνω).
Για να το καταφέρουμε, μπορούμε να αλλάξουμε το `location` ώστε να γίνει μία ξεχωριστή συνάρτηση `Angle => Image`.

```tut:silent:book
def sample(start: Angle, samples: Int, location: Angle => Image): Image = {
  // Το Angle.one είναι μία ολόκληρη περιστροφή, δηλαδή 360 μοίρες
  val step = Angle.one / samples
  def loop(count: Int): Image = {
    val angle = step * count
    count match {
      case 0 => Image.empty
      case n => location(angle) on loop(n - 1)
    }
  }
  
  loop(samples)
}
```

Μπορούμε επίσης να απομονώσουμε όλο το κομμάτι της δομημένης αναδρομής.
Όπου είχαμε

```scala
def loop(count: Int): Image = {
  val angle = step * count
  count match {
    case 0 => Image.empty
    case n => location(angle) on loop(n - 1)
  }
}
```

μπορούμε να ξεχωρίσουμε την αρχική περίπτωση (`Image.empty`) και την αναδρομή (`location(angle) on loop(n - 1)`). Το προηγούμενο ήταν απλά μία `Image` αλλά το τελευταίο είναι μία συνάρτηση με τύπο `(Angle, Image) => Image`. Μπορείτε να δείτε το τελικό αποτέλεσμα παρακάτω

```tut:silent:book
def sample(start: Angle, samples: Int, empty: Image, combine: (Angle, Image) => Image): Image = {
  //Το Angle.one είναι μία ολόκληρη περιστροφή, δηλαδή 360 μοίρες
  val step = Angle.one / samples
  def loop(count: Int): Image = {
    val angle = step * count
    count match {
      case 0 => empty
      case n => combine(angle, loop(n - 1))
    }
  }
  
  loop(samples)
}
```

Η παραπάνω συνάρτηση έχει μία μεγάλη δόση αφαιρετικότητας. Περιμένουμε ότι οι περισσότεροι άνθρωποι δεν θα δουν αυτή την αφαίρεση αλλά αν ενδιαφέρεστε περισσότερο για εξερεύνηση αυτής της ιδέας μπορείτε να διαβάσετε για folds και monoids.
</div>


### Σύνθεση

Τώρα που απομονώσαμε τα στοιχεία, μπορούμε να τα συνδυάσουμε ώστε να δημιουργήσουμε ενδιαφέροντα αποτελέσματα. Προσπαθήστε το.

<div class="solution">
Μπορεί να καταλήξατε και εσείς με κάτι σαν το παρακάτω.

```tut:silent:book
def parametricCircle(angle: Angle): Point =
  Point.cartesian(angle.cos, angle.sin)
  
def rose(angle: Angle): Point =
  Point.cartesian((angle * 7).cos * angle.cos, (angle * 7).cos * angle.sin)

def scale(factor: Double): Point => Point = 
  (pt: Point) => {
    Point.polar(pt.r * factor, pt.angle)
  }

def sample(start: Angle, samples: Int, location: Angle => Point): Image = {
  // το Angle.one είναι μία ολόκληρη περιστροφή, δηλαδή 360 μοίρες
  val step = Angle.one / samples
  val dot = triangle(10, 10)
  def loop(count: Int): Image = {
    val angle = step * count
    count match {
      case 0 => Image.empty
      case n => dot.at(location(angle).toVec) on loop(n - 1)
    }
  }
  
  loop(samples)
}

def locate(scale: Point => Point, point: Angle => Point): Angle => Point =
  (angle: Angle) => scale(point(angle))

// Τριαντάφυλλο σε κύκλο
val flower = {
  sample(0.degrees, 200, locate(scale(200), rose _)) on
  sample(0.degrees, 40, locate(scale(150), parametricCircle _)) 
}
```
</div>


### Πείραμα

Τώρα πειραματιστείτε με την δημιουργικότητά σας!

<div class="solution">
Η λύση που χρησιμοποιήσαμε για την δημιουργία της εικόνας [@fig:hof:flower-power] βρίσκεται στο [Flowers.scala](https://github.com/underscoreio/doodle/blob/develop/shared/src/main/scala/doodle/examples/Flowers.scala). Τι φτιάξατε εσείς; Αν θέλετε να μας το δείξετε τα email μας είναι `noel@underscore.io` και `dave@underscore.io`.
</div>
