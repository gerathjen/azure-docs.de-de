---
title: 'Schnellstart: Verwenden von Java und JDBC mit Azure Database for PostgreSQL Flexible Server'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Java und JDBC mit Azure Database for PostgreSQL Flexible Server verwenden.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 1bb5cb11928b31356dc61d27cc0847e6c575050d
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288102"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-postgresql-flexible-server"></a>Schnellstart: Verwenden von Java und JDBC mit Azure Database for PostgreSQL Flexible Server

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die Java und [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) verwendet, um Informationen in [Azure Database for PostgreSQL Flexible Server](./index.yml) zu speichern bzw. daraus abzurufen.

JDBC ist die Standard-Java-API, um eine Verbindung mit herkömmlichen relationalen Datenbanken herzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) oder [Azure CLI](/cli/azure/install-azure-cli). Wir empfehlen Azure Cloud Shell, damit Sie automatisch angemeldet werden und Zugriff auf alle erforderlichen Tools erhalten.
- Ein unterstütztes [Java Development Kit](/azure/developer/java/fundamentals/java-support-on-azure), Version 8 (in Azure Cloud Shell enthalten).
- Das [Apache Maven](https://maven.apache.org/)-Buildtool

## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Sie verwenden Umgebungsvariablen, um Tippfehler zu minimieren und die Anpassung der folgenden Konfiguration für Ihre spezifischen Anforderungen zu erleichtern.

Richten Sie diese Umgebungsvariablen mithilfe der folgenden Befehle ein:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name des PostgreSQL-Servers. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<YOUR_POSTGRESQL_PASSWORD>`: Das Kennwort Ihres PostgreSQL-Datenbankservers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Java-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie nun mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Wir verwenden das Hilfsprogramm `jq`, um JSON-Daten anzuzeigen und besser lesbar zu machen. Dieses Hilfsprogramm wird standardmäßig unter [Azure Cloud Shell](https://shell.azure.com/) installiert. Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Erstellen einer Azure Database for PostgreSQL-Instanz

Sie erstellen zuerst einen verwalteten PostgreSQL-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von PostgreSQL-Servern finden Sie unter [Erstellen eines Azure Database for PostgreSQL-Servers im Azure-Portal](./quickstart-create-server-portal.md).

Führen Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl aus:

```azurecli
az postgres flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1s \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

[Treten Probleme auf? Informieren Sie uns darüber.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="configure-a-postgresql-database"></a>Konfigurieren einer PostgreSQL-Datenbank

Der von Ihnen zuvor erstellte PostgreSQL-Server enthält die leere Datenbank **postgres**. In dieser Schnellstartanleitung erstellen Sie mit den folgenden Schritten eine neue Datenbank mit dem Namen `demo`:

1. Installieren Sie eine lokale Instanz von [psql](https://www.postgresql.org/docs/current/static/app-psql.html), um eine Verbindung mit einem Azure PostgreSQL-Server herzustellen. 
2. Führen Sie den folgenden psql-Befehl aus, um eine Verbindung mit der Datenbank **postgres** auf Ihrem PostgreSQL-Server herzustellen:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=postgres
   ```

   Beispiel: 
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

3. Erstellen Sie eine neue Datenbank namens **demo**, indem Sie an der Eingabeaufforderung den folgenden Befehl eingeben:

    ```bash
    CREATE DATABASE demo;
    ```

[Treten Probleme auf? Informieren Sie uns darüber.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="create-a-new-java-project"></a>Erstellen eines neuen Java-Projekts

Erstellen Sie mit Ihrer bevorzugten IDE ein neues Java-Projekt, und fügen Sie im Stammverzeichnis eine `pom.xml`-Datei hinzu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

Dabei handelt es sich um eine [Apache Maven](https://maven.apache.org/)-Datei, die das Projekt für die Verwendung folgender Komponenten konfiguriert:

- Java 8
- Aktueller PostgreSQL-Treiber für Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>Vorbereiten einer Konfigurationsdatei für das Herstellen einer Verbindung mit Azure Database for PostgreSQL

Erstellen Sie eine Datei vom Typ *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo
password=$AZ_POSTGRESQL_PASSWORD
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_POSTGRESQL_PASSWORD` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

> [!NOTE]
> Sie fügen `?ssl=true&sslmode=require` an die Konfigurationseigenschaft `url` an, um den JDBC-Treiber anzuweisen, beim Herstellen einer Verbindung mit der Datenbank TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) zu verwenden. Die Verwendung von TLS mit Azure Database for PostgreSQL ist nicht nur zwingend erforderlich, sondern auch eine bewährte Sicherheitsmaßnahme.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Erstellen einer SQL-Datei zum Generieren des Datenbankschemas

Sie verwenden eine Datei vom Typ *src/main/resources/`schema.sql`* , um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei mit folgendem Inhalt:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Codieren der Anwendung

### <a name="connect-to-the-database"></a>Herstellen der Verbindung mit der Datenbank

Fügen Sie als Nächstes den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten auf bzw. aus Ihrem PostgreSQL-Server verwendet.

Erstellen Sie eine Datei vom Typ *src/main/java/DemoApplication.java*, die Folgendes enthält:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

[Treten Probleme auf? Informieren Sie uns darüber.](https://github.com/MicrosoftDocs/azure-docs/issues)

Dieser Java-Code verwendet die weiter oben erstellten Dateien *application.properties* und *schema.sql*, um eine Verbindung mit dem PostgreSQL-Server herzustellen und ein Schema zu erstellen, in dem Ihre Daten gespeichert werden.

Sie sehen, dass in dieser Datei Methoden zum Einfügen, Lesen, Aktualisieren und Löschen von Daten kommentiert wurden: Diese Methoden werden im verbleibenden Artikel codiert, und Sie können nacheinander die Auskommentierung aufheben.

> [!NOTE]
> Die Datenbankanmeldeinformationen werden in den Eigenschaften *user* und *password* der Datei *application.properties* gespeichert. Diese Anmeldeinformationen werden bei der Ausführung von `DriverManager.getConnection(properties.getProperty("url"), properties);` verwendet, da die Eigenschaftendatei als Argument übergeben wird.

Sie können diese Hauptklasse nun mit Ihrem bevorzugten Tool ausführen:

- Bei Verwendung Ihrer IDE sollten Sie mit der rechten Maustaste auf die Klasse *DemoApplication* klicken und diese ausführen können.
- Bei Verwendung von Maven können Sie die Anwendung mithilfe von `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` ausführen.

Die Anwendung sollte eine Verbindung mit Azure Database for PostgreSQL herstellen, ein Datenbankschema erstellen und die Verbindung dann trennen, wie in den Konsolenprotokollen gezeigt:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Closing database connection
```

### <a name="create-a-domain-class"></a>Erstellen einer Domänenklasse

Erstellen Sie neben der Klasse `DemoApplication` eine neue `Todo`-Java-Klasse, und fügen Sie den folgenden Code hinzu:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Bei dieser Klasse handelt es sich um ein Domänenmodell, das der `todo`-Tabelle zugeordnet ist, die Sie beim Ausführen des Skripts *schema.sql* erstellt haben.

### <a name="insert-data-into-azure-database-for-postgresql"></a>Einfügen von Daten in Azure Database for PostgreSQL

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der main-Methode die folgende Methode ein, um Daten in die Datenbank einzufügen:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Sie können die Auskommentierung der beiden folgenden Zeilen in der Methode `main` aufheben:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-postgresql"></a>Lesen von Daten aus Azure Database for PostgreSQL

Lesen Sie die zuvor eingefügten Daten, um zu überprüfen, ob der Code ordnungsgemäß funktioniert.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `insertData`-Methode die folgende Methode ein, um Daten aus der Datenbank zu lesen:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Sie können die Auskommentierung der folgenden Zeile nun in der Methode `main` aufheben:

```java
todo = readData(connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Closing database connection
```

### <a name="updating-data-in-azure-database-for-postgresql"></a>Aktualisieren von Daten in Azure Database for PostgreSQL

Aktualisieren Sie die zuvor eingefügten Daten.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `readData`-Methode die folgende Methode ein, um die Daten in der Datenbank zu aktualisieren:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Sie können die Auskommentierung der beiden folgenden Zeilen in der Methode `main` aufheben:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Closing database connection
```

### <a name="deleting-data-in-azure-database-for-postgresql"></a>Löschen von Daten in Azure Database for PostgreSQL

Löschen Sie die zuvor eingefügten Daten.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `updateData`-Methode die folgende Methode ein, um die Daten in der Datenbank zu löschen:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Sie können die Auskommentierung der folgenden Zeile nun in der Methode `main` aufheben:

```java
deleteData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Delete data
[INFO   ] Read data
[INFO   ] There is no data in the database!
[INFO   ] Closing database connection
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Glückwunsch! Sie haben eine Java-Anwendung erstellt, die JDBC zum Speichern und Abrufen von Daten in bzw. aus Azure Database for PostgreSQL verwendet.

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](../howto-migrate-using-export-and-import.md)
