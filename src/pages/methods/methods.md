## Μέθοδοι

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Σε ένα προηγούμενο κεφάλαιο δημιουργήσαμε την εικόνα που φαίνεται στην [@fig:methods:sequential-boxes] χρησιμοποιώντας το πρόγραμμα

![Έξι κουτιά γεμισμένα με Royal Blue](./src/pages/programs/sequential-boxes.pdf+svg){#fig:methods:sequential-boxes}

```tut:silent:book
val box =
  Image.rectangle(40, 40).
    lineWidth(5.0).
    lineColor(Color.royalBlue.spin(30.degrees)).
    fillColor(Color.royalBlue) 

box beside box beside box beside box beside box
```

Φανταστείτε ότι θέλουμε να αλλάξουμε το χρώμα των κουτιών.
Θα έπρεπε να γράψουμε από την αρχή την έκφραση για κάθε διαφορετική επιλογή χρώματος.

```tut:silent:book
val paleGoldenrod = {
  val box = 
    Image.rectangle(40, 40).
      lineWidth(5.0).
      lineColor(Color.paleGoldenrod.spin(30.degrees)).
      fillColor(Color.paleGoldenrod) 

  box beside box beside box beside box beside box
}

val lightSteelBlue = {
  val box =
    Image.rectangle(40, 40).
      lineWidth(5.0).
      lineColor(Color.lightSteelBlue.spin(30.degrees)).
      fillColor(Color.lightSteelBlue) 

  box beside box beside box beside box beside box
}

val mistyRose = {
  val box =
    Image.rectangle(40, 40).
      lineWidth(5.0).
      lineColor(Color.mistyRose.spin(30.degrees)).
      fillColor(Color.mistyRose) 

  box beside box beside box beside box beside box
}
```

Αυτό είναι βαρετό.
Η κάθε έκφραση διαφέρει μόνο λίγο από τις άλλες.
Θα ήταν ωραία αν μπορούσαμε να έχουμε ένα γενικό προσχέδιο (??πρότυπο??) και να επιτρέψουμε στο χρώμα να μπορεί να αλλάζει.
Με την δήλωση μίας μεθόδου μπορούμε να κάνουμε ακριβώς αυτό.

```tut:silent:book
def boxes(color: Color): Image = {
  val box =
    Image.rectangle(40, 40).
      lineWidth(5.0).
      lineColor(color.spin(30.degrees)).
      fillColor(color) 

  box beside box beside box beside box beside box
}

// Create boxes with different colors
boxes(Color.paleGoldenrod)
boxes(Color.lightSteelBlue)
boxes(Color.mistyRose)
```

Δοκιμάστε μόνοι σας και δείτε ότι χρησιμοποιώντας μεθόδους παίρνετε το ίδιο αποτέλεσμα όπως όταν τα γράφατε όλα με το χέρι.

Τώρα που είδαμε παράδειγμα δήλωσης μεθόδου πρέπει να εξηγήσουμε το συντακτικό των μεθόδων---αυτά που γράφουμε---και την σημασιολογία των κλήσεων των μεθόδων---πως λειτουργούν σε σχέση με την μέθοδο της αντικατάστασης.
