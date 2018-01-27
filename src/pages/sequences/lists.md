## Δουλεύοντας με Λίστες

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Σ'αυτό το σημείο ίσως σκέφτεστε ότι θα ήταν καλή ιδέα να δημιουργήσουμε μία μέθοδο που θα φτιάχνει πολύγωνα αντί να τα κατασκευάζουμε κάθε φορά από την αρχή.
Είναι φανερό ότι υπάρχουν επαναλαμβανόμενα κομμάτια στην κατασκευή τους και άρα θα μπορούσαμε να τα γενικεύσουμε αν γνωρίζαμε πως να φτιάχνουμε λίστες μη προκαθορισμένου μεγέθους.
Ήρθε η ώρα να μάθουμε περισσότερα για την δημιουργία και την διαχείριση λιστών.


### Η Αναδρομική Δομή των Λιστών

Ίσως θυμάστε ότι όταν μιλήσαμε για πρώτη φορά για την δομημένη αναδρομή σε φυσικούς αριθμούς, είπαμε ότι θα μπορούσαμε να μετατρέψουμε την αναδρομική τους δομή σε οποιαδήποτε άλλη αναδρομική δομή.
Το εφαρμόσαμε στους ομόκεντρους κύκλους καθώς και σε διάφορες άλλες περιπτώσεις.

Οι λίστες έχουν μία αναδρομική δομή η οποία είναι παρόμοια με αυτή της δομής των φυσικών αριθμών. Μία λίστα μπορεί να είναι

- άδεια `Nil`, ή
- ένα ζεύγος που αποτελείται από ένα στοιχείο `a` και μία `List`, και γράφεται ως `a :: tail`, όπου το `tail` είναι το υπόλοιπο της λίστας.

Για παράδειγμα, μπορούμε να γράψουμε την λίστα `List(1, 2, 3, 4)` στην "μακρύτερη" της μορφή ως

```tut:book
1 :: 2 :: 3 :: 4 :: Nil
```

Παρατηρήστε την ομοιότητα με τους φυσικούς αριθμούς.
Προηγουμένως είπαμε ότι μπορούμε να επεκτείνουμε την δομή ενός φυσικού αριθμού ώστε να γράψουμε, για παράδειγμα, τον αριθμό `3` ως `1 + 1 + 1 + 0`.
Αν αντικαταστήσουμε το `+` με `::` και το `0` με `Nil` τότε παίρνουμε `List` `1 :: 1 :: 1 :: Nil`.

Τι σημαίνει όμως αυτό;
Σημαίνει ότι μπορούμε πολύ εύκολα να μετατρέψουμε ένα φυσικό αριθμό σε `List` χρησιμοποιώντας το γνωστό εργαλείο της δομημένης αναδρομής[^free-monoid].
Παρακάτω μπορείτε να δείτε ένα πολύ απλό παράδειγμα, στο οποίο με δεδομένο έναν αριθμό, δημιουργείται μία λίστα αυτού του μεγέθους, που περιέχει το `String` "Hi".

```tut:book
def sayHi(length: Int): List[String] =
  length match {
    case 0 => Nil
    case n => "Hi" :: sayHi(n - 1)
  }

sayHi(5)
```

Ο παραπάνω κώδικας μετατρέπει:

 - το `0` σε `Nil`, για την βασική περίπτωση, και
 - το`n` (το οποίο θυμηθείτε ότι το θεωρούμε σαν `1 + m`) σε `"Hi" :: sayHi(n - 1)`, μετατρέποντας το `1` σε `"Hi"`, το `+` σε `::`, και ως συνήθως κάνοντας αναδρομή στο `m` (το οποίο είναι `n - 1`).

[^free-monoid]: Η σύνδεσή τους είναι πιο βαθιά. Μπορούμε να χρησιμοποιήσουμε την μέθοδο της αφαίρεσης σε ότι "προστίθεται" για να εφαρμόσουμε μία έννοια η οποία ονομάζεται monoid, όπου μία λίστα αντιπροσωπεύει έναν συγκεκριμένο τύπο monoid που καλείται free monoid. Δεν θα χρησιμοποιήσουμε αυτή τη μέθοδο στην Creative Scala αλλά μπορείτε να το ψάξετε μόνοι σας!

