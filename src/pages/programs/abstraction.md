## Αφαιρετικότητα

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Στην προηγούμενη ενότητα μάθαμε πολλά για τα ονόματα.
Αν θέλουμε να χρησιμοποιήσουμε φανταχτερές λέξεις προγραμματιστών, θα λέγαμε ότι *τα ονόματα καλύπτουν τις εκφράσεις*.
Αυτή η φράση δίνει την ουσία του τι κάνει ο ορισμός ονομάτων, οπότε ας την αναλύσουμε.

Το να αφαιρείς σημαίνει να βγάζεις τις ασήμαντες λεπτομέρειες.
Για παράδειγμα οι αριθμοί είναι μία αφαίρεση.
Ο αριθμός "ένα" δεν βρίσκεται ποτέ στην φύση ως καθαρή έννοια.
Είναι πάντα ένα αντικείμενο, όπως ένα μήλο, ή ένα αντίτυπο της Creative Scala.
Όταν κάνουμε αριθμητική, η έννοια των αριθμών μας επιτρέπει να απομακρύνουμε τις ασήμαντες λεπτομέρειες και να χειριστούμε τους αριθμούς μόνους τους.

Παρομοίως ένα όνομα αντιπροσωπεύει μία έκφραση.
Η έκφραση μας λέει πώς να φτιάξουμε μία τιμή.
Εάν αυτή η τιμή έχει όνομα τότε δεν χρειάζεται να ξέρουμε τίποτε άλλο για το πώς είναι κατασκευασμένη.
Η έκφραση μπορεί να έχει αυθαίρετη περιπλοκότητα, αλλά δεν χρειάζεται να ενδιαφερθούμε γι'αυτή αν απλώς χρησιμοποιούμε το όνομα.
Αυτό ακριβώς εννοούμε όταν λέμε ότι τα ονόματα καλύπτουν τις εκφράσεις.
Όποτε έχουμε μία έκφραση, μπορούμε να την αντικαταστήσουμε με το όνομα που αναφέρεται στην ίδια τιμή.

Η αφαιρετικότητα προσδίδει ευκολία στην ανάγνωση και γραφή του κώδικα.
Ας πάρουμε ως παράδειγμα την δημιουργία μίας σειράς κουτιών όπως φαίνεται στην εικόνα [@fig:programs:sequential-boxes].

![Έξι κουτιά γεμισμένα με Royal Blue](./src/pages/programs/sequential-boxes.pdf+svg){#fig:programs:sequential-boxes}

Μπορούμε να γράψουμε μία μοναδική έκφραση που να δημιουργεί την εικόνα.

```tut:silent:book
(
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) beside
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) beside
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) beside
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) beside
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue)
)
```

Σ'αυτόν τον κώδικα είναι δύσκολο να διακρίνει κανείς το απλό μοτίβο που κρύβει.
Μπορείτε στ'αλήθεια μετά από μία ματιά να πείτε ότι όλα τα ορθογώνια είναι ακριβώς ίδια;
Αν χρησιμοποιήσουμε την αφαιρετικότητα και δώσουμε όνομα στο βασικό κουτί, τότε η ανάγνωση του κώδικα θα γίνει πολύ πιο εύκολη.

```tut:silent:book
val box =
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) 

box beside box beside box beside box beside box
```

Τώρα μπορούμε εύκολα να δούμε πώς δημιουργείτε το κουτί, και πώς η τελική εικόνα επαναλαμβάνεται πέντε φορές.


### Ασκήσεις {-}

#### Τοξοβολία και πάλι{-}

Ας επιστρέψουμε στον στόχο τοξοβολίας που είχαμε δημιουργήσει σε προηγούμενο κεφάλαιο, όπως φαίνεται στην εικόνα [@fig:programs:target3].

![Ο στόχος τοξοβολίας](./src/pages/programs/target3.pdf+svg){#fig:programs:target3}

Την τελευταία φορά που φτιάξαμε την εικόνα δεν γνωρίζαμε πώς να δώσουμε ονόματα σε τιμές, ώστε να μπορούμε να γράψουμε μία μεγάλη έκφραση.
Αυτή τη φορά, δώστε ονόματα στα στοιχεία της εικόνας ώστε να γίνει πιο εύκολο για κάποιον να καταλάβει πώς κατασκευάστηκε η εικόνα.
Θα πρέπει να χρησιμοποιήσετε το δικό σας γούστο για να αποφασίσετε ποια μέρη θα πρέπει να ονομαστούν και ποια δεν χρειάζονται δικό τους όνομα.

<div class="solution">
Αποφασίσαμε να δώσουμε όνομα στον στόχο, στο στήριγμα και στο έδαφος, όπως φαίνεται παρακάτω.
Έτσι γίνεται σαφές το πώς κατασκευάστηκε η τελική εικόνα.
Το να ονομάσουμε περισσότερα στοιχεία μας φάνηκε ότι δεν θα βοηθούσε στην κατανόηση.

```tut:silent:book
val coloredTarget =
  (
    Image.circle(10).fillColor(Color.red) on
      Image.circle(20).fillColor(Color.white) on
      Image.circle(30).fillColor(Color.red)
  )

val stand =
  Image.rectangle(6, 20) above Image.rectangle(20, 6).fillColor(Color.brown)

val ground =
  Image.rectangle(80, 25).lineWidth(0).fillColor(Color.green)

val image = coloredTarget above stand above ground
```
</div>


#### Δρόμοι Μπροστά {-}

Για πιο ενδιαφέρουσα χρήση των ονομάτων, κατασκευάστε ένα σκηνικό δρόμου όπως φαίνεται στην εικόνα [@fig:programs:street].
Με το να ονομάσετε τα διαφορετικά στοιχεία της εικόνας θα πρέπει να καταφέρετε την αποφυγή πολλών επαναλήψεων.

![Ένα σκηνικό δρόμου](./src/pages/programs/street.pdf+svg){#fig:programs:street}

<div class="solution">
Ορίστε η λύση.
Όπως μπορείτε να δείτε, με το να χωρίσουμε το σκηνικό σε μικρότερα κομμάτια καταφέραμε να γράψουμε σχετικά λίγο κώδικα.

```tut:silent:book
val roof = Image.triangle(50, 30) fillColor Color.brown

val frontDoor =
  (Image.rectangle(50, 15) fillColor Color.red) above (
    (Image.rectangle(10, 25) fillColor Color.black) on
    (Image.rectangle(50, 25) fillColor Color.red)
  )

val house = roof above frontDoor

val tree =
  (
    (Image.circle(25) fillColor Color.green) above
    (Image.rectangle(10, 20) fillColor Color.brown)
  )

val streetSegment =
  (
    (Image.rectangle(30, 3) fillColor Color.yellow) beside
    (Image.rectangle(15, 3) fillColor Color.black) above
    (Image.rectangle(45, 7) fillColor Color.black)
  )

val street = streetSegment beside streetSegment beside streetSegment

val houseAndGarden =
  (house beside tree) above street

val image = (
  houseAndGarden beside
  houseAndGarden beside
  houseAndGarden
) lineWidth 0
```
</div>
