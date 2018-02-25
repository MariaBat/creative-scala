## Προγραμματίζοντας Eκτός Kονσόλας

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Ο κώδικας που γράψαμε στην κονσόλα θα δημιουργήσει προβλήματα αν εκτελεστεί κάπου αλλού. Για παράδειγμα, βάλτε τον παρακάτω κώδικα μέσα στο `Example.scala` στον κατάλογο `src/main/scala`.

```tut:silent:book
Image.circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed
```

Τώρα επανεκκινήστε το SBT και προσπαθήστε να εισέλθετε και πάλι στην κονσόλα. Λογικά, θα δείτε ένα μήνυμα λάθους παρόμοιο με το παρακάτω

```bash
[error] src/main/scala/Example.scala:1: expected class or object definition
[error] circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed
[error] ^
[error] one error found
```

Αν χρησιμοποιείτε κάποιο IDE, θα συμβεί και εκεί κάτι παρόμοιο.

Το πρόβλημα είναι το εξής:

- Η Scala προσπαθεί να μεταγλωττίσει όλο τον κώδικά μας πριν ξεκινήσει η κονσόλα, και
- υπάρχουν κάποιοι περιορισμοί που ισχύουν για κώδικα που είναι γραμμένος σε αρχεία αλλά όχι για αυτόν που γράφεται απευθείας στην κονσόλα.

Πρέπει να ξέρουμε αυτούς τους περιορισμούς και να αλλάξουμε τον τρόπο που γράφουμε κώδικα σε αρχεία, αναλόγως.

Το μήνυμα λάθους μας δίνει ένα στοιχείο: `expected class or object definition` (αναμένονταν ορισμός κλάσης ή αντικειμένου). Δεν γνωρίζουμε ακόμη τι είναι μία κλάση αλλά γνωρίζουμε για τα αντικείμενα---όλες οι τιμές είναι αντικείμενα. Στην Scala, ο κώδικας που βρίσκεται σε ένα αρχείο πρέπει να γραφτεί μέσα σε ένα αντικείμενο ή σε μία κλάση. Μπορούμε να ορίσουμε ένα αντικείμενο εύκολα, φτιάχνοντας μία έκφραση όπως την παρακάτω.

```tut:silent:book
object Example {
  (circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed).draw
}
```

Τώρα ο κώδικας δεν θα μεταγλωττιστεί για έναν άλλο λόγο. Θα δείτε πολλά λάθη όπως τα παρακάτω

```bash
[error] doodle/shared/src/main/scala/doodle/examples/Example.scala:2: not found: value circle
[error]   (circle(100) fillColor Color.paleGoldenrod lineColor Color.indianRed).draw
[error]    ^
```

Ο μεταγλωττιστής λέει ότι έχουμε χρησιμοποιήσει ένα όνομα, το `circle`, αλλά δεν ξέρει σε ποια τιμή αναφέρεται.
Το πρόβλημα είναι παρόμοιο με αυτό που προέκυψε για το `Color` στο παραπάνω κομμάτι κώδικα.
θα μιλήσουμε με περισσότερες λεπτομέρειες για τα ονόματα σε λίγο.
Τώρα όμως ας πούμε στον μεταγλωττιστή πού μπορεί να βρει τις τιμές γι'αυτά τα ονόματα προσθέτοντας μερικά `imports`.
Το όνομα `Color` βρίσκεται μέσα σε ένα *package* που ονομάζεται `doodle.core` και το `circle` μέσα στο αντικείμενο `Image` του `doodle.core`.
Μπορούμε να πούμε στον μεταγλωττιστή να χρησιμοποιήσει όλα τα ονόματα του `doodle.core` και όλα τα ονόματα του αντικείμενου `Image`, γράφοντας

```tut:silent:book
import doodle.core._
import doodle.core.Image._
```

Υπάρχουν και μερικά ακόμη ονόματα που πρέπει να βρει ο μεταγλωττιστής ώστε να δουλέψει ολόκληρος ο κώδικας.
Μπορούμε να τα εισάγουμε με αυτές τις γραμμές

```tut:silent:book
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Θα πρέπει να τοποθετήσουμε όλα τα imports στο πάνω μέρος του αρχείου. Ο τελικός κώδικας θα μοιάζει με τον παρακάτω

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

Τώρα θα πρέπει να μεταγλωττιστεί χωρίς προβλήματα.

Όταν επιστρέψουμε στην κονσόλα μέσα στο SBT, μπορούμε να αναφερθούμε στον κώδικά μας, χρησιμοποιώντας το όνομα `Example` που του δώσαμε προηγουμένως.

```scala
Example // δημιουργεί την εικόνα
```

### Άσκηση {-}

Αν δεν το έχετε κάνει ήδη, αποθηκεύστε τον παραπάνω κώδικα στο αρχείο `src/main/scala/Example.scala`. Ελέγξτε ότι μεταγλωττίζεται και ότι η πρόσβαση σε αυτόν από την κονσόλα είναι δυνατή.
