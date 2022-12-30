---
layout: post
title: "[13] Testing e Processi di review"
date: 2022-11-23 14:40:00 +0200
toc: true
---

# Correttezza di un software

La maggior parte dei problemi che si verificano durante lo sviluppo di un progetto sono causati da _problemi di comunicazione_. Ci posso essere incomprensioni quando le informazioni passano da una figura all'altra, come quando ci si interfaccia tra cliente, analista e programmatore.
Per esempio il programmatore dovrà adattare il proprio linguaggio per farsi comprendere dal cliente,
e man mano che si addentra nello sviluppo del progetto sarà necessario prestare maggiore attenzione alla formalità e alla chiarezza della comunicazione.
Più si spiegano le cose in maniera chiara, più è facile che non si incorra in problemi successivi.
Bisogna quindi fare più attenzione alla terminologia che viene usata.

Quando un programma si definisce corretto?

Consideriamo un generico programma $$P$$ come una funzione da un insieme di dati $$D$$ (dominio) a un insieme di dati $$R$$ (codominio):

* $$P(d)$$ indica l'esecuzione di $$P$$ su un dato in ingresso $$d \in D$$

* Il risultato $$P(d)$$ è corretto se soddisfa le specifiche, altrimenti è scorretto

* $$\operatorname{ok}(P, \, d)$$ indicherà la correttezza di $$P$$ per il dato $$d$$

Allora:

$$
P \text{ è } \textit{corretto} \Leftrightarrow \forall d \in D \: \operatorname{ok}(P, \, d)
$$

Cioè, possiamo dire che _un programma è corretto quando per ogni dato del dominio vale $$\operatorname{ok}(P, \, d)$$_,
_La correttezza del programma sarà indicata quindi con $${ok}(P, \, D)$$_.

### Test

_Un **test** è un sottoinsieme del dominio dei dati_ e _un singolo **caso di test** è un elemento di esso_.
Un test sono più stimolazioni, un caso di test è una singola stimolazione.

Per esempio:

* Un test $$T$$ per un programma $$P$$ è un sottoinsieme del dominio $$D$$

* un elemento $$t$$ di un test $$T$$ è detto _caso di test_

* l'esecuzione di un test consiste nell'esecuzione del programma $$\forall t \in T$$

Se ci poniamo dal punto di vista del programma, esso passa o supera un test:

$$
\operatorname{ok}(P, \, T) \Leftrightarrow \forall t \in T | \operatorname{ok}(P, \, t)
$$

Quindi, _un programma è corretto per un test quando per ogni caso di test è corretto_.

Dal punto di vista del test invece, _un test $$T$$ ha successo se rileva uno o più malfunzionamenti presenti nel programma $$P$$_:

$$
\operatorname{successo}(T, \, P) \Leftrightarrow \exists t \in T | \neg\operatorname{ok}(P,\,t) 
$$

### Test ideale

Nel caso in cui il test non rilevi alcun malfunzionamento, non vuol dire che il mio programma sia corretto,
il test è un'attività ottimistica e non garantisce la correttezza del programma.

_Un test $$T$$ è ideale_ per $$P$$ se e solo se $$\operatorname{ok}(P, \, T) \Rightarrow \operatorname{ok}(P, \, D)$$ ovvero _se il superamento del test implica la correttezza del programma_.

_In generale, è **impossibile trovare un test ideale**._

> __Tesi di Dijkstra__:
>
> _Il test di un programma può rilevare la presenza di malfunzionamenti ma non dimostrarne l'assenza._
>
>_Non esiste quindi un algoritmo che dato un programma arbitrario $$P$$ generi un test ideale finito (il caso $$T = D$$ non va considerato)._

Perché è impossibile trovare un test ideale esaustivo? 
Vediamo il seguente esempio:
```java
class Trivial {
    static int sum(int a, int b) {
        return a + b;
    }
}
```



In Java un int è espresso su 32 bit, quindi il dominio ha di cardinalità $$2^{32} \cdot 2^{32} = 2^{64} \sim 2 \cdot 10^{19}$$.
Considerando un tempo di 1 nanosecondo per ogni test, ci dovremmo mettere più di 600 anni.

_Il **test esaustivo** è quindi **impraticabile**._

## Criterio di selezione

Come faccio a scegliere un _sottoinsieme del dominio_ intelligente cercando di approssimare il test ideale? Quali caratteristiche ci interessa che abbia il criterio che usiamo per selezionare questo sottoinsieme?

### Affidabilità
_Un criterio di selezione si dice **affidabile**_ _se presi due test $$T_1$$ e $$T_2$$ in base al criterio $$C$$ allora o entrambi hanno successo o nessuno dei due ha successo_.

$$
\operatorname{affidabile}(C, \, P) \Leftrightarrow [\forall T_1 \in C, \, \forall T_2 \in C \: \operatorname{successo}(T_1, \, P) \Leftrightarrow \operatorname{successo}(T_2, P)]
$$


### Validità
_Un criterio di selezione si dice **valido**_ _se qualora $$P$$ non sia corretto, allora esiste almeno un test $$T$$ selezionato in base al criterio $$C$$ che ha successo e quindi rileva uno o più malfunzionamenti per il programma $$P$$_

$$
\operatorname{valido}(C, \, P) \Leftrightarrow (\lnot \operatorname{ok}(P, \, D) \Rightarrow \exists T \in C \ | \ successo(T,\,P))
$$

__Esempio__: dato il codice
```java
public class Esempio {
    
    static int raddoppia(int par) {
            int risultato;
            risultato = (par * par);
            return risultato;
    }
    
}
```
Un criterio che seleziona:

* _"sottoinsiemi di $$\{0, \, 2\}$$"_ è **affidabile**, perché il programma funziona sia con 0 sia con 2, ma **non valido**, perché sappiamo che il programma non è corretto e non viene trovato nessun malfunzionamento.

* _"i sottoinsiemi di $$\{0, \, 1, \, 2, \, 3, \, 4\}$$"_ è **non affidabile**, perché i risultati dei casi di test non sono tutti coerenti, ma **valido** perché rileva i malfunzionamenti.

* _"sottoinsieme finiti di $$D$$ con almeno un valore maggiore di $$18$$"_ è **affidabile**, perché i risultati dei casi di test sono tutti coerenti, e **valido** perché rileva i malfunzionamenti.

