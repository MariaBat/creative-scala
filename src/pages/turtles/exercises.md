## Ασκήσεις

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
import doodle.turtle._
import doodle.turtle.Instruction._
```

### Επίπεδο Πολύγωνο

Χρησιμοποιώντας τις μεθόδους του Turtle, την `Range` και την `flatMap`, ξαναγράψτε την μέθοδό σας ώστε να δημιουργήσετε ένα πολύγωνο. Η δήλωση της `polygon` είναι η παρακάτω

```tut:silent:book
def polygon(sides: Int, sideLength: Double): Image = 
  ???
```

<div class="solution">
Χρησιμοποιώντας την `flatMap` μπορούμε να κάνουμε τον κώδικα πιο συμπαγή από ότι αν χρησιμοποιούσαμε δομημένη αναδρομή όπως παλαιότερα.

```tut:silent:book
def polygon(sides: Int, sideLength: Double): Image = {
  val rotation = Angle.one / sides
  
  Turtle.draw((1 to sides).toList.flatMap { n =>
    List(turn(rotation), forward(sideLength))
  })
}
```
</div>


### Επίπεδο Σπειροειδές

Χρησιμοποιώντας τις μεθόδους του Turtle, την `Range` και την `flatMap`, ξαναγράψτε την μέθοδό `squareSpiral` για να δημιουργήσετε ένα τετράγωνο σπειροειδές. Η δήλωση της `squareSpiral` είναι η παρακάτω

```tut:silent:book
def squareSpiral(steps: Int, distance: Double, angle: Angle, increment: Double): Image =
  ???
```

<div class="solution">
Και πάλι, το αποτέλεσμα θα είναι πιο συμπαγές από ότι ήταν την προηγούμενη φορά. Είναι πιο εύκολη η ανάγνωση της μεθόδου; Εμάς μας φαίνεται περίπου το ίδιο. Πιστεύουμε ότι η κατανόηση είναι θέμα εξοικείωσης και ελπίζουμε ότι έχουμε φτάσει σε καλό σημείο με την `flatMap`.

```tut:silent:book
def squareSpiral(steps: Int, distance: Double, angle: Angle, increment: Double): Image = {
  Turtle.draw((1 to steps).toList.flatMap { n =>
   List(forward(distance + (n * increment)), turn(angle)) 
  })
}
```
</div>


### Τέχνη με L-System

Σ' αυτή την άσκηση θέλουμε να χρησιμοποιήσετε την δημιουργικότητά σας για να κατασκευάσετε μία εικόνα ενός φυσικού αντικειμένου, χρησιμοποιώντας το δικό σας L-system. Έχετε ήδη δει αρκετά παραδείγματα και μπορείτε να τα χρησιμοποιήσετε ως έμπνευση.
