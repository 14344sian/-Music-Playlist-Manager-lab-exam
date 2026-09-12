# -Music-Playlist-Manager-lab-exam\
import java.util.Scanner;

// --- STEP 1: DEFINE THE SONG DATA CLASS ---
class Song {
    private String songId;
    private String songTitle;
    private String artist;
    private double duration;

    public Song(String songId, String songTitle, String artist, double duration) {
        this.songId = songId;
        this.songTitle = songTitle;
        this.artist = artist;
        this.duration = duration;
    }

    public String getSongId() { return songId; }
    public String getSongTitle() { return songTitle; }
    public String getArtist() { return artist; }
    public double getDuration() { return duration; }

    @Override
    public String toString() {
        return String.format("ID: %-10s | Title: %-20s | Artist: %-15s | Duration: %.2f mins", 
                songId, songTitle, artist, duration);
    }
}

// --- STEP 2: DEFINE THE MANUAL LINKED LIST NODE ---
class Node {
    Song data;
    Node next;

    public Node(Song data) {
        this.data = data;
        this.next = null;
    }
}

// --- STEP 3: MANUAL SINGLY LINKED LIST ADT IMPLEMENTATION ---
class PlaylistLinkedList {
    private Node head;

    public PlaylistLinkedList() {
        this.head = null;
    }

    public boolean isEmpty() {
        return head == null;
    }

    // 1. Add Song at Beginning
    public void insertFirst(Song data) {
        Node newNode = new Node(data);
        if (!isEmpty()) {
            newNode.next = head;
        }
        head = newNode;
    }

    // 2. Add Song at End
    public void insertLast(Song data) {
        Node newNode = new Node(data);
        if (isEmpty()) {
            head = newNode;
            return;
        }
        Node current = head;
        while (current.next != null) {
            current = current.next;
        }
        current.next = newNode;
    }

    // 3. Insert Song at Specific Position (1-based index)
    public boolean insertAt(int position, Song data) {
        int currentSize = size();
        
        if (position < 1 || position > currentSize + 1) {
            return false;
        }

        if (position == 1) {
            insertFirst(data);
            return true;
        }

        Node newNode = new Node(data);
        Node current = head;
        for (int i = 1; i < position - 1; i++) {
            current = current.next;
        }
        newNode.next = current.next;
        current.next = newNode;
        return true;
    }

    // 4. Display Playlist
    public void display() {
        if (isEmpty()) {
            System.out.println("The playlist is currently empty.");
            return;
        }
        Node current = head;
        while (current != null) {
            System.out.println(current.data);
            current = current.next;
        }
    }

    // 5. Search Song by ID
    public Song search(String songId) {
        Node current = head;
        while (current != null) {
            if (current.data.getSongId().equalsIgnoreCase(songId)) {
                return current.data;
            }
            current = current.next;
        }
        return null;
    }

    // 6. Remove Song by ID
    public boolean delete(String songId) {
        if (isEmpty()) {
            return false;
        }

        if (head.data.getSongId().equalsIgnoreCase(songId)) {
            head = head.next;
            return true;
        }

        Node current = head;
        while (current.next != null) {
            if (current.next.data.getSongId().equalsIgnoreCase(songId)) {
                current.next = current.next.next;
                return true;
            }
            current = current.next;
        }
        return false;
    }

    // 7. Get Size
    public int size() {
        int count = 0;
        Node current = head;
        while (current != null) {
            count++;
            current = current.next;
        }
        return count;
    }
}

