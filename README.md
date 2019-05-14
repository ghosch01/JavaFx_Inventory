# JavaFx_Inventory
Simplistic FileIO project capable of rewriting a .txt file.
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.Scanner;

import application.Animal;
import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.scene.text.Text;
import javafx.stage.Stage;

public class ZooApp extends Application {
	Scene scene1, scene2, scene3;
	Button button1;
	Button btn2;
	Button btn3;
	Button btn4;
	Button btn5;

	Label TextLabel;
	TextField aTextField;
	TextField aTextField1;
	TextField aTextField2;
	String inAnimal;
	String aCt;
	String aEat;
	Stage window;

	@Override
	public void start(Stage primaryStage) throws Exception {

		window = primaryStage;
		BorderPane bp = new BorderPane(); // add

		GridPane gp = createGridPane();
		bp.setCenter(gp); // Add
		Label l = new Label("Zoo Inventory"); // add
		l.setId("bigLabel"); // Add
		bp.setTop(l); // Add
		bp.setAlignment(l, Pos.CENTER); // Add
		bp.setMargin(bp, new Insets(66, 12, 12, 12)); // optional

		button1.setOnAction(e -> {
			inAnimal = aTextField.getText();
			String inFile = "res/zoo.txt";
			Animal_Bindable2 theAnimal = getAnimalFromFile(inFile, inAnimal);

			createNewScene(theAnimal);
			window.setScene(scene2);
		});
		btn3.setOnAction(e -> {
			System.out.printf("FLAG: This far");
			BorderPane edit = new BorderPane();
			GridPane gpEdit = createGridPane2();
			String msg = "";
			String msg1 = "";

			if (aTextField.getText() != null && !aTextField.getText().isEmpty()) {
				String inFile = "res/zoo.txt";
				inAnimal = aTextField.getText();
				Animal_Bindable2 theAnimal = getAnimalFromFile(inFile, inAnimal);
				if (theAnimal != null) {
					System.out.printf("Name = %s", theAnimal.getName());
					msg = theAnimal.getCt().toString();
					msg1 = theAnimal.getEats();
				} else {
					System.out.printf("Could not find %s", inAnimal);
//					msg = "Did not find animal: " + theAnimal.getName();
				}
				aTextField1.setText(msg);
				aTextField2.setText(msg1);

			} else {
				System.out.printf("FLAG 2: this far");
			}
			btn4 = backToScene();
			btn5 = saveFile();

			edit.setCenter(gpEdit); // Add
			Label zEdit = new Label("Zoo Edit");
			zEdit.setId("bigLabel");
			edit.setTop(zEdit);
			edit.setAlignment(zEdit, Pos.CENTER);
			edit.setPrefSize(250, 300);

			scene3 = new Scene(edit, 250, 300);
			scene3.getStylesheets().add("myStyle.css");
			window.setScene(scene3);

		});

		scene1 = new Scene(bp, 250, 400);
		scene1.getStylesheets().add("myStyle.css");

		window.setScene(scene1);
		window.setTitle("Zoo Table");
		window.show();
	}

	private Button backToScene() {
		btn4.setOnAction(e -> {
			window.setScene(scene1);
		});
		return null;
	}

