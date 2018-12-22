## Συνδυάζοντας Τυχαίες Τιμές

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

<div class="callout callout-info">
Εκτός από τα παραπάνω imports, σ' αυτή την ενότητα θα προσθέσουμε και τα παρακάτω:

```tut:silent
import doodle.random._
```
</div>

Μέχρι τώρα έχουμε δει πώς να συντάσσουμε συναρτήσεις για παραγωγή τυχαίων αριθμών χρησιμοποιώντας τον τύπο `Random` καθώς και πώς να φτιάχνουμε ντετερμινιστικούς μετασχηματισμούς για κάποια τυχαία τιμή χρησιμοποιώντας την `map`.
Σ' αυτή την ενότητα θα δούμε πώς μπορούμε να κάνουμε έναν τυχαίο (ή στοχαστικό αν προτιμάτε τους πιο τεχνικούς όρους) μετασχηματισμό μίας τυχαίας τιμής χρησιμοποιώντας την `flatMap`.

Θα ξεκινήσουμε με την δημιουργία της μεθόδου `randomConcentricCircles`, η οποία θα παράγει ομόκεντρους κύκλους με τυχαία απόχρωση χρησιμοποιώντας τις μεθόδους που αναπτύξαμε στην προηγούμενη ενότητα.

Αρχικά, θα γράψουμε τον κώδικα που θα δημιουργεί τους ομόκεντρους κύκλους με ντετερμινιστικά χρώματα με την βοήθεια των εργαλείων που αναπτύξαμε προηγουμένως.

```tut:silent:book
def concentricCircles(count: Int, size: Int, color: Color): Image =
  count match {
    case 0 => Image.empty
    case n => 
      Image.circle(size).fillColor(color) on concentricCircles(n-1, size + 5, color.spin(15.degrees))
  }
  
def randomAngle: Random[Angle] =
  Random.double.map(x => x.turns)

def randomColor(s: Normalized, l: Normalized): Random[Color] =
  randomAngle map (hue => Color.hsl(hue, s, l))
  
def randomCircle(r: Double, color: Random[Color]): Random[Image] =
  color map (fill => Image.circle(r) fillColor fill)
```

Ας φτιάξουμε έναν σκελετό για την μέθοδο `randomConcentricCircles`.

```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  ???
```

Η σημαντική αλλαγή εδώ, είναι ότι επιστρέφουμε μία `Random[Image]` και όχι απλώς μία `Image`.
Ξέρουμε ότι είναι περίπτωση δομημένης αναδρομής με φυσικούς αριθμούς άρα το μόνο που πρέπει να κάνουμε είναι να συμπληρώσουμε το σώμα της μεθόδου.

```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => ???
    case n => ???
  }
```

Η βασική περίπτωση θα είναι η `Random.always(Image.empty)`, που είναι το απευθείας ισοδύναμο της `Image.empty` για ντετερμινιστικές περιπτώσεις.


```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Random.always(Image.empty)
    case n => ???
  }
```

Τι γίνεται με την αναδρομική περίπτωση;
Θα μπορούσαμε να προσπαθήσουμε να χρησιμοποιήσουμε το παρακάτω

```tut:silent:book
val randomPastel = randomColor(0.7.normalized, 0.7.normalized)
```

```tut:fail:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Image.empty
    case n => 
      randomCircle(size, randomPastel) on randomConcentricCircles(n-1, size + 5)
  }
```

αλλά δεν μεταγλωττίζεται.
Η `randomConcentricCircles` αλλά και η `randomCircle` αξιολογούνται με `Random[Image]`.
Δεν υπάρχει μέθοδος `on` στην `Random[Image]`, άρα αυτός ο κώδικας δεν μπορεί να λειτουργήσει.

Αφού αυτός είναι ένας μετασχηματισμός δύο τιμών `Random[Image]`, είναι φανερό ότι χρειαζόμαστε κάποια μέθοδο που θα μας επιτρέψει να μετασχηματίσουμε *δύο* `Random[Image]` και όχι μόνο μία όπως μπορούμε να κάνουμε με την `map`.
Θα μπορούσαμε να ονομάσουμε αυτή την μέθοδο `map2` και να φανταστούμε ότι μπορούμε να γράψουμε κώδικα όπως τον παρακάτω

```scala
randomCircle(size, randomPastel).map2(randomConcentricCircles(n-1, size + 5)){
  (circle, circles) => circle on circles
}
```