// --- STEP 4: MAIN INTERACTIVE MENU CONTROL ---
public class Main {
    private static final PlaylistLinkedList playlist = new PlaylistLinkedList();
    private static final Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        int choice;
        do {
            displayMenu();
            choice = getValidIntegerInput("Enter your choice: ");
            switch (choice) {
                case 1 -> addSong(1);
                case 2 -> addSong(2);
                case 3 -> addSong(3);
                case 4 -> displayPlaylist();
                case 5 -> searchSong();
                case 6 -> removeSong();
                case 7 -> System.out.println("\nTotal songs in playlist: " + playlist.size());
                case 8 -> System.out.println("\nExiting Playlist Manager. Goodbye!");
                default -> System.out.println("Invalid choice! Please select an option between 1 and 8.");
            }
        } while (choice != 8);
    }

    private static void displayMenu() {
        System.out.println("\n========================================");
        System.out.println("         MUSIC PLAYLIST MANAGER          ");
        System.out.println("========================================");
        System.out.println("1. Add Song at Beginning");
        System.out.println("2. Add Song at End");
        System.out.println("3. Insert Song at Position");
        System.out.println("4. Display Playlist");
        System.out.println("5. Search Song");
        System.out.println("6. Remove Song");
        System.out.println("7. Display Playlist Size");
        System.out.println("8. Exit");
        System.out.println("========================================");
    }

    private static void addSong(int mode) {
        System.out.println("\n--- Add New Song Details ---");
        
        String id;
        while (true) {
            System.out.print("Enter Song ID: ");
            id = scanner.nextLine().trim();
            if (id.isEmpty()) {
                System.out.println("Song ID cannot be empty.");
            } else if (playlist.search(id) != null) {
                System.out.println("Error: A song with this ID already exists!");
            } else {
                break;
            }
        }

        System.out.print("Enter Song Title: ");
        String title = scanner.nextLine().trim();
        System.out.print("Enter Artist: ");
        String artist = scanner.nextLine().trim();
        double duration = getValidDoubleInput("Enter Duration (e.g., 3.50): ");

        Song newSong = new Song(id, title, artist, duration);

        if (mode == 1) {
            playlist.insertFirst(newSong);
            System.out.println("Song successfully added to the beginning!");
        } else if (mode == 2) {
            playlist.insertLast(newSong);
            System.out.println("Song successfully added to the end!");
        } else if (mode == 3) {
            int pos = getValidIntegerInput("Enter insertion position (1 to " + (playlist.size() + 1) + "): ");
            boolean inserted = playlist.insertAt(pos, newSong);
            if (inserted) {
                System.out.println("Song successfully inserted at position " + pos + "!");
            } else {
                System.out.println("Invalid position! Insertion aborted.");
            }
        }
    }

    private static void displayPlaylist() {
        System.out.println("\n--- Current Playlist ---");
        playlist.display();
        System.out.println("------------------------");
        System.out.println("Total Songs: " + playlist.size());
    }

    private static void searchSong() {
        System.out.println("\n--- Search for a Song ---");
        System.out.print("Enter Song ID to search: ");
        String id = scanner.nextLine().trim();

        Song foundSong = playlist.search(id);
        if (foundSong != null) {
            System.out.println("\nSong Found Information:");
            System.out.println(foundSong);
        } else {
            System.out.println("Song with ID '" + id + "' was not found.");
        }
    }

    private static void removeSong() {
        System.out.println("\n--- Remove Song From Playlist ---");
        System.out.print("Enter Song ID to remove: ");
        String id = scanner.nextLine().trim();

        boolean deleted = playlist.delete(id);
        if (deleted) {
            System.out.println("Song with ID '" + id + "' was successfully removed.");
        } else {
            System.out.println("Song with ID '" + id + "' not found.");
        }
    }

    private static int getValidIntegerInput(String prompt) {
        int value;
        while (true) {
            if (!prompt.isEmpty()) {
                System.out.print(prompt);
            }
            String input = scanner.nextLine().trim();
            try {
                value = Integer.parseInt(input);
                return value;
            } catch (NumberFormatException e) {
                System.out.print("Invalid choice. Please enter a valid number: ");
                prompt = "";
            }
        }
    }

    private static double getValidDoubleInput(String prompt) {
        double value;
        while (true) {
            System.out.print(prompt);
            String input = scanner.nextLine().trim();
            try {
                value = Double.parseDouble(input);
                if (value <= 0) {
                    System.out.println("Duration must be greater than zero.");
                    continue;
                }
                return value;
            } catch (NumberFormatException e) {
                System.out.println("Invalid format. Please enter a numeric value.");
                prompt = "Enter Duration (e.g., 3.50): ";
            }
        }
    }
}

