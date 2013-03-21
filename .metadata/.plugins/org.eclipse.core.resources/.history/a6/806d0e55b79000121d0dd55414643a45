package ClueGame;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Map;
import java.util.Scanner;
import java.util.Set;

public class Board {
	private ArrayList<BoardCell> cells;
	private HashMap<Character, String> rooms;
	private int numRows, numColumns;
	private String boardFile, roomFile;
	private HashSet<BoardCell> targets;
	private boolean[] visited;
	private Map<Integer, LinkedList<Integer>> adjLists;
	private LinkedList<BoardCell> adjacencies;
	private ArrayList<LinkedList<BoardCell>> listOfAdjacencies;
	
	public Board() {
		cells = new ArrayList<BoardCell>();
		rooms = new HashMap<Character, String>();
		targets = new HashSet<BoardCell>();
		adjLists = new HashMap<Integer, LinkedList<Integer>>();
		boardFile = "";
		roomFile = "";
		numRows = 0;
		numColumns = 0;

	}
	
	public Board(String boardFile, String roomFile) {
		cells = new ArrayList<BoardCell>();
		rooms = new HashMap<Character, String>();
		targets = new HashSet<BoardCell>();
		adjLists = new HashMap<Integer, LinkedList<Integer>>();
		this.boardFile = boardFile;
		this.roomFile = roomFile;
		numRows = 0;
		numColumns = 0;

	}

	//initializes adjLists
	public void setAdjList() {

		for(int i = 0; i < (numRows * numColumns); i++ ) {
			adjLists.put(i, new LinkedList<Integer>());
		}
	}

	//initializes visited
	public void setVisited() {
		visited = new boolean[numRows*numColumns];

		for(int i = 0; i < calcIndex(numRows-1, numColumns-1) ; i++ ) {
			visited[i] = false;
		}
	}

	public HashSet getTargets() {
		return targets;
	}

	//resets targets to an empty set every time getTargetsReset is called
	public HashSet getTargetsReset() {
		HashSet<BoardCell> temp = targets;
		targets = new HashSet<BoardCell>();
		return temp;
	}

	public int getNumRows() {
		return numRows;
	}

	public int getNumColumns() {
		return numColumns;
	}

	public HashMap getRooms() {
		return rooms;
	}

	public ArrayList<BoardCell> getCells() {
		return cells;
	}

	//Loads both the room and board files
	public void  loadConfigFiles() {
		try {
			loadRoomConfig();
			loadBoardConfig();
		} catch(BadConfigException e) {
			System.out.println(e);
		}
	}

	//loads every room or walkway cell into cells
	public void loadBoardConfig() throws BadConfigException {
		String tempString;
		String[] tempArray;
		int count = 0;
		FileReader reader = null;
		try {
			reader = new FileReader(boardFile);
		} catch (FileNotFoundException e) {
			System.out.println("bad board file");
		}
		Scanner in = new Scanner(reader);
		numRows = 0;
		numColumns = 0;
		int numColumnsCurrent = 0;
		boolean hasLooped = false;
		while(in.hasNextLine()) {
			boolean inLegend = false;
			numRows++;

			if(numColumnsCurrent != numColumns && hasLooped){
				throw new BadConfigException("Inconsistent number of columns in board config file.");
			}
			numColumns = numColumnsCurrent;
			numColumnsCurrent = 0;
			tempString = in.nextLine();

			tempArray = tempString.split(",");

			Set<Character> keys = rooms.keySet();

			for (String s : tempArray) {
				numColumnsCurrent++;
				inLegend = false;
				for(Character key : keys) {
					if(key.equals(s.charAt(0)))
						inLegend = true;
				}
				if(!inLegend)
					throw new BadConfigException("Room not in legend.");

				if(s == "W") {
					cells.add(new WalkwayCell(count, s));
				} else {
					cells.add(new RoomCell(count, s));
				}
			}
			if(!hasLooped){
				numColumns = numColumnsCurrent;
			}
			hasLooped = true;

		}
		in.close();
	}

