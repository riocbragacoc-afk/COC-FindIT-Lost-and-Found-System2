# COC-FindIT-Lost-and-Found-System2
The COC FindIT: Lost and Found System is a simple console-based system that helps students and staff report, track, and recover lost items efficiently. It replaces manual methods with an organized system, improving record management and increasing the chances of item recovery.

import java.util.*; 
 
// ===== USER MODEL ===== 
class User { 
    String username; 
    String password; 
    String role; // USER or ADMIN 
 
    public User(String username, String password, String role) { 
        this.username = username; 
        this.password = password; 
        this.role = role; 
    } 
} 
 
// ===== AUTH SERVICE ===== 
class AuthService { 
    ArrayList<User> users = new ArrayList<>(); 
 
    public AuthService() { 
        // ADMIN ACCOUNT 
        users.add(new User("admin", "admin123", "ADMIN")); 
    } 
 
    public void register(String username, String password) { 
        users.add(new User(username, password, "USER")); 
        System.out.println("Registered successfully!"); 
    } 
 
    public User login(String username, String password) { 
        for (User u : users) { 
            if (u.username.equals(username) && u.password.equals(password)) { 
                return u; 
            } 
        } 
        return null; 
    } 
 
    // FORGOT PASSWORD 
    public void forgotPassword(String username) { 
        for (User u : users) { 
            if (u.username.equals(username)) { 
                System.out.print("Enter new password: "); 
                Scanner sc = new Scanner(System.in); 
                u.password = sc.nextLine(); 
                System.out.println("Password updated!"); 
                return; 
            } 
        } 
        System.out.println("Username not found!"); 
    } 
} 
 
// ===== ITEM MODEL ===== 
class Item { 
    String name, description, location, date, type, owner, claimCode; 
    boolean claimed = false; 
 
    public Item(String name, String description, String location, String date, String type, String owner) { 
        this.name = name; 
        this.description = description; 
        this.location = location; 
        this.date = date; 
        this.type = type; 
        this.owner = owner; 
        this.claimCode = generateCode(); 
    } 
 
    private String generateCode() { 
        return "CODE" + (int)(Math.random() * 1000); 
    } 
} 
 
// ===== ITEM SERVICE ===== 
class ItemService { 
    ArrayList<Item> items = new ArrayList<>(); 
 
    public void addItem(Item item) { 
        items.add(item); 
        System.out.println("Item added!"); 
    } 
 
    public void viewItems() { 
        for (Item i : items) { 
            System.out.println(i.type + " | " + i.name + " | " + i.location + " | Date: " + i.date + " | Claimed: " + i.claimed); 
        } 
    } 
 
    public void matchItems() { 
        boolean matchFound = false; 
        for (Item lost : items) { 
            if (lost.type.equals("LOST")) { 
                for (Item found : items) { 
                    if (found.type.equals("FOUND") && 
                        lost.name.equalsIgnoreCase(found.name)) { 
 
                        System.out.println("MATCH FOUND!"); 
                        System.out.println("Item: " + lost.name); 
                        System.out.println("Claim Code: " + found.claimCode); 
                        matchFound = true;  
                    } 
                } 
            } 
        } 
        if (!matchFound) { 
            System.out.println("No matches found at this time."); 
        } 
    } 
 
    public void claimItem(String code) { 
        for (Item i : items) { 
            if (i.claimCode.equals(code) && !i.claimed) { 
                i.claimed = true; 
                System.out.println("Item claimed: " + i.name); 
                return; 
            } 
        } 
        System.out.println("Invalid or already claimed."); 
    } 
 
    // ADMIN FEATURE 
    public void deleteItem(int index) { 
        if (index >= 0 && index < items.size()) { 
            items.remove(index); 
            System.out.println("Item deleted."); 
        } else { 
            System.out.println("Invalid index."); 
        } 
    } 
} 
 
// ===== MAIN SYSTEM ===== 
public class Main { 
    public static void main(String[] args) { 
 
        Scanner sc = new Scanner(System.in); 
        AuthService auth = new AuthService(); 
        ItemService service = new ItemService(); 
 
        while (true) { 
            System.out.println("\n=== PHINMA COC findIT: LOST & FOUND ==="); 
            System.out.println("1. Register"); 
            System.out.println("2. Login"); 
            System.out.println("3. Forgot Password"); 
 
            int choice = sc.nextInt(); 
            sc.nextLine(); 
 
            if (choice == 1) { 
                System.out.print("Username: "); 
                String u = sc.nextLine(); 
                System.out.print("Password: "); 
                String p = sc.nextLine(); 
                auth.register(u, p); 
            } 
 
            else if (choice == 2) { 
                System.out.print("Username: "); 
                String u = sc.nextLine(); 
                System.out.print("Password: "); 
                String p = sc.nextLine(); 
 
                User currentUser = auth.login(u, p); 
 
                if (currentUser != null) { 
                    System.out.println("Login successful!"); 
 
                    // ADMIN PANEL 
                    if (currentUser.role.equals("ADMIN")) { 
                        while (true) { 
                            System.out.println("\n=== ADMIN PANEL ==="); 
                            System.out.println("1. View Items"); 
                            System.out.println("2. Delete Item"); 
                            System.out.println("3. Logout"); 
 
                            int opt = sc.nextInt(); 
                            sc.nextLine(); 
 
                            if (opt == 1) service.viewItems(); 
 
                            else if (opt == 2) { 
                                service.viewItems(); 
                                System.out.print("Enter index to delete: "); 
                                int index = sc.nextInt(); 
                                service.deleteItem(index); 
                            } 
 
                            else if (opt == 3) break; 
                        } 
                    } 
 
                    // USER PANEL 
                    else { 
                        while (true) { 
                            System.out.println("\n1. Report Lost"); 
                            System.out.println("2. Report Found"); 
                            System.out.println("3. View Items"); 
                            System.out.println("4. Match Items"); 
                            System.out.println("5. Claim Item"); 
                            System.out.println("6. Logout"); 
 
                            int opt = sc.nextInt(); 
                            sc.nextLine(); 
 
                            if (opt == 1 || opt == 2) { 
                                System.out.print("Item Name: "); 
                                String name = sc.nextLine(); 
                                System.out.print("Description: "); 
                                String desc = sc.nextLine(); 
                                System.out.print("Location: "); 
                                String loc = sc.nextLine(); 
                                System.out.print("Date: "); 
                                String date = sc.nextLine(); 
 
                                String type = (opt == 1) ? "LOST" : "FOUND"; 
 
                                service.addItem(new Item(name, desc, loc, date, type, u)); 
                            } 
 
                            else if (opt == 3) service.viewItems(); 
                            else if (opt == 4) service.matchItems(); 
 
                            else if (opt == 5) { 
                                System.out.print("Enter Claim Code: "); 
                                String code = sc.nextLine(); 
                                service.claimItem(code); 
                            } 
 
                            else if (opt == 6) break; 
                        } 
                    } 
 
                } else { 
                    System.out.println("Invalid login!"); 
                    System.out.println("Redirecting to Register..."); 
 
                    System.out.print("New Username: "); 
                    String newU = sc.nextLine(); 
                    System.out.print("New Password: "); 
                    String newP = sc.nextLine(); 
 
                    auth.register(newU, newP); 
                } 
            } 
 
            else if (choice == 3) { 
                System.out.print("Enter username: "); 
                String user = sc.nextLine(); 
                auth.forgotPassword(user); 
            } 
        } 
    } 
}
