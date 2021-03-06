## Κατανοώντας την Αναδρομή

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Έχουμε γίνει πλέον έμπειροι χρήστες της δομημένης αναδρομής με φυσικούς αριθμούς.
Ας επιστρέψουμε όμως στο μοντέλο αντικατάστασης για να δούμε αν μπορεί να χρησιμοποιηθεί σε συνδυασμό με το νέο εργαλείο που μάθαμε, την αναδρομή.

Θυμηθείτε ότι αυτή η μέθοδος μας επιτρέπει να αντικαταστήσουμε την τιμή μίας έκφρασης οπουδήποτε και αν την συναντήσουμε.
Στην περίπτωση κλήσης μεθόδου, μπορούμε να αντικαταστήσουμε το σώμα της μεθόδου μετονομάζοντας τις παραμέτρους.

Το πρώτο παράδειγμα που είδαμε με την μέθοδο της αναδρομής ήταν η `boxes` και η σύνταξή της ήταν η παρακάτω:

```tut:silent
val aBox = Image.rectangle(20, 20).fillColor(Color.royalBlue)

def boxes(count: Int): Image =
  count match {
    case 0 => Image.empty
    case n => aBox beside boxes(n-1)
  }
```

Ας προσπαθήσουμε να εφαρμόσουμε την μέθοδο της αντικατάστασης στην `boxes(3)` για να δούμε τι θα πάρουμε.

Η πρώτη αντικατάσταση που μπορούμε να κάνουμε είναι η παρακάτω

```tut:silent
boxes(3)
// Αντικατάσταση του σώματος της `boxes`
3 match {
  case 0 => Image.empty
  case n => aBox beside boxes(n-1)
}
```

Γνωρίζοντας πώς να αξιολογήσουμε μία έκφραση `match` και πώς να χρησιμοποιήσουμε την αντικατάσταση, μπορούμε να κάνουμε την παρακάτω αλλαγή

```tut:silent
3 match {
  case 0 => Image.empty
  case n => aBox beside boxes(n-1)
}
// Αντικατάσταση της δεξιάς πλευράς της έκφρασης της `case n`
aBox beside boxes(2)
```

Μπορούμε να κάνουμε άλλη μία αντικατάσταση στην `boxes(2)` και θα πάρουμε

```tut:silent
aBox beside boxes(2)
// Αντικατάσταση του σώματος της `boxes`
aBox beside {
  2 match {
    case 0 => Image.empty
    case n => aBox beside boxes(n-1)
  }
}
// Αντικατάσταση της δεξιάς πλευράς της έκφρασης της `case n`
aBox beside {
  aBox beside boxes(1)
}
```

Επαναλαμβάνοντας την ίδια διαδικασία μερικές ακόμη φορές θα πάρουμε

```tut:silent
aBox beside {
  aBox beside {
    1 match {
      case 0 => Image.empty
      case n => aBox beside boxes(n-1)
    }
  }
}
// Αντικατάσταση της δεξιάς πλευράς της έκφρασης της `case n`
aBox beside {
  aBox beside {
      aBox beside boxes(0)
  }
}
// Αντικατάσταση του σώματος της `boxes`
aBox beside {
  aBox beside {
    aBox beside {
      0 match {
        case 0 => Image.empty
        case n => aBox beside boxes(n-1)
      }
    }
  }
}
// Αντικατάσταση της δεξιάς πλευράς της έκφρασης της `case 0`
aBox beside {
  aBox beside {
    aBox beside {
      Image.empty
    }
  }
}
```

Το τελικό αποτέλεσμα έχει απλουστευτεί σε

```tut:silent
aBox beside aBox beside aBox beside Image.empty
```

και είναι ακριβώς αυτό που περιμέναμε.
Η μέθοδος της αντικατάστασης μας βοηθάει να καταλάβουμε την λογική της αναδρομής.
Τέλεια!
Παρόλα αυτά, οι αντικαταστάσεις είναι περίπλοκες και η παρακολούθησή τους είναι δύσκολη χωρίς να γράφουμε κάπου τις αλλαγές.
Ένας πιο πρακτικός τρόπος να καταλάβουμε την αναδρομή είναι να υποθέσουμε απλώς ότι λειτουργεί και να ανησυχούμε μόνο για το τι καινούριο θα φέρει το κάθε βήμα.

