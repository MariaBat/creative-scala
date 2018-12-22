## Ασκήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

### Σύνθετος Στόχος

Δημιουργήστε με γραμμές μία εικόνα ενός στόχου τοξοβολίας με τρεις ομόκεντρες ζώνες, όπως φαίνεται στην εικόνα [@fig:pictures:target1].

![Απλός στόχος τοξοβολίας](src/pages/pictures/target1.pdf+svg){#fig:pictures:target1}

Για περισσότερη εξάσκηση προσθέστε ένα στήριγμα ώστε να μπορούμε να τοποθετήσουμε τον στόχο σε οποιαδήποτε απόσταση θέλουμε, όπως φαίνεται στην εικόνα [@fig:pictures:target2].

![Χρωματιστός στόχος τοξοβολίας](src/pages/pictures/target2.pdf+svg){#fig:pictures:target2}

<div class="solution">
Η πιο απλή λύση είναι να δημιουργήσουμε τρεις ομόκεντρους κύκλους χρησιμοποιώντας τον τελεστή `on`:

```tut:silent:book
(circle(10) on circle(20) on circle(30))
```

Για περισσότερη εξάσκηση δημιουργήσαμε ένα στήριγμα χρησιμοποιώντας δύο ορθογώνια:

```tut:silent:book
(
  circle(10) on
  circle(20) on
  circle(30) above
  rectangle(6, 20) above
  rectangle(20, 6)
)
```
</div>


### Μείνετε στον Στόχο

Χρωματίστε τον στόχο κόκκινο και άσπρο. Αν φτιάξατε το στήριγμα, χρωματίστε το καφέ.
Χρωματίστε επίσης μία πράσινη περιοχή η οποία θα αναπαριστά το έδαφος πάνω στο οποίο στέκεται ο στόχος όπως φαίνεται στην εικόνα [@fig:pictures:target3].

![Στόχος τοξοβολίας με στήριγμα](src/pages/pictures/target3.pdf+svg){#fig:pictures:target3}

<div class="solution">
Το κόλπο εδώ είναι η χρήση παρενθέσεων ώστε να ελέγξετε την σειρά με την οποία χρωματίζεται η σύνθεση.
Κάθε μία από τις μεθόδους `fillColor()`, `lineColor()`, και `lineWidth()`
εφαρμόζεται σε μία μόνο εικόνα. Τέλος, πρέπει να σιγουρευτούμε ότι η εικόνα αποτελείται από τα σωστά σχήματα:

```tut:silent:book
(
  ( circle(10) fillColor Color.red ) on
  ( circle(20) fillColor Color.white ) on
  ( circle(30) fillColor Color.red lineWidth 2 ) above
  ( rectangle(6, 20) above rectangle(20, 6) fillColor Color.brown ) above
  ( rectangle(80, 25) lineWidth 0 fillColor Color.green )
)
```
</div>
