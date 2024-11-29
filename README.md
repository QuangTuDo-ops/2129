namespace FlightApp
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                Console.WriteLine("Welcome to XYZ Airlines Limited!");

                Menu.Initialize(maxCustomers: 100, customerSeed: 1, maxFlights: 50, flightSeed: 1, maxBookings: 200);

                Menu.Run();
            }
            catch (Exception ex)
            {
                Console.WriteLine("An error occurred while initializing the program: " + ex.Message);
            }
        }
    }

}

namespace FlightApp;

public class Menu
{
    private static AirlineCoordinator coord;

    public static void Initialize(int maxCustomers, int customerSeed, int maxFlights, int flightSeed, int maxBookings)
    {
        var customerManager = new CustomerManager(maxCustomers, customerSeed);
        var flightManager = new FlightManager(maxFlights, flightSeed);
        var bookingManager = new BookingManager(maxBookings, customerManager, flightManager);

        coord = new AirlineCoordinator(customerManager, flightManager, bookingManager);
    }

    public static void Run()
    {
        int choice;
        do
        {
            choice = GetValidChoice(MainMenu(), 4);
            switch (choice)
            {
                case 1:
                    CustomerMenuHandler();
                    break;
                case 2:
                    FlightMenuHandler();
                    break;
                case 3:
                    BookingMenuHandler();
                    break;
                case 4:
                    Console.WriteLine("Exiting program...");
                    break;
            }
        } while (choice != 4);
    }

    private static string MainMenu()
    {
        return "XYZ Airlines Limited\n" +
               "1: Customers\n" +
               "2: Flights\n" +
               "3: Bookings\n" +
               "4: Exit\n";
    }

    private static int GetValidChoice(string menu, int range)
    {
        int choice;
        do
        {
            Console.Clear();
            Console.WriteLine(menu);
            Console.Write("Enter your choice: ");
            string input = Console.ReadLine()?.Trim();
            if (!int.TryParse(input, out choice) || choice < 1 || choice > range)
            {
                Console.WriteLine($"Invalid input. Please enter a number between 1 and {range}.");
                Console.ReadKey();
            }
        } while (choice < 1 || choice > range);

        return choice;
    }

    private static void CustomerMenuHandler()
    {
        int choice;
        do
        {
            choice = GetValidChoice(CustomerMenu(), 4);
            switch (choice)
            {
                case 1:
                    AddCustomer();
                    break;
                case 2:
                    ViewCustomers();
                    break;
                case 3:
                    DeleteCustomer();
                    break;
                case 4:
                    break;
            }
        } while (choice != 4);
    }

    private static string CustomerMenu()
    {
        return "XYZ Airlines Limited - Customer Menu\n" +
               "1: Add Customer\n" +
               "2: View Customers\n" +
               "3: Delete Customer\n" +
               "4: Back to Main Menu\n";
    }

    private static void AddCustomer()
    {
        Console.Clear();
        Console.Write("Enter first name: ");
        string fname = Console.ReadLine()?.Trim();
        Console.Write("Enter last name: ");
        string lname = Console.ReadLine()?.Trim();
        Console.Write("Enter phone number: ");
        string phone = Console.ReadLine()?.Trim();

        if (coord.addCustomer(fname, lname, phone, 0))
            Console.WriteLine("Customer added successfully!");
        else
            Console.WriteLine("Failed to add customer.");

        Console.ReadKey();
    }

    private static void ViewCustomers()
    {
        Console.Clear();
        Console.WriteLine(coord.ViewCustomers());
        Console.ReadKey();
    }

    private static void DeleteCustomer()
    {
        Console.Clear();
        Console.Write("Enter customer ID to delete: ");
        if (int.TryParse(Console.ReadLine(), out int customerId))
        {
            Console.WriteLine($"Are you sure you want to delete customer with ID {customerId}? (Y/N)");
            string confirmation = Console.ReadLine()?.Trim().ToUpper();

            if (confirmation == "Y")
            {
                if (coord.DeleteCustomer(customerId))
                    Console.WriteLine("Customer deleted successfully!");
                else
                    Console.WriteLine("Failed to delete customer.");
            }
            else
            {
                Console.WriteLine("Deletion canceled.");
            }
        }
        else
        {
            Console.WriteLine("Invalid input. Please enter a valid customer ID.");
        }
        Console.ReadKey();
    }