Πιθανόν να χρειαζόμασταν και `map3`, `map4`, και ούτω καθεξής.
Αντί γι' αυτές τις ειδικές περιπτώσεις, μπορούμε να χρησιμοποιήσουμε την `flatMap` σε συνδυασμό με την `map`.

```scala
randomCircle(size, randomPastel) flatMap { circle =>
  randomConcentricCircles(n-1, size + 5) map { circles =>
    circle on circles
  }
}
```

Μπορείτε να δείτε τον ολοκληρωμένο κώδικα παρακάτω

```tut:silent:book
def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Random.always(Image.empty)
    case n => 
      randomCircle(size, randomPastel) flatMap { circle =>
        randomConcentricCircles(n-1, size + 5) map { circles =>
          circle on circles
        }
      }
  }
```

Ένα παράδειγμα εξόδου του παραπάνω κώδικα φαίνεται στην εικόνα [@fig:generative:random-concentric-circles].

![Το αποτέλεσμα μίας εκτέλεσης της `randomConcentricCircles(10, 10).run.draw`](./src/pages/generative/random-concentric-circles.pdf+svg){#fig:generative:random-concentric-circles}

Ας δούμε όμως πιο αναλυτικά αυτόν τον συνδυασμό της `flatMap` και της `map` για να καταλάβουμε πώς λειτουργεί.

### Άλγεβρα Τύπων

Κατά την γνώμη μας ο πιο εύκολος τρόπος για να καταλάβετε τον παρακάτω κώδικα είναι να δείτε τους τύπους.

```scala
randomCircle(size, randomPastel) flatMap { circle =>
  randomConcentricCircles(n-1, size + 5) map { circles =>
    circle on circles
  }
}
```

Ξεκινώντας από μέσα προς τα έξω, έχουμε

```scala
{ circles =>
    circle on circles
}
```

η οποία είναι μία συνάρτηση με τύπο

```scala
Image => Image
```

Γύρω από αυτό έχουμε

```scala
randomConcentricCircles(n-1, size + 5) map { circles =>
    circle on circles
  }
```

Ξέρουμε ότι η `randomConcentricCircles(n-1, size + 5)` έχει τύπο `Random[Image]`.
Αντικαθιστώντας με τον τύπο `Image => Image` που βρήκαμε προηγουμένως, έχουμε

```scala
Random[Image] map (Image => Image)
```

Τώρα μπορούμε να ασχοληθούμε με ολόκληρη την έκφραση

```scala
randomCircle(size, randomPastel) flatMap { circle =>
  randomConcentricCircles(n-1, size + 5) map { circles =>
    circle on circles
  }
}
```

Η ` randomCircle(size, randomPastel)` έχει τύπο `Random[Image]`.
Κάνοντας πάλι αντικαταστάσεις, παίρνουμε μία εξίσωση τύπων για όλη την έκφραση.

```scala
Random[Inage] flatMap (Random[Image] map (Image => Image))
```

Τώρα μπορούμε να εφαρμόσουμε αυτές τις εξισώσεις τύπων στην `map` και στην `flatMap` που είδαμε προηγουμένως:

```scala
F[A] map (A => B) = F[B]
F[A] flatMap (A => F[B]) = F[B]
```

Δουλεύοντας και πάλι από μέσα προς τα έξω, χρησιμοποιούμε πρώτα την εξίσωση τύπου για την `map`, η οποία απλοποιεί την έκφραση τύπων ως εξής

```scala
Random[Inage] flatMap (Random[Image])
```

Τώρα μπορούμε να εφαρμόσουμε την εξίσωση τύπων και στην `flatMap` παράγοντας μόνο

```scala
Random[Image]
```

Αυτό μας λέει ότι το αποτέλεσμα έχει τον τύπο που θέλουμε.
Παρατηρήστε ότι εφαρμόσαμε την μέθοδο της αντικατάστασης σε επίπεδο τύπων---αυτή την ίδια τεχνική την χρησιμοποιούμε συνήθως και στο επίπεδο τιμών.


### Ασκήσεις {-}

Μην ξεχάσετε να κάνετε import την `doodle.random._` πριν ξεκινήσετε τις παρακάτω ασκήσεις.

#### Τυχαιότητα και Τυχαιότητα {-}

Ποια είναι η διαφορά μεταξύ του αποτελέσματος της `programOne` και της `programTwo` που μπορείτε να δείτε παρακάτω; Γιατί διαφέρουν;

```tut:silent:book
def randomCircle(r: Double, color: Random[Color]): Random[Image] =
  color map (fill => Image.circle(r) fillColor fill)

def randomConcentricCircles(count: Int, size: Int): Random[Image] =
  count match {
    case 0 => Random.always(Image.empty)
    case n => 
      randomCircle(size, randomPastel) flatMap { circle =>
        randomConcentricCircles(n-1, size + 5) map { circles =>
          circle on circles
        }
      }
  }

val circles = randomConcentricCircles(5, 10)
val programOne = 
  circles flatMap { c1 => 
    circles flatMap { c2 => 
      circles map { c3 => 
        c1 beside c2 beside c3
      } 
    }
  }

val programTwo =
  circles map { c => c beside c beside c }
```

<div class="solution">
Η έξοδος της `programOne` είναι τρεις διαφορετικοί κύκλοι σε μία γραμμή ενώ η έξοδος της `programTwo` επαναλαμβάνει τον ίδιο κύκλο τρεις φορές. Η τιμή `circles` αντιπροσωπεύει ένα πρόγραμμα που παράγει μία εικόνα τυχαία χρωματισμένων ομόκεντρων κύκλων. Θυμηθείτε ότι η `map`  αναπαριστά έναν ντετερμινιστικό μετασχηματισμό και άρα η έξοδος του `programTwo` πρέπει να είναι ο ίδιος κύκλος που επαναλαμβάνεται τρεις φορές χωρίς να γίνονται τυχαίες επιλογές. Στο `programOne` συγχωνεύουμε το `circle` με τον εαυτό του τρεις φορές. Ίσως πιστεύετε ότι έτσι θα έπρεπε να υπάρχει μονο μία τυχαία εικόνα που επαναλαμβάνεται τρεις φορές και όχι τρεις διαφορετικές αλλά θυμηθείτε ότι η `Random` διατηρεί την αντικατάσταση. Μπορούμε να γράψουμε το `programOne` και όπως παρακάτω και να πάρουμε το ίδιο αποτέλεσμα

```tut:book
val programOne = 
  randomConcentricCircles(5, 10) flatMap { c1 => 
    randomConcentricCircles(5, 10) flatMap { c2 => 
      randomConcentricCircles(5, 10) map { c3 => 
        c1 beside c2 beside c3
      } 
    }
  }
```

Έτσι φαίνεται πιο καθαρά ότι φτιάχνουμε τρεις διαφορετικούς κύκλους.


#### Χρωματιστά Κουτιά {-}

Ας επιστρέψουμε σ' ένα πρόβλημα που είχαμε συναντήσει στην αρχή του βιβλίου: τον σχεδιασμό χρωματιστών κουτιών. Αυτή τη φορά θα κάνουμε την διαβάθμιση των χρωμάτων λίγο πιο ενδιαφέρουσα αναθέτοντάς τους χρώματα τυχαία.

Θυμηθείτε την βασική δομημένη αναδρομή για δημιουργία μίας σειράς από κουτιά

```tut:book
def rowOfBoxes(count: Int): Image =
  count match {
    case 0 => rectangle(20, 20)
    case n => rectangle(20, 20) beside rowOfBoxes(n-1)
  }
```

Ας αλλάξουμε τον παραπάνω κώδικα, όπως κάναμε με τους ομόκεντρους κύκλους, ώστε να γεμίζουμε το κάθε κουτί με κάποιο τυχαίο χρώμα. *Βοήθεια:* μπορεί να βρείτε βολική την χρήση του κώδικα που γράψαμε προηγουμένως για την `randomConcentricCircles`. Στην εικόνα [@fig:generative:random-color-boxes] μπορείτε να δείτε ένα παράδειγμα εξόδου.

![Κουτιά τυχαίων χρωμάτων.](./src/pages/generative/random-color-boxes.pdf+svg){#fig:generative:random-color-boxes}

<div class="solution">
Αυτός ο κώδικας έχει ακριβώς την ίδια μορφή με την `randomConcentricCircles`.

```tut:book
val randomAngle: Random[Angle] =
  Random.double.map(x => x.turns)

val randomColor: Random[Color] =
  randomAngle map (hue => Color.hsl(hue, 0.7.normalized, 0.7.normalized))

def coloredRectangle(color: Color): Image =
  rectangle(20, 20) fillColor color

def randomColorBoxes(count: Int): Random[Image] =
  count match {
    case 0 => randomColor map { c => coloredRectangle(c) }
    case n =>
      val box = randomColor map { c => coloredRectangle(c) }
      val boxes = randomColorBoxes(n-1)
      box flatMap { b =>
        boxes map { bs => b beside bs }
      }
  }
```
</div>