Quello che vorremmo è scegliere un criterio affidabile e valido, in astratto, senza conoscerne i malfunzionamenti. Se un criterio viene definito conoscendo già i malfunzionamenti non ha senso.
Ma se il test non ha successo, ci è utile avere un criterio valido e affidabile?
- Il fatto che il test non abbia avuto successo implica che non ci siano stati errori
- Il fatto che il criterio sia affidabile implica che tutti gli altri test che possiamo trovare per quel criterio non trovo errori
- Il fatto che il criterio sia valido, se ci fosse stato un errore almeno uno dei test lo avrebbe dovuto trovare

Quindi siamo __sicuri__ che il programma è corretto:
$$
\operatorname{affidabile}(C,P) \wedge \operatorname{valido}(C,P) \wedge T \in C \wedge \neg\operatorname{successo}(T,P)
$$
$$
\Longrightarrow
$$
$$
\operatorname{ok}(P,D)
$$

Ma trovare un criterio che sia _contemporaneamente_ affidabile e valido significherebbe trovare un criterio che selezioni _test ideali_ che sappiamo non esistere per la _tesi di Dijkstra_

### Utilità di un test

Quali sono le caratteristiche che rendono utile un caso di test, ovvero che rendono "possibile" o "probabile" che il caso di test mi trovi l'errore?

- Eseguire il comando che contiene l'anomalia (altrimenti non è possibile che il malfunzionamento si manifesti)
- L'esecuzione del comando che contiene l'anomalia deve portare il sistema in uno stato inconsistente
- Lo stato inconsistente dell'output deve propagarsi fino all'uscita del codice in esame in modo da produrre un output diverso da quello atteso

Possiamo utilizzare un metro di misura legato alle caratteristiche del codice: _a ogni criterio è possibile associare una **metrica**_ che misuri la _copertura del codice rispetto a uno specifico test_ (ovvero la percentuale di codice che vado ad "utilizzare" in tutto il test) e che _ci permetta di decidere quando smettere di testare_, decidere _quali altri casi di test è opportuno aggiungere_ o _confrontare la bontà di test diversi_.

### Criteri noti

#### Criterio di copertura dei comandi

_Un test $$T$$ soddisfa il criterio di __copertura dei comandi__ se e solo se ogni comando eseguibile del programma è eseguito in corrispondenza di almeno un caso di test $$t \in T$$._

Per esempio:
```c
void main(){
    float x,y;
    read(x);
    read(y);
    if (x!=0)
        x = x+10;
    y = y/x;
    write(x);
    write(y);
}
```

Posso ricostruire un diagramma di flusso di esecuzione del codice trasformando ogni comando in un nodo del diagramma:

{% responsive_image path: 'assets/13_flowChart.png' %}

Dire che voglio _coprire tutti i comandi_, avendo trasformato ogni comando in un nodo, significa voler passare per tutti i nodi raggiungibili.

Applicare il _criterio di copertura dei comandi_ significa quindi trovare un insieme dei casi di test per il cui per ogni nodo esiste un caso di test che passa per quel nodo.

Il caso di test $$<3,7>$$ risulterebbe quindi sufficiente, dato che soddisfa il criterio di copertura dei comandi al 100%

Questo però _non mi garantisce che il programma si corretto,_ perché ci sono dei malfunzionamenti che non sono stati trovati, ad esempio il caso di testing $$<0,7>$$ che provoca una divisione per 0.


#### Criterio di copertura delle decisioni

_Un test T soddisfa il criterio di copertura delle decisioni se e solo se ogni decisione effettiva viene resa sia vera che falsa in corrispondenza di almeno un caso di test t contenuto in T_

La metrica è la percentuale delle decisioni totali possibili presenti nel codice che sono state rese sia vere che false nel test.

Si noti come il criterio di copertura delle decisioni implichi il criterio di copertura dei comandi: andando a estrarre il codice in un diagramma di flusso, io copro tutte le decisioni se e solo se attraverso ogni arco presente nel flusso. Considerando un grafo connesso per il diagramma di flusso, se io attraverso tutti gli archi allora ho attraversato tutti i possibili nodi. Non è invece vero l'inverso.

Per esempio:

Riprendendo l'esempio precedente, se si volesse applicare il criterio di copertura delle decisioni si dovrebbero utilizzare almeno due casi di test, ad esempio $$<3,7>$$ e $$<0,5>$$, che se compresi nello stesso test restituirebbero una copertura delle decisioni pari al 100%.

Ma non tutti i malfunzionamenti vengono trovati, ad esempio a riga 6 è possibile che ad x sia assegnato un valore tale per cui se sommo 10 ottengo un overflow.

#### Criterio di copertura delle condizioni

_Un test T soddisfa il criterio di copertura delle condizioni se e solo se ogni singola condizione (effettiva) viene resa sia vera che falsa in corrispondenza di almeno un caso di test t contenuto in T_

Similmente ai test precedenti, la metrica è la percentuale delle condizioni che sono state rese sia vere che false su quelle per cui è possibile farlo.

Si noti come questo criterio non implichi i criteri precedenti

Per esempio:


Modifichiamo un po' l'esempio usato fino ad'ora:

```c
void main(){
    float x,y;
    read(x);
    read(y);
    if (x!=0 || y>0)
        y = y/x;
    else
        y = (y+2)/x
    y = y/x;
    write(x);
    write(y);
}
```

{% responsive_image path: 'assets/13_flowChart-2.png' %}


Prendendo come Test i casi $$<0,5>$$ e $$<5,-5>$$ soddisfo al 100% il criterio di copertura delle condizioni (`x!=0` è falsificato da $$<0,5>$$ e verificato da $$<5,-5>$$, mentre `y>0` è verificato da $$<0,5>$$ e falsificato da $$<5,-5>$$), ma la decisione è sempre vera.

Ci sono quindi anomalie sia alla riga 6 (possibile divisione per 0) che alla riga 8(Overflow e divisione per 0), ma quelle alla riga 8 non verrebbero scoperte dato che non viene coperta.

#### Criterio di copertura delle decisioni e condizioni

È intuitivamente l'intersezione del Criterio di copertura delle decisioni con quello di copertura delle condizioni.

#### Criterio di copertura delle condizioni composte

_Un test T soddisfa il criterio di copertura delle condizioni composte se e solo se ogni possibile composizione delle condizioni base vale sia vero che falso per diversi casi di test in T_

Si noti come questo criterio comporti il precedente (Criterio di copertura delle decisioni e condizioni)

Data la natura combinatoria di questo criterio, all'aumento del numero di condizioni di base _il numero di casi di test cresce troppo rapidamente_. Inoltre dato che le condizioni di base potrebbero non risultare indipendenti tra loro, potrebbero esistere combinazioni non fattibili che non sarebbe opportuno testare.

#### Criterio di copertura delle condizioni e delle decisioni modificate

