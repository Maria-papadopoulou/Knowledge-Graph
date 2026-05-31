# Κατασκευή Knowledge Graph Ακαδημαϊκών Συγγραμμάτων στο Neo4j

## 1. Εισαγωγή & Περιγραφή Έργου

Το παρόν έργο αφορά την κατασκευή και ανάλυση ενός **Knowledge Graph (Γράφου Γνώσης)** χρησιμοποιώντας το **Neo4j**. Ο γράφος αναπαριστά τις σημασιολογικές σχέσεις μεταξύ ακαδημαϊκών συγγραφέων και των βιβλίων τους σε διαφορετικά επιστημονικά πεδία. 
Στόχος είναι η αποθήκευση των δεδομένων και η εξαγωγή νέας γνώσης (διεπιστημονικότητα) μέσω **Reasoning (Συλλογιστικής)**.

---

## 2. Μεθοδολογία και Κώδικας Cypher

### Δημιουργία των Δεδομένων
Αρχικά, διασφαλίζουμε ότι η βάση είναι κενή και δημιουργούμε τους κόμβους για τους συγγραφείς (`:Author`) και για τα βιβλία (`:Book`).

```cypher
// 1. ΚΑΘΑΡΙΣΜΟΣ ΒΑΣΗΣ
MATCH (n) DETACH DELETE n;

// 2. ΔΗΜΙΟΥΡΓΙΑ ΚΟΜΒΩΝ (ΟΝΤΟΤΗΤΕΣ)
CREATE (a1:Author {name: "Nello Cristianini"})
CREATE (a2:Author {name: "Donald Knuth"})
CREATE (a3:Author {name: "Neil Campbell"})

CREATE (b1:Book {title: "An Introduction to Support Vector Machines", category: "Πληροφορική"})
CREATE (b2:Book {title: "Introduction to Computational Genomics", category: "Βιολογία"})
CREATE (b3:Book {title: "The Art of Computer Programming", category: "Πληροφορική"})
CREATE (b4:Book {title: "Concrete Mathematics", category: "Μαθηματικά"})
CREATE (b5:Book {title: "Campbell Biology", category: "Βιολογία"})
```
Κόμβοι βιβλίων:
<img width="833" height="636" alt="Screenshot_2" src="https://github.com/user-attachments/assets/d531eaaa-2f7d-47b9-8fa9-fb60c86be41b" />

Κόμβοι συγγραφέων:
<img width="833" height="636" alt="Screenshot_3" src="https://github.com/user-attachments/assets/c1b2d4e2-da7a-478e-851e-9db8d4a8b7df" />

**Αποτέλεσμα:** Ο γράφος αποτελείται αρχικά από 8 ασύνδετους κόμβους

## 3. Δημιουργία Σημασιολογικών Σχέσεων
```cypher
// 3. ΔΗΜΙΟΥΡΓΙΑ ΣΧΕΣΕΩΝ (ΣΥΝΔΕΣΕΙΣ)
CREATE (a1)-[:AUTHORS]->(b1)
CREATE (a1)-[:AUTHORS]->(b2)
CREATE (a2)-[:AUTHORS]->(b3)
CREATE (a2)-[:AUTHORS]->(b4)
CREATE (a3)-[:AUTHORS]->(b5);

// 4. ΕΜΦΑΝΙΣΗ ΟΛΟΚΛΗΡΟΥ ΤΟΥ ΓΡΑΦΟΥ
MATCH (n) RETURN n;
```
**Αποτέλεσμα:**
<img width="833" height="636" alt="Screenshot_8" src="https://github.com/user-attachments/assets/bddd5dc1-ab6b-464e-be82-7bcafd0a3636" />

## 4. Αναζήτηση με βάση συγκεκριμένα κριτήρια
Ζητάμε από το σύστημα να αναλύσει τις κατηγορίες και να βρει αυτόματα ποιοι συγγραφείς έχουν γράψει συγγράμματα σε πάνω από ένα διαφορετικά επιστημονικά πεδία.
```cypher
MATCH (a:Author)-[:AUTHORS]->(b:Book)
WITH a, collect(DISTINCT b.category) AS Fields, collect(b.title) AS Books
WHERE size(Fields) > 1
RETURN a.name AS Author, Fields AS Scientific_Fields, Books AS Book_Titles;
```
**Αποτέλεσμα:** Το σύστημα αποκλείει αυτόματα τον Neil Campbell (αμιγώς βιολόγο) και επιστρέφει τους δύο διεπιστημονικούς συγγραφείς, όπως φαίνεται στον παρακάτω πίνακα:
<img width="1156" height="244" alt="Screenshot_10" src="https://github.com/user-attachments/assets/a58560bb-8fae-4a10-a06d-1b9d3f382cae" />

## 3. Οδηγίες Αναπαραγωγής της Βάσης Δεδομένων

Για να εξετάσετε τον πλήρη γράφο και να εκτελέσετε τα ερωτήματα στον δικό σας υπολογιστή, εισάγετε το έτοιμο αντίγραφο της βάσης (Database Dump).

**Απαιτούμενο Αρχείο:** Κατεβάστε τοπικά το αρχείο με κατάληξη `.dump`

**Βήματα Εισαγωγής (Import):**

1. **Δημιουργία Βάσης:** Στο περιβάλλον του **Neo4j Desktop** (https://neo4j.com/download/), δημιουργήστε μια νέα τοπική βάση (Add -> Local DBMS). 
2. **Εισαγωγή Δεδομένων:** Κάντε κλικ στις τρεις τελείες (`...`) δίπλα στο όνομα της νέας βάσης και επιλέξτε την εντολή **`Import dump`**.
3. **Επιλογή Αρχείου:** Εντοπίστε και επιλέξτε το αρχείο `.dump`.
4. **Εκκίνηση Συστήματος:** Μόλις ολοκληρωθεί επιτυχώς η εισαγωγή, πατήστε το κουμπί **`Start`** για να ξεκινήσει η βάση και έπειτα **`Open`** για να μεταφερθείτε στο περιβάλλον του Neo4j Browser.
5. **Οπτικοποίηση:** Για να εμφανιστεί ολόκληρος ο Knowledge Graph στην οθόνη σας, πληκτρολογήστε και τρέξτε την παρακάτω εντολή:
   
```cypher
   MATCH (n) RETURN n

