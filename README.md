# 2cornot2c
It's a 101 C course for my students.
Sorry, only italian version so far.

## Introduzione

Il corso è fondamentalmente pratico, non è richiesto alcun prerequisito e nulla è dato per scontato.
Prima di iniziare è giusto ricordare che per svolgere i laboratori richiesti è necessaria la conoscenza di alcuni strumenti, in particolare:

* [git](https://git-scm.com/download/win)
* [vagrant](https://developer.hashicorp.com/vagrant/install?ajs_aid=e022a39f-7694-4bed-a4cd-f721f515b885&product_intent=vagrant#windows)

 I link forniti sopra portano alle versioni dei softare per architettura `amd64` in ambiente `windows`, questo a causa dell'assenza di macchine
 linux nei lab scolastici.
 _GIT_ e _VAGRANT_ ci serviranno per ottenere un ambiente di sviluppo identico per tutti e per un provisioning automatico; in altre parole git ci permetterà di condividere il codice dei laboratori e vagrant di condividere la stessa macchina virtuala (`ubuntu-22.04`) con l'ambiente di sviluppo preinstallato.

 ## Installare l'ambiente di sviluppo

I passi seguenti permettono di duplicare sulla tua macchina locale l'ambiente di sviluppo (codice e vm).
Nella directory radice del progetto (`2cornot2c`) troverai una directory `lab` con il codice c per tutti laboratori. 
Questa cartella `2cornot2c\lab` è montata automaticamente sul file system della macchina virtuale nella cartella `/lab`. 
Tutto quello che verrà modificato sulla macchina linux in `lab`(vm o macchina guest) verrà visto sulla macchina windows (host) in `2cornot2c\lab`. 

1) Clone il repository con il codice ed il Vagrantfile

 ```bash
git clone https://github.com/kinderp/2cornot2c.git
```

2) Entra dentro la directory root del repository
   
```bash
cd 2cornot2c
```

3) Avvia la macchina virtuale

```bash
vagrant up
```

4) Apri una sessione ssh sulla macchina appena avviata

```bash
vagrant ssh
```

## Laboratori

All'interno della cartella `/lab` nella macchian Linux troverai il codice su cui lavorare.
Ogni lab ha un numero ed un nome ad esso associato, ad esempio al primo laboratorio è assegnato il numero `0` ed il nome `intro`; questo significa che per questo lab esisterà una cartella `lab/0_intro` che conterrà tutto il codice del lab.
All'interno della cartella del laboratorio troverai dei file sorgente con estensione `.c` anche questi con un numero ed un nome; ad esempio il primo sorgente del lab `0_intro` è `0_hello.c`.
Ogni lab al suo interno contiene una cartella `bin` destinata ad ospitare i file eseguibili ottenuti al termine del processo di compilazione.

### Il processo di compilazione

I programmi sono scriti in un qualche linguaggio di programmazione, il programmatore scrive il codice sorgente sfruttando un particolare linguagggio. Il codice sorgente contiene tutte le istruzioni che il programma dovrà eseguire. Le istruzioni all'interno del codice sorgente scritte in un qualsiasi linguaggio di programmazione devono essere tradotte in una sequenza di bit (in altri termini nel linguaggio macchina) perchè la cpu è in grado di comprendere solo il linguaggio macchina, esclusivamente sequenze di bit e nient'altro. In sintesi si dice che il programma sorgente deve essere trasformato in in file eseguibile (file binario) che contiene le istruzioni (sequenze di bit) per la pecifica architettura del nostro processore.
Questo processo di trasformazione del sorgente in binario è detto processo di compilazione ed è svolto del compilatore. In realtà queto processo è articolato in vari step e non coinvolge solo il compilatore. Vediamo brevemente di studiarne le fasi.
Se non lo hai già fatto avvia la macchina virtuale con `vagrant up` ed al termine del boot avvia una sessione ssh con il comando `vagrant ssh`.
Una volta dentro nella tue home directory (utente vagrant) usa vim per creare un nuovo file in questo modo: `vim hello.c` e copia il codice mostrato sotto:

```c
#include <stdio.h>

int main(void){
    printf("Hello World");
}
```
Salva il contenuto premendo la combinazione: `Esc` + `:wq`.
Compila il sorgente `hello.c` lanciando il seguente comando: `gcc -o hello hello.c`; `gcc` è il compilatore che useremo in questo corso, lo trovi già installato sulla vm. In questo caso l'opzione `-o` specifica il nome del file oggetto (il file binario eseguibile) che vogliamo creare; ovviamente dobbiamo specificare successivamente il sorgente da cui partire per la generazione dell'eseguibile (`hello.c`). Se tutto ha funzionato puoi lanciare il programma appena compilato in questo modo: `./hello`

