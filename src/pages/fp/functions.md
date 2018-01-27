## Οι Συναρτήσεις ως Τιμές

Ένα πολύ σημαντικό στοιχείο του συναρτησιακού προγραμματισμού είναι η ικανότητά του να ορίζει *συναρτήσεις που είναι τιμές πρώτης-τάξεως*.
Η Scala έχει μία ειδική σύνταξη για τις συναρτήσεις και τους τύπους τους.
Παρακάτω μπορείτε να δείτε μία συνάρτηση η οποία κάνει κάποιους υπολογισμούς

~~~ scala
(a: Double, b: Double) => math.sqrt(a*a + b*b)
// res0: (Double, Double) => Double = <function2>

res0(3, 4)
// res1: Double = 5.0
~~~

Αφού η Scala είναι αντικειμενοστραφής γλώσσα,
όλες οι τιμές πρώτης-τάξης είναι αντικείμενα.
Αυτό σημαίνει ότι οι συναρτήσεις είναι αντικείμενα και όχι μέθοδοι!
Οι συναρτήσεις έχουν από μόνες τους χρήσιμες μεθόδους για σύνθεση εντολών:

~~~ scala
(a: Int) => a + 10
// res0: Int => Int = <function1>

(a: Int) => a * 2
// res1: Int => Int = <function1>

res0 andThen res1 // this composes the two functions
// res2: Int => Int = <function1>

res2(5)
// res3: Int = 30
~~~

Μπορεί να φαίνεται περίεργο και κάπως περιοριστικό το ότι οι μέθοδοι δεν είναι τιμές.
Μπορούμε όμως να αποδείξουμε ότι όντως ισχύει προσπαθώντας να αναφερθούμε σε μία μέθοδο πριν την καλέσουμε:

~~~ scala
Color.rgb
// <console>:20: error: missing arguments for method rgb in object Color;
// follow this method with `_' if you want to treat it as a partially applied function
//               Color.rgb
//                     ^
~~~

Ευτυχώς, όπως μας υποδεικνύει και το παραπάνω μήνυμα λάθους,
μπορούμε να μετατρέψουμε μία μέθοδο σε συνάρτηση χρησιμοποιώντας τον τελεστή `_`
και καλώντας την με τις ίδιες παραμέτρους:

~~~ scala
Color.rgb _
// res4: (Int, Int, Int) => doodle.core.Color = <function3>

res4(255, 0, 0)
// res5: doodle.core.Color = ...
~~~

## Higher Order Μέθοδοι και Συναρτήσεις

Γιατί είναι χρήσιμες οι συναρτήσεις;
Αν θέλουμε να ομαδοποιήσουμε και να ονομάσουμε ένα επαναχρησιμοποιήσιμο κομμάτι κώδικα μπορούμε να χρησιμοποιήσουμε μεθόδους.
Ποια άλλα πλεονεκτήματα παίρνουμε από την χρήση κώδικα ως τιμή;

 - μπορούμε να περάσουμε συναρτήσεις ως παραμέτρους σε άλλες συναρτήσεις ή μεθόδους,
 - μπορούμε να δημιουργήσουμε μεθόδους οι οποίες ως αποτέλεσμα επιστρέφουν συναρτήσεις.

Ας πάρουμε ως παράδειγμα την άσκηση με τους ομόκεντρους κύκλους:

~~~ scala
def manyShapes(n: Int): Image =
  if(n == 1) {
    singleShape
  } else {
    singleShape on manyShapes(n - 1)
  }

def singleShape: Image = ???
~~~

Ο παραπάνω κώδικας μας επιτρέπει να δημιουργήσουμε πολλά διαφορετικά σχήματα απλώς
αλλάζοντας τον ορισμό της `singleShape`.
Όμως, κάθε φορά που αλλάζουμε τον ορισμό της `singleShape`,
πρέπει να αλλάξουμε και τον ορισμό της `manyShapes` αντιστοίχως.

