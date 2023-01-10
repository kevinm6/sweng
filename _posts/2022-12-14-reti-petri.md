---
layout: post
title: "[14] Reti di Petri"
date: 2022-12-12 14:40:00 +0200
toc: true
---

# [Reti di Petri](https://www2.informatik.uni-hamburg.de/TGI/PetriNets/index.php)

In questa lezione verranno mostrate le reti di Petri come esempio di linguaggio formale; fin dall'inizio del corso è stato possibile apprendere come l'ingegneria del software si occupi di linguaggi e comunicazione.
Infatti partendo dai processi, che sfruttano un linguaggio poco formale e con poca terminologia tecnica (ad esempio le user story), passando per la progettazione in cui è stato utilizzato un linguaggio più rigoroso, si arriva infine a un vero linguaggio formale utile a raccogliere delle specifiche.
Per descrivere le reti di Petri esistono diversi modi, noi vedremo alcuni dialetti di cui uno utile a descrivere i sistemi real time (temporizzati), che avendo diverse criticità è fondamentale andare formalizzare i requisiti per ridurle al minimo.
Formalizzare e utilizzare linguaggi complessi può essere costoso, infatti questa pratica viene utilizzata quando l'applicazione può avere delle conseguenze gravi in caso di fallimento (ad esempio un software per gestire un razzo spaziale se dovesse avere dei problemi potrebbe causare molti danni, economici e non solo);
Quindi bisogna almeno garantire la safety prima di mettere in funzione il software.

Le reti di Petri sono in parte simili agli automi a stati finiti, ma nascono specificatamente per descrivere sistemi concorrenti.
Cambiano però il concetto di stato e transizione, in quanto lo __stato__ non è più un'informazione atomica vista a livello di sistema ma è divisa in tante parti diverse, e la sua composizione avverrà tramite la visione generale di tutti questi stati parziali.
Quindi sarà rappresentare entità distinte, avente ognuna un suo stato, ma allo stesso tempo avere un'evoluzione globale del sistema.
Di conseguenza le __transazioni__ non operano sullo stato globale ma si limitano a variarne una parte, da ciò è possibile notare la differenza con gli automi a stati finiti in cui esisteva un unico stato attivo, e gli stati disponibili erano dati dal prodotto cartesiano di tutti i possibili valori delle diverse entità.

## Definizione informale
Un vantaggio delle reti di Petri è che possono essere viste in maniera informale dal cliente, infatti è facile definire una rete di Petri in cui i nodi sono _posti_ e _transizioni_, collegati tra loro tramite degli archi.
Possiamo quindi dire che questa struttura è un grafo bipartito, ovvero un grafo in cui i nodi sono messi in relazione __solo__ con nodi dell'altro tipo, quindi i posti possono essere collegati soltanto a transizioni e viceversa.
Ai posti sono assegnati dei ___token___, ovvero degli elementi che vengono assegnati ai posti.
Ogni posto può contenere da 0 a un numero potenzialmente infinito di token (successivamente si approfondirà questo punto per capire se ha senso o no assegnare infiniti gettoni a un posto), ed è proprio la disposizione di questi gettoni nella rete a determinarne il suo stato complessivo.

{% responsive_image path: 'assets/14_rete-Petri-informale.png' %}

Essendo che i gettoni determinano lo stato di ogni parte della rete, allora sicuramente il loro assegnamento ai posti può cambiare, questo perchè lo stato della rete può evolvere.
Ciò accade tramite la trasformazione effettuata dalla transizione; possiamo quindi dire che una transizione è _abilitata_ quando all'interno dei posti collegati in ingresso a essa esistono un certo numero di gettoni.
Inoltre una transizione abilitata si dice che __scatta__ (fire) quando consuma i gettoni nei posti in ingresso e ne vengono generati altri nei posti collegati in uscita.
È importante notare come i gettoni __non si spostano__ da un posto a un altro conseguentemente a uno scatto, ma vengono proprio __distrutti__ nei posti in ingresso alla transizione e __generati__ nei posti in uscita.
Quest'ultima considerazione è importante per capire che i gettoni non sono sempre nello stesso numero.

Tramite questa struttura è facile mostrare al cliente quando qualcosa cambia all'interno del sistema, quindi è molto utile perchè risulta essere più intuitivo rispetto a un linguaggio logico e descrittivo.
Questo è il vantaggio dei __modelli operativi__ come questo, la cui pecca però è che oltre a descrivere che cosa fa il sistema da informazioni anche su come lo fa, quindi rischia di diventare una via di mezzo tra una specifica ed un oggetto di design.
Il motivo per cui è possibile chiamarla specifica è che non viene effettivamente detto come il sistema deve svolgere il suo compito ma si deve comportare nello stesso modo descritto. <!-- Questa frase può essere scritta meglio -->
Quindi la rete descritta è una macchina di riferimento da utilizzare come confronto per capire se il funzionamento del sistema finale si comporta come dovrebbe (si può quindi dire che è come se fosse un oracolo).

## Definizione matematica

Di reti di Petri ne esistono numerosi dialetti, in questo caso vediamo le __PT net__ (reti con posti e transizioni) che sono le più classiche, successivamente verranno descritte delle estensioni e riduzioni di queste reti.

Una rete di Petri classicamente è una 5-tupla $$[P, \, T; \; F, \, W, \, M_0]$$:
- $$P$$ è l'insieme degli identificatori dei posti;
- $$T$$ è l'insieme degli identificatori delle transizioni;
- $$F$$ è la relazione di flusso;
- $$W$$ è una funzione che associa un peso ad ogni flusso; 
- $$M_0$$ è la marcatura iniziale, ovvero l'assegnamento iniziale dei _gettoni_.

Da notare che P e T a livello matematico sono degli insiemi di identificatori che non si sovrappongono (ovvero sono tutti entità differenti) a cui poi verrà assegnato un significato, quindi precedentemente sono stati associati a posti e transizioni, ma di fatto sono tutti identificatori.

