## Εμφωλευμένες Μέθοδοι

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Μία μέθοδος είναι μία δήλωση.
Το σώμα μίας μεθόδου μπορεί να περιέχει δηλώσεις και εκφράσεις.
Άρα, η δήλωση μίας μεθόδου μπορεί να περιέχει δηλώσεις άλλων μεθόδων.

Για να καταλάβουμε γιατί κάτι τέτοιο είναι χρήσιμο ας δούμε πάλι μία μέθοδο που γράψαμε προηγουμένως:

```tut:book
def cross(count: Int): Image = {
  val unit = Image.circle(20)
  count match {
    case 0 => unit
    case n => unit beside (unit above cross(n-1) above unit) beside unit
  }
}
```

Έχουμε δηλώσει την `unit` μέσα στην μέθοδο `cross`.
Αυτό σημαίνει ότι η δήλωση της `unit` έχει εμβέλεια μόνο το σώμα της `cross`.
Ο περιορισμός της εμβέλειας των δηλώσεων μόνο στα σημεία στα οποία χρησιμοποιούνται είναι πολύ καλή τακτική ώστε να αποφευχθεί η κατά λάθος επισκίαση άλλων δηλώσεων. Όμως ας σκεφτούμε την συμπεριφορά της `cross` κατά την διάρκεια της εκτέλεσης και θα δούμε ότι έχει κάποια ανεπιθύμητα χαρακτηριστικά.

Θα χρησιμοποιήσουμε το μοντέλο αντικατάστασης για να επεκτείνουμε την `cross(1)`.

```scala
cross(1)
// Επεκτείνεται σε
{
  val unit = Image.circle(20)
  1 match {
    case 0 => unit
    case n => unit beside (unit above cross(n-1) above unit) beside unit
  }
}
// Επεκτείνεται σε
{
  val unit = Image.circle(20)
  unit beside (unit above cross(0) above unit) beside unit
}
// Επεκτείνεται σε
{
  val unit = Image.circle(20)
  unit beside (unit above 
  {
    val unit = Image.circle(20)
    0 match {
      case 0 => unit
      case n => unit beside (unit above cross(n-1) above unit) beside unit
    }
  }
  above unit) beside unit
}
// Επεκτείνεται σε
{
  val unit = Image.circle(20)
  unit beside (unit above 
  {
    val unit = Image.circle(20)
    unit
  }
  above unit) beside unit
}
```

Το νόημα αυτής της τεράστιας επέκτασης είναι να δείξουμε ότι δημιουργούμε την `unit` ξανά κάθε φορά που κάνουμε αναδρομή μέσα στην `cross`.
Μπορούμε να αποδείξουμε ότι συμβαίνει όντως έτσι εκτυπώνοντας κάτι κάθε φορά που δημιουργείται η `unit`.

```tut:book
def cross(count: Int): Image = {
  val unit = { 
    println("Creating unit")
    Image.circle(20)
  }
  count match {
    case 0 => unit
    case n => unit beside (unit above cross(n-1) above unit) beside unit
  }
}

cross(1)
```

Αυτό δεν έχει πολύ μεγάλη σημασία για την `unit` αφού είναι πολύ μικρή, όμως χρειάζεται πολύ μνήμη και χρόνο οπότε είναι καλό να το αποφεύγουμε όπου δεν είναι απαραίτητο.

Μπορούμε να το λύσουμε μετακινώντας την `unit` έξω από την `cross`.

```tut:book
val unit = { 
  println("Creating unit")
  Image.circle(20)
}

def cross(count: Int): Image = {
  count match {
    case 0 => unit
    case n => unit beside (unit above cross(n-1) above unit) beside unit
  }
}

cross(1)
```

Κάτι τέτοιο δεν είναι όμως επιθυμητό αφού τώρα η `unit` έχει μεγαλύτερη εμβέλεια από ότι χρειάζεται.
Μία καλύτερη λύση είναι να χρησιμοποιηθεί μία εμφωλευμένη ή εσωτερική μέθοδος.

```tut:book
def cross(count: Int): Image = {
  val unit = { 
    println("Creating unit")
    Image.circle(20)
  }
  def loop(count: Int): Image = {
    count match {
      case 0 => unit
      case n => unit beside (unit above loop(n-1) above unit) beside unit
    }
  }

  loop(count)
}

cross(1)
```