	//loads the legend into rooms
	public void loadRoomConfig() throws BadConfigException {
		Character index;
		String name, tempString, tempArray[];
		FileReader reader = null;

		try {
			reader = new FileReader(roomFile);
		} catch (FileNotFoundException e) {
			System.out.println("Bad room config file, file name given: " + roomFile);
		}
		Scanner in = new Scanner(reader);

		while(in.hasNextLine()) {
			tempString = in.nextLine();
			tempArray = tempString.split(",");

			if(tempArray.length != 2) {
				throw new BadConfigException("Invalid legend format.");
			}
			index = tempArray[0].charAt(0);
			name = tempArray[1];
			rooms.put(index, name);
		}
	}

	public int getNumRooms() {
		return rooms.size();
	}

	public int calcIndex(int row, int column) {
		return (row*(numColumns)) + column;
	}

	//returns the roomCell at the given row and column
	public RoomCell getRoomCellAt(int row, int column) {
		int index = calcIndex(row, column);
		if(cells.get(index).isRoom())
			return (RoomCell)cells.get(index);
		else
			return null;
	}
	
	//returns the BoardCell at the given index
	public BoardCell getCellAt(int index) {
		return cells.get(index);
	}

	//calculates the adjacencies for every valid cell and stores
	//them in adjLists 
	public void calcAdjacencies() {
		listOfAdjacencies = new ArrayList<LinkedList<BoardCell>>();
		for(int i = 0; i < numRows; ++i) {
			for (int j = 0; j < numColumns; ++j) {
				int currentIndex = calcIndex(i,j);
				adjacencies = new LinkedList<BoardCell>(); 
				if (i-1 >= 0) {
					int xminus1 = calcIndex(i-1, j);
					if(adjacencyIsValid(currentIndex, xminus1)){
						adjacencies.add(cells.get(xminus1));
					}
				}
				if (j-1 >= 0) {
					int yminus1 = calcIndex(i, j-1);
					if(adjacencyIsValid(currentIndex, yminus1)){
						adjacencies.add(cells.get(yminus1));
					}
				}
				if (i+1 < numRows) {
					int xplus1 = calcIndex(i+1, j);
					if(adjacencyIsValid(currentIndex, xplus1)){
						adjacencies.add(cells.get(xplus1));
					}
				}
				if (j+1 < numColumns) {
					int yplus1 = calcIndex(i, j+1);
					if(adjacencyIsValid(currentIndex, yplus1)){
						adjacencies.add(cells.get(yplus1));
					}
				}
				listOfAdjacencies.add(adjacencies);
			}
			}
	}
			
	//helper method for calc adjacency, only checks for rooms
		public boolean adjacencyIsValid(int currentIndex, int index){
			
			if(cells.get(currentIndex).isRoom()){
				if(cells.get(currentIndex).isDoorway()){
					if(cells.get(index).isWalkway()){
						return true;
					}
					else{
						return false;
					}
				} else{
					return false;
				}
				
			} 
			
			else if(cells.get(currentIndex).isWalkway()){
				if(cells.get(index).isRoom()){
					if(cells.get(index).isDoorway()){
						return true;
					} else{
						return false;
					}
				} else{
					return true;
				}
			}
					
			return true;
		}
	

	//calculates the possible targets the given number of steps away, starting
	//from the given row and column
	public void startTargets(int row, int column, int steps) {
		int index = calcIndex(row,column);
		if(index < calcIndex(numRows,numColumns)) {
			LinkedList<Integer> temp = adjLists.get(index);
			if(steps != 0 && getCellAt(index).isWalkway()){
				visited[index] = true;
				for(Integer i : temp){
					if (visited[i] == false){
						startTargets(i/numRows, i%numColumns , steps -1);
					}
				}

			}
			else{
				targets.add(cells.get(index));
				if(steps != 0){
					for(Integer i : temp){
						if (visited[i] == false){
							startTargets(i/numRows, i%numColumns , steps -1);
						}
					}
				}

			}
		}
	}

	public LinkedList<Integer> getAdjList(int index) {
		return adjLists.get(index);
	}
}