Ci si è accorti che certe combinazioni sono "più vicine o più lontane ad altre": se modificando una sola condizione base riesco a modificare la decisione, allora quella modifica è una modifica molto significativa indipendentemente da quanto sia grande. Se invece la decisione rimane la stessa, posso ipotizzare che quella modifica sia più neutra e meno significativa.

Si va quindi a dare importanza, nella selezione delle combinazioni, al fatto che la modifica di una singola condizione base porti a modificare la decisione. Per ogni condizione base devono quindi esistere 2 casi di test che modificano il valore di una sola condizione base e che modificano il valore della decisione

È inoltre dimostrabile che se si hanno $$N$$ condizioni base sono sufficienti $$N+1$$ casi di test per coprire il criterio.

#### Implicazioni tra criteri di copertura

<!--- immagine implicazioni tra criteri di copertura --->

Il criterio delle condizioni composte va considerato troppo oneroso e quindi non applicabile. Gli altri criteri invece possono essere applicati.

### Altri criteri

_I criteri visti finora non considerano i **cicli**_ e possono essere soddisfatti da test che percorrono ogni ciclo al più una volta. 

Molti errori però si verificano durante iterazioni successive alla prima, come per esempio quando si superano i limiti di un array.

Occorre quindi un criterio che tenga conto anche delle iterazioni.

#### Criterio di copertura dei cammini

_Un test T soddisfa il criterio di copertura dei cammini se e solo se ogni cammino del grafo di controllo del programma viene percorso per almeno un caso di test in T._

La metrica è il rapporto tra i cammini percorsi e quelli effettivamente percorribili.

Molto generale, ma spesso impraticabile (anche per programmi semplici).

Consideriamo quindi questo criterio **non applicabile**.

#### Criterio di n-copertura dei cicli

_Un test soddisfa il criterio di $$n$$-copertura se e solo se ogni cammino del
grafo contenente al massimo un numero d'iterazioni di ogni ciclo non
superiore a $$n$$ viene percorso per almeno un caso di test._

Il che non significa che il mio test deve eseguire $$n$$ volte un ciclo, ma significa che per ogni numero compreso tra 0 e $$n$$ ci deve essere un caso di test che esegue quel ciclo $$n$$ volte. Si sta quindi limitando il numero massimo di percorrenze dei cicli.

Di conseguenza però al crescere di $$n$$ il numero di test aumenta molto rapidamente.

Inoltre fissare questa variabile a livello di programma può non essere un'azione così semplice, il numero d'iterazioni che necessita un ciclo per essere testato può essere molto differente tra cicli diversi.

Per cercare di minimizzare il numero di test, può bastare coprire solamente le casistiche: zero iterazioni, un'iterazione e molte iterazioni.

Il caso $$n = 2$$ è il minimo per considerare queste casistiche.
Se $$n = 1$$ un ciclo (`while`) sarebbe stato indistinguibile da una semplice
selezione (`if`), testando due iterazioni incomincio a testare le caratteristiche del ciclo.

A differenza del criterio di copertura dei cammini, questo lo consideriamo **applicabile**.

## Analisi statica

_Si basa sull'esame di un insieme finito di elementi (le istruzioni del programma) contrariamente all'analisi dinamica (insieme degli stati delle esecuzioni)._
È un'attività meno costosa del testing, poiché non soffre del problema della "esplosione dello spazio degli stati". 
Non può rilevare anomalie dipendenti da uno specifico valore assunto a run-time, perché non ragione sui valori specifici delle variabili.

### Complilatori

I compilatori fanno analisi statica per fornire un eseguibile e per identificare errori sintattici nel codice.
Il lavoro dei compilatori si può dividere in quattro fasi:
* **Analisi lessicale**: viene fatta l'identificazione dei token del linguaggio, permette d'identificare la presenza di simboli non appartenenti al linguaggio.
* **Analisi sintattica**: identifica le relazioni tra token e controlla la conformità del codice alla grammatica del linguaggio.
* **Controllo dei tipi**: si cercano d'individuare violazioni delle regole d'uso dei tipi
* **Analisi flusso dei dati**: si cercano di rilevare problemi relativi alle evoluzioni dei valori associati alle variabili

### Analisi Data Flow

I primi utilizzi sono stati fatti nel campo dell'ottimizzazione dei compilatori.

Il flusso dei dati sarebbe una analisi prettamente dinamica, ma il sottoinsieme dei controlli statici è significativo.

Staticamente è possibile identificare il tipo di operazione che un comando esegue su una variabile:

* **d** $$\Rightarrow$$ **definizione** se il comando assegna un valore alla variabile.
* **u** $$\Rightarrow$$ **uso** se il comando richiede il valore di una variabile.
* **a** $$\Rightarrow$$ **annullamento** se al termine dell'esecuzione dell'istruzione il valore della variabile non è significativo/affidabile.

È possibile ridurre una sequenza d'istruzioni a una sequenza di _**d**efinizioni_, _**u**si_ e _**a**nnullamenti_.

Questo viene fatto per cercare di capire se ci sono delle situazioni anomale, con una serie di regole:

* L'_uso_ di una variabile deve essere sempre preceduto in ogni sequenza da una _definizione_ senza _annullamenti_ intermedi $$\Rightarrow$$ **au** ERR

* La _definizione_ di una variabile deve sempre essere seguita da un _uso_ prima di un suo _annullamento_ o _definizione_ $$\Rightarrow$$ **da** e **dd** ERR

* L'_annullamento_ di una variabile deve essere sempre seguito da una _definizione_ prima di un _uso_ o di un altro _annullamento_ $$\Rightarrow$$ **aa** ERR

**au**, **da**, **dd** e **aa** sono sequenze che identificano **situazioni anomale**.

Esempio _analisi data flow_:

```c
void swap(int &x1, int &x2) {
    int x;
    x2 = x;
    x2 = x1;
    x1 = x;
}
```
Le sequenze per ogni variabile sono le seguenti:

* `x` $$\Rightarrow$$ **auu**a

* `x1` $$\Rightarrow$$ ...dud...

* `x2` $$\Rightarrow$$ ...**ddd**...

Anomalie rilevabili:

* `x` viene usata senza (2 volte) senza essere stata prima definita

* `x1` nessuna anomalia

* `x2` viene definita più volte senza essere usata nel frattempo

#### Sequenze Data Flow

```c
1   void main() {
2       float a, b, x, y;
3       read(x);
4       read(y);
5       a = x;
6       b = y;
7       while(a != b) {
8           if(a > b) {
9               a = a - b;
10          } else {
11              b = b - a;
12          }
13      }
14      write(a);
15  }
```

