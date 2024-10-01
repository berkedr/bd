import java.util.ArrayList;

class Person {
    int id;
    String firstName;
    String lastName;
    int age;

    public Person(int id, String firstName, String lastName, int age) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }

    @Override
    public String toString() {
        return firstName + " " + lastName;
    }
}

class User extends Person {
    String userName;
    String password;

    public User(int id, String firstName, String lastName, String userName, String password) {
        super(id, firstName, lastName, 0);  
        this.userName = userName;
        this.password = password;
    }
}

class Category {
    int id;
    String categoryCode;
    String categoryName;

    public Category(int id, String code, String name) {
        this.id = id;
        this.categoryCode = code;
        this.categoryName = name;
    }
}

abstract class Material {
    int id;
    String name;
    ArrayList<Integer> scores;
    int releaseYear;
    int price;
    Category category;
    String type;

    public Material(int id, String name, ArrayList<Integer> scores, int releaseYear, int price, Category category, String type) {
        this.id = id;
        this.name = name;
        this.scores = scores;
        this.releaseYear = releaseYear;
        this.price = price;
        this.category = category;
        this.type = type;
    }

    public void addScore(int score) {
        this.scores.add(score);
    }

    public double getAvgScore() {
        if (scores.isEmpty()) return 0;
        double total = 0;
        for (int score : scores) {
            total += score;
        }
        return total / scores.size();
    }

    public void showDetail() {
        System.out.println("ID: " + id + ", Name: " + name + ", Release Year: " + releaseYear + ", Price: " + price + ", Average Score: " + getAvgScore());
    }
}

class Book extends Material {
    Person writer;
    int numberOfPages;

    public Book(int id, String name, ArrayList<Integer> scores, int releaseYear, int price, Category category, String type, Person writer, int numberOfPages) {
        super(id, name, scores, releaseYear, price, category, type);
        this.writer = writer;
        this.numberOfPages = numberOfPages;
    }

    public void setWriter(Person newWriter) {
        this.writer = newWriter;
    }

    @Override
    public void showDetail() {
        super.showDetail();
        System.out.println("Writer: " + writer + ", Pages: " + numberOfPages);
    }
}

class Movie extends Material {
    ArrayList<Person> actors;
    Person director;

    public Movie(int id, String name, ArrayList<Integer> scores, int releaseYear, int price, Category category, String type, ArrayList<Person> actors, Person director) {
        super(id, name, scores, releaseYear, price, category, type);
        this.actors = actors;
        this.director = director;
    }

    public void addActor(Person actor) {
        this.actors.add(actor);
    }

  
    public void showDetail() {
        super.showDetail();
        System.out.print("Actors: ");
        for (Person actor : actors) {
            System.out.print(actor + " ");
        }
        System.out.println(", Director: " + director);
    }
}

class Netflix {
    ArrayList<Material> materials;
    User credential;
    boolean isLogin = false;

    public Netflix(ArrayList<Material> materials) {
        this.materials = materials;
    }

    public void login(User user) {
        if (user.userName.equals("testUser") && user.password.equals("123456")) {
            isLogin = true;
            credential = user;
            System.out.println("Login successful!");
        } else {
            System.out.println("Invalid credentials!");
        }
    }

    public void addMovie(Movie m1) {
        if (isLogin) materials.add(m1);
    }

    public void addBook(Book b1) {
        if (isLogin) materials.add(b1);
    }

    public void addMaterial(Material m2) {
        if (isLogin) materials.add(m2);
    }

    public void showHighestAvgScore() {
        Material highest = null;
        for (Material m : materials) {
            if (highest == null || m.getAvgScore() > highest.getAvgScore()) {
                highest = m;
            }
        }
        if (highest != null) highest.showDetail();
    }

    public void showLowestAvgScore() {
        Material lowest = null;
        for (Material m : materials) {
            if (lowest == null || m.getAvgScore() < lowest.getAvgScore()) {
                lowest = m;
            }
        }
        if (lowest != null) lowest.showDetail();
    }

    public void showMostExpensiveMaterialByCategory(int categoryId) {
        Material mostExpensive = null;
        for (Material m : materials) {
            if (m.category.id == categoryId) {
                if (mostExpensive == null || m.price > mostExpensive.price) {
                    mostExpensive = m;
                }
            }
        }
        if (mostExpensive != null) mostExpensive.showDetail();
    }

    public void showMoviesByActorId(int actorId) {
        for (Material m : materials) {
            if (m instanceof Movie) {
                Movie movie = (Movie) m;
                for (Person actor : movie.actors) {
                    if (actor.id == actorId) {
                        movie.showDetail();
                        break;
                    }
                }
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        
        Person actor1 = new Person(1, "Leonardo", "DiCaprio", 47);
        Person actor2 = new Person(2, "Tom Hanks", "Hanks", 67);
        Person writer1 = new Person(3, "George", "Orwell", 46);

        
        Category action = new Category(1, "ACTION", "Action");
        Category drama = new Category(2, "DRAMA", "Drama");
        Category fiction = new Category(3, "FICTION", "Fiction");

        
        ArrayList<Integer> scores1 = new ArrayList<>();
        Movie movie1 = new Movie(1, "Inception", scores1, 2010, 15, action, "movie", new ArrayList<Person>() {{ add(actor1); }}, actor1);
        movie1.addScore(9);
        movie1.addScore(10);

        ArrayList<Integer> scores2 = new ArrayList<>();
        Movie movie2 = new Movie(2, "The Terminal", scores2, 2004, 10, drama, "movie", new ArrayList<Person>() {{ add(actor2); }}, actor2);
        movie2.addScore(8);
        movie2.addScore(7);

        
        ArrayList<Integer> scores3 = new ArrayList<>();
        Book book1 = new Book(4, "1984", scores3, 1949, 20, fiction, "book", writer1, 328);
        book1.addScore(9);
        book1.addScore(10);

       
        User user = new User(5, "Test", "User", "testUser", "123456");

        
        Netflix netflix = new Netflix(new ArrayList<>());

        
        netflix.login(user);

        
        netflix.addMovie(movie1);
        netflix.addMovie(movie2);
        netflix.addBook(book1);

        
        System.out.println("En yüksek ortalama skora sahip materyal:");
        netflix.showHighestAvgScore();

        System.out.println("\nEn düşük ortalama skorlu materyal:");
        netflix.showLowestAvgScore();

        System.out.println("\nKategori 1 için en pahalı materyal:");
        netflix.showMostExpensiveMaterialByCategory(1);

        System.out.println("\nActor ID 1'in oynadığı filmler:");
        netflix.showMoviesByActorId(1);
    }
    
}
