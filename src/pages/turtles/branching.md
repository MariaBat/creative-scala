## Δομές Διακλαδώσεων

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

<div class="callout callout-info">
Εκτός από τα imports που δίνονται στην αρχή κάθε κεφαλαίου, σ' αυτή την ενότητα θα προσθέσουμε και τα παρακάτω:

```tut:silent
import doodle.turtle._
import doodle.turtle.Instruction._
```
</div>

Χρησιμοποιώντας την εντολή `branch` του turtle μπορούμε να δημιουργήσουμε σχήματα που θα ήταν δύσκολο να τα φτιάξουμε χωρίς αυτό. Η εντολή `branch` δέχεται μία λίστα `List[Instruction]`. Αποθηκεύει την τρέχουσα κατάσταση του turtle (την θέση του και την κατεύθυνσή του), σχεδιάζει γραμμές σύμφωνα με τις εντολές που του έχουν δοθεί και επιστρέφει το turtle στην αποθηκευμένη κατάσταση.

Δείτε τον παρακάτω κώδικα που δημιουργεί την εικόνα [@fig:turtles:y]. Εδώ έχει σχεδιαστεί εύκολα με turtle και την εντολή branch αλλά δεν θα ήταν το ίδιο αν έπρεπε να χρησιμοποιήσουμε μόνο ένα μονοπάτι.

```tut:book
val y = Turtle.draw(List(
          forward(100),
          branch(turn(45.degrees), forward(100)),
          branch(turn(-45.degrees), forward(100)))
        )
```

