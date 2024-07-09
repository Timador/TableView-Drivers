//DRIVERS RESULTS
package demo_jdbc.repositories;

import javafx.beans.property.SimpleIntegerProperty;
import javafx.beans.property.SimpleStringProperty;

public class DriverResult {
    private final SimpleStringProperty driverName;
    private final SimpleIntegerProperty wins;
    private final SimpleIntegerProperty totalPoints;
    private final SimpleIntegerProperty rank;

    public DriverResult(String driverName, int wins, int totalPoints, int rank) {
        this.driverName = new SimpleStringProperty(driverName);
        this.wins = new SimpleIntegerProperty(wins);
        this.totalPoints = new SimpleIntegerProperty(totalPoints);
        this.rank = new SimpleIntegerProperty(rank);
    }

    public String getDriverName() {
        return driverName.get();
    }

    public int getWins() {
        return wins.get();
    }

    public int getTotalPoints() {
        return totalPoints.get();
    }

    public int getRank() {
        return rank.get();
    }
}




//MAIN
package demo_jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import demo_jdbc.repositories.DriverResult;
import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.scene.Scene;
import javafx.scene.control.ComboBox;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class Main extends Application {

    private ObservableList<DriverResult> driverResults = FXCollections.observableArrayList();
    private TableView<DriverResult> tableView = new TableView<>();
    private ComboBox<Integer> yearComboBox = new ComboBox<>();

    @Override
    public void start(Stage primaryStage) throws Exception {
        // Cargar los años disponibles desde la base de datos
        loadYearsFromDatabase();

        // Configurar ComboBox
        yearComboBox.setOnAction(event -> loadDataFromDatabase(yearComboBox.getValue()));

        // Crear columnas de TableView
        TableColumn<DriverResult, String> nameColumn = new TableColumn<>("Driver Name");
        nameColumn.setCellValueFactory(new PropertyValueFactory<>("driverName"));

        TableColumn<DriverResult, Integer> winsColumn = new TableColumn<>("Wins");
        winsColumn.setCellValueFactory(new PropertyValueFactory<>("wins"));

        TableColumn<DriverResult, Integer> pointsColumn = new TableColumn<>("Total Points");
        pointsColumn.setCellValueFactory(new PropertyValueFactory<>("totalPoints"));

        TableColumn<DriverResult, Integer> rankColumn = new TableColumn<>("Rank");
        rankColumn.setCellValueFactory(new PropertyValueFactory<>("rank"));

        tableView.getColumns().addAll(nameColumn, winsColumn, pointsColumn, rankColumn);
        tableView.setItems(driverResults);

        // Crear layout y agregar componentes
        VBox vbox = new VBox(yearComboBox, tableView);
        Scene scene = new Scene(vbox, 800, 600);

        primaryStage.setTitle("Driver Results");
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    private void loadYearsFromDatabase() {
        String url = "jdbc:postgresql://localhost:2003/formula1";
        String user = "postgres";
        String password = "postgresql.2003";
        String query = "SELECT DISTINCT year FROM races ORDER BY year";

        try {
            Class.forName("org.postgresql.Driver");
            Connection conn = DriverManager.getConnection(url, user, password);
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery(query);

            while (rs.next()) {
                int year = rs.getInt("year");
                yearComboBox.getItems().add(year);
            }

            rs.close();
            stmt.close();
            conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void loadDataFromDatabase(int year) {
        driverResults.clear();
        String url = "jdbc:postgresql://localhost:2003/formula1";
        String user = "postgres";
        String password = "postgresql.2003";
        String query = "SELECT * FROM ("
                + " SELECT r.year, d.forename || ' ' || d.surname AS driver_name, "
                + " COUNT(CASE WHEN res.position = 1 THEN 1 END) AS wins, "
                + " SUM(res.points) AS total_points, "
                + " ROW_NUMBER() OVER (PARTITION BY r.year ORDER BY SUM(res.points) DESC, COUNT(CASE WHEN res.position = 1 THEN 1 END) DESC) AS rank "
                + " FROM results res "
                + " JOIN races r ON res.raceid = r.raceid "
                + " JOIN drivers d ON res.driverid = d.driverid "
                + " WHERE r.year = " + year + " "
                + " GROUP BY r.year, d.driverid, d.forename, d.surname"
                + ") AS ranked_results "
                + "ORDER BY year, rank;";

        try {
            Class.forName("org.postgresql.Driver");
            Connection conn = DriverManager.getConnection(url, user, password);
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery(query);

            while (rs.next()) {
                String driverName = rs.getString("driver_name");
                int wins = rs.getInt("wins");
                int totalPoints = rs.getInt("total_points");
                int rank = rs.getInt("rank");

                DriverResult result = new DriverResult(driverName, wins, totalPoints, rank);
                driverResults.add(result);
            }

            rs.close();
            stmt.close();
            conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        launch(args);
    }
}




![image](https://github.com/Timador/TableView-Drivers/assets/168133781/f09a9a96-ee39-4ef5-b595-ba3fa09e95bb)





