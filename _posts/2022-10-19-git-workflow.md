---
layout: post
title: "[06] Git workflow"
date:   2022-10-19 14:45:00 +0200
toc: true
---

# Git Workflow

In Git è praticamente impossibile non utilizzare i branch. 
C'è __libertà completa__ sul loro utilizzo: tutti i branch hanno pari importanza, chiunque può crearli e nominarli in qualunque modo.

Lavorando in un team, è quindi necessario stabilire delle politiche sui tipi e i nomi di branch, in modo da organizzare il lavoro al meglio. 

## GitFlow

GitFlow è una tecnica di organizzazione dei branch e della repository.
I branch sono differenziati in tipi e possono avere vita limitata.
Le operazioni di creazione e _merge_ di branch sono guidate.

È disponibile online una [cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/) molto interessante, ma non esaustiva.

I branch e i tipi di branch previsti da GitFlow sono:
- branch master;
- branch develop;
- feature branches;
- release branches;
- hotfix branches.

## develop e master

{% responsive_image path: 'assets/06_gitflow-develop-master.png' %}

In GitFlow, ci sono due rami con _"vita infinita"_:
- __master__: contiene le versioni _"stabili e pronte alla consegna"_;
- __develop__: è il ramo di integrazione in cui vengono integrati i contribuiti di tutti i gruppi; è anche il punto di partenza degli altri tipi di branch.

Al termine di ogni rilascio il contenuto di develop viene mergiato in master.
Dal develop vengono rilasciate le _versioni notturne_.

## feature branch

{% responsive_image path: 'assets/06_gitflow-feature.png' width: "400" %}

I feature branch __possono partire solo dal branch develop__ e non da nessun altro branch.

Per iniziare una feature...
```bash
$ git checkout develop                  # entra nel branch develop
$ git branch feature/myFeature          # crea un branch di feature
$ git checkout feature/myFeature        # entra nel branch appena creato
```

Al termine della feature, integro:

```bash
$ git checkout develop                  # entra nel branch develop
$ git merge --no-ff feature/myFeature   # mergia il branch di feature
$ git branch -d feature/myFeature       # elimina il branch di feature
```
### -\-no-fast-forward

{% responsive_image path: 'assets/06_gitflow-feature-ff.png' %}

Di default, Git risolve il merge di due branch con la stessa storia banalmente eseguendo il _fast forward_, ovvero spostando il puntatore del branch di destinazione all'ultimo commit del branch entrante.