Ακόμη, αυτή η αναδρομική δομή μας επιτρέπει να μετατρέψουμε λίστες σε άλλες αναδρομικές δομές, όπως φυσικούς αριθμούς, άλλες λίστες, σκακιέρες κλπ.
Εδώ αυξάνουμε κάθε στοιχείο της λίστας---που σημαίνει ότι μετατρέπουμε την λίστα σε άλλη λίστα---χρησιμοποιώντας δομημένη αναδρομή.

```tut:book
def increment(list: List[Int]): List[Int] =
  list match {
    case Nil => Nil
    case hd :: tl => (hd + 1) :: tl
  }
  
increment(List(1, 2, 3))
```

Εδώ προσθέτουμε τα στοιχεία μία λίστας με ακεραίους---δηλαδή μετατρέπουμε την λίστα σε φυσικό αριθμό---χρησιμοποιώντας δομημένη αναδρομή.

```tut:book
def sum(list: List[Int]): Int =
  list match {
    case Nil => 0
    case hd :: tl => hd + sum(tl)
  }
  
sum(List(1, 2, 3)) 
```

Παρατηρήστε ότι όταν ξεχωρίζουμε τα στοιχεία της `List` για να εφαρμόσουμε τις περιπτώσεις της match, χρησιμοποιούμε το ίδιο συντακτικό `hd :: tl` που χρησιμοποιήσαμε και για να την κατασκευάσουμε.
Αυτή η συμμετρία είναι σκόπιμη.


### Μεταβλητές Τύπων

Τι γίνεται αν θέλουμε να βρούμε το μήκος μίας λίστας; Γνωρίζουμε ότι μπορούμε να χρησιμοποιήσουμε το συνηθισμένο μας εργαλείο, δηλαδή την δομημένη αναδρομή, ώστε να γράψουμε μία μέθοδο που θα κάνει ακριβώς αυτό. Παρακάτω μπορείτε να δείτε τον κώδικα για τον υπολογισμό του μήκους μίας λίστας `List[Int]`.

```tut:book
def length(list: List[Int]): Int =
  list match {
    case Nil => 0
    case hd :: tl => 1 + length(tl)
  }
```

Παρατηρήστε ότι δεν ασχολούμαστε με τα στοιχεία της λίστας---δεν μας ενδιαφέρει ο τύπος τους.
Χρησιμοποιώντας τον ίδιο σκελετό μπορούμε να υπολογίσουμε το ίδιο εύκολα το μήκος μίας λίστας `List[Int]` όπως και μίας λίστας `List[HairyYak]` αλλά μέχρι τώρα δεν ξέρουμε πως να ορίσουμε μία λίστα της οποίας δεν μας ενδιαφέρει ο τύπος των στοιχείων της.

Η Scala μας επιτρέπει να γράφουμε μεθόδους οι οποίες μπορούν να δουλέψουν με οποιονδήποτε τύπο χρησιμοποιώντας κάτι που ονομάζεται *μεταβλητή τύπου*.
Η μεταβλητή τύπου γράφεται μέσα σε αγκύλες, δηλαδή `[A]`, και βρίσκεται μετά το όνομα της μεθόδου και πριν την λίστα παραμέτρων.
Μία μεταβλητή τύπου, μπορεί να αντικαταστήσει οποιονδήποτε τύπο και μπορούμε να την χρησιμοποιήσουμε στην λίστα παραμέτρων ή στον τύπο που αποτελέσματος ώστε να αντικαταστήσει κάποιον τύπο που δεν γνωρίζουμε από την αρχή ποιος είναι.
Για παράδειγμα, παρακάτω μπορείτε να δείτε πως μπορούμε να γράψουμε την `length` ώστε να δουλεύει με λίστες όλων των τύπων.

```tut:book
def length[A](list: List[A]): Int =
  list match {
    case Nil => 0
    case hd :: tl => 1 + length(tl)
  }
```


<div class="callout callout-info">
#### Δομημένη Αναδρομή σε Λίστα {-}

Μία `List` στοιχείων τύπου `A` είναι:

- άδεια `Nil`, ή
- ένα στοιχείο `a` τύπου `A` και μία `tail` τύπου `List[A]`: `a :: tail`

Το σχήμα της δομημένης αναδρομής για την μετατροπή μίας `list` με τύπο `List[A]` σε έναν τύπο `B` είναι το παρακάτω