$$\operatorname{P}(p,a)$$ indica la **sequenza** ottenuta per la variabile $$a$$ eseguendo il cammino $$p$$.

Es. $$\operatorname{P}([1, 2, 3, 4, 5, 6, 7, 8, 9, 7, 12, 13], a) = a2, d5, u7, u8, u9, d9, u7, u12,  a13$$

Eseguendo questo tipo di operazione su tutte le variabili si potrebbero verificare le eventuali anomalie. Il problema è che per fare ciò bisognerebbe simulare l'esecuzione del programma e questo significherebbe uscire dal campo dell'analisi statica.

Quindi quello che si può fare è usare **espressioni regolari** per rappresentare $$P$$ in caso di cammini contenenti _cicli_ e _decisioni_.

Es. $$\operatorname{P}([1 →], a)$$ significa rappresentare tutti i cammini che partono dall'istruzione $$1$$ per la variabile $$a$$: 

<!--- 
a2  d5

a2  d5  u7  (        while-body        )*  u12  a13

a2  d5  u7  (       u8  if-body        )*  u12  a13

a2  d5  u7  (  u8  (u9  d9 | u11)  u7  )*  u12  a13
--->

Rappresentando la sequenza in questo modo è possibile identificare eventuali anomalie anche nel caso di cicli e decisioni.

### Analisi statica e Testing

Ma cosa c'entra l'**analisi statica** con il **testing**?

L'_analisi statica_ può aiutare a _selezionare i casi di test_ basandosi sulle _sequenze_ _definizione_-_uso_ delle variabili.

Per esempio:

* perché si presenti un malfunzionamento dovuto a una anomalia in una _definizione_, deve essere _usato_ il valore che è stato assegnato

* un ciclo dovrebbe essere ripetuto (di nuovo) se verrà _usato_ un valore _definito_ alla iterazione precedente

### Definizioni e altri criteri

$$\operatorname{def}(i)$$ è l'insieme delle variabili che sono definite in $$i$$

$$\operatorname{du}(i, x)$$ è l'insieme dei nodi j tali che:

* $$x$$ $$\in \operatorname{def}(i)$$

* $$x$$ usato in $$j$$

* esiste un cammino da $$i$$ a $$j$$, libero da definizione di $$x$$

#### Criterio di copertura delle definizioni

_Un test $$T$$ soddisfa il criterio di copertura delle definizioni se e solo se per ogni nodo $$i$$ e ogni variabile $$x$$, appartenente a $$\operatorname{def}(i)$$, $$T$$ include un caso di test che esegue un cammino libero da definizioni da $$i$$ ad almeno uno degli elementi di $$\operatorname{du}(i, x)$$_

$$T \in C$$ sse $$ \forall i \in P$$ $$\forall x \in \operatorname{def}(i)$$ $$\exists j \in \operatorname{du}(i, x)$$ $$\exists t \in T$$ che esegue un cammino $$i$$ a $$j$$ senza ulteriori definizioni di $$x$$

Esempio:

```c
1   void main() {
2       float a, b, x, y;
3       read(x);
4       read(y);
5       a = x;
6       b = y;
7       while(a != b) {
8           if(a > b) {
9               a = a - b;
10          } else {
11              b = b - a;
12          }
13      }
14      write(a);
15  }
```

Ad esempio consideriamo la variabile $$a$$:

Due definizioni: 
* $$\operatorname{def}(5)$$ = $$\{a\}$$
* $$\operatorname{def}(9)$$ = $$\{a\}$$

$$\operatorname{du}(5, a)$$ $$=$$ $$\{7, 8, 9, 11, 12\}$$

$$\operatorname{du}(9, a)$$ $$=$$ $$\{7, 8, 9, 11, 12\}$$

Sia la definizione data alla riga $$5$$ che quella data alla riga $$9$$ possono essere usate alla riga $$7, 8, 9, 11, 12$$.

Si vuole controllare che per ognuna delle definizioni si abbia un uso di quella definizione

$$d5$$ $$u7$$ viene gratis

$$d9$$ $$u7$$ basta entrare una volta nel ciclo

$$T$$ = $${ <8, 4> }$$ è un caso di test che soddisfa il criterio.

Il criterio di copertura delle definizioni non copre tutti i comandi e di conseguenza non implica il criterio di copertura dei comandi.

#### Criterio di copertura degli usi

_Un test $$T$$ soddisfa il criterio di copertura degli usi se e solo se per ogni nodo $$i$$ e ogni variabile $$x$$, appartenente a $$\operatorname{def}(i)$$, $$T$$ include un caso di test che esegue un cammino libero da definizioni da $$i$$ ad **ogni elemento** di $$\operatorname{du}(i, x)$$_

$$T \in C$$ sse $$ \forall i \in P$$ $$\forall x \in \operatorname{def}(i)$$ $$\forall j \in \operatorname{du}(i, x)$$ $$\exists t \in T$$ che esegue un cammino $$i$$ a $$j$$ senza ulteriori definizioni di $$x$$

Cioè, per ogni definizione di una variabile, tutti i possibili usi di quella definizione devono essere coperti.

Anche questo criterio non copre i comandi.

Esempio:

```c
1   void main() {
2       int a, b, c;
3       read(a);
4       read(b);
5       read(c);
6       if(a > b) {
7           if(b > c) {
8               write(a);
9           } else {
10              write(b);
11          }
12      } else {
13          if(a > c) {
14              write(a);
15          } else {
16              write(c);
17          }
18      }
19  }
```

Consideriamo la variabile $$a$$:

$$\operatorname{du}(5, a)$$ = $$\{7, 8, 9, 11, 12\}$$ e $$\operatorname{du}(9, a)$$ = $$\{7, 8, 9, 11, 12\}$$

$$d5$$ $$u7$$ $$u8$$ $$u11$$ $$u7$$ $$u12$$ 

...$$d5$$ $$u7$$ $$u8$$ $$u9$$...

...$$d9$$ $$u7$$ $$u8$$ $$u9$$...

...$$d9$$ $$u7$$ $$u8$$ $$u12$$...

...$$d9$$ $$u7$$ $$u8$$ $$u11$$...

almeno due iterazioni ad esempio:

$$T$$ = $${ <4, 8>, <12, 8>, <12, 4> }$$

#### Criterio di copertura dei cammini DU

Esistono diversi cammini che soddisfano il criterio precedente.
Questo criterio richiede che siano selezionati tutti.

$$T \in C$$ sse $$ \forall i \in P$$ $$\forall x \in \operatorname{def}(i)$$ $$\forall j \in \operatorname{du}(i, x)$$ per ogni cammino da $$i$$ a $$j$$ senza ulteriori definizioni di $$x$$, esiste un caso di test in $$T$$ che esegue quel cammino.