![Μία εικόνα της οποίας ο σχεδιασμός με turtle και την εντολή branch είναι εύκολος αλλά αλλιώς χωρίς αυτά θα ήταν δύσκολος.](src/pages/turtles/y.pdf+svg){#fig:turtles:y}

Χρησιμοποιώντας διακλαδώσεις μπορούμε να μοντελοποιήσουμε κάποιες μορφές βιολογικής ανάπτυξης, ώστε να δημιουργήσουμε για παράδειγμα εικόνες φυτών όπως την [@fig:turtles:plant]. Ένα τέτοιο μοντέλο είναι γνωστό ως *L-system*. Ένα L-system αποτελείται από δύο μέρη:

- έναν αρχικό *seed* για να ξεκινήσει η ανάπτυξη και
- τους *κανόνες αναπαραγωγής*, οι οποίοι καθορίζουν πώς γίνεται η ανάπτυξη.

Ένα παράδειγμα αυτής της διαδικασίας φαίνεται στην εικόνα [@fig:turtles:branches]. Το σχήμα στην αριστερή πλευρά είναι το seed. Οι κανόνες αναπαραγωγής είναι οι ακόλουθοι:

- κάθε ευθεία γραμμή διπλασιάζεται σε μέγεθος και
- το μπουμπούκι (το διαμάντι στην άκρη της γραμμής) μεγαλώνει σε δύο άλλα κλαδιά που και αυτά τελειώνουν με μπουμπούκια.

![Αναπαράσταση της ανάπτυξης ενός φυτού χρησιμοποιώντας κανόνες αναπαραγωγής.](src/pages/turtles/branches.pdf+svg){#fig:turtles:branches}

Επομένως μπορούμε να γράψουμε αυτούς τους κανόνες ως μετασχηματισμό της `Instruction` υποθέτοντας ότι χρησιμοποιούμε την `NoOp` για την αναπαράσταση ενός μπουμπουκιού.

```tut:book
val stepSize = 10

def rule(i: Instruction): List[Instruction] =
  i match {
    case Forward(_) => List(forward(stepSize), forward(stepSize))
    case NoOp => 
      List(branch(turn(45.degrees), forward(stepSize), noop), 
           branch(turn(-45.degrees), forward(stepSize), noop))
    case other => List(other)
  }
```

Παρατηρήστε ότι χρησιμοποιήσαμε την match στην `Instruction`, όπως την χρησιμοποιούσαμε και για άλλους αλγεβρικούς τύπους---όπως σε φυσικούς αριθμούς και στην `List`---που έχουμε δει μέχρι τώρα. Εισάγοντας την `doodle.turtle.Instruction._` μπορούμε να έχουμε πρόσβαση σε όλες τις εντολές για την `Instruction`, οι οποίες είναι οι παρακάτω

- το `Forward(distance)`, όπου το `distance` είναι τύπου `Double`,
- το `Turn(angle)`, όπου το `angle` είναι μία γωνία,
- το `NoOp` και
- το `Branch(instructions)`, όπου το `instructions` είναι μία `List[Instruction]`.

Ως συνάρτηση, η `rule` έχει τύπο `Instruction => List[Instruction]`, αφού είναι πολύ πιθανόν να μετασχηματίσουμε την κάθε εντολή σε πολλές άλλες εντολές (όπως κάνουμε στην περίπτωση της `Forward`). Πώς μπορούμε να εφαρμόσουμε αυτόν τον κανόνα στην `List[Instruction]` ώστε να δημιουργήσουμε μία `List[Instruction]` (για παράδειγμα να το εφαρμόσουμε στο `List[noop]`); Μπορούμε να χρησιμοποιήσουμε την `map`;

<div class="solution">
Όπως μας λενε οι τυποι, αυτή τη φορα η `map` δεν είναι η καταλληλη λύση. Θυμηθείτε την εξισωση τύπων για την `map`

```scala
List[A] map (A => B) = List[B]
```

Αν
- έχουμε μία `List[Instruction]` και
- χρησιμοποιήσουμε την `map` σε μία συνάρτηση `Instruction => List[Instruction]`, τότε
- το αποτέλεσμα που θα πάρουμε θα είναι `List[List[Instruction]]`

όπως μπορούμε να δούμε και από την εξίσωση τύπων.

Το turtle όμως δεν ξέρει πώς να σχεδιάσει την `List[List[Instruction]]` οπότε το παραπάνω δεν θα δουλέψει.
</div>

Υπάρχει μία μέθοδος στις `List` που ονομάζεται `flatten` και μπορεί να μετατρέψει μία `List[List[A]]` σε `List[A]`. Θα *μπορούσαμε* να χρησιμοποιήσουμε έναν συνδυασμό της `map` και της `flatten` αλλά έχουμε μία ακόμη καλύτερη λύση. Ο συνδυασμός αυτών των δύο προκύπτει τόσο συχνά---και μάλιστα σε διάφορες περιπτώσεις που θα δούμε αργότερα---που δημιουργήθηκε μία ξεχωριστή μέθοδος για τον χειρισμό του. Η μέθοδος αυτή ονομάζεται `flatMap`.

Η εξίσωση τύπων για την `flatMap` είναι

```scala
List[A] flatMap (A => List[B]) = List[B]
```

και αυτό παρουσιάζεται γραφικά στην εικόνα [@fig:turtles:flatMap]. Μπορούμε να δούμε ότι η `flatMap` έχει τον κατάλληλο τύπο ώστε να συνδυάσει την `rule` με την `List[Instruction]` για να ξαναδημιουργήσει την `List[Instruction]`.

![Γραφική αναπαράσταση της εξίσωσης τύπων για την flatMap.](src/pages/turtles/flatMap.pdf+svg){#fig:turtles:flatMap}

Σε προηγούμενες αναφορές μας στην `map`, είπαμε ότι δεν μας επιτρέπει να αλλάξουμε τον αριθμό των στοιχείων μία λίστας. Δεν μπορούμε να δημιουργήσουμε ένα νέο "κουτί" με γραφικά χρησιμοποιώντας την `map`. Με την `flatMap` μπορούμε να αλλάξουμε το κουτί. Όσον αφορά τις λίστες, μπορούμε να αλλάξουμε τον αριθμό των στοιχείων τους.


### Ασκήσεις {-}

#### Όλα Διπλά {-}

Χρησιμοποιώντας την `flatMap`, γράψτε μία μέθοδο με όνομα `double` η οποία θα μετατρέπει μία `List` σε `List` στην οποία όλα τα στοιχεία θα εμφανίζονται δύο φορές. Για παράδειγμα

```tut:invisible
def double[A](in: List[A]): List[A] =
  in.flatMap { x => List(x, x) }
```

```tut:book
double(List(1, 2, 3))
double(List("do", "ray", "me"))
```

<div class="solution">
Υπάρχουν δύο σημεία που πρέπει να προσέξουμε:

- να αναγνωρίσουμε τον τρόπο με τον οποίο θα χρησιμοποιήσουμε την `flatMap` και
- να θυμηθούμε πώς χρησιμοποιούνται οι μεταβλητές τύπου.

```tut:silent:book
def double[A](in: List[A]): List[A] =
  in.flatMap { x => List(x, x) }
```
</div>


#### Ή Τίποτα {-}

Χρησιμοποιώντας την `flatMap`, γράψτε μία μέθοδο με όνομα `nothing` η οποία μετατρέπει μία `List` σε μία κενή `List`. Για παράδειγμα

```tut:invisible
def nothing[A](in: List[A]): List[A] =
  in.flatMap { x => List() }
```

```tut:book
nothing(List(1, 2, 3))
nothing(List("do", "ray", "me"))
```

<div class="solution">
Θα μπορούσαμε πολύ εύκολα να γράψουμε αυτή την μεθοδο ως

```tut:silent:book
def nothing[A](in: List[A]): List[A] =
  List() // ή List.empty ή Nil
```

αλλά το νόημα της άσκησης είναι να εξοικειωθούμε με την χρήση της `flatMap`. Με την `flatMap` μπορούμε να γράψουμε την μέθοδο όπως παρακάτω

```tut:silent:book
def nothing[A](in: List[A]): List[A] =
  in.flatMap { x => List.empty }
```
</div>


#### Ξαναγράφοντας τους Κανόνες {-}

Γράψτε μία μέθοδο με όνομα `rewrite` με δήλωση όπως την παρακάτω

```tut:silent:book
def rewrite(instructions: List[Instruction], 
            rule: Instruction => List[Instruction]): List[Instruction] =
  ???
```

Αυτή η μέθοδος πρέπει να εφαρμόζει την `rule` ώστε να γραφτούν ξανά οι εντολές στην `instructions`, εκτός από μερικά κλαδιά που θα χρειαστεί να τα χειριστείτε ξεχωριστά. Αν βρείτε μπροστά σας μία διακλάδωση τότε θα πρέπει να ξαναγράψετε όλες τις εντολές που υπάρχουν μέσα της αλλά να αφήσετε την ίδια την διακλάδωση άθικτη.

*Σημείωση*: Θα πρέπει να περάσετε μία `List[Instruction]` στην `branch`, αφού η `branch` δέχεται μηδέν ή παραπάνω εντολές (οι οποίες ονομάζονται *varargs*). Για να μετασχηματίσετε την `List[Instruction]` σε μορφή την οποία δέχεται η `branch`, βάλτε μετά τις παραμέτρους το σύμβολο `:_*` όπως παρακάτω

```tut:book
val instructions = List(turn(45.degrees), forward(10))
branch(instructions:_*)
```

<div class="solution">
Υπάρχουν δύο σημεία που πρέπει να προσέξουμε:

- να αναγνωρίσουμε ότι πρέπει να χρησιμοποιήσουμε την `flatMap`, για τους λόγους που αναφέρθηκαν παραπάνω και
- να καταλάβουμε ότι πρέπει να καλέσουμε την `rewrite` αναδρομικά για την επεξεργασία των περιεχομένων της διακλάδωσης.

Το τελευταίο, είναι ένα παράδειγμα δομημένης αναδρομής αλλά σε λίγο πιο περίπλοκη μορφή από αυτήν που έχουμε συνηθίσει.

```tut:silent:book
def rewrite(instructions: List[Instruction], rule: Instruction => List[Instruction]): List[Instruction] =
  instructions.flatMap { i =>
    i match {
      case Branch(i) =>
        List(branch(rewrite(i, rule):_*))
      case other =>
        rule(other)
    }
  }
```
</div>
 
#### Το Δικό σας L-System {-}

Είμαστε πλέον έτοιμοι να δημιουργήσουμε ένα πλήρες L-system. Χρησιμοποιώντας την `rewrite` από παραπάνω, δημιουργήστε μία μέθοδο με όνομα `iterate` με την παρακάτω δήλωση

```tut:book
def iterate(steps: Int,
            seed: List[Instruction], 
            rule: Instruction => List[Instruction]): List[Instruction] =
  ???
```

Θα πρέπει να εφαρμόζει αναδρομικά την `rule` στη `seed` για τόσες επαναλήψεις όσες καθορίζει η `steps`.

<div class="solution">
Είναι μία απλή δομημένη αναδρομή με φυσικούς αριθμούς και η `rewrite` κάνει όλη την δύσκολη δουλειά.

```tut:silent:book
def iterate(steps: Int, 
            seed: List[Instruction], 
            rule: Instruction => List[Instruction]): List[Instruction] =
  steps match {
    case 0 => seed
    case n => iterate(n - 1, rewrite(seed, rule), rule)
  }
```
</div>


#### Φυτά και Άλλες Δημιουργίες {-}

Δημιουργήστε την εικόνα [@fig:turtles:branching] και την [@fig:turtles-koch-curve] χρησιμοποιώντας τις δικές εκδοχές του L-system.

![Πέντε επαναλήψεις ενός απλού L-system.](src/pages/turtles/branching.pdf+svg){#fig:turtles:branching}

![Πέντε επαναλήψεις της καμπύλης Koch, ένα fractal που είναι εύκολο να δημιουργηθεί χρησιμοποιώντας ένα L-System.](src/pages/turtles/koch-curve.pdf+svg){#fig:turtles:koch-curve}
