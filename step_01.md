
# HF - C.R.U.D NodeJS/MongoDB.

```
Beskrivelse: 
Vi opretter en simpel client side til vores node server.
```


## Forudsætning.

* Step 01 til 07 på node-serveren.
* Download client filerne og opret en mappe du kalder `node-server-client`
    * https://github.com/MCDM-Viborg-Documentation/hf-nodejs-crud-client/blob/main/materials/node-server-client-start-template.zip


# Step 1 : Opret forbindelse.

Start `node-server-client` projektet med `liveserver` på `index.html`

I første omgang skal du have startet din node-server.

På din node server skal vi have installeret en pakke så vores nye side kan tale sammen med vores server "croos-domain" altså påkryds af domæner/ip-adresser.

I `node-server` projektet åbn terminalen og installér pakken `cors`

```
npm install cors
```

I `server.js` lige efter vi definere `const expressServer = express();`

Der tilføjer vi `cors` som middleware/mellemmand ved hjælp af denne linie:

```JavaScript
expresServer.use(cors());
```



Hvis du ikke har ændret port, på node-serveren, fra port `3000`, *(se det i din terminal på serveren når du starter den.)* så skulle der allerede være hul igennem.

Ellers skal vi åbne `/src/scrips/user.service.js` filen og ændre port øverst i filen hvor vi definere `endpoint` stien.

```JavaScript
userservice.endpoint = 'http://localhost:3000/user';
```
Når det er gjort så skulle du gerne kunne benytte denne frontend til at Oprette, Læse, Opdatere og Slette.

## Overblik

Brug lidt tid på at kigge projektet igennem, både hvad angår struktur/filer og selve indholdet i js filerne.

**index.js**    
Er vores entry fil. Hvor vi starter vores funktioner.

**users.js**    
Er vores funktioner til at håndtere User input forme.

**user.service.js**     
Er der hvor vi udveksler data med vores server.




I `user.service.js` arbejder vi med form værdierne ved hjælp af `deconstruction`.

```JavaScript
// Destructure the elements object
const { firstname, surname, email, age, street, zip } = elements;
```

Da vi *meget let* kan trække alle vores formfelter ved at skrive:

```JavaScript
form.elements
```
Hvor `form.` er den `<form></form>` vi har modtaget og `.elements` er de elementer der er i formen altså `<inputs>`.

Nu trækker vi felterne ud ved hjælp af deres `name` attribut:

```Html
<input type="text" name="firstname" required/>
```

Så med:
```JavaScript
// Destructure the elements object
const { firstname, surname, email, age, street, zip } = elements;
```
Har vi fat i alle felter og ved at skrive `firstname.value` har vi værdien af det der står i input feltet.

Så vi opretter et `user` objekt som vi skal sende til `node-serveren`:

```
let user = {
        firstname: firstname.value,
        surname: surname.value,
        email: email.value,
        age: age.value,
        street: street.value,
        zip: zip.value
}
```

Herefter sender vi objektet til serveren

```JavaScript
// Send the user object to the server
return fetch(userservice.endpoint, { 
    method: 'POST', 
    body: JSON.stringify(user), 
    headers: { 'Content-Type': 'application/json' } 
}).then((response) => response.json()); 
```

Læg mærke til, at vi `JSON.stringify(user)` som skriver vores javascript objekt om til tekst. Vi sender en `header` der forklare det er `json` = `{ 'Content-Type': 'application/json' }` og vi benytte medtoden `POST`. Det er en vigtig detalje, da vores `fetch` kommer til at ligne hianden og *næsten* kun adskiller sig ved *metoderne* `POST`, `PUT`, `DELETE`, `GET`. Nøjagtig som på serveren og via `Postman`.

I `user.js` håndtere vi formene i hver sin metode men *stort set* ens.

```JavaScript
users.create = () => {

    const form = document.querySelector('#createForm');

    if(form)
    {
        form.addEventListener('submit', async (e) => {

            e.preventDefault();
            
            const result = await userservice.createUser(form.elements);
      
            console.log('Create Form Result', result);
           
            form.reset();
        });
    }

};
```

1. Vi forsøger at fange en form.
```JavaScript
const form = document.querySelector('#createForm');
```

Hvis formen findes på den side vi er på:
```JavaScript
if(form)
{
    ...
}
```

2. Så tilføjer vi en `eventListener` der lytter på om vi afsender formen:
```JavaScript
 form.addEventListener('submit', async (e) => {

    e.preventDefault();
    
    const result = await userservice.createUser(form.elements);

    console.log('Create Form Result', result);
    
    form.reset();

});
```

Når vi trykker på `send` knappen kalder vi denne funktion som sender `form.elements`
med `userservice.createUser(form.elements)` som beskrevet ovenfor trækker værdierne ud af form elementerne. Vi benytte asyn/await fordi vi *afventer* af `createData()` sender et svar tilbage.

Det svar vi får tilbage gemmer vi i `result` og udskriver i consollen.

Tilsidst *renser* vi formen `form.reset();`

## Opgaver.

Nu skal du til at håndtere to projekter.

Vi har vores `node-server` og vores `node-server-client`.

## 1. node-server-client

1. Udvid Update funktionaliteten så vi ikke kun kan opdatere medlemskab, men også
    * firstname
    * surname
    * age
    * street
    * zip

## 2. node-server-client & node-server

1. Tilføj et username og et password til vores `User.Model`
    * Gem bare password som ren tekst (for nu)
    * Skab hele tråden så vi kan opdatere og oprette:
        * username
        * password
    * Ingen regler endnu bare betragt begge felter som "firstname".
    * På `node-server-client` skal man under `read`, i listen kunne se `username`.


## Afslutning Step 01.

Nu har vi arbejdet med begge projekter og fået udvidet vores Model.

Inden vi skal arbejde mere med username og password har vi lige en vigtig opgave.

Gå til Step 02.
