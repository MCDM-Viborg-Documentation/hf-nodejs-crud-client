# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel client side til vores node server.
```

## Forudsætning.

* Step 02

# Step 3 : Update og Refactorering!

Nu har vi lavet CRUD. Og nu er vi igang.

Vi har været meget pædagoiske og delt alt op i CRUD for at understrege at vi skriver, læser, opdaterer og sletter. 

Men den navvngivning er ikke altid hensigtsmæssig men selve "flowet er". 

Så nu skal vi til at arbejde med ruter og flytte lidt rundt på vores funktioner for at gøre det mere over skueligt nu hvor det vokser.

Men vi har 2 ting vi skal frem til i denne opgave.

1. Få vores frontend til at være nemmere at arbejde med:
    1. Vores input felter er automatisk fyldt ud.

2. Tilpasse vores backend til at håndtere dette.

## Vi starter med først at ændre lidt ved vores server.

Hvorfor skulle vi omdøbe `users.js` til `user.js` - For at tilpasse vores ruter så de giver logisk mening.

`http://localhost:3000/user`

Alt hvad vi vil gør med dette endpoint har med én bruger at gøre.

`http://localhost:3000/users` <-- flertals user(S)

Alt hvad vi vil gør med dette endpoint har med flere brugerer at gøre. Det samme gælder alle andre endpoints vi har. 


### Så vi starter med at tilpasse vores backend for at imødekomme dette.

Start med `server.js` og lad os kigge på 

```JavaScript
// Server Endpoints (READ)
expresServer.get('/user', (req, res) => {

    user.read((code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });
    
});
```

Vores `users.read` funktion henter i virkeligheden ALLE brugere men er under vores `/user` rute.

Så lad os tilpasse denne rute til `/users` => `expresServer.get('/users', (req, res) => {`

`user.read` er i virkeligheden et dårligt navn fordi den ikke rigtig fortælle hvad den "reader"

`getAll` vil vil være mere fortællende. Samtigt er det ALLE brugere vi henter så vi burde have funtionen liggende i `users` istedet for `user`. Så kan vi skrive `users.getAll()` istedet for `user.read()`.

Det betyder at vores script vil ende med at se sådan ud:

```JavaScript
// Server Endpoints (READ)
expresServer.get('/users', (req, res) => {

    users.getAll((code, returnObj) => {

        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.status(code);
        res.send(returnObj);

    });
    
});
```
Men så er vi også nødt til at lave en ny fil som vi kalder `users.js` og lige som `user.js` skal den `requires` i toppen af `server.js`.

```JavaScript
const user = require('./user');
const users = require('./users');
```


I `users.js` skal vi have oprettet vores `getAll` funktion. Og det er i virkeligheden at flytte vores nuværende `read` funktion og omdøbe den til `getAll`og selvføleglig lave selve `users` modulet.

Nuværende `read` funktion:
```JavaScript
// User (READ)
user.read = (callback) => {

    console.log('user.read:');

    User.find({}).then( (users) => {

        callback(200, {'message' : 'Brugerene Hentet', 'data' : users});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugerene kunne ikke hentes', 'data' : err});
    
    });
    
};
```

Flyttes over i `users.js` og tilpasses modulet og det nye navn `getAll`:
```JavaScript
const UserModel = require('./models/user.model');
const users = {};

// Users (READ)
users.getAll = (callback) => {

    console.log('users.getAll:');

    UserModel.find({}).then( (users) => {

        callback(200, {'message' : 'Brugerene Hentet', 'data' : users});

    }).catch( (err) => {
            
        callback(200, {'message' : 'Brugerene kunne ikke hentes', 'data' : err});
    
    });
    
};

module.exports = users;
```

### Teste det i Klienten

For at teste om vores omstrukturering er gået godt. Så skal vi i klient koden have ændret en smule vi tager den lette vej i første omgang.

I vores `user.service.js` tilføjer vi en sti i toppen til vores `/users/` endpoint:

```JavaScript
userservice.endpointUsers = 'http://localhost:3000/users/';
```

og vi laver vores `userservice.read` function om til:

```JavaScript
// Read Users
userservice.getUsers = async () => { // <-- Læg mærke til vi har omdøbt til getUsers.

    return fetch(userservice.endpointUsers) // <-- Læg mærke til det nye endpoint
        .then((response) => response.json())

}
```

Så skal vi også tilpasse der hvor vi kalder `userservice.read` og nu skal kalde `userservice.getUsers` istedet for. Det gør vi i vores `users.read` funktion.

Så for at fuldende denne tilpasning omdøber vi nu `users.read` til `users.list` og husker at tilpasse det i `index.js`.

Hvis alt fungere som "normalt" så er vi godt på vej til at vi nu kan tage fat på mere specifikke endpoints. For nu skal vi have oprettet et endpoint der giver os mulighed for at hente en bruger ved at sende emailadresse som parameter. 

## Afslutning Step 03.

Det er meget normalt i "agil" udvikling at man "splitter" koden når projektet vokser. 
Det er også meget normal at man "refakturerer" når behovet for at logikken bliver mere overskuelig.

Fordelen er at man hele tiden optimere sin kode.

Når vi er færdige, fortsætter vi i Step 04 - Men der skal vi arbejde sammen på klassen.

Så afvent ordre.






