# Peppi esimerkki

Tämän esimerkin on tarkoitus auttaa ohjelmistoprojektin pankkiautomaatin suunnittelussa ja toteutuksessa. Esimerkin aiheena on Peppi oppilasrekisteriä vastaavan ohjelmiston rakentaminen. Esimerkin sovellukseen on otettu vain pieni osuus Peppi järjestelemästä.

## Järjestelmän toiminnan kuvaus

Sovellukseen toteutetaan seuraavat toiminnot:

- Voidakseen katsoa henkilötietonsa opiskelijan on kirjauduttava sovellukseen.
- Voidakseen katsoa arvosanansa opiskelijan on kirjauduttava sovellukseen.
- Voidakseen ilmoittautua kurssille opiskelijan on kirjauduttava sovellukseen.
- Jos kirjautuminen onnistuu opiskelijalle avautuu valikko, josta hän voi valita joko katso henkilötiedot tai katso arvosanat.
- Mikäli kirjautuminen ei onnistu, palataan kirjautumisruudulle.

## Käyttötapauskaavio

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

## Viestiyhteyskaavio

Seuraavaksi luotiin viestiyhteyskaavio

```mermaid
sequenceDiagram
    actor Student
    participant UI as User Interface
    participant LC as LoginController
    participant DB as User Database

    Student->>UI: Enter credentials
    UI->>LC: Submit login request
    LC->>DB: Verify credentials
    DB->>LC: Return verification result
    LC->>UI: Show menu if successful
    UI->>Student: Display options
    LC->>UI: Return to login if unsuccessful

    Student->>UI: Select "View Personal Info"
    UI->>LC: Request info
    LC->>DB: Fetch info
    DB->>LC: Return info
    LC->>UI: Display info
    UI->>Student: Show info

    Student->>UI: Select "View Grades"
    UI->>LC: Request grades
    LC->>DB: Fetch grades
    DB->>LC: Return grades
    LC->>UI: Display grades
    UI->>Student: Show grades

    Student->>UI: Select "Register for Course"
    UI->>LC: Request registration
    LC->>DB: Register student
    DB->>LC: Confirm registration
    LC->>UI: Show confirmation
    UI->>Student: Display confirmation
```

## Käyttöönottokaavio

Seuraavaksi suunniteltiin ohjelmiston arkkitehtuuria. Päätettiin, että käytetään MySQL-tietokantaa, REST API tehdään käyttäen Node.js/Express.js alustaa ja käyttäjän sovellus tehtään C++ ohjelointikielellä käyttäen Qt-frameworkkiä. Tällä perusteella laadittiin käyttöönottokaavio

```mermaid
flowchart TD
    subgraph ClientMachine["Client Machine"]
        QtApp["Qt application"]
    end

    subgraph Server["Server"]
        RestAPI["REST API (Node.js/Express)"]
        Database[("Database (MySQL/MariaDB)")]
    end

    QtApp -->|"HTTP Requests"| RestAPI
    RestAPI -->|"Read/Write Operations"| Database
    Database -->|"Query Responses"| RestAPI
    RestAPI -->|"JSON Responses"| QtApp
```

## Komponenttikaavio

Seuraavaksi suunniteltiin ohjelmiston komponentit ja laadittiin komponenttikaavio

```mermaid
flowchart TD
    subgraph peppi["<<Qt>> peppi"]
        MainWindow["MainWindow"]
        Login_Qt["Login"]
        StudentInfo_Qt["StudentInfo"]
        MainWindow --> Login_Qt
        Login_Qt --> StudentInfo_Qt
    end

    subgraph backend["<<REST API>> backend"]
        RouteLogin["RouteLogin"]
        RouteStudent["RouteStudent"]
        RouteGrade["RouteGrade"]
        Student_model["Student_model"]
        Grade_model["Grade_model"]
        DatabaseJs["Database.js"]
        RouteLogin --> Student_model
        RouteStudent --> Student_model
        RouteGrade --> Grade_model
        Student_model --> DatabaseJs
        Grade_model --> DatabaseJs
    end

    subgraph mysql["<<MySQL>>"]
        peppidb[("peppidb")]
    end

    Login_Qt -->|"HTTP"| RouteLogin
    StudentInfo_Qt -->|"HTTP"| RouteStudent
    DatabaseJs --> peppidb
```

## Luokkakaavio

Seuraavaksi suunniteltiin ohjelmiston luokat ja laadittiin luokkakaaviot

**REST API:n luokkakaavio**

```mermaid
classDiagram
    class App {
        -router Router
        +authenticateToken(request, response, next) void
        +use(root) void
        +use(login) void
        +use(student) void
    }

    class Login {
        -router Router
        -bcrypt bcryptjs
        -jwt jsonwebtoken
        -dotenv dotenv
        -generateAccessToken(string) jsonwebtoken
        +POST() void
    }

    class RouterStudent {
        -router Router
        +GET() jsonArray
        +GET(username) jsonObject
        +POST(jsonArray) int
        +PUT(string, jsonArray) int
        +DELETE(string) int
    }

    class StudentModel {
        -query(string) void
        +getAll(callback) QueryResult
        +getOne(string, callback) QueryResult
        +add(jsonArray, callback) QueryResult
        +update(string, jsonArray, callback) QueryResult
        +delete(string, callback) QueryResult
    }

    App --> Login
    App --> RouterStudent
    RouterStudent --> StudentModel
    Login --> StudentModel
```

**Qt sovelluksen luokkakaavio**

```mermaid
classDiagram
    class MainWindow {
        -objLogin Login
        -on_btnStart_clicked() void
    }

    class Login {
        -postManager QNetworkAccessManager
        -reply QNetworkReply
        -response_data QByteArray
        -objStudentInfo StudentInfo
        -on_btnLogin_clicked() void
        -loginSlot(reply) void
    }

    class StudentInfo {
        -username QString
        -myToken QByteArray
        -gradeManager QNetworkAccessManager
        -reply QNetworkReply
        -response_data QByteArray
        -on_btnData_clicked() void
        -on_btnGrade_clicked() void
        -gradeSlot(reply) void
        +setUsername(newUsername) void
        +setMyToken(newMyToken) void
    }

    MainWindow *-- Login
    Login *-- StudentInfo
```

---

## Tietokannan suunnittelu

Aluksi tietokannan ER-kaaviota hahmoteltiin kynällä ja paperilla ja saatiin seuraavat kuvat:

![ER-kaavion luonnos](./UML/er_plan.png)

Kaaviota piirettiin siis niin pitkälle, että todettiin ettei monen-suhde-moneen yhteyksiä ole.

Tämän jälkeen tietokannan taulut luotiin MySQL-Workbench sovelluksella. Tauluihin merkittiin kentät, perusavaimet ja luotiin viiteavaimien avulla viite-eheys. Tämän jälkeen Workbenchillä generoitiin tietokanta ja ER-kaavio. Nyt tietokanta ja sen ER-malli ovat varmasti yhtäpitävät.

![ER-malli](./UML/er_model_final.png)

## Käyttöliittymän suunnittelu

Käyttöliittymää hahmoteltiin seuraavissa kuvissa. Tarkoitus olisi piirtää paremmat kuvat ennen toteutusta.

![Käyttöliittymän suunnitelma](./UML/frontend_plan.png)