    private static void FlightMenuHandler()
    {
        int choice;
        do
        {
            choice = GetValidChoice(FlightsMenu(), 5);
            switch (choice)
            {
                case 1:
                    AddFlight();
                    break;
                case 2:
                    ViewFlights();
                    break;
                case 3:
                    ViewFlight();
                    break;
                case 4:
                    DeleteFlight();
                    break;
                case 5:
                    break;
            }
        } while (choice != 5);
    }

    private static string FlightsMenu()
    {
        return "XYZ Airlines Limited - Flights Menu\n" +
               "1: Add Flight\n" +
               "2: View Flights\n" +
               "3: View A Particular Flight\n" +
               "4: Delete Flight\n" +
               "5: Back to Main Menu\n";
    }

    private static void AddFlight()
    {
        Console.Clear();
        Console.Write("Enter flight origin: ");
        string origin = Console.ReadLine()?.Trim();
        Console.Write("Enter flight destination: ");
        string destination = Console.ReadLine()?.Trim();
        Console.Write("Enter maximum seats: ");
        if (!int.TryParse(Console.ReadLine(), out int maxSeats) || maxSeats <= 0)
        {
            Console.WriteLine("Invalid maximum seats. Please enter a positive number.");
            return;
        }

        if (coord.addFlight(0, origin, destination, maxSeats, 0))
            Console.WriteLine("Flight added successfully!");
        else
            Console.WriteLine("Failed to add flight.");

        Console.ReadKey();
    }

    private static void ViewFlights()
    {
        Console.Clear();
        Console.WriteLine(coord.ViewFlights());
        Console.ReadKey();
    }

    private static void ViewFlight()
    {
        Console.Clear();
        Console.Write("Enter flight number: ");
        if (int.TryParse(Console.ReadLine(), out int flightNum))
        {
            Console.WriteLine(coord.ViewFlight(flightNum));
        }
        else
        {
            Console.WriteLine("Invalid input.");
        }

        Console.ReadKey();
    }

    private static void DeleteFlight()
    {
        Console.Clear();
        Console.Write("Enter flight number to delete: ");
        if (int.TryParse(Console.ReadLine(), out int flightNum))
        {
            if (coord.DeleteFlight(flightNum))
                Console.WriteLine("Flight deleted successfully!");
            else
                Console.WriteLine("Failed to delete flight.");
        }
        else
        {
            Console.WriteLine("Invalid input.");
        }

        Console.ReadKey();
    }

    private static void BookingMenuHandler()
    {
        int choice;
        do
        {
            choice = GetValidChoice(BookingsMenu(), 3);
            switch (choice)
            {
                case 1:
                    MakeBooking();
                    break;
                case 2:
                    ViewBookings();
                    break;
                case 3:
                    break;
            }
        } while (choice != 3);
    }

    private static string BookingsMenu()
    {
        return "XYZ Airlines Limited - Booking Menu\n" +
               "1: Make Booking\n" +
               "2: View Bookings\n" +
               "3: Back to Main Menu\n";
    }

    private static void MakeBooking()
    {
        Console.Clear();
        Console.Write("Enter customer ID: ");
        if (!int.TryParse(Console.ReadLine(), out int customerId))
        {
            Console.WriteLine("Invalid customer ID. Please enter a valid number.");
            return;
        }

        Console.Write("Enter flight number: ");
        if (!int.TryParse(Console.ReadLine(), out int flightNum))
        {
            Console.WriteLine("Invalid flight number. Please enter a valid number.");
            return;
        }

        if (coord.makeBooking(customerId, flightNum))
            Console.WriteLine("Booking made successfully!");
        else
            Console.WriteLine("Failed to make booking.");

        Console.ReadKey();
    }

    private static void ViewBookings()
    {
        Console.Clear();
        Console.WriteLine(coord.ViewBookings());
        Console.ReadKey();
    }
}


namespace FlightApp;

public class FlightManager
{
    private Flight[] flightsList;
    private int numIndex;
    private int maxFlights;
    public static int seed;

