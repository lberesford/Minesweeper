# Minesweeper

// Lauren Beresford

// Rules: Click a tile and the number displayed will tell you
//		  the number of bombs in the immediately surrounding area.
//		  If a bomb is hit the board will reset and the console will
//		  display "Game Over :( "

import javafx.application.Application;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.layout.*;
import javafx.scene.paint.Color;
import javafx.scene.shape.Rectangle;
import javafx.scene.text.*;
import javafx.stage.Stage;
import java.util.*;


public class Minesweeper extends Application 
{
	// Declare instance variables
    private int tileSize = 40;
    private int width = 800;
    private int height = 800;

    // Creates rectangles
    private int xTiles = width / tileSize;
    private int yTiles = height / tileSize;

    private Tile[][] grid = new Tile[xTiles][yTiles];
    private Scene scene;

    // Creates the grid and bombs
    private Parent makeContent() 
    {
        Pane root = new Pane();
        root.setPrefSize(width, height);

        // Adds the tile to the grid
        for (int i = 0; i < yTiles; i++) 
        {
            for (int j = 0; j < xTiles; j++) 
            {
                Tile tile = new Tile(j, i, Math.random() < 0.2);

                grid[j][i] = tile;
                
                root.getChildren().add(tile);
            }
        }

        // Main game play
        for (int i = 0; i < yTiles; i++)
        {
            for (int j = 0; j < xTiles; j++) 
            {
                Tile tile = grid[j][i];

                // Restarts for loop if bomb is hit
                if (tile.hasBomb)
                {
                    continue;
                }

                // Counts the number of bombs in surrounding area
                long bombs = getSurroundingTiles(tile).stream().filter(t -> t.hasBomb).count();

                // Displays number of bombs
                if (bombs > 0)
                {
                    tile.text.setText(String.valueOf(bombs));
                }
            }
        }

        return root;
    }

    // Gets the surrounding tiles
    private List<Tile> getSurroundingTiles(Tile tile) 
    {
        List<Tile> neighbors = new ArrayList<>();

        // Puts coordinates for neighboring tiles in an array 
        int[] points = new int[] 
        {
              -1, -1,
              -1, 0,
              -1, 1,
              0, -1,
              0, 1,
              1, -1,
              1, 0,
              1, 1
        };

        for (int i = 0; i < points.length; i++) 
        {
            int dx = points[i];
            int dy = points[++i];

            int newX = tile.x + dx;
            int newY = tile.y + dy;

            if (newX >= 0 && newX < xTiles && newY >= 0 && newY < yTiles) 
            {
                neighbors.add(grid[newX][newY]);
            }
        }

        return neighbors;
    }

    // Creates the tiles and layout
    private class Tile extends StackPane 
    {
        private int x, y;
        private boolean hasBomb;
        private boolean isEmpty = false;

        private Rectangle border = new Rectangle(tileSize - 2, tileSize - 2);
        private Text text = new Text();

        public Tile(int x, int y, boolean hasBomb) 
        {
            this.x = x;
            this.y = y;
            this.hasBomb = hasBomb;

            border.setStroke(Color.RED);
            
            text.setText(hasBomb? "X" : "");
            text.setVisible(false);

            // Adds everything to the pane
            getChildren().addAll(border, text);

            setTranslateX(x * tileSize);
            setTranslateY(y * tileSize);

            // Event handler
            setOnMouseClicked(e -> empty());

        }

        // Manages if bomb is hit or spot is empty
        public void empty() 
        {
            if (hasBomb) 
            {
               System.out.println("Game Over :( ");
               scene.setRoot(makeContent());
               return;
            }
            
            if (isEmpty)
            {
                return;
            }

            isEmpty = true;
            text.setVisible(true);
            border.setFill(null);

            // References method open in class Tile
            if (text.getText().isEmpty()) 
            {
                getSurroundingTiles(this).forEach(Tile::empty);
            }
        }
    }
       // Sets the stage for the program
	   public void start(Stage stage) throws Exception 
	    {
	        scene = new Scene(makeContent());
	        stage.setScene(scene);
	        stage.show();
	    }

	    public static void main(String[] args) 
	    {
	        launch(args);
	    }
}
