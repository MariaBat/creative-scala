## Βοηθητικές Παράμετροι

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
val aBox = Image.rectangle(20, 20).fillColor(Color.royalBlue)
```

Είδαμε πώς μπορούμε να χρησιμοποιήσουμε την δομημένη αναδρομή σε φυσικούς αριθμούς ώστε να γράψουμε ενδιαφέροντα προγράμματα.
Σ' αυτή την ενότητα θα δούμε μία επέκταση των προηγουμένων η οποία θα μας επιτρέψει να γράψουμε πιο σύνθετα προγράμματα χρησιμοποιώντας *βοηθητικές παραμέτρους*.
Η βοηθητική παράμετρος είναι μία επιπρόσθετη παράμετρος στην μέθοδο μας και μας επιτρέπει να περάσουμε περισσότερες πληροφορίες στην αναδρομική κλήση.

Για παράδειγμα, φανταστείτε ότι δημιουργείτε την εικόνα [@fig:recursion:growing-boxes], η οποία απεικονίζει μία σειρά κουτιών το μέγεθος των οποίων μεγαλώνει καθώς προχωράνε στην σειρά.

![Κουτιά που μεγαλώνουν σε κάθε αναδρομή.](./src/pages/recursion/growing-boxes.pdf+svg){#fig:recursion:growing-boxes}

Πώς μπορούμε να φτιάξουμε αυτήν την εικόνα;

Ξέρουμε ότι πρέπει να είναι μία δομημένη αναδρομή σε φυσικούς αριθμούς και άρα μπορούμε αμέσως να γράψουμε τον σκελετό της μεθόδου

```scala
def growingBoxes(count: Int): Image =
  count match {
    case 0 => base
    case n => unit add growingBoxes(n-1)
  }
```

Χρησιμοποιώντας αυτά που μάθαμε από την προηγούμενη ενασχόλησή μας με την `boxes` μπορούμε ακόμη να γράψουμε το παρακάτω

```scala
def growingBoxes(count: Int): Image =
  count match {
    case 0 => Image.empty
    case n => Image.rectangle(???,???) beside growingBoxes(n-1)
  }
```

Η πρόκληση βρίσκεται στο πώς θα κάνουμε το κουτί να μεγαλώνει καθώς κινείται προς τα δεξιά.

Υπάρχουν δύο τρόποι για να τα καταφέρουμε.
Ο πρώτος τρόπος είναι να αλλάξουμε την σειρά στην αναδρομική περίπτωση και να ορίσουμε το μέγεθος των κουτιών σε συνάρτηση με το `n`.
Δείτε παρακάτω τον αντίστοιχο κώδικα.

```tut:book
def growingBoxes(count: Int): Image =
  count match {
    case 0 => Image.empty
    case n => growingBoxes(n-1) beside Image.rectangle(n*10, n*10)
  }
```

Αφιερώστε λίγο χρόνο στην κατανόηση του παραπάνω κώδικα πριν προχωρήσουμε στην λύση που θα χρησιμοποιεί βοηθητική παράμετρο.

Η χρήση βοηθητικής παραμέτρου σημαίνει απλώς την πρόσθεση μίας ακόμη παραμέτρου στην μέθοδο `growingBoxes`η οποία θα καθορίζει το πόσο μεγάλο πρέπει να είναι το κάθε κουτί.
Το μέγεθος αλλάζει καθώς γίνεται η κάθε αναδρομή.
Δείτε τον παρακάτω κώδικα.

```tut:book
def growingBoxes(count: Int, size: Int): Image =
  count match {
    case 0 => Image.empty
    case n => Image.rectangle(size, size) beside growingBoxes(n-1, size + 10)
  }