    public FlightManager(int maxFlights, int startIndex)
    {
        this.maxFlights = maxFlights;
        seed = startIndex;
        numIndex = 0;
        flightsList = new Flight[maxFlights];
    }

    public Flight[] GetFlightsList()
    {
        return flightsList;
    }

    public int GetNumIndex()
    {
        return numIndex;
    }

    public Flight GetFlight(int flightNum)
    {
        foreach (Flight flight in flightsList)
        {
            if (flight != null && flight.flightNum == flightNum)
            {
                return flight;
            }
        }
        return null;
    }

    public bool AddFlight(int flightNum, string origin, string destination, int maxSeats, int numPassengers)
    {
        foreach (Flight flight in flightsList)
        {
            if (flight != null && flight.flightNum == flightNum)
            {
                Console.WriteLine("Flight already exists.");
                return false;
            }
        }
        if (numIndex >= maxFlights)
        {
            Console.WriteLine("Maximum number of flights is reached.");
            return false;
        }

        flightsList[numIndex++] = new Flight(seed, origin, destination, maxSeats, numPassengers);
        seed++;
        Console.WriteLine("Flight added.");
        return true;
    }

    public string ViewAllFlights()
    {
        if (numIndex == 0)
            return "No flights yet.";

        string s = "------Flights List------\n";
        for (int index = 0; index < numIndex; index++)
        {
            s += flightsList[index].ToString() + "\n";
        }
        return s;
    }

    public Flight SearchByFlightId(int flightNum)
    {
        for (int index = 0; index < numIndex; index++)
        {
            if (flightsList[index].flightNum == flightNum)
            {
                return flightsList[index];
            }
        }
        return null;
    }

    public bool CanDeleteFlight(int flightNum)
    {
        Flight flight = SearchByFlightId(flightNum);
        if (flight == null)
        {
            Console.WriteLine($"Flight with flight number {flightNum} does not exist.");
            return false;
        }
        if (flight.numPassengers > 0)
        {
            Console.WriteLine("Cannot delete flight: There are passengers booked on this flight.");
            return false;
        }

        return true;
    }

    internal string viewAllFlights()
    {
        throw new NotImplementedException();
    }

    internal Flight searchByFlightId(int flightNum)
    {
        throw new NotImplementedException();
    }
}

namespace FlightApp;
public class Flight
{
    public int flightNum { get; set; }
    public string origin { get; set; }
    public string destination { get; set; }
    public int maxSeats { get; set; }
    public int numPassengers { get; set; }

    public Flight(int flightNum, string origin, string destination, int maxSeats, int numPassengers)
    {
        if (string.IsNullOrWhiteSpace(origin))
            throw new ArgumentException("Origin cannot be empty.");
        if (string.IsNullOrWhiteSpace(destination))
            throw new ArgumentException("Destination cannot be empty.");
        if (maxSeats <= 0)
            throw new ArgumentException("Maximum seats must be greater than zero.");
        if (numPassengers < 0 || numPassengers > maxSeats)
            throw new ArgumentException("Number of passengers must be between 0 and the maximum seats.");

        this.flightNum = flightNum;
        this.origin = origin;
        this.destination = destination;
        this.maxSeats = maxSeats;
        this.numPassengers = numPassengers;
    }

    public override string ToString()
    {
        return $"------Flight------\n" +
               $"Flight Number: {flightNum}\n" +
               $"Origin       : {origin}\n" +
               $"Destination  : {destination}\n" +
               $"Max Seats    : {maxSeats}\n" +
               $"Passengers   : {numPassengers}\n";
    }
}


namespace FlightApp;

public class CustomerManager
{
    private Customer[] customersList;
    private int numIndex;
    private int maxCustomers;
    public static int seed;

    public CustomerManager(int maxCustomers, int startIndex)
    {
        this.maxCustomers = maxCustomers;
        numIndex = 0;
        customersList = new Customer[maxCustomers];
        seed = startIndex;
    }

    public Customer[] GetCustomersList()
    {
        return customersList;
    }

    public int GetNumIndex()
    {
        return numIndex;
    }