Criterio **utile da ipotizzar**e, ma **impraticabile**.

#### Beebugging

Il problema è che può capitare che non si trovino errori, ciò può dipendere dal fatto che il programma è corretto oppure dal fatto che chi sta testando non lo sta facendo nella maniera corretta.
Per risolvere questo problema si può usare il beebugging, che consiste nel inserire deliberatamente $$n$$ errori dentro il codice prima di mandare il programma a chi lo deve testare. 
Questo è un incentivo per il team di testing, perché sa che ci sono degli errori e deve solo trovarli. La metrica che viene utilizzata è la percentuale di errori trovati.

_Quello che si cerca di fare è di trovare tutti gli errori inseriti per cercare nel frattempo di trovarne di nuovi._ 

#### Analisi mutazionale

Partendo da un programma e un test vengono aggiunti degli errori al programma per verificare se il test li trova.
Vengono generate delle mutazioni, 
per esempio, 
viene generato un insieme di programmi $$II$$ _simili_ al programma $$P$$ in esame e su di essi viene eseguito lo stesso test $$T$$ previsto per il programma $$P$$.

Cosa ci si aspetta?
* se $$P$$ è corretto allora i programmi in $$II$$ devono essere sbagliati
* per almeno un caso di test devono quindi produrre un risultato diverso

_Un test $$T$$ soddisfa il criterio di copertura dei mutanti se e solo se per ogni mutante $$π \in II$$ esiste almeno un caso di test in $$T$$ la cui esecuzione produca per $$π$$ un risultato diverso da quello prodotto da $$P$$._

La metrica è la frazione di mutanti riconosciuta come diversa da $$P$$ sul totale di mutanti generati.

Quello che deve essere fatto è l'analisi delle classi e la generazioni dei mutanti, successivamente la selezione dei casi di test e infine l'esecuzione.

Nel caso ideale si vorrebbero avere dei mutanti simili al programma di partenza, con delle differenze minime, cioè, praticamente identici dappertutto tranne dove sono stati aggiunti gli errori.
I mutanti sono virtualmente infiniti, ma facili da automatizzare.

Gli **operatori mutanti** sono delle funzioni che, dato $$P$$, generano uno o più mutanti. I più semplici effettuano modifiche sintattiche che comportino modifiche semantiche, ma non errori sintattici bloccati in compilazione.

Nella variante **HOM** _(Hing Order Mutation)_ può venire fatta più di una modifica e a volte sono più difficili da identificare le modifiche prese singolarmente.

Tra le **classi di operatori** si distinguono rispetto all'oggetto su cui operano:

* _costanti_, _variabili_, es. scambiando l'occorrenza di una con l'altra
* _operatori_ ed _espressioni_, es. `<` in `<=`, oppure `true` in `false`
* _comandi_, es. un `while` in `if`

Vediamo con uno schema come ci si può comportare per capire quali sono i problemi:
{% responsive_image path: 'assets/13_analisi-mutazionale-schema.png' %}

Il problema di questo approccio è che non garantisce la terminazione, ovvero non c'è un modo sicuro per stabilire quando il processo di analisi è completato. Ciò è dovuto a diversi fattori:
- Quando si estrae un valore casuale, c'è sempre il rischio di estrarre sempre lo stesso valore.
- Si potrebbe essere particolarmente sfortunati e non trovare il valore corretto.
- Esistono infinite varianti di programmi che svolgono la stessa funzione, anche se non sono sintatticamente identici. Di conseguenza, una modifica sintattica potrebbe non avere alcun effetto sul funzionamento del programma.

Per verificare se il test è valido, è necessario controllare il numero di mutanti generati. Se questo numero è elevato, il test non è affidabile. In alternativa, è possibile "nascondere" i mutanti, a patto che non sia richiesta una copertura totale. In questo modo, è possibile analizzare programmi che sono funzionalmente uguali ma sintatticamente diversi, al fine di dimostrare l'equivalenza o scoprire casi in cui essa non è valida.

#### Analis mutazionale in un linguaggio OO

In un linguaggio OOP, l'analisi mutazionale viene spesso utilizzata per verificare la robustezza e la qualità delle classi e degli oggetti definiti nel codice sorgente. Ad esempio, si potrebbero apportare modifiche alle proprietà o ai metodi di una classe e verificare come queste modifiche influiscono sugli oggetti creati a partire da questa classe. Oppure, si potrebbe modificare il comportamento di un oggetto in modo da verificare se questo influisce sulla correttezza del programma nel suo insieme.

Inoltre, l'analisi mutazionale può essere utilizzata per verificare la robustezza delle relazioni di ereditarietà e polimorfismo all'interno di un programma OOP. Ad esempio, si potrebbero apportare modifiche alle proprietà o ai metodi di una superclasse e verificare come queste modifiche influiscono sulla correttezza delle sottoclassi che ne ereditano le proprietà e i metodi. Oppure, si potrebbe modificare il comportamento di un oggetto polimorfo in modo da verificare se questo influisce sulla correttezza del programma nel suo insieme.

### OO testing e ereditarietà

Il testing di oggetti (OO testing) è un tipo di testing specifico per i programmi scritti utilizzando i principi della programmazione a oggetti (OOP). Si concentra sulla verifica della correttezza del comportamento degli oggetti e delle loro interazioni all'interno del programma.

Per eseguire il testing di oggetti, è spesso necessario creare dei casi di test che simulino determinate situazioni e interazioni tra gli oggetti. Ad esempio, si potrebbero creare casi di test che verifichino il comportamento di un oggetto quando viene invocato uno dei suoi metodi, o che verifichino la correttezza delle relazioni di ereditarietà tra le diverse classi del programma.

L'ereditarietà è un concetto chiave della programmazione a oggetti, che permette di creare una gerarchia di classi e di ereditare proprietà e metodi da una classe base (chiamata superclasse) a una classe derivata (chiamata sottoclasse). In questo modo, è possibile definire un comportamento comune per un gruppo di classi e personalizzarlo per ogni classe specifica.

Durante il testing di oggetti, è importante verificare la correttezza delle relazioni di ereditarietà all'interno del programma. Ad esempio, si potrebbero creare casi di test che verifichino se gli oggetti di una sottoclasse ereditano correttamente le proprietà e i metodi della superclasse, o che verifichino se il comportamento della sottoclasse è coerente con quello della superclasse. Inoltre, è importante verificare che le modifiche apportate alla superclasse non abbiano effetti indesiderati sulle sottoclassi.