```
def doSomething[A,B](list: List[A]): B =
  list match {
    case Nil => ??? // Βασική περίπτωση για τον τύπο Β
    case hd :: tl => f(hd, doSomething(tl))
  }  
```

όπου το `f` είναι μία συγκεκριμένη μέθοδος για προβλήματα η οποία συνδυάζει το `hd` και το αποτέλεσμα της αναδρομικής κλήσης ώστε να παράξει κάτι με τύπο `B`.
</div>


### Ασκήσεις {-}

#### Κατασκευή Λιστών {-}

Κάνοντας τις παρακάτω ασκήσεις θα γίνουμε πιο έμπειροι στην κατασκευή λιστών με χρήση της δομημένης αναδρομής σε φυσικούς αριθμούς

Γράψτε μία μέθοδο με όνομα `ones` η οποία δέχεται έναν ακέραιο `n` και επιστρέφει μία λίστα `List[Int]` με μήκος `n` όπου όλα τα στοιχεία της είναι `1`. Για παράδειγμα

```tut:invisible
def ones(n: Int): List[Int] =
  n match {
    case 0 => Nil
    case n => 1 :: ones(n - 1)
  }
```

```tut:book
ones(3)
```

<div class="solution">
Είναι δομημένη αναδρομή σε φυσικούς αριθμούς!

```tut:book
def ones(n: Int): List[Int] =
  n match {
    case 0 => Nil
    case n => 1 :: ones(n - 1)
  }
  
ones(3)
```
</div>


Γράψτε μία μέθοδο με όνομα `descending` η οποία δέχεται έναν φυσικό αριθμό `n` και επιστρέφει μία `List[Int]` η οποία περιέχει τους φυσικούς αριθμούς από το `n` ως το `1` ή να επιστρέφει την άδεια λίστα αν το `n` είναι μηδέν. Για παράδειγμα

```tut:invisible
def descending(n: Int): List[Int] =
  n match {
    case 0 => Nil
    case n => n :: descending(n - 1)
  }
```

```tut:book
descending(0)
descending(3)
```

<div class="solution">
Για άλλη μία φορά, μπορούμε να χρησιμοποιήσουμε δομημένη αναδρομή σε φυσικούς αριθμούς.

```tut:book
def descending(n: Int): List[Int] =
  n match {
    case 0 => Nil
    case n => n :: descending(n - 1)
  }

descending(0)
descending(3)
```

</div>


Γράψτε μία μέθοδο με όνομα `ascending` η οποία δέχεται έναν φυσικό αριθμό `n` και επιστρέφει μία `List[Int]` η οποία περιέχει τους φυσικούς αριθμούς από το `1` ως το `n` ή να επιστρέφει την άδεια λίστα αν το `n` είναι μηδέν.

```tut:invisible
def ascending(n: Int): List[Int] = {
  def iter(n: Int, counter: Int): List[Int] =
    n match {
      case 0 => Nil
      case n => counter :: iter(n - 1, counter + 1) 
    }

  iter(n, 1)
}
```

```tut:book
ascending(0)
ascending(3)
```

<div class="solution">
Είναι δομημένη αναδρομή σε φυσικούς αριθμούς αλλά αυτή τη φορά έχει έναν εσωτερικό συσσωρευτή.

```tut:book
def ascending(n: Int): List[Int] = {
  def iter(n: Int, counter: Int): List[Int] =
    n match {
      case 0 => Nil
      case n => counter :: iter(n - 1, counter + 1) 
    }

  iter(n, 1)
}
  
ascending(0)
ascending(3)
```
</div>

Φτιάξτε μία μέθοδο με όνομα `fill` η οποία δέχεται έναν φυσικό αριθμό `n` και ένα στοιχείο `a` τύπου `A` και κατασκευάζει μία λίστα μήκους `n` όπου όλα της τα στοιχεία είναι `a`.

```tut:invisible
def fill[A](n: Int, a: A): List[A] =
  n match {
    case 0 => Nil
    case n => a :: fill(n-1, a)
  }
```

```tut:book
fill(3, "Hi")
fill(3, Color.blue)
```

<div class="solution">
Σ'αυτή την άσκηση σας ζητάμε να χρησιμοποιήσετε μία μεταβλητή τύπου. Κατά τ'άλλα είναι η ίδια μορφή με πριν.

