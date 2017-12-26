## Ασκήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
import doodle.random._
import cats.syntax.cartesian._
```

### Διασκορπισμένα Σχέδια

Σ'αυτή την άσκηση θα φτιάξουμε διασκορπισμένα σχέδια όπως αυτά στην εικόνα [@fig:generative:distributions].
Πειραματιστείτε με διάφορες κατανομές (προσπαθήστε να δημιουργήσετε τις δικές σας κατανομές αλλάζοντας αυτές που ήδη έχουμε φτιάξει στην `Random`).

Όταν δημιουργούμε ένα τέτοιο σχέδιο:

- πρέπει να φτιάξουμε τα σημεία που θα σχεδιάσουμε,
- πρέπει να τοποθετήσουμε τις εικόνες την μία πάνω στην άλλη στο ίδιο σύστημα συντεταγμένων ώστε να δημιουργήσουμε το σχέδιο και
- πρέπει να μετασχηματίσουμε ένα σημείο σε εικόνα που μπορούμε να εμφανίσουμε.

Θα αναλύσουμε το κάθε ένα από τα παραπάνω σημεία ξεχωριστά.

Ξεκινήστε γράφοντας μια μέθοδο με όνομα `makePoint` που δέχεται μια `Random[Double]` για τις συντεταγμένες x και y ενός σημείου και επιστρέφει ένα `Random[Point]`.
Θα πρέπει να έχει τον ακόλουθο σκελετό:

```tut:silent:book
def makePoint(x: Random[Double], y: Random[Double]): Random[Point] =
  ???
```

Χρησιμοποιήστε έναν βρόγχο for.

<div class="solution">
Είναι ένα καλό παράδειγμα σύνθεσης τυχαίων αριθμών.

```tut:book
def makePoint(x: Random[Double], y: Random[Double]): Random[Point] =
  for {
    theX <- x
    theY <- y
  } yield Point.cartesian(theX, theY)
```
</div>

Τώρα δημιουργείστε, ας πούμε, χίλια τυχαία σημεία χρησιμοποιώντας τις τεχνικές που μάθαμε στο προηγούμενο κεφάλαιο για τις λίστες και επιλέξτε και μια κατανομή που εσείς προτιμάτε.
Θα πρέπει να καταλήξετε με μια `List[Random[Point]]`.

<div class="solution">
Κάτι σαν το παρακάτω θα πρέπει να δουλέψει.

```tut:silent:book
val normal = Random.normal(50, 15)
val normal2D = makePoint(normal, normal)

val data = (1 to 1000).toList.map(_ => normal2D)
```
</div>

Ας μετατρέψουμε την `List[Random[Point]]` σε `List[Random[Image]]`.
Κάντε το σε δύο βήματα: πρώτα γράψτε μια μέθοδο για μετατροπή ενός σημείου σε εικόνα και μετά γράψτε κώδικα για την μετατροπή της `List[Random[Point]]` σε `List[Random[Image]]`.

<div class="solution">
Μπορούμε να μετατρέψουμε ένα σημείο σε εικόνα χρησιμοποιώντας την μέθοδο `point` που μπορείτε να δείτε παρακάτω.
Παρατηρήστε ότι κάναμε το κάθε σημείο του σχεδίου σχεδόν διαφανές---έτσι γίνεται ευκολότερο να δει κανείς που μαζεύονται περισσότερα σημεία.

```tut:silent:book
def point(loc: Point): Image =
  circle(2).fillColor(Color.cadetBlue.alpha(0.3.normalized)).noLine.at(loc.toVec)
```

Η μετατροπή των λιστών είναι απλώς θέμα κλήσης της `map`.

```tut:silent:book
val points = data.map(r => r.map(point _))
```
</div>

Τώρα δημιουργήστε μια μέθοδο μετατροπής της `List[Random[Image]]` σε `Random[Image]` τοποθετώντας όλα τα σημεία το ένα πάνω στο άλλο χρησιμοποιώντας την μέθοδο `on`.
Είναι το ισοδύναμο της μεθόδου `allOn` που φτιάξαμε προηγουμένως αλλά τώρα λειτουργεί με δεδομένα μέσα στην `Random`.

<div class="solution">
Ίσως αναγνωρίσετε την παρακάτω μορφή.
Είναι σαν αυτή που χρησιμοποιήσαμε στη `allOn` αλλά προσθέσαμε και την `flatMap`, η οποία χρησιμοποιείται και στην `randomConcentricCircles` (καθώς και σε πολλά άλλα παραδείγματα).

```tut:silent:book
def allOn(points: List[Random[Image]]): Random[Image] =
  points match {
    case Nil => Random.always(Image.empty)
    case img :: imgs => 
      for {
        i  <- img
        is <- allOn(imgs)
      } yield (i on is)
  }
```
</div>

Τώρα βάλτε τα όλα μαζί για να δημιουργηθεί το τελικό σχέδιο με τα διασκορπισμένα σημεία.

<div class="solution">
Για να γίνει αυτό πρέπει απλώς να καλέσουμε μερικές μεθόδους και να χρησιμοποιήσουμε τιμές που έχουμε ήδη ορίσει.

```tut:silent:book
val plot = allOn(points)
```
</div>


### Παραμετρικός Θόρυβος

Σ'αυτή την άσκηση θα συνδυάσουμε παραμετρικές εξισώσεις, που είδαμε σε προηγούμενα κεφάλαια, με τυχαιότητα.
Ας ξεκινήσουμε φτιάχνοντας μια μέθοδο με όνομα `perturb` που προσθέτει τυχαίο θόρυβο σ'ένα σημείο.
Η μέθοδος αυτή θα πρέπει να έχει τον παρακάτω σκελετό

```tut:silent:book
def perturb(point: Point): Random[Point] =
  ???
