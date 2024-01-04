import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@SpringBootApplication
public class LibraryApplication {

    public static void main(String[] args) {
        SpringApplication.run(LibraryApplication.class, args);
    }
}

class Book {
    private String title;
    private int copiesAvailable;

    public Book(String title, int copiesAvailable) {
        this.title = title;
        this.copiesAvailable = copiesAvailable;
    }

    public String getTitle() {
        return title;
    }

    public int getCopiesAvailable() {
        return copiesAvailable;
    }

    public void decrementCopiesAvailable() {
        this.copiesAvailable--;
    }
}

class Subscriber {
    private String name;
    private List<String> subscribedBooks;

    public Subscriber(String name) {
        this.name = name;
        this.subscribedBooks = new ArrayList<>();
    }

    public String getName() {
        return name;
    }

    public List<String> getSubscribedBooks() {
        return subscribedBooks;
    }

    public void addSubscribedBook(String bookTitle) {
        subscribedBooks.add(bookTitle);
    }
}

@RestController
@RequestMapping("/library")
class LibraryController {

    private Map<String, Book> books = new HashMap<>();
    private Map<String, Subscriber> subscribers = new HashMap<>();

    @PostMapping("/addBook")
    public String addBook(@RequestBody Book book) {
        books.put(book.getTitle(), book);
        return "Book added successfully";
    }

    @GetMapping("/books")
    public Map<String, Book> getAllBooks() {
        return books;
    }

    @PostMapping("/subscribe/{subscriberName}/{bookTitle}")
    public String subscribeBook(@PathVariable String subscriberName, @PathVariable String bookTitle) {
        Subscriber subscriber = subscribers.computeIfAbsent(subscriberName, Subscriber::new);
        Book book = books.get(bookTitle);

        if (book == null) {
            return "Book not found";
        }

        if (book.getCopiesAvailable() > 0) {
            book.decrementCopiesAvailable();
            subscriber.addSubscribedBook(bookTitle);
            return "Subscription successful";
        } else {
            return "Book not available for subscription";
        }
    }

    @GetMapping("/subscribers")
    public Map<String, Subscriber> getAllSubscribers() {
        return subscribers;
    }
}
