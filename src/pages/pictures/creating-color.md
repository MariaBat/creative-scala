## Δημιουργώντας χρώματα

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DFrame._
import doodle.backend.StandardInterpreter._
```

Είδαμε πως να χρησιμοποιούμε προκαθορισμένα χρώματα στις εικόνες μας. Αλλά τι γίνεται αν θέλουμε να χρησιμοποιήσουμε τα δικά μας χρώματα; Σε αυτή την ενότητα θα δούμε πως να δημιουργήσουμε δικά μας χρώματα, και πως να μεταμορφώσουμε τα ήδη υπάρχοντα χρώματα σε νέα.

### Χρώματα RGB

Οι υπολογιστές χρησιμοποιούν χρώματα που φτιάχνονται συνδυάζοντας διαφορετικές ποσότητες κόκκινου, πράσινου και μπλε. Αυτό το μοντέλο "RGB" είναι ένα [προσθετικό μοντέλο (additive model)][additive-model] χρωμάτων. Κάθε ένα από τα συστατικά του στοιχεία, δηλαδή το κόκκινο, το πράσινο και το μπλε, μπορούν να πάρουν μια τιμή από το μηδέν ως το 255. Αν και τα τρία ρυθμιστούν στο μέγιστο, δηλαδή το 255, το αποτέλεσμα του συνδυασμού τους είναι το καθαρό λευκό. Αν ρυθμιστούν στο μηδέν θα προκύψει το μαύρο.

Μπορούμε να δημιουργήσουμε τα δικά μας χρώματα RGB χρησιμοποιώντας την μέθοδο `rgb` στο αντικείμενο `Color`. Αυτή η μέθοδος παίρνει τρεις παραμέτρους: το κόκκινο, το πράσινο και το μπλε. Αυτές οι παράμετροι είναι αριθμοί από το 0 ως το 255 και ονομάζονται `UnsignedByte`[^byte]. Δεν υπάρχει κυριολεκτική έκφραση για το `UnsignedByte` όπως υπάρχει για το `Int`, άρα πρέπει να μετατρέψουμε τον `Int` σε `UnsignedByte`. Αυτό μπορούμε να το κάνουμε χρησιμοποιώντας την μέθοδο `uByte`. Ένας `Int` μπορεί να πάρει περισσότερες τιμές από ότι ένας `UnsignedByte`, έτσι αν ο αριθμός είναι πολύ μικρός ή πολύ μεγάλος για να αναπαρασταθεί ως `UnsignedByte`, θα μετατραπεί στην κοντινότερη τιμή από 0 ως 255. Στα παρακάτω παραδείγματα παρουσιάζονται οι μετατροπές.

```tut:book
0.uByte
255.uByte
128.uByte
-100.uByte // Too small, is transformed to 0
1000.uByte // Too big, is transformed to 255
```

(Παρατηρήστε ότι το `UnsignedByte` είναι ένα στοιχείο του Doodle. Δεν είναι κάτι που παρέχεται από την Scala.)

Τώρα που ξέρουμε πως να φτιάξουμε `UnsignedBytes` μπορούμε να φτιάξουμε χρώματα RGB.

```tut:silent:book
Color.rgb(255.uByte, 255.uByte, 255.uByte) // White
Color.rgb(0.uByte, 0.uByte, 0.uByte) // Black
Color.rgb(255.uByte, 0.uByte, 0.uByte) // Red
```

### Χρώματα HSL

Η αναπαράσταση των χρωμάτων RGB δεν είναι πολύ εύκολο να χρησιμοποιηθεί. Η μορφή hue-saturation-lightness (απόχρωση- κορεσμός- φωτεινότητα)(HSL) είναι πιο κοντά στον τρόπο με τον οποίο αντιλαμβανόμαστε τα χρώματα. Σ' αυτή την αναπαράσταση ένα χρώμα αποτελείτε από:

- το *hue*, που είναι μια γωνία από 0 εώς 360 μοίρες και δίνει μια περιστροφή γύρω από τον τροχό των χρωμάτων.
- το *saturation*, που είναι ένας αριθμός από 0 εώς 1 και δίνει την ένταση του χρώματος από γκρι μέχρι καθαρό χρώμα, και
- το *lightness* μεταξύ 0 και 1 και δίνει στο χρώμα φωτεινότητα από μαύρο μέχρι καθαρό λευκό.

Η εικόνα [@fig:pictures:color-wheel] δείχνει πως διαφέρουν τα χρώματα καθώς αλλάζουμε την απόχρωση (hue) και την φωτεινότητα (lightness) και η εικόνα [@fig:pictures:saturation] δείχνει το πως επηρεάζει η αλλαγή του κορεσμού (saturation).

![Ένας τροχός χρωμάτων που δείχνει τις αλλαγές στην απόχρωση (περιστροφές) και την φωτεινότητα (απόσταση από το κέντρο), όταν ο κορεσμός έχει σταθερή τιμή 1.](src/pages/pictures/color-wheel.pdf+svg){#fig:pictures:color-wheel}

![Διαβαθμίσεις που δείχνουν πως αλλάζοντας τον κορεσμό επηρεάζεται το χρώμα, καθώς η απόχρωση και η φωτεινότητα μένουν σταθερές. Ο κορεσμός στα αριστερά είναι μηδέν και στα δεξιά είναι ένα.](src/pages/pictures/saturation.pdf+svg){#fig:pictures:saturation}

Μπορούμε να κατασκευάσουμε ένα χρώμα στην αναπαράσταση HSL χρησιμοποιώντας την μέθοδο `Color.hsl`. Αυτή η μέθοδος παίρνει ως παραμέτρους το hue, το saturation, και το lightness. Το hue είναι ένα `Angle`. Μπορούμε να μετατρέψουμε έναν `Double` σε `Angle` χρησιμοποιώντας τις μεθόδους `degrees` (ή `radians`).

```tut:book
0.degrees
180.degrees
3.14.radians
```

Ο κορεσμός και η φωτεινότητα κανονικοποιούνται μεταξύ του 0.0 και του 1.0. Μπορούμε να μετατρέψουμε ένα `Double` σε κανονικοποιημένη τιμή χρησιμοποιώντας την μέθοδο `.normalized`.

```tut:book
0.0.normalized 
1.0.normalized
1.2.normalized // Too big, is clipped to 1.0
-1.0.normalized // Too small, is clipped to 0.0
```

Τώρα, μπορούμε να φτιάξουμε χρώματα χρησιμοποιώντας την αναπαράσταση HSL.

```tut:silent:book
Color.hsl(0.degrees, 0.8.normalized, 0.6.normalized) // A pastel red
```

Για να δούμε αυτό το χρώμα μπορούμε να το ενσωματώσουμε σε μια εικόνα. Για παράδειγμα, δείτε την εικόνα [@fig:pictures:triangle-pastel-red].

![Ενσωματώνοντας κόκκινο παστέλ σε ένα τρίγωνο](./src/pages/pictures/triangle-pastel-red.pdf+svg){#fig:pictures:triangle-pastel-red}


### Χειρισμός Χρωμάτων

Η αποτελεσματικότητα μιας σύνθεσης πολύ συχνά εξαρτάται τόσο από τις σχέσεις μεταξύ των χρωμάτων όσο και από τα ίδια τα χρώματα που χρησιμοποιούνται. Τα χρώματα έχουν διάφορες μεθόδους οι οποίες μας επιτρέπουν να δημιουργήσουμε ένα νέο χρώμα από ένα που υπάρχει ήδη. Αυτές που χρησιμοποιούνται πιο συχνά είναι:

- η `spin`, η οποία περιστρέφει την απόχρωση σε κάποια `Angle`;
- η `saturate` και η `desaturate`, οι οποίες προσθέτουν και αφαιρούν αντίστοιχα μια `κανονικοποιημένη` τιμή ενός χρώματος, και
- η `lighten` και η `darken`, οι οποίες προσθέτουν και αφαιρούν αντίστοιχα μια `κανονικοποιημένη` τιμή πό την φωτεινότητα.

Για παράδειγμα,

```tut:silent:book
((circle(100) fillColor Color.red) beside 
  (circle(100) fillColor Color.red.spin(15.degrees)) beside
    (circle(100) fillColor Color.red.spin(30.degrees))).lineWidth(5.0)