	private Button saveFile() {
		btn5.setOnAction(e -> {
			try {
				executeSave();
			} catch (IOException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
		});
		return null;
	}

	private void executeSave() throws IOException {
		String iPath = "res/zoo.txt";
		ArrayList<String> fData = getDataFromFile(iPath);
		ArrayList<String> oData = editAnimal(fData, aTextField, aTextField1, aTextField2);
		String oPath = "res/zoo.txt";
		outFile(oPath, oData);
	}

	private static ArrayList<String> editAnimal(ArrayList<String> fData, TextField aText, TextField aText1,
			TextField aText2) {

		ArrayList<String> retData = new ArrayList<String>();

		for (String row : fData) {
			String toks[] = row.split(" ");

			if (toks[0].equalsIgnoreCase(aText.getText())) {
				int aCount = Integer.parseInt(toks[1]);
				String aEat = String.valueOf(toks[2]);
				String aName = toks[0];
				aCount = Integer.parseInt(aText1.getText());
				aEat = aText2.getText();
				String fixedRow = aName + " " + aCount + " " + aEat;

				retData.add(fixedRow);
			} else {
				retData.add(row);
			}
		}

		return retData;
	}

	private static ArrayList<String> getDataFromFile(String inFile) {
		File file = new File(inFile);
		ArrayList<String> oData = new ArrayList<String>();
		try {
			Scanner sc = new Scanner(file);
			while (sc.hasNextLine()) {

				String row = sc.nextLine();
				oData.add(row);
			}
			sc.close();
		} catch (FileNotFoundException e) {
			System.out.printf("\n--File Not Found:%s", inFile);
			e.printStackTrace();
		}
		return oData;
	}

	public static void outFile(String path, ArrayList<String> fData) throws IOException {
		PrintWriter outputStream = null;
		int nLines = 0;

		File file = new File(path);
		if (!file.exists()) {
			System.out.printf("\nError file:%s does not exist", path);
			if (file.createNewFile()) {
				System.out.printf("\nFile successfully created", path);
			} else {
				System.out.printf("\nError ... File cannot be created", path);
				System.exit(1);
			}

		}
		try {
			outputStream = new PrintWriter(new FileWriter(path));
			for (String row : fData) {
				outputStream.println(row);
				nLines++;
			}
		} finally {
			if (outputStream != null) {
				outputStream.close();
			}
		}

	}

	private Animal_Bindable2 getAnimalFromFile(String inFile, String inAnimal2) {

		File file = new File(inFile);
		Animal_Bindable2 retAnimal = null;

		try {
			Scanner sc = new Scanner(file);
			while (sc.hasNext()) {

				String name = sc.next();

				int ct = sc.nextInt();
				String eats = sc.next();
				if (name.equalsIgnoreCase(inAnimal2)) {
					retAnimal = new Animal_Bindable2(name, ct, eats);

					break;
				} else {
					System.out.printf("\n-- FLAG!------name:%s ct:%s e:%s inAnimal:%s", name, ct, eats, inAnimal2);

				}

			}
			sc.close();
		} catch (FileNotFoundException e) {
			System.out.printf("\n--File Not Found:%s", inFile);
			e.printStackTrace();
		}
		return retAnimal;
	}

	private void createNewScene(Animal_Bindable2 inAnimal) {
		Button btn2 = new Button("Back");
		BorderPane layout2 = new BorderPane();
		layout2.setPrefSize(100, 200);
		TableView<Animal_Bindable2> aniTableView = new TableView<>();
		aniTableView.setPrefWidth(100);
		aniTableView.setItems(getAnimals(inAnimal));
		TableColumn<Animal_Bindable2, String> nameCol = new TableColumn<>("Name");
		nameCol.setCellValueFactory(new PropertyValueFactory<Animal_Bindable2, String>("Name"));

		TableColumn<Animal_Bindable2, String> ctCol = new TableColumn<>("Ct");
		ctCol.setCellValueFactory(new PropertyValueFactory<Animal_Bindable2, String>("Ct"));
		TableColumn<Animal_Bindable2, String> eatsCol = new TableColumn<>("Eats");
		eatsCol.setCellValueFactory(new PropertyValueFactory<Animal_Bindable2, String>("Eats"));
		aniTableView.getColumns().addAll(nameCol, ctCol, eatsCol);
		layout2.setCenter(aniTableView);
		layout2.setBottom(btn2);

		scene2 = new Scene(layout2, 300, 600);
		scene2.getStylesheets().add("myStyle.css");
		window.setScene(scene1);
		btn2.setOnAction(e -> {
			window.setScene(scene1);
		});

	}

	private ObservableList<Animal_Bindable2> getAnimals(Animal_Bindable2 animal) {
		ObservableList<Animal_Bindable2> ani = FXCollections.observableArrayList();
		ani.add(animal);

		return ani;
	}

	private GridPane createGridPane() {

		Text aText = new Text("Animal:");

		aTextField = new TextField();

		button1 = new Button("Submit");
		btn3 = new Button("Edit");

		GridPane gp = new GridPane();
		gp.add(aText, 0, 0);
		gp.add(aTextField, 1, 0);

		gp.add(button1, 0, 2);
		gp.add(btn3, 1, 2);
		gp.setVgap(5);
		gp.setHgap(5);
		gp.setAlignment(Pos.CENTER);

		return gp;
	}

	private GridPane createGridPane2() {
		inAnimal = aTextField.getText();

		TextLabel = new Label(inAnimal);
		Text aText1 = new Text("Count:");
		Text aText2 = new Text("Eats:");
		aTextField1 = new TextField();
		aTextField2 = new TextField();

		btn4 = new Button("Back");
		btn5 = new Button("Save");

		GridPane gp = new GridPane();
		gp.add(TextLabel, 0, 0);
		gp.add(aText1, 1, 1);
		gp.add(aText2, 1, 2);
		gp.add(aTextField1, 2, 1);
		gp.add(aTextField2, 2, 2);

		gp.add(btn4, 2, 4);
		gp.add(btn5, 2, 3);
		gp.setVgap(5);
		gp.setHgap(5);
		gp.setAlignment(Pos.CENTER);
		return gp;
	}

	public static void main(String[] args) {
		launch(args);
	}
}