```

Ο τρόπος με την χρήση της βοηθητικής παραμέτρου έχει δύο πλεονεκτήματα: το μόνο που πρέπει να σκεφτούμε είναι το τι αλλάζει από την μία αναδρομή στην άλλη (στην συγκεκριμένη περίπτωση μεγαλώνει το μέγεθος του κουτιού), και επιτρέπει σ' αυτόν που κάνει την κλήση της μεθόδου να αλλάξει την επιπρόσθετη αυτή παράμετρο (για παράδειγμα, μπορεί να κάνει το αρχικό κουτί μικρότερο ή μεγαλύτερο).

Τώρα που είδαμε την μέθοδο της βοηθητικής παραμέτρου ας εξασκηθούμε στην χρήση της.

#### Κουτιά που Αλλάζουν Χρώμα {-}

Σ' αυτή την άσκηση θα δημιουργήσουμε την εικόνα [@fig:recursion:gradient-boxes].
Γνωρίζουμε ήδη πώς να φτιάξουμε μία σειρά από κουτιά.
Η πρόκληση σ' αυτή την άσκηση είναι να κάνουμε το χρώμα να αλλάζει σε κάθε βήμα.

Βοήθεια: μπορείτε να χρησιμοποιήσετε το `spin` για να γεμίσετε τα κουτιά με χρώμα σε κάθε αναδρομή.

![Πέντε κουτιά με διαφορετικά χρώματα ξεκινώντας από το Royal Blue](./src/pages/recursion/gradient-boxes.pdf+svg){#fig:recursion:gradient-boxes}

<div class="solution">
Υπάρχουν δύο τρόποι για να φτάσετε στην λύση.
Στον τρόπο με χρήση βοηθητικής παραμέτρου θα προστεθεί μία ακόμη παράμετρος στην `gradientBoxes` και θα περάσουμε το `Color` (χρώμα) μέσω της δομημένης αναδρομής.

```tut:book
def gradientBoxes(n: Int, color: Color): Image =
  n match {
    case 0 => Image.empty
    case n => aBox.fillColor(color) beside gradientBoxes(n-1, color.spin(15.degrees))
  }
```

Μπορούμε ακόμη να βάλουμε την fill color σε μία συνάρτηση `n`, όπως κάναμε προηγουμένως με το size για το μέγεθος των κουτιών στην `growingBoxes`.

```tut:book
def gradientBoxes(n: Int): Image =
  n match {
    case 0 => Image.empty
    case n => aBox.fillColor(Color.royalBlue.spin((15*n).degrees)) beside gradientBoxes(n-1)
  }
```
</div>

#### Ομόκεντροι κύκλοι {-}

Τώρα ας κάνουμε μία παραλλαγή και ας ζωγραφίσουμε ομόκεντρους κύκλους όπως αυτούς που φαίνονται στην εικόνα [@fig:recursion:concentric-circles]. Σ' αυτή την περίπτωση αλλάζουμε το μέγεθος σε κάθε βήμα και όχι το χρώμα. Αλλιώς το σχέδιο θα μένει το ίδιο. Προσπαθήστε να το φτιάξετε μόνοι σας.

![Ομόκεντροι κύκλοι σε χρώμα Royal Blue](./src/pages/recursion/concentric-circles.pdf+svg){#fig:recursion:concentric-circles}

<div class="solution">
Η λύση είναι σχεδόν ίδια με το `growingBoxes`.

```tut:book
def concentricCircles(count: Int, size: Int): Image =
  count match {
    case 0 => Image.empty
    case n => Image.circle(size) on concentricCircles(n-1, size + 5)
  }
```
</div>

#### Άλλη μία Άσκηση {-}

Τώρα ας συνδυάσουμε τις δύο τεχνικές ώστε να αλλάζουμε το μέγεθος αλλά και το χρώμα σε κάθε βήμα, για να πάρουμε ως αποτέλεσμα την εικόνα [@fig:recursion:colorful-circles].
Πειραματιστείτε μέχρι να βρείτε κάτι που σας αρέσει.

![Ομόκεντροι κύκλοι διαφορετικών χρωμάτων](./src/pages/recursion/colorful-circles.pdf+svg){#fig:recursion:colorful-circles}

<div class="solution">
Δείτε παρακάτω την δική μας λύση, στην οποία προσπαθήσαμε να σπάσουμε το πρόβλημα σε επαναχρησιμοποιήσιμα κομμάτια ώστε να μειωθεί ο επαναλαμβανόμενος κώδικα.
Παρόλα αυτά ακόμη υπάρχει αρκετός κώδικας που επαναλαμβάνεται αφού ακόμα δεν έχουμε τα κατάλληλα εργαλεία για να τον μειώσουμε περισσότερο.
Θα επιστρέψουμε σ' αυτό σύντομα.

```tut:book
def circle(size: Int, color: Color): Image =
  Image.circle(size).lineWidth(3.0).lineColor(color)

def fadeCircles(n: Int, size: Int, color: Color): Image =
  n match {
    case 0 => Image.empty
    case n => circle(size, color) on fadeCircles(n-1, size+7, color.fadeOutBy(0.05.normalized))
  }

def gradientCircles(n: Int, size: Int, color: Color): Image =
  n match {
    case 0 => Image.empty
    case n => circle(size, color) on gradientCircles(n-1, size+7, color.spin(15.degrees))
  }

def image: Image =
  fadeCircles(20, 50, Color.red) beside gradientCircles(20, 50, Color.royalBlue)
```
</div>