```

Επιλέξτε όποια συνάρτηση θορύβου επιθυμείτε.

<div class="solution">
Παρακάτω μπορείτε να δείτε την λύση μας.
Έχουμε ήδη δει πολύ παρόμοιο κώδικα στην υλοποίηση των διασκορπισμένων σημείων.

```tut:silent:book
def perturb(point: Point): Random[Point] =
  for {
    x <- Random.normal(0, 10)
    y <- Random.normal(0, 10)
  } yield Point.cartesian(point.x + x, point.y + y) 
```
</div>

Τώρα φτιάξτε μια παραμετρική συνάρτηση, όπως κάναμε σε προηγούμενο κεφάλαιο.
Μπορείτε να χρησιμοποιήσετε την συνάρτηση που φτιάξαμε για τα τριαντάφυλλα (την είδαμε σε προηγούμενο κεφάλαιο) ή μπορείτε να φτιάξετε μια δική σας από την αρχή. Παρακάτω μπορείτε να δείτε τον ορισμό της rose.

```tut:silent:book
def rose(k: Int): Angle => Point =
  (angle: Angle) => {
    Point.cartesian((angle * k).cos * angle.cos, (angle * k).cos * angle.sin)
  }
```

Μπορούμε να συνδυάσουμε την παραμετρική μας συνάρτηση και την `perturb` ώστε να δημιουργήσουμε μια μέθοδο με τύπο `Angle => Random[Point]`.
Μπορείτε να την γράψετε πολύ εύκολα χρησιμοποιώντας την μέθοδο `andThen` ή μπορείτε να την γράψετε με πιο αναλυτικό τρόπο.
Παρακάτω μπορείτε να δείτε ένα παράδειγμα της `andThen` στο οποίο φαίνεται πως μπορεί να υπολογιστεί η τέταρτη δύναμη ενός αριθμού χρησιμοποιώντας το τετράγωνό του.

```tut:silent:book
val square = (x: Double) => x * x
val quartic = square andThen square
```

<div class="solution">
Με χρήση της `andThen` παίρνουμε έναν ωραίο και συμμαζεμένο κώδικα.

```tut:silent:book
def perturbedRose(k: Int): Angle => Random[Point] =
  rose(k) andThen perturb
```
</div>

Τώρα, χρησιμοποιώντας την `allOn` φτιάξτε μια εικόνα που να συνδυάζει τυχαιότητα και δομή.
Μπορείτε να είστε όσο δημιουργικοί θέλετε. Προσθέστε χρώμα, διαφάνειες και ότι άλλο μπορείτε να φανταστείτε.

<div class="solution">
Παρακάτω μπορείτε να δείτε τον κώδικα που χρησιμοποιήσαμε για να δημιουργήσουμε την εικόνα [#fig:generative:volcano].
Είναι λίγο μεγαλύτερος από τους κώδικες που έχουμε δει ως τώρα αλλά θα πρέπει να καταλαβαίνετε πως δουλεύουν όλα τα στοιχεία που τον αποτελούν.

```tut:silent:book
object ParametricNoise {
  def rose(k: Int): Angle => Point =
    (angle: Angle) => {
      Point.cartesian((angle * k).cos * angle.cos, (angle * k).cos * angle.sin)
    }

  def scale(factor: Double): Point => Point =
    (pt: Point) => {
      Point.polar(pt.r * factor, pt.angle)
    }

  def perturb(point: Point): Random[Point] =
    for {
      x <- Random.normal(0, 10)
      y <- Random.normal(0, 10)
    } yield Point.cartesian(point.x + x, point.y + y) 

  def smoke(r: Normalized): Random[Image] = {
    val alpha = Random.normal(0.5, 0.1) map (a => a.normalized)
    val hue = Random.double.map(h => (h * 0.1).turns)
    val saturation = Random.double.map(s => (s * 0.8).normalized)
    val lightness = Random.normal(0.4, 0.1) map (a => a.normalized)
    val color =
      for {
        h <- hue
        s <- saturation
        l <- lightness
        a <- alpha
      } yield Color.hsla(h, s, l, a)
    val c = Random.normal(5, 5) map (r => circle(r))
    
    for {
      circle <- c
      line   <- color
    } yield circle.lineColor(line).noFill
  }

  def point(
    position: Angle => Point,
    scale: Point => Point,
    perturb: Point => Random[Point],
    image: Normalized => Random[Image],
    rotation: Angle
  ): Angle => Random[Image] = {
    (angle: Angle) => {
      val pt = position(angle)
      val scaledPt = scale(pt)
      val perturbed = perturb(scaledPt)

      val r = pt.r.normalized
      val img = image(r)

      for {
        i  <- img
        pt <- perturbed
      } yield (i at pt.toVec.rotate(rotation))
    }
  }

  def iterate(step: Angle): (Angle => Random[Image]) => Random[Image] = {
    (point: Angle => Random[Image]) => {
      def iter(angle: Angle): Random[Image] = {
        if(angle > Angle.one)
          Random.always(Image.empty)
        else
          for {
            p  <- point(angle)
            ps <- iter(angle + step)
          } yield (p on ps)
      }

      iter(Angle.zero)
    }
  }

  val image: Random[Image] = {
    val pts =
      for(i <- 28 to 360 by 39) yield {
        iterate(1.degrees){
          point(
            rose(5),
            scale(i),
            perturb _,
            smoke _,
            i.degrees
          )
        }
      }
    val picture = pts.foldLeft(Random.always(Image.empty)){ (accum, img) =>
      for {
        a <- accum
        i <- img
      } yield (a on i)
    }
    val background = (rectangle(650, 650) fillColor Color.black)

    picture map { _ on background }
  }
}
```
</div>