```

έχει ως αποτέλεσμα [@fig:pictures:three-circles-spin].

![Τρεις κύκλοι, ξεκινώντας από το Color.red και περιστρέφοντας 15 μοίρες ανά επόμενο κύκλο](./src/pages/pictures/three-circles-spin.pdf+svg){#fig:pictures:three-circles-spin}

Στην εικόνα  [@fig:pictures:saturate-and-lighten] μπορείτε να δείτε ένα παρόμοιο παράδειγμα, αλλά αυτή τη φορά για τον κορεσμό και την φωτεινότητα.

```tut:silent:book
(((circle(20) fillColor (Color.red darken 0.2.normalized))
  beside (circle(20) fillColor Color.red)
  beside (circle(20) fillColor (Color.red lighten 0.2.normalized))) above
((rectangle(40,40) fillColor (Color.red desaturate 0.6.normalized)) 
  beside (rectangle(40,40) fillColor (Color.red desaturate 0.3.normalized))
  beside (rectangle(40,40) fillColor Color.red)))
```

![Οι τρεις πάνω κύκλοι δείχνουν το αποτέλεσμα αλλαγής της φωτεινότητας, και τα τρία κάτω τετράγωνα το αποτέλεσμα αλλαγής του κορεσμού.](./src/pages/pictures/saturate-and-lighten.pdf+svg){#fig:pictures:saturate-and-lighten}

[^byte]: Το byte είναι ένας αριθμός με 256 πιθανές τιμές και χρειάζεται χώρο 8 bits για να αναπαρασταθεί σε υπολογιστή. Ένα signed byte έχει ακέραιες τιμές από το -128 μέχρι το 127, ενώ ένα unsigned byte κυμαίνεται από το 0 ως το 255.


### Διαφάνεια (Transparency)

Μπορούμε επίσης να προσθέσουμε στα χρώματά μας έναν βαθμό διαφάνειας, προσθέτοντας μια τιμή *alpha*. Μια τιμή alpha καθορισμένη στο 0.0 υποδεικνύει ένα εντελώς διάφανο χρώμα, ενώ αν της δοθεί η τιμή 1.0 το χρώμα είναι εντελώς αδιαφανές. Οι μέθοδοι `Color.rgba` και `Color.hsla` έχουν μια τέταρτη παράμετρο, η οποία είναι μια `κανονικοποιημένη` τιμή alpha. Ακόμη, μπορούμε να δημιουργήσουμε ένα νέο χρώμα με διαφορετική διαφάνεια χρησιμοποιώντας την μέθοδο `alpha` σε ένα χρώμα. Δείτε ένα παράδειγμα στην εικόνα [@fig:pictures:rgb-alpha].

```tut:silent:book
((circle(40) fillColor (Color.red.alpha(0.5.normalized))) beside
 (circle(40) fillColor (Color.blue.alpha(0.5.normalized))) on
 (circle(40) fillColor (Color.green.alpha(0.5.normalized))))
