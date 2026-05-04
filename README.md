<h1>Peppi esimerkki</h1>

<p>Tämän esimerkin on tarkoitus auttaa ohjelmistoprojektin pankkiautomaatin suunnittelussa ja toteutuksessa. Esimerkin aiheena on Peppi oppilasrekisteriä vastaavan ohjelmiston rakentaminen. Esimerkin sovellukseen on otettu vain pieni osuus Peppi järjestelemästä.</p>

<h2>Järjestelmän toiminnan kuvaus</h2>
<p>
    Sovellukseen toteutetaan seuraavat toiminnot:
    <ul>
    <li>Voidakseen katsoa henkilötietonsa opiskelijan on kirjauduttava sovellukseen.</li>
    <li>Voidakseen katsoa arvosanansa opiskelijan on kirjauduttava sovellukseen.</li>
    <li>Voidakseen ilmoittautua kurssille opiskelijan on kirjauduttava sovellukseen.</li>
    <li>Jos kirjautuminen onnistuu opiskelijalle avautuu valikko, josta hän voi valita joko katso henkilötiedot tai katso arvosanat.</li>
    <li>Mikäli kirjautuminen ei onnistu, palataan kirjautumisruudulle.</li>
    </ul>
</p>

<h2>Käyttötapauskaavio</h2>
<p>
```mermaid
flowchart TD
    Student(["👤 Student"])

    subgraph Application
        UC_Login["Log in"]
        UC_ViewInfo["View personal information"]
        UC_ViewGrades["View grades"]
        UC_RegisterCourse["Register for course"]
    end

    Student --> UC_Login
    UC_Login -->|"if successful"| UC_ViewInfo
    UC_Login -->|"if successful"| UC_ViewGrades
    UC_Login -->|"if successful"| UC_RegisterCourse
    UC_Login -->|"if unsuccessful, return to login"| UC_Login
```
</p>

<h2>Viestiyhteyskaavio</h2>
<p>
Seuraavaksi luotiin viestiyhteyskaavio
<br><img src="./UML/mySequence.png">
</p>

<h2>Käyttöönottokaavio</h2>
<p>
Seuraavaksi suunniteltiin ohjelmiston arkkitehtuuria. Päätettiin, että käytetään MySQL-tietokantaa, REST API tehdään käyttäen Node.js/Express.js alustaa ja käyttäjän sovellus tehtään C++ ohjelointikielellä käyttäen Qt-frameworkkiä. Tällä perusteella laadittiin käyttöönottokaavio
<br><img src="./UML/myDeployment.png">
</p>

<h2>Komponenttikaavio</h2>
<p>
Seuraavaksi suunniteltiin ohjelmiston komponentit ja laadittiin komponenttikaavio
<br><img src="./UML/myComponent.png">
</p>

<h2>Luokkakaavio</h2>
<p>
Seuraavaksi suunniteltiin ohjelmiston luokat ja laadittiin luokkakaaviot
</p>
<b>REST API:n luokkakaavio</b> <br>
<br><img src="./UML/myRestApiClass.png">
</p>
</p>
<b>Qt sovelluksen luokkakaavio</b> <br>
<br><img src="./UML/myPeppiClass.png">
</p>
<hr>
<h2>Tietokannan suunnittelu</h2>
<p>
Aluksi tietokannan ER-kaaviota hahmoteltiin kynällä ja paperilla ja saatiin seuraavat kuvat:
<br>
<img src="./UML/er_plan.png">
<br>
Kaaviota piirettiin siis niin pitkälle, että todettiin ettei monen-suhde-moneen yhteyksiä ole.
</p>
<p>
    Tämän jälkeen tietokannan taulut luotiin MySQL-Workbench sovelluksella. Tauluihin merkittiin kentät, perusavaimet ja luotiin viiteavaimien avulla viite-eheys. Tämän jälkeen Workbenchillä generoitiin tietokanta ja ER-kaavio. Nyt tietokanta ja sen ER-malli ovat varmasti yhtäpitävät.
<br>
<img src="./UML/er_model_final.png">
<br>
</p>
<h2>Käyttöliittymän suunnittelu</h2>
<p>
Käyttöliittymää hahmoteltiin seuraavissa kuvissa. Tarkoitus olisi piirtää paremmat kuvat ennen toteutusta. 
<br>
<img src="./UML/frontend_plan.png">
<br>  
</p>