Μπορούμε να κάνουμε την `manyShapes` γενικότερη περνώντας την
`singleShape` ως παράμετρο:

~~~ scala
def manyShapes(n: Int, singleShape: Int => Image): Image =
  if(n == 1) {
    singleShape(n)
  } else {
    singleShape(n) on manyShapes(n - 1, singleShape)
  }
~~~

Τώρα, μπορούμε να επαναχρησιμοποιήσουμε τον ορισμό της `manyShapes`
για να φτιάξουμε απλούς κύκλους, κύκλους διαφορετικής απόχρωσης,
κύκλους με διαφορετική αδιαφάνεια και ούτω καθεξής.
Το μόνο που πρέπει να κάνουμε είναι να δώσουμε τον κατάλληλο ορισμό στην `singleShape`:

~~~ scala
// Απευθείας πέρασμα κυριολεκτικής έκφρασης συνάρτησης:

val blackCircles: Image =
  manyShapes(10, (n: Int) => Circle(50 + 5*n))

// Μετατροπή μεθόδου σε συνάρτηση:

def redCircle(n: Int): Image =
  Circle(50 + 5*n) lineColor Color.red

val redCircles: Image =
  manyShapes(10, redCircle _)
~~~

<div class="callout callout-info">
*Σύντακτικό Συνάρτησης*