In GitFlow è preferibile esplicitamente __disabilitare il fast forward__ (con l'opzione `-\-no-ff`) durante il merge in develop in quanto è più facile distinguere il punto di inizio e il punto di fine di una feature.

### Squashing

Usando Git è anche possibile eseguire in fase di merge lo _squashing_ dei commit, ovvero la fusione di tutti i commit del branch entrante in uno solo. 
Questa operazione è utile per migliorare la leggibilità della history dei commit su branch grossi (come develop o master) ma il suo uso in GitFlow è opinabile: il prof. Bellettini consiglia di non utilizzarlo, in modo da mantenere i commit originali.

## Release

{% responsive_image path: 'assets/06_gitflow-release.png' %}

Lo scopo di creare una release è __cristalizzare l'insieme delle funzionalità__ presente sul branch develop all'inizio di essa dedicandosi solo alla sistemazione degli errori o alle attività necessarie per il deploy (modifica del numero di versione, ...). 
L'insieme delle funzionalità rilasciate è quello presente sul branch develop al momento di inizio di una release 

I bug fix possono essere ri-mergiati in develop, anche utilizzando la funzionalità __cherry-pick__ di Git.

Per iniziare una nuova release è sufficiente creare un nuovo branch da develop:
```bash
$ git checkout -b release/v1.2.3 develop
```

Al termine della creazione della release, occorre mergiarla in master e in develop. 
```bash
$ git checkout master               # entra nel branch master
$ git merge --no-ff release/v1.2.3  # mergia la feature
$ git tag -a v1.2.3                 # tagga sul branch master il rilascio
$ git checkout develop              # entra nel branch develop
$ git merge --no-ff release/v1.2.3  # mergia la feature
$ git branch -d release/v1.2.3      # elimina il branch della feature
```

La differenza tra tag e branch è che il __tag è puntatore costante al commit__, mette il branch va avanti tra commit e commit.

Un'altra differenza tra release e feature è che posso avere solo una release aperta in un dato istante.

## Hotfix

Un hotfix è una riparzione veloce di difetti urgenti senza dover aspettare la prossima release.
È l'unico caso per cui non si parte da develop, ma dall'ultima - o una particolare - versione rilasciata su master.

```bash
$ git checkout -b hotfix/CVE-123 master # crea un branch di hotfix
```

Quando lo chiudo:
```bash
$ git checkout master                   # entra nel branch master
$ git merge --no-ff hotfix/CVE-123      # mergia l'hotfix
$ git tag -a hotfix/CVE-123             # tagga sul branch master il rilascio
$ git checkout develop                  # entra nel branch develop
$ git merge --no-ff hotfix/CVE-123      # mergia l'hotfix
$ git branch -d hotfix/CVE-123          # elimina il branch di hotfix
```

## Limiti

Quali sono i limiti di git presentato così?

Git e GitFlow come sono stati esposti presentano numerosi vincoli, tra cui:
- la __mancanza di autorizzazione granulare__ oltre il primo livello di accesso;
- l'__assenza di code review__.

### git request-pull - generates a summary of pending changes

Il tool `git request-pull` serve per formattare la propria proposta di modifiche e inviarla a una mailing list, chiedendo di pullarle.

    git request-pull [-p] <start> <URL> [<end>]

Per esempio, i contributori Linux usano questo strumento per chiedere a Linus Torvalds di unire le modifiche nella sua versione.
L'output generato mostra file per file le modifiche fatte e i commenti dei commit creati, raggruppati per autore.

```
$ git branch --show-current
lezioni/06
$ git request-pull master git@gitlab.di.unimi.it:silab-gang/sweng.git lezioni/06
The following changes since commit 7a75b799fa0247590cb3f6d545c5b773cdf07f4a:

  Merge branch 'lezioni/03' into 'master' (2022-11-29 16:23:53 +0000)

are available in the Git repository at:

  git@gitlab.di.unimi.it:silab-gang/sweng.git lezioni/06

for you to fetch changes up to 2a9cfd798168b374e8aa33999aafee3e5fcd537c:

  Merge branch 'master' into marco/06-integrazione (2022-11-30 14:49:27 +0100)

----------------------------------------------------------------
Marco Aceti (3):
      Add lesson notes
      Integrate and refactor notes until 'Limiti'
      Merge branch 'master' into marco/06-integrazione

 _posts/2022-10-19-git-workflow.md    | 250 ++++++++++++++++++++++++++++++++++
 assets/06_gitflow-develop-master.png | Bin 0 -> 3098301 bytes
 assets/06_gitflow-feature-ff.png     | Bin 0 -> 2512627 bytes
 assets/06_gitflow-feature.png        | Bin 0 -> 1774044 bytes
 assets/06_gitflow-hotfix.png         | Bin 0 -> 131372 bytes
 assets/06_gitflow-release.png        | Bin 0 -> 4477425 bytes
 6 files changed, 250 insertions(+)
 create mode 100644 _posts/2022-10-19-git-workflow.md
 create mode 100644 assets/06_gitflow-develop-master.png
 create mode 100644 assets/06_gitflow-feature-ff.png
 create mode 100644 assets/06_gitflow-feature.png
 create mode 100644 assets/06_gitflow-hotfix.png
 create mode 100644 assets/06_gitflow-release.png
```

Questo modello è molto più _peer to peer_ delle pull request proposte dai sistemi semi-centralizzati spiegati in seguito.

# Hosting centralizzato

Un hosting centralizzato Git è un servizio che fornisce una repository centrale per i progetti Git, permettendo di superare i limiti della decentralizzazione senza rinunciare a molti suoi vantaggi.

Inventano nuovi meccanismi, provando a imporre nuovi workflow (GitHub Flow, GitLab Flow, ...).
Inoltre, molti servizi di hosting centralizzati offrono funzionalità aggiuntive, come la possibilità di eseguire il "fork" di un repository, inviare _pull request_ per le modifiche e di utilizzare strumenti di continuous integration (CI) per testare automaticamente le modifiche apportate al codice.

## Fork

Il "fork" di un repository Git è una __copia del repository originale__ che viene creata su un account di hosting diverso dal proprietario originale. Questo permette a un altro sviluppatore di creare una copia del repository e di lavorare su di essa senza influire sul lavoro del proprietario originale. In questo modo, è possibile mantenere una connessione tra i due repository e condividere facilmente le modifiche apportate.

L'utilizzo del fork __risolve__ un primo problema di __autorizzazione__ poiché permette a un altro sviluppatore di lavorare su una copia del repository senza dover ottenere le autorizzazioni del proprietario originale.

La maggioranza delle piattaforme di hosting centralizzato __ottimizza__ la condivisione dello spazio degli oggetti, utilizzando un'unica repository fisica per tutti i fork.
Ci sono delle problematiche si sicurezza: è difficile per la piattaforma stabilire in quale fork si trova l'oggetto ([esempio sul kernel Linux](https://github.com/torvalds/linux/commit/b4061a10fc29010a610ff2b5b20160d7335e69bf)). 

## Review / Pull request

Tra la creazione di una pull request e il suo _merge_, specialmente nei progetti open source (dove chiunque può proporre qualsiasi patch) è fondamentale prevedere un processo di __review__.

{% responsive_image path: 'assets/06_pull-request.png' %}

La funzionalità di review/pull request permette di facilitare le interazioni tra gli sviluppatori utilizzando il sito di hosting come luogo comune per la discussione informale e la revisione delle modifiche.

Inoltre, molti servizi di hosting centralizzati offrono strumenti di continuous integration (CI) che possono essere utilizzati per testare automaticamente le modifiche proposte nella pull request

# Gerrit

Nasce interno a Google, per sviluppare progetti interni.

Gerrit è un __sistema di review__ del codice sviluppato internamente da Google per gestire i progetti interni; si basa sul concetto di "peer review": tutti gli sviluppatori sono autorizzati a fare review delle proposte di modifica di qualsiasi zona di codice.

Nel processo di review di Gerrit, i __developer__ possono sottoporre proposte di cambiamento utilizzando un sistema di "patch" che descrive le modifiche apportate al codice. 
I __reviewer__, ovvero gli altri sviluppatori del progetto, possono quindi esaminare le patch e decidere se accettarle o rifiutarle. 
Una volta che una patch ha ricevuto un numero sufficiente di review positivi, viene automaticamente integrata nel __repository principale autorativo__ in cui tutti hanno accesso in sola lettura.

Gerrit obbliga a strutturare le modifiche (changeset) in un unico commit (squash) al momento dell'accettazione; al momento della review rimangono le versioni singole. 

## Verifier

Scarica la patch, la compila, esegue i test, controlla che ci siano tutte le funzioni, controlla che tutto funzioni come dovrebbe.

Una volta terminato il proprio processo, approva le modifiche votandole positivamente.
Solitamente sono necessari 1 o 2 voti per procedere.

## Approver

Una volta verificata, una proposta di modifiche deve essere anche approvata. 
L'approvatore deve determinare la risposta alle seguenti domande riguardo la proposta di modifiche:
- è valida per lo scopo del progetto?
- è valida per l'architettura del progetto?
- introduce nuove falle nel design che potrebbero causa problemi in futuro?
- segue le _best pratices_ stabilite dal progetto?
- è un buon modo per implementare la propria funzione?
- introduce rischi per la sicurezza o la stabilità? 

Se approva la modifica, allora può scrivere LGTM (_"Looks Good To Me"_).

# Strumenti dell'opensource

## Build automation

Un pezzo importante è la build automation, ovvero l'insieme delle operazioni che, partendo dal codice sorgente generano automaticamente l'eseguibile compilato.

### make

`make` nasce nel mondo UNIX e ne segue la filosofia _pipeline_, ovvero __singoli comandi semplici concatenati__ per svolgere compiti più complessi.

In make si definiscono a livello di shell quali sono i comandi da dare per ottenere certi _targets_ (obiettivi).
Supporta la __compilazione incrementale__ gestendo le __dipendenze__ tra file.

```make
CC=gcc
CFLAGS=-I.

%.o: %.c $(DEPS)
  $(CC) -c -o $@ $< $(FLAGS)

hellomake: hellomake.c hellofunc.o
  $(CC) -o hellomake hellomake.o hellofunc.o -I.
```

Nell'esempio, se il _target_ hellomake (definito dai file `hellomake.c` e `hellofunc.o`) è stato aggiornato, occorre ricompilarlo utilizzando i comandi sotto.

È a un livello molto basso ed è prono ad errori.
Non c'è portatibilità tra macchine (ambienti) diverse.

#### Generazione automatica

Sono stati creati dei tool (`automake`, `autoconf`, `imake`, ...) che _generano_ `Makefile` ad-hoc per l'ambiente attuale.

Il _mantra_
```bash
$ ./configure
$ make all
$ sudo make install
```
era largamente utilizzato.

### Ant 
Ant nasce in Apache per supportare il progetto Tomcat.
Data una __definizione in XML__ della struttura del progetto e delle dipendenze invocava comandi programmati tramite classi Java per compilare il progetto.

Il vantaggio è che Java offre un livello d'astrazione sufficiente a rendere il sistema di build portabile su tutte le piattaforme.

Nella versione base supporta integrazioni con altri tool come CVS, Junit, FTP, JavaDOCS, JAR, ecc...
Non solo compila, ma fa anche deployment.

I target possono avere dipendenze da altri target.
I target contengono task che fanno effettivamente il lavoro; si possono aggiungere nuovi tipi di task definendo nuove classi Java.

Esempio di un build file:

```xml
<?xml version="1.0"?>
<project name="Hello" default="compile">
  <target name="clean" description="remove intermediate files">
    <delete dir="classes" />
  </target>
  <target name="clobber" depends="clean" description="remove all artifact files">
    <delete file="hello.jar">
  </target>
  <target name="compile" description="compile the Java source code to class files">
    <mkdir dir="classes" />
    <javac srcdir="." destdir="classes" />
  </target>
  <target name="jar" depends="compile" description="create a Jar file for the application">
    <jar destfile="hello.jar">
      <fileset dir="classes" includes="**/*.class" />
      <manifest>
        <attribute name="Main-Class" value="HelloProgram" />
      </manifest>
    </jar>
  </target>
</project>
```

### Gradle

Gradle si basa sui repository Maven dove sono riposte le librerie.

Gradle supporta Groovy o Kotlin come linguaggi di scripting. 
Approccio dichiarativo e fortemente basato su convenzioni: tutto quello che è già definito come standard non è necessario ridichiarlo un'altra volta.
Definisce un linguaggio specifico per le dipendenze rendendo semplice la loro gestione.
Permette di creare build multi-progetto.

Scala bene in complessità: permette di fare cose semplici senza usare le funzioni compresse. 
È estendibile tramite plugin che servono per trattare tool, situazioni, linguaggi legati solitamente al mondo Java.

#### Plugin

I plugin servono per trattare tool, situazioni, linguaggi definendo task e regole per lavorare più facilmente.

Il plugin Java defisce
- una serie di __sourceSet__, ovvero dove è presente il codice e le risorse. Principalmente sono:
  - `src/main/java`: sorgenti Java di produzione;
  - `src/main/resources`: risorse di produzione;
  - `src/test/java`: sorgenti Java di test;
  - `src/test/resources`: sorgenti di test.
- dei __task__, anche con dipendenze tra loro.

{% responsive_image path: 'assets/06_gradle-tasks.png' %}

Altri plugin sono 

#### Altri plugin
- application, per l'esecuzione;
- FindBugs, jacoco: per la verifica e la convalida;
- eclipse, idea: per integrazione con gli IDE;

## Bug tracking

È stato reso necessario nel mondo open source per via della numerosità dei contributi e della alta probabilità di avere segnalazioni duplicate.

Ci sono diversi strumenti, tra cui:
- `git-bug`: basato su Git;
- BugZilla;
- Scarab;
- GNATS;
- BugManager;
- Mantis.

### Bug workflow

{% responsive_image path: 'assets/06_bug-workflow.png' %}

L'obiettivo del bug tracking è avere più informazioni possibili su ogni bug per saperli riprodurre e quindi arrivare a una soluzione.

È importante verificare i bug una volta che l'_issue_ è stato aperto, in modo da poter confermare la sua esistenza e la completezza delle informazioni fornite.

Ci sono diversi modi per cui può essere chiuso un bug:
- __duplicate__;
- __wontfix__: o è una feature o è un errore talmente complesso da risolvere che non ne vale la pena, secondo i progettisti;
- __can't reproduce__;
- __fixed__ vs __fix verified__: nel primo stato il bug è stato fixato, nel secondo il fix è stato integrato in una release passando tutti gli step di verifica.