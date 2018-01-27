## Προγραμματίζοντας εκτός κονσόλας

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Ο κώδικας που γράφαμε μέσα στην κονσόλα θα δημιουργήσει προβλήματα αν εκτελεστεί εκτός κονσόλας. Για παράδειγμα, βάλτε τον παρακάτω κώδικα μέσα στο `Example.scala` στον φάκελο `src/main/scala`.

```tut:silent:book
Image.circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed
```

Τώρα επανεκκινήστε το SBT και προσπαθήστε να μπείτε στην κονσόλα. Θα πρέπει να δείτε ένα μήνυμα λάθους παρόμοιο με το παρακάτω

```bash
[error] src/main/scala/Example.scala:1: expected class or object definition
[error] circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed
[error] ^
[error] one error found
```

Αν χρησιμοποιείτε IDE Θα δείτε επίσης κάτι παρόμοιο.

Το πρόβλημα είναι ότι:

- Η Scala προσπαθεί να μεταγλωττίσει όλο τον κώδικά μας πριν ξεκινήσει η κονσόλα, και
- υπάρχουν κάποιοι περιορισμοί στον κώδικα, που είναι γραμμένοι σε αρχεία και δεν ισχύουν για τον κώδικα που γράφεται απευθείας στην κονσόλα.

Πρέπει να ξέρουμε αυτούς τους περιορισμούς και να αλλάξουμε τον τρόπο που γράφουμε κώδικα σε αρχεία αναλόγως.

Το μήνυμα λάθους μας δίνει ένα στοιχείο: `expected class or object definition` (αναμένονταν ορισμός κλάσης ή αντικειμένου). Δεν γνωρίζουμε ακόμη τι είναι μία κλάση, αλλά γνωρίζουμε για αντικείμενα---όλες οι τιμές είναι αντικείμενα. Στη Scala όλος ο κώδικας μέσα σε ένα αρχείο πρέπει να είναι τοποθετημένος μέσα σε ένα αντικείμενο ή μία κλάση. Μπορούμε να ορίσουμε ένα αντικείμενο εύκολα φτιάχνοντας μία έκφραση όπως την παρακάτω.

```tut:silent:book
object Example {
  (circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed).draw
}
```

Τώρα ο κώδικας δεν θα μεταγλωττιστεί για έναν άλλο λόγο. Θα δείτε πολλά λάθη όπως το παρακάτω

```bash
[error] doodle/shared/src/main/scala/doodle/examples/Example.scala:2: not found: value circle
[error]   (circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed).draw
[error]    ^
```

Ο μεταγλωττιστής λέει ότι έχουμε χρησιμοποιήσει ένα όνομα, `circle`, αλλά ο μεταγλωττιστής δεν ξέρει σε ποια τιμή αναφέρεται αυτό το όνομα.
Θα έχει παρόμοιο πρόβλημα με το `Color` στον παραπάνω κώδικα.
θα μιλήσουμε με περισσότερες λεπτομέρειες για τα ονόματα σε λίγο.
Τώρα όμως ας πούμε στον μεταγλωττιστή που μπορεί να βρει τις τιμές γι'αυτά τα ονόματα προσθέτοντας μερικά `imports`.
Το όνομα `Color` βρίσκεται μέσα σε ένα *package (πακέτο)* που ονομάζεται `doodle.core`, και το όνομα `circle` είναι μέσα στο αντικείμενο `Image` που είναι μέσα στο `doodle.core`.
Μπορούμε να πούμε στον μεταγλωττιστή να χρησιμοποιήσει όλα τα ονόματα του `doodle.core`, και όλα τα ονόματα στο αντικείμενο `Image` γράφοντας

```tut:silent:book
import doodle.core._
import doodle.core.Image._
```

Υπάρχουν και μερικά άλλα ονόματα που πρέπει να βρει ο μεταγλωττιστής ώστε να δουλέψει ολόκληρος ο κώδικας.
Μπορούμε να τα εισάγουμε με αυτές τις γραμμές

```tut:silent:book
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Θα πρέπει να τοποθετήσουμε όλα αυτά τα imports στο πάνω μέρος του αρχείου, ώστε ο τελικός κώδικας να μοιάζει με τον παρακάτω

```scala
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._

object Example {
  (circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed).draw
}
```

Με αυτό στη σωστή θέση ο κώδικας θα πρέπει να μεταγλωττιστεί χωρίς θέμα.

Τώρα όταν πάμε στην κονσόλα μέσα στο SBT μπορούμε να αναφερόμαστε στον κώδικά μας χρησιμοποιώντας το όνομα, `Example`, που του δώσαμε προηγουμένως.

```scala
Example // φτιάχνει την εικόνα
```

### Άσκηση {-}

Αν δεν το έχετε κάνει ήδη, αποθηκεύστε τον κώδικα στο αρχείο `src/main/scala/Example.scala` και ελέγξτε ότι ο κώδικας μεταγλωττίζεται και ότι μπορείτε να έχετε πρόσβαση σε αυτόν από την κονσόλα.
