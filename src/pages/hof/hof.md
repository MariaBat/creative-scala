## Higher Order Μέθοδοι και Συναρτήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Γιατί είναι χρήσιμες οι συναρτήσεις;
Αν θέλουμε να "πακετάρουμε" και να ονομάσουμε ένα επαναχρησιμοποιήσιμο κομμάτι κώδικα, μπορούμε να χρησιμοποιήσουμε μεθόδους.
Τι πλεονεκτήματα έχει η χρήση κώδικα ως τιμή;
Έχουμε πει ότι:

 - μπορούμε να περάσουμε συναρτήσεις ως παραμέτρους σε άλλες συναρτήσεις ή μεθόδους,
 - μπορούμε να δημιουργήσουμε μεθόδους οι οποίες επιστρέφουν συναρτήσεις.

Ας πάρουμε ως παράδειγμα την άσκηση με τους ομόκεντρους κύκλους:


```tut:silent:book
def concentricCircles(count: Int, size: Int): Image =
  count match {
    case 0 => Image.empty
    case n => Image.circle(size) on concentricCircles(n-1, size + 5)
  }
```

Ο παραπάνω κώδικας μας επιτρέπει να δημιουργήσουμε διαφορετικές εικόνες
αλλάζοντας την χρήση της `Image.circle`.
Όμως, κάθε φορά που αλλάζουμε τον ορισμό της `Image.circle`,
πρέπει να αλλάξουμε και τον ορισμό της `concentricCircles` αντιστοίχως.

Μπορούμε να κάνουμε την `concentricCircles` γενικότερη περνώντας την
`Image.circle` ως παράμετρο:
Εδώ μετονομάσαμε την `concentricShapes`, αφού δεν περιοριζόμαστε πλέον στο να φτιάχνουμε μόνο κύκλους,
και ορίσαμε την `singleShape` ως υπεύθυνη για τον σχεδιασμό ενός σχήματος κατάλληλου μεγέθους.

```tut:silent:book
def concentricShapes(count: Int, singleShape: Int => Image): Image =
  count match {
    case 0 => Image.empty
    case n => singleShape(n) on concentricShapes(n-1, singleShape)
  }
```

Τώρα, μπορούμε να επαναχρησιμοποιήσουμε τον ορισμό της `concentricShapes`
για να φτιάξουμε απλούς κύκλους, κύκλους διαφορετικής απόχρωσης,
κύκλους με διαφορετική διαφάνεια και ούτω καθεξής.
Το μόνο που πρέπει να κάνουμε είναι να δώσουμε τον κατάλληλο ορισμό στην `singleShape`:

```tut:silent:book
// Απευθείας πέρασμα κυριολεκτικού συνάρτησης:
val blackCircles: Image =
  concentricShapes(10, (n: Int) => Image.circle(50 + 5*n))

// Μετατροπή μεθόδου σε συνάρτηση:
def redCircle(n: Int): Image =
  Image.circle(50 + 5*n) lineColor Color.red

val redCircles: Image =
  concentricShapes(10, redCircle _)
```

### Ασκήσεις {-}

#### Χρώμα και Σχήμα {-}

Ξεκινώντας με τον παρακάτω κώδικα, γράψτε συναρτήσεις για το χρώμα και το σχήμα
ώστε το αποτέλεσμα να είναι η εικόνα [@fig:hof:colors-and-shapes.png].