### OO testing e collegamento dinamico

Il collegamento dinamico è un concetto legato alla programmazione a oggetti che si riferisce alla capacità di un programma di decidere quali metodi o proprietà utilizzare in base al tipo di oggetto con cui si lavora.

In un linguaggio OOP, il collegamento dinamico viene solitamente implementato attraverso il polimorfismo, cioè la capacità di un oggetto di assumere diverse forme e di essere trattato come un'istanza di diverse classi a seconda del contesto. Ad esempio, se un oggetto è un'istanza di una sottoclasse di una determinata superclasse, può essere trattato come un'istanza della superclasse o della sottoclasse a seconda del contesto.

Durante il testing di oggetti, è importante verificare che il collegamento dinamico funzioni correttamente. Ad esempio, si potrebbero creare casi di test che verifichino se un oggetto polimorfo viene trattato come un'istanza della superclasse o della sottoclasse corretta a seconda del contesto, o che verifichino se gli oggetti di diverse classi possono essere utilizzati in modo intercambiabile in determinate situazioni. Inoltre, è importante verificare che le modifiche apportate a una classe non abbiano effetti indesiderati sugli oggetti di altre classi che ne ereditano le proprietà o i metodi.

### Class testing

Il testing di oggetti è una tecnica utilizzata per verificare la correttezza del comportamento degli oggetti e delle loro interazioni all'interno di un programma scritto utilizzando i principi della programmazione a oggetti (OOP).

Una delle strategie utilizzate in questo tipo di testing è la costruzione di classi stub e driver. Una classe stub è una classe fittizia che viene utilizzata per sostituire una classe reale in un determinato contesto di test. Ad esempio, si potrebbe utilizzare una classe stub per sostituire una classe che dipende da una risorsa esterna, come un database o un servizio web, in modo da poter eseguire il test in modo indipendente dal contesto esterno.

Una volta costruita la classe stub, è importante implementare eventuali metodi astratti (cioè metodi che devono essere definiti dalle sottoclassi ma non hanno un comportamento predefinito nella superclasse). In questo modo, sarà possibile utilizzare la classe stub per eseguire il test senza che sorga un'eccezione per la mancanza di implementazione dei metodi astratti.

Inoltre, può essere utile aggiungere una funzione che permetta di estrarre e esaminare lo stato dell'oggetto, al fine di bypassare l'incapsulamento (cioè la protezione delle proprietà e dei metodi di un oggetto da accessi esterni non autorizzati). Questa funzione può essere utilizzata per verificare che l'oggetto si comporti in modo coerente durante il test.

Infine, si può creare una classe driver che permetta di istanziare oggetti e di chiamare i loro metodi secondo il criterio di copertura scelto (ad esempio, per verificare che tutti i metodi vengano eseguiti almeno una volta durante il test). In questo modo, sarà possibile eseguire il test in modo automatizzato e riprodurre facilmente i risultati.

### Copertura della classe

La copertura della classe è un concetto legato al testing di oggetti che si riferisce alla quantità di codice di una classe che viene eseguita durante un test. Più in generale, la copertura di un programma indica quante parti del codice vengono eseguite durante i test.

Per determinare la copertura di una classe, è importante considerare lo stato dell'oggetto. Lo stato dell'oggetto è l'insieme delle proprietà e dei valori che assumono in un determinato momento. A seconda dello stato dell'oggetto, alcuni metodi potrebbero essere eseguiti o meno, o potrebbero avere comportamenti differenti.

Una volta definito lo stato dell'oggetto, è possibile definire in modo "statico" cosa è lo stato dell'oggetto. Ad esempio, si potrebbero considerare come stati dell'oggetto i valori di alcune proprietà, o il risultato di alcuni metodi. In questo modo, sarà possibile determinare quali casi di test sono necessari per coprire tutti gli stati dell'oggetto.

In alcuni casi, potrebbe esistere una rappresentazione dello stato dell'oggetto come una macchina a stati, che descrive gli stati possibili dell'oggetto e le transizioni tra questi stati. In questo modo, sarà possibile determinare quali casi di test sono necessari per coprire tutte le transizioni possibili.

In generale, il testing di oggetti mira a coprire il maggior numero possibile di stati dell'oggetto e di transizioni tra questi stati, al fine di garantire la correttezza del comportamento dell'oggetto e delle sue interazioni all'interno del programma. Tuttavia, è importante notare che la copertura completa potrebbe non essere sempre possibile o auspicabile, a causa della complessità del programma o dei costi associati al testing. Di conseguenza, è importante valutare attentamente quali casi di test sono necessari per garantire una copertura adeguata della classe senza essere troppo onerosi da un punto di vista del tempo o dei costi.

Inoltre, è importante tenere presente che la copertura della classe non è l'unico criterio per valutare la qualità del testing. Ad esempio, è importante anche verificare che i casi di test siano indipendenti l'uno dall'altro e che il codice sia testato in modo adeguato anche in situazioni di errore o di eccezione. Inoltre, è importante considerare anche altre metriche, come la qualità del codice o la robustezza del programma, per valutare la qualità del testing nel suo insieme.

### Criteri di copertura

I criteri di copertura sono delle strategie utilizzate per determinare quali parti di un programma devono essere testate. In particolare, i criteri di copertura possono essere utilizzati per definire quali casi di test sono necessari per coprire il maggior numero possibile di stati dell'oggetto e di transizioni tra questi stati.
{% responsive_image path: 'assets/13_criteri-copertura-grafo.png' %}

In riferimento a un diagramma, i criteri di copertura possono includere:

- Coprire tutti i nodi: questo criterio prevede di creare un caso di test per ogni nodo del diagramma, al fine di verificare che tutte le parti del codice siano eseguite almeno una volta;
- Coprire tutti gli archi: questo criterio prevede di creare un caso di test per ogni arco del diagramma, al fine di verificare che tutte le transizioni tra gli stati dell'oggetto siano eseguite almeno una volta;
- Coprire tutte le coppie di archi in/out: questo criterio prevede di creare un caso di test per ogni coppia di archi in/out (cioè per ogni arco che entra in un nodo e per ogni arco che esce da un nodo). In questo modo, si potranno verificare tutte le possibili combinazioni di transizioni tra gli stati dell'oggetto;
- Coprire tutti i cammini identificabili nel grafo: questo criterio prevede di creare un caso di test per ogni cammino identificabile nel grafo, cioè per ogni sequenza di archi che porta da un nodo ad un altro. In questo modo, si potranno verificare tutte le possibili combinazioni di transizioni tra gli stati dell'oggetto.

