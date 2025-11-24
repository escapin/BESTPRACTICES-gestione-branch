# Gestione progetti industriali

Questa guida stabilisce una serie di _best practices_ da seguire per garantire un ambiente di sviluppo pulito, 
un uso corretto del [GitFlow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) 
e una GitHistory lineare e pulita.


## Nomi branch

Per qualsiasi attività che si deve compiere (nuova funzionalità o hotfix) è buona pratica aprire 
prima una **ISSUE** per il relativo task da svolgere.


Per nuove funzionalità:
- feature/***task + codice issue***

ES: feature/destination-dataset-001

Per correzioni di anomalie:
- hotfix/***task + codice issue***

ES: hotfix/filters-planning-002


Il nome del task deve essere, il più possibile, un nome chiaro, autoesplicativo, 
in modo tale da facilitarne la lettura anche da persone terze.


## 1. Iniziare un nuovo branch

Staccare un branch dal branch di produzione (master).

```bash
git checkout master
git fetch -tp
git reset --hard origin/master    # cancella tutto ciò che è presente in locale su master e lo allinea al branch remoto
git checkout -b feature/nome
```

Stessa regola vale anche per i branch di **HOTFIX**.


## 2. Allineare branch da MASTER tramite riassegnazione (rebase)

```bash
git fetch -tp
git rebase origin/master
```

Risolvere eventuali conflitti di rebase (se presenti) e, una volta risolti, metterli in stage ed eseguire il seguente comando:

```bash
git rebase --continue
```

Una volta finiti tutti i passaggi per risolvere i conflitti, uscire dall'editor di testo 
(e.g., se vi digitare ***:wq*** e premere invio).


Alla fine del rebase, eseguire il seguente comando:

```bash
git push -f
```

L'opzione force '-f' è importante perché forza l'aggiornamento sul branch in `Remote` anche se `Local` e `Remote` 
non hanno lo stesso precursore. 

**ATTENZIONE** il rebase può essere effettuato solo dai branch da cui viene staccato il nuovo:
- es. 1: (master) --> (feature/*nome*). Su feature/*nome* si può fare il rebase da master perché deriva direttamente da esso.
- es. 2: (master) --> (feature/*nome*) --> (feature/*altroNome*). Su feature/*altroNome* **NON** si può fare il rebase da master, perché deriva da feature/*nome*. Per allinearlo a master bisogna prima fare il rebase su feature/*nome* e, una volta compiuto, effettuare il rebase su feature/*altroNome* dal suo branch genitore, quindi:

### 2.1 Posizionarsi su feature/*nome* e fare il rebase da master per prendere le modifiche:

```bash
git fetch -tp
git checkout feature/nome
git rebase origin/master
```

### 2.2 Risolvere eventuali conflitti e continuare il rebase come indicato poc'anzi e fare il push delle modifiche una volta terminato.

```bash
git push -f
```

### 2.3 Posizionarsi sul branch figlio ed effettuare il rebase dal branch padre.

```bash
git fetch -tp
git checkout feature/altroNome
git rebase origin/feature/nome
```

### 2.4 Risolvere eventuali conflitti e fare sempre il push delle modifiche come specificato ad inizio capitolo (2).


## 3. Rilasciare sul branch di sviluppo (develop)

Prima di rilasciare in SVIL o COLL, assicurarsi che il branch che si vuole rilasciare sia allineato con master, 
altrimenti seguire lo **STEP 2**.
Successivamente, allineare il branch di develop locale con quello in remoto.

```bash
git checkout develop
git fetch -tp
git reset --hard origin/develop
git merge origin/feature/nome
git push
```

Aprire merge request verso il branch di target e metterla in draft.

## Convenzione messaggio di commit

Per avere uno standard di lettura dei messaggi di commit, seguire quanto segue:

- ***sezione*** #***codice issue***: ***commento del commit***

ES: dataset #001: added filter section to dataset.

Per modifiche a componenti shared o core, seguire quanto segue:

- shared #***codice issue***: ***commento del commit***
- core #***codice issue***: ***commento del commit***
