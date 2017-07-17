## Ασκήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

### Σύνθετος στόχος (Compilation Target)

Δημιουργήστε με γραμμές μια ζωγραφιά ενός στόχου τοξοβολίας με τρεις ομόκεντρες ζώνες, όπως φαίνεται στην εικόνα [@fig:pictures:target1].

![Απλός στόχος τοξοβολίας](src/pages/pictures/target1.pdf+svg){#fig:pictures:target1}

Για περισσότερη εξάσκηση προσθέστε ένα στήριγμα ώστε να μπορούμε να τοποθετήσουμε τον στόχο σε μια εμβέλεια όπως φαίνεται στην εικόνα [@fig:pictures:target2].

![Στόχος τοξοβολίας με χρώμα](src/pages/pictures/target2.pdf+svg){#fig:pictures:target2}

<div class="solution">
Η πιο απλή λύση είναι να δημιουργήσουμε τρεις ομόκεντρους κύκλους χρησιμοποιώντας τον operator `on`:

```tut:silent:book
(circle(10) on circle(20) on circle(30))
```

Για περισσότερη εξάσκηση δημιουργήστε ένα στήριγμα χρησιμοποιώντας δύο ορθογώνια:

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


### Μείνετε στον στόχο

Χρωματίστε τον στόχο κόκκινο και άσπρο , το στήριγμα καφέ (αν είναι δυνατό),
και δημιουργήστε λίγο έδαφος σε πράσινο χρώμα όπως φαίνεται στην εικόνα [@fig:pictures:target3].

![Χρωματιστός στόχος τοξοβολίας](src/pages/pictures/target3.pdf+svg){#fig:pictures:target3}

<div class="solution">
Το κόλπο εδώ είναι η χρήση παρενθέσεων ώστε να ελένξετε την σειρά της σύνθεσης.
Οι μέθοδοι `fillColor()`, `lineColor()`, και `lineWidth()`
εφαρμόζονται σε μία εικόνα---πρέπει να σιγουρευτούμε ότι η εικόνα αποτελείται από τα σωστά σχήματα:

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