    public bool AddCustomer(string fname, string lname, string phone, int numBookings)
    {
        if (string.IsNullOrWhiteSpace(fname) || string.IsNullOrWhiteSpace(lname) || string.IsNullOrWhiteSpace(phone))
        {
            Console.WriteLine("Customer details cannot be empty.");
            return false;
        }

        if (numBookings < 0)
        {
            Console.WriteLine("Number of bookings cannot be negative.");
            return false;
        }

        foreach (Customer customer in customersList)
        {
            if (customer != null && customer.fname == fname && customer.lname == lname && customer.phone == phone)
            {
                Console.WriteLine("Customer already exists!");
                return false;
            }
        }

        if (numIndex >= maxCustomers)
        {
            Console.WriteLine("Maximum number of customers exceeded.");
            return false;
        }

        customersList[numIndex++] = new Customer(seed++, fname, lname, phone, numBookings);
        Console.WriteLine("Customer successfully added.");
        return true;
    }

    public string ViewAllCustomers()
    {
        if (numIndex == 0)
            return "No customers found!";

        string s = "------ Customers List ------\n";
        for (int index = 0; index < numIndex; index++)
        {
            s += customersList[index].ToString() + "\n";
        }
        return s;
    }

    public Customer SearchByCustomerId(int customerId)
    {
        for (int index = 0; index < numIndex; index++)
        {
            if (customersList[index].customerId == customerId)
            {
                return customersList[index];
            }
        }
        return null;
    }

    public bool CanDeleteCustomer(int customerId)
    {
        Customer customer = SearchByCustomerId(customerId);
        if (customer == null)
        {
            Console.WriteLine($"Customer with ID {customerId} does not exist.");
            return false;
        }

        if (customer.numBookings > 0)
        {
            Console.WriteLine("Cannot delete customer: The customer has active bookings.");
            return false;
        }

        return true;
    }

    internal bool viewAllCustomers()
    {
        throw new NotImplementedException();
    }

    internal Customer searchByCustomerId(int customerId)
    {
        throw new NotImplementedException();
    }
}

namespace FlightApp;

public class Customer
{
    public int customerId { get; private set; }
    public string fname { get; private set; }
    public string lname { get; private set; }
    public string phone { get; private set; }
    public int numBookings { get; private set; }

    public Customer(int customerId, string fname, string lname, string phone, int numBookings = 0)
    {
        if (string.IsNullOrWhiteSpace(fname))
            throw new ArgumentException("First name cannot be null or empty.");
        if (string.IsNullOrWhiteSpace(lname))
            throw new ArgumentException("Last name cannot be null or empty.");
        if (string.IsNullOrWhiteSpace(phone))
            throw new ArgumentException("Phone cannot be null or empty.");
        if (numBookings < 0)
            throw new ArgumentException("Number of bookings cannot be negative.");

        this.customerId = customerId;
        this.fname = fname;
        this.lname = lname;
        this.phone = phone;
        this.numBookings = numBookings;
    }

    public override string ToString()
    {
        return $"---- Customer ----\n" +
               $"Customer ID   : {customerId}\n" +
               $"First Name    : {fname}\n" +
               $"Last Name     : {lname}\n" +
               $"Phone         : {phone}\n" +
               $"Number of Bookings: {numBookings}\n";
    }

    internal void IncrementBookings()
    {
        throw new NotImplementedException();
    }
}

namespace FlightApp;

public class BookingManager
{
    private Booking[] bookingList;
    private int numBookings;
    private int max;
    private static int nextBookingNum = 1;

    public BookingManager(int max, CustomerManager cm, FlightManager fm)
    {
        this.max = max;
        numBookings = 0;
        bookingList = new Booking[max];
    }

    public Booking[] GetBookingsList()
    {
        return bookingList;
    }

    public bool MakeBooking(int customerId, int flightNum, CustomerManager cm, FlightManager fm)
    {
        Customer customer = cm.SearchByCustomerId(customerId);
        if (customer == null)
        {
            Console.WriteLine("Customer ID does not exist.");
            return false;
        }

        Flight flight = fm.SearchByFlightId(flightNum);
        if (flight == null)
        {
            Console.WriteLine("Flight ID does not exist.");
            return false;
        }

        if (flight.numPassengers >= flight.maxSeats)
        {
            Console.WriteLine("No available seats on this flight.");
            return false;
        }

        if (numBookings >= max)
        {
            Console.WriteLine("Maximum number of bookings reached.");
            return false;
        }

        bookingList[numBookings++] = new Booking(nextBookingNum++, flight, customer);
        flight.numPassengers++;
        customer.IncrementBookings();
        Console.WriteLine("Booking successful!");
        return true;
    }