<p align="center">
<img src="https://github.com/kinderp/2cornot2c/blob/main/images/processo_di_compilazione.png" align="center">
</p>

Nella figura di sopra è mostato l'intero processo di compilazione che come puoi vedere è composto da almeno quattro fasi, come puoi vedere i due parametri passati al compilatore con: `gcc -o hello hello.c` sono ripsettivamente il file di input del processo (`hello.c`) ed il file di output (`hello`) del processo.
Volendo è possibile richiedere al compilatore di fermarsi ad uno specifico step senza produrre l'output finale. Le quattro fasi del processo di compilazione sono rispettivamente:

* Preprocessamento (_Preprocessing_): il preprocessore (`cpp`) esegue tutte le sostituzione di testo richieste secondo le chiamate al preprocessore, il risultato è un file con estensione `.i` nel nostro caso quindi `hello.i`. Per bloccare il processo di compilazione alla fase di preprocessamento puoi eseguire questo comando: `gcc -E hello.c > hello.i`. Il file `hello.i` conterrà tutte le sostituzione effettuate dal preprocessore e come puoi vedere quindi ha molto più contenuto del file di partenza `hello.c`, spiegheremo le chiamate al preprocessore nei prossimi paragrafi.
* Compilazione (_Compilation_): il compilatore (`cc`) trasforma il contenuto testuale del file `hello.i` in codice c nel corrispettivo codice assembly (`hello.s`) specifico per l'architettura del processore
* Assemblaggio (_Assembly_): l'assemblatore (`as`) trasforma il codice assembl contenuto in `hello.s` nelle istruzioni macchina dell'architettura della cpu, il risultato è il file oggetto rilocabile `hello.o`
* Linkaggio (_Linking_): il linker (`ld`) ha il compito di aggreggare in un unico file oggetto (il file eseguibile) eventuali altri file oggetto di librerie esterne o del linguaggio.

### /lab/0_intro

Un programma c è di fatto una collezione di:

* Variabili
* Costanti 
* Funzioni
* Chiamate al preprocessore

Di seguito provvederemo a dare una definizione sommaria per ogni componente sopra citato, rimandiamo ai singoli paragrafi per una trattazione completa.

> [!IMPORTANT]
> Una **variabile** è una locazione di memoria a cui è stato associato un **identificatore** cioè un nome per referenziare nel codice quella cella di memoria
> Una variabile ha un **tipo**; il tipo associato ad una variabile definisce appunto che tipo di dato essa può contenere (un numero intero, un numero reale, un carattere etc.)
> in altre parole il tipo della variabile definisce il numero di byte occupati dalla locazione di memoria referenziata dall'identificatore
> Una variabile può cambiare il valore in essa contenuto durante il ciclo di vita del programma. L'operazione mediante la quale si assegna un valore iniziale ad una variabile è detto **inizializzazione**, l'operazione attraverso cui si associa un nuovo valore ad una variabile già inizializzata è detto **assegnamento**

```c
int var_intera; // variabile non inizializzata
var_intera = 5; // assegnamento
int var_intera_inizializzata = 3; // variabile inizializzata
var_intera_inizializzata = 9; // assegnamento
```

> [!IMPORTANT]
> Per la **costante** valgono le stesse considerazioni fatte per le variabili con l'eccezione che per le costanti non è possibile assegnare un nuovo valore una volta che questa è stata inizializzata

```c
const double pi = 3.14; // costante pi greco
```

> [!IMPORTANT]
> Una funzione è una collezione di istruzioni che svolgono uno specifico compito; una funzione ha un nome, un valore di ritorno, dei parametri di input ed un corpo che è delimitato da una parenti graffa aperta `{` ed una chiusa `}`

```c
int sottrazione(int minuendo, int sottraendo){
    return minuendo - sottraendo;
}
```

> [!IMPORTANT]
> Il preprocessore viene richiamato dal compilatore come primo step nel processo di generazione del file eseguibile che poi verrà avviato dall'utente. Il preprocessore ha il compito di effettuare delle semplici sostituzioni di testo; esistono diverse sostituzioni che il preprocessore può effettuare per contro nostro. L'insieme di queste operazioni sono dette chiamate al preprocessore.

#### 0_hello.c

Come da tradizione, il primo esempio di codice è il classico `Hello World`.
Il programma di sotto stampa a schermo una semplice frase: `Ciao Mondo` in inglese.

https://github.com/kinderp/2cornot2c/blob/18b60e866c1e0e22c59835fe953cbe3c534e7422/lab/0_intro/0_hello.c#L14-L19