Data la 5-tupla appena descritta esistono le seguenti proprietà:
- $$P \cap T = \varnothing$$; 
- $$P \cup T \neq \varnothing$$ (una rete in cui non c'è nulla non è una rete, almeno un posto o una transizione ci devono essere);
- $$F \subseteq (P \times T) \cup (T \times P)$$;
- $$W: \: F \rightarrow \mathbb N - \{ 0 \}$$;
- $$M_0: P \rightarrow \mathbb N$$.
|
Utilizziamo alcune _scorciatoie_:
- $$\operatorname{Pre}(a) = \{ d \in (P \cup T) \ \text{t.c.} \ \langle d, \, a \rangle \in F \}$$;
- $$\operatorname{Post}(a) = \{ d \in (P \cup T) \ \text{t.c.} \ \langle a,\, d \rangle \in F \}$$.

esplicitando queste ultime due scritture:
- Il preset di un nodo _a_ è un insieme di quegli elementi _d_ appartenenti all'unione degli insiemi degli identificatori di posti e transizioni tali che esiste una relazione di flusso tra _d_ e _a_ appartenente a F, in sostanza questo insieme rappresenta l'insieme degli identificatori precedenti di _a_ <!-- al posto di precedenti si può mettere antecedenti -->
- Il postset di un nodo _a_ è un insieme di quegli elementi _d_ appartenenti all'unione degli insiemi degli identificatori di posti e transizioni tali che esiste una relazione di flusso tra _a_ e _d_ appartenente a F, in sostanza questo insieme rappresenta l'insieme degli identificatori successivi di _a_ <!-- al posto di precedenti si può mettere conseguenti, anche se forse è un po brutto -->

Tutto questo rappresente la parte statica delle reti di Petri, ovvero prendendo la situazione in un preciso istante, senza considerare i cambiamenti che potrebbero avvenire.

### Comportamento dinamico

Una transizione $$t \in T$$ è __abilitata__ in una particolare marcatura $$M$$, ovvero dato un particolare assegnamento di gettoni (marcatura iniziale oppure una sua evoluzione) se e solo se

$$
\forall p \in \operatorname{Pre}(t) \quad M(p) \geq W( \langle p, \, t \rangle )
$$

In notazione, `M [ t >` significa che $$t$$ è abilitata in $$M$$. 

Significa che per ogni elemento collegato in ingresso a t esiste un numero di gettoni maggiore del peso dell'arco che collega $$p$$ a $$t$$.
Un aspetto interessante di questa definizione è che non si sta ragionando su tutti i posti della rete, ma solo su quelli collegati in ingresso a _t_, di conseguenza non è necessario conoscere l'intera rete per poter affermare che una transizione sia abilitata o meno, ma basta controllare la zona che comprende i posti appartenenti a $$ \operatorname{Pre}(a) $$, questa situazione è chiamata __località dell'analisi__.

Lo __scatto__ di una transizione $$t \in T$$ in una particolare marcatura $$M$$, produce nel momento successivo una nuova marcatura $$M'$$ tale per cui

$$
\begin{align*}
\forall p \in \operatorname{Pre}(t) - \operatorname{Post}(t)& \quad M'(p) = M(p) - W(\langle p, \, t \rangle) \\
\forall p \in \operatorname{Post}(t) - \operatorname{Pre}(t)& \quad M'(p) = M(p) + W(\langle t, \, p \rangle) \\
\forall p \in \operatorname{Post}(t) \cap \operatorname{Pre}(t)& \quad M'(p) = M(p) - W(\langle p, \, t \rangle) + W(\langle t, \, p \rangle) \\
\forall p \in P - \left ( \operatorname{Post}(t) \cap \operatorname{Pre}(t) \right )& \quad M'(p) = M(p)
\end{align*}
$$

<!-- L'ultimo elemento di questa lista di formule non viene allineato bene non so per quale motivo -->

specificando in modo descrittivo le notazioni precedenti:
- Per ogni identificatore $$p$$ appartenenti al preset ma non al postset della transizione in esame, il numero di gettoni della nuova marcatura $$M'$$ sarà uguale al numero di gettoni della marcatura precedente $$M$$ meno il peso dell'arco che collega $$p$$ a $$t$$;
- Per ogni identificatore $$p$$ appartenenti al postset ma non al preset della transizione in esame, il numero di gettoni della nuova marcatura $$M'$$ sarà uguale al numero di gettoni della marcatura precedente $$M$$ più il peso dell'arco che collega $$p$$ a $$t$$;
- Per ogni identificatore $$p$$ appartenenti sia al preset sia al postset della transizione in esame, il numero di gettoni della nuova marcatura $$M'$$ sarà uguale al numero di gettoni della marcatura precedente $$M$$ meno il peso dell'arco che collega $$p$$ a $$t$$ più il peso dell'arco che collega $$t$$ a $$p$$;
- Per ogni identificatore $$p$$ appartenenti all'insieme dei posti meno l'intersezione tra preset e postset di $$p$$ la marcatura non cambia.

In notazione, `M [ t > M'` significa che lo scatto di $$t$$ in $$M$$ produce $$M'$$.

È importante notare come una transizione può scattare nel caso in cui non abbia alcun elemento nel suo preset, questo significa che la transizione in questione non possiede prerequisiti per scattare.

## Da FSM a rete di Petri

È _meccanicamente_ possibile trasformare una macchina a stati finiti in una rete di Petri.

{% responsive_image path: 'assets/14_produttore.png' %}

Riferendosi all'esempio produttore, l'unico problema è che ci sono dei collegamenti diretti tra posti, ma come è stato detto in precedenza questo non è possibile in una rete di Petri, di conseguenza basterà interporre tra i posti delle transizioni per avere una rete di Petri valida.
Immaginando di mettere un solo token in uno dei due posti della rete appena creata, questo indicherà lo stato attivo che avevamo nella macchina a stati finiti.
Seguendo questi passaggi diventa banale mappare una macchina a stati finiti su una rete di Petri, quindi applicandoli anche all'esempio del consumatore e del buffer avremo altre due reti valide.

{% responsive_image path: 'assets/14_consumatore-buffer.png' %}

Componendole, andranno a creare la seguente rete di Petri.

{% responsive_image path: 'assets/14_produttore-consumatore-buffer.png' %}

A questo punto se stessimo parlando di automi a stati finiti per trovare gli stati raggiungibili da quest'ultima composizione sarebbe stato necessario fare il prodotto cartesiano tra gli stati delle altre tre macchine a stati finiti ricavate precedentemente.
In questo caso però siamo davanti ad una rete di Petri, quindi basterà fondere tutti gli identificatori uguali (ad esempio la transizione deposita della reteproduttore e della rete buffer) e aggiungere i collegamenti che possedevano.

<span style="Color: red">__ATTENZIONE__</span>: nell'esempio della rete composta le transizioni "preleva" e "deposita" dovrebbero avere due nomi differenti, ma siccome sono indicate con due rettangoli diversi è stato omesso questo partivolare, matematicamente però devono avere due nomi differenti.

Precedentemente è stato detto che il con un token nella rete si andava a rappresentare lo stato attivo, di conseguenza portando all'interno della rete composta tutti i token delle varie reti si arriva ad ottenere un risultato descritto dall'immagine sopra, in cui tutte le "entità"mantengono la propria individualità.
Quindi in questo caso si può notare che il produttore è pronto a produrre, il buffer è vuoto e il consumatore è pronto a consumare una volta che il buffer avrà al suo interno qualcosa.

### Come evolve questa rete?
Per rispondere a questa domanda la prima cosa da considerare è quali sono le transizioni abilitate, che in questo caso è solo la transizione produci sotto a $$p_0$$, in quanto è l'unica ad avere tutti gli elementi del suo preset con un numero di gettoni necessari a farla scattare, infatti $$p_0$$ possiede un gettone e l'arco ha peso 1 (quando non è specificato il peso è 1).
Una rete di Petri non forza lo scatto di alcuna transizione, quindi volendo si potrebbe rimanere nello stato corrente senza far scattare "produci" all'infinito, però se scatta il risultato è che il gettone in $$p_0$$ viene bruciato, e in $$p_1$$ viene generato un nuovo token.

{% responsive_image path: 'assets/14_primo-scatto.png' %}

Dopo questo scatto la rete di Petri si trova in una situazione in cui il produttore ha prodotto qualcosa, ed è pronto a depositarlo nel buffer, e a questo punto non resta che porsi nuovamente la domanda "quali transizioni sono abilitate?" per capire come può procedere l'evoluzione della rete.
È facile notare come la transizione "deposita" sotto $$b_0$$ sia l'unica abilitata, e di conseguenza se dovesse scattare il risultato sarebbe il seguente.

{% responsive_image path: 'assets/14_secondo-scatto.png' %}

Ora è possibile identificare una situazione particolare, ovvero quella in cui le transizioni pronte a scattare sono due, e la domanda sorge spontanea, ovvero "quale delle due transizioni scatta prima?".
Nelle reti di Petri descritte fino ad ora non è stato presentato lo scatto simultaneo delle transizioni, nulla vieta che possa avvenire in un contesto reale, ma in questo caso non è una solizione ammissibile, quindi quale delle due transizioni scatta per prima? e secondo quale criterio?
Questo è un caso di non determinismo, ovvero non posso dire quale transizione deve scattare, quindi abbiamo 3 situazioni che si possono verificare:
- viene attivata la prima transizione;
- viene attivata la seconda transizione;
- non viene attivata nessuna transizione (la _non evoluzione_ è comunque un'evoluzione).

Nel caso in cui fosse stato necessario definire che una delle due transizioni scattasse prima dell'altra, ci si troverebbe di fronte ad una rete non corretta, in quanto sarebbe possibile modificare la rete in modo tale che imponga un ordine di scatto alle transizioni. <!-- forse si potrebbe scrivere meglio -->

### Sfruttare le reti di Petri
A questo punto è possibile chiedersi se si stiano sfruttando realmente tutte le potenzialità delle reti di Petri, siccome la rete dell'esempio precedente è stata ricavata da un automa a stati finiti.
Per capire ciò è possibile osservare un secondo esempio in cui abbiamo una rete alternativa alla precedente, ma con lo stesso scopo.

{% responsive_image path: 'assets/14_rete-alternativa.png' %}

La differenza che salta subito all'occhio è il numero di gettoni presenti all'interno di $$p_0$$, e stanno ad indicare il numero di posizioni libere nel buffer.
Questo è un vantaggio perchè se dovessimo cambiare lo scenario e avere una situazione in cui il buffer passa da avere capienza due, ad avere capienza 20, sfruttando questa rete basta modificare la marcatura di $$b_0$$ e il problema sarebbe risolto, la rete precedente invece avrebbe bisogno di una pesante modifica per essere adattata.
Di conseguenza si può applicare lo stesso concetto per il consumatore e per il produttore, che aumentandone il numero dei gettoni (rispettivamente in $$p_0$$ e $$c_0$$) aumenterebbe il numero di entità in grado di produrre e consumare.

{% responsive_image path: 'assets/14_rete-alternativa-diverse-entita.png' %}

È possibile affermare quindi che cambiando il numero di gettoni è possibile moltiplicare gli elementi del sistema di cui si vuole tracciare l'evoluzione, e questo sarebbe molto oneroso in termini di dimensioni se fosse stato fatto con una macchina a stati finiti.

Per definizioni le macchine a stati finiti __non__ possono rappresentare situazioni _infinite_, quindi se si volesse modificare ulteriormente l'esempio appena visto imponendo una capienza illimitata al buffer, con una macchina a stati finiti non sarebbe possibile.
Con le reti di Petri invece basterebbe eliminare l'identificatore del posto $$b_0$$, in questo modo avremmo una situazione in cui i produttori possono depositare senza limiti all'interno del buffer, mentre i consumatori non potrebbero prelevare più elementi di quelli presenti nel buffer.
Questo vincolo è imposto dalla marcatura di $$b_1$$, infatti, la transizione "preleva" potrebbe scattare al massimo _n_ volte consecutivamente, dove _n_ è la marcatura di $$b_1$$ (assumendo che nel metre non avvengano depositi da parte dei produttori).

Un altra modifica applicabile all'esempio sfrutta i pesi degli archi, ovvero ponendo un peso di 3 all'arco che collega "deposita" a $$b_1$$ si potrebbe dire che il produttore crea e deposita tre prodotti, occupando tre posizioni nel buffer.
Invece ponendo un peso di 2 all'arco che collega $$b_1$$ a "preleva" si specifica che vengono prelevati dal buffer due elementi alla volta.
Questo esempio, in parte forzato, è utile per chiarire il fatto che nelle reti di Petri gli archi non sono semplici collegamenti, ma è possibile attribuirgli un significato.
Infatti informalemente sono chiamati archi, ma in realtà indicano una relazione che coinvolge due identificatori, e in questo esempio esiste una relazione per cui ogni elemento prodotto occupa tre posizioni all'interno del buffer, e un'altra relazione in cui ogni consumatore può prelevare obbligatoriamente due elementi alla volta.
Tramite il peso degli archi è possibile creare delle situazioni ambigue, ad esempio se la relazione che coinvolge "deposita" e $$p_0$$ avesse un peso di 2, ogni volta che viene prodotto qualcosa i produttori si moltiplicherebbero, e ovviamente questa situazione indicherebbe che la rete è sbagliata, quindi è necessario fare attenzio ad evitare queste strane situazioni.

{% responsive_image path: 'assets/14_archi-con-pesi.png' %}

È vero però che per ogni rete P/T aventi i pesi sugli archi, ne esiste una che non li possiede, e successivamente verrà mostrato come è possibile rimuoverli.

## Relazioni

Di seguito verranno elencati dei tipi di relazioni che possono coinvolgere i diversi identificatori e cosa comporta la loro presenza.

### Sequenza

Una transazione $$t_1$$ è __in sequenza__ con una transizione $$t_2$$ in una marcatura $$M$$ se e solo se

$$M \ [ \ t_1 > \land \ M \ [ \ t_2 > \land \ M \ [ \ t_1 t_2 >$$

Questa formula sta ad indicate:
- $$t_1$$ è abilitata in $$M$$;
- $$t_2$$ NON è abilitata in $$M$$;
- $$t_2$$ viene abilitata dallo scatto di $$t_1$$ in $$M$$

in questo caso abbiamo una relazione d'ordine non simmetrica, in cui lo scatto di $$t_1$$ è una condizione succificente per cui $$t_2$$ possa scattare, quindi questo tipo di relazione permette di andare a creare un ordine di scatto delle transizioni.
Condizione sufficiente perchè osservando l'esempio sottostante è facile capire che lo sacatto di $$t_0$$ non è necessario per far si che $$t_2$$ scatti, infatti anche se dovesse avvenire lo scatto di $$t_2$$ la transizione $$t_2$$ diventerebbe comunque abilitata.

{% responsive_image path: 'assets/14_sequenza.png' %}

### Conflitto

Due transazioni $$(t_1, \, t_2)$$ sono in:
- __conflitto strutturale__ se e solo se $$\operatorname{Pre}(t_1) \cap \operatorname{Pre}(t_2) \neq \varnothing $$
- __conflitto effettivo__ in una marcatura $$M$$ se e solo se:
    - $$M \ [ \ t_1 > \cap \ M \ [ \ t_2 > $$;
    - $$\exists p \in \operatorname{Pre}(t_1) \cap \operatorname{Pre}(t_2) \mid (M(p) < W(\langle p, \, t_1 \rangle) + W(\langle  p, \, t_2\rangle))$$.

Analizzando i due tipi di conflitto è possibile notare che:
- due transizioni sono in conflitto strutturale se l'interesezione dei due preset è vuota, quindi non hanno posti in ingresso in comune, e di conseguenza non possono interferire tra loro per quanto riguarda le condizioni di abilitazione.
Il conflitto strutturale dipende solo dalla topologia dela rete, infatti non vengono citate le marcature;
- due transizioni sono in conflitto effettivo se sono entrambe abilitate in una marcatura $$M$$ ed esiste un posto in ingresso in comune ai due preset tale per cui il numero di gettoni in quel posto è minore della somma dei pesi dei due flussi che vanno dal posto alla transizione (quindi il posto in ingresso non ha abbastanza gettoni per far scattare entrambe le transizioni).
Quindi entrano in conflitto sulla disponibilità di gettoni nel preset.

Esiste una versione rilassata del conflitto, ed è esplicitata dalla seguente formula:

$$
M \ [ \ t_1 > \land \ M \ [ \ t_2 > \land \ \lnot M \ [ \ t_1 t_2 > 
$$

Andandola ad analizzare il suo significato è che $$t_1$$ e $$t_2$$ sono abilitate in una marcatura $$M$$ e non è possibile la sequenza $$t_1$$ $$t_2$$ a partire da $$M$$.
Ma cosa vuol dire che è una versione rilassata? per capirlo si osservi questo l'esempio sottostante:

{% responsive_image path: 'assets/14_esempio-conflitto1.png' %}

Questa prima versione rimane in conflitto secondo la prima e la seconda definizione, ma facendo una piccola modifica è facile fare in modo che il conflitto rimanga per la prima definizione ma non per la seconda.

{% responsive_image path: 'assets/14_esempio-conflitto1-differenza.png' %}

Aggiungendo una relazione tra $$t_1$$ a $$p_1$$ è facile notare che dopo lo scatto di $$t_1$$ quest'ultima è ancora abilitata, e quindi la seconda definizione non è più rispettata.
Da questo possiamo capire che la prima definizione si basa solo sui preset, ignorando qualsiasi arco in uscita, invece la seconda ragiona anche sugli effetti dello scatto, ma allora perchè viene usata la prima definizione solitamente?
Perchè denota come le transizioni non possiedono abbastanza risorse per scattare entrambe nello stesso istante, ma solo in momenti diversi e a seguito di uno scatto hanno questa capacità, quindi di fatto è possibile vederli come due conflitti differenti. <!-- Verificare se questa parte è abbastanza chiara -->

È possibile fare qualche ulteriore osservarione riguardo alla relazione di conflitto, ovvero la presenza di un conflitto strutturale __non implica__ obbligatoriamente la presenza di un conflitto effettivo in quanto quest'ultimo per esistere necessita che venga soddisfatta una condizione in più.
Al contrario invece un conflitto effettivo __implica__ la presenza di un conflitto strutturale in qunato le condizioni di quest'ultimo sono comprese in quelle del conflitto effettivo.
Di seguito viene mostrato un esempio di conflitto effettivo e strutturale.

{% responsive_image path: 'assets/14_conflitto-effettivo-e-strutturale.png' %}

### Concorrenza

La relazione di concorreza è possibile considerarla la relazione opposta alla relazione di conflitto, quindi due transazioni $$(t_1, \, t_2)$$ sono in:
- __concorrenza strutturale__ se e solo se $$\operatorname{Pre}(t_1) \cap \operatorname{Pre}(t_2) = \varnothing$$
- __concorrenza effettiva__ in una marcatura $$M$$ se e solo se:
    -  $$M \ [ \ t_1 > \cap \ M \ [ \ t_2 > $$;
    - $$\forall p \in \operatorname{Pre}(t_1) \cap \operatorname{Pre}(t_2) \quad (M(p) \geqslant W(\langle p, \, t_1 \rangle) + W(\langle  p, \, t_2\rangle))$$.

Quest'ultima formula sta a significare che due identificatori delle transizioni sono in concorrenza effettiva che se e solo se per tutti i posti che hanno in comune ci sono un numero di gettoni sufficienti per farle scattare entrambe.

In questo caso non esiste alcun legame tra concorrenza strutturale ed effettiva, diversamente da quanto abbiamo visto in precedenza per le relazioni di conflitto.
Infatti se si verificano le condizioni per avere una concorrenza strutturale è __possibile__ che le due transizioni non siano abilitate, oppure se si verificano le condizioni per avere concorrenza effettiva è __possibile__ che $$t_1$$ e $$t_2$$ abbiamo posti in comune, e quindo sarebbe impossibile avere concorrenza strutturale.
<!-- verificare quest'ultima parte perchè non sono sicuro di aver scritto giusto -->
Questo però non esclude il fatto che sia possibile avere concorrenza strutturale ed effettiva contemporaneamente, infatti di seguito sono riportati degli esempi che confermano ciò.

{% responsive_image path: 'assets/14_esempio-concorrenza.png' %}

Ovviamente è anche possibile che non ci sia alcun tipo di concorrenza, è sufficiente che due transizioni abbiano in comune un posto una delle due non sia abilitata.

## Insieme di raggiungibilità 

L'insieme di raggiungibilità $$R$$ di una rete a partire da una marcatura $$M$$ è il più piccolo insieme di marcature tale che:
- $$M \in R(P/T, \, M)$$;
- $$(M' \in R(P/T, \, M) \land \exists t \in T \ M' [t> M'') \Rightarrow M'' \in R(P/T, \, M)$$.

Questa definizione induttiva viene interpretata nel seguente modo:
- passo base $$\rightarrow$$ la marcatura $$M$$ appartiene all'insieme di raggiungibilità $$R(P/T, \, M)$$ ($$M$$ indica la marcatura iniziale mentre P/T indica la rete posti-transizioni);
- passo induttivo $$\rightarrow$$ se $$M'$$ appartiene all'insieme di raggiungibilità (quindi si dice che è raggiungibile) ed esiste una transizione della rete tale per cui è abilitata in $$M'$$ e mi porta in $$M''$$, per cui con uno scatto è possibile passare dalla marcatura $$M'$$ alla marcatura $$M''$$, allora quest'ultima è __raggiungibile__.

procedendo ricorsivamente con questa definizione è possibile ottenere tutte le marcature raggiungibili.

## Limitatezza
<span id="limitatezza"></span>
Una proprietà importante delle reti di Petri è la __limitatezza__, ovvero la proprietà che indica se le possibili evoluzioni della rete possono essere limitate o illimitate, quindi se gli stati raggiungibili sono infiniti oppure infiniti.
Volendo dare una definizione più formale è possibile dire che una rete posti-transizioni (P/T) con marcatura $$M$$ si dice __limitata__ se e solo se:

$$\exists k \in \mathbb N \quad \forall M' \in R(P/T, \, M) \\
\forall p \in P \quad M'(p) \leq k$$

cioè se eisite un numero $$k$$ appartenente all'insieme dei numeri naturali tale per cui per ogni marcatura $$M'$$ raggiungibile da $$M$$, per ogni posto $$p$$ all'interno della rete il numero di gettoni in quella marcatura raggiungibile è minore di $$k$$, ovvero se è possibile porre un numero finito tale per cui dopo qualisasi evolizione non esista alcun posto che possiede un numero di gettoni maggiore di $$k$$, allora è possibile affermare che  la rete è limitata.
Se ciò non si verifica esiste almeno un posto in cui è possibile aumentare tendenzialmente all'infinito il numero di gettoni, tramite una certa evoluzione della rete.
È importante sottolienare che la limitatezza di una rete può dipende dalla sua marcatura iniziale.

{% responsive_image path: 'assets/14_esempio-rete-illimitata.png' %}

## Da reti di Petri a Automi

Precedentemente è stato mostrato come a partire da un'automa sia possibile ricavare una rete di petri, ma è possibile fare il contrario?
Se la rete è limitata allora l'insieme di raggiungibilità è finito, di conseguenza è possibile definire un automa a stati finiti corrispondente che prende ogni marcatura raggiungibile come un proprio stato e ne traccia le transizioni di stato dell'automa conseguenti alla transizione scattata nella rete di Petri.

- gli stati sono le possibili marcature dell'insieme di raggiungibilità;
- le transizioni sono gli eventi che permettono il passaggio da una configurazione alla successiva.

Riuscire a passare dalle reti di Petri agli automi ci permette di modellare un problema in modo più sintetico, ma allo stesso tempo rimane possibile utilizzare i tool di analisi che sfrutta cose già esistenti per gli automi a stati finiti, l'unico problema è che questo vale solo per reti limitate.

## Vitalità di una transizione

Una transazione $$t$$ in una marcatura $$M$$ è detta _viva_ con un certo __grado__ se:
- __grado 0__ non è abilitata in nessuna marcatura appartanente all'insieme di raggiungibilità (è __morta__), quindi qualunque evoluzione avvenga nella rete, la transizione non portà mai scattare (non è sempre un aspetto negativo);
- __grado 1__ esiste almeno una marcatura raggiungibile a partire da $$M$$ in cui la transizione è abilitata;
- __grado 2__ per ogni numero $$n$$ naturale escluso lo zero esiste almeno una sequenza di scatti ammissibile a partire da $$M$$ in cui la transizione scatta $$n$$ volte, ovvero è possibile far scattare la transizione un numero grande a piacere di volte; <!-- differenza tra 2 e 3 da chiarire meglio, si capisce meglio dal'esempio sotto l'immagine -->
- __grado 3__ esiste una sequenza di scatti ammissibile a partire da $$M$$ per cui la transizione scatta infinite volte;
- __grado 4__ in qualunque marcatura raggiungibile esiste una sequenza ammissibile in cui è possibile far scattare la transizione almeno una volta, di conseguenza può scattare  infinite volte in qualunque situazione ci si trovi (ovvero in qualunque marcatura), inq questo caso si dice che la transizione è __viva__ in maniera assoluta.

Gli esempi seguenti rappresentano delle situazioni verosimili riguardanti la Vitalità delle transizioni:
- __grado 0__: qualunque cosa accada la centrale nucleare non può esplodere;
- __grado 1__: in un certo momento se si assume il controllo di tutto ciò che avverrà è possibile portare la centrale nucleare allo spegnimento;
<!-- che esempi si possono mettere per 2 e 3? -->
- __grado 4__: se succede qualcosa fuori dal mio controllo all'interno della centrale posso comunque riuscire ad eseguire lo spegnimento

Una rete viene chiamata __viva__ quando tutte le sue transizioni sono vive.

{% responsive_image path: 'assets/14_esempio-vitalità-transizioni.png' %}

Da questo esempio pratico è possibile notare come la transizione $$t_0$$ è di grado 0 in quanto non potrà mai scattare, perchè è impossibile che abbia i token necessari nel preset per scattare (al massimo o in $$p_0$$ o in $$p_1$$).
La transizione $$t_1$$ è fi grado 1 perchè esiste almeno una marcatura raggiungibile per cui essa scatti, infatti la marcatura corrente è quella che ne permette lo scatto, questo però non è obbliatorio, infatti sarebbe potuto accadere che la marcatura corretta fosse un'altra.
Osservando la transizione $$t_3$$ è possibile notare che essa scatti infinite volte (e non $$n$$ grande a piacere, quindi non si tratta di una transizione di grado 2), però nel caso avvenga lo scatto di $$t_1$$ la transizione $$t_3$$ non potrà mai più essere abilitata (quindi esiste una marcatura in cui non sarà possibile il suo scatto), e questo ci garantisce che non si tratta di una transizione di grado 4, ma bensi di grado 3.
Il caso più particolare è quello della transizione $$t_2$$, è noto che $$t_3$$ può scattare infinite volte, e quindi in $$p_2$$ possono esserci infiniti gettoni, inoltre conseguentemente allo scatto di $$t_1$$ il posto $$p_1$$ conterrà un gettone, ma comunque la transizione $$t_2$$ non può scattare infinite volte.
Quseto perchè è vero che all'infinito posso generare gettoni in $$p_2$$, ma dal momento che scatta $$t_1$$ si perde questa possibilità, permettendo a $$t_2$$ di scattare tante volte quanti sono i gettoni in $$p_2$$.
Infine $$t_4$$ è una transazione viva (di grado 4), perchè qualunque sia la marcatura raggiungibile dalla marcatura corrente è possibile prendere il controllo e sicuramente esiste una sequenza di scatti tale per cui $$t_4$$ diventi abilitata.

## Capacità dei posti 

Inizialmente è stato detto che esistono diversi dialetti riguardanti le reti di Petri, infatti una possibile estensione consiste nel fissare una capacità massima rispetto al numero di token ammissibili in un posto.
Un esempio potrebbe essere quello in cui in un sistema possono essere presenti $$k$$ lettori contemporaneamente e non più di $$k$$.
Avendo la possibilità di definire una capacità dei posti, è facile intuire che diventa possibile forzare la limitatezza della rete.

Tale estensione aumenta la potenza espressiva oppure è semplicemente una scorciatoia?
Tramite l'esempio sottostate si può notare che questa estensione non è altro che una tecnica per facilitare la scrittura della rete.

{% responsive_image path: 'assets/14_simulazione-capacità-posti.png' %}

Nella rete con la capacità dei posti limitata per far sì che ad esempio la transizione $$t_0$$ scatti, è necessario che sia i posti nel suo preset abbiano gettoni sufficienti, sia che dopo il suo scatti il posto $$p_0$$ non superi il limite assegnatogli.
Volendo scrivere la stessa rete utilizzando il metodo classico visto fino ad ora basta aggiungere un __posto complementare__, e in questo modo l due reti mostrarte sopra sono equipollenti, ossia hanno lo stesso valore.
Infatti fino a che nel posto complementare esistono dei gettoni la transizione $$t_0$$ può scattare, ma dal momento che tutti i gettoni di $$p_0(compl)$$ vengono bruciati $$t_0$$ non sarà più abilitata, e nel posto $$p_0$$ ci sarà il numero massimo di gettoni possibili (da notare come la somma dei gettoni del posto condiserato sia esattamente la capacità massima scelta in precedenza).
Questo vale però solo per le reti __pure__, ovvero le reti che per ogni transizione ha preset e postset disgiunti.

### Posto complementare
Un posto complementare è un posto che ha in uscita verso ognuna delle transizioni del posto considerato, un arco di ugual peso ma direzione opposta.
Matematicamente possiamo scivere questa definizione nel seguente modo:

Un posto $$pc$$ è complementare di $$p$$ se e solo se

$$
\forall t \in T ( \exists \langle p, t \rangle \in F) \Leftrightarrow \exists \langle t, pc \rangle \in F \quad W(\langle p, t \rangle) = W(\langle t, pc \rangle)
$$

$$
\forall t \in T ( \exists \langle t, p \rangle \in F) \Leftrightarrow \exists \langle pc, t \rangle \in F \quad W(\langle pc, t \rangle) = W(\langle t, p \rangle)
$$

Per ogni transizione appartenente a $$T$$ in uscita da $$p$$, quindi tale per cui esiste una relazione di flusso dal posto $$p$$ alla transizione $$t$$ deve esistere un flusso di direzione opposta che va dalla transizione $$t$$ al posto complementare $$pc$$ avente lo stesso peso.
In più vale lo stesso discorso per le transizioni in ingresso al posto $$p$$, quindi per ogni transizione $$t$$ appartenente a $$T$$ in ingresso a $$p$$, quindi tale per cui esiste un flusso da $$t$$ al posto $$p$$ deve esistere un flusso che va dal posto complementare $$pc$$ a $$t$$ di direzione opposta e avente lo stesso peso.
<!-- Ricontrolla queste due formule -->
Questa formula garantisce che il numero di gettoni somma tra il posto e il suo complementare sia costante, e permette di formulare la condizione di abilitazione (lavorando sul preset della transizione) in modo tale da dipendere anche dal numero di token presenti nel posto in arrivo.

### Abilitazione con capacità
Potrebbe però sorgere un problema dal momento che si devono trattare reti con capacità sui posti, come è possibile definire la condizione di abilitazione in quel caso?
La definizione di _abilitazione_ è per reti con capacità sui posti è la seguente:

$$t \in T$$ è __abilitata__ in $$M$$ se e solo se 

$$
\begin{align*}
\forall p \in \operatorname{Pre}(t) &\quad M(p) \geq W(\langle p, t \rangle) \\
\forall p \in \operatorname{Post}(t) &\quad M(p) + W(\langle t, p \rangle) \leq C(p)
\end{align*}
$$

oppure si può ragionare diversamente

$$t \in T$$ è __abilitata__ in $$M$$ se solo se:

$$
\begin{align*}
\forall p \in \operatorname{Pre}(t) &\quad M(p) \geq W(\langle p, t \rangle) \\
\forall p \in \operatorname{Post}(t) - \operatorname{Pre}(t) &\quad M(p) + W(\langle t, p \rangle) \leq C(p) \\
\forall p \in \operatorname{Post}(t) \cap \operatorname{Pre}(t) &\quad M(p) - W(\langle p, t \rangle) + W(\langle t, p \rangle) \leq C(p)
\end{align*}
$$

La formula corretta è la seconda, infatti considerando l'immgine seguente possiamo notare come la rete di sinistra abbia ancora la transizione abilitata, mentre quella di destra no.
Infatti nella seconda rete è come se lo scatto venisse spezzato in due fasi, la prima in cui vengono generati i gettoni nel posto (in questo caso $$p_3$$), la seconda invece in cui vengono tolti tanti gettoni quanto è il peso dell flusso da $$p_3$$ a $$t_1$$.
Nella prima rete questo non accade invece, è come se si verificasse tutto nello stesso istante.

{% responsive_image path: 'assets/14_esempio_abilitazione_reti_capacità.png' %}

A questo punto, ci si potrebbe chiedere se è possibile genrare la situazione equivalente nel caso di reti normali, e la risposta è no nel caso in cui si posti complementari.
Con quest'ultimo caso è possibile solo per le reti pure, ma non per tutte le reti in generale, infatti fino a che non sono presenti archi in entrata e uscita allo stesso posto dalla stessa traniszione non sorge alcun tipo di problema.

Come è possibile risolvere questo problema? è possibile pensare a due approcci:
- si trova un altro approccio diverso dai posti complementar;
- si cerca di dimostrare che una rete pura ha sempre un equivalente rete non pura.

<!-- aggiungere esempio / marcature pure / pure-equivalenti / ecc .. -->
<!-- Si è fermato a questo punto durante la lezione, nella lezione 20 non ha spiegato ancora quale approccio utilizzare -->
## Archi inibitori
Esiste un'altra estenzione delle reti di petri in cui si si utilizzano gli __archi inibitori__, ovvero degli archi che permettono indicano la situazione in cui una transizione non ha bisogno di token perchè sia abilitata.
Nel caso in cui un arco inibitore abbia un peso $$n$$ indica che la transizione è abilitata se nel posto collegato non ci siano neanche $$n$$ gettoni.

In caso di rete limitata la potenza di una rete che sfrutta gli archi inibitori non cambia, questo perchè, esistendo un limite massimo $$k$$ di gettoni all'interno della rete, sarà sufficiente creare un posto complementare contente un numero di gettoni tali per cui, la somma tra quest'ultimi e i gettoni presenti nel posto considerato, sia minore di $$k$$.
A questo punto è necessario che sia presenti due archi (uno in ingresso e uno in uscita) di peso 10, in modo tale da permettere lo scatto della transizione solo nel caso in cui tutti i gettoni siano all'interno del posto complementare.
In realtà non è necessaria che tutta la rete sia limitata, ma basta che il sigolo posto lo sia, ovvero è necessario sapere che qualunque sia lo stato generale della rete, in quel preciso posto non ci siano più di $$k$$ gettoni.
Nel caso di rete non limitata invece non è sempre possibile avere una traduzione equivalente della rete di Petri, questo significa che la potenza delle reti con questi particolari archi aumenta.

Il problema di questi particolari archi è che rendono inutilizzabili alcune tecniche di analisi che verranno affrontate successivamente.

## Eliminazione pesi archi

In precedenza è stato accennato che per ogni rete avente dei pesi sugli archi è possibile crearne una equivalente ma senza pesi, ovvero tutti gli archi avranno peso uno, ma la rete avrà un comportamento del tutto analogo alla rete originale con i pesi.
Per fare ciò è necessario considerare due casi distinti, ovvero quello con peso sugli archi in ingresso ad una transizione e quello con peso sugli archi in uscita ad una traniszione.

### Pesi su archi in ingresso.

Per poter effettuare questa modifica sarà necessario avere lo scatto di una nuova transizione (perchè ovviamente non è possibile collegare due archi dallo stesso posto alla stessa transizione), però non basta.
Infatti dopo lo scatto di $$t_0$$ è possibile che $$t_0 bis$$ non scatti, e la rete evolva senza che in $$p_1$$ ci sia il giusto numero di gettoni (problema di concorrenza).
Per risolvere questo problema si sfrutta una sorta di __lock__, ovvero un posto collegato bidirezionalmente con tutte le transizioni della rete, tranne per $$t_0$$ a cui è collegato solo in ingresso, e per $$t_0 bis$$ a cui è collegato solo in uscita.
In questo modo è come se lo scatto di $$t_0$$ sia scomposto logicamente in due parti, quando $$t_0$$ scatta viene attivato il lock in modo tale che nessun'altra transizione sia abilitata, e successivamente lo scatto di $$t_0 bis$$ lo rilascia.
Questo ovviamente non obbliga $$t_0 bis$$ a scattare immediatamente, però è certo che la rete non potrà evolvere in alcun altro modo, e quindi non si creeranno marcature non esistenti nella rete originale.
Questa soluzione non è molto elegante perchè esiste un posto avente in ingresso un arco per ogni transizione della rete.

{% responsive_image path: 'assets/14_eliminazione-archi-ingresso.png' %}

### Pesi su archi in uscita

In questo caso il peso da rimuovere è su un arco in uscita da un posto e in ingresso ad una transizione, quindi è necessario che vengano distrutti due gettoni dallo stesso scatto.
L'approccio da utilizzare è simile, infatti è presente un posto globale che fa da lock in modo che risolva il problema di concorrenza tra $$t_8$$ e $$t_1$$.
In questo caso però abbiamo un ulteriore problema, ovvero al momento dello scatto di $$t_8$$ il gettone in $$p_0$$ viene consumato, di conseguenza $$t_1$$ non può scattare, inoltre il resto della rete rimane bloccata, in quanto all'interno del posto globale non è più presente il gettone, che è stato consumato sempre dallo scatto di $$t_8$$.
Questo deadlock può essere risolto aggiungendo un controllo sul posto $$p_0$$, in modo tale che possa scattare solo quando possiede due o più gettoni, in questo modo non può verificarsi la situazione in cui $$t_8$$ scatti senza un successivo scatto di $$t_1$$.
Il meccanismo della rete inizia ad essere molto complesso, e nell'esempio viene mostrato il caso in cui devono essere consumati due gettoni.
In altri caso con più gettoni, o con situazioni differenti, la rete aumenterebbe ulteriormente di complessità e quindi risulterebbe più facile pensare la rete in modo differente.
Infatti questo modo non è l'unico modo per modellare il sistema, ma è il modo meccanico per modellare questo particolare esempio, ma è comunque possibile trovare un buon modo per modellare una rete senza sfruttare i pesi e senza una traduzione meccanica di essi.

{% responsive_image path: 'assets/14_eliminazione-archi-uscita.png' %}

### Reti C/E
Le reti C/E (condizioni eventi) sono delle particolari reti più semplici, in cui tuttu gli archi hanno peso uno, e tutti i posti hanno capacità massima uno.
Questo tipo di rete possiede meno scoriatoie per modellare la rete, ma è più semplice ed immediata da capire, infatti i posti rappresentano delle condizioni che possono essere vere o false, ed in base ad esse è possibile il verificarsi di certi eventi, rappresentati dalle transizioni.
Ogni rete P/T __limitata__ è traducibile in un'equivalente rete C/E, per le reti illimitate invece non è possibile trovare una traduzione, siccome non si possono rappresentare infiniti stati con un tipo di rete che per definizione è limitata.

## Conservatività 
La conservatività è una proprietà di una rete rispetto ad una funzione $$H$$ che assegna un peso ad ogni posto della rete, e ognuno di questi pesi è positivo.
Esiste quindi una funzione di pesi $$H: P \rightarrow \mathbb N - \{ 0 \}$$ tale per cui una rete P/T con una marcatura $$M$$ si dice conservativa rispetto a tale funzione se e solo se:

$$
\forall M' \in R(P/T, \, M) \quad \sum_{p \in P} H(p) M'(p) = \sum_{p \in P} H(p) M(p)
$$

Ovvero per ogni marcatura $$M'$$ raggiungibile dalla marcatura inizale, data una certa marcatura e una funzione $$H$$, si dice che la rete è conservativa se la sommatoria dei gettoni di ogni posto pesati con la funzione $$H$$ è costante per qualunque marcatura raggiungibile.

Esiste inoltre un legame tra conservatività e limitatezza, ovvero una rete che garantisce la conservatività è limitata, ma non è detto il viceversa (quindi la limitatezza è una condizione necesaria ma non sufficiente).

dimostrazione che una rete se è conservativa è limitata:

Sapendo che $$\sum_{p \in P} H(p) M(p)$$ è un numero (quindi possiamo indicarlo con $$k$$), è possibile dire che:

$$
\forall M' \in R(P/T, \, M) \quad \sum_{p \in P} H(p) M'(p) = k
$$

Inoltre si sa che $$H(p) > 0$$, di conseguenza ogni elemento della sommatoria ha un contributo pari a 0 oppure positivo.
Questo perchè se non ci sono gettoni all'interno del posto, avremo un numero positivo ($$H(p)$$) moltiplicato per 0, quindi il suo contributo è nullo, altrimenti avrà per forza un valore positivo.
A questo punto è possibile dire che se esiste almeno una marcatura di $$p$$ in cui il numero di gettoni è diverso da 0, il suo contributo è positivo ma limitato da $$k$$.
Questo vale per ogni posto all'interno della rete, di conseguenza ci si è rincondotti alla definizione di <a href="#limitatezza">limitatezza</a>.

### Rete strettamente conservativa
La _conservatività strtta_ è un particolare caso di conservatività, ed è possibile definirla dicendo che: una rete P/T conservativa rispetto alla funzione $$H$$ che assegna pesi tutti uguali a 1 si dice strettamente conservativa.

$$
\forall M' \in R(P/T, \, M) \quad \sum_{p \in P} M'(p) = \sum_{p \in P} M(p)
$$

La formula precedente sta a significare che la sommatoria del numero di token per ogni posto in una qualsiasi marcatura è costante (ovvero è uguale alla sommatoria della marcatura iniziale per ogni posto <!-- è corretto? -->), in altre parole, preso il singolo scatto di una transizione viene forzato il fatto che per ogni gettone che viene distrutto ne viene generato un altro in uscita. <!-- in questo caso succede ciò che è sempre stato detto di non pensare, ovvero che i gettoni si spostano -->

Matematicamente questo concetto si può esprimere anche tramite questa formula:

$$
\forall t \in T \quad \sum_{p \in \operatorname{Pre}(t)} W(\langle p, t \rangle) = \sum_{p \in \operatorname{Post}(t)} W(\langle t, p \rangle)
$$

Per ogni transizione $$t$$, la somma dei pesi degli archi che collegano ogni elemento del preset di $$t$$, alla transizione $$t$$ deve essere uguale alla sommatoria dei pesi degli archi che collegano la transizione $$t$$ con ogni posto nel postset di $$t$$.

Queste due formule esprimono lo stesso concetto, ma la prima si riferisce alle marcature (stati), mentre l'altra all'aspetto topologico della rete (ovvero i pesi degli archi).
Quindi la prima è un analisi dinamica in qunto è necessario calcolare gli stati raggiungibili, l'altra inceve è statica siccome basta conoscere la rete.
È possibile fare una precisazione per quanto riguarda la seconda formula, ovvero che le transizioni da prendere in considerazione sono quelle __non morte__ (quindi di grado $$\geqslant$$ 1). <!-- Non è chiaro quali differenze ci sono tra le due formule (videolezione interno al minuto 55 della lezione 20, prima parte) -->

## Stato base e rete revertibile
Una marcautra $$M'$$ si dice __stato base__ di una rete se per ogni marcatura $$M$$ in $$R(M_0)$$, $$M'$$ è raggiungibile da $$M$$.
Ovvero qualunque sia lo stato attuale della rete è possibile raggiungere la marcatura $$M'$$.
Un caso particolare si ha quando la marcatura iniziale $$M_0$$ è lo stato base della rete per ogni marcatura $$M$$ in $$R(M_0)$$, in quel caso si dice che la rete è __reversibile__ (lo stato iniziale è uno stato base).