    public string ViewAllBookings()
    {
        if (numBookings == 0)
            return "No bookings found.";

        string result = "------ Bookings List ------\n";
        for (int i = 0; i < numBookings; i++)
        {
            result += bookingList[i].ToString() + "\n";
        }
        return result;
    }
}

namespace FlightApp;

public class Booking
{
    public string Date { get; private set; }
    public int bookingNum { get; private set; }
    public Flight flightObj { get; private set; }
    public Customer customerObj { get; private set; }

    public Booking(int bookingNum, Flight flightObj, Customer customerObj)
    {
        if (flightObj == null)
            throw new ArgumentNullException(nameof(flightObj), "Flight cannot be null.");
        if (customerObj == null)
            throw new ArgumentNullException(nameof(customerObj), "Customer cannot be null.");

        Date = DateTime.Now.ToString(@"MM\/dd\/yyyy h\:mm tt");
        this.bookingNum = bookingNum;
        this.flightObj = flightObj;
        this.customerObj = customerObj;
    }

    public override string ToString()
    {
        return $"------ Booking ------\n" +
               $"Date          : {Date}\n" +
               $"Booking Num   : {bookingNum}\n" +
               $"Flight Details: {flightObj}\n" +
               $"Customer Details: {customerObj}\n";
    }
}


namespace FlightApp;

public class AirlineCoordinator
{
    private CustomerManager cm;
    private FlightManager fm;
    private BookingManager bm;

    public AirlineCoordinator(CustomerManager cm, FlightManager fm, BookingManager bm)
    {
        this.cm = cm;
        this.fm = fm;
        this.bm = bm;
    }

    public bool AddFlight(int flightNum, string origin, string destination, int maxSeats, int numPassengers)
    {
        return fm.AddFlight(flightNum, origin, destination, maxSeats, numPassengers);
    }

    public string ViewFlights()
    {
        return fm.ViewAllFlights();
    }

    public Flight SearchFlight(int flightNum)
    {
        return fm.SearchByFlightId(flightNum);
    }

    public bool DeleteFlight(int flightNum)
    {
        return fm.CanDeleteFlight(flightNum);
    }

    public string ViewFlight(int flightNum)
    {
        Flight flight = fm.GetFlight(flightNum);
        if (flight != null)
        {
            return flight.ToString();
        }
        return $"Flight with ID {flightNum} not found.";
    }

    public bool AddCustomer(string fname, string lname, string phone, int numBookings)
    {
        return cm.AddCustomer(fname, lname, phone, numBookings);
    }

    public string ViewCustomers()
    {
        return cm.ViewAllCustomers();
    }

    public Customer SearchCustomer(int customerId)
    {
        return cm.SearchByCustomerId(customerId);
    }

    public bool DeleteCustomer(int customerId)
    {
        return cm.CanDeleteCustomer(customerId);
    }

    public bool MakeBooking(int customerId, int flightNum)
    {
        Flight f = fm.SearchByFlightId(flightNum);
        Customer c = cm.SearchByCustomerId(customerId);
        if (f == null || c == null) return false;
        return bm.MakeBooking(customerId, flightNum, cm, fm);
    }

    public string ViewBookings()
    {
        return bm.ViewAllBookings();
    }

    public string GetFlightsList()
    {
        return fm.ViewAllFlights();
    }

    public string GetCustomersList()
    {
        return cm.ViewAllCustomers();
    }

    public string GetBookingsList()
    {
        return bm.ViewAllBookings();
    }

    internal bool addCustomer(string? fname, string? lname, string? phone, int v)
    {
        throw new NotImplementedException();
    }

    internal bool addFlight(int v1, string? origin, string? destination, int maxSeats, int v2)
    {
        throw new NotImplementedException();
    }

    internal bool makeBooking(int customerId, int flightNum)
    {
        throw new NotImplementedException();
    }
}