![Χρώματα και Σχήματα](src/pages/hof/colors-and-shapes.pdf+svg){#fig:hof:colors-and-shapes.png}

```tut:silent:book
  def concentricShapes(count: Int, singleShape: Int => Image): Image =
    count match {
      case 0 => Image.empty
      case n => singleShape(n) on concentricShapes(n-1, singleShape)
    }
```

Η μέθοδος `concentricShapes` είναι ισοδύναμη με την μέθοδο
`concentricCircles` που είδαμε σε προηγούμενες ασκήσεις.
Η διαφορά τους είναι ότι περνάμε τον ορισμό της
`singleShape` ως παράμετρο.

Ας σκεφτούμε λίγο το πρόβλημα που μας δίνεται.
Πρέπει να κάνουμε δύο πράγματα:

 1. να γράψουμε κατάλληλο ορισμό για την `singleShape` για κάθε ένα
    από τα τρία σχήματα της εικόνας που έχουμε ως στόχο, και

 2. να καλέσουμε την `concentricShapes` τρεις φορές,
    περνώντας κάθε φορά τον αντίστοιχο ορισμό για την `singleShape`
    και να διατάξουμε τα αποτελέσματα με την `beside` (το ένα δίπλα στο άλλο).

Ας δούμε τον ορισμό της `singleShape` λεπτομερώς.
Ο τύπος της παραμέτρου είναι `Int => Image`,
που σημαίνει ότι είναι μία συνάρτηση η οποία δέχεται μία παράμετρο `Int` και επιστρέφει ένα `Image`.
Μπορούμε να δηλώσουμε μία μέθοδο τέτοιου τύπου όπως παρακάτω:

```tut:silent:book
def outlinedCircle(n: Int) =
  Image.circle(n * 10)
```

Μπορούμε να περάσουμε μία παράμετρο στην μέθοδο `concentricShapes` ώστε να δημιουργήσουμε
μία εικόνα ομόκεντρων κύκλων με μαύρο περίγραμμα:

```tut:silent:book
concentricShapes(10, outlinedCircle _)
```

Έτσι παράγεται το αποτέλεσμα της εικόνας [@fig:hof:colors-and-shapes-step1].

![Σχεδιασμένοι κύκλοι](src/pages/hof/colors-and-shapes-step1.pdf+svg){#fig:hof:colors-and-shapes-step1}

Η επίλυση της άσκησης είναι θέμα αντιγραφής, μετονομασίας
και μορφοποίησης της συνάρτησης ώστε να παράγονται
οι επιθυμητοί συνδυασμοί χρωμάτων και σχημάτων:

```tut:silent:book
def circleOrSquare(n: Int) =
  if(n % 2 == 0) Image.rectangle(n*20, n*20) else Image.circle(n*10)

(concentricShapes(10, outlinedCircle) beside concentricShapes(10, circleOrSquare))
```

Δείτε το αποτέλεσμα στην εικόνα [@fig:hof:colors-and-shapes-step2].

![Σχεδιασμένοι κύκλοι δίπλα σε κύκλους και τετράγωνα](src/pages/hof/colors-and-shapes-step2.pdf+svg){#fig:hof:colors-and-shapes-step2}

Για περισσότερη εξάσκηση, αφού γράψετε τον κώδικα για τα
βασικά σχήματα που ζητούνται παραπάνω, αλλάξτε τον έτσι ώστε να έχετε δύο ομάδες
βασικών συναρτήσεων---μία που παράγει χρώματα και μία που παράγει σχήματα.
Συνδέστε τις συναρτήσεις χρησιμοποιώντας έναν *combinator* όπως μπορείτε να δείτε παρακάτω και χρησιμοποιήστε το αποτέλεσμα ως παράμετρο για την `concentricShapes`

```tut:silent:book
def colored(shape: Int => Image, color: Int => Color): Int => Image =
  (n: Int) => ???
```

<div class="solution">
Η πιο απλή λύση είναι να ορίσουμε τρεις `singleShapes` όπως παρακάτω:

```tut:silent:book
def concentricShapes(count: Int, singleShape: Int => Image): Image =
  count match {
    case 0 => Image.empty
    case n => singleShape(n) on concentricShapes(n-1, singleShape)
  }

def rainbowCircle(n: Int) = {
  val color = Color.blue desaturate 0.5.normalized spin (n * 30).degrees
  val shape = Image.circle(50 + n*12)
  shape lineWidth 10 lineColor color
}

def fadingTriangle(n: Int) = {
  val color = Color.blue fadeOut (1 - n / 20.0).normalized
  val shape = Image.triangle(100 + n*24, 100 + n*24)
  shape lineWidth 10 lineColor color
}

def rainbowSquare(n: Int) = {
  val color = Color.blue desaturate 0.5.normalized spin (n * 30).degrees
  val shape = Image.rectangle(100 + n*24, 100 + n*24)
  shape lineWidth 10 lineColor color
}

val answer =
  (concentricShapes(10, rainbowCircle) beside
   concentricShapes(10, fadingTriangle) beside
   concentricShapes(10, rainbowSquare))
```

Όμως, υπάρχουν κάποια περιττά στοιχεία εδώ:
συγκεκριμένα το `rainbowCircle` και το `rainbowTriangle`,
χρησιμοποιούν τον ίδιο ορισμό για το `color`.
Υπάρχουν επίσης επαναλαμβανόμενες κλήσεις του `lineWidth(10)` και του
`lineColor(color)` οι οποίες μπορούν να εξαλειφθούν.
Στην άσκηση για παραπάνω εξάσκηση, αυτά τα δύο γίνονται διαφορετικές συναρτήσεις
και συνδέονται με το `colored`:

```tut:book
def concentricShapes(count: Int, singleShape: Int => Image): Image =
  count match {
    case 0 => Image.empty
    case n => singleShape(n) on concentricShapes(n-1, singleShape)
  }

def colored(shape: Int => Image, color: Int => Color): Int => Image =
  (n: Int) =>
    shape(n) lineWidth 10 lineColor color(n)

def fading(n: Int): Color =
  Color.blue fadeOut (1 - n / 20.0).normalized

def spinning(n: Int): Color =
  Color.blue desaturate 0.5.normalized spin (n * 30).degrees

def size(n: Int): Double =
  50 + 12 * n

def circle(n: Int): Image =
  Circle(size(n))

def square(n: Int): Image =
  Image.rectangle(2*size(n), 2*size(n))

def triangle(n: Int): Image =
  Image.triangle(2*size(n), 2*size(n))

val answer =
  (concentricShapes(10, colored(circle, spinning)) beside
   concentricShapes(10, colored(triangle, fading)) beside
   concentricShapes(10, colored(square, spinning)))
```
</div>