In generale, i criteri di copertura più completi sono anche quelli che richiedono più tempo e risorse per essere implementati, pertanto è importante valutare attentamente quali criteri di copertura sono più adeguati per il proprio programma e per il proprio obiettivo di testing.

#### Tipi di test: white o black box

I tipi di test possono essere classificati in base alla conoscenza che si ha del sistema che si sta testando. I due tipi principali di test sono i test white box e i test black box.

I test white box, anche noti come test di integrazione o di glass box, sono dei test in cui il tester ha conoscenza del funzionamento interno del sistema che si sta testando. In questo tipo di test, il tester può utilizzare questa conoscenza per creare casi di test che verifichino il comportamento del sistema in modo più accurato e dettagliato.

I test black box, invece, sono dei test in cui il tester non ha conoscenza del funzionamento interno del sistema che si sta testando. In questo tipo di test, il tester si basa solo sull'interfaccia del sistema e sui requisiti specificati per creare i casi di test. I test black box sono più adatti per verificare il comportamento del sistema dal punto di vista dell'utente finale, senza dover considerare il funzionamento interno del sistema.

In generale, entrambi i tipi di test sono importanti e possono essere utilizzati in modo complementare per garantire la qualità di un sistema. Ad esempio, i test white box possono essere utilizzati per verificare il funzionamento interno del sistema, mentre i test black box possono essere utilizzati per verificare il comportamento del sistema dal punto di vista dell'utente finale.

### Test funzionale

Il test funzionale è un tipo di test che si concentra sulla verifica del comportamento del sistema dal punto di vista dell'utente finale, senza considerare il funzionamento interno del sistema. In altre parole, il test funzionale è un approccio black box, in cui non si ha conoscenza del codice sorgente del sistema che si sta testando.

Uno dei principali vantaggi del test funzionale è che può essere l'unico approccio possibile in alcune situazioni, ad esempio quando si sta testando il lavoro di un fornitore esterno. In questo caso, non si ha accesso al codice sorgente e si deve quindi basare il test solo sull'interfaccia del sistema e sui requisiti specificati.

I dati di test per il test funzionale possono essere derivati dalle specifiche del sistema, cioè dai requisiti funzionali che descrivono il comportamento atteso del sistema. In questo modo, si può concentrare l'attenzione sul dominio delle informazioni invece che sulla struttura di controllo del codice.

Il test funzionale permette di identificare errori che non possono essere individuati con criteri strutturali, come funzionalità non implementate o flussi di esecuzione dimenticati. Inoltre, il test funzionale si pone come obiettivo anche di trovare errori di interfaccia e di prestazioni del sistema.

In generale, il test funzionale è un tipo di test molto utile per verificare il comportamento del sistema dal punto di vista dell'utente finale e per individuare errori che potrebbero altrimenti essere trascurati. Tuttavia, è importante notare che il test funzionale non garantisce la correttezza del codice sorgente del sistema e può quindi essere utilizzato in modo complementare ad altri tipi di test, come i test strutturali o i test di integrazione, per garantire la qualità del sistema nel suo insieme.

#### Tecniche di testing

Ci sono diverse tecniche di testing che possono essere utilizzate per verificare la qualità di un sistema. Ecco una panoramica di alcune tecniche comunemente utilizzate:

- Metodi basati sui grafi: come abbiamo già visto, i metodi basati sui grafi si basano sulla rappresentazione grafica delle sequenze di istruzioni e delle decisioni presenti nel programma. Utilizzando questi grafi, è possibile individuare i punti del programma che richiedono maggiore attenzione durante il testing e creare casi di test per coprire questi punti. Ad esempio, la tecnica _BUGS_ è un metodo basato sui grafi;
- Suddivisioni del dominio in classi di equivalenza: la tecnica delle category partition (suddivisioni del dominio in classi di equivalenza) consiste nel suddividere il dominio delle possibili input del sistema in classi di equivalenza, cioè in gruppi di input che dovrebbero comportarsi in modo simile. Utilizzando questa tecnica, è possibile individuare casi di test che coprano il maggior numero possibile di classi di equivalenza, garantendo così una copertura accurata del sistema;
- Analisi dei valori limite (test di fronitiera): la tecnica dei test di fronitiera consiste nell'analizzare i valori limite del sistema, cioè i valori di input che si trovano al confine tra due classi di equivalenza o che rappresentano limiti estremi del sistema. Utilizzando questa tecnica, è possibile verificare il comportamento del sistema in presenza di input particolari o estremi, che potrebbero non essere stati considerati durante il testing normale. I test di fronitiera sono particolarmente utili per individuare errori di gestione delle condizioni limite o per verificare il comportamento del sistema in presenza di input insoliti o imprevisti;
- Collaudo per confronto: il collaudo per confronto consiste nel verificare il comportamento di un sistema confrontando i risultati ottenuti con quelli attesi. Questa tecnica è particolarmente utile quando il sistema ha un comportamento noto o documentato, ad esempio quando si sta testando una nuova implementazione di un algoritmo già esistente. Utilizzando il collaudo per confronto, è possibile verificare che il sistema funzioni correttamente e che i risultati ottenuti siano coerenti con le aspettative.

In generale, queste sono solo alcune delle tecniche di testing che possono essere utilizzate per verificare la qualità di un sistema. Ognuna di queste tecniche può essere utilizzata in modo complementare alle altre e può essere adattata alle specifiche esigenze di un dato progetto. È importante scegliere la tecnica o le tecniche più adatte in base alle caratteristiche del sistema che si sta testando e ai requisiti specificati.

### Testing delle interfacce

Il testing delle interfacce è una fase importante del processo di testing di un sistema, che mira a verificare il corretto funzionamento delle interfacce tra componenti o sottosistemi. Esistono diverse tipologie di interfacce che possono essere testate, ognuna delle quali presenta caratteristiche e sfide specifiche. Ecco alcune informazioni sui tipi di interfacce e sui tipi di errore che possono essere riscontrati durante il testing delle interfacce:

Tipi di interfacce:

- A invocazione di parametri: in questo tipo di interfaccia, un componente invoca una funzione o un metodo di un altro componente passando come parametri i valori necessari per l'esecuzione della funzione;
- A condivisione di memoria: in questo tipo di interfaccia, i componenti condividono una zona di memoria per scambiarsi informazioni;
- A metodi sincroni: in questo tipo di interfaccia, i componenti comunicano tra loro attraverso metodi sincroni, cioè metodi che richiedono l'attesa della risposta prima di continuare l'esecuzione;
- A passaggio di messaggi: in questo tipo di interfaccia, i componenti comunicano tra loro attraverso il passaggio di messaggi, cioè di pacchetti di informazioni scambiati attraverso una rete o un altro canale di comunicazione.