```

![Κύκλοι με alpha με τιμή 0.5 επιδεικνύουν την διαφάνεια](./src/pages/pictures/rgb-alpha.pdf+svg){#fig:pictures:rgb-alpha}


### Ασκήσεις {-}

#### Συνδυασμένα τρίγωνα (Complementary triangles){-}

Δημιουργήστε τρία τρίγωνα, διατεταγμένα μέσα σε ένα τρίγωνο και έχουν χρώματα που ταιριάζουν μεταξύ τους. Τέτοιου είδους χρώματα είναι αυτά που έχουν παρόμοια απόχρωση (hue). Δείτε στην εικόνα [@fig:pictures:complementary-triangles]ένα (αρκετά περίπλοκο) παράδειγμα.

![Συνδυασμένα τρίγωνα. Τα χρώματα που επιλέχθηκαν είναι παραλλαγές του `darkSlateBlue`](./src/pages/pictures/complementary-triangles.pdf+svg){#fig:pictures:complementary-triangles}

<div class="solution">
Τα παραδείγματα γίνονται όλο και πιο μεγάλα για να τα γράφουμε στην κονσόλα. Παρακάτω θα δούμε έναν διαφορετικό τρόπο.

```tut:book
((triangle(40, 40)
       lineWidth 6.0
       lineColor Color.darkSlateBlue
       fillColor (Color.darkSlateBlue lighten 0.3.normalized saturate 0.2.normalized spin 10.degrees)) above
  ((triangle(40, 40)
      lineWidth 6.0
      lineColor (Color.darkSlateBlue spin (-30.degrees))
      fillColor (Color.darkSlateBlue lighten 0.3.normalized saturate 0.2.normalized spin (-20.degrees))) beside
     (triangle(40, 40)
        lineWidth 6.0
        lineColor (Color.darkSlateBlue spin (30.degrees))
        fillColor (Color.darkSlateBlue lighten 0.3.normalized saturate 0.2.normalized spin (40.degrees)))))
```
</div>