Έτσι επιτυγχάνουμε την συμπεριφορά που θέλουμε, δημιουργώντας την `unit` μόνο μία φορά και ελαχιστοποιώντας την εμβέλειά της.
Η εσωτερική μέθοδος `loop` χρησιμοποιεί δομημένη αναδρομή ακριβώς όπως είδαμε προηγουμένως.
Το μόνο που πρέπει να κάνουμε είναι να σιγουρευτούμε ότι την καλούμε στην `cross`.
Εμείς συνήθως ονομάζουμε αυτού του είδους τις μεθόδους `loop` (βρόγχος) ή `iter` (επανάληψη) (συντομία του iterate) ώστε να δείξουμε ότι εκτελείται ένας βρόγχος.

Αυτή η τεχνική είναι απλώς μία μικρή παραλλαγή των όσων έχουμε ήδη κάνει μέχρι τώρα αλλά ας κάνουμε μερικές ασκήσεις για να σιγουρευτούμε ότι μπορούμε να την χειριστούμε.


### Ασκήσεις {-}

#### Σκακιέρα {-}

Ξαναγράψτε την μέθοδο `chessboard` χρησιμοποιώντας μία εμφωλευμένη μέθοδο ώστε τα `blackSquare`, `redSquare`, και το `base` να δημιουργούνται μόνο μία φορά όταν καλείται η `chessboard`.

```tut:book
def chessboard(count: Int): Image = {
  val blackSquare = Image.rectangle(30, 30) fillColor Color.black
  val redSquare   = Image.rectangle(30, 30) fillColor Color.red
  
  val base =
    (redSquare   beside blackSquare) above (blackSquare beside redSquare)
  count match {
    case 0 => base
    case n =>
      val unit = cross(n-1)
      (unit beside unit) above (unit beside unit)
  }
}
```

<div class="solution">

Δείτε παρακάτω πως το υλοποιήσαμε εμείς. Είναι ακριβώς ο ίδιος τρόπος που χρησιμοποιήσαμε στην `boxes`.

```tut:book
def chessboard(count: Int): Image = {
  val blackSquare = Image.rectangle(30, 30) fillColor Color.black
  val redSquare   = Image.rectangle(30, 30) fillColor Color.red
  val base =
    (redSquare   beside blackSquare) above (blackSquare beside redSquare)
  def loop(count: Int): Image =
    count match {
      case 0 => base
      case n =>
        val unit = loop(n-1)
        (unit beside unit) above (unit beside unit)
    }
    
  loop(count)
}
```
</div>

#### Έξυπνα Κουτιά {-}

Ξαναγράψτε την `boxes`, η οποία φαίνεται παρακάτω, έτσι ώστε το `aBox` να έχει ως εμβέλεια μόνο την `boxes` και να δημιουργείται μόνο μία φορά όταν καλείται η `boxes`.

```tut:silent
val aBox = Image.rectangle(20, 20).fillColor(Color.royalBlue)

def boxes(count: Int): Image =
  count match {
    case 0 => Image.empty
    case n => aBox beside boxes(n-1)
  }
```

<div class="solution">

Μπορούμε να το κάνουμε σε δύο στάδια, πρώτα πρέπει να μετακινήσουμε το `aBox` μέσα στην `boxes`.

```tut:silent
def boxes(count: Int): Image = {
  val aBox = Image.rectangle(20, 20).fillColor(Color.royalBlue)
  count match {
    case 0 => Image.empty
    case n => aBox beside boxes(n-1)
  }
}
```

Στην συνέχεια μπορούμε να χρησιμοποιήσουμε μία εσωτερική μέθοδο ώστε να αποφύγουμε την δημιουργία του `aBox` σε κάθε αναδρομή.

```tut:silent
def boxes(count: Int): Image = {
  val aBox = Image.rectangle(20, 20).fillColor(Color.royalBlue)
  def loop(count: Int): Image =
    count match {
      case 0 => Image.empty
      case n => aBox beside loop(n-1)
    }
    
  loop(count)
}
```
</div>