Παραπάνω είδαμε πολλά για την σύνταξη συναρτήσεων!
Σε περίπτωση που χαθείτε χουμε αφιερώσει ένα κομμάτι της
 [γρήγορης αναφοράς](#quick-reference) στο συντακτικό των συναρτήσεων.
</div>

**Άσκηση: Το Χρώμα και το Σχήμα**

Ξεκινώντας με τον παρακάτω κώδικα, γράψτε συναρτήσεις για το χρώμα και το σχήμα
ώστε το αποτέλεσμα να είναι η ακόλουθη εικόνα:

![Χρώματα και Σχήματα](src/pages/fp/colours-and-shapes.png)

~~~ scala
def manyShapes(n: Int, singleShape: Int => Image): Image =
  if(n == 1) {
    singleShape(n)
  } else {
    singleShape(n) on manyShapes(n - 1, singleShape)
  }
~~~

Η μέθοδος `manyShapes` είναι ισοδύναμη με την μέθοδο
`concentricCircles` που είδαμε σε προηγούμενες ασκήσεις.
Η κύρια διαφορά τους είναι ότι περνάμε τον ορισμό της
`singleShape` ως παράμετρο.

Ας σκεφτούμε λίγο το πρόβλημα που μας δίνεται.
Πρέπει να κάνουμε δύο πράγματα:

 1. να γράψουμε κατάλληλο ορισμό για την `singleShape` για κάθε ένα
    από τα τρία σχήματα της εικόνας που έχουμε ως στόχο, και

 2. να καλέσουμε την `manyShapes` τρεις φορές,
    περνώντας κάθε φορά τον αντίστοιχο ορισμό για την `singleShape`
    και να διατάξουμε τα αποτελέσματα με την `beside` (το ένα δίπλα στο άλλο).

Ας δούμε τον ορισμό της `singleShape` λεπτομερώς.
Ο τύπος της παραμέτρου είναι `Int => Image`,
που σημαίνει ότι είναι μία συνάρτηση η οποία δέχεται μία παράμετρο `Int` και επιστρέφει μία `Image`.
Μπορούμε να δηλώσουμε μία μέθοδο τέτοιου τύπου όπως παρακάτω:

~~~ scala
def outlinedCircle(n: Int) =
  Circle(n * 10)
~~~

Μπορούμε να περάσουμε μία παράμετρο στην μέθοδο `manyShapes` ώστε να δημιουργήσουμε
μία εικόνα ομόκεντρων κύκλων με μαύρο περίγραμμα:

~~~ scala
manyShapes(10, outlinedCircle).draw
~~~

![Πολλοί κύκλοι με περίγραμμα](src/pages/fp/colors-and-shapes-step1.png)

Η υπόλοιπη άσκηση είναι θέμα αντιγραφής, μετονομασίας
και μορφοποίησης της συνάρτησης ώστε να παράγονται
οι επιθυμητοί συνδυασμοί χρωμάτων και σχημάτων:

~~~ scala
def circleOrSquare(n: Int) =
  if(n % 2 == 0) Rectangle(n*20, n*20) else Circle(n*10)

(manyShapes(10, outlinedCircle) beside manyShapes(10, circleOrSquare)).draw
~~~

![Πολλοί κύκλοι με περίγραμμα δίπλα σε πολλούς κύκλους και τετράγωνα](src/pages/fp/colors-and-shapes-step2.png)

Για περισσότερη εξάσκηση, αφού γράψετε τον κώδικα για τα
βασικά σχήματα που ζητούνται παραπάνω, αλλάξτε τον έτσι ώστε να έχετε δύο ομάδες
βασικών συναρτήσεων---μία που παράγει χρώματα και μία που παράγει σχήματα.
Συνδέστε τις συναρτήσεις χρησιμοποιώντας έναν *combinator* όπως μπορείτε να δείτε παρακάτω,
και χρησιμοποιήστε το αποτέλεσμα ως παράμετρο για την `manyShapes`

~~~ scala
  def colored(shape: Int => Image, color: Int => Color): Int => Image =
    (n: Int) => ???
~~~

<div class="solution">
Η πιο απλή λύση είναι να ορίσουμε τρεις `singleShapes` όπως παρακάτω:

~~~ scala
def manyShapes(n: Int, singleShape: Int => Image): Image =
  if(n == 1) {
    singleShape(n)
  } else {
    singleShape(n) on manyShapes(n - 1, singleShape)
  }

def rainbowCircle(n: Int) = {
  val color = Color.blue desaturate 0.5.normalized spin (n * 30).degrees
  val shape = Circle(50 + n*12)
  shape lineWidth 10 lineColor color
}

def fadingTriangle(n: Int) = {
  val color = Color.blue fadeOut (1 - n / 20.0).normalized
  val shape = Triangle(100 + n*24, 100 + n*24)
  shape lineWidth 10 lineColor color
}

def rainbowSquare(n: Int) = {
  val color = Color.blue desaturate 0.5.normalized spin (n * 30).degrees
  val shape = Rectangle(100 + n*24, 100 + n*24)
  shape lineWidth 10 lineColor color
}

val answer =
  manyShapes(10, rainbowCircle) beside
  manyShapes(10, fadingTriangle) beside
  manyShapes(10, rainbowSquare)
~~~

Όμως, υπάρχουν κάποια περιττά στοιχεία εδώ:
συγκεκριμένα το `rainbowCircle` και το `rainbowTriangle`,
χρησιμοποιούν τον ίδιο ορισμό για το `color`.
Υπάρχουν επίσης επαναλαμβανόμενες κλήσεις του `lineWidth(10)` και του
`lineColor(color)` οι οποίες μπορούν να εξαλειφθούν.
Στην άσκηση για παραπάνω εξάσκηση, αυτά τα δύο γίνονται διαφορετικές συναρτήσεις
και συνδέονται με το `colored`:

~~~ scala
def manyShapes(n: Int, singleShape: Int => Image): Image =
  if(n == 1) {
    singleShape(n)
  } else {
    singleShape(n) on manyShapes(n - 1, singleShape)
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
  Rectangle(2*size(n), 2*size(n))

def triangle(n: Int): Image =
  Triangle(2*size(n), 2*size(n))

val answer =
  manyShapes(10, colored(circle, spinning)) beside
  manyShapes(10, colored(triangle, fading)) beside
  manyShapes(10, colored(square, spinning))
~~~
</div>
