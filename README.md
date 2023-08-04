# La Porta D'acqua 2
This webapp will be developed with Angular (16+) and Ruby On Rails (7+) and Postgres(14+).

# Sviluppo
I'll write in italian here since I'm more confortable with it.

## Permessi
Non esisterà il concetto di ruolo, ma di "sudo mode".

L'utente avrà un attributo che sarà un timestamp del momento in cui è stato concesso il sudo mode l'ultima volta.
Alcune azioni richiederanno il sudo mode, che una volta concesso durerà 5/10 minuti, configurabile da app.yml.
La gestione del richiesto sudo mode potrebbe avvenire in questo modo:
1. L'utente richiede di effettuare un azione che richiede il sudo mode. (client fa richiesta al server)
2. Il server ritorna un codice http particolare, non standard, in cui specifica che questa azione richiede il sudo mode.
3. Il client, attraverso l'utilizzo di un interceptor, mostra una modal richiedente la password del sudo mode. L'interceptor si deve anche salvare la richiesta originale in modo da poterla ri-lanciare.
4. Una volta che l'utente avrà inserito la password nella modal del sudo-mode, la richiesta originale verrà ri-lanciata.

Nota: l'ideale sarebbe che il componente che ha lanciato la richiesta non sapesse nulla di tutto ciò: il componente fa una richiesta dal server e aspetta una risposta, ignorando completamente tutto lo scambio di informazioni avvenuto nel frattempo.
```ts
class DumbComponent {
  private readonly service = inject(DumbService);

  doSomething(): void {

    // Next will be triggered only when request is sent after user has inserted the correct password.
    // If user fails password for a couple times, request will be canceled.
    this.service.makeDangerousAction().subscribe(() => { /* ... */});
  }
}
```


## Impostazioni
Deve essere possibile gestire le impostazioni dinamicamente.
Alcune delle impostazioni saranno personali, altre globali. Tutte avranno un default.
Tutte dovranno essere validate lato server.

## Traduzioni
L'applicazione Angular dovrà essere interamente tradotta con l'utilizzo di `@angular/localize` e [Poeditor](https://poeditor.com), come stato fatto per [Kirpachov.com](https://kirpachov.com).

L'applicazione Angular dovrà disporre di una select per selezionare la lingua dei contenuti. La lingua desiderata dovrà essere salvata in un cookie ed in un servizio (oppure nello store di ngrx).

Nelle form di modifica degli elementi bisogna poter modificare in-loco le traduzioni. Di default si vedrà la lingua attuale dell'utente chiaramente.
Questo input si potrebbe pensare come un `tui-primitive-textfield` con sopra le varie lingue presenti, in forma di tab. Cliccando su ciascuna tab si selezionerà quella lingua e si potrà modificare il valore della traduzione.

## Menu
Per permettere la massima flessibilità, non vi saranno "Menu > Categorie > Piatti", ma solo "Categorie > Piatti".

Una categoria può contenere molti piatti.

Una categoria può contenere molte categorie.

Un piatto può comparire in molte categorie. <small>(Questo serve nei casi in cui un piatto identico sia presente, per esempio, sia nel menù di pranzo che di quello della cena, e si voglia fare in modo che ogni modifica sia effettiva su tutti gli elementi.)</small>

Ogni piatto e categoria potrà avere varie immagini. Le immagini devono avere un ordine.

## Immagini
Tutte le immagini di elementi come categorie, piatti e simili dovranno poter essere dinamiche.

Tutte le immagini dinamiche avranno varie "versioni":
Alcune con dimensioni specifiche, altre un po' sfocate, eccettera.

Il server dovrà poter generare runtime queste versioni e salvarsele.

| field | type | null | default |
| ------|------| ----- | --- |
| filename | string | false | |
| original | boolean | false | false |
| record_type | string | false ||
| record_id | integer | false ||


Il campo `original` indicherà proprio se il record in questione è il file originale oppure una "versione".