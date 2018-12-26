## Διάταξη

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Στην προηγούμενη ενότητα είδαμε πώς μπορούμε να κατασκευάσουμε τα βασικά σχήματα. Αν θέλουμε να δημιουργήσουμε πιο περίπλοκες εικόνες μπορούμε να χρησιμοποιήσουμε μεθόδους διάταξης. Δοκιμάστε τον παρακάτω κώδικα ---θα πρέπει να δείτε έναν κύκλο και ένα ορθογώνιο το ένα δίπλα στο άλλο, όπως φαίνεται στην εικόνα [@fig:picture:circle-rect].

~~~ scala
(circle(10) beside rectangle(10, 20)).draw
~~~

![Ένας κύκλος δίπλα σε ένα ορθογώνιο](src/pages/pictures/circle-beside-rectangle.pdf+svg){#fig:picture:circle-rect}

Το Doodle έχει αρκετές μεθόδους διάταξης για συνδυασμό εικόνων. Μπορείτε να τις βρείτε στον πίνακα [@tbl:pictures:layout]. Μπορείτε να τις δοκιμάσετε και να δείτε τι κάνουν.

----------------------------------------------------------------------------------------
Operator              Τύπος   Περιγραφή                  Παράδειγμα
--------------------- ------- -------------------------- -------------------------------
`Image beside Image`  `Image` Τοποθετεί τις εικόνες      `circle(10) beside circle(20)`
                              οριζόντια την μία δίπλα
                              στην άλλη.

`Image above Image`   `Image` Τοποθετεί τις εικόνες      `circle(10) above circle(20)`
                              κάθετα την μία πάνω από
                              την άλλη.

`Image below Image`   `Image` Τοποθετεί τις εικόνες      `circle(10) below circle(20)`
                              κάθετα την μία κάτω από
                              την άλλη.

`Image on Image`      `Image` Τοποθετεί τις εικόνες      `circle(10) on circle(20)`
                              κεντραρισμένες την μία
                              πάνω στην άλλη

`Image under Image`   `Image` Τοποθετεί τις εικόνες      `circle(10) under circle(20)`
                              κεντραρισμένες την μία
                              κάτω από την άλλη
----------------------------------------------------------------------------------------

: Οι διαθέσιμες μέθοδοι διάταξης του Doodle {#tbl:pictures:layout}

### Ασκήσεις {-}

#### Η Διάμετρος ενός Κύκλου {-}

Φτιάξτε την εικόνα [@fig:picture:width-of-a-circle] χρησιμοποιώντας τις μεθόδους διάταξης και τα βασικά σχήματα που έχουμε μάθει μέχρι τώρα.

![Η διάμετρος ενός κύκλου](src/pages/pictures/width-of-a-circle.pdf+svg){#fig:picture:width-of-a-circle}

<div class="solution">
Είναι τρεις μικροί κύκλοι πάνω από έναν μεγαλύτερο κύκλο. Μπορούμε να φτιάξουμε αυτή την εικόνα με τον παρακάτω κώδικα.

```tut:book
(circle(20) beside circle(20) beside circle(20)) on circle(60)
```
</div>