```tut:book
def fill[A](n: Int, a: A): List[A] =
  n match {
    case 0 => Nil
    case n => a :: fill(n-1, a)
  }

fill(3, "Hi")
fill(3, Color.blue)
```
</div>

#### Μετατροπή Λιστών {-}

Σ'αυτή την άσκηση θα εξασκηθούμε στην άλλη πλευρά του χειρισμού λιστών---στην μετατροπή τους σε στοιχεία άλλων τύπων (και μερικές φορές και σε διαφορετικές λίστες).

Γράψτε μία μέθοδο με όνομα `double` η οποία δέχεται μία `List[Int]` και επιστρέφει μία λίστα όπου το κάθε της στοιχείο είναι διπλασιασμένο.

```tut:invisible
def double(list: List[Int]): List[Int] =
  list match {
    case Nil => Nil
    case hd :: tl => (hd * 2) :: double(tl)
  }
```

```tut:book
double(List(1, 2, 3))
double(List(4, 9, 16))
```

<div class="solution">
Αυτή είναι μία δομημένη αναδρομή σε λίστα όπου σε κάθε της βήμα κατασκευάζει μία λίστα. Η αποδόμηση της εισόδου αντικατοπτρίζεται στην κατασκευή της εξόδου.

```tut:book
def double(list: List[Int]): List[Int] =
  list match {
    case Nil => Nil
    case hd :: tl => (hd * 2) :: double(tl)
  }

double(List(1, 2, 3))
double(List(4, 9, 16))
```
</div>


Γράψτε μία μέθοδο με όνομα `product` η οποία δέχεται μία `List[Int]` και υπολογίζει το γινόμενο όλων των στοιχείων.

```tut:invisible
def product(list: List[Int]): Int =
  list match {
    case Nil => 1
    case hd :: tl => hd * product(tl)
  }
```

```tut:book
product(Nil)
product(List(1,2,3))
```

<div class="solution">
Είναι μία δομημένη αναδρομή σε λίστα η οποία χρησιμοποιεί την ίδια μορφή με την `sum` που είδαμε παραπάνω.

```tut:book
def product(list: List[Int]): Int =
  list match {
    case Nil => 1
    case hd :: tl => hd * product(tl)
  }

product(Nil)
product(List(1,2,3))

```
</div>

Γράψτε μία μέθοδο με όνομα `contains` η οποία δέχεται μία `List[A]` και ένα στοιχείο τύπου `A` και επιστρέφει true αν η λίστα περιέχει το στοιχείο ή σε αντίθετη περίπτωση false.

```tut:invisible
def contains[A](list: List[A], elt: A): Boolean =
  list match {
    case Nil => false
    case hd :: tl => (hd == elt) || contains(tl, elt)
  }
```

```tut:book
contains(List(1,2,3), 3)
contains(List("one", "two", "three"), "four")
```

<div class="solution">
Ίδια μορφή με πριν αλλά χρησιμοποιούμε μία μεταβλητή τύπου ώστε να επιτρέψουμε την πικοιλία των τύπων των στοίχειων.

```tut:book
def contains[A](list: List[A], elt: A): Boolean =
  list match {
    case Nil => false
    case hd :: tl => (hd == elt) || contains(tl, elt)
  }

contains(List(1,2,3), 3)
contains(List("one", "two", "three"), "four")
```
</div>

Γράψτε μία μέθοδο με όνομα `first` η οποία δέχεται μία `List[A]` και ένα στοιχείο τύπου `A` και επιστρέφει το πρώτο στοιχείο της λίστας, αν αυτή δεν είναι άδεια, ή αλλιώς το στοιχείο τύπου `A` που περάσαμε ως παράμετρο.

```tut:invisible
def first[A](list: List[A], elt: A): A =
  list match {
    case Nil => elt
    case hd :: tl => hd
  }
```

```tut:book
first(Nil, 4)
first(List(1,2,3), 4)
```

<div class="solution">
Αυτή η μέθοδος είναι παρόμοια με την `contains` αλλά χρησιμοποιήσαμε μία μεταβλητή τύπου για τον τύπο του αποτελέσματος καθώς και για τους τύπους των παραμέτρων.

```tut:book
def first[A](list: List[A], elt: A): A =
  list match {
    case Nil => elt
    case hd :: tl => hd
  }
  
first(Nil, 4)
first(List(1,2,3), 4)
```
</div>


