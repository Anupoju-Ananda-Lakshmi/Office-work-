 class EmployeeDetails {
    int id;
    String name;
    double salary;

    EmployeeDetails(int id, String name, double salary){
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    public EmployeeDetails() {
    }

    public void details() {
        System.out.println("id of the employee : "+id);
        System.out.println("Name of the employee : "+name);
        System.out.println("Salary of the employee : "+salary);
    }
}

public class Constructors {
    public static void main (String args[]) {

    EmployeeDetails e1 = new EmployeeDetails();
    e1.id = 12345; 
    e1.name ="Andy";
    e1.salary = 42000;
    e1.details(); 

  EmployeeDetails e2 = new EmployeeDetails(45678,"lucky",78200);
    e2.details(); 
}
}
