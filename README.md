package demo_jdbc;

import demo_jdbc.models.Driver;
import demo_jdbc.respositories.DriverResult;
import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.scene.Scene;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.stage.Stage;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Main extends Application {

    private ObservableList<DriverResult> driverData = FXCollections.observableArrayList();

    @Override
    public void start(Stage primaryStage) throws Exception {
        // Cargar datos del archivo CSV
        loadDataFromCSV();

        // Crear TableView y configurar columnas
        TableView<DriverResult> tableView = new TableView<>();
        tableView.setItems(driverData);

        TableColumn<DriverResult, Integer> idColumn = new TableColumn<>("Driver ID");
        idColumn.setCellValueFactory(new PropertyValueFactory<>("driverId"));

        TableColumn<DriverResult, String> refColumn = new TableColumn<>("Driver Ref");
        refColumn.setCellValueFactory(new PropertyValueFactory<>("driverRef"));

        TableColumn<DriverResult, Integer> numberColumn = new TableColumn<>("Number");
        numberColumn.setCellValueFactory(new PropertyValueFactory<>("number"));

        TableColumn<DriverResult, String> codeColumn = new TableColumn<>("Code");
        codeColumn.setCellValueFactory(new PropertyValueFactory<>("code"));

        TableColumn<DriverResult, String> forenameColumn = new TableColumn<>("Forename");
        forenameColumn.setCellValueFactory(new PropertyValueFactory<>("forename"));

        TableColumn<DriverResult, String> surnameColumn = new TableColumn<>("Surname");
        surnameColumn.setCellValueFactory(new PropertyValueFactory<>("surname"));

        TableColumn<DriverResult, String> dobColumn = new TableColumn<>("Date of Birth");
        dobColumn.setCellValueFactory(new PropertyValueFactory<>("dob"));

        TableColumn<DriverResult, String> nationalityColumn = new TableColumn<>("Nationality");
        nationalityColumn.setCellValueFactory(new PropertyValueFactory<>("nationality"));

        TableColumn<DriverResult, String> urlColumn = new TableColumn<>("URL");
        urlColumn.setCellValueFactory(new PropertyValueFactory<>("url"));

        // Añadir columnas al TableView
        tableView.getColumns().addAll(idColumn, refColumn, numberColumn, codeColumn, forenameColumn, surnameColumn,
                dobColumn, nationalityColumn, urlColumn);

        // Crear escena y mostrar ventana
        Scene scene = new Scene(tableView, 1000, 600);
        primaryStage.setTitle("Drivers Table");
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    private void loadDataFromCSV() {
        String csvFile = "C:\\Users\\User\\Desktop\\SIUG\\3er Semestre\\PAG\\Proyecto\\Proyecto Integrado.zip_expanded\\Proyecto Integrado\\proyecto_integrador\\formula1\\drivers.csv"; // Ruta al archivo CSV
        String line = "";
        String csvSplitBy = ";"; // Separador de columnas en el CSV

        try (BufferedReader br = new BufferedReader(new FileReader(csvFile))) {
            // Leer la primera línea (encabezados) y omitirla
            br.readLine();

            // Leer el resto del archivo línea por línea
            while ((line = br.readLine()) != null) {
                String[] data = line.split(csvSplitBy);

                // Asegurarse de que hay suficientes campos en la línea
                if (data.length >= 9) {
                    // Crear objeto DriverResult y agregarlo a la lista
                    DriverResult driver = new DriverResult(
                            Integer.parseInt(data[0].trim()),    // driverId
                            data[1].trim(),                     // driverRef
                            Integer.parseInt(data[2].trim()),    // number
                            data[3].trim(),                     // code
                            data[4].trim(),                     // forename
                            data[5].trim(),                     // surname
                            data[6].trim(),                     // dob
                            data[7].trim(),                     // nationality
                            data[8].trim()                      // url
                    );
                    driverData.add(driver);
                } else {
                    System.out.println("Número incorrecto de campos en la línea: " + line);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } catch (NumberFormatException e) {
            System.out.println("Error al convertir número en línea: " + line);
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        launch(args);
    }
}


package demo_jdbc.respositories;

import javafx.beans.property.SimpleIntegerProperty;
import javafx.beans.property.SimpleStringProperty;

public class DriverResult {
    private final SimpleIntegerProperty driverId;
    private final SimpleStringProperty driverRef;
    private final SimpleIntegerProperty number;
    private final SimpleStringProperty code;
    private final SimpleStringProperty forename;
    private final SimpleStringProperty surname;
    private final SimpleStringProperty dob;
    private final SimpleStringProperty nationality;
    private final SimpleStringProperty url;

    public DriverResult(int driverId, String driverRef, int number, String code, String forename, String surname,
            String dob, String nationality, String url) {
        this.driverId = new SimpleIntegerProperty(driverId);
        this.driverRef = new SimpleStringProperty(driverRef);
        this.number = new SimpleIntegerProperty(number);
        this.code = new SimpleStringProperty(code);
        this.forename = new SimpleStringProperty(forename);
        this.surname = new SimpleStringProperty(surname);
        this.dob = new SimpleStringProperty(dob);
        this.nationality = new SimpleStringProperty(nationality);
        this.url = new SimpleStringProperty(url);
    }

    // Getters
    public int getDriverId() {
        return driverId.get();
    }

    public SimpleIntegerProperty driverIdProperty() {
        return driverId;
    }

    public String getDriverRef() {
        return driverRef.get();
    }

    public SimpleStringProperty driverRefProperty() {
        return driverRef;
    }

    public int getNumber() {
        return number.get();
    }

    public SimpleIntegerProperty numberProperty() {
        return number;
    }

    public String getCode() {
        return code.get();
    }

    public SimpleStringProperty codeProperty() {
        return code;
    }

    public String getForename() {
        return forename.get();
    }

    public SimpleStringProperty forenameProperty() {
        return forename;
    }

    public String getSurname() {
        return surname.get();
    }

    public SimpleStringProperty surnameProperty() {
        return surname;
    }

    public String getDob() {
        return dob.get();
    }

    public SimpleStringProperty dobProperty() {
        return dob;
    }

    public String getNationality() {
        return nationality.get();
    }

    public SimpleStringProperty nationalityProperty() {
        return nationality;
    }

    public String getUrl() {
        return url.get();
    }

    public SimpleStringProperty urlProperty() {
        return url;
    }
}

package demo_jdbc.models;

public class Driver {
    private int driverId;
    private String driverRef;
    private int number;
    private String code;
    private String forename;
    private String surname;
    private String dob;
    private String nationality;
    private String url;

    public Driver(int driverId, String driverRef, int number, String code, String forename, String surname, String dob,
            String nationality, String url) {
        this.driverId = driverId;
        this.driverRef = driverRef;
        this.number = number;
        this.code = code;
        this.forename = forename;
        this.surname = surname;
        this.dob = dob;
        this.nationality = nationality;
        this.url = url;
    }

    // Getters and Setters
    public int getDriverId() {
        return driverId;
    }

    public void setDriverId(int driverId) {
        this.driverId = driverId;
    }

    public String getDriverRef() {
        return driverRef;
    }

    public void setDriverRef(String driverRef) {
        this.driverRef = driverRef;
    }

    public int getNumber() {
        return number;
    }

    public void setNumber(int number) {
        this.number = number;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    public String getForename() {
        return forename;
    }

    public void setForename(String forename) {
        this.forename = forename;
    }

    public String getSurname() {
        return surname;
    }

    public void setSurname(String surname) {
        this.surname = surname;
    }

    public String getDob() {
        return dob;
    }

    public void setDob(String dob) {
        this.dob = dob;
    }

    public String getNationality() {
        return nationality;
    }

    public void setNationality(String nationality) {
        this.nationality = nationality;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }
    
    // Override toString() for debugging purposes
    @Override
    public String toString() {
        return "Driver [driverId=" + driverId + ", driverRef=" + driverRef + ", number=" + number + ", code=" + code
                + ", forename=" + forename + ", surname=" + surname + ", dob=" + dob + ", nationality=" + nationality
                + ", url=" + url + "]";
    }
}


![Imagen de WhatsApp 2024-07-08 a las 18 10 00_547ffdf0](https://github.com/Timador/TableView-Drivers/assets/168133781/7899fe2c-a0f6-49d2-8cf9-8e010ae3de6c)