Tipi di errore:

- Sbaglio nell'uso dell'interfaccia: durante il testing delle interfacce, è possibile riscontrare errori legati all'uso improprio dell'interfaccia da parte di un componente. Ad esempio, può accadere che un componente invochi una funzione con un ordine o un tipo di parametri non corretti, o che faccia assunzioni sbagliate circa ciò che la funzione si attende. Questi errori possono causare malfunzionamenti o crash del sistema;
- Errori di tempistica e sincornizzazione: Questi errori possono verificarsi quando i componenti coinvolti nell'interfaccia non rispettano i tempi o le sequenze di esecuzione previsti, causando conflitti o malfunzionamenti. Ad esempio, può accadere che un componente invochi una funzione prima che un altro componente abbia terminato l'elaborazione di una richiesta precedente, causando un errore di sincronizzazione. Oppure, può accadere che un componente richieda una risposta entro un determinato tempo, ma che l'altro componente non riesca a fornire la risposta entro i tempi previsti, causando un errore di tempistica. È importante prestare particolare attenzione a questi errori durante il testing delle interfacce, in modo da garantire il corretto funzionamento del sistema.
<<<<<<< HEAD

### Classi di Equivalenza

Le classi di equivalenza sono una tecnica di testing basata sulla suddivisione del dominio dei dati in ingresso in classi di dati, dalle quali derivare i casi di test. L'obiettivo di questa tecnica è quello di individuare casi di test che possano rivelare eventuali classi di errori, cioè insiemi di errori che si verificano in modo analogo per dati appartenenti a una stessa classe.

In pratica, le classi di equivalenza rappresentano un insieme di stati validi o non validi per i dati in input, e un insieme di stati validi per i dati in output. Ad esempio, una classe di equivalenza potrebbe rappresentare un insieme di valori che il sistema deve elaborare in modo corretto, oppure un intervallo di valori che il sistema deve considerare come non validi.

Un dato può appartenere a una classe di equivalenza se è un valore, un intervallo, un insieme di valori corretti o un insieme di valori non corretti. Ad esempio, un dato potrebbe appartenere alla classe di equivalenza dei numeri interi positivi, oppure alla classe di equivalenza dei caratteri alfabetici maiuscoli.

Utilizzando le classi di equivalenza, è possibile definire i casi di test in modo più efficace e individuare gli errori del sistema in modo più mirato. Tuttavia, è importante notare che le classi di equivalenza non sono sempre facili da individuare e che possono variare a seconda delle specifiche del sistema che si sta testando.

#### Suddivisione in classi

La suddivisione in classi di equivalenza può essere effettuata in base a diverse variabili, come ad esempio il tipo di valore atteso o l'intervallo di valori validi.
Nel caso in cui ci si aspetti un valore specifico, vengono definite due classi di equivalenza: una classe valida, che rappresenta i valori corretti, e una classe non valida, che rappresenta i valori non corretti. Ad esempio, se il sistema deve accettare come input un codice PIN di 4 cifre, le classi di equivalenza potrebbero essere:
- Classe valida: tutti i codici PIN di 4 cifre corretti;
- Classe non valida: tutti i codici PIN di lunghezza diversa da 4 cifre.

Nel caso in cui ci si aspetti un valore in un intervallo, vengono definite tre classi di equivalenza: una classe valida, che rappresenta i valori compresi nell'intervallo, e due classi non valide, che rappresentano i valori al di fuori dell'intervallo. Ad esempio, se il sistema deve accettare come input un numero intero compreso tra 100 e 700.

### Test di frontiera

Il test di frontiera è una tecnica di testing che si basa sulla selezione dei casi di test che esercitano i valori limite del sistema, cioè i valori di input o di output che si trovano al confine tra due classi di equivalenza. L'obiettivo di questa tecnica è quello di individuare gli errori che tendono ad accumularsi ai limiti del dominio, cioè gli errori che si verificano per valori di input o di output estremi.

Gli errori ai limiti del dominio possono essere causati da diverse ragioni. Ad esempio, potrebbero essere dovuti a una implementazione scorretta dei limiti del sistema, a una mancata gestione degli errori o a una scarsa tolleranza agli errori. Per questo motivo, è importante prestare particolare attenzione ai test di frontiera, in modo da individuare gli errori che potrebbero verificarsi ai limiti del dominio.

Il test di frontiera è complementare alla tecnica delle classi di equivalenza, poiché non si seleziona a caso un elemento della classe, ma si sceglie un elemento ai confini della classe. Ad esempio, se si definisce una classe di equivalenza che rappresenta gli interi compresi tra 1 e 10, il test di frontiera potrebbe consistere nella selezione dei valori 1 e 10, che rappresentano i confini della classe. In questo modo, è possibile verificare come il sistema gestisce gli input ai limiti del dominio.

### Category partition

La category partition (partizione per categorie) è una tecnica di testing basata sulla suddivisione del dominio dei dati in categorie di equivalenza. Questa tecnica è particolarmente utile per la definizione dei casi di test a diversi livelli di granularità, come ad esempio i test di unità, di integrazione o di sistema.

La category partition è composta da diversi passi:
- Analizzare le specifiche: in questa fase, si analizzano le specifiche del sistema e si individuano le unità funzionali che possono essere verificate singolarmente. Per ogni unità, si identificano le caratteristiche (categorie) dei parametri e dell'ambiente;
- Scegliere dei valori (scelte) per le categorie: in questa fase, si definiscono i valori che appartengono a ogni categoria. Ad esempio, si potrebbero definire le categorie "numeri interi positivi", "numeri interi negativi" e "numeri interi nulli";
- Determinare eventuali vincoli tra le scelte: in questa fase, si analizzano eventuali vincoli tra le scelte effettuate per ogni categoria. Ad esempio, si potrebbe verificare se esistono vincoli tra il numero;
- Scrivere test e documentazione: in questa fase, si scrivono i casi di test e la relativa documentazione. Ad esempio, si potrebbero definire i casi di test per ogni categoria, specificando i valori di input e le aspettative di output per ogni caso. Inoltre, si potrebbe includere nella documentazione informazioni sui vincoli tra le categorie e su eventuali altre considerazioni rilevanti.

La tecnica della category partition è molto utile per la definizione dei casi di test, poiché permette di suddividere il dominio dei dati in categorie di equivalenza e di individuare gli errori del sistema in modo più mirato. Inoltre, questa tecnica è adattabile a diverse situazioni e può essere utilizzata a diversi livelli di granularità, a seconda delle esigenze del progetto.