#### Άσκηση Πρόκληση: Αντιστροφή {-}

Γράψτε μία μέθοδο με όνομα `reverse` η οποία δέχεται μία λίστα `List[A]` και την αντιστρέφει.

```tut:invisible
def reverse[A](list: List[A]): List[A] = {
  def iter(list: List[A], reversed: List[A]): List[A] =
    list match {
      case Nil => reversed
      case hd :: tl => iter(tl, hd :: reversed)
    }
    
  iter(list, Nil)
}
```

```tut:book
reverse(List(1, 2, 3))
reverse(List("a", "b", "c"))
```

<div class="solution">
Το κόλπο είναι να χρησιμοποιήσετε έναν συσσωρευτή ώστε να να κρατήσετε την μερικώς αντεστραμμένη λίστα.
Αν καταφέρατε να το βρείτε μόνοι σας, συγχαρητήρια!---έχετε καταλάβει την δομημένη αναδρομή πολύ καλά!

```tut:book
def reverse[A](list: List[A]): List[A] = {
  def iter(list: List[A], reversed: List[A]): List[A] =
    list match {
      case Nil => reversed
      case hd :: tl => iter(tl, hd :: reversed)
    }
    
  iter(list, Nil)
}

reverse(List(1, 2, 3))
reverse(List("a", "b", "c"))
```
</div>


#### Πολύγωνα! {-}

Τέλος, ας επιστρέψουμε στο παράδειγμα με τον σχεδιασμό πολυγώνων.
Γράψτε μία μέθοδο με όνομα `polygon` η οποία δέχεται τον αριθμό των πλευρών του πολυγώνου και την αρχική περιστροφή και παράγει μία `Image` με το αντίστοιχο πολύγωνο.
*Βοήθεια:* χρησιμοποιήστε έναν εσωτερικό συσσωρευτή.

Χρησιμοποιήστε αυτόν τον κώδικα για να δημιουργήσετε μία ενδιαφέρουσα εικόνα συνδυάζοντας πολύγωνα.
Μπορείτε να δείτε το δικό μας ευφάνταστο παράδειγμα στην εικόνα [@fig:sequences:concentric-polygons]. Είμαστε σίγουροι ότι εσείς μπορείτε και καλύτερα από αυτό.

![Ομόκεντρα πολύγωνα σε παστέλ αποχρώσεις.](./src/pages/sequences/concentric-polygons.pdf+svg){#fig:sequences:concentric-polygons}

<div class="solution">
Δείτε παρακάτω τον κώδικά μας!
Παρατηρήστε ότι οργανώσαμε τον κώδικά μας σε μικρότερα κομμάτια---και θα μπορούσαμε να τα κάνουμε και ακόμα πιο μικρά αν θέλαμε.
(Μπορείτε να καταλάβετε πως το καταφέραμε; Βοήθεια: Είναι απαραίτητο να περάσουμε, για παράδειγμα, το `start` σε κάθε κλήση της `makeColor` όταν δεν αλλάζει;)

```tut:silent:book
import Point._
import PathElement._

def polygon(sides: Int, size: Int, initialRotation: Angle): Image = {
  def iter(n: Int, rotation: Angle): List[PathElement] =
    n match {
      case 0 =>
        Nil
      case n =>
        LineTo(polar(size, rotation * n + initialRotation)) :: iter(n - 1, rotation)
    }
  closedPath(moveTo(polar(size, initialRotation)) :: iter(sides, 360.degrees / sides))
}

def style(img: Image): Image = {
  img.
    lineWidth(3.0).
    lineColor(Color.mediumVioletRed).
    fillColor(Color.paleVioletRed.fadeOut(0.5.normalized))
}

def makeShape(n: Int, increment: Int): Image =
    polygon(n+2, n * increment, 0.degrees)

def makeColor(n: Int, spin: Angle, start: Color): Color =
  start spin (spin * n)

val baseColor = Color.hsl(0.degrees, 0.7.normalized, 0.7.normalized)

def makeImage(n: Int): Image = {
  n match {
    case 0 =>
      Image.empty
    case n =>
      val shape = makeShape(n, 10)
      val color = makeColor(n, 30.degrees, baseColor)
      makeImage(n-1) on (shape fillColor color)
  }
}

val image = makeImage(15)
```
</div>