Για παράδειγμα, όταν προσπαθούμε να καταλάβουμε τι γίνεται στην `boxes`

```tut:silent
def boxes(count: Int): Image =
  count match {
    case 0 => Image.empty
    case n => aBox beside boxes(n-1)
  }
```

εξετάζοντάς την, μπορούμε να πούμε ότι η βασική περίπτωση είναι σωστή.
Κοιτώντας την περίπτωση της αναδρομής *υποθέτουμε* ότι το `boxes(n-1)` θα κάνει αυτό που πρέπει.
Μετά ρωτάμε τον εαυτό μας "είναι σωστό αυτό που κάνουμε στο βήμα της περίπτωσης της αναδρομής; Είναι η ίδια η αναδρομή σωστή;"
Η απάντηση είναι ναι, αν η αναδρομή στο `boxes(n-1)` δημιουργεί `n-1` κουτιά σε σειρά, το να βάλουμε ένα κουτί πριν από αυτή τη σειρά είναι η σωστή κίνηση.
Αυτός ο τρόπος κατανόησης της λογικής της αναδρομής είναι πολύ πιο εύκολος από την χρήση της αντικατάστασης *και* εγγυάται ότι θα λειτουργήσει *αν* χρησιμοποιούμε δομημένη αναδρομή.


### Ασκήσεις {-}

Παρακάτω μπορείτε να δείτε μερικά εύκολα παραδείγματα δομημένης αναδρομής.
Βρείτε αν οι μέθοδοι κάνουν αυτό που υποστηρίζουν, *χωρίς* να τις εκτελέσετε.

```tut:silent
// Παίρνοντας ως δεδομένο έναν φυσικό αριθμό, επιστρέφει αυτόν τον αριθμό
// Παραδείγματα:
//   identity(0) == 0
//   identity(3) == 3
def identity(n: Int): Int =
  n match {
    case 0 => 0
    case n => 1 + identity(n-1)
  }
```

<div class="solution">
Λειτουργεί όπως πρέπει!
Η βασική περίπτωση είναι ξεκάθαρη.
Κοιτώντας την αναδρομική περίπτωση, υποθέτουμε ότι το `identity(n-1)` επιστρέφει το identity για `n-1` (το οποίο είναι ακριβώς `n-1`).
Το identity για `n` είναι `1 + identity(n-1)`.
</div>

```tut:silent
// Παίρνοντας ως δεδομένο έναν φυσικό αριθμό, το αποτέλεσμα πρέπει να είναι το διπλάσιό του
// Παραδείγματα:
//   double(0) == 0
//   double(3) == 6
def double(n: Int): Int =
  n match {
    case 0 => 0
    case n => 2 * double(n-1)
  }
```

<div class="solution">
Κάτι δεν πάει καλά!
Αυτή η μέθοδος είναι λάθος για δύο διαφορετικούς λόγους.
Πρώτον, αφού υπάρχει πολλαπλασιασμός μέσα στην αναδρομική περίπτωση, στο τέλος θα καταλήξουμε να πολλαπλασιάζουμε με την βασική υπόθεση, δηλαδή με το μηδέν. Άρα το αποτέλεσμα θα είναι πάντα μηδέν.

Μπορούμε να προσπαθήσουμε να το διορθώσουμε προσθέτοντας μία περίπτωση για το `1` (και μετά να αναρωτηθούμε γιατί μας απογοήτευσε ο σκελετός της δομημένης αναδρομής).

```tut:silent
def double(n: Int): Int =
  n match {
    case 0 => 0
    case 1 => 1
    case n => 2 * double(n-1)
  }
```

Παρόλα, αυτά πάλι δεν παίρνουμε σωστό αποτέλεσμα! Κάνουμε κάτι λάθος στην αναδρομική περίπτωση: θα έπρεπε να προσθέτουμε αντί να πολλαπλασιάζουμε.

Λίγη άλγεβρα:

```scala
2(n-1 + 1) == 2(n-1) + 2
```

Άρα αν το `double(n-1)` είναι ισοδύναμο με το `2(n-1)` τότε θα πρέπει να *προσθέσουμε* 2 και όχι να πολλαπλασιάσουμε με το 2.
Η σωστή υλοποίηση της μεθόδου είναι η παρακάτω

```tut:silent
def double(n: Int): Int =
  n match {
    case 0 => 0
    case n => 2 + double(n-1)
  }
```
</div>
